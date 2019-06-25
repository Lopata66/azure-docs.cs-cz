---
title: 'Načítání: Python SDK pro přípravu dat'
titleSuffix: Azure Machine Learning service
description: Další informace o načítání dat pomocí sady SDK služby Azure Machine Learning Data Prep. Můžete načíst různé typy vstupních dat, určit typy souborů dat a parametry nebo použít funkci inteligentního čtení sady SDK automaticky rozpoznat typ souboru.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 02/22/2019
ms.custom: seodec18
ms.openlocfilehash: fef3281f1f4e727b58878439e3f6456fee3b6241
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66752928"
---
# <a name="load-and-read-data-with-the-azure-machine-learning-data-prep-sdk"></a>Načtení a čtení dat pomocí sady SDK pro Azure Machine Learning Data Prep
V tomto článku se naučíte různými způsoby načítání dat pomocí sady SDK pro Azure Machine Learning Data Prep.  Sada SDK podporuje různé funkce příjem dat, včetně:

* Načtení z mnoha typů souborů s analýzy odvozování parametrů (kódování, oddělovač, hlavičky)
* Převod typu pomocí odvození během načítání souboru
* Podpora připojení pro MS SQL Server a úložiště Azure Data Lake

> [!Important]
> Pokud vytváříte nové řešení, zkuste [datové sady Azure Machine Learning](how-to-explore-prepare-data.md) (preview) pro zkoumání dat a přípravy. Datové sady je další verze přípravy dat SDK nabízí rozšířené funkce pro správu datových sad v řešení AI.
> Pokud používáte `azureml-dataprep` balíček pro vytváření toku dat s namísto použití transformace `azureml-datasets` balíček k vytvoření datové sady, nebudete moct později použít snímky nebo systémovou správou verzí datové sady.

V následující tabulce jsou uvedeny výběru sady funkcí pro načítání dat z běžných typů souborů.

| Typ souboru | Funkce | Referenční odkaz |
|-------|-------|-------|
|Jakýkoli|`auto_read_file()`|[Referenční dokumentace](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#auto-read-file-path--filepath--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|Text|`read_lines()`|[Referenční dokumentace](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-lines-path--filepath--header--azureml-dataprep-api-dataflow-promoteheadersmode----promoteheadersmode-none--0---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---skip-rows--int---0--skip-mode--azureml-dataprep-api-dataflow-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|SDÍLENÝ SVAZEK CLUSTERU|`read_csv()`|[Referenční dokumentace](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-csv-path--filepath--separator--str--------header--azureml-dataprep-api-dataflow-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---quoting--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-dataflow-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false--archive-options--azureml-dataprep-api--archiveoption-archiveoptions---none--infer-column-types--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|Excel|`read_excel()`|[Referenční dokumentace](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-excel-path--filepath--sheet-name--str---none--use-column-headers--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--include-path--bool---false--infer-column-types--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|Fixed-width|`read_fwf()`|[Referenční dokumentace](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-fwf-path--filepath--offsets--typing-list-int---header--azureml-dataprep-api-dataflow-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-dataflow-skipmode----skipmode-none--0---include-path--bool---false--infer-column-types--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|JSON|`read_json()`|[Referenční dokumentace](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-json-path--filepath--encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---flatten-nested-arrays--bool---false--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|

## <a name="load-data-automatically"></a>Načtení dat automaticky

Chcete-li automaticky načíst data bez zadání typu souboru, použijte `auto_read_file()` funkce. Automaticky se odvodit typ souboru a argumenty potřebné k jeho čtení.

```python
import azureml.dataprep as dprep

dflow = dprep.auto_read_file(path='./data/any-file.txt')
```

Tato funkce je užitečná pro automatické zjišťování typu souboru, kódování a ostatních parsování argumentů vše z jednoho pohodlný vstupní bod. Funkce také automaticky provede následující kroky běžně provádějí při načítání s oddělovači dat:

* Odvození a nastavení oddělovač
* Přeskakuje se prázdné záznamy v horní části souboru
* Odvození a nastavení záhlaví řádku

Případně pokud znáte souboru zadejte předem domluvili a chcete explicitně kontrolovat způsob, jakým je analyzován, použijte funkce konkrétního souboru.

## <a name="load-text-line-data"></a>Načtení dat řádku textu

Chcete-li čtení dat jednoduchý text do toku dat, použijte `read_lines()` bez zadání volitelné parametry.

```python
dflow = dprep.read_lines(path='./data/text_lines.txt')
dflow.head(5)
```

||Perokresba|
|----|-----|
|0|Datum \| \| minimální teploty \| \| nejvyšší teplota|
|1|2015-07-1 \| \| -4.1 \| \| 10.0.|
|2|2015-07-2 \| \| -0.8 \| \| 10.8|


Po data ingestují, spusťte následující kód pro převod objektu toku dat do Pandas dataframe.

```python
pandas_df = dflow.to_pandas_dataframe()
```

## <a name="load-csv-data"></a>Načtení dat ve formátu CSV

Při čtení textových souborů s oddělovači, lze odvodit základní spuštění analýzy parametry (oddělovač, kódování, jestli se má použít, hlavičky atd.). Spusťte následující kód, který pokus o čtení souboru CSV zadáním pouze jeho umístění.

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Hale kraj|10171002158| |
|2|ALABAMA|1|101710|Hale kraj|10171002162| |


Chcete-li při načítání, vyloučit řádky, definujte `skip_rows` parametru. Tento parametr vynechá načítání řádků sestupně v souboru CSV (pomocí indexu se základem 1).

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|ALABAMA|1|101710|Hale kraj|10171002158|29|
|1|ALABAMA|1|101710|Hale kraj|10171002162|40 |

Spusťte následující kód k zobrazení datové typy sloupce.

```python
dflow.dtypes
```
Výstup:

    stnam                     object
    fipst                     object
    leaid                     object
    leanm10                   object
    ncessch                   object
    schnam10                  object
    MAM_MTH00numvalid_1011    object
    dtype: object

Ve výchozím nastavení sady SDK pro Azure Machine Learning Data Prep nemění datový typ. Zdroj dat, které právě čtete z je textový soubor, takže sady SDK čte všechny hodnoty jako řetězce. V tomto příkladu by měl být číselné sloupce analyzovat jako čísla. Nastavte `inference_arguments` parametr `InferenceArguments.current_culture()` automaticky odvodit a převést typy sloupců při čtení souboru.

```
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dflow.dtypes
```
Výstup:

    stnam                      object
    fipst                     float64
    leaid                     float64
    leanm10                    object
    ncessch                   float64
    schnam10                   object
    ALL_MTH00numvalid_1011    float64
    dtype: object


Několik sloupců byly správně rozpozná jako číselný a jejich typ je nastavený na `float64`.

## <a name="use-excel-data"></a>Použití Excelových dat

Sada SDK zahrnuje `read_excel()` funkce načíst soubory aplikace Excel. Ve výchozím nastavení funkce načte první listu v sešitu. Chcete-li definovat konkrétního listu načíst, definujte `sheet_name` parametr s hodnotou řetězec názvu tabulky.

```python
dflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dflow.head(5)
```

| |Sloupec1|Column2|Sloupec3|Column4|Column5|Column6|Column7|Column8| | |
|-|-------|-------|-------|-------|-------|-------|-------|-------|-|-|
|0|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný| |
|1|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný| |
|2|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný| |
|3|pořadí|Titul|Studio|Celosvětově|Domácí / %|Sloupec1|Zámořské / %|Column2|Rok ^| |
|4|1|Miniatury|Fox|2788|760.5|0.273|2027.5|0.727|2009 ^|5|

Výstup ukazuje, že data v druhé tabulce měli tři prázdné řádky před záhlaví. `read_excel()` Funkce obsahuje volitelné parametry pro přeskočení řádků a pomocí hlavičky. Spusťte následující kód, který přeskočí první tři řádky a použít čtvrtý řádek jako záhlaví.

```python
dflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_column_headers=True, skip_rows=3)
```

||pořadí|Titul|Studio|Celosvětově|Domácí / %|Sloupec1|Zámořské / %|Column2|Rok ^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Miniatury|Fox|2788|760.5|0.273|2027.5|0.727|2009 ^|
|1|2|Titanic|Pamětích.|2186.8|658.7|0.301|1528.1|0.699|1997 ^|

## <a name="load-fixed-width-data-files"></a>Načíst soubory s pevnou šířkou dat

Načíst soubory s pevnou šířkou, zadáte seznam znaků posunutí. První sloupec je vždy považován za začínají nulové posunutí.

```python
dflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dflow.head(5)
```

||010000|99999|NEPLATNÉ NORSKO|NO|NO_1|ENRS|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|NEPLATNÉ NORSKO|NO|NO|ENSO||||
|1|010010|99999|JAN MAYEN|NO|JN|ENJA|+70933|-008667|+00090|


Pokud chcete zabránit záhlaví detekce a analyzovat správná data, předat `PromoteHeadersMode.NONE` k `header` parametru.

```python
dflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)
```

||Sloupec1|Column2|Sloupec3|Column4|Column5|Column6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|NEPLATNÉ NORSKO|NO|NO_1|ENRS|Column7|Column8|Column9|
|1|010003|99999|NEPLATNÉ NORSKO|NO|NO|ENSO||||


## <a name="load-sql-data"></a>Načtení dat SQL

Sady SDK můžete také načíst data ze zdroje SQL. V současné době se podporuje jenom Microsoft SQL Server. Chcete-li čtení dat z SQL serveru, vytvořte [ `MSSQLDataSource` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.mssqldatasource?view=azure-dataprep-py) objekt, který obsahuje parametry připojení. Parametr hesla `MSSQLDataSource` přijímá [ `Secret` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#register-secret-value--str--id--str---none-----azureml-dataprep-api-engineapi-typedefinitions-secret) objektu. Můžete vytvořit objekt tajných kódů dvěma způsoby:

* Prováděcí modul zaregistrujte tajný kód a její hodnotu.
* Vytvoření tajného kódu s pouze `id` (Pokud hodnota tajného klíče už je zaregistrovaný v prostředí pro spouštění) pomocí `dprep.create_secret("[SECRET-ID]")`.

```python
secret = dprep.register_secret(value="[SECRET-PASSWORD]", id="[SECRET-ID]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=secret)
```

Jakmile vytvoříte objekt zdroje dat, můžete přejít k číst data z výstupu dotazu.

```python
dflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dflow.head(5)
```

| |ProductID|Název|ProductNumber|Barva|StandardCost|ListPrice|Velikost|Hmotnost|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|Thumbnailphoto nastavuje|ThumbnailPhotoFileName|ROWGUID|ModifiedDate| |
|-|---------|----|-------------|-----|------------|---------|----|------|-----------------|--------------|-------------|-----------|----------------|--------------|----------------------|-------|------------|-|
|0|680|HL Road Frame – Black, 58|FR-R92B 58|Black|1059.3100|1431.50|58|1016.04|18|6|2002-06-01: 00:00:00 + 00:00|Žádný|Žádný|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.GIF|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000 + 00:00|
|1|706|HL Road Frame – Red, 58|FR-R92R 58|Červená|1059.3100|1431.50|58|1016.04|18|6|2002-06-01: 00:00:00 + 00:00|Žádný|Žádný|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.GIF|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000 + 00:00|
|2|707|Sport – 100 Helmet, Red|HL-U509-R|Červená|13.0863|34.99|Žádný|Žádný|35|33|2005-07-01: 00:00:00 + 00:00|Žádný|Žádný|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.GIF|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000 + 00:00|


## <a name="use-azure-data-lake-storage"></a>Použití Azure Data Lake Storage

Existují dva způsoby, jak sada SDK můžete získat potřebné token OAuth pro přístup k Azure Data Lake Storage:

* Získání přístupového tokenu z poslední relace přihlášení uživatele Azure CLI
* Pomocí hlavního názvu služby (SP) a certifikát jako tajný klíč

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>Pomocí přístupového tokenu z poslední relace příkazového řádku Azure

Na místním počítači spusťte následující příkaz.

```azurecli
az login
az account show --query tenantId
dflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dflow.head(5) head
```

> [!NOTE]
> Pokud váš uživatelský účet je členem více než jednoho tenanta Azure, budete muset zadat tenanta ve formátu název hostitele adresy URL AAD.

### <a name="create-a-service-principal-with-the-azure-cli"></a>Vytvoření instančního objektu pomocí Azure CLI

Pomocí Azure CLI k vytvoření instančního objektu a odpovídající certifikát. Tento konkrétní instanční objekt se nakonfigurují `reader` role, kde je její obor omezit na jenom účet úložiště Azure Data Lake "dpreptestfiles".

```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```

Tento příkaz vysílá `appId` a cesta k souboru certifikátu (obvykle ve složce home). Soubor .crt obsahuje veřejný certifikát a privátní klíč ve formátu PEM.

```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Ke konfiguraci seznamu ACL pro systém souborů Azure Data Lake Storage, použijte ID objektu uživatele. V tomto příkladu slouží objekt služby.

```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Ke konfiguraci `Read` a `Execute` přístup pro systém souborů Azure Data Lake Storage, můžete nakonfigurovat seznam ACL pro soubory a složky jednotlivě. Toto je skutečnost, že základní model řízení přístupu HDFS nepodporuje dědičnosti.

```azurecli
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```

```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```

### <a name="acquire-an-oauth-access-token"></a>Získání přístupového tokenu OAuth

Použití `adal` balíčku (`pip install adal`) vytvořit kontext ověřování na MSFT na úrovni tenanta a získání přístupového tokenu OAuth. ADLS, musí být prostředek v žádosti o token pro ' https:\//datalake.azure.net ", která se liší od nejvíce dalších prostředků Azure.

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Web|Ulice|city|Oblast|
|----|------|-----|----|----|----|----|
|0|1012063|Přidružení - Danville Kaledonie Farmářům uvedení na trh|https://sites.google.com/site/caledoniafarmers... ||Danville|Kaledonie|
|1|1011871|Věci odvál čas Stearns Farmářům ' trhu|http://Stearnshomestead.com |Silniční ridge 6975|Parma|Cuyahoga|
|2|1011878|100 přepočet mílí na trhu|https://www.pfcmarkets.com |507 Harrison St|Kalamazoo|Kalamazoo|
|3|1009364|106 S. hlavní ulice Farmářům trhu|http://thetownofsixmile.wordpress.com/ |106 S. hlavní ulice|Šest míle|||
|4|1010691|Ulice 10 Farmářům trh|https://agrimissouri.com/... |Ulice 10 a topolů|Lamar|Barton|

## <a name="next-steps"></a>Další postup

* Zobrazit sadu SDK pro Azure Machine Learning Data Prep [kurzu](tutorial-data-prep.md) příklad řešení konkrétní scénář
