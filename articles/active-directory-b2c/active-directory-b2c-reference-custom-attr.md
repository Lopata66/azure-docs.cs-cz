---
title: Definování uživatelských atributů, které v Azure Active Directory B2C | Dokumentace Microsoftu
description: Definujte vlastní atributy pro vaši aplikaci v Azure Active Directory B2C ke shromažďování informací o vašich zákazníků.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 629b872f78a30592fc00cb268066970b12b20561
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952801"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definování uživatelských atributů, které v Azure Active Directory B2C

 Každá aplikace určených pro zákazníky má jedinečné požadavky, které je potřeba shromáždit informace. Váš tenant Azure Active Directory (Azure AD) B2C se dodává s integrovanou sadou informací uložených v atributech, například křestní jméno, příjmení, Město a PSČ. S Azure AD B2C můžete rozšířit sadu atributů, které jsou uložené na každý účet zákazníka. 
 
 Můžete vytvořit vlastní atributy [webu Azure portal](https://portal.azure.com/) a jejich použití v toky registraci uživatelů, toky registrace nebo přihlašování uživatelů nebo toky uživatelů pro upravování profilu. Může číst a zapisovat pomocí těchto atributů [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md). Použití vlastních atributů v Azure AD B2C [Azure AD Graph API adresáře schématu rozšíření](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).  

> [!NOTE]
> Podpora pro novější [Microsoft Graph API](https://docs.microsoft.com/graph/overview?view=graph-rest-1.0) pro dotazování na Azure AD B2C tenanta je stále ve vývoji.
>

## <a name="create-a-custom-attribute"></a>Vytvoření vlastního atributu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Přepněte v pravém horním rohu portálu Azure Portal na adresář, který obsahuje tenanta Azure AD B2C, a ujistěte se tak, že používáte správný adresář. Vyberte informace o předplatném a pak **Přepnout adresář**. 

    ![Přepnutí na tenanta Azure AD B2C](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    Vyberte adresář, který obsahuje vašeho tenanta.

    ![Výběr adresáře](./media/active-directory-b2c-reference-custom-attr/select-directory.png)

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **atributy uživatele**a pak vyberte **přidat**.
5. Zadejte **název** pro vlastní atribut (například "ShoeSize")
6. Zvolte **datový typ**. Pouze **řetězec**, **logická**, a **Int** jsou k dispozici.
7. Volitelně můžete zadat **popis** k informačním účelům. 
8. Klikněte na možnost **Vytvořit**.

Vlastní atribut je nyní k dispozici v seznamu **atributy uživatele** a pro použití ve svých tocích uživatelů. Vlastní atribut je jen pro vytvoří při prvním se používá v toku uživatele, a ne v případě, že ho přidáte do seznamu **atributy uživatele**. 


## <a name="use-a-custom-attribute-in-your-user-flow"></a>Použít vlastní atribut ve svém toku uživatele

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů**.
2. Vyberte zásadu (například "B2C_1_SignupSignin") a otevřete ho. 
4. Vyberte **atributy uživatele** a pak vyberte vlastní atribut (například "ShoeSize"). Klikněte na **Uložit**.
5. Vyberte **deklarace identit aplikace** a pak vyberte vlastní atribut. 
6. Klikněte na **Uložit**.

Po vytvoření nového uživatele pomocí tok uživatele, která využívá nově vytvořený vlastní atribut objektu může být dotazována v [Azure AD Graph Explorer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart). Případně můžete použít [ **spustit tok uživatele** ](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) funkci na tok uživatele. Chcete-li ověřit uživatelské prostředí. Teď byste měli vidět **ShoeSize** v seznam atributů shromážděných během registrace cesty a vidět ji v tokenu odesílaných zpět do aplikace. 

