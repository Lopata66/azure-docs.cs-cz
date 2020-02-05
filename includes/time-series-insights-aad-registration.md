---
title: zahrnout soubor
description: zahrnout soubor
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 5be6e7937a6e1f710b8e2576a9058963413fb6c2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984529"
---
1. V [Azure Portal](https://ms.portal.azure.com/)vyberte **Azure Active Directory** > **Registrace aplikací** > **nové registrace**.

   [![registraci nové aplikace v Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    Vaše aplikace se tady zobrazí po registraci.

1. Dejte aplikaci název a vyberte **účty v tomto organizačním adresáři jenom** k určení **podporovaných typů účtů** , které můžou mít přístup k rozhraní API. Zvolte platný identifikátor URI pro přesměrování uživatelů na ověření a pak **Zaregistrujte**.

   [![vytvořit aplikaci v Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Důležité informace o Azure Active Directory aplikace se zobrazí v okně **Přehled** vaší aplikace v seznamu. Vyberte svou aplikaci v části **vlastněné aplikace**a pak klikněte na **Přehled**.

   [![zkopírovat ID aplikace](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Zkopírujte **ID aplikace (klienta)** pro použití v klientské aplikaci.

1. Okno **ověřování** určuje nastavení konfigurace pro ověřování. 

    1. Přidejte **identifikátory URI pro přesměrování** a nakonfigurujte **přístupové tokeny** tak, že vyberete **+ Přidat platformu**.

    1. Určete, jestli je aplikace **veřejným klientem** , nebo ne, a to výběrem možnosti **Ano** nebo **ne**.

    1. Ověřte, které účty a klienti jsou podporované.

    [![konfigurace implicitního udělení](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. Po výběru příslušné platformy nakonfigurujte **identifikátory URI přesměrování** a **přístupové tokeny** na bočním panelu napravo od uživatelského rozhraní.

    1. **Identifikátory URI pro přesměrování** se musí shodovat s adresou zadanou požadavkem ověřování:

        * Pro aplikace hostované v místním vývojovém prostředí vyberte možnost **veřejný klient (mobilní & Desktop)** . Nezapomeňte nastavit **veřejného klienta** na **Ano**.
        * V případě aplikací s jednou stránkou hostovaných v Azure App Service vyberte **Web**.

    1. Určete, zda je **Adresa URL pro odhlášení** vhodná.

    1. Povolte tok implicitního udělení kontrolou **přístupových tokenů** nebo **tokenů ID**.

    [![vytváření identifikátorů URI pro přesměrování](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Klikněte na **Konfigurovat**a pak na **Uložit**.

1. Vyberte **certifikáty & tajných klíčů** a **nový tajný klíč klienta** k vytvoření hesla aplikace, které může klientská aplikace použít k prokázání jeho identity.

   [![vytvoření nového tajného klíče klienta](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Zobrazí se vaše tajné heslo klienta. Zkopírujte klíč do svého oblíbeného textového editoru.

   > [!NOTE]
   > Místo toho máte možnost importovat certifikát. Pro rozšířené zabezpečení se doporučuje certifikát. Chcete-li použít certifikát, vyberte **Odeslat certifikát**.

1. Přidružte svoji aplikaci Azure Active Directory Azure TIme Series Insights. Vyberte **oprávnění rozhraní API** > **přidejte oprávnění** > **rozhraní API moje organizace používá**. 

    [![přidružení rozhraní API k aplikaci Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Do panelu hledání zadejte `Azure Time Series Insights` a pak vyberte `Azure Time Series Insights`.

1. Dále zadejte oprávnění API typu, které vaše aplikace vyžaduje. Ve výchozím nastavení se zvýrazní **delegovaná oprávnění** . Zvolte typ oprávnění a pak vyberte **Přidat oprávnění**.

    [![určit druh oprávnění API, které vaše aplikace vyžaduje](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)
