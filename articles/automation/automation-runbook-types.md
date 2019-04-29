---
title: Typy Runbooků ve službě Azure Automation
description: 'Popisuje různé typy sad runbook, které můžete v Azure Automation a důležité informace, které byste měli vzít v úvahu při určování typ. '
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7a3990366814beda83852fc7c07c896445388c23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737155"
---
# <a name="azure-automation-runbook-types"></a>Typy runbooků Azure Automation

Azure Automation podporuje několik typů sad runbook, které jsou popsány v následující tabulce.  Následující části obsahují další informace o jednotlivých typech, včetně posouzení toho, kdy použít.

| Type | Popis |
|:--- |:--- |
| [Grafický](#graphical-runbooks)|Na základě prostředí Windows PowerShell a vytvořené a upravené zcela v grafickém editoru na webu Azure portal. |
| [Grafický Powershellový pracovní postup](#graphical-runbooks)|Podle pracovního postupu prostředí Windows PowerShell a vytvoří a upravují výhradně v grafickém editoru na webu Azure portal. |
| [PowerShell](#powershell-runbooks) |Textového runbooku, který založené na skriptu prostředí Windows PowerShell. |
| [Pracovní postup PowerShellu](#powershell-workflow-runbooks)|Textového runbooku, který podle pracovního postupu prostředí Windows PowerShell. |
| [Python](#python-runbooks) |Textového runbooku, který založeno na Pythonu. |

## <a name="graphical-runbooks"></a>Grafické runbooky

[Grafické](automation-runbook-types.md#graphical-runbooks) a grafický Powershellový pracovní postup sady runbook vytvářejí se a upravují v grafickém editoru na webu Azure Portal.  Můžete je exportovat do souboru a následně je importovat do jiného účtu automation. Ale nejde vytvořit nebo upravit pomocí jiného nástroje. Grafické runbooky generování kódu Powershellu, ale nelze přímo zobrazit nebo upravit kód. Grafické runbooky se nedá převést na jeden z [textových formátů](automation-runbook-types.md), ani lze sadu runbook text převést na grafický formát. Grafické runbooky lze převést na sady runbook grafický Powershellový pracovní postup při importu a naopak.

### <a name="advantages"></a>Výhody

* Vložit odkaz Konfigurovat vytváření modelu Visual  
* Zaměřte se na tok dat v rámci procesu  
* Vizuálně reprezentují procesů správy  
* Zahrnout další sady runbook jako podřízené sady runbook vysoké úrovně pracovní postupy  
* Může vést ke vzniku modulární programování  

### <a name="limitations"></a>Omezení

* Nelze upravit runbook mimo web Azure portal.
* Může vyžadovat aktivitě s kódem, kódem Powershellu provádět komplexní logiku.
* Nelze zobrazit nebo upravit přímo kódem Powershellu, který je vytvořen pomocí grafického pracovního postupu. Můžete zobrazit kód, který můžete vytvořit libovolný kód aktivity.
* Nelze spustit v systému Linux Hybrid Runbook Worker

## <a name="powershell-runbooks"></a>Powershellové runbooky

Powershellové runbooky jsou založené na prostředí Windows PowerShell.  Přímo upravovat kód sady runbook pomocí textového editoru na webu Azure Portal.  Můžete také použít libovolný editor offline text a [naimportujte sadu runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Implementujte všechny komplexní logiku s kódem Powershellu bez dalších složitostí pracovního postupu Powershellu.
* Runbook spouští rychleji než runbooky pracovních postupů Powershellu, protože není nutné sestavit před spuštěním.
* Můžete spustit v Azure nebo v Linuxu i Windows Hybrid Runbook Worker

### <a name="limitations"></a>Omezení

* Musí být znalost skriptování Powershellu.
* Nelze použít [paralelní zpracování](automation-powershell-workflow.md#parallel-processing) spustit paralelně několik akcí.
* Nelze použít [kontrolní body](automation-powershell-workflow.md#checkpoints) pokračování sady runbook, pokud dojde k chybě.
* Runbooky pracovních postupů Powershellu a grafické runbooky pouze lze vložit jako podřízené sady runbook pomocí rutiny Start-AzureAutomationRunbook vytvoří novou úlohu.

### <a name="known-issues"></a>Známé problémy

Toto jsou aktuální známé problémy s Powershellovými runbooky.

* Powershellové runbooky nejde načíst nezašifrované [variabilní prostředek](automation-variables.md) s hodnotou null.
* Nelze načíst Powershellové runbooky [variabilní prostředek](automation-variables.md) s *~* v názvu.
* Get-Process ve smyčce v Powershellu runbook může chybově ukončit po přibližně 80 iteracích.
* Powershellový runbook může selhat, pokud se pokusí o zapisovat velké množství dat najednou do výstupního datového proudu.   Tento problém můžete obejít obvykle ve výstupu pouze informace, které potřebujete při práci s velké objekty.  Například místo výstupu něco jako *Get-Process*, můžete výstup jenom do požadovaných polí *Get-Process | Vyberte název procesu, procesoru*.

## <a name="powershell-workflow-runbooks"></a>Runbooky pracovních postupů Powershellu

Runbooky pracovních postupů Powershellu jsou textové runbooky vycházející [pracovního postupu prostředí Windows PowerShell](automation-powershell-workflow.md).  Přímo upravovat kód sady runbook pomocí textového editoru na webu Azure Portal.  Můžete také použít libovolný editor offline text a [naimportujte sadu runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Implementujte všechny komplexní logiku s kódem pracovního postupu Powershellu.
* Použití [kontrolní body](automation-powershell-workflow.md#checkpoints) pokračování sady runbook, pokud dojde k chybě.
* Použití [paralelní zpracování](automation-powershell-workflow.md#parallel-processing) umožňuje provádět více akcí souběžně.
* Jako podřízené sady runbook vysoké úrovně pracovní postupy mohou zahrnovat jiné grafické runbooky a runbooky pracovních postupů Powershellu.

### <a name="limitations"></a>Omezení

* Autor musí být obeznámeni s pracovního postupu Powershellu.
* Sady Runbook musí čelit další složitosti pracovních postupů Powershellu, jako [deserializovat objekty](automation-powershell-workflow.md#code-changes).
* Sada Runbook provede delší dobu než sady runbook Powershellu, protože musí být zkompilovány před spuštěním.
* Powershellové runbooky může být pouze zahrnuta jako podřízené sady runbook pomocí rutiny Start-AzureAutomationRunbook vytvoří novou úlohu.
* Nelze spustit v systému Linux Hybrid Runbook Worker

## <a name="python-runbooks"></a>Sady runbook Pythonu

Runbook Python kompilaci Python 2.  Můžete přímo upravit kód sady runbook pomocí textového editoru na webu Azure Portal nebo pomocí offline textového editoru a [naimportujte sadu runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Využijte robustní knihoven Pythonu.
* Můžete spustit v Azure nebo v obou Linux Hybrid Runbook Worker. Procesy Hybrid Runbook Worker Windows podporují [python2.7](https://www.python.org/downloads/release/latest/python2) nainstalované.

### <a name="limitations"></a>Omezení

* Musí být obeznámeni s skriptů Pythonu.
* Pouze Python 2 se podporuje v tuto chvíli, což znamená, že konkrétní funkce Python 3 se nezdaří.
* Chcete-li použít knihovny třetích stran, musíte [importu balíčku ho](python-packages.md) do účtu Automation, který se má použít.

## <a name="considerations"></a>Požadavky

Vezměte v úvahu následující další aspekty při určování, který typ použít konkrétní sady runbook.

* Sady runbook nelze převést z grafického textový typ nebo naopak.
* Existují omezení pomocí runbooků různých typů jako podřízené sady runbook. Další informace najdete v tématu [podřízené runbooky ve službě Azure Automation](automation-child-runbooks.md).

## <a name="next-steps"></a>Další postup

* Chcete-li další informace o vytváření grafického runbooku, přečtěte si téma [vytváření grafického obsahu ve službě Azure Automation](automation-graphical-authoring-intro.md)
* Chcete-li pochopit rozdíly mezi prostředí PowerShell a prostředí PowerShell pracovních postupů pro sady runbook, naleznete v tématu [pracovním postupu Windows Powershellu](automation-powershell-workflow.md)
* Další informace o tom, jak vytvořit nebo importovat Runbook najdete v tématu [vytvoření nebo import Runbooku](manage-runbooks.md)

