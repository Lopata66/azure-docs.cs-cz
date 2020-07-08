---
title: Spravovat účty Azure Media Services V2 | Microsoft Docs
description: Pokud chcete začít spravovat, šifrovat, kódovat, analyzovat a streamovat mediální obsah v Azure, musíte vytvořit účet Media Services. Tento článek vysvětluje, jak spravovat účty Azure Media Services V2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 09a5f004570430fafe5c86f4f8ae048f2d1fe4c4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "75981943"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Správa účtů Azure Media Services V2

Pokud chcete začít spravovat, šifrovat, kódovat, analyzovat a streamovat mediální obsah v Azure, musíte vytvořit účet Media Services. Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Přesunutí účtu Media Services mezi předplatnými 

Pokud potřebujete přesunout Media Services účet k novému předplatnému, musíte nejdřív přesunout celou skupinu prostředků, která obsahuje Media Services účet k novému předplatnému. Je nutné přesunout všechny připojené prostředky: účty Azure Storage, profily Azure CDN atd. Další informace najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Stejně jako u všech prostředků v Azure může dokončení přesunutí skupiny prostředků nějakou dobu trvat.

Media Services V2 nepodporuje model víceklientské architektury. Pokud potřebujete přesunout účet Media Services k předplatnému v novém tenantovi, vytvořte v novém tenantovi novou aplikaci Azure Active Directory (Azure AD). Pak přesuňte účet k předplatnému v novém tenantovi. Po dokončení přesunu tenanta můžete začít používat aplikaci Azure AD z nového tenanta pro přístup k účtu Media Services pomocí rozhraní API v2. 

> [!IMPORTANT]
> Pro přístup k Media Services rozhraní API v2 je potřeba resetovat informace o [ověřování Azure AD](media-services-portal-get-started-with-aad.md) .  
### <a name="considerations"></a>Důležité informace

* Před migrací na jiné předplatné vytvořte zálohy všech dat ve vašem účtu.
* Musíte zastavit všechny koncové body streamování a prostředky živého streamování. Uživatelé nebudou mít přístup k vašemu obsahu po dobu trvání přesunutí skupiny prostředků. 

> [!IMPORTANT]
> Nespouštějte koncový bod streamování, dokud se přesun úspěšně nedokončí.

### <a name="troubleshoot"></a>Řešení potíží 

Pokud se účet Media Services nebo přidružený Azure Storage účet stane "Odpojeno" po přesunutí skupiny prostředků, zkuste klíče účtu úložiště otočit. Pokud se při střídání klíčů účtu úložiště nevyřeší stav "Odpojeno" účtu Media Services, založte novou žádost o podporu z nabídky podpora a řešení potíží v účtu Media Services.  
 
## <a name="next-steps"></a>Další kroky

[Vytvoření účtu](media-services-portal-create-account.md)
