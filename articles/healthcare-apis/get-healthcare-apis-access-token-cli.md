---
title: Získání přístupového tokenu pomocí Azure CLI – Azure API pro FHIR
description: Tento článek vysvětluje, jak získat přístupový token pro Azure API pro FHIR pomocí Azure CLI.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: matjazl
ms.openlocfilehash: 4d1c4cfcb15d97a2c54a04344f0bd098f65c1392
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660368"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Získání přístupového tokenu pro Azure API pro FHIR pomocí Azure CLI

V tomto článku se dozvíte, jak získat přístupový token pro Azure API pro FHIR pomocí Azure CLI. Když [zřizujete rozhraní API Azure pro FHIR](fhir-paas-portal-quickstart.md), nakonfigurujete skupinu uživatelů nebo instančních objektů, které mají přístup ke službě. Pokud je ID objektu uživatele v seznamu povolených ID objektů, můžete ke službě přistupovat pomocí tokenu získaného pomocí Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="obtain-a-token"></a>Získání tokenu

Rozhraní Azure API pro FHIR používá `resource`  nebo `Audience` s identifikátorem URI se rovná identifikátoru URI serveru FHIR `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` . Token můžete získat a Uložit do proměnné (s názvem `$token` ) pomocí následujícího příkazu:

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

## <a name="use-with-azure-api-for-fhir"></a>Použití s rozhraním Azure API pro FHIR

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s postupem získání přístupového tokenu pro rozhraní Azure API pro FHIR pomocí Azure CLI. Pokud se chcete dozvědět, jak získat přístup k rozhraní FHIR API pomocí post, přejděte do kurzu post.

>[!div class="nextstepaction"]
>[Přístup k rozhraní FHIR API pomocí post](access-fhir-postman-tutorial.md)
