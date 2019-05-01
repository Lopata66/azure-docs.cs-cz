---
title: Kurz – přizpůsobení rozhraní uživatelské prostředí – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak přizpůsobit uživatelské rozhraní aplikace v Azure Active Directory B2C na webu Azure portal.
services: B2C
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: c2a84bf72ab68937224ac93bd9ffd035e32c603d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702551"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Kurz: Přizpůsobení rozhraní uživatelského prostředí v Azure Active Directory B2C

Pro běžné uživatelské prostředí jako například registrace, přihlašování a úpravy profilu, můžete použít [toky uživatelů](active-directory-b2c-reference-policies.md) v Azure Active Directory (Azure AD) B2C. Informace v tomto kurzu vám umožní získat informace tom, jak [přizpůsobení uživatelského rozhraní (UI)](customize-ui-overview.md) z těchto prostředí s využitím vlastní soubory HTML a CSS.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření souborů přizpůsobení uživatelského rozhraní
> * Aktualizovat tok uživatele k využití souborů
> * Testování vlastní uživatelské rozhraní

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

[Vytvořit tok uživatele](tutorial-create-user-flows.md) umožňují uživatelům registraci a přihlášení do vaší aplikace.

## <a name="create-customization-files"></a>Vytvořit soubory vlastního nastavení

Vytvoření účtu služby Azure storage a kontejner a pak umístit základní soubory HTML a CSS v kontejneru.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

I když můžete ukládat soubory v mnoha směrech pro účely tohoto kurzu, jsou uloženy v [úložiště objektů Blob v Azure](../storage/blobs/storage-blobs-introduction.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje předplatné Azure. Vyberte **filtr adresářů a předplatných** v horní nabídce a vyberte adresář, který obsahuje vaše předplatné. Tento adresář je jiný než ten, který obsahuje váš tenant Azure B2C.
3. Vyberte všechny služby v levém horním rohu webu Azure portal, vyhledejte a vyberte **účty úložiště**. 
4. Vyberte **Přidat**.
5. V části **skupiny prostředků**vyberte **vytvořit nový**, zadejte název pro novou skupinu prostředků a pak klikněte na tlačítko **OK**.
6. Zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure, mít délku 3 až 24 znaků a může obsahovat jen číslice a malá písmena.
7. Vyberte umístění účtu úložiště, nebo přijměte výchozí umístění. 
8. Přijměte všechny výchozí hodnoty, vyberte **revize + vytvořit**a potom klikněte na tlačítko **vytvořit**.
9. Po vytvoření účtu úložiště vyberte **přejít k prostředku**.

### <a name="create-a-container"></a>Vytvoření kontejneru

1. Na stránce Přehled účtu úložiště vyberte **objekty BLOB**.
2. Vyberte **kontejneru**, zadejte název kontejneru, zvolte **objektů Blob (anonymní přístup pro čtení pro objekty BLOB pouze)** a potom klikněte na tlačítko **OK**.

### <a name="enable-cors"></a>Povolení CORS

 Azure AD B2C kód v prohlížeči používá moderní a standardní přístup k načtení vlastního obsahu z adresy URL, kterou zadáte v toku uživatele. Prostředků mezi zdroji (CORS) pro sdílení obsahu s omezeným přístupem prostředků umožňuje na webové stránce vyžadované z jiných domén.

1. V nabídce vyberte **CORS**.
2. Pro **povolené zdroje**, zadejte `https://your-tenant-name.b2clogin.com`. Nahraďte `your-tenant-name` s názvem vašeho tenanta Azure AD B2C. Například, `https://fabrikam.b2clogin.com`. Budete muset použít jenom malá písmena. Pokud zadáte název vašeho klienta.
3. Pro **povolené metody**, vyberte oba `GET` a `OPTIONS`.
4. Pro **povolené hlavičky**, zadejte hvězdičku (*).
5. Pro **zveřejněné hlavičky**, zadejte hvězdičku (*).
6. Pro **maximální stáří**, zadejte 200.

    ![Povolení CORS](./media/tutorial-customize-ui/enable-cors.png)

5. Klikněte na **Uložit**.

### <a name="create-the-customization-files"></a>Vytvořit soubory vlastního nastavení

Přizpůsobení uživatelského rozhraní přihlašovacího prostředí, začnete vytvořením jednoduchého souboru HTML a CSS. Můžete nakonfigurovat libovolným způsobem, ale musí mít kódu HTML **div** element s identifikátorem `api`. Například, `<div id="api"></div>`. Vloží prvky do Azure AD B2C `api` stránka se zobrazí v kontejneru.

1. V místní složce, vytvořte následující soubor a ujistěte se, že změníte `your-storage-account` k názvu účtu úložiště a `your-container` název kontejneru, který jste vytvořili. Například, `https://store1.blob.core.windows.net/b2c/style.css`.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>  
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    Na stránce nelze navrhovat způsobem, který chcete, ale **api** div element se vyžaduje pro libovolný soubor vlastního nastavení HTML, který vytvoříte. 

3. Uložte soubor jako *vlastní ui.html*.
4. Vytvořte následující jednoduchý šablon stylů CSS, která centra pro všechny elementy na stránce registrace / přihlášení, včetně prvků, které Azure AD B2C vkládá.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center; 
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center; 
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. Uložte soubor jako *style.css*.

### <a name="upload-the-customization-files"></a>Nahrát soubory vlastního nastavení

V tomto kurzu ukládáte soubory, které jste vytvořili v účtu úložiště tak, aby Azure AD B2C k nim přistupovat.

1. Zvolte **všechny služby** v levém horním rohu webu Azure portal, vyhledejte a vyberte **účty úložiště**.
2. Vyberte účet úložiště, který jste vytvořili, vyberte **objekty BLOB**a potom vyberte kontejner, který jste vytvořili.
3. Vyberte **nahrát**, najděte a vyberte *vlastní ui.html* souboru a pak klikněte na tlačítko **nahrát**.

    ![Nahrát soubory vlastního nastavení](./media/tutorial-customize-ui/upload-blob.png)

4. Zkopírujte adresu URL souboru, který jste nahráli do použít později v tomto kurzu.
5. Zopakujte kroky 3 a 4 *style.css* souboru.

## <a name="update-the-user-flow"></a>Aktualizovat tok uživatele.

1. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
2. Vyberte **toky uživatelů (zásady)** a pak vyberte *B2C_1_signupsignin1* tok uživatele.
3. Vyberte **rozložení stránek**a potom v části **stránku registrace nebo přihlášení Unified**, klikněte na tlačítko **Ano** pro **použít vlastní obsah stránky**.
4. V **vlastní identifikátor URI stránky**, zadejte identifikátor URI *vlastní ui.html* soubor, který jste si poznamenali dříve.
5. V horní části stránky vyberte **Uložit**.

## <a name="test-the-user-flow"></a>Testování tohoto toku uživatele

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů** a vyberte *B2C_1_signupsignin1* tok uživatele.
2. V horní části stránky klikněte na tlačítko **spustit tok uživatele**.
3. Klikněte na tlačítko **spustit tok uživatele** tlačítko.

    ![Spuštění toku registrace nebo přihlašování uživatelů](./media/tutorial-customize-ui/run-user-flow.png)

    Měli byste vidět stránku podobně jako v následujícím příkladu s elementy na střed na základě souboru šablon stylů CSS, který jste vytvořili:

    ![Výsledky toku uživatele](./media/tutorial-customize-ui/run-now.png) 

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření souborů přizpůsobení uživatelského rozhraní
> * Aktualizovat tok uživatele k využití souborů
> * Testování vlastní uživatelské rozhraní

> [!div class="nextstepaction"]
> [Přizpůsobení jazyka v Azure Active Directory B2C](active-directory-b2c-reference-language-customization.md)
