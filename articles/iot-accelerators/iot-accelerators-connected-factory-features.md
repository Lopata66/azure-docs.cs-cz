---
title: Funkce řešení propojené továrny – Azure | Microsoft Docs
description: Tento článek popisuje přehled funkcí předkonfigurovaného řešení propojené továrny, jako je například řídicí panel cloudu, pravidla a výstrahy.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: c868aa0f1c2449ccf163523c9ded25a31d1d84c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "73820113"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>K čemu je připojená akcelerátor řešení IoT Factory?

Propojená továrna je implementací referenční architektury Azure v průmyslu Azure, která je zabalená jako řešení open source. Můžete ho použít jako výchozí bod pro komerční produkt. Předem vytvořenou verzi řešení propojené továrny můžete nasadit do svého předplatného Azure z [akcelerátorů řešení Azure IoT](https://www.azureiotsolutions.com/#solutions/types/CF).

![Řídicí panel řešení propojené továrny](./media/iot-accelerators-connected-factory-features/dashboard.png)

Kód akcelerátoru řešení propojené továrny [je k dispozici na GitHubu](https://github.com/Azure/azure-iot-connected-factory).

Propojená továrna zahrnuje tyto funkce:

## <a name="industrial-device-interoperability"></a>Interoperabilita průmyslových zařízení

- Připojte se k průmyslovým prostředkům pomocí rozhraní OPC UA.
- Pomocí simulovaných výrobních linek (se spuštěnými servery OPC UA v kontejnerích Docker) si můžete prohlédnout živou telemetrii z nich.
- Projděte si informační model OPC UA serverů OPC UA z řídicího panelu cloudu.

## <a name="remote-management"></a>Vzdálená správa

- Nakonfigurujete prostředky OPC UA z řídicího panelu cloudu (volání metod, čtení a zápisu dat).
- Publikujte a odpublikujte data telemetrie z assetů OPC UA z řídicího panelu cloudu.

## <a name="cloud-dashboard"></a>Řídicí panel cloudu

- Zobrazení verzí Preview telemetrie přímo na řídicím panelu cloudu.
- Zobrazte trendy v datech telemetrie a vytvořte korelace pomocí řídicího panelu Průzkumníka Time Series Insights.
- Přečtěte si část vypočtená Celková efektivita vybavení (celkové efektivity zařízení) a klíčové ukazatele výkonu (KPI) z řídicího panelu cloudu.
- Zobrazení hierarchií průmyslových prostředků ve stromové topologii a také na interaktivní mapě.
- Zobrazení, potvrzení a zavření výstrah z řídicího panelu cloudu.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) je sestavená pro ukládání, vizualizaci a dotazování velkých objemů dat časových řad. Propojená továrna tuto službu využívá.
- Propojená továrna s touto službou umožňuje provádět hloubkovou analýzu dat zařízení v reálném čase.

## <a name="rules-and-alerts"></a>Pravidla a výstrahy

[Nakonfigurujte pravidla na základě prahových hodnot pro výstrahy](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Komplexní zabezpečení

- Nakonfigurujte oprávnění zabezpečení pro uživatele pomocí Access Control na základě rolí (RBAC).
- Komplexní šifrování se implementuje pomocí ověřování OPC UA (pomocí certifikátů X. 509) a také tokenů zabezpečení.

## <a name="customizability"></a>Přizpůsobitelnost

- Řešení můžete přizpůsobit tak, aby splňovalo konkrétní obchodní požadavky.
- Kompletní zdrojový kód řešení dostupný na GitHubu. Podívejte se na úložiště [předkonfigurovaných řešení propojené továrny](https://github.com/Azure/azure-iot-connected-factory) .

## <a name="next-steps"></a>Další kroky

Další informace o akcelerátoru řešení propojené továrny najdete v tématu rychlý Start [vyzkoušet cloudové řešení pro správu mých zařízení v průmyslu IoT](quickstart-connected-factory-deploy.md).
