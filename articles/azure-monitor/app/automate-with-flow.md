---
title: Automatizace procesů Azure Application Insights pomocí Microsoft Flow
description: Zjistěte, jak můžete pomocí Microsoft Flow k automatizaci rychle opakovatelnou procesů pomocí konektoru služby Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mbullwin
ms.openlocfilehash: cbb9cceef68febec0af991c3d8d5df3e3404f1c6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834046"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatizace procesů Azure Application Insights pomocí konektoru pro Microsoft Flow

Připadá vám sami opakovaného spouštění stejné dotazů na vaše telemetrická data, zkontrolujte, že vaše služba funguje správně? Pokud chcete automatizovat tyto dotazy pro vyhledání trendy a anomálie a následně vytvořit vlastní pracovní postupy kolem sebe? Azure Application Insights connector pro Microsoft Flow je nejvhodnější nástroj pro tyto účely.

Pomocí této integrace můžete automatizovat řadu procesů aniž byste museli napsat jediný řádek kódu. Po vytvoření toku pomocí Application Insights akci, tok se spustí automaticky vašeho analytického dotazu Application Insights. 

Můžete přidat i další akce. Microsoft Flow je k dispozici stovky akce. Například můžete použít Microsoft Flow automaticky pošle oznámení e-mailu nebo vytvořit chybu ve službě Azure DevOps. Můžete také použít jeden mnoho [šablony](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) , které jsou k dispozici pro konektor pro Microsoft Flow. Tyto šablony zrychlení procesu vytváření toku. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Vytvoření toku pro službu Application Insights

V tomto kurzu se dozvíte, jak vytvořit tok, který používá algoritmus automatického clusteru Analytics skupiny atributů v datech pro webovou aplikaci. Tento tok automaticky odesílá výsledky e-mailem, pouze jeden příklad použití Microsoft Flow a Application Insights Analytics dohromady. 

### <a name="step-1-create-a-flow"></a>Krok 1: Vytvořit tok
1. Přihlaste se k [Microsoft Flow](https://flow.microsoft.com)a pak vyberte **Moje toky**.
2. Klikněte na tlačítko **nový** pak **vytvořit z prázdné**.

    ![Vytvoření nového toku od začátku](./media/automate-with-flow/1createflow.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>Krok 2: Vytvoření aktivační události pro váš tok
1. V sestavení v kartě vyberte **plán**a pak vyberte **plán – opakování**.

    ![Vyberte plánování v části sestavení](./media/automate-with-flow/2schedule.png)

1. V **Interval** zadejte **1**a **frekvence** vyberte **den**.
2. Klikněte na tlačítko **nový krok**

    ![Nastavit plán opakování při zadávání frekvencí a intervalem](./media/automate-with-flow/3schedulerecurrence.png)


### <a name="step-3-add-an-application-insights-action"></a>Krok 3: Přidání akce Application Insights
1. Vyhledejte **Azure Application Insights**.
2. Klikněte na tlačítko **Azure Application Insights – vizualizace analytický dotaz**.
 
    ![Zvolte akci: Azure vizualizovat analytického dotazu Application Insights](./media/automate-with-flow/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Krok 4: Připojení k prostředku služby Application Insights

K dokončení tohoto kroku potřebujete ID aplikace a klíč rozhraní API pro váš prostředek. Můžete je znovu načíst z portálu Azure portal, jak je znázorněno v následujícím diagramu:

![ID aplikace na webu Azure Portal](./media/automate-with-flow/5apiaccess.png)

![Klíč rozhraní API na webu Azure Portal](./media/automate-with-flow/6apikey.png)

- Zadejte název připojení, spolu s klíčem rozhraní API a ID aplikace.

    ![Okno připojení Microsoft Flow](./media/automate-with-flow/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Krok 5: Zadejte typ dotazu a graf Analytics
Tento příklad dotaz vybere neúspěšných žádostí za poslední den a koreluje s výjimky, ke kterým došlo v rámci operace. Analytics koreluje je na základě identifikátoru operation_Id. Výsledky dotazu pak segmenty pomocí algoritmu autocluster. 

Když vytvoříte vlastní dotazy, ověřte, že fungují správně v Analytics předtím, než ho přidáte do vašeho toku.

- Přidejte následující dotaz Analytics a vyberte typ grafu tabulku HTML. Potom vyberte **nový krok**.

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```
    
    ![Okno Konfigurace dotazu Analytics](./media/automate-with-flow/8query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Krok 6: Nakonfigurujte tok poslání e-mailu

1. Vyhledejte **Office 365 Outlook**.
2. Klikněte na tlačítko **Office 365 Outlook – odeslat e-mail**.

    ![Okno pro výběr Office 365 Outlooku](./media/automate-with-flow/9outlookaction.png)

1. V **odeslat e-mailu** okno, postupujte takto:

   a. Zadejte e-mailovou adresu příjemce.

   b. Zadejte předmět e-mailu.

   c. Klikněte kamkoli do **tělo** pole a pak v nabídce dynamického obsahu, které se otevře na pravé straně vyberte **tělo**.

   d. Klikněte na tlačítko **zobrazit pokročilé možnosti**.

    ![Konfigurace Office 365 Outlooku](./media/automate-with-flow/10sendemailbody.png)

1. V nabídce dynamického obsahu postupujte takto:

    a. Vyberte **název přílohy**.

    b. Vyberte **obsah přílohy**.
    
    c. V **je HTML** vyberte **Ano**.

    ![Okno Konfigurace e-mailu Office 365](./media/automate-with-flow/11emailattachment.png)

### <a name="step-7-save-and-test-your-flow"></a>Krok 7: Uložit a testování vašeho toku
- V **název toku** pole, přidat pro nový tok název a potom klikněte na tlačítko **Uložit**.

    ![Pojmenujte tok a uložit](./media/automate-with-flow/12nameflow.png)

Můžete počkat, aktivační událost pro spuštění této akce nebo můžete spustit tok okamžitě podle [aktivační událost spouštět na vyžádání](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Při běhu tohoto toku, příjemce, které jste zadali v seznamu e-mailu dostávat e-mailovou zprávu, která vypadá nějak takto:

![Ukázka e-mailu](./media/automate-with-flow/flow9.png)


## <a name="next-steps"></a>Další postup

- Další informace o vytváření [analytických dotazů](../../azure-monitor/log-query/get-started-queries.md).
- Další informace o [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





