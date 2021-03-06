---
title: Zobrazit informace v upozorněních
description: Vyberte výstrahu z okna výstrahy a zkontrolujte podrobnosti.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/03/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 804cdbd6266f2e77b5562d914bac089fce80f645
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839132"
---
# <a name="view-information-in-alerts"></a>Zobrazit informace v upozorněních

Vyberte výstrahu z okna **výstrahy** a zkontrolujte podrobnosti výstrahy. Podrobnosti obsahují následující informace:

- Metadata výstrahy

- Informace o přenosech, zařízeních a událostech

- Odkazy na připojená zařízení v mapě zařízení

- Komentáře definované analytiky a správci zabezpečení

- Doporučení pro zkoumání události

## <a name="alert-metadata"></a>Metadata výstrahy

Podrobnosti výstrahy zahrnují následující metadata výstrahy:

  - ID výstrahy

  - Modul zásad, který aktivoval výstrahu

  - Datum a čas, kdy byla výstraha aktivována

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="Bylo zjištěno neoprávněné připojení k Internetu.":::

## <a name="information-about-devices-traffic-and-the-event"></a>Informace o zařízeních, provozu a událostech

Zpráva s upozorněním poskytuje následující informace:

  - Zjištěná zařízení.

  - Provoz zjištěný mezi zařízeními, jako jsou protokoly a kódy funkcí.

  - Přehledy o důsledcích události.

Tyto informace můžete použít při rozhodování o tom, jak spravovat událost výstrahy.

## <a name="links-to-connected-devices-in-the-device-map"></a>Odkazy na připojená zařízení v mapě zařízení

Pokud se chcete dozvědět víc o zařízeních připojených ke zjištěným zařízením, můžete v ní vybrat obrázek zařízení a v mapě zobrazit připojená zařízení.

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="Operace RCP se nezdařila.":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="Snímek obrazovky zařízení":::

Mapování se filtruje na vybrané zařízení a k němu jsou připojená další zařízení. Mapa také zobrazí dialogové okno **Rychlé vlastnosti** pro zařízení zjištěná v výstrahách.

## <a name="comments-defined-by-security-analysts-and-administrators"></a>Komentáře definované analytiky a správci zabezpečení 

Výstrahy mohou obsahovat seznam předdefinovaných komentářů. Komentáře mohou například obsahovat pokyny týkající se zmírňování akcí, které je třeba provést, nebo jména jednotlivců, kteří se na událost kontaktovali.

Když spravujete událost výstrahy, můžete zvolit komentář nebo komentáře, které nejlépe odpovídají stavu události nebo kroky, které jste provedli k prošetření výstrahy.

Vybrané komentáře jsou uloženy ve zprávě s výstrahou. Práce s poznámkami vylepšuje komunikaci mezi jednotlivci a týmy během vyšetřování události výstrahy. V důsledku toho komentáře můžou zrychlit dobu odezvy na incidenty.

Správce nebo analytik zabezpečení předdefinuje komentáře. Vybrané komentáře nejsou předávány partnerským systémům definovaným v pravidlech předávání.

Po kontrole informací v upozornění můžete provádět různé kroky forenzní, které vás provedou správou události výstrahy. Například:

- Analyzovat poslední aktivitu zařízení (sestava dolování dat). 

- Analyzujte další události, ke kterým došlo ve stejnou dobu (časová osa události). 

- Analýza komplexních přenosů událostí (soubor PCAP).

## <a name="pcap-files"></a>Soubory PCAP

V některých případech můžete k souboru PCAP získat přístup ze zprávy s upozorněním. To může být užitečné, pokud chcete podrobnější informace o souvisejícím síťovém provozu.

Pokud chcete stáhnout soubor PCAP, vyberte :::image type="content" source="media/how-to-work-with-alerts-sensor/download-pcap.png" alt-text="ikonu stáhnout."::: v pravém horním rohu dialogového okna **Podrobnosti výstrahy** .

## <a name="recommendations-for-investigating-an-event"></a>Doporučení pro prošetření události 

V oblasti **doporučení** výstrahy se zobrazují informace, které vám pomůžou lépe porozumět události. Před správou události výstrahy nebo provedením akce v zařízení nebo v síti zkontrolujte tyto informace.

## <a name="see-also"></a>Viz také

[Urychlení pracovních postupů výstrah](how-to-accelerate-alert-incident-response.md)

[Spravovat událost výstrahy](how-to-manage-the-alert-event.md)
