---
title: Zabezpečení a dat o ochraně osobních údajů – Azure Search
description: Azure Search je kompatibilní s další certifikace, SOC 2 a HIPAA. Filtruje připojení a šifrování, ověřování a identita přístup k datům prostřednictvím uživatele a skupiny identifikátory zabezpečení ve službě Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: f366726f539a817f515a78fbc35bfeaa3b65514e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024504"
---
# <a name="security-and-data-privacy-in-azure-search"></a>Zabezpečení a dat o ochraně osobních údajů ve službě Azure Search

Funkce komplexního zabezpečení a řízení přístupu jsou integrované do Azure Search zajistit, že soukromý obsah zůstane tímto způsobem. Tento článek uvádí funkce a standardy dodržování předpisů zabezpečení integrované do Azure Search.

Architektura zabezpečení služby Azure Search zahrnuje fyzického zabezpečení, šifrované přenosy, šifrované úložiště a dodržování standardů celou platformu. Azure Search z provozního hlediska přijímá pouze ověřené žádosti. Řízení přístupu na uživatele na obsah můžete volitelně přidat filtry zabezpečení. Tento článek týká zabezpečení v každé vrstvě, ale je primárně zaměřen na službu jak zabezpečená data a operace ve službě Azure Search.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Standardy dodržování předpisů: ISO 27001, SOC 2, HIPAA

Služba Azure Search má certifikaci pro následující normy jako [jsme oznámili v červnu 2018](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/):

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [Dodržování požadavků SOC 2 typ 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) pro celou sestavu, přejděte na [Azure – a Azure Government SOC 2 typ II sestavy](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Health Insurance Portability and Accountability Act (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR Part 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS úrovně 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Austrálie IRAP neutajované DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

Standardy dodržování předpisů se vztahuje na funkce obecně dostupná. Při přechodu do všeobecné dostupnosti a nesmí se používat v řešeních s požadavky přísné normy, které jsou certifikované funkce ve verzi Preview. Certifikace dodržování předpisů je popsána v [dodržování předpisů přehled Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) a [Centrum](https://www.microsoft.com/en-us/trustcenter). 

## <a name="encrypted-transmission-and-storage"></a>Šifrované přenos a ukládání

Šifrování rozšiřuje v celém celý kanál indexování: z připojení prostřednictvím přenosu a to až indexovaná data uložená ve službě Azure Search.

| Vrstva zabezpečení | Popis |
|----------------|-------------|
| Šifrování během přenosu <br>(HTTPS/SSL/TLS) | Služba Azure Search naslouchá na portu HTTPS 443. Napříč platformami jsou šifrované připojení ke službám Azure. <br/><br/>Všechny interakce klienta služby Azure Search jsou schopné SSL/TLS 1.2.  Nezapomeňte použít TLSv1.2 pro připojení SSL na vaši službu.|
| Šifrování v klidovém stavu <br>Spravovaná Microsoftem klíče | V procesu indexování bez jakéhokoli dopadu na měřitelné indexování čas dokončení nebo velikost indexu je plně internalized šifrování. Automaticky se objeví na veškeré indexování, včetně na přírůstkové aktualizace, které nejsou šifrovány plně indexu (vytvořené před lednem 2018).<br><br>Interně, je šifrování na základě [šifrování služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), pomocí 256bitového [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).<br><br> Šifrování je interní do služby Azure Search, s certifikáty a šifrovacích klíčů interně spravuje Microsoft a použít univerzálně. Nelze vypnout nebo zapnout šifrování, spravovat nebo nahradit vlastní klíče nebo zobrazit nastavení šifrování na portálu nebo prostřednictvím kódu programu.<br><br>Šifrování v klidovém stavu ve 24. ledna 2018 jsme představili a platí pro všechny úrovně služby, včetně sdílených služeb (zdarma) ve všech oblastech. Úplné šifrování musí být vyřazen a znovu vytvořen, aby se šifrování na indexy vytvořené před tímto datem. V opačném případě je šifrována pouze nová data přidá za leden 24.|
| Šifrování v klidovém stavu <br>Klíče spravované zákazníkem | Šifrování pomocí klíčů zákazníka spravovat je **ve verzi preview** služby funkce, která není k dispozici zdarma. Pro placené služby, je dostupná jenom pro vyhledávacích služeb vytvořených na nebo za leden 2019 použitím nejnovější preview verze api-version (verze api-version = 2019-05-06-Preview).<br><br>Indexy Azure Search a mapy synonym lze nyní šifrovat v klidovém stavu pomocí klíčů zákazníka spravované klíče ve službě Azure Key Vault. Další informace najdete v tématu [správu šifrovacích klíčů ve službě Azure Search](search-security-manage-encryption-keys.md).<br>Tato funkce se nahradí výchozí šifrování v klidovém stavu, ale místo toho se použijí společně s jeho.<br>Povolení této funkce zvýšit velikost indexu a snížit výkon dotazů. Podle vyjádření data, můžete očekávat nezvýší o 30 – 60 % zrychlení, i když skutečný výkon se bude lišit v závislosti na definici indexu a typy dotazů. Z důvodu této dopad na výkon doporučujeme povolit pouze tuto funkci na indexy, které skutečně potřebujete.

## <a name="azure-wide-user-access-controls"></a>Řízení přístupu uživatele v Azure – celé

Několik mechanismů pro zabezpečení jsou k dispozici celý Azure a vytvoříte tedy automaticky dostupné pro prostředky Azure Search.

+ [Zámky na úrovni prostředků abyste zabránili odstranění nebo předplatného](../azure-resource-manager/resource-group-lock-resources.md)
+ [Na základě rolí řízení přístupu (RBAC) k řízení přístupu k informacím a operace správy](../role-based-access-control/overview.md)

Všechny služby Azure podporují řízení přístupu na základě rolí (RBAC) k nastavení úrovně přístupu konzistentně napříč všemi službami. Například zobrazení citlivých dat, jako například klíč správce, je omezen na role vlastníka a přispěvatelů, zatímco zobrazení stavu služby je k dispozici pro členy jakékoli role. RBAC poskytuje role vlastník, Přispěvatel a čtenář. Všichni správci služby jsou ve výchozím nastavení členové role vlastník.

<a name="service-access-and-authentication"></a>

## <a name="service-access-and-authentication"></a>Služba přístupu a ověřování

Zatímco Azure Search dědí záruky zabezpečení platformy Azure, poskytuje také vlastní ověřování na základě klíče. Klíč rozhraní api se řetězec skládá náhodně generované čísel a písmen. Typ klíče (správce nebo dotaz) určuje úroveň přístupu. Odeslání platný klíč je považovaný za důkaz žádost pochází z důvěryhodné entity. 

Existují dvě úrovně přístupu k vaší vyhledávací službě umožněné dva typy klíčů:

* Přístup správce (platí pro všechny operace čtení a zápis na službu)
* Přístup k dotazům (platí jen pro čtení operací, jako jsou dotazy proti kolekci dokumentů v indexu)

*Klíče správce* vytvářejí, když je služba zřízená. Existují dva klíče správce, určený jako *primární* a *sekundární* Novoroční přímo, ale ve skutečnosti jsou zaměnitelné. Každá služba má dva klíče správce, takže můžete jeden ho znovu vygenerovat aniž by ztratily přístup k službě. Je možné [klíč znovu vygenerovat správce](search-security-api-keys.md#regenerate-admin-keys) pravidelně za Azure security osvědčené postupy, ale nelze přidat do počtu celkový správu klíčů. Jsou maximálně dva klíče správce službě search.

*Klíče dotazů* vytvářejí podle potřeby a jsou navrženy pro klientské aplikace, které vydávat dotazy. Můžete vytvořit až 50 klíče dotazu. V kódu aplikace zadejte adresa URL pro hledání a dotazu api-key, pokud chcete povolit přístup jen pro čtení do kolekce documents konkrétního indexu. Koncový bod, klíč rozhraní api pro přístup jen pro čtení a cílový index společně definují obor a přístup k úrovni připojení z klientské aplikace.

U každého požadavku, ve kterém každý požadavek se skládá z povinných klíč, operace a objekt se vyžaduje ověřování. Když zřetězen dohromady, dvou úrovních oprávnění (úplné nebo jen pro čtení) a kontextu (například operace dotazu na index) jsou dostačující pro zajištění zabezpečení opensourcová operací služby. Další informace o klíčích najdete v tématu [vytvořit a spravovat klíče api Key](search-security-api-keys.md).

## <a name="index-access"></a>Index přístup

Ve službě Azure Search jednotlivé indexu není zabezpečitelných objektů. Místo toho je určen přístup do indexu ve vrstvě služby (čtení nebo zápisu), spolu s kontextu operace.

Pro přístup koncových uživatelů lze struktury požadavků na dotazy a připojte se pomocí klíče dotazu, který umožňuje všechny požadavky jen pro čtení a zahrnují konkrétního indexu, která vaše aplikace používá. V žádosti o dotaz neexistuje koncept propojení indexy nebo přístupu k nim více indexů současně tak cílit na všechny požadavky podle definice jeden index. Vytváření dotazu na žádost (klíč plus jeden cílový index) v důsledku toho definuje hranice zabezpečení.

Přístup správců a vývojářů pro indexy nediferencovanými: oba potřebují přístup pro zápis do vytvářet, odstraňovat a aktualizovat objekty, které služba spravuje. Každý, kdo má klíč správce pro vaši službu může číst, upravit nebo odstranit jakýkoli index ve stejné službě. Pro ochranu před náhodným nebo zlovolným vymazáním indexů interní zdrojového kódu assetů je řešením pro vrácení nežádoucím index, odstranění nebo úpravy. Služba Azure Search má převzetí služeb při selhání v rámci clusteru a zajistit tak dostupnost, ale to se neukládají ani spuštění speciální kódu použít k vytvoření nebo načítání indexů.

Víceklientská architektura řešení vyžadující hranic zabezpečení na úrovni index taková řešení obvykle zahrnují střední vrstvy, které zákazníci používají ke izolace indexu. Další informace o případu použití víceklientské najdete v tématu [modely návrhu pro víceklientské aplikace SaaS a Azure Search](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access"></a>Přístup správce

[Přístup na základě rolí (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) Určuje, jestli máte přístup k ovládacím prvkům přes službu a její obsah. Pokud jste roli vlastníka nebo přispěvatele na službu Azure Search, můžete na portálu nebo Powershellu **Az.Search** modulu, který chcete vytvořit, aktualizovat nebo odstranit objekty ve službě. Můžete také použít [Management REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

## <a name="user-access"></a>Přístup uživatelů

Ve výchozím nastavení přístup uživatelů k indexu je určeno přístupový klíč v dotazu žádosti. Většina vývojářů vytvořte a přiřaďte [ *klíče dotazů* ](search-security-api-keys.md) pro požadavky na vyhledávání na straně klienta. Klíč dotazu uděluje přístup pro čtení k celému obsahu v indexu.

Pokud potřebujete podrobné, na uživatelský ovládací prvek obsahu, můžete vytvořit filtry zabezpečení na své dotazy vracející dokumenty přidružené k dané zabezpečení identity. Místo předdefinované role a přiřazení rolí, řízení přístupu založené na identitě je implementovaný jako *filtr* , že výsledky dokumentů a obsah hledání ořízne na základě identit. Následující tabulka popisuje dva přístupy pro výsledky hledání oříznutí neoprávněné obsahu.

| Přístup | Popis |
|----------|-------------|
|[Oříznutí zabezpečení na základě filtrů identity](search-security-trimming-for-azure-search.md)  | Dokumenty základní pracovní postup k implementaci řízení přístupu identity uživatele. Popisuje přidání identifikátory zabezpečení do indexu a vysvětluje, filtrování pole mají být odebrány výsledky zakázané obsahu. |
|[Oříznutí zabezpečení na základě identit Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Tento článek dál navazuje na předchozí článek, konkrétně postup pro načítání identit z Azure Active Directory (AAD), jeden z [bezplatné služby](https://azure.microsoft.com/free/) v cloudové platformy Azure. |

## <a name="table-permissioned-operations"></a>Tabulka: Udělili oprávnění operace

Následující tabulka shrnuje operace povolené ve službě Azure Search a který klíč odemkne přístup určitou operaci.

| Operace | Oprávnění |
|-----------|-------------------------|
| Vytvoření služby | Vlastník předplatného Azure|
| Škálování služby | Klíč správce, RBAC vlastníka nebo přispěvatele, s prostředkem  |
| Odstranit službu | Klíč správce, RBAC vlastníka nebo přispěvatele, s prostředkem |
| Vytvoření, úprava nebo odstranění objektů ve službě: <br>Indexy a součásti (včetně definice analyzátoru, profily vyhodnocování, možnosti CORS), indexery, zdroje dat, synonyma, moduly pro návrhy. | Klíč správce, RBAC vlastníka nebo přispěvatele, s prostředkem  |
| Dotazování indexu | Klíč správce nebo dotazu (RBAC není k dispozici) |
| Dotazování systémové informace, jako je například vrácení statistiky, počty a seznam objektů. | Klíč správce, RBAC v prostředku (vlastník, Přispěvatel, Čtenář) |
| Spravovat klíče správce | Klíč správce, RBAC vlastníka nebo přispěvatele, s prostředkem. |
| Spravovat klíče dotazů |  Klíč správce, RBAC vlastníka nebo přispěvatele, s prostředkem.  |

## <a name="physical-security"></a>Fyzické zabezpečení

Datovými centry společnosti Microsoft poskytuje špičkové fyzického zabezpečení a jsou kompatibilní s rozsáhlou portfolio standardů a nařízení. Další informace, přejděte na [globálních datových center](https://www.microsoft.com/cloud-platform/global-datacenters) stránce nebo podíváte na krátké video s daty, center security.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Další informace najdete v tématech

+ [Získat spuštění .NET (ukazuje vytvoření indexu pomocí klíč správce)](search-create-index-dotnet.md)
+ [Získat spuštění REST (ukazuje vytvoření indexu pomocí klíč správce)](search-create-index-rest-api.md)
+ [Ovládací prvek přístupu na základě identit pomocí Azure Search filtry](search-security-trimming-for-azure-search.md)
+ [Aktivní řízení přístupu na základě identity adresáře pomocí filtrů Azure Search](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtry ve službě Azure Search](search-filters.md)