---
title: Koncepty architektury v Azure IoT Central – energie | Microsoft Docs
description: V tomto článku se seznámíte s klíčovými koncepty týkajícími se architektury šablony aplikace Azure IoT Central Energy.
author: op-ravi
ms.author: omravi
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: f87e1a7893fa8c2ea22711f90696a5d2836eb3ac
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965614"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central – architektura aplikací inteligentního měření

Tento článek poskytuje přehled architektury šablon aplikace monitorování inteligentního měření. Následující diagram znázorňuje běžně použitou architekturu pro aplikace inteligentního měření v Azure pomocí IoT Central platformy.

> [!div class="mx-imgBorder"]
> ![architektury inteligentního měření](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Tato architektura se skládá z následujících komponent. Některá řešení nemusí vyžadovat všechny součásti, které jsou tady uvedené.

## <a name="smart-meters-and-connectivity"></a>Inteligentní měřiče a konektivita 

Inteligentní měřič je jedním z nejdůležitějších zařízení ze všech energetických prostředků. Zaznamenává data spotřeby energie a komunikuje s nimi pro účely monitorování a jiné případy použití, například fakturace a odpověď na vyžádání. Na základě typu měřiče se může připojit k IoT Central buď prostřednictvím bran, nebo prostřednictvím jiných zprostředkujících zařízení nebo systémů, jako jsou hraniční zařízení a hlavní systémy. Sestavte most IoT Central zařízení pro připojení zařízení, která se nedají připojit přímo. Most zařízení IoT Central je open source řešení a [tady](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge)můžete najít kompletní podrobnosti. 


## <a name="iot-central-platform"></a>IoT Central platforma

Azure IoT Central je platforma, která zjednodušuje vytváření řešení IoT a pomáhá snižovat zatížení a náklady na správu, provoz a vývoj IoT. Díky IoT Central se můžete snadno připojit, monitorovat a spravovat vaše prostředky Internet věcí (IoT) ve velkém měřítku. Po připojení inteligentních měřičů k IoT Central se v šabloně aplikace používají předdefinované funkce, jako jsou například modely zařízení, příkazy a řídicí panely. Šablona aplikace také používá IoT Central úložiště pro scénáře teplé cesty, jako jsou monitorování, analýzy, pravidla a vizualizace dat měřiče v reálném čase. 


## <a name="extensibility-options-to-build-with-iot-central"></a>Možnosti rozšíření pro sestavení pomocí IoT Central
Platforma IoT Central poskytuje dvě možnosti rozšíření: průběžné exporty dat (CDE) a rozhraní API. Zákazníci a partneři si můžou vybrat mezi těmito možnostmi a přizpůsobit jejich řešení konkrétním potřebám. Například jeden z našich partnerů nakonfiguroval CDE pomocí Azure Data Lake Storage (ADLS). Používají ADLS pro dlouhodobé uchovávání dat a jiné scénáře úložiště s studenými cestami, jako jsou například účely dávkového zpracování, auditování a vytváření sestav. 

## <a name="next-steps"></a>Další kroky

* Teď, když jste se seznámili s architekturou, můžete [zdarma vytvořit aplikaci inteligentního měření](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring) .
* Další informace o IoT Central najdete v tématu [IoT Central Overview](https://docs.microsoft.com/azure/iot-central/) .
