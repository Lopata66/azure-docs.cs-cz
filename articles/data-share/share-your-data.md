---
title: 'Kurz: sdílení mimo vaši organizaci – Azure Data Share'
description: Kurz – sdílení dat se zákazníky a partnery pomocí Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/30/2020
ms.openlocfilehash: 1de793dc2f4f72efb67c954e60262c3d7f1b74fc
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2020
ms.locfileid: "87511969"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Kurz: sdílení dat pomocí Azure Data Share  

V tomto kurzu se naučíte, jak nastavit novou sdílenou složku Azure a začít sdílet data se zákazníky a partnery mimo vaši organizaci Azure. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte instanci služby Data Share.
> * Přidejte do ní datové sady.
> * Povolte plán snímků pro sdílenou složku dat. 
> * Přidejte do ní příjemce. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Přihlašovací e-mailová adresa Azure vašeho příjemce (pomocí e-mailového aliasu nebude fungovat).
* Pokud je zdrojové úložiště dat Azure v jiném předplatném Azure, než je ten, který použijete k vytvoření prostředku pro sdílení dat, zaregistrujte [poskytovatele prostředků Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) v předplatném, kde se nachází úložiště dat Azure. 

### <a name="share-from-a-storage-account"></a>Sdílet z účtu úložiště:

* Účet Azure Storage: Pokud ho ještě nemáte, můžete vytvořit [účet Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Oprávnění k zápisu do účtu úložiště, který je k dispozici v *Microsoft. Storage/storageAccounts/Write*. Toto oprávnění existuje v roli Přispěvatel.
* Oprávnění k přidání přiřazení role k účtu úložiště, který je k dispozici v *Microsoft. autorizace/přiřazení role/zápis*. Toto oprávnění existuje v roli vlastníka. 


### <a name="share-from-a-sql-based-source"></a>Sdílení ze zdroje založeného na jazyce SQL:

* Azure SQL Database nebo Azure synapse Analytics (dříve Azure SQL Data Warehouse) s tabulkami a zobrazeními, které chcete sdílet.
* Oprávnění k zápisu do databází na SQL serveru, které jsou k dispozici v *Microsoft. SQL/serverech/databázích/Write*. Toto oprávnění existuje v roli Přispěvatel.
* Oprávnění ke sdílení dat pro přístup k datovému skladu. To lze provést pomocí následujících kroků: 
    1. Nastavte jako správce Azure Active Directory pro SQL Server.
    1. Připojte se k Azure SQL Database/datový sklad pomocí Azure Active Directory.
    1. Pomocí Editoru dotazů (Preview) spusťte následující skript, který přidá identitu spravovaného prostředku pro sdílení dat jako db_datareader. Je nutné se připojit pomocí služby Active Directory a SQL Server ověřování. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Všimněte si, že *<share_acc_name>* je název vašeho prostředku pro sdílení dat. Pokud jste ještě nevytvořili prostředek pro sdílení dat, můžete se k tomuto předběžnému požadavku vrátit později.  

* Uživatel Azure SQL Database s přístupem "db_datareader", který umožňuje procházet a vybírat tabulky a zobrazení, která chcete sdílet. 

* IP adresa klienta SQL Server přístup k bráně firewall. To lze provést pomocí následujících kroků: 
    1. V systému SQL Server v Azure Portal přejděte na *brány firewall a virtuální sítě* .
    1. Kliknutím na přepínač **zapnuto** povolíte přístup ke službám Azure.
    1. Klikněte na **+ Přidat IP adresu klienta** a klikněte na **Uložit**. IP adresa klienta se může změnit. Tento proces může být nutné zopakovat při příštím sdílení dat SQL z Azure Portal. Můžete také přidat rozsah IP adres. 

### <a name="share-from-azure-data-explorer"></a>Sdílení z Azure Průzkumník dat
* Cluster Azure Průzkumník dat s databázemi, které chcete sdílet.
* Oprávnění k zápisu do clusteru Azure Průzkumník dat, který se nachází v *Microsoft. Kusto/Clusters/Write*. Toto oprávnění existuje v roli Přispěvatel.
* Oprávnění k přidání přiřazení role do clusteru Azure Průzkumník dat, který je k dispozici v *Microsoft. autorizace/přiřazení role/zápis*. Toto oprávnění existuje v roli vlastníka.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na web [Azure Portal](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Vytvoření účtu pro sdílení dat

Vytvořte prostředek sdílené složky Azure ve skupině prostředků Azure.

1. Vyberte tlačítko **Vytvořit prostředek** (+) v levém horním rohu portálu.

1. Vyhledejte *sdílenou složku dat*.

1. Vyberte sdílet data a vyberte **vytvořit**.

1. Vyplňte základní podrobnosti prostředku Azure Data Share s následujícími informacemi. 

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Název | *datashareacount* | Zadejte název vašeho účtu pro sdílení dat. |
    | Předplatné | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro svůj účet pro sdílení dat.|
    | Skupina prostředků | *test-Resource-Group* | Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků. |
    | Umístění | *USA – východ 2* | Vyberte oblast pro svůj účet pro sdílení dat.
    | | |

1. Vyberte **vytvořit** a zřiďte svůj účet pro sdílení dat. Zřizování nového účtu pro sdílení dat obvykle trvá přibližně 2 minuty nebo méně. 

1. Po dokončení nasazení vyberte **Přejít k prostředku**.

## <a name="create-a-data-share"></a>Vytvoření sdílené složky dat

1. Přejděte na stránku s přehledem sdílení dat.

    ![Sdílení dat](./media/share-receive-data.png "Sdílení dat") 

1. Vyberte možnost **začít sdílet data**.

1. Vyberte **Vytvořit**.   

1. Vyplňte podrobnosti pro vaši sdílenou složku dat. Zadejte název, typ sdílení, popis obsahu sdílení a podmínek použití (volitelné). 

    ![EnterShareDetails](./media/enter-share-details.png "Zadat podrobnosti o sdílené složce") 

1. Vybrat **pokračovat**

1. Pokud chcete do sdílené složky dat přidat datové sady, vyberte **přidat datové sady**. 

    ![Datové sady](./media/datasets.png "Datové sady")

1. Vyberte typ datové sady, který chcete přidat. V závislosti na typu sdílené položky (snímku nebo na místě), který jste vybrali v předchozím kroku, se zobrazí jiný seznam typů datových sad. Pokud sdílíte z Azure SQL Database nebo Azure SQL Data Warehouse, budete vyzváni k zadání některých přihlašovacích údajů SQL. Proveďte ověření pomocí uživatele, který jste vytvořili jako součást požadavků.

    ![AddDatasets](./media/add-datasets.png "Přidat datové sady")    

1. Přejděte k objektu, který chcete sdílet, a vyberte přidat datové sady. 

    ![SelectDatasets](./media/select-datasets.png "Vybrat datové sady")    

1. Na kartě příjemci zadejte do e-mailových adres vašeho příjemce dat a vyberte + Přidat příjemce. 

    ![AddRecipients](./media/add-recipient.png "Přidání příjemců") 

1. Vybrat **pokračovat**

1. Pokud jste vybrali typ sdílené složky snímků, můžete nakonfigurovat plán snímků pro poskytování aktualizací vašich dat příjemci dat. 

    ![EnableSnapshots](./media/enable-snapshots.png "Povolit snímky") 

1. Vyberte čas spuštění a interval opakování. 

1. Vybrat **pokračovat**

1. Na kartě Revize + vytvořit zkontrolujte obsah balíčku, nastavení, příjemce a nastavení synchronizace. Vyberte **Vytvořit**.

Vaše sdílená složka Azure je teď vytvořená a příjemce vaší sdílené složky je teď připravený přijmout vaše pozvání. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte, jak vytvořit sdílenou složku Azure a pozvat příjemce. Další informace o tom, jak může příjemce dat přijmout a přijmout sdílená data, najdete v kurzu [přijímání a přijímání dat](subscribe-to-data-share.md) . 
