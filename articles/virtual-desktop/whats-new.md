---
title: Co je nového ve virtuálním počítači s Windows? – Azure
description: Nové funkce a aktualizace produktů pro virtuální počítač s Windows
author: Heidilohr
ms.topic: overview
ms.date: 09/02/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.custom: references_regions
ms.openlocfilehash: d698470f450f6fe903ab68334764e0918d659d7f
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89319810"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Co je nového ve virtuálním počítači s Windows?

Virtuální počítače s Windows se pravidelně aktualizují. V tomto článku najdete informace o:

- Nejnovější aktualizace
- Nové funkce
- Vylepšení existujících funkcí
- Opravy chyb

Tento článek se aktualizuje měsíčně. Nezapomeňte se podívat, jak často se budou zobrazovat nové aktualizace.

## <a name="august-2020"></a>Srpen 2020

Tady je postup, který se změnil v srpnu 2020:

- Vylepšili jsme výkon, aby se snížila latence připojení v následujících oblastech Azure: 

    - Spojené království
    - Francie
    - Norsko
    - Jižní Korea

   Pomocí [Estimator zkušeností](https://azure.microsoft.com/services/virtual-desktop/assessment/) můžete získat obecnou představu o tom, jak tyto změny ovlivní vaše uživatele.

- Klient vzdálené plochy Microsoft Store (v 10.2.1522 +) je teď všeobecně dostupný! Tato verze klienta Microsoft Store vzdálené plochy je kompatibilní s virtuálním počítačem s Windows. Zavedli jsme také aktualizované toky uživatelského rozhraní pro vylepšené uživatelské prostředí. Tato aktualizace zahrnuje návrh, světlé a tmavé režimy a mnoho dalších zajímavých změn. Také jsme přepsali klienta, aby používal stejný základní modul protokolu RDP (Remote Desktop Protocol) jako klienti iOS, macOS a Android. To nám umožní doručovat nové funkce na všech platformách na vyšší míru. [Stáhněte si klienta](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab) a podělte se o to!

- Opravili jsme problém v desktopovém klientovi Teams (verze 1.3.00.21759), kde klient v chatu, kanálech a kalendáři ukázal jenom časové pásmo UTC. Aktualizovaný klient teď místo toho zobrazuje časové pásmo vzdálené relace.

- Azure Advisor je teď součástí virtuálního klienta Windows. Při přístupu k virtuálnímu klientovi Windows prostřednictvím Azure Portal můžete zobrazit doporučení pro optimalizaci prostředí virtuálních počítačů s Windows. Další informace najdete na [Azure Advisor](azure-advisor.md).

- Azure CLI teď podporuje virtuální počítače s Windows ( `az desktopvirtualization` ), které vám pomůžou automatizovat nasazení virtuálních počítačů s Windows. Seznam příkazů rozšíření najdete v [desktopvirtualization](/cli/azure/ext/desktopvirtualization/?view=azure-cli-latest) .

- Aktualizovali jsme naše šablony nasazení tak, aby byly plně kompatibilní s rozhraními Azure Resource Manager virtuálních počítačů s Windows. Šablony můžete najít na [GitHubu](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates).

- Portál US Gov Virtual Desktop pro Windows je teď ve verzi Public Preview. Další informace najdete v [našem oznámení](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/).

## <a name="july-2020"></a>Červenec 2020  

Červenec byla v době, kdy se integrace virtuálních počítačů s Windows s Azure Resource Management stala všeobecně dostupnou.

Tato nová verze se změnila: 

- "Verze 2019 Release" se teď označuje jako "virtuální plocha Windows (Classic)", zatímco vydaná verze pružiny 2020 je teď jenom "virtuální plocha Windows". Další informace najdete v [tomto blogovém příspěvku](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/). 

Další informace o nových funkcích najdete v [tomto blogovém příspěvku](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="autoscaling-tool-update"></a>Aktualizace nástroje automatického škálování

Nejnovější verze nástroje pro automatické škálování, který je ve verzi Preview, je teď všeobecně dostupná. Tento nástroj používá účet Azure Automation a aplikaci logiky Azure k automatickému vypnutí a restartování virtuálních počítačů hostitele relace v rámci fondu hostitelů, což snižuje náklady na infrastrukturu. Další informace najdete v informacích o [škálování hostitelů relací pomocí Azure Automation](set-up-scaling-script.md).

### <a name="azure-portal"></a>portál Azure

Pomocí Azure Portal ve virtuální ploše Windows teď můžete provádět následující akce: 

- Přímé přiřazování uživatelů k hostitelům relace na osobním počítači  
- Změna nastavení prostředí ověřování pro fondy hostitelů 

### <a name="diagnostics"></a>Diagnostika

Vydali jsme některé nové předem připravené dotazy pro Log Analytics pracovní prostor. Pokud chcete získat přístup k dotazům, přejděte na **protokoly** a v části **kategorie**vyberte **virtuální klient Windows**. Další informace najdete v [části použití Log Analytics pro diagnostické funkce](diagnostics-log-analytics.md).

### <a name="update-for-remote-desktop-client-for-android"></a>Aktualizace pro klienta vzdálené plochy pro Android

[Klient služby Vzdálená plocha pro Android](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) teď podporuje připojení k virtuálním plochám Windows. Od verze 10.0.7 klient pro Android nabízí nové uživatelské rozhraní pro lepší uživatelské prostředí. Klient se taky integruje s Microsoft Authenticator na zařízeních s Androidem, aby se při přihlášení k odběru virtuálních počítačů s Windows virtuálními plochami povolil podmíněný přístup.  

Předchozí verze klienta vzdálené plochy se teď nazývá "Vzdálená plocha 8". Všechna existující připojení, která jste v předchozí verzi klienta, budou bezproblémově převedena na nového klienta. Nový klient se přepsal do stejného základního modulu protokolu RDP Core jako klienti iOS a macOS, což je rychlejší vydání nových funkcí napříč všemi platformami. 

### <a name="teams-update"></a>Aktualizace týmů

Provedli jsme vylepšení Microsoft Teams pro virtuální počítače s Windows. Nejdůležitější je, že Windows Virtual Desktop teď podporuje optimalizaci zvuku a videa pro klienta Windows Desktop. Přesměrování vylepšuje latenci vytvořením přímých cest mezi uživateli při použití zvuku nebo videa v voláních a schůzkách. Menší vzdálenost znamená menší počet segmentů směrování, což způsobí, že volání budou vypadat a hladce zvuk. Další informace najdete v informacích [o používání týmů na virtuálním počítači s Windows](teams-on-wvd.md).

## <a name="june-2020"></a>Červen 2020

Minulý měsíc představili jsme virtuální plochu Windows s Azure Resource Manager Integration ve verzi Preview. Tato aktualizace má spoustu zajímavých nových funkcí, o kterých vám řekneme. Tady je co je novinka v této verzi virtuálního klienta Windows.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Virtuální plocha Windows je teď integrovaná s Azure Resource Manager

Virtuální počítač s Windows je teď integrovaný do Azure Resource Manager. V nejnovější aktualizaci jsou nyní všechny objekty virtuálních klientů Windows Azure Resource Manager prostředky. Tato aktualizace je také integrovaná s řízením přístupu na základě role Azure (Azure RBAC). Další informace najdete v tématu [co je Azure Resource Manager?](../azure-resource-manager/management/overview.md) .

Tady je tato změna pro vás:

- Virtuální plocha Windows je teď integrovaná s Azure Portal. To znamená, že můžete všechno spravovat přímo na portálu, ale nevyžadují se žádné PowerShelly, webové aplikace ani nástroje třetích stran. Pokud chcete začít, podívejte se na náš kurz v [části Vytvoření fondu hostitelů pomocí Azure Portal](create-host-pools-azure-marketplace.md).

- Před touto aktualizací byste mohli jenom publikovat vzdálené aplikace RemoteApp a plochy pro jednotlivé uživatele. Pomocí Azure Resource Manager nyní můžete publikovat prostředky do skupin Azure Active Directory.

- Starší verze virtuálního počítače s Windows obsahovaly čtyři předdefinované role správce, které můžete přiřadit k tenantovi nebo fondu hostitelů. Tyto role jsou teď v [řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/overview.md). Tyto role můžete použít pro každý objekt Azure Resource Manager virtuální plochy Windows, který vám umožní plně funkční model delegování.

- V této aktualizaci už nemusíte spouštět Azure Marketplace nebo šablonu GitHubu opakovaně, abyste mohli rozšířit fond hostitelů. K rozšíření fondu hostitelů stačí přejít do fondu hostitelů v Azure Portal a vybrat **+ Přidat** pro nasazení dalších hostitelů relací.

- Nasazení fondu hostitelů je teď plně integrované s [galerií sdílených imagí Azure](../virtual-machines/windows/shared-image-galleries.md). Galerie sdílených imagí je samostatná služba Azure, která ukládá definice imagí virtuálních počítačů (VM), včetně správy verzí imagí. K kopírování a posílání imagí do jiných oblastí Azure pro místní nasazení můžete použít taky globální replikaci.

- Funkce monitorování, které se použily k provedení prostřednictvím PowerShellu nebo webové aplikace diagnostické služby, se teď přesunuly na Log Analytics v Azure Portal. Nyní máte dvě možnosti vizualizace vašich sestav. Můžete spouštět dotazy Kusto a používat sešity k vytváření vizuálních sestav.

- Už nebudete muset Azure Active Directory (Azure AD) vyjádřit souhlas s používáním virtuálního klienta Windows. V této aktualizaci tenant Azure AD v předplatném Azure ověřuje vaše uživatele a poskytuje pro správce ovládací prvky pro řízení přístupu na základě služby.


### <a name="powershell-support"></a>Podpora prostředí PowerShell

Do Azure PowerShell AZ Module této aktualizace jsme přidali nové rutiny AzWvd. Tento nový modul se podporuje v PowerShell Core, který běží na .NET Core.

Pokud chcete modul nainstalovat, postupujte podle pokynů v části [nastavení modulu PowerShell pro virtuální počítač s Windows](powershell-module.md).

Seznam dostupných příkazů můžete zobrazit také v [Referenční příručce k AzWvd prostředí PowerShell](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization).

Další informace o nových funkcích najdete v [našem blogovém příspěvku](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245).

### <a name="additional-gateways"></a>Další brány

Přidali jsme nový cluster brány v Jižní Africe, aby se snížila latence připojení.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams na virtuálním počítači s Windows (Preview)

Provedli jsme několik vylepšení Microsoft Teams pro virtuální počítače s Windows. Nejdůležitější je, že Windows Virtual Desktop teď podporuje zvukové a vizuální přesměrování pro volání. Přesměrování vylepšuje latenci vytvořením přímých cest mezi uživateli při volání pomocí zvuku nebo videa. Menší vzdálenost znamená menší počet segmentů směrování, což způsobí, že volání budou vypadat a hladce zvuk.

Další informace najdete v [našem blogovém příspěvku](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="client-updates"></a>Aktualizace klienta

V těchto článcích najdete informace o aktualizacích pro naše klienty pro virtuální počítače a službu Vzdálená plocha systému Windows:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="next-steps"></a>Další kroky

Přečtěte si o budoucích plánech v [Microsoft 365 plán virtuálních klientů pro Windows](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).

