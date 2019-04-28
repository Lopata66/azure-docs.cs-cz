---
title: Příprava na změnu adresy příchozí IP - službě Azure App Service
description: Pokud chcete změnit vaše příchozí IP adresa, zjistěte, co můžete udělat tak, aby vaše aplikace i nadále fungovat po provedení změny.
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: aaa89b5a3bb1af6878ed21e0160a534a1c989228
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270060"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Postup přípravy na změnu příchozí IP adresy

Pokud jste dostali oznámení, že se mění příchozí IP adresu vaší aplikace Azure App Service, postupujte podle pokynů v tomto článku.

## <a name="determine-if-you-have-to-do-anything"></a>Zjistit, pokud máte dělat nic.

* Option 1: Pokud vaše aplikace app Service nemá žádné vlastní domény, nemusíte nic dělat.

* Option 2: Pokud pouze záznam CNAME (záznam DNS odkazuje na identifikátor URI) je nakonfigurován v portálu pro registraci vaší domény (třetích stran poskytovatele DNS nebo Azure DNS), nemusíte nic dělat.

* Možnost 3: Pokud záznam (záznam DNS odkazuje přímo na IP adresu) je nakonfigurován v portálu pro registraci vaší domény (třetích stran poskytovatele DNS nebo Azure DNS), nahraďte existující IP adresu nového. Podle pokynů uvedených v následující části najdete novou IP adresu.

* Option 4: Pokud je vaše aplikace za nástroj pro vyrovnávání zatížení, filtr nebo jiný mechanismus IP, která vyžaduje vaše aplikace IP adresu, nahraďte existující IP adresu nového. Podle pokynů uvedených v následující části najdete novou IP adresu.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Zjistit novou příchozí IP adresu na webu Azure Portal

Novou příchozí IP adresu, dostane do vaší aplikace je v portálu **virtuální IP adresa** pole. Tuto novou IP adresu a ten starý jsou připojené k vaší aplikaci nyní a později se starou odpojí.

1.  Otevřete web [Azure Portal](https://portal.azure.com).

2.  V levé navigační nabídce vyberte **App Services**.

3.  Vyberte ze seznamu vaši aplikaci služby App Service.

1.  Pokud je aplikace function app, naleznete v tématu [aplikace Function app příchozí IP adresu](../azure-functions/ip-addresses.md#function-app-inbound-ip-address).

4.  V části **nastavení** záhlaví, klikněte na tlačítko **vlastnosti** v levém navigačním panelu a najít v části s názvem **virtuální IP adresa**.

5. Zkopírujte IP adresu a překonfigurujte záznamu domény nebo IP mechanismus.

## <a name="next-steps"></a>Další postup

Tento článek vysvětlil, jak připravit pro změnu IP adresy, který byl inicializován nástrojem Azure. Další informace o IP adresách v Azure App Service najdete v tématu [příchozí a odchozí IP adresy ve službě Azure App Service](overview-inbound-outbound-ips.md).
