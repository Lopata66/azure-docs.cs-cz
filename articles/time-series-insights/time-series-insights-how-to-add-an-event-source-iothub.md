---
title: Přidání zdroje událostí IoT Hub do služby Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje postup přidání zdroje událostí, která je připojená do služby IoT Hub pro vaše prostředí Time Series Insights
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: 3168fb6cf6e9b08fe4d2de921179129db241d153
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847163"
---
# <a name="how-to-add-an-iot-hub-event-source-to-time-series-insights-environment"></a>Přidání zdroje událostí IoT Hub do prostředí Time Series Insights

Tento článek popisuje postup přidání zdroje událostí, která čte data ze služby IoT Hub do vašeho prostředí Azure Time Series Insights (TSI) pomocí webu Azure portal.

> [!NOTE]
> Tyto pokyny platí pro všeobecné dostupnosti služby TSI a TSI prostředí ve verzi preview.

## <a name="prerequisites"></a>Požadavky

* Vytvoření prostředí služby TSI. Další informace najdete v tématu [vytvořit prostředí Azure TSI](time-series-insights-update-create-environment.md).
* Vytvořit IoT Hub. Další informace o IoT hub najdete v tématu [vytvoření IoT Hubu pomocí webu Azure portal](../iot-hub/iot-hub-create-through-portal.md).
* IoT Hub musí mít aktivní zprávě události odesílány ve.
* Vytvořte vyhrazenou skupinu spotřebitelů ve službě IoT Hub pro prostředí služby TSI na využívání z. Každý zdroj událostí TSI musí mít svůj vlastní vyhrazenou skupinu spotřebitelů, kterou nebude sdílet s dalšími uživateli. Pokud více čtenářům přijímat události z stejnou skupinu uživatelů, jsou pravděpodobně uvidíte selhání všechny nástroje pro čtení. Podrobnosti najdete v tématu [Příručka vývojáře pro IoT Hub](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Přidat skupinu uživatelů do služby IoT Hub

Skupiny příjemců používají aplikace k načítání dat ze služby Azure IoT Hubs. Poskytují vyhrazenou skupinu spotřebitelů, pro použití u této služby TSI prostředí pouze spolehlivě číst data ze služby IoT Hub.

Chcete-li přidat novou skupinu uživatelů do služby IoT Hub, postupujte podle těchto kroků:

1. Na webu Azure Portal najděte a otevřete své Centrum IoT.
1. V části **nastavení** záhlaví, vyberte **integrovaných koncových bodech**.

   ![IoT Hub, jeden][1]

1. Vyberte **události** koncového bodu a **vlastnosti** otevře se stránka.

1. V části **skupiny příjemců** záhlaví, zadejte nový jedinečný název pro skupinu příjemců. Při vytváření nového zdroje událostí, použijte tento stejný název v prostředí služby TSI.

1. Vyberte **Uložit** uložte novou skupinu uživatelů.

## <a name="add-a-new-event-source"></a>Přidat nový zdroj událostí

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyhledání vašeho stávajícího prostředí služby TSI. Klikněte na tlačítko **všechny prostředky** v nabídce na levé straně webu Azure portal. Vyberte vaše prostředí TSI.

1. V části **topologie prostředí** záhlaví, klikněte na tlačítko **zdroje událostí**.

   ![Dvě služby IoT Hub][2]

1. Klikněte na tlačítko **+ Přidat**.

1. Poskytují **název zdroje událostí** jedinečné pro toto prostředí TSI například **datového proudu událostí**.

   ![Tři služby IoT Hub][3]

1. Vyberte **zdroj** jako **služby IoT Hub**.

1. Vyberte příslušné **možnost importu**.

   * Zvolte **pomocí služby IoT Hub z dostupných předplatných** Pokud už máte existující centrum IoT Hub na jednom z vašich předplatných. Toto je nejjednodušší přístup.
   * Zvolte **nastavení poskytovat služby IoT Hub ručně** při služby IoT Hub je externí ke svým předplatným, nebo chcete zvolit Pokročilá nastavení.

1. Pokud jste vybrali **pomocí služby IoT Hub z dostupných předplatných** možnost, následující tabulka vysvětluje všechny požadované vlastnosti:

   ![IoT Hub čtyři][4]

   | Vlastnost | Popis |
   | --- | --- |
   | ID předplatného | Vyberte předplatné, ve kterém byla vytvořena centra IoT Hub.
   | Název centra IoT | Vyberte název centra IoT.
   | Název zásad centra IoT | Vyberte zásady sdíleného přístupu, který se nachází na kartě nastavení služby IoT Hub. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj událostí *musí* mají **služba připojit** oprávnění.
   | Klíč zásad centra IoT | Klíč je předem.
   | Skupina uživatelů centra IoT | Skupina uživatelů ke čtení události ze služby IoT Hub. Důrazně doporučujeme používat vyhrazenou skupinu spotřebitelů pro váš zdroj událostí.
   | Formát serializace události | JSON je k dispozici pouze serializace v současné době. Zprávy událostí musí být v tomto formátu, nebo mohou být přečteny žádná data. |
   | Název vlastnosti časového razítka | Chcete-li tuto hodnotu určit pochopit formátu dat zprávy odeslané do služby IoT Hub. Tato hodnota je **název** vlastnosti specifické události v datech zprávu, kterou chcete použít jako časové razítko události. Hodnota je velká a malá písmena. Pokud nevyplníte, **čas zařazení události** v rámci události zdroj se používá jako časové razítko události. |

1. Pokud jste vybrali **nastavení poskytovat služby IoT Hub ručně** možnost, následující tabulka vysvětluje všechny požadované vlastnosti:

   | Vlastnost | Popis |
   | --- | --- |
   | ID předplatného | Předplatné, ve které byla vytvořena služby IoT Hub.
   | Skupina prostředků | Název skupiny prostředků, ve které byla vytvořena služby IoT Hub.
   | Název centra IoT | Název služby IoT Hub. Pokud jste vytvořili službu IoT Hub, dáte mu taky určitý název.
   | Název zásad centra IoT | Zásady sdíleného přístupu, které se dají vytvořit na kartě nastavení služby IoT Hub. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj událostí *musí* mají **služba připojit** oprávnění.
   | Klíč zásad centra IoT | Sdílený přístupový klíč používaný k ověření přístupu k oboru názvů služby Service Bus. Primární nebo sekundární klíč zadejte sem.
   | Skupina uživatelů centra IoT | Skupina uživatelů ke čtení události ze služby IoT Hub. Důrazně doporučujeme používat vyhrazenou skupinu spotřebitelů pro váš zdroj událostí.
   | Formát serializace události | JSON je k dispozici pouze serializace v současné době. Zprávy událostí musí být v tomto formátu, nebo mohou být přečteny žádná data. |
   | Název vlastnosti časového razítka | Chcete-li tuto hodnotu určit pochopit formátu dat zprávy odeslané do služby IoT Hub. Tato hodnota je **název** vlastnosti specifické události v datech zprávu, kterou chcete použít jako časové razítko události. Hodnota je velká a malá písmena. Pokud nevyplníte, **čas zařazení události** v rámci události zdroj se používá jako časové razítko události. |

1. Přidáte vyhrazenou TSI příjemce názvu skupiny, kterou jste přidali do služby IoT Hub.

1. Vyberte **vytvořit** přidat nový zdroj událostí.

   ![Centrum IoT pět][5]

1. Po vytvoření zdroje událostí začne Time Series Insights automaticky streamovat data do vašeho prostředí.

## <a name="next-steps"></a>Další postup

* [Definovat zásady přístupu k datům](time-series-insights-data-access.md) zabezpečit data.
* [Odesílání událostí](time-series-insights-send-events.md) ke zdroji události.
* Přístup k prostředí na [Průzkumníka služby Time Series Insights](https://insights.timeseries.azure.com).

<!-- Images -->
[1]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png
[2]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png
[3]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png
[4]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png
[5]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png