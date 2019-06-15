---
title: Inteligentní zjišťování – Pack detekce zabezpečení pomocí Azure Application Insights | Dokumentace Microsoftu
description: Monitorování aplikace pomocí Azure Application Insights pro potenciální problémy se zabezpečením.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 90d58d1b22e893e922aa0f3770198fc95f539419
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64572888"
---
# <a name="application-security-detection-pack-preview"></a>Sada zjišťování zabezpečení aplikace (preview)

Application Insights automaticky analyzuje telemetrii generovanou vaší aplikace a detekuje potenciální problémy se zabezpečením. Tato funkce umožňuje identifikovat případné problémy zabezpečení a jejich zpracování po opravě aplikace nebo provedením potřebných bezpečnostních opatření.

Tato funkce vyžaduje žádné speciální instalační program, než [konfiguraci vaší aplikace odesílat telemetrická data](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Při obdržení tohoto typu upozornění inteligentního zjišťování
Existují tři typy, které jsou zjištěny problémy se zabezpečením:
1. Zabezpečený přístup k adrese URL: adresa URL aplikace, je přistupováno prostřednictvím protokolu HTTP a HTTPS. Obvykle adresu URL, která přijímá požadavky HTTPS by neměla přijímat požadavky HTTP. To může znamenat chybu nebo zabezpečení problém ve vaší aplikaci.
2. Nezabezpečené formuláře: formuláře (nebo jiný požadavek "POST") v aplikaci používá protokol HTTP místo protokolu HTTPS. Pomocí protokolu HTTP může ohrozit uživatelská data, která se ve formuláři odesílají.
3. Podezřelé aktivity uživatele: aplikace se přistupuje z více zemí a oblastí stejným uživatelem na přibližně ve stejnou dobu. Například stejného uživatele získat přístup k aplikaci z Španělsko a USA během jedné hodiny. Tato detekce indikuje pokus o potenciálně škodlivém přístup do vaší aplikace.

## <a name="does-my-app-definitely-have-a-security-issue"></a>Moje aplikace jednoznačně má potíže se zabezpečením?
Ne, oznámení, neznamená, že má vaše aplikace jednoznačně potíže se zabezpečením. Detekce kterékoli z výše uvedených scénářích může v mnoha případech znamenat potíže se zabezpečením. Ale zjišťování může mít fyzické obchodní odůvodnění a můžete ignorovat.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Jak opravit detekce "Nezabezpečené adresy URL přístup"?
1. **Třídění.** Oznámení obsahuje počet uživatelů, kteří navštívili nezabezpečené adresy URL a adresy URL, která byla většina ovlivněny nezabezpečené přístup. Můžete přiřadit prioritu problému.
2. **Obor.** Jaké je procento uživatelů přistupuje nezabezpečené adresy URL? Kolik adresy URL se to týká? Tyto informace můžete získat z oznámení.
3. **Diagnostika.** Zjišťování poskytuje seznam nezabezpečené požadavky a seznamy adres URL a uživatelů, které byly ovlivněny, při další diagnostice problému.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Jak opravit detekce "Nezabezpečené formuláře"?
1. **Třídění.** Oznámení obsahuje počet nezabezpečené formulářů a počet uživatelů, jejichž data se potenciálně dojde k ohrožení bezpečnosti. Můžete přiřadit prioritu problému.
2. **Obor.** Jaký byl zahrnut v největší počet nezabezpečené přenosy a co je rozdělení nezabezpečené přenosy v čase? Tyto informace můžete získat z oznámení.
3. **Diagnostika.** Zjišťování poskytuje seznam nezabezpečené formulářů a rozpis počtu nezabezpečené přenosy pro každý formulář při další diagnostice problému.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Jak opravit detekce "podezřelé aktivity uživatele"?
1. **Třídění.** Oznámení obsahuje počet různých uživatelů, kteří monitorujícím podezřelého chování. Můžete přiřadit prioritu problému.
2. **Obor.** Ze kterých zemích nebo oblastech podezřelé požadavky pocházejí? Který uživatel byl nejvíce podezřelých? Tyto informace můžete získat z oznámení.
3. **Diagnostika.** Zjišťování poskytuje seznam uživatelů, podezřelých a seznam země nebo oblasti pro každého uživatele, při další diagnostice problému.
