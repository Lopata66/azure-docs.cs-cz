---
title: Průvodce publikováním pro nabídky kontejnerů v Azure Marketplace
description: Tento článek popisuje požadavky na publikování nabídek kontejneru v Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: fae11343332fe741301016d4871d098b0214c7f5
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964443"
---
# <a name="publishing-guide-for-container-offers"></a>Průvodce publikováním pro nabídky kontejnerů

Nabídky kontejnerů vám pomůžou publikovat image kontejneru do Azure Marketplace. Tento průvodce vám pomůže pochopit požadavky této nabídky. 

Nabídky kontejnerů jsou nabídky transakcí, které se nasazují a účtují prostřednictvím Azure Marketplace. Volání akce, které uživatel vidí, je "získat hned".

Pokud je vaše řešení image kontejneru Docker, která je nastavená jako instance služby Azure Container Service, která je založená na Kubernetes, použijte typ nabídky *kontejneru* . 

> [!NOTE]
> Příklady instancí Azure Container Service založené na Kubernetes jsou Azure Kubernetes Service nebo Azure Container Instances, což je výběr zákazníků Azure pro modul runtime kontejneru na bázi Kubernetes.  

Microsoft v současné době podporuje bezplatných licenčních modelů (BYOL).

## <a name="container-offer-requirements"></a>Požadavky na nabídky kontejneru

| Požadavek | Podrobnosti |  
|:--- |:--- |  
| Fakturace a měření | Podpora modelu fakturace Free nebo BYOL.<br><br> |  
| Obrázek sestavený z souboru Dockerfile | Image kontejneru musí být založené na specifikaci image Docker a sestavené z souboru Dockerfile.<br> <br>Další informace o vytváření imagí Docker najdete v části "použití" tématu [souboru Dockerfile reference](https://docs.docker.com/engine/reference/builder/#usage).<br><br> |  
| Hostování v úložišti Azure Container Registry | Image kontejneru musí být hostované v úložišti Azure Container Registry.<br> <br>Další informace o práci s Azure Container Registry najdete v tématu [rychlý Start: Vytvoření privátního registru kontejnerů pomocí Azure Portal](../container-registry/container-registry-get-started-portal.md).<br><br> |  
| Označování obrázků | Image kontejneru musí obsahovat aspoň jednu značku (maximální počet značek: 16).<br><br>Další informace o označování obrázku najdete na `docker tag` stránce s [dokumentací k dokovacímu](https://docs.docker.com/engine/reference/commandline/tag) serveru.<br><br> |  

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, přečtěte si, jak [rozšířit cloudovou firmu pomocí Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Registrace a zahájení práce v partnerském centru:

- [Přihlaste se do partnerského centra](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) a vytvořte nebo dokončete vaši nabídku.
- Další informace najdete v tématu [Vytvoření nabídky Azure Container](./partner-center-portal/create-azure-container-offer.md) .
