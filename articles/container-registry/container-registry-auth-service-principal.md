---
title: Azure Container Registry ověřování pomocí instančního objektu
description: Poskytují přístup k obrázkům v váš privátní registr kontejnerů pomocí instančního objektu služby Azure Active Directory.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: 5d8904b5906adbdab68989b3a5cf9c3975c23533
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347060"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Ověřování služby Azure Container Registry pomocí instančních objektů

Instanční objekt Azure Active Directory (Azure AD) můžete použít k poskytování image kontejneru `docker push` a `pull` přístupu do vašeho registru kontejneru. S použitím instančního objektu, můžete poskytnout přístup k "bezobslužný" služeb a aplikací.

## <a name="what-is-a-service-principal"></a>Co je instanční objekt?

Azure AD *instanční* poskytují přístup k prostředkům Azure v rámci vašeho předplatného. Si můžete představit službu objektu zabezpečení jako identitu uživatele u služby, kde "služba" se všechny aplikace, služby nebo platforma, která potřebuje přístup k prostředkům. Konfigurace instančního objektu s přístupovými právy obor pouze na tyto prostředky, které zadáte. Potom nakonfigurujte vaše aplikace nebo služba používat přihlašovací údaje instančního objektu pro přístup k těmto prostředkům.

V rámci Azure Container Registry můžete vytvořit i službu Azure AD instanční objekt služby s o přijetí změn, vložení a o přijetí změn nebo další oprávnění do privátního registru v Azure. Úplný seznam najdete v tématu [Azure Container Registry role a oprávnění](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Proč používat instanční objekt služby?

S použitím instančního objektu služby Azure AD, můžete zadat oborový přístup na váš privátní registr kontejnerů. Můžete vytvořit jiný instanční objekty pro každý z vašich aplikací nebo služeb, každý s míru přístupová práva ke svému registru. A protože přihlašovacích údajů mezi služby a aplikace pro sdílení obsahu se můžete vyhnout, můžete pravidelná Změna přihlašovacích údajů nebo odvolat přístup pouze instanční objekt (a tedy aplikace), kterou zvolíte.

Například webové aplikace můžete použít objekt služby, který poskytuje image `pull` přístup pouze, když váš sestavovací systém, můžete použít objekt služby, který poskytuje v rámci `push` a `pull` přístup. Pokud vývoj vaší aplikace změní rukou, můžete svoje přihlašovací údaje služby Princip otočit aniž by to ovlivnilo systém sestavení.

## <a name="when-to-use-a-service-principal"></a>Kdy použít instanční objekt služby

Hlavního názvu služby by měl použít k poskytnutí přístupu registru v **bezobslužného scénáře**. To znamená, že všechny aplikace, služby nebo skript, který musí push nebo pull kontejneru Image v automatických nebo jinak bezobslužném.

Pro jednotlivý přístup k registru, jako je například při ruční vyžádání image kontejneru do pracovní stanice vývoj je vhodné použít vlastní [identity Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) pro přístup k registru (například s [az acr přihlášení][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>Ukázkové skripty

Předchozí ukázky skriptů pro rozhraní příkazového řádku Azure na Githubu najdete jako i verze pro prostředí Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Další postup

Jakmile budete mít instanční objekt služby, že jste poskytli přístup do vašeho registru kontejneru, můžete použít pověření uživatele vaší aplikace a služby pro interakci bezobslužného registru. Můžete použít pověření instančního objektu z libovolné služby Azure, které se můžou ověřovat pomocí služby Azure container registry. Příklady obsahují:

* [Ověřování pomocí Azure Container Registry z Azure Kubernetes Service (AKS)](container-registry-auth-aks.md)
* [Ověřování pomocí Azure Container Registry z Azure Container Instances (ACI)](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
