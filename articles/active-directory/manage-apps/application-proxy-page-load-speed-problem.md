---
title: Aplikace Proxy aplikací trvá příliš dlouho | Dokumentace Microsoftu
description: Řešení problémů s výkonem načítání stránky s Azure AD Application Proxy
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac1182d719d7c90129115e1fadf94f4f86a28e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65782653"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Aplikace Proxy aplikací trvá příliš dlouho

Tento článek pomůže pochopit, proč Azure AD Application Proxy aplikace může trvat dlouhou dobu načtení. Také vysvětluje, co můžete udělat, abyste tento problém vyřešit.

## <a name="overview"></a>Přehled
I když vaše aplikace pracují, budou mít dlouhé latence. Pravděpodobně došlo k vylepšení topologie sítě, které můžete použít ke zlepšení rychlosti. Vyhodnocení různých topologií, najdete v článku [dokumentu důležité informace o síti](application-proxy-network-topology.md).

Kromě topologie sítě nejsou momentálně žádné další doporučení pro optimalizaci výkonu. Jako Proxy aplikací, které rozšiřuje služba by mohl pocházet do datového centra, který je fyzicky blíž. Těsné blízkosti vám můžou pomoct s latencí. Seznam datových center Azure, najdete v článku [latence zkušební stránku](http://www.azurespeed.com/Azure/Latency). 

Součástí datových center se službou Proxy aplikací [nástroj pro testování konektoru porty](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Názor na aplikaci Proxy datacentrum v umístění 
Můžou existovat datových center Azure, které není dosud zahrnují Proxy aplikací, ale by vedly k vylepšení skvělé latenci za vás. Odeslat data center umístění aadapfeedback@microsoft.com. Společnost Microsoft používá vaše zpětná vazba pro rozšíření plány.

Microsoft pracuje na dalších možností do Zlepšete latenci. Jakmile tato vylepšení jsou k dispozici, budou aktualizovány v dokumentaci.

## <a name="next-steps"></a>Další postup
[Práce s existující místní proxy servery](application-proxy-configure-connectors-with-proxy-servers.md)
