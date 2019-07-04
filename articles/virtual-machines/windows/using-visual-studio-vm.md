---
title: Pomocí sady Visual Studio na virtuálním počítači Azure | Dokumentace Microsoftu
description: Na virtuálním počítači Azure pomocí sady Visual Studio.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: cathys
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.custom: vs-azure
ms.workload: azure-vs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.date: 06/24/2019
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 31b856b4f6937b57f0a2b82d94fc0fb02936f83f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449375"
---
# <a name="visual-studio-images-on-azure"></a>Visual Studio Image v Azure
Pomocí sady Visual Studio v předkonfigurovaném Azure virtuální počítač (VM) je rychlý a snadný způsob, jak přejít od ničeho nahoru a spuštění vývojové prostředí. Bitové kopie systému s různými konfiguracemi sady Visual Studio jsou k dispozici v [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure).

Zatím Azure neznáte? [Vytvořte si bezplatný účet Azure](https://azure.microsoft.com/free).

## <a name="what-configurations-and-versions-are-available"></a>Jaké konfigurace a verze jsou k dispozici?
Image pro nejnovější hlavní verze, Visual Studio 2019, Visual Studio 2017 a Visual Studio 2015, najdete na webu Azure Marketplace.  Pro každý vydání hlavní verze, zobrazí původně "vydáno pro web" verzi (RTW) a nejnovější aktualizované verze.  Každá z těchto verzí nabízí edice Visual Studio Community a Visual Studio Enterprise.  Tyto Image jsou aktualizovány alespoň každý měsíc zahrnout nejnovější aktualizace sady Visual Studio a Windows.  Názvy imagí budou i nadále stejná, obsahuje popis každého obrázku nainstalovaný produkt verze a "k" datu image.

| Prodejní verze                                                                                                                                                | Edice              | Verze produktu   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019: Nejnovější verzi (verze 16.1)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Organizace, Community | Verze 16.1.3    |
| [Visual Studio 2019: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019?tab=Overview)                         | Organizace, Community | Verze 16.0.5    |
| [Visual Studio 2017: Nejnovější (verzi 15.9)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)           | Organizace, Community | Verze 15.9.13   |
| [Visual Studio 2017: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)                             | Organizace, Community | Verze 15.0.24   |
| [Visual Studio 2015: Nejnovější (aktualizace 3)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)               | Organizace, Community | Verze 14.0.25431.01 |

> [!NOTE]
> V souladu s Microsoft zásady obsluhy původně vydaná verze (RTW) sady Visual Studio 2015 vypršela pro obsluhu. Visual Studio 2015 Update 3 je jediný zbývající verze nabízí pro produktovou řadu Visual Studio 2015.

Další informace najdete v tématu [zásad údržby aplikace Visual Studio](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Jaké funkce jsou nainstalovány?
Každý image obsahuje funkci doporučené nastavení pro tuto verzi sady Visual Studio. Obecně platí že instalační program obsahuje:

* Všechny dostupné úlohy, včetně Každá úloha doporučuje volitelné součásti
* .NET 4.6.2 a .NET 4.7 vývojářské nástroje, sady SDK a sady Targeting Pack
* Visual F#
* Rozšíření GitHub pro Visual Studio
* Nástroje LINQ to SQL

Příkazový řádek použitý k instalaci sady Visual Studio při sestavování Image je následujícím způsobem:

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

Pokud Image nezahrnují funkce sady Visual Studio, kterou požadujete, poskytnou zpětnou vazbu prostřednictvím nástroje pro zpětnou vazbu v pravém horním rohu stránky.

## <a name="what-size-vm-should-i-choose"></a>Jaké velikosti virtuálních počítačů zvolte
Azure nabízí celou řadu velikostí virtuálních počítačů. Visual Studio je výkonný Vícevláknová aplikace, chcete, aby velikost virtuálního počítače, který obsahuje alespoň dva procesory a 7 GB paměti. Doporučujeme následující velikosti virtuálních počítačů pro bitové kopie sady Visual Studio:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Další informace o nejnovější velikosti počítačů najdete v tématu [velikosti pro Windows virtual machines v Azure](/azure/virtual-machines/windows/sizes).

S Azure můžete obnovit rovnováhu zvoleného počáteční změnou velikosti virtuálního počítače. Můžete zřídit nový virtuální počítač s více odpovídající velikost, nebo změňte velikost existujícího virtuálního počítače na jiný základní hardware. Další informace najdete v tématu [změnit velikost virtuálního počítače s Windows](/azure/virtual-machines/windows/resize-vm).

## <a name="after-the-vm-is-running-whats-next"></a>Jakmile je virtuální počítač spuštěný, co se chystá?
Visual Studio se řídí modelem "používání vlastní licence" v Azure. Stejně jako u instalace na speciální hardware, jednu z prvních kroků je licencování instalaci sady Visual Studio. Visual Studio, buď odemknout:
- Přihlaste se pomocí účtu Microsoft, který je spojen s předplatným Visual Studia 
- Odemknout pomocí kódu product key, které byly dodány s počátečním nákupu sady Visual Studio

Další informace najdete v tématu [přihlášení k Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) a [jak odemknout Visual Studio](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Jak můžu uložit vývojový virtuální počítač pro budoucnost nebo tým použít?

Celé spektrum od vývojových prostředích je obrovský a je skutečné náklady spojené s vytvoření složitější prostředí. Bez ohledu na konfiguraci vašeho prostředí můžete uložit nebo zachytit virtuální počítač nakonfigurovaný jako "základní image" pro budoucí použití nebo pro ostatní členy týmu. Potom při dalším spuštění nového virtuálního počítače, můžete zřídit ze základní image místo image Azure Marketplace.

Rychlý souhrn: Použijte nástroj pro přípravu systému (Sysprep) a vypnout na spuštěný virtuální počítač a pak zachytíte *(obrázek 1)* virtuálního počítače jako bitovou kopii prostřednictvím uživatelského rozhraní na webu Azure Portal. Azure uloží `.vhd` soubor, který obsahuje bitovou kopii v účtu úložiště, které si vyberete. Nová bitová kopie se potom zobrazí jako prostředek obrázku v seznamu prostředků vašeho předplatného.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center> *(Obrázek 1) Zachycení image na webu Azure portal uživatelského rozhraní.* </center>

Další informace najdete v tématu [vytvoření spravované image zobecněného virtuálního počítače v Azure](/azure/virtual-machines/windows/capture-image-resource).

> [!IMPORTANT]
> Nezapomeňte pomocí programu Sysprep k přípravě virtuálního počítače. Pokud tento krok přeskočíte, Azure nemůže zřídit virtuální počítač z bitové kopie.

> [!NOTE]
> I za náklady pro úložiště bitových kopií, ale, že může být neplatné dodatečných nákladů ve srovnání s režijní náklady k opětovnému sestavení virtuálních počítačů od začátku pro každého člena týmu, kteří potřebují jeden. Například to stojí za pár dolarů k vytvoření a uložení image 127 GB po dobu jednoho měsíce, který je opakovaně použitelné celý tým. Tyto náklady jsou však nevýznamné ve srovnání s hodin, po které jednotliví zaměstnanci investuje do sestavení a ověření správně nakonfigurovanou vývojovém pro vlastní použití.

Kromě toho vaše úkoly vývoje nebo technologie může být nutné další škálování, jako jsou typy konfigurací vývoje a konfigurací s více počítači. Azure DevTest Labs můžete použít k vytvoření _recepty_ , automatizace procesu vytváření vaší "zlaté image." DevTest Labs můžete také použít ke správě zásad pro váš tým spuštěných virtuálních počítačů. [Pomocí Azure DevTest Labs pro vývojáře](/azure/devtest-lab/devtest-lab-developer-lab) je nejlepší zdrojem pro další informace o službě DevTest Labs.

## <a name="next-steps"></a>Další kroky
Teď už víte o předem nakonfigurovaným imagím sady Visual Studio, dalším krokem je vytvoření nového virtuálního počítače:

* [Vytvoření virtuálního počítače na webu Azure portal](quick-create-portal.md)
* [Přehled Windows Virtual Machines](overview.md)
