---
title: Přidat nativní klientské aplikace – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak přidat nativní klientské aplikace do svého tenanta Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b4e9b95cb226aeec686816d0ed7160062e110c62
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511828"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Přidat nativní klientské aplikace do svého tenanta Azure Active Directory B2C

Nativní klient systému prostředky musí být zaregistrovaní ve vašem tenantovi předtím, než aplikace komunikovat se službou Azure Active Directory B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikací**a pak vyberte **přidat**.
2. Zadejte název aplikace. Například *nativeapp1*.
3. Pro **zahrnout webovou aplikaci / webové rozhraní API**vyberte **ne**.
4. Pro **zahrnout nativního klienta**vyberte **Ano**.
5. Pro **identifikátor URI pro přesměrování**, zadejte na platný identifikátor URI přesměrování s vlastním schématem. Existují dva důležité aspekty při výběru identifikátoru URI přesměrování:

    - **Jedinečné** – schéma identifikátoru URI přesměrování by mělo být pro každou aplikaci jedinečné. V příkladu `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` schéma com.onmicrosoft.contoso.appname. Tento model má následovat. Pokud dvě aplikace sdílejí stejné schéma, uživateli je možnost volby vybrat aplikace. Pokud uživatel použije nesprávnou volbu, přihlášení selže.
    - **Kompletní** – identifikátor URI pro přesměrování musí mít schéma a cestu. Cesta musí obsahovat za doménou alespoň jedno lomítko. Například `//contoso/` funguje a `//contoso` selže. Ujistěte se, že identifikátor URI přesměrování neobsahuje speciální znaky jako podtržítka.

6. Klikněte na možnost **Vytvořit**.
7. Na stránce Vlastnosti zaznamenejte ID aplikace, které budete používat při konfiguraci nativní klientské aplikace.
