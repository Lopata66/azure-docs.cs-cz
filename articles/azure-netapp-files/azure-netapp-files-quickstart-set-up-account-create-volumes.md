---
title: Rychlý Start – Nastavení Azure NetApp Files a vytvoření svazku NFS | Microsoft Docs
description: Rychlý Start – popisuje, jak rychle nastavit Azure NetApp Files a vytvořit svazek.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/12/2019
ms.author: b-juche
ms.openlocfilehash: 9676b10eafbc72a04cb68fc828a72f77e6c3916f
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298585"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Rychlý Start: nastavení Azure NetApp Files a vytvoření svazku NFS 

V tomto článku se dozvíte, jak rychle nastavit Azure NetApp Files a vytvořit svazek. 

V tomto rychlém startu vytvoříte následující položky:

- Registrace pro poskytovatele prostředků Azure NetApp Files a NetApp
- Účet NetApp
- Fond kapacit
- Svazek NFS pro Azure NetApp Files

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="before-you-begin"></a>Než začnete 

> [!IMPORTANT] 
> Je potřeba udělit přístup ke službě Azure NetApp Files.  Chcete-li požádat o přístup ke službě, přečtěte si [stránku odeslání Azure NetApp Files pořadníku](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u).  Než budete pokračovat, musíte počkat na oficiální e-mail s potvrzením od Azure NetApp Files týmu. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Registrace pro poskytovatele prostředků Azure NetApp Files a NetApp

> [!NOTE]
> Dokončení procesu registrace může trvat delší dobu.
>

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

Pro kroky registrace pomocí portálu otevřete relaci Cloud Shell, jak je uvedeno výše, a postupujte podle těchto kroků Azure CLI:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Tento článek s postupem vyžaduje Azure PowerShell modul AZ verze 2.6.0 nebo novější. Aktuální verzi zjistíte spuštěním `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud budete chtít, můžete místo toho použít konzolu Cloud Shell v relaci prostředí PowerShell.

1. V příkazovém řádku PowerShellu (nebo v relaci Cloud Shell PowerShellu) zadejte předplatné, které je na seznamu povolených pro Azure NetApp Files:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Zaregistrujte poskytovatele prostředků Azure:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

---

## <a name="create-a-netapp-account"></a>Vytvoření účtu NetApp

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Do vyhledávacího pole Azure Portal zadejte **Azure NetApp Files** a v seznamu, který se zobrazí, vyberte **Azure NetApp Files** .

      ![Vyberte Azure NetApp Files](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Kliknutím na **+ Přidat** vytvořte nový účet NetApp.

     ![Vytvořit nový účet NetApp](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. V okně Nový účet NetApp zadejte následující informace: 
   1. Jako název účtu zadejte **myaccount1** . 
   2. Vyberte své předplatné.
   3. Vyberte **vytvořit novou** a vytvořte novou skupinu prostředků. Jako název skupiny prostředků zadejte **myRG1** . Klikněte na **OK**. 
   4. Vyberte umístění svého účtu.  

      ![Okno nového účtu NetApp](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Okno skupiny prostředků](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Kliknutím na **vytvořit** vytvořte nový účet NetApp.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Definujte některé proměnné, na které můžeme v rámci zbývajících příkladů odkazovat:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ``` 

    > [!NOTE]
    > Seznam podporovaných oblastí najdete [v tématu dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=netapp&regions=all) .
    > Pokud chcete získat název oblasti, který podporuje naše nástroje příkazového řádku, použijte prosím `Get-AzLocation | select Location`.
    >

1. Pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) vytvořte novou skupinu prostředků:

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. Vytvořte Azure NetApp Files účet pomocí příkazu [New-AzNetAppFilesAccount](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount) :
   
    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definujte některé proměnné, na které můžeme v rámci zbývajících příkladů odkazovat:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ``` 

    > [!NOTE]
    > Seznam podporovaných oblastí najdete [v tématu dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=netapp&regions=all) .
    > Pokud chcete získat název oblasti, který podporuje naše nástroje příkazového řádku, použijte prosím `az account list-locations -query "[].{Region:name}" --out table`.
    >

2. Pomocí příkazu [AZ Group Create](/cli/azure/group#az-group-create) vytvořte novou skupinu prostředků:

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. Vytvořte účet Azure NetApp Files pomocí příkazu [AZ netappfiles Account Create](/cli/azure/netappfiles/account#az-netappfiles-account-create) :
   
    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```
---

## <a name="set-up-a-capacity-pool"></a>Nastavení fondu kapacity

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

1. V okně Správa Azure NetApp Files vyberte účet NetApp (**myaccount1**).

    ![Vybrat účet NetApp](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. V okně Správa Azure NetApp Files účtu NetApp klikněte na **fondy kapacit**.

    ![Klikněte na fondy kapacit](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Klikněte na **+ Přidat fondy**. 

    ![Klikněte na Přidat fondy.](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Zadejte informace pro fond kapacit: 
    1. Jako název fondu zadejte **mypool1** .
    2. Jako úroveň služby vyberte **Premium** . 
    3. Jako velikost fondu zadejte **4 (TIB)** . 

5. Klikněte na **OK**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Definování některých nových proměnných pro budoucí referenci

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. Vytvoření nového fondu kapacity pomocí [New-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool)

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definování některých nových proměnných pro budoucí referenci

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. Vytvořte nový fond kapacit pomocí funkce [AZ netappfiles Pool Create](/cli/azure/netappfiles/pool#az-netappfiles-pool-create) . 

    ```azurecli-interactive
    az netappfiles pool create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --size $POOL_SIZE_TiB \
        --service-level $SERVICE_LEVEL
    ```

---

## <a name="create-nfs-volume-for-azure-netapp-files"></a>Vytvořit svazek systému souborů NFS pro Azure NetApp Files

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

1. V okně Správa Azure NetApp Files účtu NetApp klikněte na **svazky**.

    ![Klikněte na svazky.](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Klikněte na **+ Přidat svazek**.

    ![Klikněte na Přidat svazky.](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. V okně vytvořit svazek zadejte informace o svazku: 
   1. Jako název svazku zadejte **myvol1** . 
   2. Vyberte fond kapacit (**mypool1**).
   3. Pro kvótu použijte výchozí hodnotu. 
   4. V části virtuální síť klikněte na **vytvořit novou** a vytvořte novou virtuální síť Azure (VNET).  Pak vyplňte následující informace:
       * Jako název virtuální sítě zadejte **myvnet1** .
       * Zadejte adresní prostor pro vaše nastavení, například 10.7.0.0/16.
       * Jako název podsítě zadejte **myANFsubnet** .
       * Zadejte rozsah adres podsítě, například 10.7.0.0/24. Vyhrazenou podsíť nelze sdílet s jinými prostředky.
       * V případě delegování podsítě vyberte **Microsoft. NetApp/** Volumes (svazky).
       * Kliknutím na **OK** vytvořte virtuální síť.
   5. V části podsíť vyberte nově vytvořenou virtuální síť (**myvnet1**) jako podsíť delegáta.

      ![Vytvořit okno svazku](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Okno vytvořit virtuální síť](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Klikněte na **protokol**a pak proveďte následující akce: 
    * Jako typ protokolu pro svazek vyberte **systém souborů NFS** .  
    * Jako cestu k souboru zadejte **myfilepath1** , který se použije k vytvoření cesty pro export pro daný svazek.  
    * Vyberte verzi systému souborů NFS (**NFSv3** nebo **nfsv 4.1**) pro svazek.  
> [!IMPORTANT] 
> Přístup k funkci NFSv 4.1 vyžaduje přidávání do seznamu povolených.  Pokud chcete požádat o přidávání do seznamu povolených žádostí, odešlete žádost o <anffeedback@microsoft.com>. 

    ![Specify NFS protocol for quickstart](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Klikněte na **Zkontrolovat a vytvořit**.

    ![Zkontrolovat a vytvořit okno](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

6. Zkontrolujte informace o svazku a pak klikněte na **vytvořit**.  
    Vytvořený svazek se zobrazí v okně svazky.

    ![Svazek vytvořen](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Pomocí příkazu [New-AzDelegation](/powershell/module/az.network/new-azdelegation) vytvořte delegování podsítě na Microsoft. NetApp/Volumes.

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. Vytvořte konfiguraci podsítě pomocí příkazu [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) .

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. Vytvořte virtuální síť pomocí příkazu [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) .
    
    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. Pomocí příkazu [New-AzNetAppFilesVolume](/powershell/module/az.netappfiles/new-aznetappfilesvolume) vytvořte svazek.
   
    ```powershell-interactive
    $volumeSizeBytes = 1099511627776 # 100GiB
    $subnetId = $vnet.Subnets[0].Id

    New-AzNetAppFilesVolume -ResourceGroupName $resourceGroup `
        -Location $location `
        -AccountName $anfAccountName `
        -PoolName $poolName `
        -Name "myvol1" `
        -UsageThreshold $volumeSizeBytes `
        -SubnetId $subnetId `
        -CreationToken "myfilepath1" `
        -ServiceLevel $serviceLevel `
        -ProtocolType NFSv3
    ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Definování proměnných pro pozdější použití.
    
    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. Pomocí příkazu [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create) vytvořte virtuální síť bez podsítě.
    
    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. Pomocí příkazu [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) vytvořte delegovanou podsíť.

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. Pomocí příkazu [AZ netappfiles Volume Create](/cli/azure/netappfiles/volume#az-netappfiles-volume-create) vytvořte svazek.
   
    ```azurecli-interactive
    VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
    SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
    VOLUME_SIZE_GiB=100 # 100 GiB
    UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within all ANF Accounts

    az netappfiles volume create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --name "myvol1" \
        --service-level $SERVICE_LEVEL \
        --vnet $VNET_ID \
        --subnet $SUBNET_ID \
        --usage-threshold $VOLUME_SIZE_GiB \
        --creation-token $UNIQUE_FILE_PATH \
        --protocol-types "NFSv3"
    ```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

Až budete hotovi, a pokud chcete, můžete odstranit skupinu prostředků. Akce odstranění skupiny prostředků je nevratná.  

> [!IMPORTANT]
> Všechny prostředky v rámci skupin prostředků se trvale odstraní a nedá se vrátit zpátky. 

1. Do vyhledávacího pole Azure Portal zadejte **Azure NetApp Files** a v seznamu, který se zobrazí, vyberte **Azure NetApp Files** .

2. V seznamu odběrů klikněte na skupinu prostředků (myRG1), kterou chcete odstranit. 

    ![Navigace do skupin prostředků](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Na stránce skupina prostředků klikněte na **Odstranit skupinu prostředků**.

    ![Odstranění skupiny prostředků](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png) 

    Otevře se okno s upozorněním týkajícím se prostředků, které budou odstraněny s vybranou skupinou prostředků.

4. Zadejte název skupiny prostředků (myRG1), abyste potvrdili, že chcete trvale odstranit skupinu prostředků a všechny prostředky v ní, a pak klikněte na **Odstranit**.

    ![Odstranění skupiny prostředků](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png ) 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Až budete hotovi, a pokud chcete, můžete odstranit skupinu prostředků. Akce odstranění skupiny prostředků je nevratná.  

> [!IMPORTANT]
> Všechny prostředky v rámci skupin prostředků se trvale odstraní a nedá se vrátit zpátky.

1. Odstraňte skupinu prostředků pomocí příkazu [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .
   
    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Až budete hotovi, a pokud chcete, můžete odstranit skupinu prostředků. Akce odstranění skupiny prostředků je nevratná.  

> [!IMPORTANT]
> Všechny prostředky v rámci skupin prostředků se trvale odstraní a nedá se vrátit zpátky.

1. Odstraňte skupinu prostředků pomocí příkazu [AZ Group Delete](/cli/azure/group#az-group-delete) .
   
    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```
---

## <a name="next-steps"></a>Další kroky  

> [!div class="nextstepaction"]
> [Správa svazků pomocí Azure NetApp Files](azure-netapp-files-manage-volumes.md)  
