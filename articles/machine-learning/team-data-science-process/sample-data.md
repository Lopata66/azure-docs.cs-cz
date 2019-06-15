---
title: Ukázková data do umístění jiného úložiště Azure - vědecké zpracování týmových dat
description: Ukázková data v Azure kontejnery, SQL Server, objektů blob a tabulky snížit na velikost menší, ale reprezentativní a lépe zvládnutelné Hive.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fc91aec80f74488125649cfe807757ba5ae49c9b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60253104"
---
# <a name="heading"></a>Ukázková data v kontejnerech objektů blob v Azure, SQL Server a v tabulkách Hivu

Následující články popisují, jak ukázková data, která je uložena v jednom ze tří různých umístěních Azure:

* [**Datový kontejner objektů blob v Azure** ](sample-data-blob.md) vzorkovat stahování prostřednictvím kódu programu a potom vzorkování pomocí vzorového kódu Pythonu.
* [**Data systému SQL Server** ](sample-data-sql-server.md) je shromažďovat vzorky pomocí SQL a programovací jazyk Python. 
* [**Data v tabulce Hive** ](sample-data-hive.md) je shromažďovat vzorky pomocí dotazů Hive.

Tato úloha vzorkování je krok [vědecké zpracování týmových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Proč ukázkových dat?**

Pokud je velké datové sady, které chcete analyzovat, je obvykle vhodné na nižší dat ke snížení velikosti menší, ale reprezentativní a lépe zvládnutelné. To usnadňuje pochopení dat, prozkoumávání a vytváření funkcí. Jejich rolí v procesu sady Cortana Analytics je umožnit rychlé vytváření prototypů funkcí pro zpracování dat a modelů strojového učení.

