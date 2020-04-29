---
title: Omezení Azure Data Box Gateway | Microsoft Docs
description: Popisuje omezení systému a Doporučené velikosti pro Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: 641d7410e414be2adae2a83840c90a680aedd2fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683360"
---
# <a name="azure-data-box-gateway-limits"></a>Omezení Azure Data Box Gateway

Tato omezení zvažte při nasazení a provozu Data Box Gateway řešení Microsoft Azure. 

## <a name="data-box-gateway-service-limits"></a>Omezení služby Data Box Gateway

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Omezení Data Box Gateway zařízení

Následující tabulka popisuje omezení pro Data Box Gateway zařízení.

| Popis | Hodnota |
|---|---|
|Ne. souborů na zařízení |100 000 000 <br> Pro každý 25 000 000 souborů, které se přidávají (s maximálním limitem na 100 000 000), byste měli přidat 2 TB místa na disku, 8 GB paměti RAM a 4 jádra procesoru. |
|Ne. sdílených složek na zařízení |24 |
|Ne. sdílených složek na kontejner úložiště Azure |1 |
|Maximální velikost souboru zapsaná do sdílené složky|Pro virtuální zařízení o velikosti 2 TB je maximální velikost souboru 500 GB. <br> Maximální velikost souboru se zvětšuje s velikostí datových disků v předchozím poměru, dokud nedosáhne maximálního počtu 5 TB. |

## <a name="azure-storage-limits"></a>Omezení úložiště Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Výhody a rizika nahrávání dat

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Omezení velikosti účtu a velikosti objektu Azure Storage

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Omezení velikosti objektů Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Další kroky

- [Příprava k nasazení služby Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
