---
title: Návrh tabulky pokyny pro úložiště Azure | Dokumentace Microsoftu
description: Navrhněte svoji službu Azure table pro efektivní podporovat operace čtení.
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 04/23/2018
ms.date: 12/10/2018
ms.author: v-jay
ms.component: tables
ms.openlocfilehash: d056d29469ad9a60fceeee307aca3c0e1319283c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269829"
---
# <a name="guidelines-for-table-design"></a>Pokyny pro návrh tabulek

Navrhování tabulek pro použití se službou Azure storage tabulky se velmi liší od aspekty návrhu pro relační databáze. Tento článek popisuje pokyny k návrhu řešení pro službu tabulky efektivní číst a zapisovat efektivní.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Navrhněte řešení služby tabulky účinný pro čtení

* ***Návrh pro dotazování aplikace náročné na čtení.*** Při návrhu tabulek, rozmyslete si dotazy (zejména latence citlivé ty), které budou spouštět předtím, než si myslíte o tom, jak budete aktualizovat entity. Obvykle v důsledku efektivní a výkonné řešení.  
* ***Zadejte PartitionKey a rowkey používají v dotazech.*** *Bod dotazy* jako jedná se o nejúčinnější dotazy table service.  
* ***Zvažte uložení duplicitní kopie entity.*** Table storage je levné proto zvažte uložení stejná entita více než jednou (s různými klíči) umožňující účinnější dotazy.  
* ***Vezměte v úvahu denormalizing vaše data.*** Table storage je levné proto zvažte denormalizing vaše data. Například ukládat souhrn entit, aby se dotazy pro agregovaná data, která stačí pro přístup k jedné entity.  
* ***Použijte složené klíčové hodnoty.*** Jsou jenom klíče máte **PartitionKey** a **RowKey**. Například použijte složené klíčové hodnoty a povolit alternativní s klíči přístup cesty k entitám.  
* ***Použijte dotaz projekce.*** Můžete snížit množství dat, která přenos přes síť pomocí dotazů, které vybírají pouze pole, které potřebujete.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Navrhněte řešení služby tabulky účinný zápisu  

* ***Nevytvářejte aktivních oddílů.*** Zvolte klíče, které vám umožní rozdělit mezi několik oddílů v některém okamžiku vašich požadavků.  
* ***Vyhněte se špičkám provozu.*** Provoz uhladil určité přiměřené době a vyhnout se špičkám provozu.
* ***Není nutně vytvořit samostatnou tabulku pro každý typ entity.*** Pokud požadujete atomické transakce mezi typy entit, můžete uložit tyto několik typů entit ve stejném oddílu ve stejné tabulce.
* ***Vezměte v úvahu maximální propustnost, kterou musí dosáhnout.*** Musí být vědomi cíle škálovatelnosti pro služby Table service a ujistěte se, že váš návrh nezpůsobí překročíte je.  

Jak si ji přečtete, zobrazí se příklady, které všechny tyto zásady vložit do praxe. 

## <a name="next-steps"></a>Další postup

- [Vzory návrhu tabulky](table-storage-design-patterns.md)
- [Návrh pro dotazování](table-storage-design-for-query.md)
- [Šifrování dat tabulky](table-storage-design-encrypt-data.md)
- [Návrh pro úpravu dat](table-storage-design-for-modification.md)