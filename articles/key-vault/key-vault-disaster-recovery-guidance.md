---
title: Co dělat v případě Azure služby přerušení, které ovlivní Azure Key Vault – Azure Key Vault | Dokumentace Microsoftu
description: Zjistěte, co můžete dělat v případě výpadku služby Azure, který má vliv na služby Azure Key Vault.
services: key-vault
author: barclayn
manager: barbkess
editor: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: barclayn
ms.openlocfilehash: dba1fe91a635f467f4a3aeeaa048897065822869
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236643"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Azure Key Vault dostupnost a redundance

Služba Azure Key Vault nabízí několik vrstev redundance, aby se zajistilo, že vaše klíče a tajné kódy nadále k dispozici aplikace i v případě selhání jednotlivých součástí služby.

Obsah trezoru klíčů se replikují v rámci oblasti a do sekundární oblasti alespoň 150 mil okamžitě, ale ve stejné zeměpisné oblasti. To udržuje vysoká odolnost klíčů a tajných kódů. Zobrazit [spárovaných oblastech Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) dokumentu podrobné informace o párování konkrétních oblastí.

Pokud selžou i jednotlivé komponenty ve službě key vault, alternativní komponenty v rámci oblasti vstoupit poskytovat vaši žádost, abyste měli jistotu, že neexistuje žádné snížení funkčnosti. Není nutné provádět žádnou akci, který spustí. Probíhá automaticky a bude pro vás transparentní.

V nepravděpodobném případě, že není k dispozici celé oblasti Azure, se automaticky směrují požadavky, které jste provedli z Azure Key Vault v dané oblasti (*převzetí služeb při selhání*) do sekundární oblasti. Až primární oblast opět k dispozici, se požadavky směrují zpět (*při navrácení služeb obnoví*) do primární oblasti. Znovu není potřeba provádět žádnou akci, protože k tomu dojde automaticky.

Prostřednictvím tohoto návrhu vysokou dostupnost služby Azure Key Vault nevyžádá žádný výpadek pro činnosti údržby.

Existuje několik upozornění zajímat:

* V případě selhání oblasti může trvat několik minut, než service pro převzetí služeb při selhání. Požadavky odeslané během této doby může selhat, až do dokončení převzetí služeb při selhání.
* Po dokončení převzetí služeb při selhání je váš trezor klíčů není v režimu jen pro čtení. Požadavky, které jsou podporovány v tomto režimu jsou:
  * Seznam trezorů klíčů
  * Získá vlastnosti trezorů klíčů
  * Výpis tajných kódů
  * Získat tajných kódů
  * Vypsat klíče
  * Získání klíčů (Vlastnosti)
  * Šifrování
  * Dešifrování
  * Zalamování řádků
  * Rozbalení
  * Ověřit
  * přihlášení
  * Backup
* Po navrácení převzetí služeb při selhání všech žádostí typy (včetně čtení *a* požadavků na zápis) jsou k dispozici.

