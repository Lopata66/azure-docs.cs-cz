---
title: Nastavení registrace a přihlášení pomocí OpenID Connect-Azure Active Directory B2C | Microsoft Docs
description: Nastavte si registraci a přihlaste se pomocí OpenID Connect pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0303f8c7e18a5c229bc5a8c5e9b90d95cdaccbe7
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672908"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí OpenID Connect pomocí Azure Active Directory B2C

[OpenID Connect](active-directory-b2c-reference-oidc.md) je protokol ověřování založený na OAuth 2,0, který se dá použít k zabezpečenému přihlášení uživatelů. Většina zprostředkovatelů identity, kteří používají tento protokol, se v Azure AD B2C podporují. Tento článek vysvětluje, jak můžete do toků uživatelů přidat vlastní zprostředkovatele identity OpenID Connect.

## <a name="add-the-identity-provider"></a>Přidat zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C kliknutím na v horní nabídce na položku filtr **adresářů a předplatných** a vyberte adresář, který obsahuje vašeho tenanta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a potom vyberte **Nový poskytovatel OpenID Connect**.

## <a name="configure-the-identity-provider"></a>Konfigurace zprostředkovatele identity

Každý zprostředkovatel identity OpenID Connect popisuje dokument metadat, který obsahuje většinu informací potřebných k provedení přihlášení. Zahrnuje to i informace, jako jsou adresy URL, které se mají použít, a umístění veřejných podpisových klíčů služby. Dokument metadat OpenID Connect je vždycky umístěný na koncovém bodu, který končí `.well-known\openid-configuration`na. Pro poskytovatele identity OpenID Connect, kterého chcete přidat, zadejte adresu URL metadat.

## <a name="client-id-and-secret"></a>ID klienta a tajný kód

Pokud chcete uživatelům dovolit, aby se přihlásili, poskytovatel identity vyžaduje, aby vývojáři zaregistrovali aplikaci ve své službě. Tato aplikace má ID, které se označuje jako **ID klienta** a **tajný klíč klienta**. Zkopírujte tyto hodnoty z poskytovatele identity a zadejte je do odpovídajících polí.

> [!NOTE]
> Tajný kód klienta je nepovinný. Pokud byste ale chtěli použít [tok autorizačního kódu](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), musíte zadat tajný klíč klienta, který používá tajný klíč k výměně kódu pro token.

## <a name="scope"></a>Scope

Obor definuje informace a oprávnění, které chcete shromáždit od vlastního poskytovatele identity. Žádosti o připojení OpenID musí obsahovat `openid` hodnotu oboru, aby bylo možné získat token ID od poskytovatele identity. Bez tokenu ID se uživatelé nebudou moct přihlašovat k Azure AD B2C pomocí vlastního zprostředkovatele identity. Další obory lze přidat oddělené mezerou. Další informace o tom, jaké další obory mohou být k dispozici, najdete v dokumentaci vlastního zprostředkovatele identity.

## <a name="response-type"></a>Typ odpovědi

Typ odpovědi popisuje, jaký druh informací se pošle zpátky při počátečním volání `authorization_endpoint` vlastního zprostředkovatele identity. Můžete použít následující typy odpovědí:

* `code`: Dle [toku autorizačního kódu](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)se kód vrátí zpět na Azure AD B2C. Azure AD B2C pokračuje v `token_endpoint` volání metody pro výměnu kódu pro token.
* `id_token`: Token ID se vrátí zpět do Azure AD B2C z vlastního zprostředkovatele identity.

## <a name="response-mode"></a>Režim odpovědi

Režim odezvy definuje metodu, která se má použít k odeslání dat zpět z vlastního zprostředkovatele identity do Azure AD B2C. Můžete použít následující režimy odezvy:

* `form_post`: Tento režim odezvy se doporučuje pro nejvyšší zabezpečení. Odpověď je přenášena prostřednictvím metody http `POST` s kódem nebo tokenem kódovaným v těle `application/x-www-form-urlencoded` pomocí formátu.
* `query`: Kód nebo token se vrátí jako parametr dotazu.

## <a name="domain-hint"></a>Nápověda domény

Pomocí pomocného parametru domény můžete přeskočit přímo na přihlašovací stránku zadaného zprostředkovatele identity, aniž by uživatel musel vybírat ze seznamu dostupných zprostředkovatelů identity. Chcete-li tento druh chování použít, zadejte hodnotu parametru doména. Pokud chcete přejít na vlastního zprostředkovatele identity, přidejte parametr `domain_hint=<domain hint value>` na konec vaší žádosti při volání Azure AD B2C pro přihlášení.

## <a name="claims-mapping"></a>Mapování deklarací identity

Jakmile vlastní zprostředkovatel identity pošle token ID zpátky na Azure AD B2C, Azure AD B2C potřebuje být schopný mapovat deklarace identity z přijatého tokenu na deklarace identity, které Azure AD B2C rozpoznává a používá. Pro každé z následujících mapování si přečtěte dokumentaci k vlastnímu zprostředkovateli identity a pochopte deklarace vracené zpět v tokenech zprostředkovatele identity:

* **ID uživatele**: Zadejte deklaraci identity, která poskytuje *jedinečný identifikátor* přihlášeného uživatele.
* **Zobrazovaný název**: Zadejte deklaraci identity, která pro uživatele poskytne *zobrazované jméno* nebo *celé jméno* .
* **Křestní jméno**: Zadejte deklaraci identity, která poskytuje *křestní jméno* uživatele.
* **Příjmení**: Zadejte deklaraci identity, která poskytuje *poslední jméno* uživatele.
* **E-mail**: Zadejte deklaraci identity, která poskytuje *e-mailovou adresu* uživatele.
