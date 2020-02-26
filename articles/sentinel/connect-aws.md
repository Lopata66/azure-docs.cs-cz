---
title: Připojit AWS CloudTrail ke službě Azure Sentinel | Microsoft Docs
description: Naučte se připojit data AWS CloudTrail k Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 5cbef1f31ea7088d4fab4888f5630af1b765a910
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588650"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Připojení Azure Sentinel k AWS CloudTrail

Pomocí konektoru AWS můžete streamovat všechny události AWS CloudTrail do Azure Sentinel. Tento proces připojení deleguje delegáty přístupu pro Azure Sentinel do protokolů prostředků AWS a vytvoří vztah důvěryhodnosti mezi AWS CloudTrail a Azure Sentinel. To se provádí na AWS vytvořením role, která pro přístup k protokolům AWS uděluje oprávnění službě Azure Sentinel.

## <a name="prerequisites"></a>Předpoklady

V pracovním prostoru Sentinel Azure musíte mít oprávnění k zápisu.

> [!NOTE]
> Sentinel Azure shromažďuje události CloudTrail ze všech oblastí. Doporučujeme nevytvářet streamování událostí z jedné oblasti do druhé.

## <a name="connect-aws"></a>Připojení AWS 


1. V Azure Sentinel vyberte **datové konektory** a pak vyberte **Amazon Web Services** řádek v tabulce a v podokně AWS vpravo klikněte na **otevřít stránku konektoru**.

1. Postupujte podle pokynů v části **Konfigurace** pomocí následujících kroků.
 
1.  V konzole Amazon Web Services v části **zabezpečení, identita & kompatibilita**vyberte **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Zvolte **role** a vyberte **vytvořit roli**.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Vyberte **jiný účet AWS.** V poli **ID účtu** zadejte **ID účtu Microsoft** (**123412341234**), které najdete na stránce konektoru AWS na portálu Azure Sentinel.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Ujistěte se, že je vybraná možnost **vyžadovat externí ID** a pak zadejte externí ID (ID pracovního prostoru), které najdete na stránce KONEKTORu AWS na portálu Azure Sentinel.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  V části **připojení zásady oprávnění** vyberte **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Zadejte značku (volitelné).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Pak zadejte **název role** a klikněte na tlačítko **vytvořit roli** .

    ![AWS7](./media/connect-aws/aws-7.png)

1.  V seznamu role vyberte roli, kterou jste vytvořili.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Zkopírujte **ARN role**. Na portálu služby Azure Sentinel na obrazovce konektoru Amazon Web Services ji vložte do **role pro přidání** pole a klikněte na **Přidat**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Pokud chcete použít příslušné schéma v Log Analytics pro události AWS, vyhledejte **AWSCloudTrail**.



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit AWS CloudTrail ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.

