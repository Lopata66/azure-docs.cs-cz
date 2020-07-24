---
title: Co je Azure API for FHIR? – Azure API pro FHIR
description: Azure API for FHIR umožňuje rychlé výměny dat prostřednictvím rozhraní API FHIR. Ingestujte, spravujte a zachovejte chráněné informace o stavu ve službě Managed Cloud Service.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 72e199e45047e1b425b2587c6b4028efb84060df
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087437"
---
# <a name="what-is-azure-api-for-fhirreg"></a>Co je Azure API pro FHIR &reg; ?

Azure API for FHIR umožňuje rychlou výměnu dat prostřednictvím rozhraní API pro rychlé provázání zdravotní součinnosti (FHIR®), které zajišťuje spravovaná nabídka na platformě jako službu (PaaS) v cloudu. Pro všechny uživatele, kteří pracují s [daty o stavu](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html) , usnadňuje ingestování, správu a uchovávání chráněných informací o stavu v cloudu: 

- Spravovaná služba FHIR zřízená v cloudu v řádu minut 
- FHIR koncový bod na podnikové úrovni na základě® v Azure pro přístup k datům a úložiště ve formátu FHIR®
- Vysoký výkon, nízká latence
- Zabezpečená správa chráněných dat o stavu (FÍ) v kompatibilním cloudovém prostředí
- Inteligentní na FHIR pro mobilní a webové implementace
- Správa vlastních dat ve velkém měřítku s využitím Access Control na základě rolí (RBAC)
- Sledování protokolu auditu pro přístup, vytváření, úpravy a čtení v rámci každého úložiště dat

Azure API pro FHIR umožňuje vytvořit a nasadit službu FHIR během několika minut a využít tak Elastické škálování cloudu.  Platíte jenom za propustnost a úložiště, které potřebujete. Služby Azure, které Power Azure API for FHIR jsou navržené tak, aby bez ohledu na velikost datových sad, které spravujete, byly navrženy pro zajištění rychlého výkonu.

Rozhraní FHIR API a kompatibilní úložiště dat vám umožní bezpečně se připojit a pracovat s jakýmkoli systémem, který využívá rozhraní FHIR API.  Společnost Microsoft v rámci nabídky PaaS využívá operace, údržbu, aktualizace a požadavky na dodržování předpisů, takže můžete uvolnit vlastní provozní a vývojové prostředky. 

Následující video představuje přehled rozhraní Azure API pro FHIR:

>[!VIDEO https://www.youtube.com/embed/5vS7Iq9vpXE]

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>Využití síly vašich dat v FHIR

Obor zdravotnictví rychle transformuje data o stavu na nově vznikajícím standardu [FHIR &reg; ](https://hl7.org/fhir) (rychlé provázání prostředků pro interoperabilitu zdravotní péče). FHIR umožňuje robustní a rozšiřitelný datový model se standardizovanou sémantikou a výměnou dat, který umožňuje vzájemnou spolupráci všech systémů využívajících FHIR.  Transformace dat na FHIR umožňuje rychle připojit stávající zdroje dat, jako jsou například systémy záznamů o prostředcích a výzkumné databáze. FHIR také umožňuje rychlé výměny dat v moderních implementacích vývoje mobilních aplikací a webů. Nejdůležitější je, že FHIR může zjednodušit přijímání dat a zrychlit vývoj pomocí nástrojů pro analýzu a strojového učení.  

### <a name="securely-manage-health-data-in-the-cloud"></a>Bezpečná Správa dat o stavu v cloudu

Rozhraní API Azure pro FHIR umožňuje výměnu dat prostřednictvím konzistentních rozhraní API RESTful, FHIR založených na specifikaci změněného HL7 FHIR. Díky spravované nabídce PaaS v Azure poskytuje také škálovatelné a zabezpečené prostředí pro správu a ukládání chráněných dat o stavu (FÍ) v nativním formátu FHIR.  

### <a name="free-up-your-resources-to-innovate"></a>Uvolněte své prostředky pro inovace

Mohli byste investovat prostředky, které vytvářejí a spouštějí vlastní službu FHIR, ale s rozhraním API Azure pro FHIR Microsoft využívá úlohy operací, údržby, aktualizací a dodržování předpisů, které vám umožní uvolnit své vlastní provozní a vývojové prostředky.

### <a name="enable-interoperability-with-fhir"></a>Povolení interoperability s FHIR

Použití rozhraní API Azure pro FHIR umožňuje připojit se k libovolnému systému, který využívá rozhraní API FHIR pro čtení, zápis, hledání a další funkce.  Dá se použít jako výkonný nástroj pro konsolidaci, normalizaci a použití strojového učení s klinickými daty z elektronických záznamů o zdravotním prostředí, řídicích panelů clinician a pacientů, programů pro vzdálené monitorování nebo s databázemi mimo váš systém, které mají rozhraní FHIR API.

### <a name="control-data-access-at-scale"></a>Řízení přístupu k datům ve velkém měřítku

Data se řídí. Access Control na základě rolí (RBAC) umožňuje spravovat způsob ukládání a přístupu k datům.  Zajištění zvýšeného zabezpečení a snížení administrativních úloh, určíte, kdo má přístup k datovým sadám, které vytvoříte, na základě definic rolí, které vytvoříte pro vaše prostředí.  

### <a name="audit-logs-and-tracking"></a>Protokoly a sledování auditu 

Pomocí integrovaných protokolů auditu rychle Sledujte, kde se data procházejí. Sledujte přístup, vytváření, úpravy a čtení v rámci každého úložiště dat.

### <a name="secure-your-data"></a>Zabezpečení dat

Chraňte svůj FÍ pomocí neparalelního analytického zabezpečení.  Vaše data jsou izolovaná do jedinečné databáze na instanci rozhraní API a chráněná s převzetím služeb při selhání ve více oblastech. Rozhraní Azure API pro FHIR implementuje pro vaše data vrstvenou, hloubkovou a rozšířenou ochranu před internetovými útoky.  

## <a name="applications-for-a-fhir-service"></a>Aplikace pro službu FHIR

Servery FHIR jsou klíčové nástroje pro interoperabilitu dat o stavu.  Rozhraní Azure API pro FHIR je navržené jako rozhraní API a služba, které můžete vytvořit, nasadit a začít používat rychle.  V případě, že se FHIR Standard rozroste na zdravotní péči, se případy použití budou dál rozšiřovat, ale některé úvodní aplikace zákazníka, kde je Azure API pro FHIR, jsou užitečné: 

- **Vývoj pro spuštění/IoT a aplikace:**  Zákazníci, kteří vyvíjející pacienty nebo aplikaci orientované na poskytovatele (mobilní zařízení nebo Web), můžou využít Azure API pro FHIR jako plně spravovanou back-end službu. Rozhraní Azure API pro FHIR poskytuje cenné prostředky v tom, že zákazníci můžou spravovat data a vyměňovat data v zabezpečeném cloudovém prostředí určeném pro data o stavu, využívat inteligentní pokyny k implementaci FHIR a povolit jejich využívání v rámci všech systémů poskytovatelů (například většina EHRs povolila rozhraní API pro FHIR čtení).   
- **Ekosystémy zdravotní péče:**  I když v mnoha klinických nastaveních existuje EHRs jako primární "zdroj pravdy", není to pro poskytovatele Neběžné, že mají více databází, které nejsou připojené k jinému nebo ukládají data v různých formátech.  Používání Azure API pro FHIR jako služby, která je umístěná na těchto systémech, umožňuje standardizovat data ve formátu FHIR.  To pomáhá povolit výměnu dat napříč různými systémy pomocí konzistentního formátu dat. 

- **Výzkum:** Výzkumníki zdravotnictví budou obecně Hledat FHIR Standard a rozhraní Azure API pro FHIR je užitečné, protože normalizuje data kolem běžného FHIR datového modelu a snižuje zatížení pro strojové učení a sdílení dat.
Výměna dat prostřednictvím rozhraní API Azure pro FHIR poskytuje protokoly auditu a řízení přístupu, které vám pomůžou řídit tok dat a kteří mají přístup k jakým datovým typům. 

## <a name="fhir-from-microsoft"></a>FHIR od Microsoftu

FHIR možnosti Microsoftu jsou k dispozici ve dvou konfiguracích:

* Azure API pro FHIR – nabídka PaaS v Azure, která se dá snadno zřídit v Azure Portal a spravovaná Microsoftem.
* FHIR Server pro Azure – open source projekt, který se dá nasadit do předplatného Azure, které je dostupné na GitHubu na adrese https://github.com/Microsoft/fhir-server .

Pro případy použití, které vyžadují rozšíření nebo přizpůsobení serveru FHIR nebo vyžadování přístupu k podkladovým službám, jako je například databáze – bez průchodu rozhraními API FHIR by měli vývojáři zvolit Open Source Server FHIR pro Azure.   Pro implementaci rozhraní FHIR API a back-endu připravené pro produkční prostředí, kde by trvalá data měla být dostupná jenom prostřednictvím rozhraní FHIR API, můžou vývojáři zvolit rozhraní Azure API pro FHIR.

## <a name="iot-connector-preview"></a>IoT Connector (Preview)

Služba IoT Connector je volitelnou funkcí rozhraní Azure API pro FHIR, která poskytuje schopnost ingestovat data ze zařízení s IoMT (internetem lékařských věcí). Internet lékařských věcí je kategorie zařízení IoT, která zachycují a vyměňují si stav & wellness data s jinými systémy IT v oblasti zdravotnictví přes síť. Mezi příklady zařízení IoMT patří vhodnost a klinické wearables, sledovací senzory, sledování aktivit, veřejné terminály nebo dokonce inteligentní klid. Funkce konektor IoT vám umožňuje rychle nastavit službu pro ingestování IoMT dat do Azure API pro FHIR ve škálovatelném, zabezpečeném a kompatibilním způsobem.

Služba IoT Connector může přijmout jakékoli zprávy založené na JSON, které zařízení IoMT odesílá. Tato data se nejdřív transformují na příslušné FHIR prostředky pro [pozorování](https://www.hl7.org/fhir/observation.html) a pak se uloží do Azure API pro FHIR. Logika transformace dat je definována pomocí páru mapování nakonfigurovaných šablon na základě schématu zprávy a FHIR požadavků. Data zařízení je možné přímo do konektoru IoT přidat nebo hladce používat ve vzájemné součinnosti s dalšími řešeními Azure IoT ([azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) a [Azure IoT Central](https://docs.microsoft.com/azure/iot-central/)). IoT Connector poskytuje zabezpečený datový kanál a současně umožňuje řešení Azure IoT spravovat zřizování a údržbu fyzických zařízení.

### <a name="applications-of-iot-connector-preview"></a>Aplikace konektoru IoT (Preview)

Používání zařízení IoMT se rychle rozšiřuje na zdravotní péči a konektor IoT Connector je navržený tak, aby se přemostěním přenosu více zařízení do rozhraní Azure API pro FHIR nastavilo zabezpečení a dodržování předpisů. Uvedení dat IoMT do serveru FHIR umožňuje holistický data Insights a inovativní klinické pracovní postupy. Mezi běžné scénáře pro IoT Connector patří:
- **Monitorování vzdáleného pacienta/zdravotní stav:** Vzdálené monitorování pacientů poskytuje možnost shromažďování dat o zdravotním stavu, mimo tradiční nastavení zdravotnictví. Zdravotnické instituce můžou pomocí konektoru IoT Connector přenést data o stavu vygenerovaná vzdálenými zařízeními do Azure API pro FHIR. Tato data se dají využít k tomu, aby se daly pozorně sledovat stav pacientů, monitorovat pacienty, které jsou v souladu s plánem zpracování, a poskytují
- **Výzkum a biologická vědy:** Klinická hodnocení rychle přijímají IoMT zařízení, jako jsou biosenzory, wearables, mobilní aplikace pro zachycení zkušebních dat. Tyto zkušební verze můžou pomocí konektoru IoT Connector přenášet data zařízení do Azure API pro FHIR zabezpečeným, efektivním a efektivním způsobem. V Azure API pro FHIR se zkušební data dají použít ke spuštění analýzy zkušebních dat v reálném čase.
- **Pokročilá analýza:** Zařízení IoMT můžou poskytovat velké objemy dat a nejrůznější data s vysokou rychlostí. díky tomu jsou velmi vhodné pro poskytování školení a testování dat pro modely strojového učení. Konektor IoT je v podstatě sestavený tak, aby fungoval s nejrůznější frekvencí dat, flexibilním schématem dat a škálováním v cloudu s nízkou latencí. Tyto atributy vytvářejí IoT Connector ideální volbou pro zachycení dat zařízení pro potřeby pokročilé analýzy.
- **Inteligentní nemocnice/clinics:** V dnešní době inteligentních nemocnicích a clinicech se nastavuje infrastruktura propojených digitálních prostředků. IoT Connector se dá použít k zachycení a integraci dat z těchto připojených součástí. Užitečné poznatky z takové sady dat umožňují lepší péči o pacientům a provozní efektivitu.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít pracovat s rozhraním Azure API pro FHIR, postupujte podle 5 minut rychlého startu a nasaďte rozhraní Azure API pro FHIR.

>[!div class="nextstepaction"]
>[Nasazení Azure API for FHIR](fhir-paas-portal-quickstart.md)

Pokud si chcete vyzkoušet funkci IoT Connector, Projděte si rychlý Start k nasazení konektoru IoT pomocí Azure Portal.

>[!div class="nextstepaction"]
>[Nasazení konektoru IoT](iot-fhir-portal-quickstart.md)


FHIR je registrovaná ochranná známka HL7 a používá se s povolením HL7.
