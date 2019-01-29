---
title: Pomocí skupiny pro správu přístupu k aplikacím SaaS | Dokumentace Microsoftu
description: Jak používat skupiny v Azure Active Directory Premium nebo Basic k přiřazení přístupu k aplikacím SaaS, které jsou integrované s Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/14/2017
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: a7e4a20905b390945c22557af044cf739bc17ce8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197207"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Použití skupiny ke správě přístupu k aplikacím SaaS
Pomocí Azure Active Directory (Azure AD) s licencí Azure AD Premium nebo Azure AD Basic, můžete použít skupiny k přiřazení přístupu k aplikaci SaaS, která je integrovaná s Azure AD. Například pokud chcete přiřadit přístup pro marketingové oddělení k použití pěti různým aplikacím SaaS, můžete vytvořit skupinu, která obsahuje uživatelé v marketingovém oddělení a potom přidělit této skupině na těchto pět SaaS aplikací, které jsou vyžadované marketingové oddělení. Tímto způsobem můžete ušetřit čas tím, že spravuje členství v marketingovém oddělení na jednom místě. Uživatelé pak jsou přiřazeni k aplikaci při jsou přidány jako členové skupiny marketing a jejich přiřazení odebrali z aplikace, když jsou odebrány ze skupiny marketing. Tato funkce je možné se stovkami aplikací, které můžete přidat z v rámci Galerie aplikací Azure AD.

> [!IMPORTANT]
> Tato funkce slouží pouze po spuštění zkušební verze Azure AD Premium nebo při nákupu licencí Azure AD Premium nebo Azure AD Basic. Přiřazení na základě skupin se podporuje jenom pro skupiny zabezpečení. Vnořené členství ve skupinách momentálně není podporované v případě přiřazování k aplikacím podle skupiny.

**K přiřazení přístupu pro uživatele nebo skupiny k aplikaci SaaS**

1. V [centrum pro správu Azure AD](https://aad.portal.azure.com)vyberte **podnikové aplikace**.
2. Vyberte, že jste přidali v galerii aplikace ho otevřete.
3. Vyberte **uživatelů a skupin**a pak vyberte **přidat uživatele**.
4. Na **přidat přiřazení**vyberte **uživatelů a skupin** otevřít **uživatelů a skupin** seznamu výběru.
6. Vyberte libovolný počet skupin nebo uživatelů chcete, potom klikněte nebo klepněte na **vyberte** jejich přidání do **přidat přiřazení** seznamu. Roli můžete uživateli přiřadit také v této fázi.
7. Vyberte **přiřadit** přiřazení uživatelů nebo skupin k vybrané podnikové aplikace.

### <a name="next-steps"></a>Další postup
Následující články poskytují další informace o službě Azure Active Directory.

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Správa aplikací ve službě Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](groups-settings-cmdlets.md)
* [Představení služby Azure Active Directory](../fundamentals/active-directory-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
