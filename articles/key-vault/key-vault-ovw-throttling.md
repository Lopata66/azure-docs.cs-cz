---
title: Doprovodné materiály k omezování služby Azure Key Vault
description: Omezení služby Key Vault omezuje počet souběžných volání, aby se zabránilo nadměrnému využití prostředků.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: bryanla
ms.openlocfilehash: 4906be4dc6315d8b4dd3c1e640b40caec28b7743
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301996"
---
# <a name="azure-key-vault-throttling-guidance"></a>Doprovodné materiály k omezování služby Azure Key Vault

Omezení šířky pásma je proces, který spustíte funkci, která omezuje počet souběžných volání do služby Azure, aby se zabránilo nadměrnému využití prostředků. Azure Key Vaultu (AKV) je navržená k velkému počtu požadavků. Pokud dojde k příliš velkého počtu požadavků, omezování požadavků vašeho klienta pomáhá udržet optimální výkon a spolehlivost služby službou AZURE.

Omezení se liší v závislosti na scénáři. Například pokud provádíte velké objemy zápisy, možnost pro omezení je vyšší než pokud pouze provádíte čtení.

## <a name="how-does-key-vault-handle-its-limits"></a>Jak Key Vault zpracovává jeho omezení?

Existují omezení služby ve službě Key Vault k zabránění zneužití prostředky a zajistit kvalitu služby pro všechny klienty služby Key Vault. Při překročení prahové hodnoty služby Key Vault omezení všechny další žádosti od tohoto klienta pro určitou dobu. Pokud k tomu dojde, Key Vault vrátí stavový kód HTTP 429 (příliš mnoho požadavků), a neúspěšné požadavky. Navíc neúspěšné požadavky, které vracejí 429 počet směrem k limitech omezení sledován pomocí funkce služby Key Vault. 

Pokud budete mít obchodní případ pro vyšší omezení limity, kontaktujte nás prosím.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Omezení vaší aplikace v reakci na omezení služby

Následují **osvědčené postupy** pro omezení vaší aplikace:
- Snížit počet operací na požadavek.
- Snižte frekvenci požadavků.
- Vyhněte se okamžité opakování. 
    - Všechny požadavky kumulovat proti vaší limity využití.

Při implementaci vaší aplikace pro zpracování chyb, použijte k detekci potřebu omezování na straně klienta kód chyby protokolu HTTP 429. Pokud požadavek selže s kódem chyby HTTP 429 znovu, že máte stále omezení služby Azure. Dál používat doporučené straně klienta omezování metoda, opakování žádosti, dokud nebude úspěšný.

### <a name="recommended-client-side-throttling-method"></a>Doporučenou metodou omezování na straně klienta

Na kód chyby protokolu HTTP 429 začněte omezování klienta pomocí exponenciálního omezení rychlosti přístupu:

1. Počkejte 1 sekundu, opakování žádosti
2. Pokud i nadále omezený počkejte 2 sekundy, zkuste žádost
3. Pokud i nadále omezený čekání 4 sekundy opakujte požadavek
4. Pokud i nadále omezený čekání 8 sekund opakujte požadavek
5. Pokud i nadále omezený čekání 16 sekund opakujte požadavek

V tomto okamžiku jste by neměly zobrazovat kódy odpovědí protokolu HTTP 429.

## <a name="see-also"></a>Další informace najdete v tématech

Lepší orientaci omezení využití sítě v cloudu Microsoftu, najdete v části [model omezení využití sítě](https://docs.microsoft.com/azure/architecture/patterns/throttling).

