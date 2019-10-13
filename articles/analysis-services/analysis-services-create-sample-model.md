---
title: Kurz – přidání ukázkového modelu do serveru Azure Analysis Services | Microsoft Docs
description: V této lekci kurzu zjistíte, jak přidat ukázkový model do služby Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9a9721b05fbd478d108f06c36017ee444f721d28
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298689"
---
# <a name="tutorial-add-a-sample-model-from-the-portal"></a>Kurz: Přidání ukázkového modelu z portálu

V tomto kurzu přidáte na server ukázkovou tabulkovou modelovou databázi Adventure Works. Tento ukázkový model je dokončená verze ukázkového datového modelu Adventure Works Internet Sales (1200). Ukázkový model je užitečný k testování správy modelu, připojení k nástrojům a klientským aplikacím a dotazování dat modelu. V tomto kurzu se [Azure Portal](https://portal.azure.com) a [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) používá k: 

> [!div class="checklist"]
> * Přidání dokončeného ukázkového tabulkového datového modelu na server 
> * Propojení modelu s SQL Server Management Studiem

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="before-you-begin"></a>Než začnete

Pro absolvování tohoto kurzu potřebujete:

- Server Azure Analysis Services. Další informace najdete v článku [Vytvoření serveru – portál](analysis-services-create-server.md).
- Oprávnění ke správě serveru
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu](https://portal.azure.com/).

## <a name="add-a-sample-model"></a>Přidání ukázkového modelu

1. Na stránce **Přehled** serveru klikněte na **Nový model**.

    ![Vytvoření ukázkového modelu](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. V oblasti **Nový model** > **Zvolit zdroj dat** ověřte, že je vybraná možnost **Ukázková data**, a pak klikněte na **Přidat**.

    ![Výběr ukázkových dat](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. Na stránce **Přehled** ověřte, že je přidaný ukázkový model `adventureworks`.

    ![Výběr ukázkových dat](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Ukázkový model využívá paměťové prostředky mezipaměti. Pokud ukázkový model nepoužíváte k testování, měli byste ho ze serveru odebrat.

Tento postup popisuje odstranění modelu ze serveru pomocí SQL Server Management Studia.

1. V **Průzkumníku objektů** SQL Server Management Studia klikněte na **Připojit** > **Analysis Services**.

2. Do pole **Připojit k serveru** vložte název serveru, v poli **Ověření** zvolte **Active Directory – univerzální s podporou vícefaktorového ověřování**, zadejte své uživatelské jméno a klikněte na **Připojit**.

    ![Přihlásit se](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. V **Průzkumníku objektů** klikněte pravým tlačítkem na ukázkovou databázi `adventureworks` a pak klikněte na **Odstranit**.

    ![Odstranění ukázkové databáze](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>Další kroky 

V tomto kurzu jste se naučili, jak na server přidat základní ukázkový model. Teď, když máte modelovou databázi, se k ní můžete připojit z SQL Server Management Studia a přidat uživatelské role. Pokud chcete vědět víc, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Kurz: Konfigurace správce serveru a uživatelských rolí](analysis-services-database-users.md)


