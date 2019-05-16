---
title: Programové vytváření předplatných Azure Enterprise | Dokumentace Microsoftu
description: Zjistěte, jak programově vytvářet další předplatná Azure Enterprise nebo Enterprise pro vývoj/testování.
services: azure-resource-manager
author: jureid
manager: jureid
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: 7985451eb2bb5e9fd4fbcfb3d2fcf35149122c15
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796065"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Programové vytváření předplatných Azure Enterprise (preview)

Jako zákazník Azure na [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), EA (MS-AZR - 0017 P) a předplatných EA pro vývoj/testování (MS-AZR - 0148 P) můžete vytvořit prostřednictvím kódu programu. V tomto článku se dozvíte, jak vytvářet předplatná prostřednictvím kódu programu pomocí Azure Resource Manageru.

Při vytváření předplatného služby Azure z tohoto rozhraní API, toto předplatné se řídí Smlouvou, pod kterým jste získali služby Microsoft Azure od společnosti Microsoft nebo autorizovaného prodejce. Další informace najdete v tématu [právní informace týkající se Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Chcete vytvářet předplatná v rámci registrace účtu musí mít roli vlastníka. Existují dva způsoby, jak získat tyto role:

* Správce registrace můžete [můžete nastavit jako vlastníka účtu](https://ea.azure.com/helpdocs/addNewAccount) (vyžadováno přihlášení) který vám umožňuje být vlastníkem účtu registrace. Postupujte podle pokynů v e-mailové pozvánce, že abyste dostávali ruční vytvoření počátečního předplatného. Potvrdit vlastnictví účtu a ruční vytvoření počátečního předplatného EA, než budete pokračovat k dalšímu kroku. Stačí přidat účet pro přihlášení není k dispozici dostatek.

* Můžete stávající vlastník účtu registrace [udělení přístupu k](grant-access-to-create-subscription.md). Podobně, pokud chcete použít k vytvoření předplatného EA instanční objekt služby, musíte [udělit takového objektu služby možnost vytvářet předplatná](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Najít účty, ke kterým máte přístup k

Po přidání na registraci smlouvy Azure EA jako vlastník účtu Azure používá k určení, kam účtovat poplatky za odběr vztah registrace účtu. Všechna předplatná vytvořená v rámci účtu se účtují směrem k registraci smlouvy Enterprise, který obsahuje příslušný účet. Vytvářet předplatná, musíte předat hodnoty o registraci účtu a objekty zabezpečení uživatelů k vlastní předplatné. 

Pokud chcete spustit následující příkazy, musíte být přihlášení k majiteli účtu *domovský adresář*, což je adresář, který odběry vytvářejí ve výchozím nastavení.

## <a name="resttabrest"></a>[REST](#tab/rest)

Požadavek na výpis všech registračních účtů, ke kterým máte přístup k:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure jako odpověď vrátí seznam všech registračních účtů, ke kterým máte přístup k:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

Použití `principalName` vlastnost k identifikaci účtu, který má předplatné účtovat na. Kopírovat `name` tohoto účtu. Například, pokud chcete vytvářet předplatná v rámci SignUpEngineering@contoso.com účet pro zápis, budou zkopírovány ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. To je ID objektu registrace účtu. Vložte tuto hodnotu někde, takže ho můžete použít v dalším kroku jako `enrollmentAccountObjectId`.

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Otevřít [Azure Cloud Shell](https://shell.azure.com/) a vyberte prostředí PowerShell.

Použití [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) rutiny pro zobrazení seznamu všechny registračních účtů, kterým máte přístup.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure jako odpověď vrátí seznam registračních účtů, ke kterým máte přístup k:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Použití `principalName` vlastnost k identifikaci účtu, který má předplatné účtovat na. Kopírovat `ObjectId` tohoto účtu. Například, pokud chcete vytvářet předplatná v rámci SignUpEngineering@contoso.com účet pro zápis, budou zkopírovány ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Vložte toto ID objektu někde, takže ho můžete použít v dalším kroku jako `enrollmentAccountObjectId`.

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Použití [az fakturační účet registrace seznamu](https://aka.ms/EASubCreationPublicPreviewCLI) seznam všech registračních účtů, máte přístup k příkazu.

```azurecli-interactive 
az billing enrollment-account list
```

Azure jako odpověď vrátí seznam registračních účtů, ke kterým máte přístup k:

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

Použití `principalName` vlastnost k identifikaci účtu, který má předplatné účtovat na. Kopírovat `name` tohoto účtu. Například, pokud chcete vytvářet předplatná v rámci SignUpEngineering@contoso.com účet pro zápis, budou zkopírovány ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. To je ID objektu registrace účtu. Vložte tuto hodnotu někde, takže ho můžete použít v dalším kroku jako `enrollmentAccountObjectId`.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Vytvářet předplatná v rámci konkrétní registraci účtu

Následující příklad vytvoří odběr s názvem *Dev týmového odběru* v registraci účtu vybrána v předchozím kroku. Nabídka předplatného je *MS-AZR - 0017P* (pravidelných Microsoft Enterprise Agreement). Také v případě potřeby přidá dva uživatele jako vlastníky RBAC pro předplatné.

# <a name="resttabrest"></a>[REST](#tab/rest)

Ujistěte se, následující žádosti, přičemž nahraďte `<enrollmentAccountObjectId>` s `name` zkopírována z první krok (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Pokud chcete zadat vlastníky, přečtěte si [jak získat objekt user ID](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Název elementu  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ne      | String | Zobrazovaný název předplatného. Pokud není zadán, je nastavena na název nabídky, jako je "Microsoft Azure Enterprise."                                 |
| `offerType`   | Ano      | String | Nabídka předplatného. Jsou dvě možnosti pro EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (použití v produkčním prostředí) a [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (pro vývoj/testování, musí být [zapnuté na portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Ne       | String | ID objektu každý uživatel, který chcete přidat jako vlastníka předplatného RBAC při jeho vytvoření.  |

V odpovědi, které získáte zpět `subscriptionOperation` objekt monitorování. Po dokončení vytvoření předplatného `subscriptionOperation` vrátí objekt `subscriptionLink` objektu, který má ID předplatného.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Nejdřív nainstalujte tento modul ve verzi preview spuštěním `Install-Module Az.Subscription -AllowPrerelease`. Aby se zajistilo `-AllowPrerelease` funguje, nainstalujte nejnovější verzi modulu PowerShellGet z [získat modul PowerShellGet](/powershell/gallery/installing-psget).

Spustit [New-AzSubscription](/powershell/module/az.subscription) příkaz nahrazujte `<enrollmentAccountObjectId>` s `ObjectId` shromážděných v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Pokud chcete zadat vlastníky, přečtěte si [jak získat objekt user ID](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Název elementu  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Ne      | String | Zobrazovaný název předplatného. Pokud není zadán, je nastavena na název nabídky, jako je "Microsoft Azure Enterprise."                                 |
| `OfferType`   | Ano      | String | Nabídka předplatného. Jsou dvě možnosti pro EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (použití v produkčním prostředí) a [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (pro vývoj/testování, musí být [zapnuté na portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Ano       | String | Účet pro zápis, že je předplatné vytvořené v rámci a účtuje na ID objektu. Tato hodnota je identifikátor GUID, který obdržíte od `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Ne       | String | ID objektu každý uživatel, který chcete přidat jako vlastníka předplatného RBAC při jeho vytvoření.  |
| `OwnerSignInName`    | Ne       | String | E-mailová adresa každý uživatel, který chcete přidat jako vlastníka předplatného RBAC při jeho vytvoření. Můžete použít tento parametr místo `OwnerObjectId`.|
| `OwnerApplicationId` | Ne       | String | ID aplikace všechny instanční objekt, který chcete přidat jako vlastníka předplatného RBAC při jeho vytvoření. Můžete použít tento parametr místo `OwnerObjectId`. Při použití tohoto parametru, musí mít instanční objekt služby [přístup pro čtení k adresáři](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Úplný seznam všech parametrů najdete v tématu [New-AzSubscription](/powershell/module/az.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Toto rozšíření ve verzi preview nejprve nainstalovat spuštěním `az extension add --name subscription`.

Spustit [vytvořit účet az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) příkaz nahrazujte `<enrollmentAccountObjectId>` s `name` jste zkopírovali v prvním kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Pokud chcete zadat vlastníky, přečtěte si [jak získat objekt user ID](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Název elementu  | Požaduje se | Typ   | Popis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Ne      | String | Zobrazovaný název předplatného. Pokud není zadán, je nastavena na název nabídky, jako je "Microsoft Azure Enterprise."                                 |
| `offer-type`   | Ano      | String | Nabídka předplatného. Jsou dvě možnosti pro EA [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (použití v produkčním prostředí) a [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (pro vývoj/testování, musí být [zapnuté na portálu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Ano       | String | Účet pro zápis, že je předplatné vytvořené v rámci a účtuje na ID objektu. Tato hodnota je identifikátor GUID, který obdržíte od `az billing enrollment-account list`. |
| `owner-object-id`      | Ne       | String | ID objektu každý uživatel, který chcete přidat jako vlastníka předplatného RBAC při jeho vytvoření.  |
| `owner-upn`    | Ne       | String | E-mailová adresa každý uživatel, který chcete přidat jako vlastníka předplatného RBAC při jeho vytvoření. Můžete použít tento parametr místo `owner-object-id`.|
| `owner-spn` | Ne       | String | ID aplikace všechny instanční objekt, který chcete přidat jako vlastníka předplatného RBAC při jeho vytvoření. Můžete použít tento parametr místo `owner-object-id`. Při použití tohoto parametru, musí mít instanční objekt služby [přístup pro čtení k adresáři](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Úplný seznam všech parametrů najdete v tématu [vytvořit účet az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Omezení vytváření předplatného Azure Enterprise API

- Pouze Azure Enterprise odběry můžete vytvořit pomocí tohoto rozhraní API.
- Je počáteční omezený na 50 předplatných na jeden účet pro zápis, ale můžete [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) o zvýšení limitu na 200. Potom můžete odběry vytvořit pouze prostřednictvím centra účtů.
- Musí existovat alespoň jeden EA nebo EA pro vývoj/testování předplatných v rámci účtu, což znamená, že vlastník účtu má prošli ruční registraci alespoň jednou.
- Uživatelé, kteří nejsou vlastníky účtů, ale byly přidány do registrace účtu prostřednictvím RBAC, nelze vytvářet odběry pomocí centra pro účty.
- Nelze vybrat tenanta pro předplatné má být vytvořen v. Předplatné je vytvořen vždy v domovském tenantovi vlastníka účtu. Chcete-li přesunout předplatné na jiného tenanta, naleznete v tématu [změnit předplatného tenanta](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Další postup

* Příklad vytvoření předplatného pomocí rozhraní .NET, naleznete v tématu [ukázkový kód na Githubu](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Teď, když vytvoříte odběr, můžete udělit tuto možnost pro ostatní uživatele a instančních objektů. Další informace najdete v tématu [udělit přístup k vytvoření předplatného Azure Enterprise (preview)](grant-access-to-create-subscription.md).
* Další informace o správě velkého počtu předplatných pomocí skupin pro správu najdete v tématu [uspořádání prostředků se skupinami pro správu Azure](management-groups-overview.md)
