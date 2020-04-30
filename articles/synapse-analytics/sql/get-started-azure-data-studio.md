---
title: 'Azure Data Studio (Preview): připojení a dotazování SQL synapse'
description: Pomocí Azure Data Studio (Preview) se můžete připojit k SQL Azure synapse Analytics a dotazovat se na něj synapse.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 561ea1a4f3577e037708c3b090188c59bd3c4aad
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82187541"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio-preview"></a>Připojení k synapse SQL pomocí Azure Data Studio (Preview)

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

K připojení a dotazování SQL synapse v Azure synapse Analytics můžete použít [Azure Data Studio (Preview)](/sql/azure-data-studio/download-azure-data-studio?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) . 

## <a name="connect"></a>Připojit

Pokud se chcete připojit k synapse SQL, otevřete Azure Data Studio a vyberte **nové připojení**.

![Otevřít Azure Data Studio](./media/get-started-azure-data-studio/1-start.png)

Jako **Typ připojení**vyberte **Microsoft SQL Server** .

Připojení vyžaduje následující parametry:

* **Server:** Server ve formě `<Azure Synapse workspace name>`– OnDemand.SQL.azuresynapse.NET
* **Databáze:** Název databáze

> [!NOTE]
> Pokud chcete použít **SQL na vyžádání (Preview)** , adresa URL by měla vypadat takto:
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net.
>
> Pokud byste chtěli použít **fond SQL** , adresa URL by měla vypadat takto:
>
> - `<Azure Synapse workspace name>`. sql.azuresynapse.net

Jako **typ ověřování**vyberte možnost **ověřování systému Windows**, **Azure Active Directory**nebo **přihlášení SQL** .

Pokud chcete jako typ ověřování použít **přihlášení SQL** , přidejte parametry uživatelského jména a hesla:

* **Uživatel:** Uživatel serveru ve formuláři`<User>`
* **Heslo:** Heslo přidružené k uživateli

Chcete-li použít Azure Active Directory, je nutné vybrat požadovaný typ ověřování.

![Ověřování AAD](./media/get-started-azure-data-studio/3-aad-auth.png)

Následující snímek obrazovky ukazuje **Podrobnosti o připojení** pro **ověřování systému Windows**:

![Ověřování systému Windows](./media/get-started-azure-data-studio/3-windows-auth.png)

Následující snímek obrazovky ukazuje **Podrobnosti připojení** pomocí **přihlášení SQL**:

![Přihlášení k SQL serveru](./media/get-started-azure-data-studio/2-database-details.png)

Po úspěšném přihlášení byste měli vidět řídicí panel podobný tomuto: ![řídicí panel](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Dotaz

Po připojení můžete dotazovat synapse SQL pomocí podporovaných příkazů [jazyka Transact-SQL (T-SQL)](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) na instanci. Začněte tím, že vyberete **Nový dotaz** v zobrazení řídicího panelu.

![Nový dotaz](./media/get-started-azure-data-studio/5-new-query.png)

Například můžete použít následující příkaz Transact-SQL k [dotazování souborů Parquet](query-parquet-files.md) pomocí SQL na vyžádání:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Další kroky 
Prozkoumejte další způsoby, jak se připojit k synapse SQL: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Sqlcmd](get-started-connect-sqlcmd.md)
 
