---
title: zahrnout soubor
description: zahrnout soubor
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 8693c48905155ed757bb727e42f4180f36c015f1
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513994"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Přidružení účtu služby Azure Storage do služby IoT Hub

Protože aplikace simulovaného zařízení nahraje soubor do objektu blob, musíte mít [služby Azure Storage](../articles/storage/common/storage-quickstart-create-account.md) účet spojený s centrem IoT. Když přidružíte účet Azure Storage pomocí služby IoT hub, IoT hub vygeneruje identifikátor URI SAS. Zařízení můžete pomocí tohoto identifikátoru URI SAS bezpečně nahrát soubor do kontejneru objektů blob. Služba IoT Hub a sady SDK pro zařízení koordinovat proces, který vygeneruje identifikátor URI SAS a zpřístupňuje je na zařízení používat k nahrání souboru.

Postupujte podle pokynů v [nahrání souborů konfigurace pomocí webu Azure portal](../articles/iot-hub/iot-hub-configure-file-upload.md). Ujistěte se, že kontejner objektů blob souvisí s centrem IoT a že jsou povolené soubor oznámení.

![Povolit soubor oznámení na portálu](./media/iot-hub-associate-storage/enable-file-notifications.png)