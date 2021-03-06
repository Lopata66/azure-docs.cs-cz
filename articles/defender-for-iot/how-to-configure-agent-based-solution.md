---
title: Konfigurace řešení Azure Defender pro řešení založené na agentech IoT
description: Naučte se konfigurovat shromažďování dat v Azure Defenderu pro řešení založené na agentech IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 53fc01839ef522afaffe52cd8a3126e40ba94a05
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809759"
---
# <a name="configure-azure-defender-for-iot-agent-based-solution"></a>Konfigurace řešení Azure Defender pro řešení založené na agentech IoT  

Tento článek popisuje, jak nakonfigurovat shromažďování dat v Azure Defenderu pro řešení založené na agentech IoT.

## <a name="configure-data-collection"></a>Konfigurace shromažďování dat

Konfigurace shromažďování dat v Azure Defenderu pro řešení založené na agentovi IoT: 

1. Přejděte na Azure Portal a vyberte IoT Hub, ke kterému je připojen Defender pro IoT. 

1. V nabídce  **zabezpečení**   Vyberte **Nastavení**. 

1. Vyberte **shromažďování dat**. 

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-collection.png" alt-text="V nastavení nabídky zabezpečení vyberte shromažďování dat.":::

## <a name="geolocation-and-ip-address-handling"></a>Geografická poloha a zpracování IP adres 

Aby bylo možné zabezpečit vaše řešení IoT, budou se ve výchozím nastavení shromažďovat a ukládat IP adresy příchozích a odchozích připojení pro vaše zařízení IoT, IoT Edge a IoT Hub. Tyto informace jsou nezbytné a slouží ke zjištění neobvyklého připojení ze zdrojů podezřelých IP adres. Například když dojde k pokusům o navázání připojení ze zdroje IP adres známého botnetu nebo ze zdroje IP adresy mimo vaši geografickou polohu. Defender pro službu IoT nabízí flexibilitu, která umožňuje kdykoli povolit a zakázat shromažďování dat IP adres. 

Pokud chcete povolit nebo zakázat shromažďování dat IP adres: 

1. Otevřete IoT Hub a v nabídce zabezpečení vyberte **Nastavení**    ****   . 

1. Vyberte obrazovku pro **shromažďování dat**   , upravte zeměpisnou polohu a nastavení zpracování IP adres tak, aby vyhovovalo vašim potřebám. 

## <a name="log-analytics-creation"></a>Vytváření Log Analytics 

Defender pro IoT umožňuje ukládat výstrahy zabezpečení, doporučení a nezpracovaná data zabezpečení do svého pracovního prostoru Log Analytics. Ingestování Log Analytics v IoT Hub je ve výchozím nastavení **vypnuté** v řešení Defender for IoT. Je možné připojit Defender pro IoT k pracovnímu prostoru analytického protokolu a uložit i data zabezpečení. 

Ve vašem pracovním prostoru Log Analytics jsou ve výchozím nastavení uloženy dva typy informací – Defender pro IoT:
 
- Výstrahy zabezpečení.

- Doporučit. 

Můžete zvolit, že chcete přidat úložiště dodatečného typu informací jako `raw events` . 

> [!Note] 
> Uložení  `raw events`   v Log Analytics přináší další náklady na úložiště. 

Povolení spolupráce Log Analytics s mikroagentem: 

1. Přejděte do   >  **kolekce konfigurační data** v pracovním prostoru a přepněte přepínač na  **zapnuto**. 

1. Vytvořte nový pracovní prostor Log Analytics nebo připojte stávající. 

1. Ověřte, že je vybraná možnost **přístup k nezpracovaným datům zabezpečení**   .  

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-settings.png" alt-text="Ujistěte se, že je vybraná možnost přístup k nezpracovaným datům zabezpečení.":::

1. Vyberte **Uložit**.

Každý měsíc je prvních 5 gigabajtů dat ingestované na zákazníky do služby Azure Log Analytics Service zdarma. Každé gigabajty dat, které se ingestují do vašeho pracovního prostoru Azure Log Analytics, se v prvních 31 dnech uchovávají zdarma. Další informace o cenách najdete v tématu [Log Analytics ceny](https://azure.microsoft.com/pricing/details/monitor/). 

Změna konfigurace pracovního prostoru Log Analytics: 

1. V IoT Hub v nabídce **zabezpečení** vyberte  **Nastavení**. 

1. Vyberte obrazovku **shromažďování dat**   a upravte konfiguraci pracovního prostoru Log Analytics nastavení tak, aby vyhovovala vašim potřebám. 

Přístup k výstrahám v pracovním prostoru Log Analytics po konfiguraci:

1. Vyberte výstrahu v programu Defender pro IoT.

1. Vyberte možnost **prozkoumat výstrahy v pracovním prostoru Log Analytics**.

Přístup k výstrahám v pracovním prostoru Log Analytics po konfiguraci:

1. Vyberte doporučení v programu Defender pro IoT.

1. Vyberte možnost **prozkoumat doporučení v pracovním prostoru Log Analytics**. 
 
Další informace o dotazování dat z Log Analytics najdete v tématu [Začínáme s dotazy v Log Analytics](../azure-monitor/log-query/get-started-queries.md). 

## <a name="turn-off-defender-for-iot"></a>Vypnout Defender pro IoT 

Postup zapnutí nebo vypnutí programu Defender pro službu IoT na konkrétním IoT Hub: 

1. V IoT Hub v nabídce **zabezpečení** vyberte  **Nastavení**.

1. Vyberte obrazovku **shromažďování dat**   a upravte konfiguraci pracovního prostoru Log Analytics nastavení tak, aby vyhovovala vašim potřebám.

## <a name="next-steps"></a>Další kroky 

Přejděte k dalšímu článku, abyste mohli [nakonfigurovat vaše řešení](quickstart-configure-your-solution.md). 
