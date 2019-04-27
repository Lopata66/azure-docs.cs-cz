---
title: Ukázkový skript Azure PowerShellu – Migrace objektů blob mezi účty úložiště pomocí AzCopy ve Windows | Microsoft Docs
description: Pomocí AzCopy se zkopíruje obsah objektů blob v jednom účtu úložiště Azure do jiného.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/01/2018
ms.author: rogarana
ms.openlocfilehash: 88eb97a206bc75bc9635da90eb9d48818ceef0f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61364486"
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>Migrace objektů blob mezi účty úložiště pomocí AzCopy ve Windows

Tato ukázka zkopíruje všechny objekty blob z uživatelem zadaného zdrojového účtu úložiště do uživatelem zadaného cílového účtu úložiště. 

Provede to s použitím příkazu `Get-AzStorageContainer`, který vypíše všechny kontejnery v účtu úložiště. Ukázka pak vyšle příkazy AzCopy a zkopíruje všechny kontejnery ze zdrojového účtu úložiště do cílového účtu úložiště. Pokud dojde k selhání, ukázka pokus opakuje tolikrát, kolik je hodnota v proměnné $retryTimes (výchozí hodnota je 3, ale můžete ji upravit pomocí parametru `-RetryTimes`). Pokud k selhání dojde při každém opakování, uživatel může skript spustit znovu a zadat pro ukázku poslední úspěšně zkopírovaný kontejner pomocí parametru `-LastSuccessContainerName`. Ukázka pak bude pokračovat v kopírování kontejnerů od tohoto bodu.

Tato ukázka vyžaduje verzi modulu Azure Powershellu pro úložiště **0,7** nebo novější. Nainstalovanou verzi můžete zkontrolovat pomocí příkazu `Get-Module -ListAvailable Az.storage`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Tato ukázka vyžaduje také nejnovější verzi [AzCopy ve Windows](https://aka.ms/downloadazcopy). Výchozí adresář instalace je `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\`.

Tato ukázka přijímá název a klíč zdrojového účtu úložiště, název a klíč cílového účtu úložiště a úplnou cestu k nástroji AzCopy.exe (pokud není nainstalovaný ve výchozím adresáři).

Následují příklady vstupu pro tuto ukázku:

Pokud je nástroj AzCopy nainstalovaný ve výchozím adresáři:
```powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
```

Pokud nástroj AzCopy není nainstalovaný ve výchozím adresáři:

```Powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
AzCopyPath: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe
```

Pokud dojde k selhání a ukázku je potřeba spustit znovu od konkrétního kontejneru: 

`.\copyScript.ps1 -LastSuccessContainerName myContainerName`

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/storage/migrate-blobs-between-accounts/migrate-blobs-between-accounts.ps1 "Migrate blobs between storage accounts.")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript ke kopírování dat z jednoho účtu úložiště do jiného používá následující příkazy. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | Vrátí kontejnery přidružené k tomuto účtu úložiště. |
| [New-AzStorageContext](/powershell/module/az.storage/New-AzStorageContext) | Vytvoří kontext služby Azure Storage. |

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro úložiště najdete v tématu [Ukázky PowerShellu pro úložiště objektů blob v Azure](../blobs/storage-samples-blobs-powershell.md).
