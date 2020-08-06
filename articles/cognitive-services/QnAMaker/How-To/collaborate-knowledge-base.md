---
title: Spolupráce na znalostní bázi Knowledge Base – QnA Maker
description: QnA Maker umožňuje více lidem spolupracovat na znalostní bázi. Tato funkce je k dispozici s řízením přístupu na základě role Azure (Azure RBAC).
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 86c8d919618b37223e51a23635a1322a4e0d780c
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87837427"
---
# <a name="collaboration-with-authors-and-editors"></a>Spolupráce s autory a editory

Spolupráce je poskytována na úrovni prostředků QnA Maker, která umožňuje omezit přístup spolupracovníka na základě role spolupracovníka. Přečtěte si další informace o QnA Maker [konceptech](../Concepts/role-based-access-control.md)ověřování spolupracovníka.

## <a name="add-azure-role-based-access-control-azure-rbac-to-your-qna-maker-resource"></a>Přidejte řízení přístupu na základě role Azure (Azure RBAC) do prostředku QnA Maker.

QnA Maker umožňuje více lidem spolupracovat na všech základech znalostní báze ve stejném prostředku QnA Maker. Tato funkce je k dispozici u [Access Control na základě rolí](../../../active-directory/role-based-access-control-configure.md)Azure.

## <a name="access-at-the-qna-maker-resource-level"></a>Přístup na úrovni prostředků QnA Maker

V QnA Maker službě nemůžete sdílet konkrétní znalostní bázi. Pokud chcete podrobnější řízení přístupu, zvažte distribuci znalostní báze mezi různými QnA Maker prostředky a pak přidejte role do každého prostředku.

## <a name="add-role-to-resource"></a>Přidat roli do prostředku

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Přidat uživatelský účet do prostředku QnA Maker

V následujících krocích se používá role spolupracovníka, ale kterákoli z [rolí](../reference-role-based-access-control.md) se dá přidat pomocí těchto kroků.

1. Přihlaste se k webu [Azure](https://portal.azure.com/) Portal a Projděte si prostředek QnA maker.

    ![Seznam prostředků QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Přejít na kartu **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Vyberte **Přidat**.

    ![QnA Maker IAM – přidat](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Vyberte roli z následujícího seznamu:

    |Role|
    |--|
    |Vlastník|
    |Přispěvatel|
    |Čtecí modul Cognitive Services QnA Maker|
    |Editor QnA Maker Cognitive Services|
    |Cognitive Services uživatel|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker přidat roli IAM.":::

1. Zadejte e-mailovou adresu uživatele a stiskněte **Uložit**.

    ![QnA Maker IAM – Přidání e-mailu](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>Zobrazit QnA Maker základy znalostní báze

Když někdo nasdílel službu QnA Maker pomocí protokolů na [portál QnA maker](https://qnamaker.ai), uvidí všechny databáze znalostí v této službě na základě jejich role.

Když vyberou znalostní bázi, jejich aktuální role na tomto QnA Maker prostředku se zobrazí vedle názvu znalostní báze.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="Snímek znalostní báze v režimu úprav s názvem role v závorkách vedle názvu znalostní báze v levém horním rohu webové stránky.":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Testování znalostní báze](./test-knowledge-base.md)

Další informace o spolupráci:
* Řízení přístupu na základě role v [Azure](../../../active-directory/role-based-access-control-configure.md)
* QnA Maker [koncepce](../Concepts/role-based-access-control.md) řízení přístupu na základě rolí
