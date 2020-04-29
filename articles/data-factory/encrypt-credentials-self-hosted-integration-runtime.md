---
title: Šifrovat přihlašovací údaje v Azure Data Factory
description: Naučte se šifrovat a ukládat přihlašovací údaje pro místní úložiště dat na počítači pomocí prostředí Integration runtime v místním prostředí.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 8ce26360aca8d8408135cbe89aabff4f923013b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416373"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Šifrování přihlašovacích údajů pro místní úložiště dat v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Můžete šifrovat a ukládat přihlašovací údaje místních úložišť dat (propojených služeb s citlivými informacemi) na počítači s integrovaným prostředím Integration runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Předáte soubor definice JSON s přihlašovacími údaji k <br/>Rutina [**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) k vytvoření výstupního souboru definice JSON s šifrovanými přihlašovacími údaji. Pak pomocí aktualizované definice JSON vytvořte propojené služby.

## <a name="author-sql-server-linked-service"></a>Vytvořit propojenou službu SQL Server
V libovolné složce vytvořte soubor JSON s názvem **SqlServerLinkedService. JSON** s následujícím obsahem:  

Před `<servername>`uložením `<username>`souboru hodnoty `<password>` , `<databasename>`, a nahraďte hodnotami pro SQL Server. A nahraďte `<integration runtime name>` názvem vašeho prostředí Integration runtime. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Šifrování přihlašovacích údajů
K šifrování citlivých dat z datové části JSON v místním prostředí Integration runtime, spusťte rutinu **New-AzDataFactoryV2LinkedServiceEncryptedCredential**a předejte ji do datové části JSON. Tato rutina zajišťuje šifrování přihlašovacích údajů pomocí rozhraní DPAPI a uložených místně v uzlu Integration runtime v místním prostředí. Výstupní datová část obsahující šifrovaný odkaz na přihlašovací údaje může být přesměrovaná na jiný soubor JSON (v tomto případě "encryptedLinkedService. JSON").

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Použití formátu JSON s šifrovanými přihlašovacími údaji
Teď použijte výstupní soubor JSON z předchozího příkazu, který obsahuje šifrované přihlašovací údaje, a nastavte **SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Další kroky
Informace o bezpečnostních faktorech při přesunu dat najdete v tématu [požadavky na zabezpečení při přesunu dat](data-movement-security-considerations.md).

