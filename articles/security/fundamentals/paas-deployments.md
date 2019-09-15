---
title: Osvědčené postupy pro nasazení zabezpečených PaaS – Microsoft Azure
description: Seznamte se s osvědčenými postupy pro navrhování, sestavování a správu zabezpečených cloudových aplikací v Azure a pochopte výhody zabezpečení PaaS oproti jiným modelům cloudových služeb.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: techlake
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 67a34b2b0a997a118cb2fe1b99de04bd58063307
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999043"
---
# <a name="securing-paas-deployments"></a>Zabezpečení nasazení PaaS

Tento článek poskytuje informace, které vám pomůžou:

- Pochopení výhod zabezpečení hostujících aplikací v cloudu
- Vyhodnoťte výhody zabezpečení platforem jako služby (PaaS) oproti jiným modelům cloudových služeb.
- Změna zaměření na zabezpečení ze sítě na přístup k hraničnímu přístupu zaměřenému na identitu
- Implementace obecných doporučení pro osvědčené postupy zabezpečení PaaS

[Vývoj zabezpečených aplikací v Azure](abstract-develop-secure-apps.md) je obecným průvodcem pro bezpečnostní otázky a kontroly, které byste měli zvážit v každé fázi životního cyklu vývoje softwaru při vývoji aplikací pro Cloud.

## <a name="cloud-security-advantages"></a>Výhody zabezpečení cloudu
Existují výhody zabezpečení v cloudu. V místním prostředí organizace pravděpodobně mají nesplnění zodpovědnosti a omezené prostředky, které jsou k dispozici pro investici do zabezpečení, což vytváří prostředí, ve kterém můžou útočníci zneužít ohrožení zabezpečení ve všech vrstvách.

![Výhody zabezpečení cloudového prostředí](./media/paas-deployments/advantages-of-cloud.png)

Organizace můžou zlepšit detekci hrozeb a doby odezvy pomocí cloudových možností zabezpečení a cloudových funkcí poskytovatele.  Díky posunutí zodpovědností poskytovateli cloudu můžou organizace získat větší pokrytí zabezpečení, které jim umožní znovu přidělit prostředky a rozpočet zabezpečení jiným obchodním prioritám.

## <a name="division-of-responsibility"></a>Rozdělení odpovědnosti
Je důležité pochopit rozdělení zodpovědnosti mezi vámi a společností Microsoft. V místním prostředí můžete celý zásobník vlastnit, ale při přesunu do cloudu se některé zodpovědnosti přenesou do Microsoftu. Následující matice zodpovědnosti ukazuje oblasti zásobníku v nasazení SaaS, PaaS a IaaS, na které zodpovídáte za vás a Microsoft zodpovídá za to.

![Zóny odpovědnosti](./media/paas-deployments/responsibility-zones.png)

Pro všechny typy cloudového nasazení vlastníte svá data a identity. Zodpovídáte za ochranu zabezpečení vašich dat a identit, místních prostředků a součástí cloudu, které ovládáte (které se liší podle typu služby).

Odpovědnosti, které se vždycky uchovávají, bez ohledu na typ nasazení:

- Data
- Koncové body
- Účet
- Správa přístupu

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Výhody zabezpečení pro model cloudové služby PaaS
Pomocí stejné matrice odpovědnosti se podíváme na výhody zabezpečení nasazení Azure PaaS i v místním prostředí.

![Výhody zabezpečení PaaS](./media/paas-deployments/advantages-of-paas.png)

Od konce zásobníku má fyzická infrastruktura dopad na běžná rizika a zodpovědnosti společnosti Microsoft. Vzhledem k tomu, že je Microsoft Cloud nepřetržitě monitorovaný Microsoftem, je obtížné ho zaútočit. Neznamená to, že by útočník mohl jako cíl vydávat Cloud od Microsoftu. Pokud má útočník velký počet peněz a prostředků, může se útočník přesunout na jiný cíl.  

Uprostřed zásobníku nedochází k žádnému rozdílu mezi nasazením PaaS a místním prostředím. V aplikační vrstvě a vrstvě správy účtu a přístupu máte podobná rizika. V části Další kroky tohoto článku Vás provedeme s doporučenými postupy pro odstranění těchto rizik nebo jejich minimalizaci.

V horní části zásobníku, zásad správného řízení dat a Rights Management můžete využít jedno riziko, které je možné zmírnit správou klíčů. (Správa klíčů se zabývá osvědčenými postupy.) I když je Správa klíčů další zodpovědností, máte oblasti v nasazení PaaS, které už nemusíte spravovat, abyste mohli prostředky přesunout do správy klíčů.

Platforma Azure taky poskytuje silnou DDoS ochranu pomocí různých síťových technologií. Všechny typy metod ochrany DDoS založené na síti ale mají omezení na jednotlivé odkazy a datové centrum. Abyste se vyhnuli dopadu velkých útoků DDoS, můžete využít výhod základních cloudových funkcí Azure, které vám umožní rychle a automaticky škálovat a chránit před útoky DDoS. Podrobnější informace o tom, jak to můžete udělat, najdete v článcích doporučených postupů.

## <a name="modernizing-the-defenders-mindset"></a>Modernizaci Defenderu místo
Díky nasazením PaaS se v celkovém přístupu k zabezpečení dokončí Shift. Posunete se od potřeby, abyste mohli řídit všechno, co si sami sdílíte s Microsoftem.

Dalším významným rozdílem mezi PaaS a tradičními místními nasazeními je nové zobrazení toho, co definuje primární hraniční zabezpečení. V minulosti byl primární místní hraniční zabezpečení vaší sítí a většina místních návrhů zabezpečení síť používá jako primární Pivot zabezpečení. Pro nasazení PaaS je lepší pracovat tím, že považujte identitu za primární hraniční zabezpečení.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Přijmout zásadu identity jako primární hraniční zabezpečení
Jednou z pěti podstatných charakteristik cloud computingu je široce přístup k síti, který je zaměřený na to, že se v síti nehodí. Cílem mnoha cloud computingu je dovolit uživatelům přístup k prostředkům bez ohledu na jejich umístění. Pro většinu uživatelů se jejich umístění nachází někde na internetu.

Následující obrázek ukazuje, jak se hraniční obvodu vyvinulo z hraniční sítě do hraničního okruhu identity. Zabezpečení se zasahuje do ochrany vaší sítě a další informace o tom, jak chrání vaše data, a také o správě zabezpečení vašich aplikací a uživatelů. Hlavním rozdílem je, že chcete předávat zabezpečení směrem nahoru k co důležitému pro vaši společnost.

![Identita jako nové hraniční zabezpečení](./media/paas-deployments/identity-perimeter.png)

Zpočátku služby Azure PaaS (například webové role a Azure SQL) poskytují minimální nebo žádné tradiční obrany hraniční sítě. Bylo pochopitelné, že účel elementu byl zpřístupněn Internetu (webová role) a že ověřování poskytuje nové hraniční úložiště (například objekt BLOB nebo Azure SQL).

Moderní postupy zabezpečení předpokládají, že nežádoucí osoba porušil hraniční síť. Proto byly moderní postupy obrany přesunuty do identity. Organizace musí zřídit hraniční zabezpečení na základě identity se silným ověřováním a hygienou autorizace (osvědčené postupy).

Zásady a vzory pro hraniční sítě byly k dispozici po desetiletí. V takovém případě má odvětví poměrně méně zkušeností s používáním identity jako primárního hraničního zabezpečení. V takovém případě jsme shromáždili dostatek zkušeností, aby bylo možné poskytnout některá obecná doporučení, která jsou prověřena v poli a platí pro téměř všechny služby PaaS Services.

Níže jsou uvedené osvědčené postupy pro správu hraničního řízení identity.

**Osvědčené postupy**: Zabezpečte své klíče a přihlašovací údaje a zabezpečte nasazení PaaS.   
**Podrobnosti**: Ztráta klíčů a přihlašovacích údajů je běžný problém. Můžete použít centralizované řešení, kde klíče a tajné klíče mohou být uloženy v modulech hardwarového zabezpečení (HSM). [Azure Key Vault](../../key-vault/key-vault-overview.md) chrání klíče a tajné kódy tím, že šifruje ověřovací klíče, klíče účtu úložiště, šifrovací klíče dat, soubory PFX a hesla pomocí klíčů chráněných pomocí HSM.

**Osvědčené postupy**: Neumísťujte přihlašovací údaje a další tajné kódy do zdrojového kódu nebo GitHubu.   
**Podrobnosti**: Jediná věc, kterou neztratí vaše klíče a přihlašovací údaje, má přístup k nim neoprávněná osoba. Útočníci můžou využívat výhod technologií robota k nalezení klíčů a tajných kódů uložených v úložištích kódu, jako je GitHub. Neumísťujte klíč a tajné klíče do těchto veřejných úložišť kódu.

**Osvědčené postupy**: Chraňte své rozhraní pro správu virtuálních počítačů na hybridních PaaS a IaaSch službách pomocí rozhraní pro správu, které vám umožní přímo spravovat tyto virtuální počítače.   
**Podrobnosti**: Můžete použít protokoly vzdálené správy, jako jsou [SSH](https://en.wikipedia.org/wiki/Secure_Shell), [RDP](https://support.microsoft.com/kb/186607)a [Vzdálená komunikace PowerShellu](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) . Obecně doporučujeme, abyste nepovolili přímý vzdálený přístup k virtuálním počítačům z Internetu.

Pokud je to možné, použijte alternativní přístupy, jako je použití virtuálních privátních sítí ve službě Azure Virtual Network. Pokud nejsou k dispozici alternativní přístupy, nezapomeňte použít složitá přístupová hesla a dvojúrovňové ověřování (například [Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)).

**Osvědčené postupy**: Používejte silné ověřování a platformy pro autorizaci.   
**Podrobnosti**: Používejte federované identity ve službě Azure AD místo vlastních uživatelských úložišť. Pokud používáte federované identity, využijte výhod přístupu založeného na platformě a delegujete správu autorizovaných identit pro vaše partnery. Federovaný přístup k identitě je obzvláště důležitý, když se zaměstnanci ukončí a že je potřeba odrážet informace prostřednictvím několika identit a autorizačních systémů.

Místo vlastního kódu použijte mechanismy ověřování a autorizace dodané platformou. Důvodem je, že vývoj vlastního ověřovacího kódu může být náchylný k chybám. Většina vašich vývojářů není odborníkem na zabezpečení a pravděpodobně si nebude vědoma odlišností a nejnovějšího vývoje v ověřování a autorizaci. Komerční kód (například od společnosti Microsoft) je často široce prověřený zabezpečením.

Používejte dvojúrovňové ověřování. Dvojúrovňové ověřování je aktuální standard pro ověřování a autorizaci, protože brání slabým místům zabezpečení vyplývajícím z uživatelského jména a hesla ověřování. Přístup k rozhraním pro správu Azure (portál nebo vzdálené prostředí PowerShell) a službám s přístupem k zákazníkům by měly být navržené a nakonfigurované pro používání [Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication).

Používejte standardní ověřovací protokoly, jako je OAuth2 a Kerberos. Tyto protokoly byly výrazně zkontrolovány a jsou pravděpodobně implementovány jako součást knihoven platforem pro ověřování a autorizaci.

## <a name="use-threat-modeling-during-application-design"></a>Použití modelování hrozeb během návrhu aplikace
[Životní cyklus vývoje zabezpečení](https://www.microsoft.com/en-us/sdl) společnosti Microsoft určuje, že týmy by se měly během fáze návrhu zapojit do procesu nazývaného modelování hrozeb. Z důvodu usnadnění tohoto procesu společnost Microsoft vytvořila [Threat Modeling Tool SDL](/azure/security/azure-security-threat-modeling-tool). Modelování návrhu aplikace a vyčíslení hrozeb pro [Rozteč](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) napříč všemi hranicemi vztahů důvěryhodnosti může zachytit chyby návrhu včas.

V následující tabulce jsou uvedeny hrozby pro rozteč a jejich ukázková omezení, která používají funkce Azure. Tato zmírnění rizika nebudou v každé situaci fungovat.

| Nebezpečí | Vlastnost zabezpečení | Potenciální omezení na platformě Azure |
| --- | --- | --- |
| Falšování identity | Ověřování | Vyžadovat připojení HTTPS. |
| Manipulace | Integrita | Ověřte certifikáty SSL. |
| Popírání odpovědnosti | Neodmítnutí | Povolte [monitorování a diagnostiku](/azure/architecture/best-practices/monitoring)Azure. |
| Zpřístupnění informací | Chovávat | Šifrování citlivých dat v klidovém formátu pomocí [certifikátů služby](/rest/api/appservice/certificates). |
| Přerušení služby | Dostupnost | Monitoruje metriky výkonu pro potenciální podmínky odepření služeb. Implementujte filtry připojení. |
| Zvýšení oprávnění | Authorization | Použijte [Privileged Identity Management](/azure/active-directory/privileged-identity-management/subscription-requirements). |

## <a name="develop-on-azure-app-service"></a>Vývoj na Azure App Service
[Azure App Service](/azure/app-service/overview) je nabídka PaaS, která umožňuje vytvářet webové a mobilní aplikace pro jakoukoli platformu nebo zařízení a připojovat se k datům kdekoli, v cloudu i v místním prostředí. App Service zahrnují webové a mobilní funkce, které byly dříve dodávány samostatně jako Azure websites a Azure Mobile Services. Obsahuje také nové možnosti pro automatizaci obchodních procesů a hostování cloudových rozhraní API. Jako jediná integrovaná služba App Service přináší bohatou sadu možností pro webové, mobilní a integrační scénáře.

Níže jsou uvedené osvědčené postupy pro používání App Service.

**Osvědčené postupy**: [Ověřování prostřednictvím Azure Active Directory](/azure/app-service/overview-authentication-authorization).   
**Podrobnosti**: App Service poskytuje službě OAuth 2,0 pro vašeho poskytovatele identity. OAuth 2,0 se zaměřuje na jednoduchost vývojáře v klientských počítačích a poskytuje konkrétní autorizační toky pro webové aplikace, desktopové aplikace a mobilní telefony. Azure AD používá OAuth 2,0, který vám umožní autorizovat přístup k mobilním a webovým aplikacím.

**Osvědčené postupy**: Omezte přístup na základě nutnosti znát a nejnižší principy zabezpečení oprávnění.   
**Podrobnosti**: Omezení přístupu je nezbytné pro organizace, které chtějí vyhovět zásadám zabezpečení pro přístup k datům. Pomocí RBAC můžete přiřadit oprávnění uživatelům, skupinám a aplikacím v určitém oboru. Další informace o tom, jak udělit uživatelům přístup k aplikacím, najdete v tématu [Začínáme se správou přístupu](/azure/role-based-access-control/overview).

**Osvědčené postupy**: Chraňte své klíče.   
**Podrobnosti**: Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče, které využívají cloudové aplikace a služby. Pomocí Key Vault můžete šifrovat klíče a tajné klíče (například ověřovací klíče, klíče účtu úložiště, šifrovací klíče dat). Soubory PFX a hesla) pomocí klíčů chráněných moduly hardwarového zabezpečení (HSM). Pro zvýšené bezpečí můžete klíče importovat nebo generovat v modulech HSM. Další informace najdete v tématu [Azure Key Vault](/azure/key-vault/key-vault-overview) . Pomocí Key Vault můžete také spravovat certifikáty TLS s automatickým obnovením.

**Osvědčené postupy**: Omezte příchozí IP adresy příchozích zdrojů.   
**Podrobnosti**: [App Service Environment](/azure/app-service/environment/intro) má funkci Integrace virtuální sítě, která vám pomůže omezit příchozí IP adresy příchozích dat prostřednictvím skupin zabezpečení sítě. Virtuální sítě umožňují umístit prostředky Azure do sítě, ve které není Internet, směrovatelný síť, ke které budete řídit přístup. Další informace najdete v tématu [integrace aplikace do služby Azure Virtual Network](/azure/app-service/web-sites-integrate-with-vnet).

**Osvědčené postupy**: Monitorujte stav zabezpečení vašich App Servicech prostředí.   
**Podrobnosti**: Pomocí Azure Security Center můžete monitorovat App Service prostředí. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří [doporučení](../../security-center/security-center-virtual-machine-protection.md) , která vás provedou procesem konfigurace potřebných ovládacích prvků.

> [!NOTE]
> App Service monitorování je ve verzi Preview a je k dispozici pouze na [úrovni Standard](/azure/security-center/security-center-pricing) Security Center.
>
>

## <a name="install-a-web-application-firewall"></a>Instalace firewallu webových aplikací
Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Mezi tyto běžné typy zneužití patří mimo jiné například útoky prostřednictvím injektáže SQL nebo skriptování mezi weby. Předcházet takovým útokům v kódu aplikace může být náročné a může vyžadovat pečlivou údržbu, opravy a monitorování mnoha vrstev topologie aplikace. Centralizovaný firewall webových aplikací značně zjednodušuje správu zabezpečení a nabízí správcům lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Stávající aplikační brány je možné jednoduše převést na aplikační brány doplněné webovým aplikačním firewallem.

[Firewall webových aplikací (WAF)](/azure/frontdoor/waf-overview) je funkce Application Gateway, která poskytuje centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení. WAF je založen na pravidlech ze [základních sad pravidel OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,0 nebo 2.2.9.

## <a name="monitor-the-performance-of-your-applications"></a>Sledování výkonu aplikací
Monitorování je proces shromažďování a analýzy dat pro určení výkonu, stavu a dostupnosti aplikace. Efektivní strategie monitorování vám pomůže porozumět provozu jednotlivých komponent vaší aplikace. Pomůže vám prodloužit dobu provozu tím, že vás upozorní na kritické problémy, abyste je mohli vyřešit před tím, než dojde k problémům. Pomůže vám taky detekovat anomálie, které by mohly souviset se zabezpečením.

Pomocí [Azure Application Insights](https://azure.microsoft.com/documentation/services/application-insights) můžete monitorovat dostupnost, výkon a využití vaší aplikace, ať už jsou hostované v cloudu nebo místně. Pomocí Application Insights můžete rychle identifikovat a diagnostikovat chyby v aplikaci, aniž byste čekali na jejich nahlášení uživatele. Na základě shromažďovaných informací můžete informovaně rozhodovat o údržbě a vylepšení vaší aplikace.

Application Insights obsahuje řadu nástrojů pro interakci se shromažďovanými daty. Application Insights ukládá svoje data do běžného úložiště. Může využít výhod sdílených funkcí, jako jsou výstrahy, řídicí panely a hloubková analýza, pomocí dotazovacího jazyka Kusto.

## <a name="perform-security-penetration-testing"></a>Provádění testování průniku zabezpečení
Ověřování ochrany zabezpečení je důležité jako testování jakékoli jiné funkce. Udělejte [testování průniku](pen-testing.md) jako standardní část procesu sestavení a nasazení. Naplánujte pravidelné testy zabezpečení a kontrolu ohrožení zabezpečení u nasazených aplikací a sledujte otevřené porty, koncové body a útoky.

Fuzzy testuje je metoda pro hledání chyb programu (chyby kódu) tím, že poskytuje chybná vstupní data do programových rozhraní (vstupní body), která analyzují a spotřebovávají tato data. [Zjišťování rizik zabezpečení společnosti Microsoft](https://www.microsoft.com/en-us/security-risk-detection/) je cloudový nástroj, který můžete použít k vyhledání chyb a dalších chyb zabezpečení v softwaru před nasazením do Azure. Nástroj je navržený tak, aby zachytával chyby zabezpečení před nasazením softwaru, takže nemusíte opravovat chybu, zabývat se havárií nebo reagovat na útok po vydání softwaru.


## <a name="next-steps"></a>Další kroky
V tomto článku jsme se zaměřili na výhody zabezpečení nasazení Azure PaaS a osvědčené postupy zabezpečení pro cloudové aplikace. Dále se seznamte s doporučenými postupy pro zabezpečení webových a mobilních řešení PaaS pomocí konkrétních služeb Azure. Začneme s Azure App Service, Azure SQL Database a Azure SQL Data Warehouse a Azure Storage. V případě, že budou k dispozici články o doporučených postupech pro jiné služby Azure, budou odkazy uvedeny v následujícím seznamu:

- [Azure App Service](paas-applications-using-app-services.md)
- [Azure SQL Database a Azure SQL Data Warehouse](paas-applications-using-sql.md)
- [Azure Storage](paas-applications-using-storage.md)
- Azure Cache for Redis
- Azure Service Bus
- Firewall webových aplikací

V tématu [vývoj zabezpečených aplikací v Azure](abstract-develop-secure-apps.md) najdete bezpečnostní otázky a kontroly, které byste měli zvážit v každé fázi životního cyklu vývoje softwaru při vývoji aplikací pro Cloud.

V článku [osvědčené postupy a vzory zabezpečení Azure](best-practices-and-patterns.md) najdete v tématu Doporučené postupy zabezpečení pro použití při navrhování, nasazování a správě cloudových řešení pomocí Azure.

K dispozici jsou následující prostředky, které poskytují obecnější informace o zabezpečení Azure a souvisejících službách Microsoftu:
* [Blog týmu pro zabezpečení Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – aktuální informace o nejnovější verzi v zabezpečení Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – kde můžou být chyby zabezpečení Microsoftu, včetně problémů s Azure, nahlášené nebo prostřednictvím e-mailu.secure@microsoft.com


