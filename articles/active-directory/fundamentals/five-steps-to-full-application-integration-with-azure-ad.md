---
title: Pět kroků pro integraci všech vašich aplikací se službou Azure AD
description: Tato příručka vysvětluje, jak integrovat všechny aplikace do Azure AD. V každém kroku vyvysvětlíme hodnotu a získáte odkazy na zdroje, které budou vysvětlit technické podrobnosti.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: c75d04faf9ac59f21aad1dcd88dfe83699a11941
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057184"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>Pět kroků pro integraci všech vašich aplikací se službou Azure AD

Azure Active Directory (Azure AD) je cloudová služba pro správu identit a přístupu založená na Microsoftu. Azure AD poskytuje zabezpečená řešení pro ověřování a autorizaci, aby mohli zákazníci, partneři a zaměstnanci přistupovat k aplikacím, které potřebují. Pomocí služby Azure AD, [podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/overview), [vícefaktorového ověřování](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks), [jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)a [automatického zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) usnadňuje a zabezpečuje správu identit a přístupu.

Pokud má vaše společnost předplatné Microsoft 365, pravděpodobně již službu Azure AD [používáte](https://docs.microsoft.com/office365/enterprise/about-office-365-identity) . Službu Azure AD je ale možné použít pro všechny vaše aplikace a [centralizaci správy aplikací](https://docs.microsoft.com/azure/active-directory/manage-apps/common-scenarios) můžete využívat stejné funkce, nástroje a zásady správy identit napříč celým portfoliem aplikace. Tím zajistíte sjednocené řešení, které zlepšuje zabezpečení, snižuje náklady, zvýší produktivitu a umožní vám zajistit dodržování předpisů. Pak získáte vzdálený přístup k místním aplikacím.

Tato příručka vysvětluje, jak integrovat všechny aplikace do Azure AD. V každém kroku vyvysvětlíme hodnotu a získáte odkazy na zdroje, které budou vysvětlit technické podrobnosti. Tyto kroky prezentujeme v pořadí, které doporučujeme. Můžete ale přejít na libovolnou část procesu, abyste mohli začít pracovat s tím, co pro vás přináší nejvíc hodnot.

Další materiály k tomuto tématu, včetně podrobných dokumentů White Paper v obchodních procesech, najdete v našich [materiálech k migraci aplikací na Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) stránku.

## <a name="1-use-azure-ad-for-new-applications"></a>1. použití služby Azure AD pro nové aplikace

Nejprve se zaměřte na nově načtené aplikace. Když vaše firma začne používat novou aplikaci, [přidejte ji do svého tenanta Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) hned. Nastavte zásady společnosti tak, aby bylo možné přidat nové aplikace do služby Azure AD jako standardní postup ve vaší organizaci. To má minimální dopad na stávající obchodní procesy a umožňuje prozkoumat a prokázat hodnotu, kterou získáte při integraci aplikací, aniž byste museli měnit způsob, jakým uživatelé obchodují ve vašem prostředí ještě dnes.

Azure Active Directory (Azure AD) obsahuje galerii obsahující tisíce předem integrovaných aplikací, které usnadňují začátek práce. Můžete [Přidat aplikaci Galerie do vaší organizace Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) s podrobnými [kurzy](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) pro integraci s oblíbenými aplikacemi, jako jsou:

- [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-tutorial)
- [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)
- [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-tutorial)
- [AWS](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial)
- [Slack](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial)

Kromě toho můžete [integrovat aplikace, které nejsou v galerii](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app), včetně jakékoli aplikace, která již existuje ve vaší organizaci, nebo jakékoli aplikace třetí strany od dodavatele, který ještě není součástí Galerie Azure AD. [Aplikaci můžete do galerie přidat](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-app-gallery-listing) také v případě, že tam není.

Nakonec můžete integrovat i aplikace, které vyvíjíte interně. Tento postup je popsaný v kroku 5 tohoto průvodce.

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. určení používání stávajících aplikací a stanovení priorit práce

V dalším kroku zjistíte, že se zaměstnanci aplikace často používají, a určete prioritu práce při jejich integraci s Azure AD.

Můžete začít pomocí [nástrojů Cloud discovery](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) Microsoft Cloud App Security&#39;s a vyhledat a spravovat &quot; stínové &quot; IT ve vaší síti (tj. aplikace, které nespravuje oddělení IT). [Pomocí rozšířené ochrany před internetovými útoky v programu Microsoft Defender](https://docs.microsoft.com/cloud-app-security/wdatp-integration) můžete zjednodušit a rozšířit proces zjišťování.

Kromě toho můžete pomocí [sestavy aktivita aplikace AD FS](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) v Azure Portal zjistit všechny AD FS aplikace ve vaší organizaci, počet jedinečných uživatelů, kteří se k nim přihlásili, a kompatibilitu při jejich integraci s Azure AD.

Po zjištění stávající krajiny budete chtít [vytvořit plán a nastavit](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) prioritu aplikací s nejvyšší prioritou k integraci. V některých ukázkových dotazech se můžete zeptat na tento proces:

- Které aplikace se používají nejčastěji?
- Které jsou nejrizikovějších?
- Které aplikace budou v budoucnu vyřazené, takže se přesun nepotřebuje?
- Které aplikace musí zůstat v místním prostředí a nelze je přesunout do cloudu?

Až budou všechny vaše aplikace integrované a už nebudete spoléhat na více řešení identity, uvidíte největší výhody a úspory nákladů. Při přesunu stupňovaný k tomuto cíli ale budete mít snazší správu identit a zvýšené zabezpečení. Tuto dobu chcete použít k určení priorit práce a rozhodování, co je vhodné pro vaši situaci.

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. Integrujte aplikace, které spoléhají na jiné zprostředkovatele identity.

Během procesu zjišťování jste možná našli aplikace, které nesleduje IT oddělení, což ponechá vaše data a prostředky v ohrožení. Můžete mít i aplikace, které používají alternativní řešení identit, včetně Active Directory Federation Services (AD FS) (ADFS) nebo jiných zprostředkovatelů identity. Vezměte v úvahu, jak můžete konsolidovat správu identit a přístupu, abyste ušetřili peníze a zvýšili zabezpečení. Snižte počet řešení identity, která budete mít:

- Ušetříte si peníze tím, že Eliminujte nutnost místního zřizování a ověřování uživatelů a také licenční poplatky placené jiným poskytovatelům cloudových identit pro stejnou službu.
- Snižte režijní náklady na správu a zapněte užší zabezpečení s menším počtem redundancí v procesu správy identit a přístupu.
- Umožněte zaměstnancům zabezpečený přístup pro jednotné přihlašování ke všem aplikacím, které potřebují prostřednictvím [portálu Mojeapl](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).
- Zvyšte si inteligentní informace o službách Azure AD&#39;s [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) , jako je podmíněný přístup, tím, že zvýšíte množství dat, která získá z využití vaší aplikace, a rozšíříte své výhody na nově přidané aplikace.

Publikovali jsme doprovodné materiály pro správu podnikových procesů integrování aplikací se službou Azure AD, včetně [plakátu](https://aka.ms/AppOnePager) a [prezentace](https://aka.ms/AppGuideline) , pomocí kterých mohou vlastníci podniku a aplikace dělat a zajímat. Tyto ukázky můžete upravit pomocí vlastního brandingu a publikovat je ve vaší organizaci prostřednictvím portálu společnosti, bulletinu nebo jiného média, jak budete postupovat na dokončení tohoto procesu.

Dobrým místem, kde začít, je vyhodnocování používání Active Directory Federation Services (AD FS) (ADFS). Řada organizací používá službu ADFS k ověřování s aplikacemi SaaS, vlastními obchodními aplikacemi a aplikacemi pro Office 365 a Azure AD:

![Diagram zobrazuje místní aplikace, obchodní aplikace, aplikace SaaS a prostřednictvím služby Azure AD 365, které se připojují s tečkovanými řádky do Active Directory a AD FS.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

Tuto konfiguraci můžete upgradovat tak, že v [Azure AD nahradíte službu AD FS jako střed](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) svého řešení pro správu identit. Díky tomu je možné přihlašovat se ke každé aplikaci, ke které mají vaši zaměstnanci přístup, a usnadnit tak zaměstnancům, aby si našli jakékoli obchodní aplikace, které potřebují prostřednictvím [portálu Mojeapl](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access), a navíc k ostatním výhodám uvedeným výše.

![Diagram zobrazuje místní aplikace přes službu Active Directory a AD FS, obchodní aplikace, aplikace SaaS a sadu Office 365, které se připojují s tečkovanými čarami do Azure Active Directory.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

Jakmile se služba Azure AD stala centrálním poskytovatelem identity, možná bude možné přepínat ze služby AD FS zcela, ale nepoužívat federované řešení. Aplikace, které dřív používaly ADFS pro ověřování, můžou teď používat jenom Azure AD.

![Diagram zobrazuje místní, obchodní aplikace, aplikace SaaS a Office 365, které se připojují s tečkami na Azure Active Directory. Služba Active Directory a AD FS není k dispozici.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

V Azure AD můžete také migrovat aplikace, které používají jiného cloudového poskytovatele identity. Vaše organizace může mít několik řešení pro správu přístupu k identitě (IAM). Migrace na jednu infrastrukturu Azure AD je příležitostí k omezení závislostí na licencích IAM (v místním prostředí nebo v cloudu) a na náklady na infrastrukturu. V případech, kdy jste už mohli platit za Azure AD prostřednictvím licencí M365, neexistuje žádný důvod, proč platíte přidané náklady na jiné řešení IAM.

## <a name="4-integrate-on-premises-applications"></a>4. Integrace místních aplikací

Tradičně byly aplikace zabezpečeny tím, že umožňují přístup pouze při připojení k podnikové síti. Ve stále propojeném světě chceme mít přístup k aplikacím pro zákazníky, partnery a zaměstnance, a to bez ohledu na to, kde jsou na světě. [Azure proxy aplikací služby AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) (AppProxy) je funkce služby Azure AD, která připojuje vaše stávající místní aplikace do služby Azure AD a nevyžaduje, abyste zachovali hraniční servery nebo další další infrastrukturu.

![Diagram zobrazuje službu proxy aplikací v akci. Uživatel má přístup https://sales.contoso.com a jeho žádost se přesměruje prostřednictvím " https://sales-contoso.msappproxy.net " v Azure Active Directory na místní adresu " http://sales ".](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

Můžete použít [kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) pro povolení proxy aplikací a Přidání místní aplikace do tenanta služby Azure AD.

Kromě toho můžete integrovat řadiče pro doručování aplikací, jako je F5 Big-IP APM nebo Zscaler Private Access. Integrací těchto funkcí se službou Azure AD získáte moderní ověřování a správu identit služby Azure AD společně se správou provozu a funkcemi zabezpečení partnerského produktu. Toto řešení zavoláme [zabezpečeným hybridním přístupem](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access). Pokud dnes použijete některou z následujících služeb, máme k dispozici kurzy, které vás provedou integrací se službou Azure AD.

- [Přístup k podnikovým aplikacím Akamai (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)
- [Řadič pro doručování aplikací pro Citrix (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial) (dříve označovaný jako Citrix NetScaler)
- [S funkcí F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)
- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)

## <a name="5-integrate-apps-your-developers-build"></a>5. Integrace aplikací, které vaše vývojáři sestaví

Pro aplikace, které jsou vytvořené v rámci vaší společnosti, můžou vývojáři implementovat ověřování a autorizaci pomocí [platformy Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/) . Aplikace integrované s platformou se [registrují ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app) a spravují se stejně jako jakékoli jiné aplikace v portfoliu.

Vývojáři můžou používat platformu pro aplikace interního použití i pro aplikace pro zákazníky a existují i další výhody, které se s využitím platformy dodávají. [Knihovny Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview), které jsou součástí platformy, umožňují vývojářům povolit moderní prostředí, jako je vícefaktorové ověřování, a použití klíčů zabezpečení pro přístup ke svým aplikacím, aniž by je museli implementovat sami sebe. Kromě toho můžou aplikace integrované s platformou Microsoft Identity získat přístup k [Microsoft Graph](https://docs.microsoft.com/azure/active-directory/develop/microsoft-graph-intro) – jedná se o sjednocený koncový bod rozhraní API, který poskytuje Microsoft 365 data, která popisují vzory produktivity, identity a zabezpečení v organizaci. Vývojáři můžou tyto informace využít k implementaci funkcí, které zvyšují produktivitu pro vaše uživatele. Například určením osob, se kterými uživatel interaktivně pracoval, a zpřístupnění je v uživatelském rozhraní aplikace&#39;s.

Máme [video řady](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX) , které poskytují ucelený Úvod k platformě a také [mnoho ukázek kódu](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) v podporovaných jazycích a platformách.

## <a name="next-steps"></a>Další kroky

- [Prostředky pro migraci aplikací na Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)
