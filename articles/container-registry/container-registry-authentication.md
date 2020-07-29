---
title: Možnosti ověřování v registru
description: Možnosti ověřování privátního služby Azure Container Registry, včetně přihlašování pomocí Azure Active Directory identity, pomocí instančních objektů a použití volitelných přihlašovacích údajů správce.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84712033"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Ověřování pomocí služby Azure Container Registry

Existuje několik způsobů, jak ověřit pomocí služby Azure Container Registry, z nichž každá je platná pro jeden nebo více scénářů použití registru.

Mezi doporučené způsoby patří ověřování v registru přímo přes [jednotlivé přihlašovací údaje](#individual-login-with-azure-ad), nebo vaše aplikace a orchestrace kontejnerů můžou provádět bezobslužné nebo "nezabezpečené" ověřování pomocí [instančního objektu služby](#service-principal)Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Možnosti ověřování

V následující tabulce jsou uvedeny dostupné metody ověřování a Doporučené scénáře. Podrobnosti najdete v tématu s propojeným obsahem.

| Metoda                               | Ověřování                                           | Scénáře                                                            | RBAC                             | Omezení                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Individuální identita AD](#individual-login-with-azure-ad)                | `az acr login` v Azure CLI                             | Interaktivní nabízení a vyžádané sdílení pro vývojáře, testery                                    | Yes                              | Token AD se musí obnovit každé 3 hodiny.     |
| [Instanční objekt služby AD](#service-principal)                  | `docker login`<br/><br/>`az acr login`v Azure CLI<br/><br/> Nastavení přihlášení do registru v rozhraních API nebo nástrojích<br/><br/> [Tajný kód pro vyžádání obsahu Kubernetes](container-registry-auth-kubernetes.md)                                           | Bezobslužné vkládání z kanálu CI/CD<br/><br/> Bezobslužné stažení do Azure nebo externích služeb  | Yes                              | Výchozí platnost hesla SP je 1 rok.       |                                                           
| [Integrace s AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Připojit registr při vytvoření nebo aktualizaci clusteru AKS  | Bezobslužné stažení do clusteru AKS                                                  | Ne, jenom přístup pro vyžádání             | K dispozici pouze v clusteru AKS            |
| [Spravovaná identita pro prostředky Azure](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` v Azure CLI                                       | Bezobslužné vkládání z kanálu CI/CD z Azure<br/><br/> Bezobslužné získání dat do služeb Azure<br/><br/>   | Yes                              | Používejte jenom ze služeb Azure, které [podporují spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) .              |
| [Uživatel s oprávněními správce](#admin-account)                            | `docker login`                                          | Interaktivní nabízená oznámení nebo vyžádaná osoba pro jednotlivé vývojáře nebo testery                           | Ne, vždycky získávat a nabízet přístup  | Jeden účet na registr, nedoporučuje se pro více uživatelů         |
| [Přístupový token s oborem úložiště](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login`v Azure CLI   | Interaktivní nabízená oznámení nebo vyžádání do úložiště podle individuálního vývojáře nebo testerů<br/><br/> Bezobslužné odeslání/vyžádání do úložiště podle individuálního systému nebo externího zařízení                  | Yes                              | Aktuálně není integrováno se službou AD identity  |

## <a name="individual-login-with-azure-ad"></a>Individuální přihlášení pomocí Azure AD

Při přímém práci s registrem, jako je například navýšení a vkládání imagí z vývojové pracovní stanice, proveďte ověření pomocí příkazu [AZ ACR Login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) v rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Při přihlášení pomocí `az acr login` rozhraní příkazového řádku použije token vytvořený při provedení [AZ Login](/cli/azure/reference-index#az-login) k bezproblémovému ověření relace s registrem. K dokončení toku ověřování musí být Docker nainstalovaný a spuštěný ve vašem prostředí. `az acr login`k nastavení tokenu Azure Active Directory v souboru používá klienta Docker `docker.config` . Po přihlášení tohoto postupu jsou vaše přihlašovací údaje uložené v mezipaměti a následné `docker` příkazy ve vaší relaci nevyžadují uživatelské jméno ani heslo.

> [!TIP]
> Slouží také `az acr login` k ověření individuální identity v případě, že chcete vyžádat nebo načíst artefakty jiné než Image Docker do registru, například [artefakty OCI](container-registry-oci-artifacts.md).  


V případě přístupu k registru je token používaný nástrojem `az acr login` platný po dobu **3 hodin**, proto doporučujeme, abyste se před spuštěním příkazu vždy přihlásili k registru `docker` . V případě vypršení platnosti tokenu ho můžete znovu aktualizovat pomocí `az acr login` příkazu znovu a ověřit. 

Použití `az acr login` s identitami Azure zajišťuje [přístup založený na rolích](../role-based-access-control/role-assignments-portal.md). V některých scénářích se můžete chtít přihlásit k registru s vlastní individuální identitou v Azure AD. Pro scénáře mezi službami nebo pro zpracování potřeb pracovní skupiny nebo pracovního postupu vývoje, kde nechcete spravovat individuální přístup, se můžete také přihlásit pomocí [spravované identity pro prostředky Azure](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Instanční objekt

Pokud k registru přiřadíte [instanční objekt](../active-directory/develop/app-objects-and-service-principals.md) , vaše aplikace nebo služba ho můžou použít pro bezobslužné ověřování. Instanční objekty umožňují [přístup na základě rolí](../role-based-access-control/role-assignments-portal.md) k registru a k registru můžete přiřadit více instančních objektů. Několik instančních objektů vám umožní definovat různý přístup pro různé aplikace.

K dispozici jsou role pro registr kontejnerů:

* **AcrPull**: pull

* **AcrPush**: vyžádání a vložení

* **Vlastník**: vyžádání, vložení a přiřazení rolí jiným uživatelům

Úplný seznam rolí najdete v tématu [Azure Container Registry role a oprávnění](container-registry-roles.md).

Pro skripty rozhraní příkazového řádku pro vytvoření instančního objektu pro ověřování pomocí služby Azure Container registry a další pokyny najdete v tématu [Azure Container Registry ověřování pomocí instančních objektů](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Účet správce

Každý registr kontejnerů obsahuje účet uživatele správce, který je ve výchozím nastavení zakázaný. Můžete povolit uživatele správce a spravovat jeho přihlašovací údaje v Azure Portal nebo pomocí Azure CLI nebo jiných nástrojů Azure.

> [!IMPORTANT]
> Účet správce je navržený pro jednoho uživatele, který má přístup k registru, hlavně pro účely testování. Nedoporučujeme sdílení přihlašovacích údajů účtu správce mezi více uživateli. Všichni uživatelé, kteří se ověřují pomocí účtu správce, se zobrazí jako jednotliví uživatelé s přístupem push a pull do registru. Změna nebo zakázání tohoto účtu zakáže přístup k registru pro všechny uživatele, kteří používají své přihlašovací údaje. Pro uživatele a instanční objekty se doporučuje použít pro scénáře s doplňováním provozu individuální identitu.
>

Účet správce je k dispozici se dvěma hesly, z nichž lze znovu vygenerovat. Dvě hesla umožňují udržovat připojení k registru pomocí jednoho hesla při opětovném vygenerování. Pokud je povolen účet správce, můžete `docker login` při zobrazení výzvy k zadání základního ověřování do registru předat uživatelské jméno a heslo k příkazu. Příklad:

```
docker login myregistry.azurecr.io 
```

Osvědčené postupy pro správu přihlašovacích údajů najdete v tématu Reference k příkazům [Docker Login](https://docs.docker.com/engine/reference/commandline/login/) .

Pokud chcete povolit uživatele s oprávněními správce pro existující registr, můžete použít `--admin-enabled` parametr příkazu [AZ ACR Update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) v rozhraní příkazového řádku Azure CLI:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Uživatele s oprávněními správce můžete v Azure Portal povolit tak, že přejdete do registru, vyberete **přístupové klíče** v části **Nastavení**a potom **povolíte** v části **uživatel s oprávněními správce**.

![Povolit uživatelské rozhraní uživatele pro správu v Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Další kroky

* [Vložení první Image pomocí Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
