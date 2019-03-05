---
title: Správa prostředků a klíč – QnA Maker
titleSuffix: Azure Cognitive Services
description: Vaše služba QnA Maker se zabývá dvěma druhy klíče, klíče předplatného a klíče koncového bodu.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c1d8104f94d3f215108000b904848f3b968492ee
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341903"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>Správa klíčů v nástroje QnA Maker

Vaše služba QnA Maker se zabývá dvěma druhy klíče, **klíče předplatného** a **klíče koncového bodu**.

![Správa klíčů](../media/qnamaker-how-to-key-management/key-management.png)

1. **Klíče předplatného**: Tyto klíče se používají pro přístup [rozhraní API nástroje QnA Maker správy služeb](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff). Tato rozhraní API umožňují provádět úpravy znalostní báze.  

2. **Koncový bod klíče**: Tyto klíče se používají pro přístup ke koncovému bodu znalostní báze získat odpověď pro otázku uživatele. Obvykle použijete tento koncový bod v chatovací robot nebo kódem klientské aplikace, která využívá službu QnA Maker.
 
## <a name="subscription-keys"></a>Klíčů předplatného
Můžete zobrazit a obnovit klíče předplatného z portálu Azure, ve které jste vytvořili prostředek QnA Maker. 
1. Přejdete k prostředku QnA Maker na webu Azure Portal.

    ![Seznam prostředků nástroje QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Přejděte na **klíče**.

    ![Klíč předplatného](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Klíče koncového bodu

Klíče koncového bodu je možné spravovat z [portál QnA Maker](https://qnamaker.ai).

1. Přihlaste se k [portál QnA Maker](https://qnamaker.ai), přejděte na svůj profil a potom klikněte na tlačítko **nastavení služby**.

    ![Klíč koncového bodu](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Zobrazení nebo resetovat vaše klíče.

    ![klíče správce koncového bodu](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Pokud se domníváte, že jsou ohrožené, aktualizujte svoje klíče. To může vyžadovat odpovídající změny vaše klientská aplikace nebo kódu robota.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze v jiném jazyce](./language-knowledge-base.md)
