---
title: Řešení potíží s připojením ve službě Azure dosah
description: Tento článek vysvětluje kroky pro řešení potíží s připojením v Azure dosah.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: f76a05757f86308785d4ca678675b87b3fa9d63e
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552906"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Řešení potíží s připojením ve službě Azure dosah

Tento článek popisuje, jak řešit chyby připojení při nastavování kontrol datových zdrojů v Azure dosah.

## <a name="permission-the-credential-on-the-data-source"></a>Oprávnění přihlašovacích údajů ke zdroji dat

Pokud používáte spravovanou identitu nebo instanční objekt jako metodu ověřování pro kontroly, budete muset těmto identitám dovolit přístup k vašemu zdroji dat.

Pro každý typ zdroje jsou k dispozici konkrétní pokyny:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Cosmos DB](register-scan-azure-cosmos-database.md#setting-up-authentication-for-a-scan)
- [Azure Data Explorer](register-scan-azure-data-explorer.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database spravovaná instance](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
- [SQL Server](register-scan-on-premises-sql-server.md#setting-up-authentication-for-a-scan)
- [Power BI](register-scan-power-bi-tenant.md)

## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>Uložení přihlašovacích údajů do trezoru klíčů a používání správného tajného názvu a verze

Přihlašovací údaje musíte také uložit v instanci Azure Key Vault a použít správný tajný název a verzi.

Ověřte to pomocí následujících kroků:

1. Přejděte na Key Vault.
1. Vyberte **Settings** (Nastavení) > **Secrets** (Tajné kódy).
1. Vyberte tajný klíč, který používáte k ověřování vůči zdroji dat pro kontrolu.
1. Vyberte verzi, kterou chcete použít, a ověřte, že heslo nebo klíč účtu jsou správné kliknutím na **Zobrazit hodnotu tajného** kódu. 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>Ověřte oprávnění pro spravovanou identitu dosah na vašem Azure Key Vault

Ověřte, jestli jsou nakonfigurovaná správná oprávnění pro spravovanou dosah identitu pro přístup k vašemu Azure Key Vault.

Pokud to chcete ověřit, proveďte následující kroky:

1. Přejděte do trezoru klíčů a do části **zásady přístupu** .
1. Ověřte, že se v části *aktuální zásady přístupu* zobrazuje spravovaná identita dosah s oprávněním **získat** oprávnění k tajným klíčům aspoň

Pokud se vaše dosah spravovaná identita nezobrazuje, postupujte podle kroků v tématu [Vytvoření a Správa přihlašovacích údajů pro kontroly](manage-credentials.md) a přidejte je. 

## <a name="next-steps"></a>Další kroky

- [Procházet Azure dosah Data Catalog](how-to-browse-catalog.md)
- [Hledání ve službě Azure dosah Data Catalog](how-to-search-catalog.md)
