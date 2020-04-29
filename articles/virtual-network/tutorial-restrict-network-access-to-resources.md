---
title: Omezení přístupu k prostředkům PaaS – kurz – Azure Portal
description: V tomto kurzu zjistíte, jak pomocí webu Azure Portal omezit síťový přístup k prostředkům Azure, jako jsou služby Azure Storage a Azure SQL Database, s využitím koncových bodů služeb.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 08/23/2018
ms.author: kumud
ms.openlocfilehash: 85fc5687b82947ed16bde0c30ca2b947514ba958
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "74186372"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Kurz: Omezení síťového přístupu k prostředkům PaaS s využitím koncových bodů služby pro virtuální síť pomocí webu Azure Portal

Koncové body služby pro virtuální síť umožňují omezení síťového přístupu k prostředkům některých služeb Azure na podsíť virtuální sítě. Můžete také odebrat internetový přístup k prostředkům. Koncové body služeb poskytují přímé připojení z vaší virtuální sítě k podporovaným službám Azure a umožňují pro přístup ke službám Azure použít privátní adresní prostor virtuální sítě. Provoz směřující do prostředků Azure prostřednictvím koncových bodů služby zůstává vždy v páteřní síti Microsoft Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě s jednou podsítí
> * Přidání podsítě a povolení koncového bodu služby
> * Vytvoření prostředku Azure a povolení síťového přístupu k tomuto prostředku pouze z podsítě
> * Nasazení virtuálního počítače do každé podsítě
> * Ověření přístupu k prostředku z podsítě
> * Ověření odepření přístupu k prostředku z podsítě a internetu

Pokud chcete, můžete tento kurz absolvovat s použitím [Azure CLI](tutorial-restrict-network-access-to-resources-cli.md) nebo [Azure PowerShellu](tutorial-restrict-network-access-to-resources-powershell.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Sítě** a pak vyberte **Virtuální síť**.
3. Zadejte nebo vyberte následující informace a pak vyberte **Vytvořit**:

   |Nastavení|Hodnota|
   |----|----|
   |Název| myVirtualNetwork |
   |Adresní prostor| 10.0.0.0/16|
   |Předplatné| Vyberte své předplatné.|
   |Skupina prostředků | Vyberte **Vytvořit novou** a zadejte *myResourceGroup*.|
   |Umístění| Vyberte **východní USA** |
   |Název podsítě| Public|
   |Rozsah adres podsítě| 10.0.0.0/24|
   |Ochrana před útoky DDoS| Základní|
   |Koncové body služby| Zakázáno|
   |Brána firewall| Zakázáno|

   ![Zadání základních informací o virtuální síti](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

## <a name="enable-a-service-endpoint"></a>Povolení koncového bodu služby

Koncové body služby se povolují pro každou službu a podsíť. Vytvořte podsíť a povolte pro ni koncový bod služby.

1. Do pole **Hledat prostředky, služby a dokumenty** v horní části portálu zadejte *myVirtualNetwork*. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork**, vyberte ji.
2. Přidejte do virtuální sítě podsíť. V části **Nastavení**vyberte **podsítě**a pak vyberte **+ podsíť**, jak je znázorněno na následujícím obrázku:

    ![Přidání podsítě](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. V části **Přidat podsíť** vyberte nebo zadejte následující informace a pak vyberte **OK**:

    |Nastavení|Hodnota|
    |----|----|
    |Název| Soukromé |
    |Rozsah adres| 10.0.1.0/24|
    |Koncové body služby| V části **Služby** vyberte **Microsoft.Storage**.|

> [!CAUTION]
> Než povolíte koncový bod služby pro existující podsíť s prostředky, přečtěte si pokyny pro [změnu nastavení podsítě](virtual-network-manage-subnet.md#change-subnet-settings).

## <a name="restrict-network-access-for-a-subnet"></a>Omezení síťového přístupu pro podsíť

Ve výchozím nastavení mohou všechny virtuální počítače v podsíti komunikovat se všemi prostředky. Komunikaci do těchto prostředků nebo z nich můžete omezit vytvořením skupiny zabezpečení sítě a jejím přiřazením k podsíti.

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Sítě** a pak **Skupina zabezpečení sítě**.
3. V části **Vytvořit skupinu zabezpečení sítě** zadejte nebo vyberte následující informace a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |----|----|
    |Název| myNsgPrivate |
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků | Vyberte **Použít existující** a pak vyberte *myResourceGroup*.|
    |Umístění| Vyberte **východní USA** |

4. Po vytvoření skupiny zabezpečení sítě do pole **Hledat prostředky, služby a dokumenty** v horní části portálu zadejte *myNsgPrivate*. Jakmile se ve výsledcích hledání zobrazí skupina zabezpečení sítě **myNsgPrivate**, vyberte ji.
5. V části **NASTAVENÍ** vyberte **Odchozí pravidla zabezpečení**.
6. Vyberte **+ Přidat**.
7. Vytvořte pravidlo pro povolení odchozí komunikace do služby Azure Storage. Zadejte nebo vyberte následující informace a pak vyberte **Přidat**:

    |Nastavení|Hodnota|
    |----|----|
    |Zdroj| Vyberte **VirtualNetwork**. |
    |Rozsahy zdrojových portů| * |
    |Cíl | Vyberte **Značka služby**.|
    |Značka cílové služby | Vyberte **Storage**.|
    |Rozsahy cílových portů| * |
    |Protocol (Protokol)|Všechny|
    |Akce|Povolit|
    |Priorita|100|
    |Název|Allow-Storage-All|

8. Vytvořte další odchozí pravidlo zabezpečení, které zakáže komunikaci s internetem. Toto pravidlo přepíše výchozí pravidlo ve všech skupinách zabezpečení sítě, které odchozí komunikaci s internetem povoluje. Zopakujte kroky 5 až 7 s použitím následujících hodnot:

    |Nastavení|Hodnota|
    |----|----|
    |Zdroj| Vyberte **VirtualNetwork**. |
    |Rozsahy zdrojových portů| * |
    |Cíl | Vyberte **Značka služby**.|
    |Značka cílové služby| Vyberte **Internet**.|
    |Rozsahy cílových portů| * |
    |Protocol (Protokol)|Všechny|
    |Akce|Odepřít|
    |Priorita|110|
    |Název|Deny-Internet-All|

9. V části **NASTAVENÍ** vyberte **Příchozí pravidla zabezpečení**.
10. Vyberte **+ Přidat**.
11. Vytvořte příchozí pravidlo zabezpečení, které povolí provoz protokolu RDP (Remote Desktop Protocol) do podsítě odkudkoli. Toto pravidlo přepíše výchozí pravidlo zabezpečení, které zakazuje veškerý příchozí provoz z internetu. Připojení ke vzdálené ploše jsou pro podsíť povolená z důvodu testování připojení v pozdějším kroku. V části **NASTAVENÍ** vyberte **Příchozí pravidla zabezpečení**, pak **+ Přidat**, zadejte následující hodnoty a pak vyberte **Přidat**:

    |Nastavení|Hodnota|
    |----|----|
    |Zdroj| Všechny |
    |Rozsahy zdrojových portů| * |
    |Cíl | Vyberte **VirtualNetwork**.|
    |Rozsahy cílových portů| 3389 |
    |Protocol (Protokol)|Všechny|
    |Akce|Povolit|
    |Priorita|120|
    |Název|Allow-RDP-All|

12. V části **NASTAVENÍ** vyberte **Podsítě**.
13. Vyberte **+ Přidružit**.
14. V části **Přidružit podsíť** vyberte **Virtuální síť** a pak v části **Zvolte virtuální síť** vyberte **myVirtualNetwork**.
15. V části **Zvolte podsíť** vyberte **Private** a pak vyberte **OK**.

## <a name="restrict-network-access-to-a-resource"></a>Omezení síťového přístupu k prostředku

Kroky potřebné k omezení síťového přístupu k prostředkům vytvořeným prostřednictvím služeb Azure povolených v koncových bodech se u jednotlivých služeb liší. Konkrétní kroky pro jednotlivé služby najdete v dokumentaci příslušné služby. Zbývající část tohoto kurzu jako příklad obsahuje kroky k omezení síťového přístupu k účtu služby Azure Storage.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Úložiště** a pak vyberte **Účet úložiště – objekt blob, soubor, tabulka, fronta**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |----|----|
    |Název| Zadejte název, který je jedinečný ve všech umístěních Azure, je dlouhý 3 až 24 znaků a obsahuje pouze číslice a malá písmena.|
    |Account kind (Druh účtu)|StorageV2 (obecné účely v2)|
    |Umístění| Vyberte **východní USA** |
    |Replikace| Místně redundantní úložiště (LRS)|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků | Vyberte **Použít existující** a pak vyberte *myResourceGroup*.|

### <a name="create-a-file-share-in-the-storage-account"></a>Vytvoření sdílené složky v účtu úložiště

1. Po vytvoření účtu úložiště do pole **Hledat prostředky, služby a dokumenty** v horní části portálu zadejte název tohoto účtu úložiště. Jakmile se ve výsledcích hledání zobrazí váš účet úložiště, vyberte ho.
2. Vyberte **Soubory**, jak je znázorněno na následujícím obrázku:

   ![Účet úložiště](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. Vyberte **+ Sdílená složka**.
4. V části **Název** zadejte *my-file-share* a pak vyberte **OK**.
5. Zavřete okno **Souborová služba**.

### <a name="restrict-network-access-to-a-subnet"></a>Omezení síťového přístupu k podsíti

Účty úložiště ve výchozím nastavení přijímají síťová připojení z klientů v jakékoli síti včetně internetu. Zakažte síťový přístup z internetu a všech ostatních podsítí ve všech virtuálních sítích kromě podsítě *Private* ve virtuální síti *myVirtualNetwork*.

1. V části **NASTAVENÍ** účtu úložiště vyberte **Brány firewall a virtuální sítě**.
2. Vyberte **Vybrané sítě**.
3. Vyberte **+ Přidat existující virtuální síť**.
4. V části **Přidat sítě** vyberte následující hodnoty a pak vyberte **Přidat**:

    |Nastavení|Hodnota|
    |----|----|
    |Předplatné| Vyberte své předplatné.|
    |Virtuální sítě|V části **Virtuální sítě** vyberte **myVirtualNetwork**.|
    |Podsítě| V části **Podsítě** vyberte **Private**.|

    ![Brány firewall a virtuální sítě](./media/tutorial-restrict-network-access-to-resources/storage-firewalls-and-virtual-networks.png)

5. Vyberte **Uložit**.
6. Zavřete okno **Brány firewall a virtuální sítě**.
7. V části **NASTAVENÍ** účtu úložiště vyberte **Přístupové klíče**, jak je znázorněno na následujícím obrázku:

      ![Brány firewall a virtuální sítě](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

8. Poznamenejte si hodnotu **Klíč**, protože ji budete muset ručně zadat v pozdějším kroku při mapování sdílené složky na písmeno jednotky na virtuálním počítači.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Pokud chcete otestovat síťový přístup k účtu úložiště, nasaďte do každé podsítě virtuální počítač.

### <a name="create-the-first-virtual-machine"></a>Vytvoření prvního virtuálního počítače

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**.
3. Zadejte nebo vyberte následující informace a pak vyberte **OK**:

   |Nastavení|Hodnota|
   |----|----|
   |Název| myVmPublic|
   |Uživatelské jméno|Zadejte libovolné uživatelské jméno.|
   |Heslo| Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   |Předplatné| Vyberte své předplatné.|
   |Skupina prostředků| Vyberte **Použít existující** a pak vyberte **myResourceGroup**.|
   |Umístění| Vyberte **USA – východ**.|

   ![Zadání základních informací o virtuálním počítači](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)
4. Vyberte velikost virtuálního počítače a pak vyberte **Vybrat**.
5. V části **Nastavení** vyberte **Síť** a pak **myVirtualNetwork**. Pak vyberte **Podsíť** a **Public**, jak je znázorněno na následujícím obrázku:

   ![Výběr virtuální sítě](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)

6. V části **Skupina zabezpečení sítě** vyberte **Upřesnit**. Portál pro vás automaticky vytvoří skupinu zabezpečení sítě, která povolí port 3389, který musí být otevřený, abyste se později mohli připojit k virtuálnímu počítači. Na stránce **Nastavení** vyberte **OK**.
7. Na stránce **Souhrn** výběrem možnosti **Vytvořit** spusťte nasazení virtuálního počítače. Nasazení virtuálního počítače trvá několik minut, ale zatímco se virtuální počítač vytváří, můžete pokračovat k dalšímu kroku.

### <a name="create-the-second-virtual-machine"></a>Vytvoření druhého virtuálního počítače

Zopakujte kroky 1 až 7, ale v kroku 3 pojmenujte virtuální počítač *myVmPrivate* a v kroku 5 vyberte podsíť **Private**.

Nasazení virtuálního počítače trvá několik minut. Nepokračujte k dalšímu kroku, dokud se nevytvoří a na portálu se neotevře jeho nastavení.

## <a name="confirm-access-to-storage-account"></a>Ověření přístupu k účtu úložiště

1. Po vytvoření virtuálního počítače *myVmPrivate* se v Azure otevře jeho nastavení. Připojte se k virtuálnímu počítači výběrem tlačítka **Připojit**, jak je znázorněno na následujícím obrázku:

   ![Připojení k virtuálnímu počítači](./media/tutorial-restrict-network-access-to-resources/connect-to-virtual-machine.png)

2. Po kliknutí na tlačítko **Připojit** se vytvoří soubor protokolu RDP (.rdp) a stáhne se na váš počítač.  
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. 
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.
6. Na virtuálním počítači *myVmPrivate* pomocí PowerShellu namapujte sdílenou složku Azure na jednotku Z. Před spuštěním následujících příkazů nahraďte `<storage-account-key>` (klíč účtu úložiště) a `<storage-account-name>` (název účtu úložiště) hodnotami, které jste zadali a načetli v části [Vytvoření účtu úložiště](#create-a-storage-account).

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   PowerShell vrátí podobný výstup jako v následujícím příkladu:

   ```powershell
   Name           Used (GB)     Free (GB) Provider      Root
   ----           ---------     --------- --------      ----
   Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
   ```

   Sdílená složka Azure se úspěšně namapovala na jednotku Z.

7. Na příkazovém řádku potvrďte, že virtuální počítač nemá možnost odchozího připojení k internetu:

   ```
   ping bing.com
   ```

   Neobdržíte žádné odpovědi, protože skupina zabezpečení sítě přidružená k podsíti *Private* nepovoluje odchozí přístup k internetu.

8. Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Ověření odepření přístupu k účtu úložiště

1. Do pole **Hledat prostředky, služby a dokumenty** v horní části portálu zadejte *myVmPublic*.
2. Jakmile se ve výsledcích hledání zobrazí virtuální počítač **myVmPublic**, vyberte ho.
3. Proveďte pro virtuální počítač *myVmPublic* kroky 1 až 6 v části [Ověření přístupu k účtu úložiště](#confirm-access-to-storage-account).

   Po krátkém čekání se zobrazí chyba `New-PSDrive : Access is denied`. Přístup byl odepřen, protože virtuální počítač *myVmPublic* je nasazený v podsíti *Public*. Podsíť *Public* nemá pro Azure Storage povolen žádný koncový bod služby. Účet úložiště povoluje síťový přístup pouze z podsítě *Private*, nikoliv z podsítě *Private*.

4. Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPublic*.

5. Na svém počítači přejděte na [Azure Portal](https://portal.azure.com).
6. Do pole **Hledat prostředky, služby a dokumenty** zadejte název účtu úložiště, který jste vytvořili. Jakmile se ve výsledcích hledání zobrazí váš účet úložiště, vyberte ho.
7. Vyberte **soubory**.
8. Zobrazí se chyba jako na následujícím obrázku:

   ![Chyba odepření přístupu](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)

   Přístup byl odepřen, protože se váš počítač nenachází v podsíti *Private* virtuální sítě *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup*. Jakmile se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. V části **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** zadejte *myResourceGroup* a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili koncový bod služby pro podsíť virtuální sítě. Dozvěděli jste se, že koncové body služeb můžete povolit pro prostředky nasazené z několika služeb Azure. Vytvořili jste účet služby Azure Storage a omezili jste síťový přístup k účtu úložiště pouze na prostředky v rámci podsítě virtuální sítě. Další informace o koncových bodech služeb najdete v tématech [Přehled koncových bodů služeb](virtual-network-service-endpoints-overview.md) a [Správa podsítí](virtual-network-manage-subnet.md).

Pokud ve svém účtu máte více virtuálních sítí, možná budete chtít propojit dvě virtuální sítě, aby mezi sebou mohly komunikovat prostředky v obou virtuálních sítích. Informace o postupu propojení virtuálních sítí najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Připojení virtuálních sítí](./tutorial-connect-virtual-networks-portal.md)
