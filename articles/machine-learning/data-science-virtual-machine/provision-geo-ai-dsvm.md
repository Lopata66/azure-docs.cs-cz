---
title: Zřízení virtuálního počítače geografickou umělou inteligenci v Azure – Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit a nakonfigurovat virtuální počítač geograficky AI datové vědy. Počítač pro virtuální Geo AI datové vědy poskytuje nástroje pro vytváření řešení AI a ML pomocí zeměpisné údaje.
keywords: obsáhlý learning, AI, nástrojů pro datové vědy, virtuální počítač pro datové vědy, geoprostorové analýzy
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: dde9b71c3615a592f8c08e040c5e9ba7bc756bd6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60578512"
---
# <a name="provision-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Zřízení virtuálního počítače geografickou umělou inteligenci v Azure 

Geograficky AI virtuální počítač pro datové vědy (Geo-DSVM) je rozšířením Oblíbené [virtuální počítač Azure datové vědy](https://aka.ms/dsvm) , který je speciálně nakonfigurován zkombinovat AI a geoprostorové analýzy. Geoprostorové analýzy ve virtuálním počítači využívají [ArcGIS Pro](https://www.arcgis.com/features/index.html). Virtuální počítač pro datové vědy umožňuje rychlé školení strojové učení modely a dokonce i z hloubkového učení modely na datech, která rozšiřují o zeměpisné údaje. To je podporována pouze na Windows 2016 DSVM. 

Geo-DSVM obsahuje několik nástrojů pro AI včetně:

- Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer; jako jsou GPU edice oblíbených architektur obsáhlého learningu 
- Nástroje pro získání a předběžného zpracování obrazu, textová data 
- Nástroje pro vývoj pro aktivity, jako je Microsoft R Server Developer Edition, Anaconda Python, poznámkové bloky Jupyter pro Python a jazyka R, Integrovaná vývojová prostředí pro Python nebo R, SQL databáze
- ArcGIS Pro desktopového softwaru společnosti ESRI spolu s Pythonu a r. rozhraní, které budou fungovat s geoprostorových dat z vašich aplikací AI. 
 

## <a name="create-your-geo-ai-data-science-vm"></a>Vytvoření vaší geografické AI pro datové vědy virtuálního počítače

Tady je postup pro vytvoření instance virtuální počítač pro geograficky AI datové vědy: 


1. Přejděte k virtuálnímu počítači na [webu Azure portal](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
2. Vyberte **vytvořit** tlačítko v dolní části mají být provedeny do průvodce.
![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)
3. Průvodce slouží k vytvoření Geo-DSVM vyžaduje **vstupy** pro každou **čtyři kroky** uvedené na pravé straně tohoto obrázku. Tady jsou vstupy potřebné ke konfiguraci každý z těchto kroků:



   - **Základy**

      1. **Název**: Název serveru datové vědy, kterou vytváříte.

      2. **Uživatelské jméno**: Id přihlášení účtu správce.

      3. **Heslo**: Heslo účtu správce.

      4. **Předplatné**: Pokud máte více předplatných, vyberte ten, ve které je vytvořené a fakturuje počítač.

      5. **Skupina prostředků**: Vytvořit nové nebo použijte **prázdný** existující skupinu prostředků Azure v rámci vašeho předplatného.

      6. **Umístění**: Vyberte datové centrum, které je nejvhodnější. Obvykle se většina dat nebo je nejblíže vašemu fyzickému umístění pro nejrychlejší přístup k síti datového centra. Pokud je potřeba hloubkového učení na grafickém procesoru, je třeba zvolit jedno z umístění v Azure, která má instance virtuálních počítačů NC-Series GPU. Aktuálně jsou umístění, které mají virtuální počítače GPU: **USA – východ, USA (střed) – sever, střed USA – Jih, USA – západ 2, Severní Evropa, západní Evropa**. Nejaktuálnější seznam najdete v dokumentaci [produkty Azure podle oblasti stránky](https://azure.microsoft.com/regions/services/) a hledejte **NC-Series** pod **Compute**. 


   - **Nastavení**: Pokud budete chtít spustit hloubkové učení na grafickém procesoru na vaší geografické DSVM, vyberte jednu z velikost virtuálního počítače GPU NC-Series. V opačném případě můžete zvolit jeden z procesoru na základě instance.  Vytvoření účtu úložiště pro virtuální počítač. 
   
   - **Souhrn**: Ověřte správnost všech informací, které jste zadali.

   - **Koupit**: Klikněte na tlačítko **koupit** zahájíte zřizování. Pomocí odkazu uvedeného na podmínky služby. Virtuální počítač nemá žádné další poplatky za výpočetní prostředky pro velikost serveru, kterou jste zvolili v **velikost** kroku. 
 
>[!NOTE]
> Zřizování zabere asi 20 – 30 minut. Stav zřizování se zobrazí na portálu Azure portal.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Jak získat přístup k počítač pro virtuální Geo AI datové vědy

 Po vytvoření virtuálního počítače, jste připraveni začít používat nástroje, které jsou nainstalovány a předem nakonfigurované na něj. Existují dlaždic nabídky start a ikony na ploše pro celou řadu nástrojů. Vzdálená plocha můžete do něj pomocí přihlašovacích údajů účtu správce, které jste nakonfigurovali v předchozím **Základy** oddílu. 

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Použití ArcGIS Pro nainstalované ve virtuálním počítači

Geo-DSVM již desktop ArcGIS Pro předem nainstalované a prostředí předem nakonfigurované pro práci se všemi nástroji datové VĚDY. Spuštění ArcGIS budete vyzváni k přihlášení ke svému účtu ArcGIS. Pokud již máte účet ArcGIS a máte licencemi na software, můžete vaše stávající přihlašovací údaje.  

![Oblouk. GIS přihlášení](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

V opačném případě můžete zaregistrovat nový účet ArcGIS a licence nebo získat [bezplatnou zkušební verzi](https://www.arcgis.com/features/free-trial.html). 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Po registraci placené nebo bezplatné zkušební verze ArcGIS účet může autorizovat ArcGIS Pro pro váš účet podle pokynů v [Začínáme se službou ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf). 

Po přihlášení k ploše ArcGIS Pro pomocí svého účtu ArcGIS, jste připraveni začít používat nástrojů pro datové vědy, které jsou nainstalované a nakonfigurované na virtuálním počítači pro geoprostorové analýzy a machine learningu projekty.

## <a name="next-steps"></a>Další postup

Začínáme používat virtuální počítač pro geograficky AI datové vědy s pokyny najdete v následujících tématech:

* [Použití v geograficky AI pro datové vědy virtuálního počítače](use-geo-ai-dsvm.md)
