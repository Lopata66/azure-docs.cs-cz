---
title: zahrnout soubor
description: zahrnout soubor
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 9/17/2018
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: 167e774b00bcfdb7bc1e26202bd7826f525a6ace
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890398"
---
| Prostředek | Výchozí omezení |
| --- | --- |
| Přední dveře prostředků na předplatné | 100 |
| Front-endu hostitele, včetně vlastních domén pro každý prostředek | 100 |
| Pravidla směrování pro každý prostředek | 100 |
| Pro každý prostředek back-endové fondy | 50 |
| Back-end na fond back-endu | 100 |
| Vzorů cest, které je nutné u pravidla směrování | 25 |
| Pravidla brány firewall na vlastní webové aplikace podle zásad | 10 |
| Zásady brány firewall webových aplikací pro každý prostředek | 100 |

### <a name="timeout-values"></a>Hodnoty časového limitu
#### <a name="client-to-front-door"></a>Klient branou
- Přední dveře má TCP připojení časový limit nečinnosti 61 sekund.

#### <a name="front-door-to-application-backend"></a>Přední dveře k back-endu aplikace
- Pokud je odpověď na odezvu bloku, 200 bude vrácen, pokud / když je obdržena u prvního bloku.
- Po předá požadavek protokolu HTTP back-endu branou počká 30 sekund pro první paket z back-endu, před vrácením klientovi došlo k chybě 503.
- Po přijetí prvního paketu z back-endu branou počká 30 sekund (časový limit nečinnosti) došlo k chybě 503 vrácením klientovi.
- Přední dveře k back-endu časový limit relace TCP je 30 minut.

### <a name="upload--download-data-limit"></a>Nahrání / stažení omezení dat

|  | S blokového kódování (CTE) přenosu | Bez dat protokolu HTTP |
| ---- | ------- | ------- |
| **Stáhnout** | Neexistuje žádné omezení na velikost ke stažení | Neexistuje žádné omezení na velikost ke stažení |
| **Nahrávání** |  Bez omezení, dokud každý CTE nahrát je menší než 28.6 MB | Velikost nesmí být větší než 28.6 MB. |
