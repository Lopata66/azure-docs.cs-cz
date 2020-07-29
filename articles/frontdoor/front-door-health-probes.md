---
title: Sledování stavu back-endu pro Azure front-endu | Microsoft Docs
description: Tento článek vám pomůže pochopit, jak přední dveře Azure monitorují stav back-endu.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: e2e656c395f1a31c1f5ebbd46d5a18a046f854f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "79471570"
---
# <a name="health-probes"></a>Sondy stavu

Aby bylo možné určit stav a blízkost každého back-endu z určitého prostředí front-endu, každé prostředí front-endu pravidelně odesílá do každého nakonfigurovaného back-endu syntetickou žádost HTTP/HTTPS. Služba Front Door pak s využitím odpovědí z těchto sond určí nejlepší back-endy, do kterých má směrovat skutečné požadavky klientů. 

> [!WARNING]
> Vzhledem k tomu, že přední dvířka mají globálně mnoho hraničních prostředí, může být objem požadavků na vaše back-endy poměrně vysoký od 25 požadavků každou minutu až po 1200 požadavků za minutu, v závislosti na konfiguraci frekvence sond stavu. S výchozí frekvencí sondy 30 sekund by měl mít svazek sondy na back-endu přibližně 200 požadavků za minutu.

## <a name="supported-protocols"></a>Podporované protokoly

Přední dvířka podporují odesílání sond přes protokoly HTTP nebo HTTPS. Tyto sondy se odesílají přes stejné porty TCP, jaké jsou nakonfigurované pro směrování požadavků klientů. Toto chování není možné přepsat.

## <a name="supported-http-methods-for-health-probes"></a>Podporované metody HTTP pro sondy stavu

Přední dvířka podporují následující metody protokolu HTTP pro odesílání sond stavu:

1. **Získat:** Metoda GET znamená načtení jakýchkoli informací (ve formě entity), které jsou identifikovány identifikátorem požadavku-URI.
2. **Pozice:** Metoda HEAD je shodná s metodou GET s tím rozdílem, že server nesmí v odpovědi vracet tělo zprávy. Pro nové profily front-dveří je ve výchozím nastavení metoda sondy nastavena jako HEAD.

> [!NOTE]
> V případě nižšího zatížení a nákladů na back-endy doporučuje přední dveře použití požadavků hlav pro sondy stavu.

## <a name="health-probe-responses"></a>Odezvy sond stavu

| Odpovědi  | Description | 
| ------------- | ------------- |
| Určování stavu  |  200 OK stavový kód označuje, že back-end je v pořádku. Všechno ostatní je považováno za selhání. Pokud z nějakého důvodu (včetně selhání sítě) neobdrží platná odpověď HTTP, bude test paměti počítán jako selhání.|
| Latence měření  | Latence je čas na zeď měřený od okamžiku těsně před odesláním žádosti o test do okamžiku, kdy obdržíte poslední bajt odpovědi. Pro každý požadavek používáme nové připojení TCP, takže toto měření není posunuté směrem k back-endu se stávajícími teplými připojeními.  |

## <a name="how-front-door-determines-backend-health"></a>Jak přední dveře určují stav back-endu

Přední dveře Azure používají pro určení stavu stejný postup tří kroků v rámci všech algoritmů.

1. Vyloučit zakázané back-endy

2. Vyloučit back-endy s chybami sond stavu:
    * Tento výběr se provádí zobrazením posledních _n_ odpovědí na test stavu. Pokud je v pořádku alespoň _x_ , back-end je považován za dobrý.

    * _n_ se konfiguruje změnou vlastnosti SampleSize v nastavení vyrovnávání zatížení.

    * _x_ se konfiguruje změnou vlastnosti SuccessfulSamplesRequired v nastavení vyrovnávání zatížení.

3. Ze sady bezproblémového back-endu ve fondu back-endu navíc přední dveře měří a udržuje latenci (dobu odezvy) pro každý back-end.


## <a name="complete-health-probe-failure"></a>Dokončit selhání sondy stavu

Pokud sondy stavu selžou u každého back-endu v back-endu fondu, pak přední dveře považují všechny back-endy v pořádku a směruje provoz v kruhovém dotazování napříč všemi.

Jakmile se kterýkoli back-end vrátí do stavu v pořádku, pak přední dveře obnoví normální algoritmus vyrovnávání zatížení.

## <a name="disabling-health-probes"></a>Zakázání sond stavu

Pokud máte ve fondu back-endu jeden back-end, můžete se rozhodnout zakázat sondy stavu, které snižují zatížení back-endu aplikace. I v případě, že máte více back-endu ve fondu back-end, ale pouze jeden z nich je v povoleném stavu, můžete sondy stavu zakázat.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
