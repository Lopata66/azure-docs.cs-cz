---
title: Doprovodné materiály k licencování pro spolupráci B2B – Azure Active Directory | Microsoft Docs
description: Azure Active Directory spolupráce B2B nevyžaduje placené licence Azure AD, ale můžete také získat placené funkce pro uživatele typu Host B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 081061eae07fa3765d032ad155e59ebf5aa3cbc9
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512554"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Doprovodné materiály k licencování spolupráce Azure Active Directory s B2B

Díky spolupráci B2B (Business-to-Business) Azure Active Directory (Azure AD) můžete pozvat externí uživatele (neboli uživatele typu Host), aby mohli používat placené služby Azure AD. Některé funkce jsou bezplatné, ale u všech placených funkcí Azure AD můžete pozvat až pět uživatelů typu Host pro každou licenci Azure AD, kterou vlastníte pro zaměstnance nebo uživatele, který není Host, ve vašem tenantovi.

> [!NOTE]
> Podrobnosti o cenách služby Azure AD a funkcích spolupráce B2B najdete v tématu [Azure Active Directory ceny](https://azure.microsoft.com/pricing/details/active-directory/) .

Licence uživatelů hosta B2B se automaticky vypočítávají a nahlásí na základě poměru 1:5. V současné době není možné přiřadit uživatelské licence B2B hosta přímo uživatelům typu Host.

Uživatelé typu Host můžou taky používat bezplatné funkce Azure AD bez dalších licenčních požadavků. Uživatelé typu Host mají přístup k bezplatným funkcím služby Azure AD, a to i v případě, že nemáte žádné placené licence Azure AD. 

## <a name="examples-calculating-guest-user-licenses"></a>Příklady: Výpočet uživatelských licencí typu Host
Jakmile určíte, kolik uživatelů typu Host potřebuje získat přístup k placeným službám Azure AD, ujistěte se, že máte dostatek placené licence Azure AD, aby pokryly uživatele typu Host v požadovaném poměru 1:5. Následuje několik příkladů:

- Chcete pozvat 100 uživatelů typu host na vaše aplikace nebo služby Azure AD a chcete přiřadit správu přístupu a zřizování všem uživatelům typu Host. Také budete chtít vyžadovat MFA a podmíněný přístup pro 50 těchto uživatelů typu Host. K pokrytí této kombinace budete potřebovat 10 licencí Azure AD Basic a licence 10 Azure AD Premium P1. Pokud plánujete použití funkcí ochrany identit u uživatelů typu Host, budete potřebovat Azure AD Premium licence P2 ve stejném poměru 1:5, aby pokryly uživatele typu Host.
- Chcete pozvat 60 uživatelů typu Host, kteří požadují MFA, takže musíte mít minimálně 12 Azure AD Premium licencí P1. Máte 10 zaměstnanců s licencemi Azure AD Premium P1, což by umožnilo 50 uživatelům typu Host v rámci licenčního poměru 1:5. Abyste pokryli 10 dalších uživatelů typu Host, budete si muset koupit dvě další licence Premium P1.

## <a name="next-steps"></a>Další kroky

Podívejte se na následující zdroje informací o spolupráci Azure AD B2B:

* [Ceny Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Nejčastější dotazy týkající se spolupráce Azure Active Directory s B2B](faq.md)
