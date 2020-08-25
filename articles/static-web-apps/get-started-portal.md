---
title: 'Rychlý Start: Vytvoření první statické webové aplikace pomocí statického Web Apps Azure pomocí Azure Portal'
description: Naučte se vytvořit instanci statického Web Apps Azure pomocí Azure Portal.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: e0b78c5e053c5668fbebd8ebaac91a90aa2b364f
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752890"
---
# <a name="quickstart-building-your-first-static-web-app-in-the-azure-portal"></a>Rychlý Start: Vytvoření první statické webové aplikace v Azure Portal

Služba Azure static Web Apps publikuje web do produkčního prostředí tím, že vytváří aplikace z úložiště GitHub. V tomto rychlém startu nasadíte webovou aplikaci do statických webových aplikací Azure pomocí portálu.

Pokud nemáte předplatné Azure, [Vytvořte si bezplatný zkušební účet](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Předpoklady

- Účet [GitHub](https://github.com)
- Účet [Azure](https://portal.azure.com)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>Vytvoření statické webové aplikace

Teď, když je úložiště vytvořené, můžete z Azure Portal vytvořit statickou webovou aplikaci.

1. Přejít na [Azure Portal](https://portal.azure.com)
1. Vyberte **vytvořit prostředek** .
1. Vyhledejte **Static Web Apps**.
1. Vybrat **statické Web Apps (Náhled)**
1. Vyberte **Vytvořit**.

Na kartě _základy_ začněte konfigurací nové aplikace a propojením s úložištěm GitHub.

:::image type="content" source="media/getting-started-portal/basics-tab.png" alt-text="Karta základy":::

1. Výběr vašeho _předplatného Azure_
1. Vyberte nebo vytvořte novou _skupinu prostředků_ .
1. Pojmenujte aplikaci **My-First-static-Web-App**.
      1. Platné znaky jsou `a-z` (bez rozlišování malých a velkých písmen), `0-9` a `-`.
1. Vyberte _oblast_ , která je pro vás nejblíže
1. Výběr **bezplatné** _SKU_
1. Vyberte tlačítko **Přihlásit se pomocí GitHubu** a proveďte ověření pomocí GitHubu.

Až se přihlásíte pomocí GitHubu, zadejte informace o úložišti.

:::image type="content" source="media/getting-started-portal/repository-details.png" alt-text="Podrobnosti úložiště":::

1. Vybrat upřednostňovanou _organizaci_
1. Z rozevíracího seznamu _úložiště_ vyberte **My-First-web-static-App** .
1. V rozevíracím seznamu _větev_ vyberte **Hlavní** .
1. Pro úpravu konfigurace sestavení vyberte tlačítko **Další: >sestavení**

:::image type="content" source="media/getting-started-portal/next-build-button.png" alt-text="Tlačítko Další sestavení":::

> [!NOTE]
> Pokud nevidíte žádná úložiště, možná budete muset autorizovat Azure static Web Apps v GitHubu. Přejděte do úložiště GitHub a přejděte na **nastavení > aplikace > autorizovaných aplikací OAuth**, vyberte **statické Web Apps Azure**a pak vyberte **udělit**. V případě úložišť organizace musíte být vlastníkem organizace, abyste udělili oprávnění.

1. Na kartě _sestavení_ přidejte podrobnosti o konfiguraci, které jsou specifické pro vaši preferovanou front-end architekturu.

    # <a name="no-framework"></a>[Žádná architektura](#tab/vanilla-javascript)

    - Vymazat výchozí hodnotu v poli _umístění aplikace_
    - Vymazat výchozí hodnotu z pole _umístění rozhraní API_
    - Zrušte zaškrtnutí políčka Výchozí hodnota z _umístění artefaktu aplikace_ .

    # <a name="angular"></a>[Angular](#tab/angular)

    - Vymazat výchozí hodnotu v poli _umístění aplikace_
    - Vymazat výchozí hodnotu z pole _umístění rozhraní API_
    - V poli _umístění artefaktu aplikace_ zadejte **DIST/úhlové – Basic** .

    # <a name="react"></a>[React](#tab/react)

    - Vymazat výchozí hodnotu v poli _umístění aplikace_
    - Vymazat výchozí hodnotu z pole _umístění rozhraní API_
    - Do pole _umístění artefaktu aplikace_ zadejte **Build** .

    # <a name="vue"></a>[Vue](#tab/vue)

    - Vymazat výchozí hodnotu v poli _umístění aplikace_
    - Vymazat výchozí hodnotu z pole _umístění rozhraní API_
    - Typ **DIST** v poli _umístění artefaktu aplikace_

    ---

1. Vyberte **Zkontrolovat a vytvořit**.

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="Tlačítko pro vytvoření Revize":::

    > [!NOTE]
    > Úpravou [souboru pracovního postupu](github-actions-workflow.md) můžete po vytvoření aplikace tyto hodnoty změnit.

1. Vyberte **Vytvořit**.

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="Tlačítko Create (Vytvořit)":::

1. Vyberte **Přejít k prostředku**.

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="Tlačítko Go to resource (Přejít k prostředku)":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, můžete instanci statického Web Apps Azure odstranit pomocí následujících kroků:

1. Otevřete [Azure Portal](https://portal.azure.com).
1. Hledání **My-First-web-static-App** z horního panelu hledání
1. Vyberte název aplikace.
1. Vybrat na tlačítku **Odstranit**
1. Kliknutím na **Ano** potvrďte akci odstranit.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání rozhraní API](add-api.md)
