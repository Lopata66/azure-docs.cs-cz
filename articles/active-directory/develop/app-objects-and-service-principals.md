---
title: Aplikace a instanční objekty v Azure Active Directory
description: Další informace o vztah mezi aplikací a instanční objekty v Azure Active Directory.
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
services: active-directory
editor: ''
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2019
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b7ab4007a94efbc48bfea67c6b954c02de0b2ba
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887433"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Aplikace a instanční objekty v Azure Active Directory

V některých případech význam "aplikace" může být nesprávně pochopeny, při použití v rámci služby Azure Active Directory (Azure AD). Tento článek vysvětluje koncepční a konkrétní aspekty integraci aplikací Azure AD, pomocí registrace a vyjádření souhlasu pro ilustraci [aplikace s více tenanty](developer-glossary.md#multi-tenant-application).

## <a name="overview"></a>Přehled

Aplikace integrovaná s Azure AD má důsledky, které přesahují aspekt softwaru. "Aplikace" se často používá jako konceptuální termín, odkazující na nejen aplikačního softwaru, ale také jeho registrace služby Azure AD a role v ověřování/autorizace "konverzace" za běhu.

Podle definice může aplikace fungovat v těchto rolích:

- [Klient](developer-glossary.md#client-application) role (využívání prostředku)
- [Server prostředků](developer-glossary.md#resource-server) role (vystavení rozhraní API pro klienty)
- Klientská role a role serveru prostředků

[Toku udělení autorizace OAuth 2.0](developer-glossary.md#authorization-grant) definuje konverzace protokol, který umožňuje klienta nebo prostředek, který má přístup/ochranu zdroje dat, v uvedeném pořadí.

V následujících částech uvidíte, jak představuje aplikačního modelu služby Azure AD aplikace v době návrhu a za běhu.

## <a name="application-registration"></a>Registrace aplikací

Když se zaregistrujete aplikaci Azure AD v [webu Azure portal][AZURE-Portal], jsou vytvořeny dva objekty ve vašem tenantovi Azure AD:

- Objekt aplikace a
- Instanční objekt

### <a name="application-object"></a>Objekt aplikace

Aplikaci Azure AD je definován tak, že je jeden a pouze aplikační objekt, který je umístěný v tenantovi Azure AD, kde byla aplikace zaregistrované, známé jako "domovskou" tenant vaší aplikace. Azure AD Graph [aplikace entity] [ AAD-Graph-App-Entity] definuje schéma pro objekt aplikace vlastnosti.

### <a name="service-principal-object"></a>instanční objekt

Pro přístup k prostředkům, které jsou zabezpečené pomocí tenanta služby Azure AD, musí být reprezentována entity, která vyžaduje přístup k objektu zabezpečení. To platí pro uživatele (uživatel instančního objektu) a aplikací (instanční objekt).

Objekt zabezpečení definuje zásady přístupu a oprávnění pro uživatele nebo aplikaci v tenantovi Azure AD. To umožňuje základní funkce, jako je ověřování uživatelů a aplikací při přihlášení a ověření při přístupu k prostředkům.

Když aplikaci uděleno oprávnění pro přístup k prostředkům v tenantovi (při registraci nebo [souhlas](developer-glossary.md#consent)), se vytvoří instanční objekt. Azure AD Graph [ServicePrincipal entity] [ AAD-Graph-Sp-Entity] definuje schéma pro instanční objekt pro vlastnosti.

### <a name="application-and-service-principal-relationship"></a>Aplikace a relace instančního objektu služby

Vezměte v úvahu objekt aplikace jako *globální* reprezentace vaší aplikace pro použití ve všech tenantů a instanční objekt služby, jako *místní* reprezentaci pro použití v konkrétního tenanta.

Objekt slouží aplikace jako šablony, ze které běžné a výchozí vlastnosti jsou *odvozené* pro použití při vytváření odpovídající instanční objekty. Objekt aplikace proto má vztah 1:1 s softwarové aplikace a 1:many vztahy s jeho odpovídající objekty instančního objektu služby.

Hlavní název služby musí být vytvořeny v každého klienta, kde se aplikace používá, bude moct vytvořit identitu pro přihlášení a/nebo přístup k prostředkům, které se zabezpečují prostřednictvím klienta. Aplikace jedním tenantem má pouze jeden instanční objekt (v domovském tenantovi), vytvořit a vyjádření souhlasu pro použití při registraci aplikace. Více tenanty webová aplikace/rozhraní API má také objekt služby, která je vytvořená v rámci každého tenanta, kde uživatele z tohoto tenanta vyjádřil souhlas se jeho použití. 

> [!NOTE]
> Všechny změny provedené na váš objekt aplikace se také projeví v jeho instanční objekt v domovském tenantovi aplikace pouze (tenanta, ve kterém byl zaregistrován). Pro víceklientské aplikace, změny pro daný aplikační objekt se neprojeví v žádné klienty příjemce instanční objekty, dokud neodebere přístup prostřednictvím [přístupového panelu aplikací](https://myapps.microsoft.com) a udělit znovu.
>
> Všimněte si také, že je ve výchozím nastavení nativní aplikace zaregistrovaní jako více tenantů.

## <a name="example"></a>Příklad:

Následující diagram znázorňuje vztah mezi objekt aplikace a instanční objekty v rámci aplikace s více tenanty ukázka volat odpovídající služby aplikací **aplikací HR**. V tomto ukázkovém scénáři existují tři klienty Azure AD:

- **Adatum** -tenanta používaných ve společnosti, který vyvinul **aplikací HR**
- **Contoso** -tenanta používá organizace Contoso, což je příjemce **aplikací HR**
- **Společnost Fabrikam** -tenanta, používá společnost Fabrikam organizace, které také využívá **aplikací HR**

![Vztah mezi objektu aplikační a instanční objekt](./media/app-objects-and-service-principals/application-objects-relationship.png)

V tomto ukázkovém scénáři:

| Krok | Popis |
|------|-------------|
| 1    | Je proces vytváření aplikace a instanční objekty v domovském tenantovi aplikace. |
| 2    | Při dokončení souhlas správce společnosti Contoso a Fabrikam instanční objekt se v tenantovi Azure AD vaší společnosti a přiřadit oprávnění, které poskytuje správce. Všimněte si také, že aplikace HR může být nakonfigurovaný/umožňují souhlasu uživatelem pro individuální použití. |
| 3    | Tenanti příjemce HR aplikace (Contoso a Fabrikam) každý mají své vlastní instanční objekt. Každý představuje odsouhlasený. jejich použití instance aplikace za běhu, řídit oprávnění podle příslušného správce. |

## <a name="next-steps"></a>Další postup

- Můžete použít [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) zadávat dotazy aplikaci a instanční objekty.
- Objekt aplikace aplikace pomocí Azure AD Graph API, dostanete [webu Azure portal] [ AZURE-Portal] editoru manifestu aplikace, nebo [rutin Powershellu pro Azure AD](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), reprezentovaná jeho OData [aplikace entity][AAD-Graph-App-Entity].
- Instanční objekt aplikace přístupné prostřednictvím rozhraní Azure AD Graph API nebo [rutin Powershellu pro Azure AD](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), jak je reprezentován jeho OData [ServicePrincipal entity] [ AAD-Graph-Sp-Entity].

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[AAD-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
