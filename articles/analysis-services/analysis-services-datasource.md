---
title: Zdroje dat podporované v Azure Analysis Services | Microsoft Docs
description: Popisuje zdroje dat a konektory podporované tabulkami 1200 a vyššími datovými modely v Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dc25c853a37de5c310d37e7ee64c6f762283cb0a
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077435"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Podporované zdroje dat ve službě Azure Analysis Services

Zdroje dat a konektory zobrazené v průvodci získáním dat nebo importu tabulek v aplikaci Visual Studio s Analysis Services projekty jsou zobrazeny pro Azure Analysis Services i SQL Server Analysis Services. V Azure Analysis Services ale nejsou podporované všechny zdroje dat a konektory. Typy zdrojů dat, ke kterým se můžete připojit, závisí na mnoha faktorech, například na úrovni kompatibility modelů, dostupných datových konektorech, typu ověřování a místní podpoře datových bran. Následující tabulky popisují podporované zdroje dat pro Azure Analysis Services.

## <a name="azure-data-sources"></a>Zdroje dat Azure

|Zdroj dat  |V paměti  |DirectQuery  |Poznámky |
|---------|---------|---------|---------|
|Azure SQL Database      |   Ano      |    Yes      |<sup>[2](#azprovider)</sup>, <sup> [3](#azsqlmanaged)</sup>|
|Azure synapse Analytics (SQL DW)      |   Ano      |   Yes       |<sup>[2](#azprovider)</sup>|
|Azure Blob Storage      |   Yes       |    No      | <sup>[první](#tab1400a)</sup> |
|Azure Table Storage     |   Yes       |    No      | <sup>[první](#tab1400a)</sup>|
|Azure Cosmos DB     |  Yes        |  No        |<sup>[první](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   Yes       |    No      |<sup>[první](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Yes       |    No      |<sup>[1](#tab1400a)</sup>, <sup> [5](#gen2)</sup>|
|HDFS Azure HDInsight    |     Yes     |   No       |<sup>[první](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Yes       |   No       |<sup>[1](#tab1400a)</sup>, <sup> [4](#databricks)</sup>|
||||

**Poznámky:**

<a name="tab1400a">1</a> -tabelární 1400 a vyšší modely.  
<a name="azprovider">2</a> – Pokud jsou zadané jako zdroj dat *zprostředkovatele* v tabulkovém 1200 a vyšších modelech, v paměti i v modelech DIRECTQUERY OLE DB vyžaduje SQL Server MSOLEDBSQL (doporučeno), SQL Server Native Client 11,0 nebo .NET Framework Zprostředkovatel dat pro SQL Server.  
<a name="azsqlmanaged">3</a> – podporuje se spravovaná instance Azure SQL. Vzhledem k tomu, že se spravovaná instance SQL spouští v rámci virtuální sítě Azure s privátní IP adresou, musí být v instanci povolený veřejný koncový bod. Pokud není povolená, vyžaduje se [místní brána dat](analysis-services-gateway.md) .  
<a name="databricks">4</a> – Azure Databricks používání konektoru Sparku se v tuto chvíli nepodporuje.  
<a name="gen2">5</a> -adls Gen2 konektor se momentálně nepodporuje, ale konektor Azure Blob Storage můžete použít se zdrojem dat adls Gen2.

## <a name="other-data-sources"></a>Other data sources

|Zdroj dat | V paměti | DirectQuery |Poznámky   |
|  --- | --- | --- | --- |
|Databáze aplikace Access     |  Yes | No |  |
|Active Directory     |  Yes | No | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  Yes | No |  |
|Systém Analytics Platform System     |  Yes | No |  |
|Soubor CSV  |Yes | No |  |
|Dynamics 365     |  Yes | No | <sup>[6](#tab1400b)</sup> |
|Excelový sešit     |  Yes | No |  |
|Výměna      |  Yes | No | <sup>[6](#tab1400b)</sup> |
|Složka      |Ano | No | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |Yes | No |  |
|Dokument JSON      |  Yes | No | <sup>[6](#tab1400b)</sup> |
|Řádky z binárního souboru      | Yes | No | <sup>[6](#tab1400b)</sup> |
|Databáze MySQL     | Yes | No |  |
|Datový kanál OData      |  Yes | No | <sup>[6](#tab1400b)</sup> |
|Dotaz ODBC     | Yes | No |  |
|OLE DB     |   Yes | No |  |
|Oracle  | Ano  |Yes  | <sup>[9](#oracle)</sup> |
|Databáze PostgreSQL   | Yes | No | <sup>[6](#tab1400b)</sup> |
|Objekty Salesforce|  Yes | No | <sup>[6](#tab1400b)</sup> |
|Sestavy Salesforce |Yes | No | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  Yes | No |  |
|SAP Business Warehouse    |  Yes | No | <sup>[6](#tab1400b)</sup> |
|Sharepointový seznam      |   Yes | No | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |Ano   | Yes  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|SQL Server datový sklad |Ano   | Yes  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|Databáze Sybase     |  Yes | No |  |
|Teradata | Ano  | Yes  | <sup>[10](#teradata)</sup> |
|Soubor TXT  |Yes | No |  |
|Tabulka XML    |  Yes | No | <sup>[6](#tab1400b)</sup> |
| | | |

**Poznámky:**  
<a name="tab1400b">6</a> – tabulkové 1400 a vyšší modely.  
<a name="sqlim">7</a> – při zadání jako zdroje dat *zprostředkovatele* v tabulkových 1200 a vyšších modelech zadejte Microsoft OLE DB Driver for SQL Server MSOLEDBSQL (doporučeno), SQL Server Native Client 11,0 nebo .NET Framework Zprostředkovatel dat pro SQL Server.  
<a name="instgw">8</a> – Pokud zadáte MSOLEDBSQL jako poskytovatele dat, může být nutné stáhnout a nainstalovat [ovladač Microsoft OLE DB pro SQL Server](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) na stejném počítači jako místní brána dat.  
<a name="oracle">9</a> – pro tabelární modely 1200 nebo jako zdroj dat *zprostředkovatele* v tabulkách 1400 s více modely určete Oracle zprostředkovatel dat pro .NET.  
<a name="teradata">10</a> – pro tabelární modely 1200 nebo jako zdroj dat *zprostředkovatele* v tabulkách 1400 s více modely určete zprostředkovatel dat Teradata pro .NET.  
<a name="filesSP">11</a> – soubory v místní službě SharePoint nejsou podporovány.

Připojení k místním zdrojům dat z Azure Analysis Services serveru vyžaduje místní [bránu](analysis-services-gateway.md). Při použití brány se vyžadují 64 zprostředkovatelé.

## <a name="understanding-providers"></a>Principy zprostředkovatelů

Při vytváření tabelárních projektů 1400 a vyšších modelů v aplikaci Visual Studio ve výchozím nastavení neurčíte poskytovatele dat při připojování ke zdroji dat pomocí **získat data**. Tabelární 1400 a vyšší modely používají konektory [Power Query](/power-query/power-query-what-is-power-query) ke správě připojení, datových dotazů a hybridních webových aplikací mezi zdrojem dat a Analysis Services. Někdy se v nastaveních vlastností připojení označují jako *strukturovaná* připojení zdrojů dat. Můžete však povolit starší zdroje dat pro projekt modelu v aplikaci Visual Studio. Pokud je tato možnost povolená, můžete použít **Průvodce importem tabulky** pro připojení k určitým zdrojům dat, které jsou tradičně podporované v tabulkovém 1200 a nižších modelech jako *starší verze*nebo zdroje dat *poskytovatele* . Pokud je zadaný jako zdroj dat zprostředkovatele, můžete zadat konkrétního poskytovatele dat a další rozšířené vlastnosti připojení. Můžete se třeba připojit k SQL Server instanci datového skladu nebo dokonce k Azure SQL Database jako k staršímu zdroji dat. Pak můžete vybrat ovladač OLE DB pro poskytovatele dat SQL Server MSOLEDBSQL. V takovém případě může poskytovatel dat OLE DB poskytovat lepší výkon prostřednictvím konektoru Power Query. 

Při použití Průvodce importem tabulky v aplikaci Visual Studio připojení k jakémukoli zdroji dat vyžaduje poskytovatele dat. Pro vás je vybraný výchozí zprostředkovatel dat. V případě potřeby můžete změnit zprostředkovatele dat. Typ poskytovatele, který zvolíte, může záviset na výkonu, bez ohledu na to, jestli model používá úložiště v paměti nebo DirectQuery a které Analysis Services platforma, do které model nasazujete.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Určení zdrojů dat zprostředkovatele v tabulkových projektech 1400 a vyšších

Chcete-li povolit zdroje dat poskytovatele, v aplikaci Visual Studio klikněte na možnost **nástroje**  >  **Možnosti**  >  **Analysis Services tabelární**  >  **Import dat**, vyberte možnost **Povolit starší zdroje dat**.

![Povolit starší zdroje dat](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Se zapnutými staršími zdroji dat v **Průzkumníkovi tabulkových modelů**klikněte pravým tlačítkem na **zdroje dat**  >  **Importovat ze zdroje dat (starší verze)**.

![Starší zdroje dat v Průzkumníkovi tabulkových modelů](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Stejně jako u tabulkových projektů tabelárních 1200 se pomocí **Průvodce importem tabulky** připojte ke zdroji dat. Na stránce připojit klikněte na **Upřesnit**. Zadejte poskytovatele dat a další nastavení připojení v **Nastavení Upřesnit vlastnosti**.

![Rozšířené vlastnosti zdrojů dat ve starší verzi](media/analysis-services-datasource/aas-import-legacy-advanced.png)

## <a name="impersonation"></a>Zosobnění
V některých případech může být nutné zadat jiný účet zosobnění. Účet zosobnění lze zadat v aplikaci Visual Studio nebo SQL Server Management Studio (SSMS).

Pro místní zdroje dat:

* Pokud používáte ověřování SQL, měl by se jednat o zosobnění účtu služby.
* Pokud používáte ověřování systému Windows, nastavte uživatele nebo heslo systému Windows. Pro SQL Server se ověřování Windows s konkrétním účtem zosobnění podporuje jenom pro datové modely v paměti.

Pro cloudové zdroje dat:

* Pokud používáte ověřování SQL, měl by se jednat o zosobnění účtu služby.

## <a name="oauth-credentials"></a>Přihlašovací údaje OAuth

Pro tabelární modely na úrovni kompatibility 1400 a vyšší s použitím režimu v paměti, Azure SQL Database, Azure synapse (dříve SQL Data Warehouse), Dynamics 365 a SharePointového seznamu podporují přihlašovací údaje OAuth. Azure Analysis Services spravuje aktualizace tokenu pro zdroje dat OAuth, aby nedocházelo k vypršení časových limitů pro dlouhotrvající operace aktualizace. Pokud chcete generovat platné tokeny, nastavte přihlašovací údaje pomocí SSMS.

Režim přímého dotazu není u přihlašovacích údajů OAuth podporován.

## <a name="next-steps"></a>Další kroky

* [Místní brána](analysis-services-gateway.md)
* [Správa serveru](analysis-services-manage.md)
