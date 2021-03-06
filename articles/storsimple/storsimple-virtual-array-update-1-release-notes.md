---
title: Zpráva k vydání verze pro StorSimple Virtual Array Update 1,0
description: Popisuje kritické otevřené problémy a řešení pro virtuální pole StorSimple s aktualizací 1,0.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 2ec88e4e97d45b27c0226198491b3adec6448496
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000891"
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>Zpráva k vydání verze pro StorSimple Virtual Array Update 1,0

## <a name="overview"></a>Přehled

Následující poznámky k verzi identifikují kritické otevřené problémy a vyřešené problémy pro Microsoft Azure StorSimple aktualizace virtuálních polí.

Poznámky k verzi se průběžně aktualizují a při zjištění kritických problémů vyžadujících alternativní řešení se přidají. Před nasazením StorSimple virtuálního pole pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 1,0 odpovídá **10.0.10296.0** verze softwaru.

> [!IMPORTANT]
> - Aktualizace jsou rušivé a restartují vaše zařízení. Pokud probíhá vstupně-výstupní operace, dojde k výpadku zařízení. Podrobné pokyny, jak použít aktualizaci, najdete v článku [instalace aktualizace 1,0](storsimple-virtual-array-install-update-1.md).
>
> - Aktualizace 1 je dostupná jenom přes Azure Portal, pokud je na zařízení spuštěná aktualizace 0,6.

## <a name="whats-new-in-update-10"></a>Co je nového v aktualizaci 1,0

**Aktualizace 1,0 obsahuje změny související s ověřováním služby StorSimple Device Manager a měli byste je nasadit do svého nejbližšího.** Tato aktualizace obsahuje následující vylepšení a opravy chyb:

 - **Použití Azure Active Directory (AAD) k ověření ve službě StorSimple Device Manager Service** – od aktualizace 1,0 a vyšší, se k ověřování pomocí služby StorSimple Device Manager používá Azure Active Directory. Starý ověřovací mechanismus bude od prosince 2017 zastaralý. Všichni uživatelé musí ve svých pravidlech firewallu zahrnovat nové adresy URL pro ověřování. Další informace najdete na adrese URL pro ověřování uvedené v článku [požadavky na síť pro virtuální pole StorSimple](storsimple-ova-system-requirements.md).
 
    Pokud adresa URL pro ověření není v pravidlech brány firewall zahrnutá, zobrazí se uživatelům kritická výstraha, že jejich zařízení StorSimple nebylo možné ověřit pomocí služby. Pokud se uživatelům zobrazí tato výstraha, musí zahrnovat novou adresu URL pro ověření. Další informace najdete na webu výstrahy týkající se [StorSimple sítě](storsimple-virtual-array-manage-alerts.md).

 - **Vylepšení výkonu** – bylo provedeno několik oprav chyb, které zlepšují rychlost čtení v cloudu, vrstev a vrstev. V důsledku toho je výkon zálohování i obnovení vylepšený pro zařízení iSCSI a souborový server.

 - **Vylepšení shromažďování paměti** – Tato verze obsahuje opravy chyb, které zlepšují výkon cyklu uvolňování paměti, když je zařízení a účet úložiště ve dvou vzdálených oblastech.

 - **Vylepšení protokolování** – Tato verze obsahuje vylepšení protokolování souvisejícího s uvolňováním paměti a vstupně-výstupní cestou.


## <a name="issues-fixed-in-update-10"></a>Problémy opravené v aktualizaci 1,0

Následující tabulka poskytuje souhrn chyb opravených v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Ověřování pomocí AAD| Tato verze obsahuje změny, které umožňují ověřování AAD pomocí Device Manager StorSimple.|
| 2 |Uvolnění paměti| Tento problém se nahlásil na zákaznické stránce, kde se zařízení a účty úložiště nacházejí v různých oblastech, a zákazník oznámil občasné chyby sítě a tím má vliv na fakturaci. V této verzi byl tento problém vyřešen. |
| 3 |Výkon| Tato verze obsahuje změny, které vedou k vylepšení výkonu při obnovení nebo čtení nebo na úrovni cloudu.|
| 4 |Aktualizace| Došlo k potížím s aktualizací v dřívější verzi, která vedla k selhání zálohování na zákaznickém webu. Tento problém je opravený v této verzi.|

## <a name="known-issues-in-update-10"></a>Známé problémy v aktualizaci 1,0

Následující tabulka obsahuje souhrn známých problémů pro virtuální pole StorSimple a obsahuje vydané verze zaznamenané z předchozích verzí.

| Ne. | Funkce | Problém | Alternativní řešení/komentáře |
| --- | --- | --- | --- |
| **1.** |Aktualizace |Virtuální pole vytvořená ve verzi Preview nelze aktualizovat na podporovanou verzi obecné dostupnosti. |U těchto virtuálních polí je potřeba převzít služby při selhání pro vydání obecné dostupnosti pomocí pracovního postupu zotavení po havárii (DR). |
| **2.** |Zřízený datový disk |Jakmile zřídíte datový disk určité zadané velikosti a vytvoříte odpovídající virtuální pole StorSimple, nesmíte tento datový disk zvětšit ani zmenšit. Při pokusu o provedení dojde ke ztrátě všech dat v místních vrstvách zařízení. | |
| **3.** |Zásady skupiny |Když je zařízení připojené k doméně, může použití zásad skupiny negativně ovlivnit operaci zařízení. |Ujistěte se, že vaše virtuální pole je ve své vlastní organizační jednotce pro službu Active Directory a že pro něj nejsou použité žádné objekty zásad skupiny (GPO). |
| **4.** |Místní webové uživatelské rozhraní |Pokud jsou v aplikaci Internet Explorer povoleny rozšířené funkce zabezpečení (IE ESC), některé místní stránky webového uživatelského rozhraní, jako je například řešení potíží nebo údržba, nemusí správně fungovat. Tlačítka na těchto stránkách také nemusí fungovat. |Vypnout rozšířené funkce zabezpečení v Internet Exploreru. |
| **čl.** |Místní webové uživatelské rozhraní |Ve virtuálním počítači s technologií Hyper-V se síťová rozhraní ve webovém uživatelském rozhraní zobrazují jako rozhraní o 10 GB/s. |Toto chování je reflexe technologie Hyper-V. Technologie Hyper-V vždy zobrazuje 10 GB/s pro virtuální síťové adaptéry. |
| **6.** |Vrstvené svazky nebo sdílené složky |Uzamykání rozsahu bajtů pro aplikace, které pracují se StorSimple vrstvenými svazky, se nepodporuje. Pokud je povolené zamykání rozsahu bajtů, nefunguje vrstvení StorSimple. |Doporučené míry zahrnují: <br></br>Vypněte uzamykání rozsahu bajtů v logice aplikace.<br></br>Vyberte, chcete-li umístit data pro tuto aplikaci na místně připnuté svazky na rozdíl od vrstveného svazku.<br></br>*Upozornění*: Pokud je povolené použití místně připojených svazků a uzamykání rozsahu bajtů, může být místně připojený svazek online, i když se obnovení nedokončí. V takových případech, pokud probíhá obnovení, je nutné počkat na dokončení obnovení. |
| **čl.** |Vrstvené sdílené složky |Práce s velkými soubory může mít za následek zpomalení vrstvy. |Při práci s velkými soubory doporučujeme, aby byl největší soubor menší než 3% velikosti sdílené složky. |
| **8.** |Využitá kapacita pro sdílené složky |Pokud sdílená složka neobsahuje žádná data, může se zobrazit spotřeba sdílení. Tato spotřeba je způsobená tím, že využitá kapacita pro sdílené složky zahrnuje metadata. | |
| **9.** |Zotavení po havárii |Zotavení po havárii souborového serveru můžete provést jenom se stejnou doménou, jakou má zdrojové zařízení. V této verzi není podporováno zotavení po havárii do cílového zařízení v jiné doméně. |To je implementováno v novější verzi. Další informace najdete v [poznámkách k převzetí služeb při selhání a zotavení po havárii pro virtuální pole StorSimple](storsimple-virtual-array-failover-dr.md) . |
| **10pruhový.** |Azure PowerShell |Virtuální pole StorSimple se nedají spravovat prostřednictvím Azure PowerShell v této verzi. |Veškerá správa virtuálních zařízení by se měla provádět prostřednictvím Azure Portal a místního webového uživatelského rozhraní. |
| **odst.** |Změna hesla |Konzola zařízení Virtual Array podporuje pouze vstup ve formátu klávesnice en-US. | |
| **12,5.** |CHAP |Po vytvoření nelze odstranit přihlašovací údaje protokolu CHAP. Pokud navíc upravíte přihlašovací údaje protokolu CHAP, musíte svazky převést do režimu offline a pak je převést do režimu online, aby se změna projevila. |Tento problém je řešen v pozdější verzi. |
| **13,5.** |Server iSCSI |Zobrazené úložiště pro svazek iSCSI se může lišit v StorSimple Device Manager službě a hostiteli iSCSI. |Hostitel iSCSI má zobrazení systému souborů.<br></br>Zařízení vidí bloky přidělené v případě, že byl svazek v maximální velikosti. |
| **čtrnáct.** |Souborový server |Pokud k souboru ve složce je přidružen alternativní datový proud (ADS), nejsou tyto reklamy zálohovány ani obnoveny prostřednictvím zotavení po havárii, klonování a obnovení na úrovni položek. | |
| **15.** |Souborový server |Symbolické odkazy nejsou podporovány. | |
| **16bitovém.** |Souborový server |Soubory chráněné systémem Windows systém souborů EFS (Encrypting File System) (EFS) při zkopírování nebo uložení na souborový server virtuálních polí StorSimple mají za následek nepodporovanou konfiguraci.  | |
| **sedmnáct.** |Aktualizace |Pokud se při pokusu o instalaci opravy hotfix prostřednictvím místního uživatelského rozhraní zobrazí kód chyby: 2359302 (Hex 0x240006), znamená to, že oprava hotfix je už na vašem zařízení nainstalovaná.   | |
| **let.** |Aktualizace |Pokud použijete místní webové uživatelské rozhraní k instalaci Update 1 na virtuální pole, musíte zajistit, aby byla spuštěna aktualizace 0,6. Pokud používáte verzi nižší než Update 0,6, musíte nejdřív nainstalovat Update 0,6 a pak použít Update 1. Pokud přímo nainstalujete aktualizaci 1,0 z verze předběžné aktualizace 0,6, nebudete mít nějaké aktualizace a grafy monitorování nebudou fungovat.   | |


## <a name="next-steps"></a>Další kroky
[Nainstalujte aktualizaci 1,0](storsimple-virtual-array-install-update-1.md) na virtuální pole StorSimple.

## <a name="references"></a>Reference
Hledáte starší poznámku k verzi? Přejít na:
*  [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,6](storsimple-virtual-array-update-06-release-notes.md)
* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,5](storsimple-virtual-array-update-05-release-notes.md)
* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,4](storsimple-virtual-array-update-04-release-notes.md)
* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,3](storsimple-ova-update-03-release-notes.md)
* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,1 a 0,2](storsimple-ova-update-01-release-notes.md)
* [Poznámky k verzi pro StorSimple Virtual Array General Availability](./storsimple-virtual-array-update-06-release-notes.md)