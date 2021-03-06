---
title: Koncepce chatu v komunikačních službách Azure
titleSuffix: An Azure Communication Services concept document
description: Přečtěte si o konceptech chatu komunikačních služeb.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 077500e0188d1cc20864d436a2e2fd711b180702
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560232"
---
# <a name="chat-concepts"></a>Koncepce chatu

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Konverzace klientských knihoven služby Azure Communication Services slouží k přidávání textových konverzací do vašich aplikací v reálném čase. Tato stránka shrnuje klíčové pojmy a možnosti chatu.

Další informace o konkrétních jazycích a funkcích klientské knihovny najdete v tématu [Přehled klientské knihovny služby Communications](./sdk-features.md) .

## <a name="chat-overview"></a>Přehled chatu 

Chatové konverzace se vyskytují v rámci konverzačních vláken. Vlákno konverzace může obsahovat mnoho zpráv a mnoho uživatelů. Každá zpráva patří do jednoho vlákna a uživatel může být součástí jednoho nebo více vláken. 

Každý uživatel ve vlákně chat se nazývá člen. Ve vlákně chatu můžete mít až 250 členů. Pouze členové vlákna mohou odesílat a přijímat zprávy nebo přidávat nebo odebírat členy ve vlákně chatu. Maximální povolená velikost zprávy je přibližně 28KB. Pomocí operace můžete načíst všechny zprávy ve vlákně chatu `List/Get Messages` . Komunikační služby ukládají historii chatů, dokud neprovedete operaci odstranění ve vlákně nebo zprávě chatu nebo dokud žádné členy nezůstanou ve vlákně chatu, kdy je v tom případě nezůstane osamocená a zpracovaná pro odstranění.   

Pro vlákna chatu s více než 20 členy je přečtení potvrzení a zadávání funkcí indikátoru zakázané. 

## <a name="chat-architecture"></a>Architektura chatu

K dispozici jsou dvě základní součásti pro konverzaci: 1) klientská aplikace pro důvěryhodné služby a 2).

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagram znázorňující architekturu chatu komunikačních služeb":::

 - **Důvěryhodná služba:** Aby bylo možné správně spravovat relaci konverzace, potřebujete službu, která vám pomůže se připojit ke komunikačním službám pomocí připojovacího řetězce prostředků. Tato služba zodpovídá za vytváření chatovacích vláken, správu členství v vláknech a poskytování přístupových tokenů uživatelům. Další informace o přístupových tokenech najdete v našem rychlém startu [přístupových tokenů](../../quickstarts/access-tokens.md) .

 - **Klientská aplikace:**  Klientská aplikace se připojí k vaší důvěryhodné službě a získá přístupové tokeny, které se používají pro připojení přímo ke komunikačním službám. Po navázání tohoto připojení může klientská aplikace odesílat a přijímat zprávy.
    
## <a name="message-types"></a>Typy zpráv

Služba Communications Services chat sdílí uživatelem generované zprávy i zprávy generované systémem, které se nazývají **aktivity vláken**. Aktivity vlákna jsou generovány při aktualizaci vlákna chatu. Při volání `List Messages` nebo `Get Messages` v konverzačním vlákně bude výsledek obsahovat textové zprávy generované uživatelem a také systémové zprávy v chronologickém pořadí. To vám pomůže určit, kdy se člen přidal nebo odebral nebo když se aktualizovalo téma konverzačního vlákna. Podporované typy zpráv:  

 - `Text`: Zpráva s prostým textem složená a odeslaná uživatelem jako součást konverzace chatu. 
 - `RichText/HTML`: Naformátovaná textová zpráva. Všimněte si, že uživatelé komunikačních služeb aktuálně nemůžou odesílat zprávy RTF. Tento typ zprávy je podporován zprávami odesílanými od týmů uživatelů ke komunikačním službám ve scénářích spolupráce týmů.
 - `ThreadActivity/AddMember`: Systémová zpráva, která indikuje, že jeden nebo více členů byl přidán do vlákna chatu. Například:

```xml

<addmember>
    <eventtime>1598478187549</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</id>
        <friendlyName>User 1</friendlyName>
    </detailedtargetinfo>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</addmember>

```  

- `ThreadActivity/DeleteMember`: Systémová zpráva, která indikuje, že člen byl odebrán z konverzačního vlákna. Například:

```xml

<deletemember>
    <eventtime>1598478187642</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</deletemember>

```

- `ThreadActivity/MemberJoined`: Systémová zpráva generovaná v případě, že se uživatel typu Host připojí k chatu schůzky. Uživatelé komunikačních služeb se můžou připojit jako host týmu, kteří se účastní chatů. Například:  
```xml
{ 
  "id": "1606351443605", 
  "type": "ThreadActivity/MemberJoined", 
  "version": "1606347753409", 
  "priority": "normal", 
  "content": "{\"eventtime\":1606351443080,\"initiator\":\"8:orgid:8a53fd2b5ef150bau8442ad732a6ac6b_0e8deebe7527544aa2e7bdf3ce1b8733\",\"members\":[{\"id\":\"8:acs:9b665d83-8164-4923-ad5d-5e983b07d2d7_00000006-7ef9-3bbe-b274-5a3a0d0002b1\",\"friendlyname\":\"\"}]}", 
  "senderId": " 19:meeting_curGQFTQ8tifs3EK9aTusiszGpkZULzNTTy2dbfI4dCJEaik@thread.v2", 
  "createdOn": "2020-11-29T00:44:03.6950000Z" 
} 
```
- `ThreadActivity/MemberLeft`: Systémová zpráva generovaná v případě, že uživatel typu Host opustí schůzku v konverzaci. Uživatelé komunikačních služeb se můžou připojit jako host týmu, kteří se účastní chatů. Například: 
```xml
{ 
  "id": "1606347703429", 
  "type": "ThreadActivity/MemberLeft", 
  "version": "1606340753429", 
  "priority": "normal", 
  "content": "{\"eventtime\":1606340755385,\"initiator\":\"8:orgid:8a53fd2b5u8150ba81442ad732a6ac6b_0e8deebe7527544aa2e7bdf3ce1b8733\",\"members\":[{\"id\":\"8:acs:9b665753-8164-4923-ad5d-5e983b07d2d7_00000006-7ef9-3bbe-b274-5a3a0d0002b1\",\"friendlyname\":\"\"}]}", 
  "senderId": "19:meeting_9u7hBcYiADudn41Djm0n9DTVyAHuMZuh7p0bDsx1rLVGpnMk@thread.v2", 
  "createdOn": "2020-11-29T23:42:33.4290000Z" 
} 
```
- `ThreadActivity/TopicUpdate`: Systémová zpráva, která indikuje, že téma bylo aktualizováno. Například:

```xml

<topicupdate>
    <eventtime>1598477591811</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <value>New topic</value>
</topicupdate>

```

## <a name="real-time-signaling"></a>Signalizace v reálném čase 

Klientská knihovna JavaScriptu pro chat zahrnuje signalizaci v reálném čase. To umožňuje klientům naslouchat v reálném čase aktualizace a příchozí zprávy do konverzačního vlákna, aniž by museli dotazovat rozhraní API. K dispozici jsou tyto události:

 - `ChatMessageReceived` – Při odeslání nové zprávy do konverzačního vlákna, ve kterém je uživatel členem. Tato událost se neposílá pro automaticky generované systémové zprávy, které jsme probrali v předchozím tématu.  
 - `ChatMessageEdited` – Když se ve vlákně chatu upraví zpráva, na kterou je uživatel členem. 
 - `ChatMessageDeleted` – Když se ve vlákně chatu odstraní zpráva, na kterou je uživatel členem. 
 - `TypingIndicatorReceived` – když jiný člen zadá zprávu ve vlákně chatu, na které je uživatel členem. 
 - `ReadReceiptReceived` – když jiný člen přečte zprávu, kterou odeslal uživatel ve vlákně chatu. 

## <a name="chat-events"></a>Události chatu 

Signalizace v reálném čase umožňuje uživatelům chatovat v reálném čase. Vaše služby můžou použít Azure Event Grid k přihlášení k odběru událostí souvisejících s chatem. Další podrobnosti najdete v tématu [zpracování událostí – koncepční](../event-handling.md).

## <a name="using-cognitive-services-with-chat-client-library-to-enable-intelligent-features"></a>Povolení inteligentních funkcí pomocí Cognitive Services s využitím klientské knihovny chatu

[Rozhraní API pro rozpoznávání Azure](../../../cognitive-services/index.yml) můžete použít spolu s knihovnou klienta chat k přidání inteligentních funkcí do aplikací. Můžete například:

- Umožněte uživatelům, aby v různých jazycích navzájemly konverzaci. 
- Pomoc agentovi podpory určení priorit lístků pomocí zjištění negativního míněníu příchozího problému od zákazníka.
- Analyzujte příchozí zprávy pro detekci klíčů a rozpoznávání entit a pro uživatele v aplikaci podle obsahu zprávy vyzvat relevantní informace.

Jedním ze způsobů, jak toho dosáhnout, je jednat o členství vaší důvěryhodné služby jako člena konverzačního vlákna. Řekněme, že chcete povolit převod jazyka. Tato služba bude zodpovědná za naslouchání zpráv vyměňovaných jinými členy [1], volání rozhraní API pro rozpoznávání obsahu pro překlad obsahu na požadovaný jazyk [2, 3] a odeslání přeloženého výsledku jako zprávy ve vlákně chatu [4]. 

Historie zpráv tak bude obsahovat původní i přeložené zprávy. V klientské aplikaci můžete přidat logiku pro zobrazení původní nebo přeložené zprávy. V [tomto rychlém](../../../cognitive-services/translator/quickstart-translator.md) startu se naučíte, jak používat rozpoznávání rozhraní API k překladu textu do různých jazyků. 

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagram znázorňující Cognitive Services interakci s komunikačními službami.":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s chatem](../../quickstarts/chat/get-started.md)

Následující dokumenty můžou být zajímavé:

- Seznámení s [knihovnou klienta chat](sdk-features.md)
