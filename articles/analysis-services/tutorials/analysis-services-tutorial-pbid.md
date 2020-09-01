---
title: Kurz – připojení Azure Analysis Services s Power BI Desktop | Microsoft Docs
author: minewiskan
description: V tomto kurzu se naučíte, jak získat Analysis Services název serveru z Azure Portal a pak se pomocí Power BI Desktop připojit k serveru.
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: 950447aa146f05714b6ec5ea3d6e47c368a2140e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229306"
---
# <a name="tutorial-connect-with-power-bi-desktop"></a>Kurz: Připojení pomocí Power BI Desktopu

V tomto kurzu použijete Power BI Desktop pro připojení k ukázkové modelové databázi adventureworks na vašem serveru. Vámi provedené úlohy budou simulovat typické uživatelské připojení k modelu a vytvoření základní sestavy z dat modelu.

> [!div class="checklist"]
> * Získání názvu serveru z portálu
> * Připojení pomocí Power BI Desktopu
> * Vytvoření základní sestavy

## <a name="prerequisites"></a>Předpoklady

- [Přidejte ukázkovou modelovou databázi adventureworks](../analysis-services-create-sample-model.md) na server.
- Ukázková modelová databáze adventureworks musí mít oprávnění ke [*čtení*](../analysis-services-server-admins.md).
- [Nainstalujte nejnovější Power BI Desktop](https://powerbi.microsoft.com/desktop).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
V tomto kurzu jste na portálu a získali jenom název serveru. Uživatelé obvykle získají název serveru od správce serveru.

Přihlaste se k [portálu](https://portal.azure.com/).

## <a name="get-server-name"></a>Získání názvu serveru
Abyste se mohli k serveru připojit z Power BI Desktopu, potřebujete nejdříve název serveru. Název serveru můžete získat z portálu.

Na portálu **Azure Portal** > Server > **Přehled** > **Název serveru** zkopírujte název serveru.
   
   ![Získání názvu serveru v Azure](./media/analysis-services-tutorial-pbid/aas-copy-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Připojení v Power BI Desktopu

1. V Power BI Desktop klikněte na **získat data**  >  **Azure**  >  **databáze Azure Azure Analysis Services**.

   ![Připojení v okně Získat data](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aasserver.png)

2. V části **Server** vložte název serveru, potom v části **Databáze** zadejte **adventureworks** a potom klikněte na **OK**.

   ![Zadání názvu serveru a modelové databáze](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aas-servername.png)

3. Po zobrazení výzvy zadejte své přihlašovací údaje. Vámi zadaný účet musí mít pro ukázkovou modelovou databázi adventureworks minimálně oprávnění ke čtení.

    Model adventureworks se otevře v Power BI Desktopu s prázdnou sestavou v zobrazení sestav. Seznam **Pole** zobrazuje všechny neskryté objekty modelu. Stav připojení se zobrazuje v pravém dolním rohu.

4. V části **VIZUALIZACE** vyberte **Skupinový pruhový graf**, pak klikněte na **Formát** (ikona malířského válečku) a pak zapněte **Popisky dat**. 

   ![Vizualizace](./media/analysis-services-tutorial-pbid/aas-pbid-visualizations-report.png)

5. V **poli**  >  tabulka**internetových prodejů** vyberte míry **internetového prodeje celková** a **marže** . V tabulce **Product Category** (Kategorie produktů) vyberte **Product Category Name** (Název kategorie produktů).

   ![Dokončení sestavy](./media/analysis-services-tutorial-pbid/aas-pbid-complete-report.png)

    Věnujte pár minut prozkoumání ukázkového modelu adventureworks, vytvořte různé vizualizace a průřezy daty a metrikami. Až budete se sestavou spokojení, nezapomeňte ji uložit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už sestavu nepotřebujete, neukládejte ji. Pokud jste ji uložili, soubor můžete odstranit.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili používat Power BI Desktop pro připojení k datovému modelu na serveru a také vytvořit základní sestavu. Pokud nejste obeznámeni s tím, jak vytvořit datový model, přečtěte si kurz o vytváření [tabulkových dat společnosti Adventure Works Internet Sales](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial) v dokumentaci SQL Server Analysis Services.