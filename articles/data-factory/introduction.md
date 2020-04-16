---
title: Úvod do služby Azure Data Factory
description: Seznamte se se službou Azure Data Factory, cloudovou službou pro integraci dat, která orchestruje a automatizuje přesouvání a transformaci dat.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: overview
ms.date: 09/30/2019
ms.openlocfilehash: 327aadbc2996e91ef0f7639da86a647ddd42247c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410945"
---
# <a name="what-is-azure-data-factory"></a>Co je služba Azure Data Factory?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ve světě velkých objemů dat jsou nezpracovaná, neuspořádaná data často uložená v relačních, nerelačních a jiných systémech úložiště. Nezpracovaná data sama o sobě ale nemají potřebný kontext ani význam, aby mohla analytikům, datovým vědcům nebo osobám rozhodujícím v rámci podniků poskytnout smysluplný přehled. 

Velké objemy dat vyžadují službu, která umožňuje orchestraci a operacionalizaci procesů, aby se tato obrovská úložiště nezpracovaných dat proměnila v obchodní informace, podle kterých se lze rozhodovat. Azure Data Factory je spravovaná cloudová služba vytvořená pro tyto komplexní projekty hybridní extrakce, transformace a načítání (ETL), extrakce, načítání a transformace (ELT) a integrace dat.

Představte si například společnost vyrábějící hry, která shromažďuje celé petabajty herních protokolů generovaných hrami v cloudu. Společnost chce tyto protokoly analyzovat, aby získala informace o preferencích zákazníků, demografických ukazatelích a způsobech používání. Zároveň chce identifikovat příležitosti křížového a následného prodeje, vyvíjet nové, zajímavé funkce, podpořit obchodní růst a zlepšovat zkušenosti zákazníků.

Aby společnost mohla tyto protokoly analyzovat, potřebuje použít referenční data, jako jsou informace o zákaznících, hrách a marketingových kampaních, které jsou uložené v místním úložišti dat. Společnost chce využít tato data z místního úložiště dat a zkombinovat je s dalšími daty protokolů, která má uložená v cloudovém úložišti dat. 

Za účelem získání informací hodlá zpracovat spojená data pomocí clusteru Spark v cloudu (Azure HDInsight) a transformovaná data publikovat do cloudového datového skladu, jako je služba Azure SQL Data Warehouse, aby z nich mohla snadno vytvářet sestavy. Tento pracovní postup chce automatizovat a provádět jeho monitorování a správu na každodenní bázi. Zároveň ho chce spouštět, jakmile se v kontejneru úložiště objektů blob objeví soubory.

Právě v takových scénářích práce s daty nachází uplatnění platforma Azure Data Factory. Jedná se o *cloudovou ETL a službu integrace dat, která umožňuje vytvářet pracovní postupy řízené daty pro orchestraci pohybu dat a transformaci dat ve velkém měřítku*. Pomocí služby Azure Data Factory můžete vytvářet a plánovat pracovní postupy řízené daty (označované jako kanály), které dokáží ingestovat data z různorodých zdrojů dat. Můžete vytvářet složité etl procesy, které vizuálně transformují data pomocí toků dat nebo pomocí výpočetních služeb, jako je Azure HDInsight Hadoop, Azure Databricks a Azure SQL Database. 

Kromě toho můžete publikovat transformovaná data do úložišť dat, jako je Azure SQL Data Warehouse pro business intelligence (BI) aplikace využívat. V neposlední řadě služba Azure Data Factory umožňuje uspořádání nezpracovaných dat do smysluplných úložišť dat a datových jezer, která pomáhají při obchodním rozhodování.

![Zobrazení nejvyšší úrovně služby Data Factory](media/data-flow/overview.png)

## <a name="how-does-it-work"></a>Jak to funguje?

Data Factory obsahuje řadu propojených systémů, které poskytují kompletní end-to-end platformu pro datové inženýry.

### <a name="connect-and-collect"></a>Připojení a shromažďování

Podniky mají data různých typů, která jsou uložená v různorodých místních zdrojích nebo v cloudu, a data strukturovaná, nestrukturovaná i částečně strukturovaná, která přicházejí v různých intervalech a různou rychlostí. 

Prvním krokem při sestavování systému vytváření informací je připojení ke všem požadovaným zdrojům dat a zpracování, jako jsou například služby typu software jako služba (SaaS), databáze, sdílené složky a webové služby FTP. Dalším krokem je přesun dat podle potřeby do centralizovaného umístění pro následné zpracování.

Bez služby Data Factory musí podniky sestavovat vlastní komponenty pro přesun dat nebo vyvíjet vlastní služby pro integraci a zpracování těchto zdrojů dat. Integrace a údržba takových systémů je nákladná a složitá. Kromě toho často postrádají monitorování a upozorňování na podnikové úrovni a ovládací prvky, které může nabídnout plně spravovaná služba.

Se službou Data Factory můžete pomocí [aktivity kopírování](copy-activity-overview.md) v datovém kanálu přesouvat data z místních i cloudových úložišť dat do centralizovaného úložiště v cloudu pro účely další analýzy. Například můžete shromažďovat data v Azure Data Lake Storage a transformovat data později pomocí výpočetní služby Azure Data Lake Analytics. Data můžete shromažďovat i ve službě Azure Blob Storage a později je transformovat pomocí clusteru Azure HDInsight Hadoop.

### <a name="transform-and-enrich"></a>Transformace a rozšíření
Poté, co jsou data přítomna v centralizovaném úložišti dat v cloudu, zpracujte nebo transformujte shromážděná data pomocí toků mapování ADF. Toky dat umožňují datovým inženýrům vytvářet a udržovat grafy transformace dat, které se spouštějí na Sparku, aniž by museli rozumět clusterům Spark nebo programování Spark.

Pokud dáváte přednost ručnítransformaci kódu, ADF podporuje externí aktivity pro provádění transformace na výpočetní služby, jako je HDInsight Hadoop, Spark, Data Lake Analytics a Machine Learning.

### <a name="cicd-and-publish"></a>CI/CD a publikovat
Data Factory nabízí plnou podporu pro CI/CD vašich datových kanálů pomocí Azure DevOps a GitHub. To umožňuje postupně vyvíjet a dodávat vaše ETL procesy před publikováním hotového produktu. Po převodu nezpracovaných dat do podoby, která umožňuje využití v rámci podniku, můžete tato data nahrát do služby Azure Data Warehouse, Azure SQL Database, Azure CosmosDB nebo jakéhokoli jiného analytického nástroje, na který se můžou vaši obchodní uživatelé nasměrovat ze svých nástrojů business intelligence.

### <a name="monitor"></a>Monitorování
Jakmile úspěšně sestavíte a nasadíte kanál integrace dat, který ze zpracovaných dat získává obchodní hodnotu, můžete monitorovat naplánované aktivity a kanály a jejich míru úspěšnosti a chyb. Azure Data Factory má integrovanou podporu pro monitorování kanálu přes Azure Monitor, ROZHRANÍ API, PowerShell, protokoly Azure Monitoru a panely stavu na webu Azure Portal.

## <a name="top-level-concepts"></a>Koncepty nejvyšší úrovně
Předplatné Azure může obsahovat jednu nebo více instancí služby Azure Data Factory (neboli datových továren). Azure Data Factory se skládá ze čtyř klíčových součástí. Tyto součásti společně poskytují platformu, na které můžete vytvářet pracovní postupy řízené daty s kroky pro přesun a transformaci dat.

### <a name="pipeline"></a>Kanál
Datová továrna může mít jeden nebo víc kanálů. Kanál je logické seskupení aktivit, které dohromady provádějí určitou jednotku práce. Aktivity v kanálu společně provádí úlohy. Kanál může například obsahovat skupinu aktivit, které ingestují data z objektu blob Azure a pak na clusteru HDInsight spustí dotaz Hivu pro rozdělení dat. 

Výhodou tohoto přístupu je, že vám kanál umožňuje spravovat aktivity jako sadu, a ne každou jednotlivě. Aktivity v kanálu je možné zřetězit, aby probíhaly postupně, nebo můžou probíhat souběžně a nezávisle na sobě.

### <a name="mapping-data-flows"></a>Toky dat mapování
Vytvořte a spravujte grafy logiky transformace dat, které můžete použít k transformaci dat libovolné velikosti. Můžete vytvořit opakovaně použitelnou knihovnu rutin transformace dat a spustit tyto procesy v horizontálním navýšení kapacity způsobem z vašich kanálů ADF. Data Factory spustí vaši logiku v clusteru Spark, který se otáčí nahoru a točí dolů, když ji potřebujete. Nikdy nebudete muset spravovat nebo udržovat clustery.

### <a name="activity"></a>Aktivita
Aktivity představují krok zpracování v rámci kanálu. Například můžete použít aktivitu kopírování ke kopírování dat z jednoho úložiště dat do jiného. Podobně můžete použít aktivitu Hivu, která spustí dotaz Hivu na clusteru Azure HDInsight, aby transformoval a analyzoval vaše data. Data Factory podporuje tři typy aktivit: aktivity přesunu dat, aktivity transformace dat a aktivity řízení.

### <a name="datasets"></a>Datové sady
Datové sady představují datové struktury v rámci úložišť dat, které jednoduše odkazují na data, která chcete ve svých aktivitách použít jako vstupy nebo výstupy. 

### <a name="linked-services"></a>Propojené služby
Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Můžete si to představit tak, že propojená služba definuje připojení ke zdroji dat a datová sada představuje strukturu těchto dat. Například propojená služba Azure Storage určuje připojovací řetězec pro připojení k účtu služby Azure Storage. Datová sada objektu blob Azure navíc určuje kontejner objektů blob a složku obsahující data.

Propojené služby slouží ve službě Data Factory ke dvěma účelům:

- Představují **úložiště dat**, k nimž mimo jiné patří například místní databáze SQL Serveru, databáze Oracle, sdílená složka nebo účet úložiště objektů blob Azure. Seznam podporovaných úložišť dat najdete v článku [Aktivita kopírování](copy-activity-overview.md).

- Představují **výpočetní prostředek**, který může hostovat provádění aktivity. Například aktivita HDInsightHive se spouští na clusteru HDInsight Hadoop. Seznam aktivit transformace a podporovaných výpočetních prostředí najdete v článku o [transformaci dat](transform-data.md).

### <a name="triggers"></a>Aktivační události
Aktivační události jsou jednotkou zpracování, která určuje, kdy se má zahájit provádění kanálu. Pro různé typy událostí existují různé typy aktivačních událostí.

### <a name="pipeline-runs"></a>Spuštění kanálu
Spuštění kanálu je instance provádění kanálu. Instance spuštění kanálu se obvykle vytvářejí předáváním argumentů do parametrů definovaných v kanálech. Argumenty je možné předat ručně nebo v rámci definice aktivační události.

### <a name="parameters"></a>Parametry
Parametry jsou dvojice klíčů a hodnot v rámci konfigurace jen pro čtení.Parametry jsou definované v kanálu. Argumenty definovaných parametrů se předávají během provádění z kontextu spuštění vytvořeného aktivační událostí nebo ručně spuštěným kanálem. Aktivity v rámci kanálu využívají hodnoty parametrů.

Datová sada je parametr silného typu a opakovaně použitelná/odkazovatelná entita. Aktivita může odkazovat na datové sady a může využívat vlastnosti definované v definici datové sady.

Propojená služba je také parametr silného typu, který obsahuje informace o připojení k úložišti dat nebo výpočetnímu prostředí. Je to také opakovaně použitelná/odkazovatelná entita.

### <a name="control-flow"></a>Tok řízení
Tok řízení představuje orchestraci aktivit kanálu, která zahrnuje zřetězení aktivit v sekvenci, větvení, definování parametrů na úrovni kanálu, a předávání argumentů při volání kanálu na vyžádání nebo z aktivační události. Zahrnuje také předávání vlastního stavu a kontejnery smyček, tedy iterátory For-each.

### <a name="variables"></a>Proměnné
Proměnné lze použít uvnitř kanálu k ukládání dočasných hodnot a lze je také použít ve spojení s parametry pro povolení předávání hodnot mezi kanály, toky dat a další aktivity.

## <a name="next-steps"></a>Další kroky
Zde jsou důležité další kroky dokumenty k prozkoumání:

- [Datové sady a propojené služby](concepts-datasets-linked-services.md)
- [Kanály a aktivity](concepts-pipelines-activities.md)
- [Prostředí Integration Runtime](concepts-integration-runtime.md)
- [Mapování datových toků](concepts-data-flow-overview.md)
- [Uživatelské rozhraní Data Factory na webu Azure Portal](quickstart-create-data-factory-portal.md)
- [Nástroj pro kopírování dat na webu Azure Portal](quickstart-create-data-factory-copy-data-tool.md)
- [PowerShell](quickstart-create-data-factory-powershell.md)
- [.NET](quickstart-create-data-factory-dot-net.md)
- [Python](quickstart-create-data-factory-python.md)
- [REST](quickstart-create-data-factory-rest-api.md)
- [Šablona Azure Resource Manageru](quickstart-create-data-factory-resource-manager-template.md)
 
