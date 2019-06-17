---
title: Omezení služby pro vrstvy a skladové položky – Azure Search
description: Limity pro plánování kapacity a maximálního počtu žádostí a odpovědí pro službu Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 419c533aabd67637efa64777387c491dd890596e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024639"
---
# <a name="service-limits-in-azure-search"></a>Omezení služby Azure Search
Maximální omezuje na úložiště, úlohy a množství indexů, dokumenty, a dalších objektů závisí na tom, zda jste [zřízení Azure Search](search-create-service-portal.md) na **Free**, **základní**,  **Standardní**, nebo **optimalizované pro úložiště** cenové úrovně.

+ **Bezplatné** je víceklientská služba sdílené, který je součástí vašeho předplatného Azure.

+ **Základní** zajišťují vyhrazené výpočetní prostředky pro produkční úlohy v menším měřítku.

+ **Standardní** běží na vyhrazených počítačů s další kapacitou úložiště a zpracování na všech úrovních. Standard je k dispozici ve čtyřech úrovních: S1, S2, S3 a S3 HD, High Density.

+ **Optimalizováno pro úložiště** běží na vyhrazených počítačů s více celková velikost úložiště, šířku pásma úložiště a paměti, než je **standardní**. Optimalizováno pro úložiště k dispozici ve dvou úrovních: L1 a L2

> [!NOTE]
> Úrovně optimalizované pro úložiště služby jsou aktuálně k dispozici ve verzi preview za zlevněné ceny pro účely testování a experimentování s cílem shromažďování zpětné vazby. Chcete zjistit koncové ceny oznámíme později při těchto úrovních jsou obecně dostupné. Nedoporučujeme tyto úrovně používají, aplikacích v produkčním prostředí.

  S3 High Density (S3 HD, High Density) je navržené pro konkrétní úlohy: [víceklientská](search-modeling-multitenant-saas-applications.md) a velké množství malých indexy (1 000 dokumentů na index, tři tisíc indexů na službu). Tato úroveň neposkytuje [indexer funkce](search-indexer-overview.md). Na S3 HD, High Density musí využívat ingestování přístupu nabízených oznámení pomocí volání rozhraní API k odeslání dat ze zdroje do indexu. 

> [!NOTE]
> Služba se zřizuje na konkrétní úroveň. Přechod k získání kapacity vrstvy zahrnuje zřizování novou službu (neexistuje žádný místní upgrade). Další informace najdete v tématu [zvolte skladovou Položku nebo úroveň](search-sku-tier.md). Další informace o nastavení kapacity v rámci služby jste zřídili, naleznete v tématu [úrovně škálování prostředků pro dotazy a indexování úloh](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Omezení předplatného
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Omezení úložiště
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Omezení indexu

| Resource | Free | Basic&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maximální počet indexů |3 |5 nebo 15 |50 |200 |200 |1 000 na oddíl nebo 3 000 na službu |10 |10 |
| Maximální jednoduché polí v indexu |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maximální komplexní kolekci polí v indexu |40 |40 |40 |40 |40 |40 |40 |40 |
| Maximální počet prvků přes všechny kolekce komplexních v jednom dokumentu |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maximální hloubka komplexní polí |10 |10 |10 |10 |10 |10 |10 |10 |
| Maximální [moduly pro návrhy](https://docs.microsoft.com/rest/api/searchservice/suggesters) každý index |1 |1 |1 |1 |1 |1 |1 |1 |
| Maximální [profily skórování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) každý index |100 |100 |100 |100 |100 |100 |100 |100 |
| Maximální funkce podle profilů. |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> základní služby vytvořené před. prosince 2017 mají nižší omezení (5 místo 15) na indexy. Úroveň Basic je jenom SKU s nižší maximálně 100 polí v indexu.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limity pro dokumenty 

Od října 2018 je již neexistují žádné limity pro dokumenty pro všechny nové služby vytvoří v jakékoli fakturovatelné úrovni (Basic, S1, S2, S3, S3 HD, High Density) v libovolné oblasti. Když jste využili většinu oblastí neomezený počet dokumentu se počítá od listopadu/prosince 2017, bylo pět oblastí, které se uplatňují omezení dokument i nadále. V závislosti na tom, kdy a kde vytvořit vyhledávací službu může být spuštěna služba, která je stále v souladu s limity pro dokumenty.

Pokud chcete zjistit, zda má vaše služba limity pro dokumenty, zkontrolujte dlaždice využití na stránce Přehled vaší služby. Zdokumentujte počty jsou neomezeného nebo předmětu k omezení založené na úrovni.

  ![Dlaždice využití](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-previously-having-document-limits"></a>Oblasti dříve omezení dokumentů vztahuje

Portál bude informovat limit dokumentů, vaše služba byla vytvořena buď před koncem 2017, zda byl vytvořen v datovém centru pomocí malých kapacity clusterů pro hostování služby Azure Search:

+ Austrálie – východ
+ Východní Asie
+ Střed Indie
+ Japonsko – západ
+ Západní střed USA

Pro služby v souladu s limity pro dokumenty platí následující omezení maximální:

|  Free | Basic | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10,000 |1&nbsp;milionů |15 milionů na oddíl nebo 180 milionů na službu |60 milionů na oddíl nebo 720 milionů na službu |120 milionů na oddíl nebo 1,4 miliard na službu |1 milion na index nebo 200 milionů na oddíl |

Pokud má služba omezení, které jste se blokovat, vytvořte novou službu a pak znovu publikovat veškerý obsah na tuto službu. Neexistuje žádný mechanismus pro vaši službu na nový hardware na pozadí bez problémů neukončil.

> [!Note] 
> Pro služby S3 High Density vytvořené po pozdní 2017 se odebrala ale dokumentu 1 milion na index limit zůstane 200 milionů dokumentů na oddíl.


### <a name="document-size-limits-per-api-call"></a>Omezení velikosti dokumentu za volání rozhraní API

Dokument maximální velikost při volání rozhraní API indexu je přibližně 16 MB.

Velikost dokumentu je ve skutečnosti nijak velikost textu požadavku rozhraní API indexu. Protože Index rozhraní API můžete předat dávce více dokumentů současně, omezení velikosti realisticky závisí na počtu dokumentů v dávce. Dokument maximální velikost pro službu batch pomocí jednoho dokumentu, je 16 MB JSON.

Omezit velikost dokumentu, nezapomeňte vyloučit bez možnosti dotazování dat z požadavku. Obrázky a další binární data nejsou přímo zadávat dotazy a neměl by být uložen v indexu. K integraci dat bez možnosti dotazování na výsledky hledání, definujte neprohledávatelném pole uchovávající odkaz URL na prostředek.

## <a name="indexer-limits"></a>Omezení indexeru

Existují maximální dobu spuštění, aby zůstatek a stabilitu ke službě jako celek, ale větší sady dat potřebovat víc indexování času, než umožňuje maximální. Pokud úloha indexování nelze dokončit v povoleném čase maximální, ji zkuste spustit podle plánu. Plánovač uchovává informace o stavu indexování. Pokud z nějakého důvodu dojde k plánované úlohy indexování, indexeru můžete pokračovat tam, kde poslední skončila v příštím plánovaném spuštění.


| Resource | Bezplatné&nbsp;<sup>1</sup> | Basic&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maximální počet indexerů |3 |5 nebo 15|50 |200 |200 |neuvedeno |10 |10 |
| Maximální počet zdrojů dat |3 |5 nebo 15 |50 |200 |200 |neuvedeno |10 |10 |
| Maximální dovednosti <sup>4</sup> |3 |5 nebo 15 |50 |200 |200 |neuvedeno |10 |10 |
| Maximální indexování zatížení na vyvolání |10 000 dokumentů |Omezeno pouze maximální počet dokumentů |Omezeno pouze maximální počet dokumentů |Omezeno pouze maximální počet dokumentů |Omezeno pouze maximální počet dokumentů |neuvedeno |Bez omezení |Bez omezení |
| Minimální plán | 5 minut |5 minut |5 minut |5 minut |5 minut |5 minut |5 minut | 5 minut |
| Maximální doba spuštění <sup>5</sup> | 1 až 3 minut |24 hodin |24 hodin |24 hodin |24 hodin |neuvedeno  |24 hodin |24 hodin |
| Maximální doba běhu pro dovednosti kognitivního vyhledávání nebo objekt blob indexování s analýzou image <sup>5</sup> | 3 až 10 minut |2 hodiny |2 hodiny |2 hodiny |2 hodiny |neuvedeno  |2 hodiny |2 hodiny |
| Indexování objektů blob: blob maximální velikost, MB |16 |16 |128 |256 |256 |neuvedeno  |256 |256 |
| Indexování objektů blob: maximální počet znaků obsahu extrahují z objektu blob |32,000 |64,000 |4&nbsp;milionů |4&nbsp;milionů |4&nbsp;milionů |neuvedeno |4&nbsp;milionů |4&nbsp;milionů |

<sup>1</sup> bezplatné služby mají maximální doba spuštění indexeru 3 minut pro objekt blob zdroje a pro všechny ostatní zdroje dat 1 minuta. Pro AI indexování volající do služeb Cognitive Services jsou bezplatné služby omezený na 20 transakcí zdarma za den, kde je definován transakce jako dokument, který úspěšně projde přes rozšíření kanálu.

<sup>2</sup> základní služby vytvořené před. prosince 2017 mají nižší omezení (5 místo 15) na indexery, zdroje dat a dovednosti.

<sup>3</sup> S3 HD, High Density služby nezahrnují nepodporuje indexery.

<sup>4</sup> maximálně 30 dovednosti na dovednosti.

<sup>5</sup> úlohy kognitivního vyhledávání a analýza obrázků v indexování objektů blob v Azure mají kratší dobu spuštění než indexování běžný text. Analýza a zpracování přirozeného jazyka obrázků jsou náročné na výpočty a využívat neúměrné množství dostupné výpočetní výkon. Doba spuštění se zkrátilo na poskytnout příležitosti ke spuštění dalších úloh ve frontě.  

## <a name="queries-per-second-qps"></a>Dotazů za sekundu (QPS)

Každý zákazník musí být nezávisle na sobě vyvinuté QPS odhady. Velikost indexu a složitosti, dotaz velikost a složitost a objem provozu jsou primární faktory QPS. Neexistuje žádný způsob, jak nabídnout smysluplné odhady při tyto faktory neznámé.

Jsou odhady více předvídatelný při výpočtu na službách, které běží na vyhrazených prostředcích (úrovně Basic a Standard). Chcete-li odhadnout QPS další úzce vzhledem k tomu, že budete mít kontrolu nad více parametrů. Pokyny k odhadu přístup, najdete v části [Azure Search výkon a optimalizace](search-performance-optimization.md).

Pro úrovně optimalizované pro úložiště měli byste očekávat nižší propustnost dotazů a vyšší latenci, než úrovně Standard.  Metodologie pro odhad výkonu dotazů, které budete prostředí je stejný jako úrovně Standard.

## <a name="data-limits-cognitive-search"></a>Omezení datové (kognitivního vyhledávání)

A [kognitivního vyhledávání kanálu](cognitive-search-concept-intro.md) , která provádí volání na prostředek pro analýzu textu pro [rozpoznávání entit](cognitive-search-skill-entity-recognition.md), [klíče extrakce frází](cognitive-search-skill-keyphrases.md), [analýzu subjektivního hodnocení ](cognitive-search-skill-sentiment.md), a [rozpoznávání jazyka](cognitive-search-skill-language-detection.md) je v souladu s limity data. Maximální velikost záznamu by měla být 50 000 znaků pohledu `String.Length`. Pokud je potřeba rozdělit data před odesláním analýza mínění, použijte [dovedností rozdělit textové](cognitive-search-skill-textsplit.md).

## <a name="api-request-limits"></a>Omezení žádostí o rozhraní API
* Maximálně 16 MB na požadavek <sup>1</sup>
* Maximální délka adresy URL velikosti 8 KB
* Maximální počet 1 000 dokumentů na dávku index odešle, sloučení nebo odstraní
* Maximální 32 pole v klauzuli $orderby
* Maximální hledání termín velikost je 32 766 bajtů (32 KB minus 2 bajtů) textu kódování UTF-8

<sup>1</sup> v Azure Search text požadavku je v souladu s maximální limit je 16 MB, nastavení praktické omezení na obsahu jednotlivá pole nebo kolekce, které jinak nejsou omezeny teoretické omezení (viz [nepodporuje data typy](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) Další informace o omezení a pole složení).

## <a name="api-response-limits"></a>Omezení odpovědi rozhraní API
* Maximální vrácených na stránce výsledků hledání 1 000 dokumentů
* Maximální 100 návrhy vrátí na žádost návrh rozhraní API

## <a name="api-key-limits"></a>Omezení klíčů rozhraní API
Klíče rozhraní API se používají pro ověřování služby. Existují dva typy. Klíče správce jsou určené v hlavičce žádosti a udělit přístup k úplné čtení a zápis do služby. Klíče dotazu jsou jen pro čtení, zadaná na adresu URL a obvykle se distribuují klientským aplikacím.

* Maximálně 2 klíče správce na službu
* Maximálně 50 klíče dotaz na službu
