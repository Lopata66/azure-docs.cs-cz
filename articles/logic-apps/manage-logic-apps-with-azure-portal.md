---
title: Správa aplikací logiky v Azure Portal
description: Umožňuje upravit, povolit, zakázat nebo odstranit Logic Apps pomocí Azure Portal.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/13/2020
ms.openlocfilehash: f726ca90c215c4aff3734bd8022bbc1ad4dc5f87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415981"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Správa aplikací logiky v Azure Portal

Aplikace logiky můžete spravovat pomocí [Azure Portal](https://portal.azure.com) nebo sady [Visual Studio](manage-logic-apps-with-visual-studio.md). Tento článek popisuje, jak upravit, zakázat, povolit nebo odstranit Logic Apps v Azure Portal. Pokud s Azure Logic Appsem začínáte, přečtěte si téma [co je Azure Logic Apps](logic-apps-overview.md)?

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Existující aplikace logiky. Informace o tom, jak vytvořit aplikaci logiky v Azure Portal, najdete v tématu [rychlý Start: vytvoření prvního pracovního postupu pomocí Azure Logic Apps-Azure Portal](quickstart-create-first-logic-app-workflow.md).

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>Hledání aplikací logiky

K vyhledání a otevření aplikace logiky použijte následující postup:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

1. Na panelu hledání Azure zadejte `logic apps`a vyberte **Logic Apps**.

   ![Vyhledejte a vyberte "Logic Apps"](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. Na stránce **Logic Apps** vyhledejte a vyberte aplikaci logiky, kterou chcete spravovat.

   Po otevření podokna **přehledu** aplikace logiky můžete filtrovat seznam, který se zobrazí na stránce **Logic Apps** , tímto způsobem:

   * Hledání Logic Apps podle názvu
   * Filtrovat aplikace logiky podle předplatného, skupiny prostředků, umístění a značek
   * Seskupit aplikace logiky podle skupiny prostředků, typu, předplatného a umístění

## <a name="view-logic-app-properties"></a>Zobrazit vlastnosti aplikace logiky

1. V Azure Portal [Najděte a otevřete aplikaci logiky](#find-logic-app).

1. V nabídce aplikace logiky v části **Nastavení**vyberte **vlastnosti**.

1. V podokně **vlastnosti** můžete zobrazit a zkopírovat následující informace o vaší aplikaci logiky:

   * **Název**
   * **ID prostředku**
   * **Skupina prostředků**
   * **Umístění**
   * **Typ** 
   * **Název předplatného**
   * **ID předplatného**
   * **Koncový bod přístupu**
   * **Běhové odchozí IP adresy**
   * **Přístup k IP adresám koncových bodů**
   * **Odchozí IP adresy konektoru**

## <a name="disable-or-enable-logic-apps"></a>Zakázání nebo povolení aplikací logiky

Můžete povolit nebo zakázat [jednu aplikaci logiky](#disable-enable-single-logic-app) nebo [několik aplikací logiky současně](#disable-or-enable-multiple-logic-apps) v Azure Portal. Můžete také [Povolit nebo zakázat Logic Apps v aplikaci Visual Studio](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app).

Zakázání aplikace logiky ovlivní vaše instance pracovního postupu a spustí se následujícími způsoby:

* Všechny probíhající a probíhající běhy pokračují, dokud se nedokončí. V závislosti na počtu těchto spuštění může tento proces nějakou dobu trvat.

* Modul Logic Apps nevytvoří ani nespustí nové instance pracovního postupu.

* Aktivační událost se neaktivuje při příštím splnění podmínek.

* Stav triggeru pamatuje bod, ve kterém se aplikace logiky zastavila. Pokud tedy aplikaci logiky znovu povolíte, Trigger se aktivuje u všech nezpracovaných položek od posledního spuštění.

  Pokud chcete zastavit spouštění aplikace logiky u nezpracovaných položek od posledního spuštění, vymažte stav triggeru předtím, než znovu povolíte aplikaci logiky:

  1. V Azure Portal [Najděte a otevřete aplikaci logiky](#find-logic-app).

  1. Upravte jakoukoli část triggeru aplikace logiky.

  1. Uložte provedené změny. Tento krok obnoví aktuální stav triggeru.

  1. [Znovu aktivujte svoji aplikaci logiky](#disable-enable-single-logic-app).

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>Zakázání nebo povolení aplikace s jednou logikou

1. V Azure Portal [Najděte a otevřete aplikaci logiky](#find-logic-app).

1. V nabídce aplikace logiky zvolte **Přehled**. Vyberte si z těchto možností:

   * Na panelu nástrojů vyberte **Zakázat**.

     ![Zakázat aplikaci s jednou logikou v Azure Portal](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     Pokud je vaše aplikace logiky už zakázaná, zobrazí se jenom možnost **Povolit** .

   * Na panelu nástrojů vyberte **Povolit**.

     ![Povolit aplikaci s jednou logikou v Azure Portal](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     Pokud je vaše aplikace logiky už povolená, zobrazí se jenom možnost **Zakázat** . 

   Azure Portal na hlavním panelu nástrojů Azure zobrazuje oznámení o tom, zda byla operace úspěšná nebo neúspěšná.

   ![Oznámení pro potvrzení stavu operace](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>Zakázání nebo povolení více aplikací logiky

1. V Azure Portal [Najděte aplikace logiky](#find-logic-app) , které chcete zakázat nebo povolit.

1. Pokud chcete zkontrolovat, jestli je aplikace logiky aktuálně povolená nebo zakázaná, na stránce **Logic Apps** zkontrolujte sloupec **stav** pro tuto aplikaci logiky. 

   ![Sloupec stav Logic Apps](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   Pokud sloupec **stav** není zobrazený, vyberte na panelu nástrojů **Logic Apps** možnost **vyzkoušet si verzi Preview**.

   ![Zapnout náhled](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. Ve sloupci zaškrtávací políčko Vyberte aplikace logiky, které chcete zakázat nebo povolit. Na panelu nástrojů vyberte **Zakázat** nebo **Povolit**.

   ![Povolení nebo zakázání více aplikací logiky v Azure Portal](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. Po zobrazení pole potvrzení vyberte **Ano** a pokračujte.

   Azure Portal na hlavním panelu nástrojů Azure zobrazuje oznámení o tom, zda byla operace úspěšná nebo neúspěšná.

## <a name="delete-logic-apps"></a>Odstranit Logic Apps

Můžete [odstranit jednu aplikaci logiky](#delete-single-logic-app) nebo v Azure Portal [odstranit více aplikací logiky současně](#delete-multiple-logic-apps) . [Aplikaci logiky můžete také odstranit v aplikaci Visual Studio](manage-logic-apps-with-visual-studio.md#delete-your-logic-app).

Odstranění vaší aplikace logiky má vliv na instance pracovního postupu v těchto ohledech:

* Všechny probíhající a probíhající běhy pokračují, dokud se nedokončí. V závislosti na počtu těchto spuštění může tento proces nějakou dobu trvat.

* Modul Logic Apps nevytvoří ani nespustí nové instance pracovního postupu.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>Odstranit aplikaci s jednou logikou

1. V Azure Portal [Najděte a otevřete aplikaci logiky](#find-logic-app).

1. V nabídce aplikace logiky zvolte **Přehled**. Na panelu nástrojů aplikace logiky vyberte **Odstranit**.

   ![Na panelu nástrojů aplikace logiky vyberte Odstranit.](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. Po zobrazení pole potvrzení zadejte název vaší aplikace logiky a vyberte **Odstranit**.

   ![Potvrďte odstranění aplikace logiky](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Azure Portal na hlavním panelu nástrojů Azure zobrazuje oznámení o tom, zda byla operace úspěšná nebo neúspěšná.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>Odstranění více aplikací logiky

1. V Azure Portal [Najděte aplikace logiky, které chcete odstranit](#find-logic-app).

1. Ve sloupci zaškrtávací políčko Vyberte aplikace logiky, které chcete odstranit. Na panelu nástrojů vyberte **Odstranit**.

   ![Odstranění více aplikací logiky](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. Jakmile se zobrazí okno potvrzení, zadejte `yes`a vyberte **Odstranit**.

   ![Potvrďte odstranění vašich aplikací logiky](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Azure Portal na hlavním panelu nástrojů Azure zobrazuje oznámení o tom, zda byla operace úspěšná nebo neúspěšná.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>Správa verzí aplikací logiky

Můžete použít Azure Portal pro správu verzí vašich aplikací logiky. Můžete najít historii verzí vaší aplikace logiky a propagovat předchozí verze.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>Najít a zobrazit předchozí verze

1. V Azure Portal [Najděte aplikaci logiky, kterou chcete spravovat](#find-logic-app).

1. V nabídce aplikace logiky v části **vývojové nástroje**vyberte možnost **verze**.

   ![V nabídce aplikace logiky vyberte v části vývojové nástroje možnost verze.](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. Vyberte **verzi** vaší aplikace logiky, kterou chcete spravovat ze seznamu. Chcete-li filtrovat seznam, můžete zadat identifikátor **verze** na panelu hledání.

1. Na stránce **Historie verze** se zobrazí podrobnosti o předchozí verzi v režimu jen pro čtení. Můžete vybrat mezi Logic Apps **Návrháře** a režimy **zobrazení kódu** .

   ![Stránka s verzí historie pro aplikaci logiky se zobrazením kódu a návrhářem Logic Apps](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>Propagovat předchozí verze

1. V historii verzí aplikace logiky [vyhledejte a vyberte verzi, kterou chcete propagovat](#find-version-history).

1. Na stránce **Historie verze** vyberte **zvýšit úroveň**.

   ![Tlačítko zvýšit úroveň v historii verzí aplikace logiky](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. Na stránce **návrháře Logic Apps** , která se otevře, upravte podle potřeby verzi, kterou podporujete. Můžete přepínat mezi **návrhářem** a režimy **zobrazení kódu** . Můžete také aktualizovat **parametry**, **šablony**a **konektory**.

   ![Stránka návrháře Logic Apps pro zvýšení úrovně předchozí verze](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. Pokud chcete uložit všechny aktualizace a dokončit zvýšení úrovně předchozí verze, vyberte **Uložit**. (Nebo pokud chcete změny zrušit, vyberte **Zrušit**.) 

   Když znovu [zobrazíte historii verzí aplikace logiky](#find-version-history) , povýšená verze se zobrazí v horní části seznamu a má nový identifikátor.

## <a name="next-steps"></a>Další kroky

* [Monitorování aplikací logiky](monitor-logic-apps.md)
