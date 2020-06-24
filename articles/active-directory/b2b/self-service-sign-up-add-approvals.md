---
title: Přidání vlastních schválení pro samoobslužné registrační toky – Azure AD
description: Přidání konektorů API pro vlastní pracovní postupy schvalování v samoobslužné registraci externích identit – Azure Active Directory (Azure AD)
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45a08ff8d1f796cec2d4562ae64392253a539416
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84886490"
---
# <a name="add-a-custom-approval-workflow-to-self-service-sign-up"></a>Přidání vlastního pracovního postupu schválení pro samoobslužné přihlášení

Pomocí [konektorů rozhraní API](api-connectors-overview.md)můžete integrovat s vlastními pracovními postupy schvalování pomocí samoobslužné registrace, abyste mohli spravovat, které uživatelské účty hosta se vytvoří ve vašem tenantovi.

Tento článek obsahuje příklad integrace se schvalovacím systémem. V tomto příkladu uživatelský tok samoobslužné registrace shromažďuje data uživatelů během procesu registrace a předává je do vašeho schvalovacího systému. Systém schvalování pak může:

- Automaticky schválí uživatele a povolí službě Azure AD vytvořit uživatelský účet.
- Aktivovat ruční kontrolu. Pokud je žádost schválena, systém schvalování používá Microsoft Graph k zřízení uživatelského účtu. Schvalovací systém může také uživatele informovat o tom, že byl vytvořen jejich účet.

## <a name="register-an-application-for-your-approval-system"></a>Registrace aplikace pro váš systém schválení

Svůj schvalovací systém musíte zaregistrovat jako aplikaci v tenantovi Azure AD, aby se mohl ověřit pomocí Azure AD a mít oprávnění k vytváření uživatelů. Přečtěte si další informace o [ověřování a základech autorizace pro Microsoft Graph](https://docs.microsoft.com/graph/auth/auth-concepts).

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. V části **služby Azure**vyberte **Azure Active Directory**.
3. V nabídce vlevo vyberte **Registrace aplikací**a pak vyberte **Nová registrace**.
4. Zadejte **název** aplikace, například _schválení registrací_.

   <!-- ![Register an application for the approval system](./self-service-sign-up-add-approvals/approvals/register-an-approvals-application.png) -->

5. Vyberte **Zaregistrovat**. Můžete ponechat další pole ve výchozím nastavení.

   ![Registrovat stránku aplikace](media/self-service-sign-up-add-approvals/register-approvals-app.png)

6. V části **Spravovat** v nabídce vlevo vyberte **oprávnění rozhraní API**a pak vyberte **Přidat oprávnění**.
7. Na stránce **žádost o oprávnění API** vyberte **Microsoft Graph**a pak vyberte **oprávnění aplikace**.
8. V části **vybrat oprávnění**rozbalte položku **uživatel**a potom zaškrtněte políčko **uživatel. čtení. All** . Toto oprávnění umožňuje schvalovacímu systému vytvořit uživatele při schválení. Pak vyberte **Přidat oprávnění**.

   ![Registrovat stránku aplikace](media/self-service-sign-up-add-approvals/request-api-permissions.png)

9. Na stránce **oprávnění rozhraní API** vyberte **udělit souhlas správce pro (název tenanta)** a pak vyberte **Ano**.
10. V části **Spravovat** v nabídce vlevo vyberte **certifikáty & tajných**kódů a pak vyberte **nový tajný klíč klienta**.
11. Zadejte **Popis** tajného kódu, například _schválení tajného kódu klienta_, a vyberte dobu, po **jejímž uplynutí bude platnost**tajného klíče klienta ukončena. Pak vyberte **Přidat**.
12. Zkopírujte hodnotu tajného klíče klienta.

    ![Kopírovat tajný klíč klienta pro použití ve schvalovacím systému](media/self-service-sign-up-add-approvals/client-secret-value-copy.png)

13. Nakonfigurujte systém schvalování tak, aby používal **ID aplikace** jako ID klienta a **tajný klíč klienta** , který jste vygenerovali pro ověření ve službě Azure AD.

## <a name="create-the-api-connectors"></a>Vytvoření konektorů rozhraní API

V dalším kroku [vytvoříte konektory rozhraní API](self-service-sign-up-add-api-connector.md#create-an-api-connector) pro uživatelský tok samoobslužné registrace. Vaše systémové rozhraní API pro schvalování potřebuje dva konektory a odpovídající koncové body, podobně jako v níže uvedených příkladech. Tyto konektory API jsou následující:

- **Zkontroluje stav schválení**. Jakmile se uživatel přihlásí pomocí poskytovatele identity, požádejte ho, aby zkontroloval, jestli má uživatel existující žádost o schválení nebo už byl zamítnutý. Pokud váš systém schvalování provede jenom rozhodnutí o automatickém schválení, nemusí být tento konektor API potřebný. V následujícím příkladu je konektor rozhraní API pro kontrolu stavu schválení.

  ![Zkontroluje konfiguraci konektoru rozhraní API stavu schválení.](./media/self-service-sign-up-add-approvals/check-approval-status-api-connector-config-alt.png)

- **Žádost o schválení** – odeslání volání schvalovacímu systému poté, co uživatel dokončí stránku kolekce atributů, ale před vytvořením uživatelského účtu pro vyžádání schválení. Žádost o schválení se dá automaticky udělit nebo ručně zkontrolovat. Následuje příklad konektoru rozhraní API "schválení žádosti". Vyberte všechny **deklarace identity, které zasílají** , aby schvalovací systém mohl učinit rozhodnutí o schválení.

  ![Konfigurace požadavku na schválení konektoru rozhraní API](./media/self-service-sign-up-add-approvals/create-approval-request-api-connector-config-alt.png)

Chcete-li vytvořit tyto konektory, postupujte podle kroků v části [Vytvoření konektoru rozhraní API](self-service-sign-up-add-api-connector.md#create-an-api-connector).

## <a name="enable-the-api-connectors-in-a-user-flow"></a>Povolení konektorů rozhraní API v toku uživatele

Nyní přidáte konektory rozhraní API k samoobslužnému uživatelskému toku registrace pomocí těchto kroků:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/) jako správce.
2. V části **služby Azure**vyberte **Azure Active Directory**.
3. V nabídce vlevo vyberte **externí identity**.
4. Vyberte **toky uživatelů (Preview)** a pak vyberte tok uživatele, pro který chcete povolit konektor API.
5. Vyberte možnost **konektory rozhraní API**a potom vyberte koncové body rozhraní API, které chcete vyvolat, v následujících krocích v toku uživatele:

   - **Po přihlášení pomocí zprostředkovatele identity**: vyberte svůj stav schválení váš konektor API, třeba _Ověřte stav schválení_.
   - **Před vytvořením uživatele**: vyberte svůj konektor API žádosti o schválení, například _žádost o schválení_.

   ![Přidání rozhraní API do toku uživatele](./media/self-service-sign-up-add-approvals/api-connectors-user-flow-api.png)

6. Vyberte **Uložit**.

## <a name="control-the-sign-up-flow-with-api-responses"></a>Řízení toku registrace pomocí odpovědí rozhraní API

Váš systém schvalování může použít [typy odezvy rozhraní API](self-service-sign-up-add-api-connector.md#expected-response-types-from-the-web-api) ze dvou koncových bodů rozhraní API k řízení toku registrace.

### <a name="request-and-responses-for-the-check-approval-status-api-connector"></a>Požadavek a odpovědi na konektor rozhraní API pro kontrolu stavu schválení

Níže je příklad žádosti přijaté rozhraním API z konektoru rozhraní API pro kontrolu stavu schválení:

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email_address": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "ui_locales":"en-US"
}
```

#### <a name="continuation-response-for-check-approval-status"></a>Reakce na pokračování pro "kontrolu stavu schválení"

Koncový bod rozhraní API pro **kontrolu stavu schválení** by měl vrátit odpověď pokračování, pokud:

- Uživatel dřív nepožadoval schválení.

Následuje příklad reakce na pokračování:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

#### <a name="blocking-response-for-check-approval-status"></a>Blokování odpovědi na stav schválení kontroly

Koncový bod rozhraní API pro **kontrolu stavu schválení** by měl vrátit blokující odpověď, pokud:

- Čeká se na schválení uživatele.
- Uživatel byl zamítnut a neměl by mít povolený požadavek na schválení.

Následují příklady blokování odpovědí:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your access request is already processing. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-PENDING"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-DENIED"
}
```

### <a name="request-and-responses-for-the-request-approval-api-connector"></a>Žádost a odpovědi pro konektor API "schválení žádosti"

Následuje příklad požadavku HTTP přijatého rozhraním API z konektoru API "schválení žádosti":

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email_address": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

#### <a name="continuation-response-for-request-approval"></a>Reakce na pokračování pro "schválení žádosti"

Koncový bod rozhraní API pro **schválení žádosti** by měl vrátit odpověď pokračování, pokud:

- Uživatel může být **_automaticky schválen_**.

Následuje příklad reakce na pokračování:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

> [!IMPORTANT]
> Pokud je přijata odpověď na pokračování, Azure AD vytvoří uživatelský účet a přesměruje uživatele do aplikace.

#### <a name="blocking-response-for-request-approval"></a>Blokující odpověď pro "schválení žádosti"

Koncový bod rozhraní API pro **schvalování žádostí** by měl vrátit blokující odpověď, pokud:

- Žádost o schválení uživatele se vytvořila a teď čeká na vyřízení.
- Žádost o schválení uživatele se automaticky zamítla.

Následují příklady blokování odpovědí:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your account is now waiting for approval. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-REQUESTED"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-AUTO-DENIED"
}
```

`userMessage`V odpovědi se zobrazí uživateli, například:

![Příklad stránky čeká na schválení](./media/self-service-sign-up-add-approvals/approval-pending.png)

## <a name="user-account-creation-after-manual-approval"></a>Vytvoření uživatelského účtu po manuálním schválení

Po získání ručního schválení vytvoří vlastní systém schvalování [uživatelský](https://docs.microsoft.com/graph/azuread-users-concept-overview) účet pomocí [Microsoft Graph](https://docs.microsoft.com/graph/use-the-api). Způsob, jakým systém schvalování zajišťuje, závisí uživatelský účet na poskytovateli identity, který použil uživatel.

### <a name="for-a-federated-google-or-facebook-user"></a>Pro uživatele federovaného Google nebo Facebook

> [!IMPORTANT]
> Systém schvalování musí explicitně ověřit, jestli `identities` `identities[0]` `identities[0].issuer` jsou přítomná a která se `identities[0].issuer` rovná Facebooku nebo Google, aby používala tuto metodu.

Pokud se uživatel přihlásil pomocí účtu Google nebo Facebook, můžete použít [rozhraní API pro vytvoření uživatele](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0&tabs=http).

1. Systém schvalování používá požadavek HTTP od toku uživatele.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email_address": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. Systém schvalování používá Microsoft Graph k vytvoření uživatelského účtu.

```http
POST https://graph.microsoft.com/v1.0/users
Content-type: application/json

{
 "userPrincipalName": "johnsmith_outlook.com#EXT@contoso.onmicrosoft.com",
 "accountEnabled": true,
 "mail": "johnsmith@outlook.com",
 "userType": "Guest",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value"
}
```

| Parametr                                           | Povinné | Popis                                                                                                                                                            |
| --------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| userPrincipalName (Hlavní název uživatele)                                   | Ano      | Dá se vygenerovat tak, že se převezme deklarace, která se `email_address` pošle do rozhraní API, nahradí se `@` znak za `_` a předá ho do `#EXT@<tenant-name>.onmicrosoft.com` . |
| accountEnabled                                      | Ano      | Musí být nastaven na hodnotu `true` .                                                                                                                                                 |
| pošta                                                | Ano      | Ekvivalent k `email_address` deklaraci identity odeslané do rozhraní API.                                                                                                               |
| userType                                            | Ano      | Musí být `Guest` . Určí tohoto uživatele jako uživatel typu Host.                                                                                                                 |
| nebyly                                          | Ano      | Informace o federované identitě.                                                                                                                                    |
| \<otherBuiltInAttribute>                            | Ne       | Jiné předdefinované atributy jako `displayName` , `city` a další. Názvy parametrů jsou stejné jako parametry odesílané konektorem rozhraní API.                            |
| \<extension\_\{extensions-app-id}\_CustomAttribute> | Ne       | Vlastní atributy uživatele Názvy parametrů jsou stejné jako parametry odesílané konektorem rozhraní API.                                                            |

### <a name="for-a-federated-azure-active-directory-user"></a>Pro uživatele federovaného Azure Active Directory

Pokud se uživatel přihlásí pomocí federovaného Azure Active Directory účtu, je nutné použít [rozhraní API pro pozvání](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0) k vytvoření uživatele a volitelně také [rozhraní API pro aktualizaci uživatele](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0) k přiřazení dalších atributů uživateli.

1. Schvalovací systém přijme požadavek HTTP od toku uživatele.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email_address": "johnsmith@fabrikam.onmicrosoft.com",
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. Systém schvalování vytvoří pozvánku pomocí `email_address` konektoru rozhraní API.

```http
POST https://graph.microsoft.com/v1.0/invitations
Content-type: application/json

{
    "invitedUserEmailAddress":"johnsmith@fabrikam.onmicrosoft.com",
    "inviteRedirectUrl" : "https://myapp.com"
}
```

Následuje příklad odpovědi:

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    ...
    "invitedUser": {
        "id": "<generated-user-guid>"
    }
}
```

3. Systém schvalování používá ID pozvaného uživatele k aktualizaci uživatelského účtu pomocí shromážděných atributů uživatele (volitelné).

```http
PATCH https://graph.microsoft.com/v1.0/users/<generated-user-guid>
Content-type: application/json

{
    "displayName": "John Smith",
    "city": "Redmond",
    "extension_<extensions-app-id>_AttributeName": "custom attribute value"
}
```

## <a name="next-steps"></a>Další kroky

- Začněte s našimi [ukázkami Azure Functions pro rychlý Start](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
- Zarezervujte [samoobslužnou registraci pro uživatele typu Host pomocí ukázky ručního schválení](code-samples-self-service-sign-up.md#custom-approval-workflows). 
