---
title: Přístup k souborům v úložišti na vyžádání SQL (Preview)
description: Popisuje dotazování souborů úložiště pomocí prostředků SQL na vyžádání (ve verzi Preview) v rámci synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: d7f990b059346c4c782ca923e663997317c4df16
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046882"
---
# <a name="accessing-external-storage-in-synapse-sql-on-demand"></a>Přístup k externímu úložišti v synapse SQL (na vyžádání)

Tento dokument popisuje, jak může uživatel číst data ze souborů uložených na Azure Storage v synapse SQL (na vyžádání). Uživatelé mají k dispozici následující možnosti pro přístup k úložišti:

- Funkce [OpenRowset](develop-openrowset.md) , která umožňuje provádět dotazy ad-hoc přes soubory v Azure Storage.
- [Externí tabulka](develop-tables-external-tables.md) , která je předdefinovanou datovou strukturou založenou na sadě externích souborů.

Uživatel může použít [jiné metody ověřování](develop-storage-files-storage-access-control.md) , jako je například ověřování předávacího služby Azure AD (výchozí pro objekty zabezpečení Azure AD) a ověřování SAS (výchozí pro objekty zabezpečení SQL).

## <a name="openrowset"></a>OPENROWSET

Funkce [OpenRowset](develop-openrowset.md) umožňuje uživateli číst soubory z úložiště Azure.

### <a name="query-files-using-openrowset"></a>Dotazování souborů pomocí OPENROWSET

OPENROWSET umožňuje uživatelům dotazovat se na externí soubory ve službě Azure Storage, pokud mají přístup k úložišti. Uživatel, který je připojený k Synapsemu koncovému bodu SQL na vyžádání, by měl použít následující dotaz ke čtení obsahu souborů v Azure Storage:

```sql
SELECT * FROM
 OPENROWSET(BULK 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/*.parquet', format= 'parquet') as rows
```

Uživatel může získat přístup k úložišti pomocí následujících pravidel přístupu:

- Uživatel Azure AD – OPENROWSET bude používat identitu Azure AD volajícího pro přístup k Azure Storage nebo přístupu k úložišti s anonymním přístupem.
- Uživatel SQL – OPENROWSET bude mít přístup k úložišti s anonymním přístupem.

Objekty zabezpečení SQL mohou také použít OPENROWSET k přímému dotazování na soubory chráněné pomocí tokenů SAS nebo spravované identity pracovního prostoru. Pokud uživatel SQL tuto funkci spustí, musí uživatel s `ALTER ANY CREDENTIAL` oprávněním vytvořit pověření v oboru serveru, které odpovídá adrese URL ve funkci (pomocí názvu a kontejneru úložiště) a oprávnění uděleným odkazům pro toto pověření pro volající funkci OPENROWSET:

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES CREDENTIAL::[https://<storage_account>.dfs.core.windows.net/<container>] TO sqluser
```

Pokud není k dispozici žádné přihlašovací údaje na úrovni serveru, které by odpovídaly adrese URL nebo uživatel SQL nemá oprávnění pro přístup k tomuto pověření, bude vrácena chyba. Objekty zabezpečení SQL se nemůžou zosobnit pomocí nějaké identity Azure AD.

> [!NOTE]
> Tato verze OPENROWSET je navržená pro rychlé a snadné zkoumání dat pomocí výchozího ověřování. Chcete-li využít zosobnění nebo spravovanou identitu, použijte OPENROWSET se zdrojem dat popsaným v následující části.

### <a name="querying-data-sources-using-openrowset"></a>Dotazování na zdroje dat pomocí OPENROWSET

OPENROWSET umožňuje uživateli zadat dotaz na soubory umístěné na některém externím zdroji dat:

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.parquet',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'parquet') as rows
```

Uživatel s oprávněním k řízení databáze by musel vytvořit pověření s ROZSAHem databáze, která budou použita pro přístup k úložišti a EXTERNÍmu zdroji dat, který určuje adresu URL zdroje dat a přihlašovacích údajů, které by měly být použity:

```sql
CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

POVĚŘENÍ v oboru databáze určuje, jak přistupovat k souborům na odkazovaném zdroji dat (aktuálně SAS a spravovanou identitu).

Volající musí mít jedno z následujících oprávnění ke spuštění funkce OPENROWSET:

- Jedno z oprávnění ke spuštění OPENROWSET:
  - `ADMINISTER BULK OPERATIONS`povolí přihlášení k provedení funkce OPENROWSET.
  - `ADMINISTER DATABASE BULK OPERATIONS`umožňuje, aby uživatel s oborem databáze spustil funkci OPENROWSET.
- ODKAZUJE na přihlašovací údaje VYMEZENé databáze na přihlašovací údaje, na které se odkazuje v EXTERNÍm zdroji dat.

#### <a name="accessing-anonymous-data-sources"></a>Přístup k anonymním zdrojům dat

Uživatel může vytvořit externí zdroj dat bez PŘIHLAŠOVACÍch údajů, který bude odkazovat na úložiště veřejného přístupu nebo používat předávací ověřování Azure AD:

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```

## <a name="external-table"></a>EXTERNÍ TABULKA

Uživatel s oprávněními ke čtení tabulky může získat přístup k externím souborům pomocí externí tabulky vytvořené na základě sady Azure Storage složek a souborů.

Uživatel, který má [oprávnění k vytvoření externí tabulky](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=sql-server-ver15#permissions) (například CREATE TABLE a změnu PŘIHLAŠOVACÍch údajů s rozsahem databáze), může pomocí následujícího skriptu vytvořit tabulku nad Azure Storage zdroj dat:

```sql
CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( ProductKey int, ProductLabel nvarchar, ProductName nvarchar )
WITH
(
LOCATION='/DimProduct/year=*/month=*' ,
DATA_SOURCE = AzureDataLakeStore ,
FILE_FORMAT = TextFileFormat
) ;
```

Uživatel s oprávněním řídicí databáze by musel vytvořit pověření v oboru databáze, které bude použito pro přístup k úložišti a EXTERNÍmu zdroji dat, který určuje adresu URL zdroje dat a přihlašovacích údajů, které by měly být použity:

```sql
CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>' ,
 CREDENTIAL = cred
 ) ;
```

POVĚŘENÍ oboru databáze určuje, jak přistupovat k souborům na odkazovaném zdroji dat.

### <a name="reading-external-files-with-external-table"></a>Čtení externích souborů s externí TABULKou

EXTERNÍ tabulka umožňuje číst data ze souborů, na které se odkazuje přes zdroj dat pomocí standardního příkazu SELECT jazyka SQL:

```sql
SELECT *
FROM dbo.DimProductsExternal
```

Volající musí mít následující oprávnění pro čtení dat:
- `SELECT`oprávnění pro externí tabulku
- `REFERENCES DATABASE SCOPED CREDENTIAL`oprávnění, pokud `DATA SOURCE` má`CREDENTIAL`

## <a name="permissions"></a>Oprávnění

V následující tabulce jsou uvedena požadovaná oprávnění pro výše uvedené operace.

| Dotaz | Požadovaná oprávnění|
| --- | --- |
| OPENROWSET (BULK) bez DataSource | `ADMINISTER BULK OPERATIONS``ADMINISTER DATABASE BULK OPERATIONS`přihlašovací údaje, nebo přihlášení SQL musí mít přihlašovací údaje:: \<URL> pro úložiště chráněné přes SAS. |
| OPENROWSET (hromadné) se zdrojem dat bez přihlašovacích údajů | `ADMINISTER BULK OPERATIONS`nebo `ADMINISTER DATABASE BULK OPERATIONS` , |
| OPENROWSET (hromadné) se zdrojem dat s přihlašovacími údaji | `REFERENCES DATABASE SCOPED CREDENTIAL`a jedna z `ADMINISTER BULK OPERATIONS` nebo`ADMINISTER DATABASE BULK OPERATIONS` |
| VYTVOŘIT EXTERNÍ ZDROJ DAT | `ALTER ANY EXTERNAL DATA SOURCE` a `REFERENCES DATABASE SCOPED CREDENTIAL` |
| VYTVOŘIT EXTERNÍ TABULKU | `CREATE TABLE`, `ALTER ANY SCHEMA` , `ALTER ANY EXTERNAL FILE FORMAT` a`ALTER ANY EXTERNAL DATA SOURCE` |
| VYBRAT Z EXTERNÍ TABULKY | `SELECT TABLE` a `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CETAS | Vytvoření tabulky- `CREATE TABLE` , `ALTER ANY SCHEMA` , a `ALTER ANY DATA SOURCE` `ALTER ANY EXTERNAL FILE FORMAT` . Čtení dat: `ADMINISTER BULK OPERATIONS` nebo `REFERENCES CREDENTIAL` nebo pro `SELECT TABLE` jednotlivé tabulky, zobrazení nebo funkce v dotazech + R/W v úložišti |

## <a name="next-steps"></a>Další kroky

Nyní jste připraveni pokračovat s následujícím článkem:

- [Dotazování dat v úložišti](query-data-storage.md)

- [Dotazování souboru CSV](query-single-csv-file.md)

- [Dotazování složek a několika souborů](query-folders-multiple-csv-files.md)

- [Dotazování konkrétních souborů](query-specific-files.md)

- [Dotazování souborů Parquet](query-parquet-files.md)

- [Vnořené typy dotazů](query-parquet-nested-types.md)

- [Dotazování souborů JSON](query-json-files.md)

- [Vytváření a používání zobrazení](create-use-views.md)
