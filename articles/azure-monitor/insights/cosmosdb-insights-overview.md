---
title: Monitorovat Azure Cosmos DB pomocí Azure Monitor pro Cosmos DB (Preview) | Microsoft Docs
description: Tento článek popisuje Azure Monitor pro funkci Cosmos DB, která poskytuje Cosmos DBm vlastníkům rychlé porozumění problémům s výkonem a využitím jejich účtů CosmosDB.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: d28db9907094a651835078f4459a985b9d15e589
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657380"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>Prozkoumat Azure Monitor pro Azure Cosmos DB (Preview)

Azure Monitor for Azure Cosmos DB (Preview) poskytuje přehled o celkovém výkonu, selháních, kapacitě a provozním stavu všech vašich Azure Cosmos DBch prostředků v jednotném interaktivním prostředí. Tento článek vám pomůže pochopit výhody tohoto nového prostředí pro monitorování a jak můžete upravit a přizpůsobit prostředí tak, aby vyhovovalo jedinečným potřebám vaší organizace.   

## <a name="introduction"></a>Úvod

Předtím, než se začnete do prostředí, byste měli pochopit, jak prezentuje a vizualizují informace. 

Poskytuje:

* V **perspektivě škály** svých Azure Cosmos DBch prostředků napříč všemi vašimi předplatnými v jednom místě s možností selektivně přidružit jenom k předplatným a prostředkům, které vás zajímají.

* Procházejte **analýzu** konkrétního prostředku Azure CosmosDB, která vám pomůžou diagnostikovat problémy nebo provádět podrobnou analýzu pomocí kategorií, selhání, kapacity a operací. Výběrem jedné z těchto možností získáte Podrobný pohled na relevantní metriky Azure Cosmos DB.  

* **Přizpůsobitelné** – toto prostředí je postavené na šablonách sešitu Azure monitor, což vám umožní změnit, jaké metriky se zobrazí, upravit nebo nastavit prahové hodnoty, které odpovídají vašim limitům, a pak je Uložit do vlastního sešitu. Grafy v sešitech je pak možné připnout na řídicí panely Azure.  

Tato funkce nevyžaduje, abyste povolili ani nenakonfigurovali cokoli. Tyto Azure Cosmos DB metriky se ve výchozím nastavení shromažďují.

>[!NOTE]
>Pro přístup k této funkci se neplatí žádné poplatky a účtují se vám jenom Azure Monitor základní funkce, které nakonfigurujete nebo povolíte, jak je popsáno na stránce [Azure monitor s podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Zobrazit metriky na úrovni operace pro Azure Cosmos DB

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com/).

1. V levém navigačním panelu vyberte **monitor** a vyberte **metriky**.

   ![Podokno metrik v Azure Monitor](./media/cosmosdb-insights-overview/monitor-metrics-blade.png)

1. V podokně **metriky** > **Vyberte prostředek** > zvolte požadované **předplatné**a **skupinu prostředků**. Jako **typ prostředku**vyberte **Azure Cosmos DB účty**, zvolte jeden ze stávajících účtů Azure Cosmos a pak vyberte **použít**.

   ![Vyberte účet Cosmos DB, pro který chcete zobrazit metriky.](./media/cosmosdb-insights-overview/select-cosmosdb-account.png)

1. Dále můžete vybrat metriku ze seznamu dostupných metrik. Můžete vybrat metriky specifické pro jednotky žádosti, úložiště, latenci, dostupnost, Cassandra a další. Podrobné informace o všech dostupných metrikách v tomto seznamu najdete v článku [metriky podle kategorií](../../cosmos-db/monitor-cosmos-db-reference.md) . V tomto příkladu vybereme **jednotky žádosti** a jako hodnotu agregace určíte **průměr** .

   Kromě těchto podrobností můžete také vybrat **časový rozsah** a **časovou členitost** metrik. V poli Max (maximum) si můžete zobrazit metriky za posledních 30 dní.  Po použití filtru se v závislosti na vašem filtru zobrazí graf. Pro vybrané období můžete zobrazit průměrný počet spotřebovaných jednotek žádostí za minutu.  

   ![Vyberte metriku z Azure Portal](./media/cosmosdb-insights-overview/metric-types.png)

### <a name="add-filters-to-metrics"></a>Přidání filtrů do metrik

Můžete také filtrovat metriky a graf zobrazený podle konkrétního typu **CollectionName**, **DatabaseName**, **typem operace OperationType**, **region**a **StatusCode**. Chcete-li filtrovat metriky, vyberte možnost **Přidat filtr** a zvolte požadovanou vlastnost, například **typem operace OperationType** , a vyberte hodnotu, jako je například **dotaz**. V grafu se pak zobrazí jednotky žádosti spotřebované pro operaci dotazování pro vybrané období. Operace provedené prostřednictvím uložené procedury nejsou protokolovány, takže nejsou k dispozici v rámci metriky typem operace OperationType.

![Přidejte filtr pro výběr členitosti metriky.](./media/cosmosdb-insights-overview/add-metrics-filter.png)

Metriky můžete seskupit pomocí možnosti **použít rozdělení** . Například můžete seskupit jednotky žádostí na typ operace a zobrazit graf pro všechny operace najednou, jak je znázorněno na následujícím obrázku:

![Přidat použít dělicí filtr](./media/cosmosdb-insights-overview/apply-metrics-splitting.png)

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Zobrazit metriky využití a výkonu pro Azure Cosmos DB

Pokud chcete zobrazit využití a výkon účtů úložiště napříč všemi vašimi předplatnými, proveďte následující kroky.

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).

2. Vyhledejte **monitorování** a vyberte **monitor**.

    ![Vyhledávací pole se slovem "monitor" a rozevírací seznam, který říká službám "monitor" s obrázkem stylu rychloměru](./media/cosmosdb-insights-overview/search-monitor.png)

3. Vyberte **Cosmos dB (Preview)** .

    ![Snímek obrazovky s přehledem Cosmos DBového sešitu](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Přehled

V **přehledu**zobrazuje tabulka interaktivní metriky Azure Cosmos DB. Výsledky můžete filtrovat podle vybraných možností z následujících rozevíracích seznamů:

* V seznamu **předplatných** jsou uvedena pouze předplatná, která mají prostředek Azure Cosmos DB.  

* **Cosmos DB** – můžete vybrat vše, podmnožinu nebo jeden prostředek Azure Cosmos DB.

* **Časový rozsah** – ve výchozím nastavení zobrazí poslední 4 hodiny informací na základě odpovídajících výběrů.

Dlaždice počítadla v rozevíracích seznamech shrnuje celkový počet Azure Cosmos DBch prostředků ve vybraných předplatných. Pro sloupce v sešitu jsou podmíněné barevné kódování nebo Heat mapy, které vykazují metriky transakcí. Hluboká barva má nejvyšší hodnotu a světlejší barva je založena na nejnižších hodnotách. 

Když vyberete šipku rozevíracího seznamu vedle jednoho z Azure Cosmos DB prostředků, zobrazí se rozpis metrik výkonu na úrovni jednotlivého kontejneru databáze:

![Rozšířené rozevírací seznam odhalí jednotlivé kontejnery databáze a související rozpis výkonu.](./media/cosmosdb-insights-overview/container-view.png)

Když vyberete název prostředku Azure Cosmos DB zvýrazněný modře, přejdete k výchozímu **přehledu** pro přidružený účet Azure Cosmos DB. 

### <a name="failures"></a>Selhání

V horní části stránky vyberte **selhání** a otevře se část **selhání** šablony sešitu. Zobrazuje celkový počet požadavků s distribucí odpovědí, které tvoří tyto požadavky:

![Snímek obrazovky s chybami s rozpisem podle typu požadavku HTTP](./media/cosmosdb-insights-overview/failures.png)

| Kód      |  Popis       | 
|-----------|:--------------------|
| `200 OK`  | Jedna z následujících operací REST byla úspěšná: </br>– ZÍSKAT u prostředku. </br> -PUT do prostředku. </br> – ZVEŘEJNĚNÍ na prostředku. </br> -POST v prostředku uložené procedury a spusťte uloženou proceduru.|
| `201 Created` | Operace POST pro vytvoření prostředku je úspěšná. |
| `404 Not Found` | Operace se pokouší jednat o prostředek, který již neexistuje. Prostředek mohl být například již odstraněn. |

Úplný seznam stavových kódů najdete v [článku Azure Cosmos DB stavový kód HTTP](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="capacity"></a>Kapacita

V horní části stránky vyberte **kapacitu** a v šabloně sešitu se otevře část **kapacita** . Ukazuje, kolik dokumentů máte, váš dokument roste v čase, využití dat a celkově dostupné úložiště, které jste opustili.  Dá se využít k identifikaci potenciálních problémů s úložištěm a využitím dat.

![Pracovní sešit kapacity](./media/cosmosdb-insights-overview/capacity.png) 

Stejně jako u sešitu s přehledem se po výběru rozevíracího seznamu vedle Azure Cosmos DB prostředku ve sloupci **odběr** zobrazí rozpis jednotlivých kontejnerů, které tvoří databázi.

### <a name="operations"></a>Operace 

V horní části stránky vyberte **operace** a otevře se část **operace** v šabloně sešitu. Dává vám možnost Zobrazit vaše požadavky rozdělené podle typu vámi provedených požadavků. 

Takže v příkladu níže vidíte, že `eastus-billingint` má za následek, že se v rámci toho dostávají požadavky na čtení, ale s malým počtem Upsert a požadavky na vytvoření. Vzhledem k tomu, že `westeurope-billingint` je jen pro čtení z perspektivy žádosti, nejméně za poslední čtyři hodiny, na které je sešit aktuálně vymezený prostřednictvím parametru časového rozsahu.

![Sešit operací](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>Připnout, exportovat a rozbalit

Libovolný oddíl metriky můžete připnout na [řídicí panel Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) tak, že vyberete ikonu připínáčku v pravém horním rohu oddílu.

![Připnout oddíl metriky na příklad řídicího panelu](./media/cosmosdb-insights-overview/pin.png)

Chcete-li exportovat data do formátu aplikace Excel, vyberte ikonu šipky dolů nalevo od ikony připínáčku.

![Ikona exportovat sešit](./media/cosmosdb-insights-overview/export.png)

Chcete-li rozbalit nebo sbalit všechna rozevírací zobrazení v sešitu, vyberte ikonu rozbalení vlevo od ikony exportu:

![Ikona rozbalení sešitu](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>Přizpůsobení Azure Monitor pro Azure Cosmos DB (Preview)

Vzhledem k tomu, že toto prostředí je postavené na šablonách sešitu Azure Monitor, máte možnost **přizpůsobit** > **Upravit** a **Uložit** kopii změněné verze do vlastního sešitu. 

![Přizpůsobit pruh](./media/cosmosdb-insights-overview/customize.png)

Sešity se ukládají v rámci skupiny prostředků, a to buď v části **Mé sestavy** , která je pro vás nebo ve **sdílených sestavách** přístupná pro všechny s přístupem ke skupině prostředků. Po uložení vlastního sešitu musíte přejít do galerie sešitů a spustit ho.

![Spustit galerii sešitů z panelu příkazů](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Další kroky

* Nakonfigurujte [Upozornění metrik](../platform/alerts-metric.md) a [oznámení o stavu služby](../../service-health/alerts-activity-log-service-notifications.md) , abyste nastavili automatizované upozorňování na pomoc při zjišťování problémů.

* Seznamte se s scénáři, které jsou navržené tak, aby podporovaly, vytváření nových a přizpůsobení existujících sestav a další informace najdete v tématu [vytváření interaktivních sestav pomocí Azure Monitorch sešitů](../app/usage-workbooks.md).
