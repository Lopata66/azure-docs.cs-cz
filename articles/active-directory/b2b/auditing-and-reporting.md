---
title: Auditování a vytváření sestav uživatele spolupráce B2B – Azure Active Directory | Dokumentace Microsoftu
description: Vlastnosti uživatele typu Host se dají konfigurovat v spolupráce Azure Active Directory s B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5763a7e5f122702ddaf86246fbfbd18326878146
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60357158"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Auditování a vytváření sestav uživatele spolupráce B2B
Uživatelé typu Host máte podobné možnosti auditování s využitím členské uživatele. 

## <a name="access-reviews"></a>Kontroly přístupu
Kontroly přístupu můžete pravidelně ověřovat, jestli uživatelé typu Host stále potřebují přístup k vašim prostředkům. **Kontrol přístupu** funkce je dostupná v **Azure Active Directory** pod **spravovat** > **organizační vztahy**. ("Kontroly přístupu" můžete vyhledat taky z **všechny služby** na webu Azure Portal.) Další informace o použití kontroly přístupu, najdete v článku [kontroly přístupu hostů spravovat pomocí služby Azure AD access](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Protokoly auditu

Auditování Azure AD protokoly poskytuje záznamy systémových a uživatelských aktivit, včetně aktivit iniciovaných uživatele typu Host. Pro přístup k protokolům auditu v **Azure Active Directory**v části **monitorování**vyberte **protokoly auditu**. Tady je příklad historie pozvánku a uplatnění pozvaného Sam Oogle:

![Snímek obrazovky zobrazující a příklad auditu protokolu výstupu](./media/auditing-and-reporting/audit-log.png)

Můžete věnovat každá z těchto událostí, který obsahuje podrobné informace. Například Pojďme se podívat na podrobnosti přijetí.

![Snímek obrazovky zobrazující a příklad výstupu podrobnosti aktivity](./media/auditing-and-reporting/activity-details.png)

Můžete také exportovat tyto protokoly z Azure AD a získání přizpůsobených sestav pomocí vytváření sestav nástroje podle vašeho výběru.

### <a name="next-steps"></a>Další postup

- [Vlastnosti uživatele spolupráce B2B](user-properties.md)

