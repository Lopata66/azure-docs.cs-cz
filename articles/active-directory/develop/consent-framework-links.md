---
title: Jak funguje souhlasu s aplikací | Dokumentace Microsoftu
description: Další informace o fungování rozhraní pro udělování souhlasu Azure AD, pokud chcete zobrazit, jak ho můžete použít při vývoji aplikací v Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 766b7572ed54cc194dc28fce1ad7e4979f1af5a5
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540138"
---
# <a name="how-application-consent-works"></a>Způsob fungování povolení spuštění aplikace

V tomto článku je pomoct vám další informace o fungování rozhraní pro udělování souhlasu Azure AD, takže můžete vyvíjet aplikace efektivněji.

## <a name="recommended-documents"></a>Doporučené dokumenty

- Získat obecné principy systému [jak umožňuje souhlas vlastníka prostředku k řízení přístupu aplikace k prostředkům](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#consent).
- Získejte podrobný přehled o [způsob implementace rozhraní Azure AD pro udělování souhlasu souhlasu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Větší hloubky, přečtěte si [použití rozhraní pro udělování souhlasu víceklientské aplikaci](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) k implementaci "user" a "admin" souhlas, podpora více pokročilé modely vícevrstvých aplikací.
- Větší hloubky, přečtěte si [jak souhlasu se podporuje ve vrstvě protokolu OAuth 2.0 během toku přidělení kódu autorizace.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)

## <a name="next-steps"></a>Další postup
[StackOverflow pro Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
