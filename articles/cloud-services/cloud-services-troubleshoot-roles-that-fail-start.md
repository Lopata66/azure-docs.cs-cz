---
title: Řešení potíží s rolemi, které se nepodařilo spustit | Microsoft Docs
description: Tady jsou některé běžné důvody, proč se spuštění role cloudové služby nemusí zdařit. Jsou k dispozici také řešení těchto problémů.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 9e1ae9ca6106a6a55e0cfc933ad47a5944ed5abb
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945400"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Řešení potíží s rolemi cloudových služeb, které se nedaří spustit
Tady jsou některé běžné problémy a řešení týkající se rolí Azure Cloud Services, které se nedaří spustit.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Chybějící knihovny DLL nebo závislosti
Nereagující role a role, které secyklují mezi inicializací, **zaneprázdnění**a stavy **zastavení** , mohou být způsobeny chybějícími knihovnami DLL nebo sestaveními.

Příznaky chybějících knihoven DLL nebo sestavení mohou být:

* Vaše instance role se cykluje prostřednictvím **inicializace**, **zaneprázdnění**a stavu **zastavení** .
* Vaše instance role byla přesunuta do připraveného, ale pokud přejdete na webovou aplikaci, stránka se nezobrazí.

Existuje několik doporučených metod pro zkoumání těchto problémů.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnostika chybějících problémů knihoven DLL ve webové roli
Když přejdete na web, který je nasazený ve webové roli, a v prohlížeči se zobrazí chyba serveru, která je podobná následujícímu, může to znamenat, že chybí knihovna DLL.

![Chyba serveru v aplikaci/](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnostikujte problémy vypnutím vlastních chyb.
Další informace o kompletní chybě lze zobrazit tak, že nakonfigurujete Web. config pro webovou roli a nastavíte vlastní režim chyb na vypnuto a znovu nasadíte službu.

Zobrazení dalších úplných chyb bez použití funkce Vzdálená plocha:

1. Otevřete řešení v Microsoft Visual Studio.
2. V **Průzkumník řešení**vyhledejte soubor Web. config a otevřete jej.
3. V souboru Web. config vyhledejte část System. Web a přidejte následující řádek:

    ```xml
    <customErrors mode="Off" />
    ```
4. Uložte soubor.
5. Znovu zabalit a znovu nasaďte službu.

Po opětovném nasazení služby se zobrazí chybová zpráva s názvem chybějícího sestavení nebo knihovny DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnostika potíží zobrazením chyby vzdáleně
Pomocí vzdálené plochy můžete získat přístup k roli a vzdáleně zobrazit informace o chybě. K zobrazení chyb pomocí vzdálené plochy použijte následující postup:

1. Ujistěte se, že je nainstalovaná sada Azure SDK 1,3 nebo novější.
2. Během nasazování řešení pomocí sady Visual Studio povolte vzdálenou plochu. Další informace najdete v tématu [povolit připojení ke vzdálené ploše pro roli v cloudových službách Azure pomocí sady Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. V portál Microsoft Azure, jakmile instance zobrazí stav připraveno, vzdálenědo instance. Další informace o používání vzdálené plochy s Cloud Services najdete v tématu [vzdálené instance rolí](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Přihlaste se k virtuálnímu počítači pomocí přihlašovacích údajů, které jste zadali při konfiguraci vzdálené plochy.
6. Otevřete okno příkazového řádku.
7. Zadejte `IPconfig`.
8. Poznamenejte si hodnotu adresy IPV4.
9. Otevřete Internet Explorer.
10. Zadejte adresu a název webové aplikace. Například, `http://<IPV4 Address>/default.aspx`.

Když přejdete na web, budou se teď vracet explicitní chybové zprávy:

* Chyba serveru v aplikaci/
* Popis: Při provádění aktuálního webového požadavku došlo k neošetřené výjimce. Přečtěte si prosím trasování zásobníku, kde najdete další informace o chybě a kde vznikly v kódu.
* Podrobnosti výjimky: System. IO. FIleNotFoundException: Nelze načíst soubor nebo sestavení Microsoft. WindowsAzure. StorageClient, Version = 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35 nebo jedna z jejích závislostí. Systém nemůže najít zadaný soubor.

Příklad:

![Explicitní chyba serveru v aplikaci/](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnostika problémů pomocí emulátoru COMPUTE
Emulátor Microsoft Azure COMPUTE můžete použít k diagnostice a řešení potíží s chybějícími závislostmi a chybami Web. config.

Pro dosažení nejlepších výsledků použití této metody diagnostiky byste měli použít počítač nebo virtuální počítač, který má čistou instalaci systému Windows. Pro optimální simulaci prostředí Azure použijte Windows Server 2008 R2 x64.

1. Nainstalujte samostatnou verzi sady [Azure SDK](https://azure.microsoft.com/downloads/).
2. Na vývojovém počítači Sestavte projekt cloudové služby.
3. V Průzkumníku Windows přejděte do složky bin\Debug projektu cloudové služby.
4. Zkopírujte složku. csx a soubor. cscfg do počítače, který používáte k ladění problémů.
5. Na čistém počítači otevřete okno příkazového řádku sady Azure SDK a zadejte `csrun.exe /devstore:start`.
6. Do příkazového řádku zadejte `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.
7. Po spuštění role se v Internet Exploreru zobrazí podrobné informace o chybě. K dalšímu diagnostikování problému můžete také použít standardní nástroje pro řešení potíží se systémem Windows.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnostika problémů pomocí IntelliTrace
U pracovních procesů a webových rolí, které používají .NET Framework 4, můžete použít [IntelliTrace](/visualstudio/debugger/intellitrace), který je k dispozici v Microsoft Visual Studio Enterprise.

Pomocí těchto kroků nasaďte službu s povoleným IntelliTrace:

1. Potvrďte, že je nainstalovaná sada Azure SDK 1,3 nebo novější.
2. Nasaďte řešení pomocí sady Visual Studio. Během nasazování zaškrtněte políčko **Povolit IntelliTrace pro role .NET 4** .
3. Po spuštění instance otevřete **Průzkumník serveru**.
4. Rozbalte uzel **Azure\\Cloud Services** a vyhledejte nasazení.
5. Rozbalte nasazení, dokud neuvidíte instance role. Klikněte pravým tlačítkem na jednu z těchto instancí.
6. Vyberte možnost **Zobrazit protokoly IntelliTrace**. Otevře se **Souhrn IntelliTrace** .
7. Vyhledejte část s výjimkami v části Souhrn. Pokud existují výjimky, oddíl bude označovat **data výjimky**.
8. Rozbalte **data výjimky** a vyhledejte chyby **System. IO. FileNotFoundException** podobné následujícímu:

![Data výjimky, chybějící soubor nebo sestavení](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Adresa chybějících knihoven DLL a sestavení
Chcete-li vyřešit chybějící DLL a chyby sestavení, postupujte podle následujících kroků:

1. Otevřete řešení v sadě Visual Studio.
2. V **Průzkumník řešení**otevřete složku **odkazy** .
3. Klikněte na sestavení identifikované v chybě.
4. V podokně **vlastnosti** Najděte položku **Kopírovat místní vlastnost** a nastavte hodnotu na **true**.
5. Znovu nasaďte cloudovou službu.

Jakmile ověříte, že byly všechny chyby opraveny, můžete službu nasadit bez zaškrtnutí políčka **Povolit IntelliTrace pro role .NET 4** .

## <a name="next-steps"></a>Další postup
Podívejte se na další články týkající se [řešení potíží](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) pro Cloud Services.

Informace o řešení potíží s rolemi cloudové služby pomocí dat diagnostiky počítačů Azure PaaS najdete v tématu [série blogů v Kevin Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
