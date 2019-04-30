---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3b596e5bad8202d88ea06c7eee114bec1063a35f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075681"
---
# <a name="enabling-azure-ultra-ssds"></a>Povolení Azure ultra disky SSD

Azure ultra SSD poskytovat vysokou propustnost, vysoké IOPS a stálá nízká latence diskové úložiště pro virtuální počítače Azure IaaS. Tato nová nabídka poskytuje horní řádek výkon ve stejné úrovně dostupnosti jako naše stávající nabídky disky. Další výhody ultra SSD patří schopnost dynamicky měnit výkonu disku spolu s vašimi úlohami, aniž byste museli restartovat své virtuální počítače. Ultra SSD jsou vhodné pro úlohy náročné na data, jako je SAP HANA, databáze na nejvyšší úrovni a transakce náročné úlohy.

V současné době ve verzi preview jsou ultra SSD, a vy musíte [zaregistrovat](https://aka.ms/UltraSSDPreviewSignUp) ve verzi preview, aby bylo možné přistupovat k nim.

Po schválení se spuštěním jednoho z následujících příkazů můžete určit, které zóna v oblasti východní USA 2, ultra disku pro nasazení:

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI: `az vm list-skus --resource-type disks --query “[?name==’UltraSSD_LRS’]”`

Odpověď bude podobná formuláři níže, kde X je pásmo má být použito pro nasazení v oblasti východní USA 2. X může být buď 1, 2 nebo 3.

|ResourceType  |Název  |Location  |Zóny  |Omezení  |Schopnost  |Hodnota  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

Pokud příkaz neodpověděl, znamená to registrace pro funkci je buď stále čekající na vyřízení nebo není schváleno ještě.

Teď, když znáte zóně, ve které chcete nasadit, postupujte podle kroků nasazení v tomto článku se získat první virtuální počítače nasazené pomocí ultra SSD.

## <a name="deploying-an-ultra-ssd"></a>Nasazení ultra SSD

Nejprve určete velikost virtuálního počítače k nasazení. V rámci této verze Preview jsou podporovány pouze řady DsV3 a EsV3 virtuálního počítače. Naleznete v druhé tabulce v tomto [blogu](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) další podrobné informace o těchto velikostí virtuálních počítačů.
Také odkaz na ukázku [vytvoření Virtuálního počítače s více ultra SSD](https://aka.ms/UltraSSDTemplate), který ukazuje, jak vytvořit virtuální počítač s více ultra SSD.

Dále jsou změny šablony Resource Manageru nových/upravených: **apiVersion** pro `Microsoft.Compute/virtualMachines` a `Microsoft.Compute/Disks` musí být nastavena jako `2018-06-01` (nebo novější).

Zadejte Disk skladová položka UltraSSD_LRS, kapacity disku, IOPS a propustnost v MB/s pro vytvoření ultra disku. Tady je příklad, který vytvoří disk s 1 024 GB (GiB = 2 ^ 30 bajtů), 80 000 vstupně-výstupních operací a 1 200 MB/s (MB/s = 10 ^ 6 bajtů za sekundu):

```json
"properties": {  
    "creationData": {  
    "createOption": "Empty"  
},  
"diskSizeGB": 1024,  
"diskIOPSReadWrite": 80000,  
"diskMBpsReadWrite": 1200,  
}
```

Přidat další možnosti ve vlastnostech virtuálního počítače k označení jeho ultra povolené (odkazovat [ukázka](https://aka.ms/UltraSSDTemplate) pro úplnou šablonu Resource Manageru):

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "properties": {
                    "hardwareProfile": {},
                    "additionalCapabilities" : {
                                    "ultraSSDEnabled" : "true"
                    },
                    "osProfile": {},
                    "storageProfile": {},
                    "networkProfile": {}
    }
}
```

Po zřízení virtuálního počítače můžete oddílu a formátování datových disků a nakonfigurovat je pro vaše úlohy.

## <a name="additional-ultra-ssd-scenarios"></a>Další scénáře ultra SSD

- Během vytváření virtuálních počítačů ultra SSD lze implicitně vytvořit také. Ale tyto disky se zobrazí výchozí hodnotu (500) IOPS a propustnost (8 MiB/s).
- Další ultra SSD lze připojit k kompatibilních virtuálních počítačů.
- Ultra SSD podporují nastavení vlastnosti výkonu disku (IOPS a propustnost) za běhu bez odpojení disku od virtuálního počítače. Jakmile se operace změny velikosti disku výkonu se vystavil na disku, může trvat až hodinu, tato změna se skutečně projeví.
- Stále se rozšiřující kapacity disku vyžaduje virtuální počítač, aby se zrušit jejich přidělení.

## <a name="next-steps"></a>Další postup

Pokud jste chtěli vyzkoušet nový typ disku a ještě nezaregistrovali pro verzi preview, [žádat o přístup prostřednictvím tohoto průzkumu](https://aka.ms/UltraSSDPreviewSignUp).
