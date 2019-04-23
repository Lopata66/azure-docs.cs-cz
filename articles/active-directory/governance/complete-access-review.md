---
title: Dokončení kontroly přístupu skupiny nebo aplikace – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak dokončení kontroly přístupu členů skupiny nebo přístupu k aplikacím v kontrol přístupu Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/02/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4265a7e08eab079e55ce91b27142ec3e55b3f3e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60246475"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Dokončení kontroly přístupu skupin nebo kontrol přístupu aplikací ve službě Azure AD

Správci můžou pomocí Azure Active Directory (Azure AD) [vytvořit kontrolu přístupu](create-access-review.md) pro členy skupiny nebo uživatele přiřazené k aplikaci. Azure AD automaticky pošle e-mail s výzvou ke kontrole přístupu revidující. Pokud uživatel e-mail neobdrželi, můžete jim poslat pokyny [kontrolovat přístup skupinám nebo aplikacím](perform-access-review.md). (Všimněte si, že hosté, kteří jsou přiřazeny jako revidující, ale nepřijali pozvánku nebude dostávat e-mailu z kontroly přístupu, jak se musí nejdřív přijmout pozvánku před revizí.) Po období kontroly přístupu nebo zastaví kontrolu přístupu správce, postupujte podle kroků v tomto článku najdete v článku a použít výsledky.

## <a name="view-an-access-review-in-the-azure-portal"></a>Zobrazit kontroly přístupu na webu Azure Portal

1. Přejděte [stránku kontrol přístupu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)vyberte **programy**a vyberte program, který obsahuje ovládací prvek kontroly přístupu.

2. Vyberte **spravovat**a vyberte ovládací prvek kontroly přístupu. Pokud v programu existuje mnoho ovládacích prvků, můžete vyfiltrovat ovládací prvky konkrétního typu a seřadit je podle jejich stavu. Můžete také hledat podle názvu ovládacího prvku kontroly přístupu nebo zobrazovaného názvu vlastníka, který ho vytvořil. 

## <a name="stop-a-review-that-hasnt-finished"></a>Zastavte kontrolu, která nebyla dokončena

Pokud kontrola nedosáhla naplánované koncové datum, můžete vybrat správce **Zastavit** ukončit revizi již v rané fázi. Po zastavení revize můžete zkontrolovat už uživatelé. Kontrola nelze restartovat po je zastaven.

## <a name="apply-the-changes"></a>Použít změny 

Po dokončení kontroly přístupu, buď protože se dosáhlo koncové datum nebo správce ručně zastavena a automaticky použít nebyl nakonfigurován k revizi, můžete vybrat **použít** ručně změny se projeví. Výsledek kontroly je implementováno aktualizuje skupinu nebo aplikaci. Pokud uživatele přístup byl odepřen v revizi, pokud správce vybere tato možnost, Azure AD se odeberou jejich přiřazení členství nebo aplikace. 

Po dokončení kontroly přístupu a automaticky použít byl nakonfigurován, pak se změní z dokončeno prostřednictvím průběžných stavů stav kontroly a nakonec se změní na stav použito. Zobrazení odepřených uživatelů, měli byste očekávat, pokud existuje, se odebere z prostředku skupiny členství a přiřazení aplikací během několika minut.

Nakonfigurované automatické použití kontroly, nebo jeho výběru **použít** nemá vliv na skupinu, která pochází z místního adresáře nebo dynamické skupiny. Pokud chcete změnit skupinu, která mohou být místní, stáhněte si výsledky a tyto změny použít k reprezentaci dané skupiny v tomto adresáři.

## <a name="download-the-results-of-the-review"></a>Stáhnout výsledky kontroly

Chcete-li načíst výsledky kontroly, vyberte **schválení** a pak vyberte **Stáhnout**. Výsledný soubor CSV lze je zobrazit v Excelu nebo v jiných aplikacích, které se otevřou UTF-8 kódování souborů CSV.

## <a name="optional-delete-a-review"></a>Volitelné: Odstranit kontrolu
Pokud už máte zájem revizi, můžete ho odstranit. Vyberte **odstranit** odebrat revizi ze služby Azure AD.

> [!IMPORTANT]
> Neexistuje žádná upozornění, než dojde k odstranění, proto buďte Opravdu chcete odstranit revizi.
> 
> 

## <a name="next-steps"></a>Další postup

- [Správa přístupu uživatelů pomocí kontrol přístupu Azure AD](manage-user-access-with-access-reviews.md)
- [Správa přístupu uživatelů typu host pomocí kontrol přístupu Azure AD](manage-guest-access-with-access-reviews.md)
- [Správa programů a ovládacích prvků pro kontroly přístupu Azure AD](manage-programs-controls.md)
- [Vytvoření kontroly přístupu skupiny nebo aplikace](create-access-review.md)
- [Vytváření kontroly přístupu uživatelů v roli správce Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
