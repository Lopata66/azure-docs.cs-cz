---
title: Nastavení registrace a přihlášení s účtem WeChat pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty WeChat ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9c9f6b930c5173e97e29148270dedc7eb086ef5a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316302"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení s účtem WeChat pomocí Azure Active Directory B2C

> [!NOTE]
> Tato funkce je ve verzi Preview.
> 

## <a name="create-a-wechat-application"></a>Vytvoření aplikace WeChat

Použít účet WeChat jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet WeChat, získáte informace v [ https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html ](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registrace aplikace WeChat

1. Přihlaste se k [ https://open.weixin.qq.com/ ](https://open.weixin.qq.com/) pomocí svých přihlašovacích údajů WeChat.
2. Vyberte**管理中心**(System center management).
3. Postupujte podle pokynů pro registraci nové aplikace.
4. Zadejte `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v**授权回调域**(adresu URL zpětného volání). Například pokud je název klienta contoso, nastavte adresu URL bude `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
5. Kopírovat **ID aplikace** a **klíče aplikace**. Budete potřebovat tyto přidání zprostředkovatele identity do vašeho tenanta.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurace WeChat jako zprostředkovatele identity ve vašem tenantovi

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *WeChat*.
6. Vyberte **typ zprostředkovatele identit**vyberte **WeChat (Preview)** a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte ID aplikace, které jste si poznamenali dříve, jako **ID klienta** a zadejte klíč aplikace, který jste si poznamenali jako **tajný kód klienta** z WeChat aplikaci, kterou jste vytvořili dříve.
8. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci WeChat.

