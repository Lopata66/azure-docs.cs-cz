---
title: Živý datový proud s místními kodéry pomocí Azure Portal | Microsoft Docs
description: Tento kurz vás provede kroky pro vytvoření Kanálu, který je nakonfigurován pro průchozí doručování.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 6f4acd95-cc64-4dd9-9e2d-8734707de326
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 08d1e9cd281cf41a79d1b30d709062c01abbab33
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695437"
---
# <a name="perform-live-streaming-with-on-premises-encoders-using-azure-portal"></a>Živé streamování pomocí místních kodérů pomocí Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](media-services-portal-live-passthrough-get-started.md)
> * [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
> * [REST](/rest/api/media/operations/channel)
> 
> 

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

Tento kurz vás provede kroky pro vytvoření **Kanálu**, který je nakonfigurován pro průchozí doručování. 

## <a name="prerequisites"></a>Požadavky
K dokončení kurzu potřebujete následující:

* Účet Azure: Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
* Účet Media Services. Pokud chcete vytvořit účet Media Services, přečtěte si článek [Jak vytvořit účet Media Services](media-services-portal-create-account.md).
* Webová kamera. Například [kodér Telestream Wirecast](media-services-configure-wirecast-live-encoder.md). 

Důrazně doporučujeme přečtení následujících článků:

* [Podpora RTMP ve službě Azure Media Services a kodéry pro kódování v reálném čase.](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
* [Přehled pro živé streamování pomocí služby Azure Media Services](media-services-manage-channels-overview.md)
* [Živé streamování pomocí místních kodérů, které vytvářejí datové proudy s více přenosovými rychlostmi](media-services-live-streaming-with-onprem-encoders.md)

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Běžný scénář živého streamování

Následující kroky popisují úlohy, které jsou běžně součástí procesu vytváření aplikací pro živé streamování, které používají kanály, nakonfigurované pro průchozí doručování. Tento kurz ukazuje, jak vytvořit a spravovat průchozí kanál a živé události.

> [!NOTE]
> Zkontrolujte, že koncový bod streamování, ze kterého chcete streamovat obsah, je ve stavu **Spuštěno**. 
    
1. Připojte k počítači videokameru. <br/>V případě nápadů při instalaci se podívejte na [Nastavení jednoduchého a přenosného kormidelního videa událostí]( https://link.medium.com/KNTtiN6IeT).
1. Spusťte a nakonfigurujte místní kodér pro kódování v reálném čase, který produkuje RTMP s více přenosovými rychlostmi nebo fragmentovaný proud MP4. Další informace najdete v článku [Podpora RTMP ve službě Azure Media Services a kodéry pro kódování v reálném čase](https://go.microsoft.com/fwlink/?LinkId=532824).<br/>Podívejte se také na tento blog: [živá streamovaná výroba pomocí OBS](https://link.medium.com/ttuwHpaJeT).
   
    Tento krok můžete provést i po vytvoření kanálu.
1. Vytvořit a spustit průchozí kanál.
1. Načtěte adresu URL ingestování kanálu. 
   
    Adresu URL ingestování používá kodér po kódování v reálném čase k odesílání datového proudu do kanálu.
1. Načtěte adresu URL náhledu kanálu. 
   
    Tuto adresu URL můžete použít, když chcete ověřit, jestli kanál správně přijímá proud živého vysílání.
1. Vytvořte živou událost nebo program. 
   
    Na portálu Azure se při vytváření živé události vytvoří také asset. 

1. Jakmile budete připraveni začít streamovat a archivovat, spusťte událost nebo program.
1. Volitelně můžete dát kodéru pro kódování v reálném čase signál, aby spustil reklamu. Reklama bude vložena do výstupního proudu.
1. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte událost nebo program.
1. Odstraňte událost nebo program (volitelně můžete odstranit i asset).     

> [!IMPORTANT]
> Informace o konceptech a důležité informace, týkající se živého streamování pomocí místních kodérů a průchozích kanálů, najdete v tématu [Živé streamování pomocí místních kodérů, které vytvářejí datové proudy s více přenosovými rychlostmi](media-services-live-streaming-with-onprem-encoders.md).
> 
> 

## <a name="to-view-notifications-and-errors"></a>Zobrazení upozornění a chyb
Pokud chcete zobrazit upozornění a chyby, vytvořené na portálu Azure, klikněte na ikonu oznámení.

![Oznámení](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

## <a name="create-and-start-pass-through-channels-and-events"></a>Vytvoření a spuštění průchozího kanálu.
Kanál je přidružený k událostem a programům, které vám umožňují řídit publikování a ukládání segmentů v živém datovém proudu. Kanály spravují události. 

Nastavením délky **archivačního okna** můžete určit počet hodin, po které chcete uchovávat zaznamenaný obsah programu. Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin. Délka archivačního okna také určuje maximální časový úsek, který můžou klienti prohledávat od aktuální živé pozice směrem zpět v čase. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každá událost je přidružena k assetu. Chcete-li publikovat událost, musíte pro přidružený prostředek vytvořit lokátor OnDemand. Tento lokátor vám umožní sestavit adresu URL pro streamování, kterou následně poskytnete svým klientům.

Kanál podporuje až tři současně spuštěné programy, takže si můžete vytvořit několik archivů stejného příchozího datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Požadavek vaší firmy může být například takový, že chcete archivovat 6 hodin programu, ale vysílat jenom posledních 10 minut. K tomu potřebujete vytvořit dva současně spuštěné programy. Jeden program nastavíte, aby archivoval 6 hodin události, ale tento program nebudete publikovat. Druhý program nastavíte, aby archivoval 10 minut a tento program budete publikovat.

Neměli byste znovu používat existující živé události. Místo toho vytvořte a spusťte novou událost pro každou jednotlivou událost.

Jakmile budete připraveni začít streamovat a archivovat, spusťte událost. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte program. 

Pokud chcete archivovaný obsah odstranit, zastavte a odstraňte událost a potom odstraňte přidružený asset. Asset nemůžete odstranit, pokud ho událost používá. Nejdřív odstraňte událost. 

I po zastavení a odstranění události můžou uživatelé streamovat archivovaný obsah jako video na vyžádání, a to tak dlouho, dokud asset neodstraníte.

Pokud chcete archivovaný obsah zachovat, ale nechcete ho zpřístupňovat pro streamování, odstraňte lokátor streamování.

### <a name="to-use-the-portal-to-create-a-channel"></a>Použití portálu k vytvoření kanálu
Tento oddíl ukazuje, jak vytvořit průchozí kanál pomocí možnosti **Rychle vytvořit**.

Další podrobnosti o průchozích kanálech najdete v tématu [Živé streamování pomocí místních kodérů, které vytvářejí datové proudy s více přenosovými rychlostmi](media-services-live-streaming-with-onprem-encoders.md).

1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. V okně **Nastavení** klikněte na **Živé streamování**. 
   
    ![Začínáme](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
   
    Zobrazí se okno **Živé vysílání datového proudu**.
3. Klikněte na **Rychle vytvořit** a vytvořte průchozí kanál s protokolem ingestování RTMP.
   
    Zobrazí se okno **VYTVOŘIT NOVÝ KANÁL**.
4. Zadejte název nového kanálu a klikněte na **Vytvořit**. 
   
    Tím vytvoříte průchozí kanál s protokolem ingestování RTMP.

## <a name="create-events"></a>Vytvoření událostí
1. Vyberte kanál, ke kterému chcete přidat událost.
2. Stiskněte tlačítko **Živá událost**.

![Událost](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)

## <a name="get-ingest-urls"></a>Získání ingestovaných adres URL
Po vytvoření kanálu můžete získat ingestované adresy URL, které poskytnete kodéru pro kódování v reálném čase. Kodér tyto adresy URL používá ke vkládání živého proudu.

![Snímek obrazovky zobrazující stránku živého streamování s vybraným kanálem a zobrazeným podoknem kanálů.](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

## <a name="watch-the-event"></a>Sledování události
Pokud chcete sledovat událost, klikněte na tlačítko **Sledovat** na webu Azure Portal nebo zkopírujte adresu URL streamování a použijte přehrávač dle svého výběru. 

![Vytvořeno](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

Při zastavení se živá událost automaticky převede na obsah na vyžádání.

## <a name="clean-up"></a>Vyčištění
Další podrobnosti o průchozích kanálech najdete v tématu [Živé streamování pomocí místních kodérů, které vytvářejí datové proudy s více přenosovými rychlostmi](media-services-live-streaming-with-onprem-encoders.md).

* Kanál se dá zastavit jenom v případě, že byly zastaveny všechny jeho události nebo programy.  Zastavený kanál zastaví narůstání poplatků. Když bude potřeba kanál znovu spustit, bude mít stejnou ingestovanou adresu URL, takže nebude nutné kodér znovu konfigurovat.
* Kanál se dá odstranit jenom v případě, že byly odstraněny všechny jeho živé události.

## <a name="view-archived-content"></a>Zobrazení archivovaného obsahu
I po zastavení a odstranění události můžou uživatelé streamovat archivovaný obsah jako video na vyžádání, a to tak dlouho, dokud asset neodstraníte. Asset nemůžete odstranit, pokud ho událost používá. Nejdřív odstraňte událost. 

Pokud chcete spravovat prostředky, vyberte **Nastavení** a klikněte na **prostředky**.

![Prostředky](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

## <a name="next-step"></a>Další krok
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
