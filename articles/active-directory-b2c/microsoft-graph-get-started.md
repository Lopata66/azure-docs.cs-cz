---
title: Správa prostředků pomocí Microsoft Graph
titleSuffix: Azure AD B2C
description: Příprava na správu Azure AD B2Cch prostředků pomocí Microsoft Graph registraci aplikace, která má udělena požadovaná oprávnění Graph API.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bcddcf051e73381af35ca40e22c443c7b9ae30a7
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492939"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Správa Azure AD B2C s využitím Microsoft Graph

[Microsoft Graph][ms-graph] vám umožní spravovat spoustu prostředků v rámci vašeho Azure AD B2Cho tenanta, včetně uživatelských účtů zákazníků a vlastních zásad. Při psaní skriptů nebo aplikací, které volají [rozhraní Microsoft Graph API][ms-graph-api], můžete automatizovat úkoly správy klientů, jako například:

* Migrace stávajícího úložiště uživatelů do tenanta Azure AD B2C
* Nasazení vlastních zásad pomocí kanálu Azure v Azure DevOps a Správa klíčů vlastních zásad
* Registrace uživatelů hostitele na vlastní stránce a vytvoření uživatelských účtů v adresáři Azure AD B2C na pozadí
* Automatizace registrace aplikace
* Získat protokoly auditu

Následující části vám pomůžou připravit se na používání rozhraní Microsoft Graph API k automatizaci správy prostředků v adresáři Azure AD B2C.

## <a name="microsoft-graph-api-interaction-modes"></a>Microsoft Graph režimy interakce rozhraní API

Existují dva režimy komunikace, které můžete použít při práci s rozhraním Microsoft Graph API ke správě prostředků ve vašem tenantovi Azure AD B2C:

* **Interaktivní** – vhodný pro úlohy spouštěné po jednom použití účtu správce v tenantovi B2C k provádění úloh správy. Tento režim vyžaduje, aby se správce přihlásil pomocí svých přihlašovacích údajů před voláním rozhraní Microsoft Graph API.

* **Automatizovaná** – pro naplánované nebo průběžné spouštění úloh používá tato metoda účet služby, který nakonfigurujete s oprávněními potřebnými k provádění úloh správy. Účet služby vytvoříte v Azure AD B2C tím, že zaregistrujete aplikaci, kterou vaše aplikace a skripty používají pro ověřování pomocí jejího *ID aplikace (klienta)* a udělení přihlašovacích údajů klienta OAuth 2,0. V tomto případě aplikace funguje stejně jako volání rozhraní API Microsoft Graph, nikoli uživatel s oprávněními správce jako v dříve popsané interaktivní metodě.

Scénář **automatizované** interakce povolíte tak, že vytvoříte registraci aplikace, která je uvedená v následujících částech.

## <a name="register-management-application"></a>Registrovat aplikaci pro správu

Předtím, než můžou skripty a aplikace pracovat s [rozhraním Microsoft Graph API][ms-graph-api] ke správě prostředků Azure AD B2C, je potřeba vytvořit registraci aplikace ve vašem tenantovi Azure AD B2C, která udělí požadovaná oprávnění rozhraní API.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-api-access"></a>Udělení přístupu k rozhraní API

Potom udělte registrovanou aplikaci oprávnění k manipulaci s prostředky klienta prostřednictvím volání rozhraní Microsoft Graph API.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Vytvořit tajný klíč klienta

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Nyní máte aplikaci, která má oprávnění k *vytváření*, *čtení*, *aktualizaci*a *odstraňování* uživatelů ve vašem tenantovi Azure AD B2C. Přejděte k další části a přidejte oprávnění k *aktualizaci hesla* .

## <a name="enable-user-delete-and-password-update"></a>Povolit odstranění a aktualizaci hesla uživatele

Oprávnění *číst a zapisovat data adresáře* **nezahrnují možnost** odstraňovat uživatele nebo aktualizovat hesla uživatelských účtů.

Pokud vaše aplikace nebo skript potřebuje odstranit uživatele nebo aktualizovat hesla, přiřaďte aplikaci roli *Správce uživatelů* :

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a pomocí filtru **adresář + odběr** přepněte na svého tenanta Azure AD B2C.
1. Vyhledejte a vyberte **Azure AD B2C**.
1. V části **Spravovat**vyberte **role a správci**.
1. Vyberte roli **správce uživatele** .
1. Vyberte **Přidat přiřazení**.
1. Do textového pole pro **Výběr** zadejte název aplikace, kterou jste zaregistrovali dříve, například *managementapp1*. Vyberte aplikaci, která se zobrazí ve výsledcích hledání.
1. Vyberte **Přidat**. Aby bylo možné plně šířit oprávnění, může trvat několik minut.

## <a name="next-steps"></a>Další kroky

Teď, když jste zaregistrovali aplikaci pro správu a udělili jí požadovaná oprávnění, můžou vaše aplikace a služby (například Azure Pipelines) používat své přihlašovací údaje a oprávnění k interakci s rozhraním Microsoft Graph API.

* [Operace B2C podporované nástrojem Microsoft Graph](microsoft-graph-operations.md)
* [Správa Azure AD B2C uživatelských účtů pomocí Microsoft Graph](manage-user-accounts-graph-api.md)
* [Získání protokolů auditu pomocí rozhraní API pro vytváření sestav Azure AD](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: https://docs.microsoft.com/graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview
