---
title: Příručka pro řízení chování při vypínání Windows v Azure Lab Services | Microsoft Docs
description: Postup automatického vypnutí nečinného virtuálního počítače s Windows a odebrání příkazu pro vypnutí systému Windows.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 3/30/2020
ms.author: spelluru
ms.openlocfilehash: 7b839df5940ab26e5c1a99a1bda1fbd2545f8cc4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113124"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Průvodce řízením chování při vypínání Windows

Azure Lab Services poskytuje několik řízení nákladů, které zajistí, že virtuální počítače s Windows nejsou neočekávaně spuštěné:
 - [Nastavit plán](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [Nastavit kvóty pro uživatele](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [Povolení automatického vypnutí při odpojení](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

I s těmito ovládacími prvky nákladů existují situace, kdy se virtuální počítač s Windows může nečekaně spustit; a v důsledku toho odečte z kvóty studenta:

- **Okno RDP je ponecháno otevřené**
  
    Když se student připojí ke svému virtuálnímu počítači pomocí protokolu RDP, může nechtěně ponechat okno RDP otevřené.  Dokud zůstane okno RDP otevřené, nastavení **automatického vypnutí při odpojení** se nikdy neprojeví, protože se aktivuje jenom po odpojení relace RDP.

- **Příkaz pro vypnutí systému Windows slouží k vypnutí virtuálního počítače.**
  
    Student může použít příkaz pro vypnutí systému Windows nebo jiné mechanismy vypnutí poskytované v rámci systému Windows k vypnutí virtuálního počítače místo použití [tlačítka zastavit Azure Lab Services](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm).  V takovém případě se virtuální počítač stále používá v perspektivě Azure Lab Services.
    
V této příručce najdete kroky pro automatické vypnutí nečinného virtuálního počítače s Windows a odebrání příkazu pro vypnutí Windows z nabídky **Start** .  

> [!NOTE]
> Virtuální počítač se také může nečekaně odečíst od kvóty, když Student spustí svůj virtuální počítač, ale nikdy se k němu nepřipojí pomocí protokolu RDP.  Tato *Příručka aktuálně tento scénář neřeší.*  Místo toho by se měli po jeho spuštění na studenty připojit ke svému virtuálnímu počítači hned pomocí protokolu RDP; nebo by měl virtuální počítač zastavit.

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>Automatické odpojení a vypnutí protokolu RDP pro nečinný virtuální počítač

Systém Windows poskytuje **místní nastavení zásady skupiny** , pomocí kterých můžete nastavit časový limit pro automatické odpojení relace RDP, pokud se nejedná o nečinný.  V případě, že se *nejedná o žádný vstup* mouse\keyboard, je zjištěno, že relace je nečinná.  Jakékoli dlouhodobě běžící aktivity, které nezahrnují vstup mouse\keyboard, způsobí, že se virtuální počítač nachází ve stavu nečinnosti.  To zahrnuje spuštění dlouhého dotazu, streamování videa, kompilování atd.  V závislosti na potřebách vaší třídy se můžete rozhodnout nastavit časový limit nečinnosti, aby byl dostatečně dlouhý pro zpracování těchto typů aktivit.  V případě potřeby můžete například nastavit časový limit nečinnosti na 1 nebo více hodin.

Toto je činnost studenta při konfiguraci **limitu nečinných relací** v kombinaci s nastavením [**automatického vypnutí při odpojení**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect) :
 1. Student se připojí ke svému virtuálnímu počítači s Windows pomocí protokolu RDP.
 2. Když student opustí otevřené okno RDP a virtuální počítač je nečinný pro zadaný **limit nečinné relace** (například 5 minut), student uvidí následující dialog:

    ![Časový limit nečinnosti – dialogové okno vypršení platnosti](../media/how-to-windows-shutdown/idle-time-expired.png)

1. Pokud student *neklikne* na tlačítko **OK**, jejich relace RDP se automaticky odpojí po 2 minutách.
2. Jakmile se relace RDP odpojí, jakmile se dosáhne zadaného časového rámce pro nastavení **Automatické vypnutí při odpojení** , virtuální počítač se automaticky vypíná Azure Lab Services.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>Nastavení časového limitu nečinné relace protokolu RDP na virtuálním počítači šablony

Pokud chcete nastavit časový limit nečinnosti relace RDP, můžete se připojit k virtuálnímu počítači šablony a spustit níže uvedený skript PowerShellu.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 1000

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
Nebo se můžete rozhodnout, že budete postupovat podle těchto ručních kroků pomocí šablony VM:

1. Stiskněte klávesu Windows, zadejte **gpedit**a pak vyberte **upravit zásady skupiny (ovládací panely)**.

1. V **části Konfigurace počítače > Šablony pro správu > součásti systému Windows > služba Vzdálená plocha > hostitel relace vzdálené plochy > časový limit relace**.  

    ![Editor místních zásad skupiny](../media/how-to-windows-shutdown/group-policy-idle.png)
   
1. Klikněte pravým tlačítkem na **nastavit časový limit aktivních a nečinných relací vzdálené plochy**a klikněte na **Upravit**.

1. Zadejte následující nastavení a potom klikněte na **OK**:
   1. Vyberte **Povoleno**.
   1. V části **Možnosti**zadejte **limit nečinné relace**.

    ![Limit nečinné relace](../media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. Nakonec, pokud chcete toto chování zkombinovat s nastavením **Automatické vypnutí při odpojení** , postupujte podle kroků v článku postup: [Povolení automatického vypnutí virtuálních počítačů při odpojení](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect).

> [!WARNING]
> Po nakonfigurování tohoto nastavení pomocí PowerShellu pro změnu nastavení registru přímo nebo ručně pomocí editoru Zásady skupiny musíte nejdřív restartovat virtuální počítač, aby se nastavení projevilo.  I když nakonfigurujete nastavení pomocí registru, Editor Zásady skupiny nebude vždycky aktualizovat, aby odrážel změny nastavení registru. nastavení registru se ale pořád projeví podle očekávání a při nečinnosti po dobu, kterou jste zadali, se zobrazí relace RDP odpojená.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Odebrat příkaz pro vypnutí Windows z nabídky Start

Nastavení **místní zásady skupiny** systému Windows vám také umožní odebrat příkaz pro vypnutí z nabídky **Start** .

K odebrání příkazu pro vypnutí se můžete připojit k virtuálnímu počítači šablony a spustit pod ním skript prostředí PowerShell.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

Nebo se můžete rozhodnout, že budete postupovat podle těchto ručních kroků pomocí šablony VM:

1. Stiskněte klávesu Windows, zadejte **gpedit**a pak vyberte **upravit zásady skupiny (ovládací panely)**.

1. Přejděte na **Konfigurace počítače > Šablony pro správu > nabídce Start a na hlavním panelu**.  

    ![Editor místních zásad skupiny](../media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Klikněte pravým tlačítkem na **Odebrat a zabraňte přístup k příkazům pro vypnutí, restartování, spánku a hibernaci**a klikněte na **Upravit**.

1. Vyberte nastavení **povoleno** a pak klikněte na **OK**:
 
   ![Nastavení vypnutí](../media/how-to-windows-shutdown/edit-shutdown.png)

1. Všimněte si, že příkaz pro vypnutí se již nezobrazuje v nabídce **Start** systému Windows. zobrazí se pouze příkaz **Odpojit** .

    ![Shutdown – příkaz](../media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Další kroky
Přečtěte si článek o tom, jak připravit virtuální počítač s Windows šablonou: [Průvodce nastavením počítače šablony Windows v Azure Lab Services](how-to-prepare-windows-template.md)