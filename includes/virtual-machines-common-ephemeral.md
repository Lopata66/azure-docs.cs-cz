---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d848b92da5d4181832adff8499b3531d020c30c9
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "78155390"
---
Dočasné disky s operačním systémem se vytvářejí na místním úložišti virtuálních počítačů a neukládají se do vzdáleného Azure Storage. Dočasné disky s operačním systémem fungují pro bezstavové úlohy, kde jsou aplikace odolné proti selhání jednotlivých virtuálních počítačů, ale mají vyšší vliv na dobu nasazení virtuálních počítačů nebo obnovování imagí jednotlivých instancí virtuálních počítačů. S dočasným diskem s operačním systémem získáte nižší latenci čtení a zápisu na disk s operačním systémem a rychlejší přebitovou kopii virtuálního počítače. 
 
Klíčové funkce dočasných disků: 
- Ideální pro bezstavové aplikace.
- Je možné je používat s Marketplace i s vlastními imagemi.
- Možnost rychlého resetování a obnovení imagí virtuálních počítačů a škálování instancí sad na původní stav spouštění.  
- Nižší latence, podobně jako dočasný disk. 
- Dočasné disky s operačním systémem jsou bezplatné, neúčtují se žádné náklady na úložiště pro disk s operačním systémem.
- Jsou k dispozici ve všech oblastech Azure. 
- [Galerie sdílených imagí](/azure/virtual-machines/linux/shared-image-galleries)podporuje dočasný disk s operačním systémem. 
 

 
Klíčové rozdíly mezi trvalými a dočasnými disky s operačním systémem:

|                             | Trvalý disk s operačním systémem                          | Dočasný disk s operačním systémem                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Omezení velikosti pro disk s operačním systémem      | 2 TiB                                                                                        | Velikost mezipaměti pro velikost virtuálního počítače nebo 2TiB, podle toho, která hodnota je menší. **Velikost mezipaměti v GIB najdete v**tématu [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md)a [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series) .              |
| Podporované velikosti virtuálních počítačů          | Vše                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Podpora typů disků           | Spravovaný a nespravovaný disk s operačním systémem                                                                | Jenom spravovaný disk s operačním systémem                                                               |
| Podpora oblastí              | Všechny oblasti                                                                                  | Všechny oblasti                              |
| Trvalost dat            | Data disku s operačním systémem zapsaná na disk s operačním systémem se ukládají v Azure Storage                                  | Data zapsaná na disk s operačním systémem se ukládají do místního úložiště virtuálního počítače a neukládají se do Azure Storage. |
| Stav zastavení a zrušení přidělení      | Virtuální počítače a instance sady škálování se dají zastavit – zrušit přidělení a restartovat ze stavu Zastaveno (přidělení). | Virtuální počítače a instance sady škálování nelze zastavit – zrušit přidělení                                  |
| Specializovaná podpora disků s operačním systémem | Ano                                                                                          | Ne                                                                                 |
| Změna velikosti disku s operačním systémem              | Podporováno během vytváření virtuálních počítačů a po zastavení virtuálního počítače – zrušení přidělení                                | Podporováno pouze během vytváření virtuálních počítačů                                                  |
| Změna velikosti nového virtuálního počítače   | Data disku operačního systému se zachovají.                                                                    | Data na disku s operačním systémem se odstraní, operační systém se znovu zřídí.                                      |

## <a name="size-requirements"></a>Požadavky na velikost

Můžete nasadit image virtuálních počítačů a instancí až do velikosti mezipaměti virtuálních počítačů. Například standardní image Windows serveru z webu Marketplace mají přibližně 127 GiB, což znamená, že potřebujete velikost virtuálního počítače, která má mezipaměť větší než 127 GiB. V takovém případě má [Standard_DS2_v2](~/articles/virtual-machines/dv2-dsv2-series.md) velikost mezipaměti 86 GIB, což není dostatečně velké. Standard_DS3_v2 má velikost mezipaměti 172 GiB, která je dostatečně velká. V tomto případě je Standard_DS3_v2 nejmenší velikost v řadě DSv2, kterou můžete použít s tímto obrázkem. Image základního systému Linux na webu Marketplace a imagí Windows serveru, které jsou označeny `[smallsize]`, mají asi 30 GiB a můžou využívat většinu dostupných velikostí virtuálních počítačů.

Dočasné disky také vyžadují, aby velikost virtuálního počítače podporovala službu Premium Storage. Velikosti obvykle mají `s` v názvu, jako je například DSv2 a EsV3. Další informace najdete v tématu [velikosti virtuálních počítačů Azure](../articles/virtual-machines/linux/sizes.md) pro podrobnosti o tom, které velikosti podporují Premium Storage.

## <a name="powershell"></a>PowerShell

Pokud chcete pro nasazení virtuálního počítače PowerShell použít dočasný disk, použijte rutinu [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) v konfiguraci virtuálního počítače. Nastavte `-DiffDiskSetting` na `Local` a `-Caching` na `ReadOnly`.     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

V případě nasazení sady škálování použijte v konfiguraci rutinu [set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) . Nastavte `-DiffDiskSetting` na `Local` a `-Caching` na `ReadOnly`.


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>Rozhraní příkazového řádku

Pokud chcete pro nasazení virtuálního počítače CLI použít dočasný disk, nastavte parametr `--ephemeral-os-disk` v části [AZ VM Create](/cli/azure/vm#az-vm-create) to `true` a parametr `--os-disk-caching` na `ReadOnly`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

U sady škálování použijte stejný parametr `--ephemeral-os-disk true` pro [AZ-VMSS-Create](/cli/azure/vmss#az-vmss-create) a nastavte parametr `--os-disk-caching` na `ReadOnly`.

## <a name="portal"></a>Portál   

V Azure Portal můžete při nasazení virtuálního počítače použít dočasné disky otevřením části **Upřesnit** na kartě **disky** . Pro možnost **použít dočasný disk s operačním systémem** vyberte **Ano**.

![Snímek obrazovky znázorňující přepínač pro výběr použití dočasného disku s operačním systémem](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Pokud je možnost použít dočasný disk šedá, možná jste vybrali velikost virtuálního počítače, která nemá větší velikost mezipaměti než bitová kopie operačního systému nebo nepodporuje službu Premium Storage. Vraťte se na stránku **základy** a zkuste zvolit jinou velikost virtuálního počítače.

Pomocí portálu můžete také vytvořit škálované sady s dočasnými disky s operačním systémem. Jenom se ujistěte, že jste vybrali velikost virtuálního počítače, která má dostatečnou velikost mezipaměti, a pak v rámečku **použít dočasný disk s operačním systémem** vyberte **Ano**.

![Snímek obrazovky znázorňující přepínač pro výběr použití dočasného disku s operačním systémem pro sadu škálování](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Nasazování šablony sady škálování  
Proces vytvoření sady škálování, která používá dočasný disk s operačním systémem, je přidání vlastnosti `diffDiskSettings` do typu prostředku `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` v šabloně. Zásady ukládání do mezipaměti musí být také nastavené na `ReadOnly` pro dočasný disk s operačním systémem. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>Nasazení šablony virtuálního počítače 
Pomocí šablony můžete nasadit virtuální počítač s dočasným diskem s operačním systémem. Proces vytvoření virtuálního počítače, který používá dočasné disky s operačním systémem, je přidání vlastnosti `diffDiskSettings` do typu prostředku Microsoft. COMPUTE/virtualMachines v šabloně. Zásady ukládání do mezipaměti musí být také nastavené na `ReadOnly` pro dočasný disk s operačním systémem. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Obnovení virtuálního počítače z Image pomocí REST
Můžete obnovit instanci virtuálního počítače s dočasným diskem s operačním systémem pomocí REST API, jak je popsáno níže, a prostřednictvím webu Azure Portal, a to tak, že se zobrazí okno s přehledem virtuálního počítače. Pro sady škálování je už obnovení imagí dostupné prostřednictvím PowerShellu, CLI a portálu.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: jaká je velikost místních disků s operačním systémem?**

Odpověď: podporujeme platformu a vlastní image, až do velikosti mezipaměti virtuálních počítačů, kde všechny čtení a zápisy na disk s operačním systémem budou místní na stejném uzlu jako virtuální počítač. 

**Otázka: může být změněna velikost dočasného disku s operačním systémem?**

Odpověď: Ne, jakmile se zřídí dočasný disk s operačním systémem, nelze změnit velikost disku s operačním systémem. 

**Otázka: mohu připojit Managed Disks k dočasnému virtuálnímu počítači?**

Odpověď: Ano, můžete připojit spravovaný datový disk k virtuálnímu počítači, který používá dočasný disk s operačním systémem. 

**Otázka: budou pro dočasné disky s operačním systémem podporovány všechny velikosti virtuálních počítačů?**

Odpověď: Ne, podporují se všechny velikosti Premium Storage virtuálních počítačů (DS, ES, FS, GS a M) s výjimkou velikostí B-Series, N-Series a H-Series.  
 
**Otázka: je možné použít dočasný disk s operačním systémem pro existující virtuální počítače a sady škálování?**

Odpověď: Ne, dočasný disk s operačním systémem se dá použít jenom během vytváření virtuálního počítače a sady škálování. 

**Otázka: je možné kombinovat dočasné a běžné disky s operačním systémem v sadě škálování?**

Odpověď: Ne, nemůžete mít kombinaci dočasných a trvalých diskových instancí operačního systému v rámci stejné sady škálování. 

**Otázka: je možné vytvořit dočasný disk s operačním systémem pomocí PowerShellu nebo rozhraní příkazového řádku?**

Odpověď: Ano, můžete vytvořit virtuální počítače s dočasným diskem s operačním systémem pomocí REST, šablon, PowerShellu a rozhraní příkazového řádku.

**Otázka: Jaké funkce nejsou pro dočasný disk s operačním systémem podporované?**

Odpověď: dočasné disky nepodporují:
- Zachytávání imagí virtuálních počítačů
- Snímky disků 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Prohození disku s operačním systémem 
