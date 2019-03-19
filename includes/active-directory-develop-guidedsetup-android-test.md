---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 0a2e5982832dcf1747389eb78c6a1b1cb5762b64
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57964113"
---
## <a name="test-your-app"></a>Testování aplikace

1. Vaše zařízení nebo emulátoru spustíte kód.
2. Zkuste se přihlásit pomocí účtu služby Azure Active Directory (pracovní nebo školní účet) nebo účet Microsoft (live.com, outlook.com). 

    ![Testování aplikace](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![Zadejte uživatelské jméno a heslo](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="consent-to-your-app"></a>Souhlas s vaší aplikace

Při prvním přihlášení uživatele do vaší aplikace, uživatelé budou vyzváni k vyjádřit souhlas s oprávněními požadavkům vašich aplikací, jak je znázorněno zde: 

![Zadejte svůj souhlas pro přístup k aplikaci](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)

### <a name="success"></a>Výborně!

Po přihlášení a vyjádření souhlasu, aplikace se zobrazí odpověď z rozhraní Microsoft Graph API. Je toto specifické volání **/me** koncový bod a vrátí [profilu uživatele](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_get). Seznam dalších koncových bodů Microsoft Graphu najdete v tématu [dokumentaci pro vývojáře Microsoft Graph API](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="scopes-and-delegated-permissions"></a>Obory a delegovaná oprávnění

Vyžaduje rozhraní Microsoft Graph API *User.Read* obory a čtení profilu uživatele. Tento obor je automaticky v každé aplikaci, který je registrován v portálu pro registraci aplikace. Další rozhraní API bude vyžadovat další obory. Například rozhraní Microsoft Graph API vyžaduje *Calendars.Read* oboru seznam kalendářů uživatele.

Chcete-li přístup ke kalendářům uživatele, přidejte *Calendars.Read* delegovaná oprávnění aplikace informace o registraci. Pak přidejte *Calendars.Read* rozsah `acquireTokenSilent` volání. 

> [!NOTE]
> Uživatelům může zobrazit výzva k další souhlasu při změně registrace vaší aplikace.

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
