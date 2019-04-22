---
title: Rychlý start Azure – Vytvoření účtu Azure Automation | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit účet Azure Automation a spustit runbook.
services: automation
author: csand-msft
ms.author: csand
ms.date: 04/04/2019
ms.topic: quickstart
ms.service: automation
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 7f7905a4b09e685ad98a1663333aa32bc1d7ae90
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59009508"
---
# <a name="create-an-azure-automation-account"></a>Vytvoření účtu Azure Automation

Účty Azure Automation je možné vytvářet přes Azure. Tato metoda poskytuje uživatelské rozhraní v prohlížeči, pomocí kterého můžete vytvářet a konfigurovat účty Automation a související prostředky. Tento rychlý start prochází jednotlivé kroky k vytvoření účtu Automation a spuštění runbooku v tomto účtu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k Azure na adrese https://portal.azure.com.

## <a name="create-automation-account"></a>Vytvoření účtu Automation

1. Klikněte na tlačítko **Vytvořit prostředek** v levém horním rohu Azure.

1. Vyberte **Nástroje pro správu** a pak vyberte **Automation**.

1. Zadejte informace o účtu. U možnosti **Vytvořit účet Spustit v Azure jako** zvolte **Ano**, aby se automaticky povolily artefakty, které zjednoduší ověřování do Azure. Je důležité si uvědomit, že při vytváření účtu Automation není možné změnit již zvolený název. *Názvy účtů Automation jsou na oblast a skupinu prostředků jedinečné. Názvy pro účty Automation, které se odstranily nemusí být hned dostupné.* Jeden účet Automation může spravovat prostředky napříč všemi oblastmi a předplatnými daného tenanta. Jakmile budete hotovi, klikněte na **Vytvořit** a spusťte nasazování účtu Automation.

    ![Zadání informací o účtu Automation na stránce](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Aktualizovaný seznam umístění, do kterých můžete účet Automation nasadit, najdete na stránce [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=automation&regions=all).

1. Po dokončení nasazení klikněte na tlačítko **všechny služby**vyberte **účty Automation** a vyberte účet Automation, který jste vytvořili.

    ![Přehled účtu Automation](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Spuštění runbooku

Spusťte některý z runbooků pro tento kurz.

1. V části **AUTOMATIZACE PROCESŮ** klikněte na **Runbooky**. Zobrazí se seznam runbooků. Ve výchozím nastavení je v účtu povoleno několik cvičných runbooků.

    ![Seznam runbooků v účtu Automation](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Vyberte runbook **AzureAutomationTutorialScript**. Tato akce otevře stránku s přehledem runbooku.

    ![Přehled runbooku](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Klikněte na **Spustit**, na stránce **Spustit Runbook** klikněte na **OK** a spusťte runbook.

    ![Stránka úlohy runbooku](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Jakmile se **Stav úlohy** změní na **Spuštěno**, klikněte na **Výstup** nebo **Všechny protokoly** a zobrazte výstup úlohy runbooku. V případě tohoto cvičného runbooku je výstupem seznam vašich prostředků Azure.

## <a name="next-steps"></a>Další postup

V rámci tohoto rychlého startu jste nasadili účet Automation, spustili úlohu runbooku a zobrazili výsledky úlohy. Další informace o službě Azure Automation najdete v rychlém startu k vytvoření prvního runbooku.

> [!div class="nextstepaction"]
> [Rychlý start se službou Automation – Vytvoření runbooku](./automation-quickstart-create-runbook.md)

