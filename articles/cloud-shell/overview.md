---
title: Přehled služby Azure Cloud Shell | Dokumentace Microsoftu
description: Přehled služby Azure Cloud Shell.
services: ''
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: 5608b3e0f9b98db62d22245de5a864f757f48799
ms.sourcegitcommit: f4469b7bb1f380bf9dddaf14763b24b1b508d57c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "60199644"
---
# <a name="overview-of-azure-cloud-shell"></a>Přehled služby Azure Cloud Shell
Azure Cloud Shell je interaktivní, prohlížeč přístupné prostředí pro správu prostředků Azure.
Poskytuje možnost vybrat si prostředí, která nejlépe vyhovuje stylu vaší práce.
Uživatelé Linuxu si mohou vybrat Bash. Uživatelé Windows se mohou rozhodnout pro PowerShell.

Zkuste z shell.azure.com kliknutím níže.

[![Vložit spuštění](https://shell.azure.com/images/launchcloudshell.png "spustit Azure Cloud Shell")](https://shell.azure.com)

Zkuste z webu Azure portal pomocí ikony Cloud Shell.

![Spuštění portálu](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funkce

### <a name="browser-based-shell-experience"></a>Příkazové prostředí založené na prohlížeči
Cloud Shell umožňuje přístup k založené na prohlížeči prostředí příkazového řádku vytvořené s úkoly správy Azure v úvahu.
Využijte Cloud Shell pro práci untethered z místního počítače způsobem pouze cloud může poskytovat.

### <a name="choice-of-preferred-shell-experience"></a>Volba preferovaného prostředí
Z rozevíracího seznamu prostředí moci uživatelé zvolit mezi Bashe nebo Powershellu.

![Bash ve službě Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell ve službě Cloud Shell](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Ověřený a nakonfigurovanou Azure pracovní stanice
Cloud Shell se spravuje přes Microsoft proto jde o s oblíbenými nástroji příkazového řádku a podpora jazyků. Cloud Shell také bezpečně ověří automaticky pro okamžitý přístup k vašim prostředkům prostřednictvím rutin prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure.

Projděte si kompletní [seznam nástrojů, které jsou nainstalované ve službě Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Integrovaný editor Cloud Shell
Cloud Shell nabízí integrované grafické text editor založený na editoru Monaco open source. Jednoduše vytvořte a upravte konfigurační soubory spuštěním `code .` umožňuje snadné nasazení pomocí Azure Powershellu nebo rozhraní příkazového řádku Azure.

[Další informace o službě Cloud Shell editor](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Integrován webu docs.microsoft.com

Cloud Shell, můžete použít přímo z dokumentace ke službě hostované na [docs.microsoft.com](https://docs.microsoft.com). Je integrován v [Microsoft Learn](https://docs.microsoft.com/learn/), [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) a [dokumentace k Azure CLI](https://docs.microsoft.com/cli/azure) – klikněte na tlačítko "Vyzkoušet" ve fragmentu kódu. Chcete-li otevřít atraktivní prostředí prostředí. 

### <a name="multiple-access-points"></a>Několik přístupových bodů
Cloud Shell je flexibilní nástroj, který je možné z:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Dokumentace k Azure CLI](https://docs.microsoft.com/cli/azure)
* [Dokumentace k Azure Powershellu](https://docs.microsoft.com/powershell/azure/overview)
* [Mobilní aplikace Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Rozšíření sady Visual Studio účtu Azure v kódu](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Připojte své úložiště Microsoft Azure Files
Cloudové prostředí počítače jsou dočasné a vyžadují nové nebo existující soubory Azure sdílenou složku a připojit jako `clouddrive` pro uchovávání souborů.

Při prvním spuštění Cloud Shell zobrazí výzvu k vytvoření prostředku skupiny, účet úložiště a službou soubory Azure sdílet vaším jménem. Toto je jednorázová kroku a automaticky připojí na všechny relace. Sdílení souborů lze mapovat a použije Bash i PowerShell ve službě Cloud Shell.

Přečtěte si další informace o připojení [účtu úložiště nové nebo existující](persisting-shell-storage.md).

## <a name="concepts"></a>Koncepty
* Cloud Shell spouští na dočasné hostiteli k dispozici na relací, jednotlivé uživatele
* Cloud Shell vyprší časový limit po 20 minutách bez interaktivní aktivity
* Cloud Shell vyžaduje sdílenou složku Azure připojit
* Pro Bash i PowerShell cloud Shell používá stejné sdílené složky Azure
* Cloud Shell je přiřazen jeden počítač v rámci uživatelského účtu
* Cloud Shell nevyřeší $HOME pomocí 5 GB image uložené ve sdílené složce
* Oprávnění jsou nastavena jako běžný uživatel Linuxu v prostředí Bash

Další informace o funkcích v [Bash ve službě Cloud Shell](features.md) a [prostředí PowerShell ve službě Cloud Shell](features-powershell.md).

## <a name="pricing"></a>Ceny
Počítače hostujícího Cloud Shell je bezplatné, s předpokladem připojené sdílené složky Azure Files. Náklady na úložiště regulární použít.

## <a name="next-steps"></a>Další postup
[Bash ve službě Cloud Shell rychlý start](quickstart.md) <br>
[PowerShell ve službě Cloud Shell rychlý start](quickstart-powershell.md)
