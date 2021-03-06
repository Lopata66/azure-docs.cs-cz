---
title: Kurz – odstranění clusteru Azure Red Hat OpenShift
description: V tomto kurzu se dozvíte, jak odstranit cluster Azure Red Hat OpenShift pomocí Azure CLI.
author: sakthi-vetrivel
ms.custom: fasttrack-edit
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 372f531154528698da05c7a20c5d842b593259d1
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072248"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>Kurz: odstranění clusteru Azure Red Hat OpenShift 4

V tomto kurzu, který je třetí částí než tři, se odstraní cluster Azure Red Hat OpenShift se systémem OpenShift 4. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Odstranění clusteru Azure Red Hat OpenShift


## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se vytvořil cluster Azure Red Hat OpenShift a připojil se k používání webové konzole OpenShift. Pokud jste tyto kroky neudělali a chcete je sledovat, začněte s [kurzem 1 – Vytvoření clusteru Azure Red Hat OpenShift 4.](tutorial-create-cluster.md)

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.6.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud používáte Azure CLI místně, spusťte příkaz `az login` pro přihlášení k Azure.

```bash
az login
```

Pokud máte přístup k několika předplatným, spusťte `az account set -s {subscription ID}` nahrazení `{subscription ID}` pomocí předplatného, které chcete použít.

## <a name="delete-the-cluster"></a>Odstranění clusteru

V předchozích kurzech byly nastaveny následující proměnné.

```bash
CLUSTER=yourclustername
RESOURCEGROUP=yourresourcegroup
```

Pomocí těchto hodnot odstraňte cluster:

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

Pak se zobrazí výzva k potvrzení, jestli chcete cluster odstranit. Po potvrzení v nástroji `y` bude trvat několik minut, než se cluster odstraní. Po dokončení příkazu se odstraní celá skupina prostředků a všechny prostředky uvnitř ní, včetně clusteru –.

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se naučili:
> [!div class="checklist"]
> * Odstranění clusteru Azure Red Hat OpenShift 4

Další informace o používání OpenShift s oficiální [dokumentací Red Hat OpenShift](https://docs.openshift.com/container-platform/4.6/welcome/index.html)
