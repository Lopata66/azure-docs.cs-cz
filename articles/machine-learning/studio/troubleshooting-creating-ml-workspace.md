---
title: Řešení potíží s pracovním prostorem
titleSuffix: Azure Machine Learning Studio
description: Tato příručka poskytuje řešení pro některé často problémy při nastavení pracovních prostorů Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7cc825daa29a0398793f3c6fc5ce8ee426ad79e6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884187"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-workspace"></a>Průvodce odstraňováním potíží: Vytvoření a připojení k pracovnímu prostoru Azure Machine Learning Studio
Tato příručka poskytuje řešení pro některé často problémy při nastavení pracovních prostorů Azure Machine Learning Studio.



## <a name="workspace-owner"></a>Vlastník pracovního prostoru
Otevřete pracovní prostor v Machine Learning Studio, musíte být přihlášeni k Account Microsoft jste použili k vytvoření pracovního prostoru, nebo potřebujete přijmout pozvánku od vlastníka k pracovnímu prostoru. Na webu Azure Portal můžete spravovat pracovní prostor, který zahrnuje možnost konfigurovat přístup.

Další informace o správě pracovního prostoru, naleznete v tématu [Správa pracovního prostoru Azure Machine Learning Studio].

[Správa pracovního prostoru Azure Machine Learning Studio]: manage-workspace.md

## <a name="allowed-regions"></a>Povolených oblastí
Machine Learning je aktuálně k dispozici v omezeném počtu oblastí. Pokud součástí vašeho předplatného není jedna z těchto oblastí, může se zobrazit chybová zpráva, "V oblasti povolené nemáte žádná předplatná."

Požádat o přidaná v oblasti vašeho předplatného, vytvořit novou žádost o podporu společnosti Microsoft na webu Azure Portal, vyberte **fakturace** jako typ problému a postupujte podle výzev k odeslání vaší žádosti.

## <a name="storage-account"></a>Účet úložiště
Služba Machine Learning potřebuje účet úložiště k ukládání dat. Můžete použít existující účet úložiště, nebo můžete vytvořit nový účet úložiště, když vytvoříte nový pracovní prostor Machine Learning Studio (Pokud nemáte kvótu pro vytvoření nového účtu úložiště).

Jakmile se vytvoří nový pracovní prostor Machine Learning Studio, můžete přihlásit ke službě Machine Learning Studio pomocí účtu Microsoft, který jste použili k vytvoření pracovního prostoru. Pokud narazíte na chybovou zprávu, "Pracovní prostor nebyl nalezen" (podobně jako na následujícím snímku obrazovky), použijte následující kroky a odstranit soubory cookie v prohlížeči.

![Pracovní prostor nebyl nalezen](media/troubleshooting-creating-ml-workspace/screen3.png)

**Chcete-li odstranit soubory cookie v prohlížeči**

1. Pokud používáte Internet Explorer, klikněte na tlačítko **nástroje** tlačítko v pravém horním rohu a vyberte **Možnosti Internetu**.  

   ![Možnosti Internetu](media/troubleshooting-creating-ml-workspace/screen4.png)

2. V části **Obecné** klikněte na tlačítko **odstranit...**

   ![Karta Obecné](media/troubleshooting-creating-ml-workspace/screen5.png)

3. V **odstranit historii procházení** dialogové okno pole, ujistěte se, že **soubory cookie a data webu** je vybraná a klikněte na tlačítko **odstranit**.

   ![Odstranění souborů cookie](media/troubleshooting-creating-ml-workspace/screen6.png)

Po odstranění souborů cookie, restartujte prohlížeč a přejděte [Microsoft Azure Machine Learning Studio](https://studio.azureml.net) stránky. Po zobrazení výzvy k zadání uživatelského jména a hesla, zadejte stejný účet Microsoft, který jste použili k vytvoření pracovního prostoru.

## <a name="comments"></a>Komentáře

Naším cílem je, aby co nejvíc jako bezproblémové prostředí Machine Learning. Zveřejněte ji prosím všechny komentáře a problémy ve [fórum pro Azure Machine Learning](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) nám můžete poskytovat lepší.
