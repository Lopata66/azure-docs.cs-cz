---
title: Vypršení platnosti dat v Azure Cosmos DB s časem až Live
description: Pomocí TTL Microsoft Azure Cosmos DB poskytuje možnost mít po určitou dobu automaticky vyčištěné dokumenty ze systému.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.reviewer: sngun
ms.openlocfilehash: c504e2f574970142942945de5a0a9fb409bb166b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498299"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Hodnota TTL (Time to Live) v Azure Cosmos DB 

S hodnotou **Time to Live** nebo TTL Azure Cosmos DB poskytuje možnost automaticky odstraňovat položky z kontejneru po určitém časovém období. Ve výchozím nastavení můžete nastavit možnost čas na živý na úrovni kontejneru a přepsat hodnotu na základě jednotlivých položek. Jakmile nastavíte hodnotu TTL v kontejneru nebo na úrovni položky, Azure Cosmos DB tyto položky po uplynutí časového období automaticky odebrat od doby, kdy byla naposledy změněna. Hodnota TTL (Time to Live) se nakonfiguruje během několika sekund. Když nastavíte hodnotu TTL, systém automaticky odstraní položky s vypršenou platností na základě hodnoty TTL bez nutnosti operace odstranění, která je explicitně vystavena klientskou aplikací.

Odstraněním položek, jejichž platnost vypršela, je úloha na pozadí, která využívá [jednotky žádostí o](request-units.md)převzetí služeb při selhání. Jedná se o jednotky požadavků, které nevyužily požadavky uživatelů. I po vypršení platnosti TTL dojde v případě přetížení kontejneru v případě požadavků a v případě, že není k dispozici dostatek RU, odstranění dat je zpožděno. Data se odstraní, jakmile je k dispozici dostatek ru k provedení operace odstranění. I když je odstranění dat zpožděno, data se po vypršení hodnoty TTL nevrátí žádným dotazům (pomocí rozhraní API).

## <a name="time-to-live-for-containers-and-items"></a>Doba provozu pro kontejnery a položky

Hodnota TTL (Time to Live) je nastavena v sekundách a je interpretována jako rozdíl od doby poslední změny položky. Můžete nastavit hodnotu TTL (Time to Live) pro kontejner nebo položku v rámci kontejneru:

1. **Doba do živého kontejneru** (nastaveného pomocí `DefaultTimeToLive`):

   - Pokud chybí (nebo je nastavené na hodnotu null), položky se automaticky nevyprší.

   - Je-li k dispozici a hodnota nastavená na "-1", bude rovna hodnotě nekonečno a položky ve výchozím nastavení nevyprší.

   - Pokud je tato hodnota nastavená na nějaké číslo *"n"* , vyprší *"n"* sekund po datu poslední změny.

2. **Čas do živého pro položku** (nastavenou pomocí `ttl`):

   - Tato vlastnost je platná pouze v případě, že je k dispozici `DefaultTimeToLive` a není pro nadřazený kontejner nastavena na hodnotu null.

   - Je-li k dispozici, Přepisuje hodnotu `DefaultTimeToLive` nadřazeného kontejneru.

## <a name="time-to-live-configurations"></a>Doba do živých konfigurací

* Pokud je hodnota TTL nastavena na *"n"* na kontejneru, pak položky v tomto kontejneru vyprší po *n* sekundách.  Pokud jsou ve stejném kontejneru nějaké položky, které mají svůj vlastní čas v reálném čase, nastavte na hodnotu-1 (což znamená, že nevyprší platnost), nebo pokud některé položky přepsaly nastavení čas na živé s jiným číslem, vyprší platnost těchto položek na základě vlastní nakonfigurované hodnoty TTL. 

* Pokud hodnota TTL není nastavena u kontejneru, nemá čas na živé položky v tomto kontejneru žádný vliv. 

* Pokud je hodnota TTL u kontejneru nastavená na hodnotu-1, vyprší platnost položky v tomto kontejneru, která má hodnotu TTL nastavenou na n. po dobu n sekund vyprší platnost a zbývající položky nebudou vypršet. 

Odstraňování položek na základě hodnoty TTL je zdarma. Neúčtují se žádné další náklady (to znamená, že se nespotřebovává žádné další ru), když se položka odstraní v důsledku vypršení hodnoty TTL.

## <a name="examples"></a>Příklady

V této části jsou uvedeny některé příklady s různými časy pro živé hodnoty přiřazené k kontejneru a položkám:

### <a name="example-1"></a>Příklad 1

Hodnota TTL u kontejneru je nastavená na hodnotu null (DefaultTimeToLive = null).

|Hodnota TTL u položky| Výsledek|
|---|---|
|TTL = null|    Hodnota TTL je zakázána. Platnost položky nebude nikdy vypršet (výchozí nastavení).|
|TTL =-1   |Hodnota TTL je zakázána. Platnost položky nebude nikdy vypršet.|
|TTL = 2000 |Hodnota TTL je zakázána. Platnost položky nebude nikdy vypršet.|


### <a name="example-2"></a>Příklad 2

Hodnota TTL u kontejneru je nastavená na-1 (DefaultTimeToLive =-1).

|Hodnota TTL u položky| Výsledek|
|---|---|
|TTL = null |Hodnota TTL je povolena. Platnost položky nebude nikdy vypršet (výchozí nastavení).|
|TTL =-1   |Hodnota TTL je povolena. Platnost položky nebude nikdy vypršet.|
|TTL = 2000 |Hodnota TTL je povolena. Platnost položky vyprší po 2000 sekundách.|


### <a name="example-3"></a>Příklad 3

Hodnota TTL u kontejneru je nastavená na 1000 (DefaultTimeToLive = 1000).

|Hodnota TTL u položky| Výsledek|
|---|---|
|TTL = null|    Hodnota TTL je povolena. Platnost položky vyprší po 1000 sekundách (výchozí nastavení).|
|TTL =-1   |Hodnota TTL je povolena. Platnost položky nebude nikdy vypršet.|
|TTL = 2000 |Hodnota TTL je povolena. Platnost položky vyprší po 2000 sekundách.|

## <a name="next-steps"></a>Další kroky

Naučte se konfigurovat dobu v provozu v následujících článcích:

* [Jak nakonfigurovat dobu provozu](how-to-time-to-live.md)
