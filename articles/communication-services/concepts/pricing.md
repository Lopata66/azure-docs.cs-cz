---
title: Cenové scénáře pro volání (hlasové a video) a chat
titleSuffix: An Azure Communication Services concept document
description: Přečtěte si o cenovém modelu komunikačních služeb.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 779fa577b25bd4f2aa92aa8b8cc1244a58bdf1ae
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126186"
---
# <a name="pricing-scenarios"></a>Cenové scénáře

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Ceny za komunikační služby Azure jsou obecně založené na modelu průběžných plateb.

## <a name="voicevideo-calling-and-screen-sharing"></a>Volání hlasu a videa a sdílení obrazovky

Komunikační služby Azure umožňují přidat do vašich aplikací volání hlasu a videa a sdílení obrazovky. Můžete vložit prostředí do svých aplikací pomocí jazyka JavaScript, objektivu: C (Apple), Java (Android) nebo klientských knihoven .NET. Podívejte se na náš [úplný seznam dostupných klientských knihoven](./sdk-options.md).

### <a name="pricing"></a>Ceny

Služby pro volání a sdílení obrazovky se účtují za minutu za účastníka na úrovni $0,004 za účastníka za minutu pro volání skupin. Chcete-li pochopit různé toky volání, které jsou možné, přečtěte si [tuto stránku](./call-flows.md).

Každý účastník volání se bude počítat za každou minutu, ke které jsou připojeny ke volání. To má hodnotu true bez ohledu na to, zda uživatel používá audiovizuální volání, volání hlasu nebo sdílení obrazovky.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>Příklad ceny: rozdělit zvuk/video volání pomocí klientských knihoven JS a iOS

Alice provedla volání skupiny s kolegy, Bobem a Charlie. Alice a Bob použily klientské knihovny JS, Charlie klientské knihovny iOS.

- Volání trvá celkem 60 minut.
- Alice a Bob se účastnily celého volání. Alice zapnula své video po dobu pěti minut a Nasdílela obrazovku po dobu 23 minut. U Boba bylo video zapnuté pro celé volání (60 minut) a sdílená obrazovka po dobu 12 minut.
- Charlie opustí volání po 43 minutách. Charlie využití zvuku a videa po dobu, po kterou se účastnila (43 minut).

**Výpočty nákladů**

- 2 účastníci × 60 minut × $0,004 za účastníka za minutu = $0,48 [video i zvuk se účtují stejnou sazbou.]
- 1 účastník x 43 minut × $0,004 za účastníka za minutu = $0,172 [video i zvuk se účtují stejnou sazbou.]

**Celkové náklady na volání skupiny**: $0,48 + $0,172 = $0,652

## <a name="chat"></a>Chat

Pomocí komunikačních služeb můžete rozšířit aplikaci o možnost odesílat a přijímat zprávy chatu mezi 2 nebo více uživateli. Chat pro klientské knihovny jsou k dispozici pro jazyky JavaScript, .NET, Python a Java. [Další informace o knihovnách klienta najdete na této stránce](./sdk-options.md) .

### <a name="price"></a>Cena

Za každou odeslanou zprávu chatu se vám bude účtovat $0,0008.

### <a name="pricing-example-chat-between-two-users"></a>Příklad ceny: chat mezi dvěma uživateli 

Geeta spustí chatovací vlákno s Emily ke sdílení aktualizace a pošle 5 zpráv. Chat trvá 10 minut na Geeta a Emily odeslání dalších 15 zpráv.

**Výpočty nákladů** 
- Počet odeslaných zpráv (5 + 15 + 15) × $0,0008 = $0,028

### <a name="pricing-example-group-chat-with-multiple-users"></a>Příklad ceny: seskupení chatu s více uživateli 

Charlie spustí chatovací vlákno se svými přáteli Casey & jednotek Jasmine k naplánování dovolené. Při současném chatování Charlie, Casey & jednotek Jasmine odesílají 20, 30 a 18 zpráv. Uvědomují si, že jejich přítel růže může být zajímat se i na cestách, aby se připojili ke konverzačnímu vláknu a aby s ní sdílel veškerou historii zpráv. 

Tato zpráva se zobrazí a spustí se konverzace. V mezitím Casey získá hovor a rozhodne o tom, aby se v konverzaci později mohl zachytit. Charlie, jednotek Jasmine & růže na data o cestování a pošle další 30, 25 a 35 zpráv.

**Výpočty nákladů** 

- Počet odeslaných zpráv (20 + 30 + 18 + 30 + 25 + 35) x $0,0008 = $0,1264


## <a name="telephony-and-sms"></a>Telefonie a SMS

## <a name="price"></a>Cena 

Poplatky za telefonní služby se účtují po minutách, zatímco SMS se účtuje na základě jednotlivých zpráv. Ceny se určují podle typu a umístění čísla, které používáte, i cíle volání a SMS zpráv.

### <a name="telephone-number-leasing"></a>Leasing telefonního čísla

Poplatky za telefonní číslo se účtují předem a pak se opakují na základě měsíce na měsíc:

|Typ čísla   |Měsíční poplatek   |
|--------------|-----------|
|Místní (USA)     |$1/měsíc        |
|Bezplatná linka (USA) |$2/mo |


### <a name="telephone-calling"></a>Telefonní volání

Tradiční telefonní volání (volání, ke kterému dochází přes telefonickou síť v rámci veřejné sítě) je dostupné s cenami průběžných plateb pro telefonní čísla založená na USA. Cena je sazba za minutu na základě typu použitého čísla a cíle volání. V následující tabulce jsou uvedeny podrobnosti o cenách nejoblíbenějších volajících cílů. Úplný seznam cílů najdete v [seznamu s podrobnými cenami](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv) .


#### <a name="united-states-calling-prices"></a>USA ceny za volání

Následující ceny zahrnují požadavky na komunikaci a poplatky až do 30. června 2021:

|Typ čísla   |Chcete-li provést volání   |Pro příjem volání|
|--------------|-----------|------------|
|Místní     |Začíná na $0.013/min.       |$0.0085/min        |
|Bezplatná linka |$0.013/min   |$0.0220/min |

#### <a name="other-calling-destinations"></a>Jiné cíle volání

Následující ceny zahrnují požadavky na komunikaci a poplatky až do 30. června 2021:

|Volat na   |Cena za minutu|
|-----------|------------|
|Kanada     |Začíná na $0.013/min.   |
|Spojené království     |Začíná na $0,015/min.   |
|Německo     |Začíná na $0,015/min.   |
|Francie     |Začíná na $0,016/min.   |


### <a name="sms"></a>SMS

SMS nabízí ceny průběžných plateb. Cena je poplatek za zprávu na základě cíle zprávy. Zprávy mohou být zasílány pomocí telefonních čísel bez poplatků na telefonní čísla umístěná v rámci USA. Všimněte si, že místní (geografické) telefonní čísla nelze použít k zasílání zpráv SMS.

Následující ceny zahrnují požadavky na komunikaci a poplatky až do 30. června 2021:

|Země   |Odesílání zpráv|Příjem zpráv|
|-----------|------------|------------|
|USA (bez poplatků)    |$0.0075/MSG   | $0.0075/MSG |
