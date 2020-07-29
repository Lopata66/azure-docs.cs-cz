---
title: Co je Windows Virtual Desktop? – Azure
description: Přehled virtuálního klienta Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 07/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 26be41361a861795a9897442d9a0cc8bb14c1450
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292590"
---
# <a name="what-is-windows-virtual-desktop"></a>Co je Windows Virtual Desktop? 

Virtuální plocha Windows je služba virtualizace plochy a aplikací, která běží v cloudu.

Tady je seznam toho, co můžete udělat při spuštění virtuální plochy Windows v Azure:

* Nastavení nasazení s více relacemi s Windows 10, které poskytuje plnou sadu Windows 10 s škálovatelností
* Virtualizace Microsoft 365ch aplikací pro podniky a jejich optimalizaci pro spouštění ve virtuálních scénářích s více uživateli
* Poskytněte virtuálním klientům Windows 7 bezplatné rozšířené aktualizace zabezpečení.
* Přenesení stávajících klientských počítačů a aplikací pro vzdálenou plochu (RDS) a Windows serveru do libovolného počítače
* Virtualizujte desktopy i aplikace
* Správa stolních počítačů a aplikací pro Windows 10, Windows Server a Windows 7 pomocí jednotného prostředí pro správu

## <a name="introductory-video"></a>Úvodní video

Seznamte se s virtuálními počítači s Windows, proč je jedinečný a co je nového v tomto videu:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Další informace o virtuálním počítači s Windows najdete v [našem seznamu](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev)testů.

## <a name="key-capabilities"></a>Klíčové funkce

S virtuálním počítačem s Windows můžete nastavit škálovatelné a flexibilní prostředí:

* Vytvořte v předplatném Azure úplné prostředí virtualizace plochy, aniž byste museli spouštět žádné další servery brány.
* Publikujte tolik fondů hostitelů, kolik potřebujete pro nejrůznější úlohy.
* Využijte vlastní image pro produkční úlohy nebo testy z Galerie Azure.
* Snižte náklady pomocí fondu a prostředků s více relacemi. Díky nové možnosti více relací Windows 10 Enterprise výhradně pro role Windows Virtual Desktop a Hostitel relace vzdálené plochy (autovzdálené plochy) na Windows serveru můžete významně snížit počet virtuálních počítačů a režie operačního systému (OS) a zároveň zajistit stejné prostředky uživatelům.
* Poskytněte individuální vlastnictví prostřednictvím osobních (trvalých) ploch.

Můžete nasazovat a spravovat virtuální klienty:

* K nakonfigurování fondů hostitelů, vytváření skupin aplikací, přiřazování uživatelů a publikování prostředků použijte rozhraní PowerShell a rozhraní REST pro virtuální počítače s Windows.
* Můžete publikovat celou plochu nebo jednotlivé vzdálené aplikace z jednoho fondu hostitelů, vytvořit jednotlivé skupiny aplikací pro různé sady uživatelů nebo dokonce přiřadit uživatele k několika skupinám aplikací, abyste snížili počet imagí.
* Při správě prostředí použijte integrovaný delegovaný přístup k přiřazování rolí a shromažďování diagnostiky pro pochopení různých konfiguračních a uživatelských chyb.
* Použijte novou diagnostickou službu k řešení chyb.
* Jenom spravovat image a virtuální počítače, ne infrastrukturu. Nemusíte osobně spravovat role vzdálené plochy, jako je třeba Vzdálená plocha, a to jenom virtuální počítače ve vašem předplatném Azure.

Můžete také přiřadit uživatele k virtuálním plochám a propojit je:

* Po přiřazení můžou uživatelé spustit libovolného klienta virtuální plochy Windows a připojit uživatele k publikovaným plochám a aplikacím Windows. Připojte se z libovolného zařízení prostřednictvím nativní aplikace na zařízení nebo webového klienta pro HTML5 virtuálních počítačů s Windows.
* Bezpečně navažte uživatele prostřednictvím zpětného připojení ke službě, takže nikdy nemusíte mít otevřené žádné příchozí porty.

## <a name="requirements"></a>Požadavky

K dispozici je několik věcí, které potřebujete k nastavení virtuální plochy Windows, a k úspěšnému připojení uživatelů ke svým plochám a aplikacím v systému Windows.

Plánujeme přidat podporu pro následující operačních systémech, takže se ujistěte, že máte [příslušné licence](https://azure.microsoft.com/pricing/details/virtual-desktop/) pro uživatele na základě plochy a aplikací, které plánujete nasadit:

|Operační systém|Požadovaná licence|
|---|---|
|Windows 10 Enterprise s více relacemi nebo Windows 10 Enterprise|Microsoft 365 E3, E5, a3, A5, F3, Business Premium<br>Windows E3, E5, a3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, a3, A5, F3, Business Premium<br>Windows E3, E5, a3, A5|
|Windows Server 2012 R2, 2016, 2019|Licence CAL pro klientský přístup (CAL) se Software Assurance|

K podpoře virtuálního klienta Windows potřebuje vaše infrastruktura tyto věci:

* [Azure Active Directory](/azure/active-directory/)
* Služba Windows Server Active Directory je synchronizovaná s Azure Active Directory. Můžete ji nakonfigurovat jedním z následujících způsobů:
  * Azure AD Connect (pro hybridní organizace)
  * Azure AD Domain Services (pro hybridní nebo cloudové organizace)
* Předplatné Azure, které obsahuje virtuální síť, která buď obsahuje nebo je připojená k Windows serveru Active Directory
  
Virtuální počítače Azure, které vytvoříte pro virtuální počítače s Windows, musí být:

* [Standardní](../active-directory-domain-services/active-directory-ds-comparison.md) služba AD připojená k doméně nebo k ní [připojená](../active-directory/devices/hybrid-azuread-join-plan.md). Virtuální počítače nemůžou být připojené k Azure AD.
* Spouští se jedna z následujících [podporovaných bitových kopií operačního systému](#supported-virtual-machine-os-images).

>[!NOTE]
>Pokud potřebujete předplatné Azure, můžete si [zaregistrovat měsíční bezplatnou zkušební verzi](https://azure.microsoft.com/free/). Pokud používáte bezplatnou zkušební verzi Azure, měli byste použít službu Azure AD Domain Services k udržování synchronizace služby Windows Server Active Directory s Azure Active Directory.

Seznam adres URL, které byste měli odblokovat, aby nasazení virtuálních klientů Windows fungovalo tak, jak má, najdete v našem [seznamu bezpečných adres URL](safe-url-list.md).

Windows Virtual Desktop obsahuje stolní počítače a aplikace Windows, které dodáváte uživatelům a řešení pro správu, které je hostované jako služba v Azure od Microsoftu. Stolní počítače a aplikace můžou být nasazené na virtuálních počítačích v libovolné oblasti Azure a řešení pro správu a data pro tyto virtuální počítače se budou nacházet v USA. To může vést k přenosu dat do USA.

Pro zajištění optimálního výkonu zajistěte, aby vaše síť splňovala následující požadavky:

* Latence operace Round-Trip (RTT) ze sítě klienta do oblasti Azure, ve které byly nasazené fondy hostitelů, musí být nižší než 150 ms.
* Síťový provoz se může směrovat mimo hranice země/oblasti, když se virtuální počítače, které hostují desktopy a aplikace, připojí ke službě správy.
* Pro optimalizaci výkonu sítě doporučujeme, aby se virtuální počítače hostitele relace společně umístěného ve stejné oblasti Azure jako služba pro správu.

## <a name="supported-remote-desktop-clients"></a>Podporovaní klienti vzdálené plochy

Virtuální plocha Windows podporuje následující klienti vzdálené plochy:

* [Plocha Windows](connect-windows-7-10.md)
* [Web](connect-web.md)
* [macOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (Preview)](connect-android.md)

> [!IMPORTANT]
> Virtuální počítač s Windows nepodporuje klienta připojení k aplikacím RemoteApp a vzdálené ploše (RADC) ani klienta Připojení ke vzdálené ploše (MSTSC).

> [!IMPORTANT]
> Virtuální počítač s Windows v současné době nepodporuje klienta vzdálené plochy z Windows Storu. Podpora tohoto klienta se přidá v budoucí verzi.

Další informace o adresách URL, které musíte odblokovat, abyste mohli používat vzdálené klienty, najdete v [seznamu bezpečných adres URL](safe-url-list.md).

## <a name="supported-virtual-machine-os-images"></a>Podporované image operačních systémů virtuálních počítačů

Virtuální počítač s Windows podporuje následující image operačních systémů x64:

* Windows 10 Enterprise s více relacemi verze 1809 nebo novější
* Windows 10 Enterprise, verze 1809 nebo novější
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Virtuální počítač s Windows nepodporuje image operačního systému x86 (32-bit), Windows 10 Enterprise N nebo Windows 10 Enterprise KN. Windows 7 také nepodporuje žádná profilová řešení založená na VHD nebo VHDX, která jsou hostovaná na spravovaných Azure Storage z důvodu omezení velikosti sektoru.

Dostupné možnosti automatizace a nasazení závisí na tom, který operační systém a jakou verzi si zvolíte, jak je znázorněno v následující tabulce: 

|Operační systém|Galerie imagí Azure|Ruční nasazení virtuálního počítače|Integrace šablon Azure Resource Manager|Zřizování fondů hostitelů na Azure Marketplace|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Více relací Windows 10, verze 1903|Ano|Ano|Ano|Ano|
|Více relací Windows 10, verze 1809|Ano|Ano|Ne|Ne|
|Windows 10 Enterprise, verze 1903|Ano|Ano|Ano|Ano|
|Windows 10 Enterprise, verze 1809|Ano|Ano|Ne|Ne|
|Windows 7 Enterprise|Ano|Ano|Ne|Ne|
|Windows Server 2019|Ano|Ano|Ne|Ne|
|Windows Server 2016|Ano|Ano|Ano|Ano|
|Windows Server 2012 R2|Ano|Ano|Ne|Ne|

## <a name="next-steps"></a>Další kroky

Pokud používáte virtuální plochu Windows (Classic), můžete začít s naším kurzem na webu [Vytvoření tenanta ve virtuálním počítači s Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).

Pokud používáte virtuální plochu Windows s Azure Resource Manager integrací, budete místo toho muset vytvořit fond hostitelů. Přejděte k následujícímu kurzu, abyste mohli začít.

> [!div class="nextstepaction"]
> [Vytvoření fondu hostitelů s využitím webu Azure Portal](create-host-pools-azure-marketplace.md)
