---
title: Azure Active Directory pro vývojáře (verze 1.0) – přehled
description: Tento článek obsahuje přehled přihlašování Microsoft pracovních a školních účtů s využitím koncového bodu Azure Active Directory verze 1.0 a platformu.
services: active-directory
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2af95d979e90f25c013f5112322d830232199a4b
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067643"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Azure Active Directory pro vývojáře (verze 1.0) – přehled

Azure Active Directory (Azure AD) je cloudová služba identit umožňující vývojářům sestavovat aplikace, které bezpečně přihlásí uživatele s pracovním nebo školním účtem Microsoft. Azure AD podporuje vývojáře vytvářející jak obchodní aplikace s jedním tenantem, tak i vývojáře, kteří se zajímají o vyvíjení aplikací s více tenanty. Kromě základního přihlašování umožňuje Azure AD volat nejen rozhraní API Microsoftu, jako je [Microsoft Graph](https://docs.microsoft.com/graph/overview), ale i vlastní rozhraní API vytvořená na platformě Azure AD. Tato dokumentace vám ukáže, jak do vlastní aplikace přidat podporu Azure AD s použitím standardních oborových protokolů, jako jsou OAuth 2.0 a OpenID Connect.

> [!NOTE]
> Většina obsahu na této stránce se zaměřuje na v1.0 koncový bod a platformu, která podporuje jenom Microsoft pracovní nebo školní účty. Pokud chcete k přihlášení uživatelů nebo osobní účty Microsoft, najdete v tématu [koncový bod verze 2.0 a platforma](v2-overview.md). Koncový bod v2.0 nabízí jednotné vývojářské prostředí pro aplikace, které chcete podepsat ve všech identitách Microsoft.

| | |
| --- | --- |
|[Základy ověřování](authentication-scenarios.md) | Úvod k ověřování pomocí Azure AD |
|[Typy aplikací](app-types.md) | Přehled scénářů ověřování, které podporuje Azure AD |
| | |

## <a name="get-started"></a>Začínáme

Tato verze 1.0 šablon rychlý start a kurzy vás provedou vytvořením aplikace na preferované platformě pomocí Azure AD Authentication Library (ADAL) SDK. Najdete v článku **v1.0 rychlých startů** a **v1.0 kurzy** v [platforma identit Microsoft (Azure Active Directory pro vývojáře)](index.yml) začít.

## <a name="how-to-guides"></a>Návody

Zobrazit **v1.0 provede postupy** pro podrobné informace a návody zvládnout běžné úkoly ve službě Azure AD.

## <a name="reference-topics"></a>Referenční témata

Následující články poskytují podrobné informace o rozhraních API, zprávách protokolů a termínech používaných v Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Knihovny ověřování (ADAL)](active-directory-authentication-libraries.md)   | Přehled knihoven a sad SDK, které poskytuje Azure AD |
| [Ukázky kódu](sample-v1-code.md)                                  | Seznam všech ukázek kódu Azure AD |
| [Glosář](developer-glossary.md)                                      | Terminologie a definice slov, která se používají v této dokumentaci |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
