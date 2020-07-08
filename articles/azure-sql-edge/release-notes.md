---
title: Poznámky k verzi pro Azure SQL Edge
description: Poznámky k verzi s podrobnostmi o tom, co je nového nebo co se změnilo v obrázcích Azure SQL Edge
keywords: Okraj SQL pro poznámky k verzi
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 06/05/2020
ms.openlocfilehash: d5d60db2cc0c35b908454be3f00716db6d2da945
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361638"
---
# <a name="azure-sql-edge-release-notes"></a>Poznámky k verzi Azure SQL Edge 

Tento článek popisuje, co je nového a co se změnilo u každého nového buildu Azure SQL Edge.

## <a name="ctp-21"></a>CTP 2,1 
### <a name="sql-engine-build-number---15020001545"></a>Číslo sestavení pro modul SQL Engine – 15.0.2000.1545
### <a name="fixes"></a>Opravy
1. Opravte předpověď s ONNX modely pro zpracování potíží s CPUID v ARM. 
2. Oprava pro zlepšení zpracování cesty selhání při spuštění streamování TSQL 
3. Pokud nejsou k dispozici žádná data, opravte nesprávnou hodnotu prodlevy vodoznaku v metrikách úlohy. 
4. Vyřešte problém s výstupním adaptérem, pokud adaptér obsahuje schéma proměnných mezi dávkami.  

## <a name="ctp-20"></a>CTP 2,0 
### <a name="sql-engine-build-number---15020001401"></a>Číslo sestavení pro modul SQL Engine – 15.0.2000.1401
### <a name="whats-new"></a>Co je nového?
1.  Název produktu se aktualizoval na Azure SQL Edge.
1.  Date_bucket funkce

    i.  Podpora pro datum, čas, typ DateTime
3.  PŘEDPOVĚĎ pomocí ONNX
    
    i.  Pro ONNX se vyžaduje parametr modulu RUNTIME. 
    
4.  Podpora streamování TSQL (omezená verze Preview) 
 
### <a name="known-issues"></a>Známé problémy

1. <b>Problém:</b> Potenciální chyby s použitím DACPAC při spuštění kvůli problému s časováním.

    <b>Alternativní řešení:</b> Restartování SQL Server nebo kontejneru se znovu pokusí použít DACPAC a tento problém by se měl vyřešit.
### <a name="request-support"></a>Požádat o podporu
1. Na [stránce podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)můžete požádat o podporu.

4. Ujistěte se, že jsou vybrána následující pole: 
    * Typ problému – technický 
    * Služba – IoT Edge
    * Typ problému – můj problém má vztah k IoT Edge modulu
    * Podtyp problému – Azure SQL Edge

   ![Vzorový lístek podpory](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1,5
### <a name="sql-engine-build-number---15020001331"></a>Číslo sestavení pro modul SQL Engine – 15.0.2000.1331
### <a name="whats-new"></a>Co je nového?
1. Date_bucket funkce
    
    i. Podpora pro typ DateTimeOffset
2. Předpověď s ONNX modely

    i. Podpora nvarchar
 
## <a name="ctp-14"></a>CTP 1,4
### <a name="sql-engine-build-number---15020001247"></a>Číslo sestavení pro modul SQL Engine – 15.0.2000.1247
### <a name="whats-new"></a>Co je nového?
1.  Předpověď s ONNX modely
 
    i.  Varchar – podpora
    
    ii. Migrace na modul runtime ONNX verze 1,0 
2.  Podpora funkcí – jsou povolené následující funkce:

    i.   Podpora funkce CDC

    ii.  Tabulka historie s kompresí

    iii. Vysoký faktor škálování pro čtení protokolu předem

    iv.  Režim dávky ES pro filtrování směrem dolů

    v.   Optimalizace před čtením
 
## <a name="ctp-13"></a>CTP 1,3
### <a name="sql-engine-build-number---15020001147"></a>Číslo sestavení pro modul SQL Engine – 15.0.2000.1147
### <a name="whats-new"></a>Co je nového?
1. Nasazení portálu Azure IOT Portal 

    i.   Podpora pro nasazování imagí AMD64 a ARM

    ii.  Podpora vytváření úloh streamování

    iii. Nasazení DACPAC
2. Předpověď s ONNX modely

    i. Podpora číselného typu
3. Podpora funkcí – jsou povolené následující funkce:

    i.  Prohledávání směrem dolů do úložiště sloupců

    ii. Šťastné a kulaté kontroly
4. Omezení využití paměti a paměti
