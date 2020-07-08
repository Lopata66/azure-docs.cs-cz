---
title: Vytvoření první funkce na webu Azure Portal
description: Naučíte se postup vytvoření první funkce Azure Function pro provádění pomocí webu Azure Portal bez serveru.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 030af8a289daaf03d17f8402e8d603e893657853
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83123602"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Vytvoření první funkce na webu Azure Portal

Azure Functions umožňuje spuštění kódu v prostředí bez serveru, aniž by bylo nutné nejprve vytvořit virtuální počítač nebo publikovat webovou aplikaci. V tomto článku se naučíte, jak pomocí Azure Functions vytvořit funkci triggeru HTTP "Hello World" v Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Pokud jste vývojář jazyka C#, zvažte [Vytvoření první funkce v aplikaci Visual Studio 2019](functions-create-your-first-function-visual-studio.md) místo na portálu. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

## <a name="create-a-function-app"></a>Vytvoření Function App

K hostování provádění funkcí musíte mít aplikaci Function App. Aplikace Function App umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování, škálování a sdílení prostředků.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Dále vytvořte funkci v nové aplikaci Function App.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Vytvoření funkce triggeru HTTP

1. V levé nabídce okna **Functions (funkce** ) vyberte **funkce**a pak v horní nabídce vyberte **Přidat** . 
 
1. V **novém okně funkce** vyberte **Trigger http**.

    ![Zvolit funkci triggeru HTTP](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. V okně **Nová funkce** přijměte výchozí název **nové funkce**nebo zadejte nový název. 

1. V rozevíracím seznamu **úroveň autorizace** zvolte **anonymní** a pak vyberte **vytvořit funkci**.

    Azure vytvoří funkci triggeru HTTP. Novou funkci můžete spustit odesláním požadavku HTTP.

## <a name="test-the-function"></a>Testování funkce

1. V nové funkci triggeru HTTP v nabídce vlevo vyberte **Code + test** a potom v horní nabídce vyberte **získat adresu URL funkce** .

    ![Vyberte získat adresu URL funkce](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. V dialogovém okně **získat adresu URL funkce** vyberte v rozevíracím seznamu možnost **výchozí** a potom vyberte ikonu **Kopírovat do schránky** . 

    ![Kopírování adresy URL funkce z webu Azure Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Vložte adresu URL funkce do panelu Adresa vašeho prohlížeče. Na `?name=<your_name>` konec této adresy URL přidejte hodnotu řetězce dotazu a stisknutím klávesy ENTER spusťte požadavek. 

    Následující příklad ukazuje odpověď v prohlížeči:

    ![Odezva funkce v prohlížeči.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    Adresa URL požadavku obsahuje klíč, který je ve výchozím nastavení nezbytný pro přístup k funkci přes protokol HTTP.

1. Při spuštění funkce se do protokolů zaznamenávají informace o trasování. Chcete-li zobrazit výstup trasování, vraťte se na stránku **Code + test** na portálu a rozbalte šipku **protokoly** v dolní části stránky.

   ![Prohlížeč protokolu funkcí na webu Azure Portal.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

