---
title: Správa aktualizací pro několik virtuálních počítačů Azure
description: Tento článek popisuje, jak spravovat aktualizace pro virtuální počítače Azure.
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 16fe2d23fdd07f8f150cc010b0a1d232c761c77f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798668"
---
# <a name="manage-updates-for-multiple-machines"></a>Správa aktualizací pro několik počítačů

Řešení Update Management můžete použít ke správě aktualizací a oprav pro virtuální počítače Windows a Linuxem. Z účtu [Azure Automation](automation-offering-get-started.md) můžete:

- Připojit virtuální počítače
- Vyhodnotit stav dostupných aktualizací
- Naplánovat instalaci požadovaných aktualizací
- Zkontrolovat výsledky nasazení za účelem ověření, že aktualizace úspěšně použily na všech virtuálních počítačích, u kterých je povolená Správa aktualizací

## <a name="prerequisites"></a>Požadavky

Chcete-li použít správu aktualizací, budete potřebovat:

- Virtuální počítač nebo počítač s nainstalovaným jedním z podporovaných operačních systémů.

## <a name="supported-operating-systems"></a>Podporované operační systémy

Správa aktualizací je podporována v následujících operačních systémech:

|Operační systém  |Poznámky  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Podporuje pouze aktualizovat posouzení.         |
|Windows Server 2008 R2 SP1 a novější     |Vyžaduje se Windows PowerShell 4.0 nebo novější. ([Stáhnout WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))</br> Windows PowerShell 5.1 se doporučuje pro zvýšení spolehlivosti. ([Stáhnout WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) a 7 (x64)      | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|Red Hat Enterprise 6 (x86/x64) a 7 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|SUSE Linux Enterprise Server 11 (x86/x64) a 12 (x64)     | Agenty Linux musí mít přístup k úložišti aktualizací.        |
|Ubuntu 14.04 LTS, 16.04 LTS a 18.04 LTS (x86/x64)      |Agenty Linux musí mít přístup k úložišti aktualizací.         |

> [!NOTE]
> Pokud se chcete zabránit tomu, aby se aktualizace používaly mimo časové období údržby v Ubuntu, změňte konfiguraci balíčku Unattended-Upgrade tak, aby automatické aktualizace byly zakázány. Další informace najdete v [tématu věnovaném automatickým aktualizacím v příručce k Ubuntu Serveru](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Agenty Linux musí mít přístup k úložišti aktualizací.

Toto řešení nepodporuje agenta Log Analytics pro Linux, který je nakonfigurovaný k ukládání dat do několika pracovních prostorů Azure Log Analytics.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Povolení řešení Update Management pro Azure virtual machines

Na webu Azure Portal otevřete svůj účet Automation a pak vyberte **Správa aktualizací**.

Vyberte **přidat virtuální počítače Azure**.

![Přidat kartu virtuálního počítače Azure](./media/manage-update-multi/update-onboard-vm.png)

Vyberte virtuální počítač, který chcete připojit. 

V části **povolit řešení Update Management**vyberte **povolit** pro připojení virtuálního počítače.

![Dialogové okno Povolit správu aktualizací](./media/manage-update-multi/update-enable.png)

Až po dokončení registrace, povolení správy aktualizací pro virtuální počítač.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Povolení správy aktualizací pro počítače a virtuální počítače umístěné mimo Azure

Informace o povolení správy aktualizací pro počítače a virtuální počítače Windows Azure najdete v tématu [počítače Windows se připojit ke službě Azure Monitor v Azure](../log-analytics/log-analytics-windows-agent.md).

Další postup pro povolení správy aktualizací pro a virtuální počítače s Linuxem jiné než Azure najdete v tématu [připojení počítačů s Linuxem do Azure monitoru protokolů](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Zobrazení počítačů připojených k účtu služby Automation

Po povolení správy aktualizací pro počítače, můžete zobrazit informace o počítači tak, že vyberete **počítače**. Zobrazí se informace *název počítače*, *stav dodržování předpisů*, *prostředí*, *typ operačního systému*, *kritické a nainstalované aktualizace zabezpečení*, *jiné nainstalované aktualizace*, a *připravenost agenta aktualizací* pro počítače.

  ![Zobrazení karty Počítače](./media/manage-update-multi/update-computers-tab.png)

Počítače, které se nedávno byly povoleny pro řízení aktualizace nemusí mít neposoudil. Stav stavu dodržování předpisů pro tyto počítače je **nevyhodnoceno**. Tady je seznam možných hodnot pro stav dodržování předpisů:

- **Kompatibilní**: Počítače, které jsou nechybí kritické aktualizace nebo aktualizace zabezpečení.

- **Nedodržující předpisy**: Počítače, kterým chybí minimálně jedna kritická aktualizace nebo aktualizace zabezpečení.

- **Nevyhodnoceno**: Data o posouzení aktualizací nebyla přijata od počítače v očekávaném časovém rámci. Pro počítače s Linuxem je časový rámec expect za poslední 3 hodiny. Pro počítače s Windows je očekávaném časovém rámci za posledních 12 hodin.

Chcete-li zobrazit stav agenta, klikněte na odkaz ve **připravenost agenta aktualizací** sloupce. Výběrem této možnosti se otevře **Hybrid Worker** podokně a zobrazuje stav procesu Hybrid Worker. Následující obrázek ukazuje příklad agenta, který nebyl připojen ke správě aktualizací pro delší časové období:

![Zobrazení karty Počítače](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Zobrazení posouzení aktualizací

Po povolení řešení Update Management se otevře podokno **Správa aktualizací**. Na kartě **Chybějící aktualizace** můžete zobrazit seznam chybějících aktualizací.

## <a name="collect-data"></a>Shromažďování dat

Agenty, které jsou nainstalovány na virtuálních počítačích a počítačích shromažďují data o aktualizacích. Agenti odeslat data do Azure Update Management.

### <a name="supported-agents"></a>Podporovaní agenti

Následující tabulka popisuje připojené zdroje, které toto řešení podporuje:

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| Agenti systému Windows |Ano |Správa aktualizací shromažďuje informace o aktualizacích systému pro agenty Windows a poté zahájí instalaci požadovaných aktualizací. |
| Agenti systému Linux |Ano |Správa aktualizací shromažďuje informace o aktualizacích systému od agentů systému Linux a pak zahajuje instalaci požadovaných aktualizací v podporovaných distribucích. |
| Skupina pro správu Operations Manageru |Ano |Správa aktualizací shromažďuje informace o aktualizacích systému z agentů v připojené skupině pro správu. |
| Účet služby Azure Storage |Ne |Úložiště Azure neobsahuje informace o aktualizacích systému. |

### <a name="collection-frequency"></a>Četnost shromažďování dat

Po dokončení kontroly dodržování předpisů pro aktualizace do počítače agenta předává informace hromadné protokoly Azure monitoru. Na počítači s Windows se kontrola dodržování předpisů ve výchozím nastavení spouští každých 12 hodin.

Mimo plán kontrol je zahájeno kontroly dodržování předpisů pro aktualizace do 15 minut po restartování, před instalací aktualizací a po instalaci aktualizací agenta MMA.

Pro počítač s Linuxem se kontrola dodržování předpisů ve výchozím nastavení provádí každé tři hodiny. Pokud restartování agenta MMA, kontroly dodržování předpisů je zahájeno do 15 minut.

Může trvat 30 minut až 6 hodin na řídicím panelu zobrazí aktualizovaná data ze spravovaných počítačů.

## <a name="schedule-an-update-deployment"></a>Naplánování nasazení aktualizace

K instalaci aktualizací, naplánujte nasazení, které odpovídá verzi okno plánu a služby. Můžete zvolit typy aktualizací, které budou součástí nasazení. Můžete například zahrnout důležité aktualizace nebo aktualizace zabezpečení a vyloučit kumulativní aktualizace.

V části naplánovat nové nasazení aktualizací pro jeden nebo více virtuálních počítačů, **Správa aktualizací**vyberte **naplánovat nasazení aktualizací**.

V **nové nasazení aktualizací** podokně zadejte následující informace:

- **Název**: Zadejte jedinečný název pro identifikaci nasazení aktualizace.
- **Operační systém**: Vyberte **Windows** nebo **Linux**.
- **Skupiny, které se aktualizace (preview)**: Definování dotazu na základě kombinace předplatného, skupiny prostředků, míst a značky vytvářet dynamické skupiny virtuálních počítačů Azure má zahrnout do vašeho nasazení. Další informace najdete v tématu věnovaném [dynamickým skupinám](automation-update-management.md#using-dynamic-groups).
- **Počítače k aktualizaci**: Vyberte Uložit hledání, importované skupiny, nebo vybrat počítače, které chcete vybrat počítače, které chcete aktualizovat. Pokud zvolíte možnost **Počítače**, ve sloupci **PŘIPRAVENOST AGENTA AKTUALIZACE** se zobrazí připravenost počítačů. Zobrazí se stav počítače a před naplánovat nasazení aktualizace. Další informace o různých způsobech vytvoření skupiny počítačů v Azure Monitor protokolů, najdete v článku [skupiny počítačů v protokoly Azure monitoru](../azure-monitor/platform/computer-groups.md)

  ![Podokno nasazení nové aktualizace](./media/manage-update-multi/update-select-computers.png)

- **Klasifikace aktualizací**: Vyberte typy softwaru, které chcete zahrnout do nasazení aktualizace. Popis typy klasifikace najdete v tématu [klasifikace aktualizací](automation-update-management.md#update-classifications). Typy klasifikace jsou:
  - Důležité aktualizace
  - Aktualizace zabezpečení
  - Kumulativní aktualizace
  - Balíčky funkcí
  - Aktualizace Service Pack
  - Aktualizace definic
  - Nástroje
  - Aktualizace

- **Aktualizace, které se mají zahrnout nebo vyloučit** – Otevře stránku **Zahrnout nebo vyloučit**. Aktualizace, které se mají zahrnout nebo vyloučit jsou na samostatných kartách. Další informace o tom, jak se zahrnutí provádí, najdete v tématu o [chování zahrnutí](automation-update-management.md#inclusion-behavior).

- **Nastavení plánu**: Můžete přijmout výchozí datum a čas, což je 30 minut od aktuálního času. Můžete také zadat jiný čas.

   Můžete také určit, jestli nasazení proběhne jednou nebo opakovaně. V části Nastavení plánu opakování **opakování**vyberte **periodický**.

   ![Dialogové okno Nastavení plánu](./media/manage-update-multi/update-set-schedule.png)

- **Skripty před a po skripty**: Vyberte skripty spouštěné před a po nasazení. Další informace najdete v tématu týkajícím se [správy předzálohovacích a pozálohovacích skriptů](pre-post-scripts.md).
- **Časové období údržby (minuty)**: Zadejte dobu, po které má dojít k nasazení aktualizací. Toto nastavení pomůže zajistit, že se změny provedou v rámci definovaných časových intervalů pro správu a údržbu.

- **Restartovat ovládací prvek** – toto nastavení určuje, jak se zpracovává restartování počítače pro nasazení aktualizace.

   |Možnost|Popis|
   |---|---|
   |Restartování v případě potřeby| **(Výchozí)**  v případě potřeby je zahájeno restartování, pokud umožňuje časové období údržby.|
   |Vždy restartovat|Bez ohledu na to, zda je požadována je zahájeno restartování. |
   |Nikdy nerestartovat|Bez ohledu na to jsou Pokud restartování se vyžaduje, potlačit restartování počítače.|
   |Pouze restartovat – nenainstalují se aktualizace|Tato možnost bude ignorovat instalaci aktualizací a pouze zahájí restartovat počítač.|

Jakmile dokončíte konfiguraci plánu, vyberte **vytvořit** se vrátit na řídicí panel stavu. **Naplánované** tabulky zobrazí plán nasazení, který jste vytvořili.

> [!NOTE]
> Update Management podporuje nasazování aktualizací od Microsoftu a předběžné stahování oprav. To vyžaduje změny v systémech se opravit, naleznete v tématu [první strany a předem stáhnout podporu](automation-update-management.md#firstparty-predownload) Další informace o konfiguraci těchto nastavení ve vašich systémech.

## <a name="view-results-of-an-update-deployment"></a>Zobrazení výsledků nasazení aktualizací

Po spuštění naplánovaného nasazení se stav tohoto nasazení zobrazí na kartě **Nasazení aktualizací** v části **Správa aktualizací**.

Pokud je nasazení aktuálně spuštěné, jeho stav je **Probíhá**. Po úspěšném dokončení nasazení se stav změní na **Succeeded**.

Pokud u jedné nebo více aktualizací v nasazení dojde k chybě, stav je **Částečně neúspěšné**.

![Stav nasazení aktualizace](./media/manage-update-multi/update-view-results.png)

Pokud chcete zobrazit řídicí panel pro nasazení aktualizace, vyberte dokončené nasazení.

**Aktualizovat výsledky** podokno zobrazuje celkový počet aktualizací a výsledků nasazení pro virtuální počítač. V tabulce vpravo je podrobný rozpis všech aktualizací a výsledků instalace. Výsledkem instalace může být jedna z následujících hodnot:

- **Nebyl proveden pokus**: Aktualizace se nenainstalovala, protože byl dostatek času k dispozici podle definovaného časového období údržby.
- **Úspěšné**: Aktualizace byla úspěšná.
- **Nepovedlo**: Aktualizace se nezdařila.

Výběrem možnosti **Všechny protokoly** zobrazíte všechny položky protokolu, které toto nasazení vytvořilo.

Pokud chcete zobrazit datový proud úlohy runbooku, který spravuje nasazení aktualizací na cílovém virtuálním počítači, vyberte dlaždici výstup.

Kliknutím na **Chyby** zobrazíte podrobné informace o případných chybách tohoto nasazení.

## <a name="next-steps"></a>Další postup

- Další informace o správě aktualizací, včetně protokolů, výstupu a chyb, naleznete v tématu [řešení Update Management v Azure](../operations-management-suite/oms-solution-update-management.md).

