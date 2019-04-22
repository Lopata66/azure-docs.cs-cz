---
title: Začínáme s Azure Data Lake Storage Gen1 pomocí Powershellu | Dokumentace Microsoftu
description: Pomocí prostředí Azure PowerShell k vytvoření účtu Azure Data Lake Storage Gen1 a provádění základních operací
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 6635b572d11339307083b8aa2aaaba69dc731101
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885855"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Začínáme s Azure Data Lake Storage Gen1 pomocí Azure Powershellu
> [!div class="op_single_selector"]
> * [Azure Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Zjistěte, jak pomocí prostředí Azure PowerShell k vytvoření účtu Azure Data Lake Storage Gen1 a provádění základních operací, jako je vytváření složek, nahrávání a stahování datových souborů, odstranění účtu atd. Další informace o Data Lake Storage Gen1 najdete v tématu [přehled nástroje Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 nebo vyšší**. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Authentication
Tento článek používá jednodušší přístup ověřování s Data Lake Storage Gen1 ve kterém se výzva k zadání přihlašovacích údajů k účtu Azure. Úroveň přístupu k Data Lake Storage Gen1 účtu a systému souborů se pak řídí úrovní přístupu přihlášeného uživatele. Existují však další přístupy také k ověřování ve službě Data Lake Storage Gen1, které jsou **ověřování koncového uživatele** nebo **ověřování služba služba**. Pokyny a další informace o ověřování najdete v tématu [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md) nebo [Ověřování služba-služba](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-data-lake-storage-gen1-account"></a>Vytvoření účtu Data Lake Storage Gen1
1. Otevřete na ploše nové okno Windows PowerShellu. Zadejte následující fragment kódu a přihlaste se ke svému účtu Azure, nastavte předplatné a zaregistrujte poskytovatele Data Lake Storage Gen1. Po zobrazení výzvy k přihlášení, ujistěte se, že se že přihlásíte jako jeden ze správců/vlastník předplatného:

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Účet Data Lake Storage Gen1 souvisí s skupiny prostředků Azure. Začněte vytvořením skupiny prostředků Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Vytvoření skupiny prostředků Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Vytvoření skupiny prostředků Azure")
3. Vytvoření účtu Data Lake Storage Gen1. Zadaný název musí obsahovat jenom malá písmena a číslice.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
        New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    ![Vytvoření účtu Data Lake Storage Gen1](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "vytvoření účtu Data Lake Storage Gen1")
4. Ověřte, že se účet úspěšně vytvořil.

        Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name

    Výstup této rutiny by měl být **True** (pravda).

## <a name="create-directory-structures-in-your-data-lake-storage-gen1-account"></a>Vytváření struktur adresářů v účtu Data Lake Storage Gen1
V rámci účtu Data Lake Storage Gen1 ke správě a ukládání dat můžete vytvářet adresáře.

1. Zadejte kořenový adresář.

        $myrootdir = "/"
2. V zadaném kořenovém adresáři vytvořte nový adresář s názvem **mynewdirectory**.

        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
3. Ověřte, že se nový adresář úspěšně vytvořil.

        Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir

    Měl by se zobrazit výstup jako na následujícím snímku obrazovky:

    ![Ověření adresáře](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Ověření adresáře")

## <a name="upload-data-to-your-data-lake-storage-gen1-account"></a>Nahrání dat do účtu Data Lake Storage Gen1
Data můžete nahrát do Data Lake Storage Gen1 přímo na úrovni kořenového adresáře nebo do adresáře, který jste vytvořili v rámci účtu. Fragmenty kódu v této části ukazují, jak nahrát ukázková data do adresáře (**mynewdirectory**), který jste vytvořili v předchozí části.

Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Stáhněte si tento soubor a uložte ho do místního adresáře v počítači, například C:\sampledata\.

    Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-storage-gen1-account"></a>Přejmenování, stažení a odstranění dat z účtu Data Lake Storage Gen1
Pokud chcete přejmenovat soubor, použijte tento příkaz:

    Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Pokud chcete stáhnout soubor, použijte tento příkaz:

    Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Pokud chcete odstranit soubor, použijte tento příkaz:

    Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Po zobrazení výzvy zadejte **Y**, a položku tak odstraňte. Pokud chcete odstranit více souborů, můžete zadat všechny požadované cesty oddělené čárkou.

    Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-data-lake-storage-gen1-account"></a>Odstranění účtu Data Lake Storage Gen1
Použijte následující příkaz k odstranění účtu Data Lake Storage Gen1.

    Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name

Po zobrazení výzvy zadejte **Y**, a účet tak odstraňte.

## <a name="next-steps"></a>Další postup
* [Průvodce laděním výkonu pro použití Powershellu s Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-powershell.md)
* [Použití Azure Data Lake Storage Gen1 pro potřeby velkého objemu dat](data-lake-store-data-scenarios.md) 
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
