---
title: Ověřování pro Azure Key Vault
description: Zjistěte, jak ověřit a Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 08/27/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: d247e657e93afd0c43ecee1154c542398304d8dd
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89481371"
---
# <a name="authenticate-to-azure-key-vault"></a>Ověřování pro Azure Key Vault

Azure Key Vault umožňuje ukládat tajné klíče a řídit jejich distribuci v centralizovaném, zabezpečeném cloudovém úložišti, což eliminuje nutnost ukládat přihlašovací údaje v aplikacích. Pro přístup k těmto tajným klíčům se aplikace potřebují ověřit pouze pomocí Key Vault za běhu.

## <a name="app-identity-and-security-principals"></a>Identita aplikace a objekty zabezpečení

Ověřování pomocí Key Vault funguje ve spojení s [Azure Active Directory (Azure AD)](/azure/active-directory/fundamentals/active-directory-whatis), která zodpovídá za ověřování identity libovolného **objektu zabezpečení**.

Objekt zabezpečení je objekt, který představuje uživatele, skupinu, službu nebo aplikaci požadující přístup k prostředkům Azure. Azure přiřadí jedinečné **ID objektu** každému objektu zabezpečení.

* Objekt zabezpečení **uživatele** identifikuje jednotlivce, který má profil v Azure Active Directory.

* Objekt zabezpečení **skupiny** identifikuje sadu uživatelů vytvořených v Azure Active Directory. Všechny role nebo oprávnění přiřazených ke skupině jsou udělena všem uživatelům v rámci dané skupiny.

* **Instanční objekt** je typ instančního objektu, který slouží k identitě aplikace nebo služby, což znamená, že místo uživatele nebo skupiny říkáte část kódu. ID objektu instančního objektu se označuje jako jeho **ID klienta** a funguje jako jeho uživatelské jméno. **Tajný kód klienta** instančního objektu funguje jako jeho heslo.

Pro aplikace existují dva způsoby, jak získat instanční objekt:

* Doporučené: pro aplikaci povolte **spravovanou identitu** přiřazenou systémem.

    Díky spravované identitě Azure interně spravuje instanční objekt aplikace a automaticky ověřuje aplikaci s ostatními službami Azure. Spravovaná identita je k dispozici pro aplikace nasazené do různých služeb.

    Další informace najdete v tématu [Přehled spravované identity](/azure/active-directory/managed-identities-azure-resources/overview). Podívejte se také na [služby Azure, které podporují spravovanou identitu](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities), které odkazují na články, které popisují, jak povolit spravovanou identitu pro konkrétní služby (například App Service, Azure Functions, Virtual Machines atd.).

* Pokud nemůžete použít spravovanou identitu, můžete místo toho aplikaci **zaregistrovat** u svého TENANTA Azure AD, jak je popsáno v tématu [rychlý Start: registrace aplikace s platformou identity Azure](/azure/active-directory/develop/quickstart-register-app). Registrace vytvoří také druhý objekt aplikace, který identifikuje aplikaci napříč všemi klienty.

## <a name="authorize-a-security-principal-to-access-key-vault"></a>Autorizovat objekt zabezpečení pro přístup k Key Vault

Key Vault pracuje se dvěma různými úrovněmi autorizace:

- **Zásady přístupu** určují, jestli uživatel, skupina nebo instanční objekt mají autorizaci pro přístup k tajným klíčům, klíčům a certifikátům *v rámci* existujícího prostředku Key Vault (někdy se označuje jako "operace roviny dat"). Zásady přístupu jsou obvykle udělovány uživatelům, skupinám a aplikacím.

    Pokud chcete přiřadit zásady přístupu, přečtěte si následující články:

    - [Azure Portal](assign-access-policy-portal.md)
    - [Azure CLI](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- **Oprávnění role** určují, jestli uživatel, skupina nebo instanční objekt mají autorizaci k vytváření, odstraňování a jiné správě Key Vault prostředku (někdy označovaného jako "rovina správy"). Tyto role jsou nejčastěji uděleny jenom správcům.
 
    Chcete-li přiřadit a spravovat role, přečtěte si následující články:

    - [Azure Portal](/azure/role-based-access-control/role-assignments-portal)
    - [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
    - [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell)

    Key Vault aktuálně podporuje roli [Přispěvatel](/azure/role-based-access-control/built-in-roles#key-vault-contributor) , která umožňuje operace správy u prostředků Key Vault. Řada dalších rolí je aktuálně ve verzi Preview. Můžete také vytvořit vlastní role, jak je popsáno v tématu [vlastní role Azure](/azure/role-based-access-control/custom-roles).

    Obecné informace o rolích najdete v tématu [co je Azure založené na rolích Access Control (RBAC)?](/azure/role-based-access-control/overview).


> [!IMPORTANT]
> Pro nejvyšší zabezpečení vždy sledujte základní objekty s nejnižšími oprávněními a udělte pouze ty nejdůležitější zásady přístupu a role, které jsou nezbytné. 
    
## <a name="configure-the-key-vault-firewall"></a>Konfigurace brány Key Vault firewall

Ve výchozím nastavení Key Vault umožňuje přístup k prostředkům prostřednictvím veřejných IP adres. Pro zajištění vyššího zabezpečení můžete také omezit přístup ke konkrétním rozsahům IP adres, koncovým bodům služby, virtuálním sítím nebo soukromým koncovým bodům.

Další informace najdete v tématu [přístup Azure Key Vault za bránou firewall](/azure/key-vault/general/access-behind-firewall).


## <a name="the-key-vault-authentication-flow"></a>Key Vault tok ověřování

1. Instanční objekt požaduje ověření ve službě Azure AD, například:
    * Uživatel se do Azure Portal přihlásí pomocí uživatelského jména a hesla.
    * Aplikace vyvolá Azure REST API, který prezentuje ID klienta a tajný klíč nebo klientský certifikát.
    * Prostředek Azure, jako je třeba virtuální počítač se spravovanou identitou, kontaktuje koncový bod [azure instance metadata Service (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) REST, který získá přístupový token.

1. Pokud je ověřování pomocí služby Azure AD úspěšné, instančnímu objektu se udělí token OAuth.

1. Instanční objekt provede volání Key Vault REST API prostřednictvím koncového bodu Key Vault (URI).

1. Brána firewall Key Vault kontroluje následující kritéria. Pokud je splněno jakékoli kritérium, volání je povoleno. V opačném případě se volání zablokuje a vrátí se zakázaná odpověď.

    * Brána firewall je zakázaná a veřejný koncový bod Key Vault dosažitelný z veřejného Internetu.
    * Volající je [Key Vault Důvěryhodná služba](/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services), která umožňuje obejít bránu firewall.
    * Volající je uveden v bráně firewall podle IP adresy, virtuální sítě nebo koncového bodu služby.
    * Volající může kontaktovat Key Vault přes nakonfigurované připojení pomocí privátního propojení.    

1. Pokud brána firewall povoluje volání, Key Vault volá službu Azure AD za účelem ověření přístupového tokenu objektu služby.

1. Key Vault zkontroluje, jestli má instanční objekt pro požadovanou operaci zásadu přístupu. V takovém případě Key Vault vrátí zakázanou odpověď.

1. Key Vault provede požadovanou operaci a vrátí výsledek.

Následující obrázek znázorňuje proces pro aplikaci, která volá rozhraní API "Get tajného klíče" Key Vault:

![Azure Key Vault tok ověřování](../media/authentication/authentication-flow.png)

## <a name="code-examples"></a>Příklady kódu

Následující tabulka obsahuje odkazy na různé články, které ukazují, jak pracovat s Key Vault v kódu aplikace pomocí knihoven sady Azure SDK pro daný jazyk. Pro usnadnění jsou k dispozici další rozhraní, jako je Azure CLI a Azure Portal.

| Key Vault tajných klíčů | Key Vault klíče | Key Vault certifikátů |
|  --- | --- | --- |
| [Python](/azure/key-vault/secrets/quick-create-python) | [Python](/azure/key-vault/keys/quick-create-python) | [Python](/azure/key-vault/certificates/quick-create-python) | 
| [.NET (SDK v4)](/azure/key-vault/secrets/quick-create-net) | -- | -- |
| [.NET (sada SDK V3)](/azure/key-vault/secrets/quick-create-net-v3) | -- | -- |
| [Java](/azure/key-vault/secrets/quick-create-java) | -- | -- |
| [JavaScript](/azure/key-vault/secrets/quick-create-node) | -- | -- | 
| | | |
| [Azure Portal](/azure/key-vault/secrets/quick-create-portal) | [Azure Portal](/azure/key-vault/keys/quick-create-portal) | [Azure Portal](/azure/key-vault/certificates/quick-create-portal) |
| [Azure CLI](/azure/key-vault/secrets/quick-create-cli) | [Azure CLI](/azure/key-vault/keys/quick-create-cli) | [Azure CLI](/azure/key-vault/certificates/quick-create-cli) |
| [Azure PowerShell](/azure/key-vault/secrets/quick-create-powershell) | [Azure PowerShell](/azure/key-vault/keys/quick-create-powershell) | [Azure PowerShell](/azure/key-vault/certificates/quick-create-powershell) |
| [Šablona ARM](/azure/key-vault/secrets/quick-create-net) | -- | -- |

## <a name="next-steps"></a>Další kroky

- [Řešení potíží se zásadami přístupu Key Vault](troubleshooting-access-issues.md)
- [Kódy chyb Key Vault REST API](rest-error-codes.md)
- [Key Vault příručka pro vývojáře](developers-guide.md)
- [Co je Access Control založené na rolích Azure (RBAC)?](/azure/role-based-access-control/overview)
