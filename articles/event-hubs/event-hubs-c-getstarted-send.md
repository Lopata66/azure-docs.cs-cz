---
title: Odesílání událostí pomocí jazyka C - Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek poskytuje návod pro vytvoření aplikace v jazyce C, která zasílá události do služby Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6cb1f788f41fe07516d759b177e1d76405dd2bf8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57529701"
---
# <a name="send-events-to-azure-event-hubs-using-c"></a>Odesílání událostí do služby Azure Event Hubs pomocí jazyka C

## <a name="introduction"></a>Úvod
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

Tento kurz popisuje, jak odesílat události do centra událostí pomocí konzolové aplikace v jazyce C. 

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu potřebujete:

* Vývojové prostředí jazyka C. Tento kurz předpokládá gcc zásobníku na virtuálním počítači Azure s Linuxem s Ubuntu 14.04.
* [Microsoft Visual Studio](https://www.visualstudio.com/).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Vytvoření oboru názvů Event Hubs a centra událostí
Prvním krokem je použití webu [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centra událostí, postupujte podle pokynů v [v tomto článku](event-hubs-create.md).

Získání hodnoty přístupový klíč pro Centrum událostí podle pokynů v článku: [Získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). V kódu, který napíšete později v tomto kurzu použijete přístupový klíč. Je výchozí název klíče: **RootManageSharedAccessKey**.

Nyní postupujte podle následující kroků v tomto kurzu.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Napsání kódu pro odesílání zpráv do služby Event Hubs
V této části ukazuje, jak psát aplikace v jazyce C k odesílání událostí do vašeho centra událostí. Kód používá knihovnu kanálem AMQP od [projektu Apache Qpid](https://qpid.apache.org/). To je obdobou pomocí front a témat Service Bus pomocí protokolu AMQP from C, jak je znázorněno [v této ukázce](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Další informace najdete v tématu [Qpid kanálem dokumentaci](https://qpid.apache.org/proton/index.html).

1. Z [stránky Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html), postupujte podle pokynů k instalaci Qpid kanálem v závislosti na vašem prostředí.
2. Pro kompilaci kanálem knihovny, nainstalujte následující balíčky:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Stáhněte si [Qpid kanálem knihovny](https://qpid.apache.org/proton/index.html)a rozbalte ho, například:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Vytvořte adresář sestavení, kompilace a instalace:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. Pracovní adresář, vytvořte nový soubor s názvem **sender.c** následujícím kódem. Nezapomeňte nahradit hodnoty nebo název klíče SAS, název centra událostí a obor názvů. Musíte taky nahradit kódovaná adresou URL verzi klíče pro **SendRule** vytvořili dříve. Můžete kódování URL ho [tady](https://www.w3schools.com/tags/ref_urlencode.asp).
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Zkompilujte soubor, za předpokladu, že **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Tento kód používá odchozí okna 1 přinutit zpráv si co nejdříve. Doporučuje se, že vaše aplikace pokusí zprávy dávkových chcete zvýšit propustnost. Najdete v článku [stránky Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html) informace o tom, jak použít knihovnu Qpid kanálem v tomto a dalších prostředích a z platforem, pro které jsou k dispozici vazby (aktuálně Perl, PHP, Python a Ruby).

Spuštění aplikace pro odesílání zpráv do centra událostí. 

Blahopřejeme! Nyní jste odeslali zprávy do centra událostí.

## <a name="next-steps"></a>Další postup
Další informace o přijímání událostí z centra událostí, klikněte na příslušný přijímající jazyk v rámci **přijímat události z centra událostí** uzel v obsahu.


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
