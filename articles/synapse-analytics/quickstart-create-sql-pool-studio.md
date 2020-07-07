---
title: Rychlý Start – vytvoření synapse fondu SQL (ve verzi Preview) pomocí synapse studia
description: Podle kroků v této příručce vytvořte nový synapse fond SQL pomocí synapse studia.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 3/19/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f93eb55b888c58ad111bd67b2011ba9c996b16bb
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2020
ms.locfileid: "85960261"
---
# <a name="quickstart-create-a-synapse-sql-pool-preview-using-synapse-studio"></a>Rychlý Start: vytvoření synapse fondu SQL (ve verzi Preview) pomocí synapse studia

Azure synapse Analytics nabízí různé analytické moduly, které vám pomůžou ingestovat, transformovat, modelovat a analyzovat vaše data. Fond SQL nabízí výpočetní a úložné funkce založené na T-SQL. Po vytvoření fondu SQL ve vašem pracovním prostoru synapse se dají data načíst, namodelovat, zpracovávat a doručovat pro rychlejší analytické poznatky.

Tento rychlý Start popisuje kroky pro vytvoření fondu SQL v pracovním prostoru synapse pomocí synapse studia.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Pracovní prostor synapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [Azure Portal](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Přejděte do pracovního prostoru synapse.

1. Přejděte do pracovního prostoru synapse, kde se vytvoří fond SQL zadáním názvu služby (nebo názvu prostředku přímo) do panelu hledání.
![Azure Portal panel hledání s pracovními prostory synapse zadanými v.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. V seznamu pracovních prostorů zadejte název (nebo část názvu) pracovního prostoru, který chcete otevřít. V tomto příkladu použijeme pracovní prostor s názvem **contosoanalytics**.
![Seznam pracovních prostorů synapse filtrovaných k zobrazení těch, které obsahují název contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Spuštění funkce Synapse Studio

1. V přehledu pracovního prostoru vyberte **Spustit synapse Studio** a otevřete tak umístění, kde se vytvoří fond SQL. Zadejte název služby nebo název prostředku přímo do panelu hledání.
![Azure Portal synapse pracovní prostor – Přehled s zvýrazněnou možností spustit synapse Studio.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-sql-pool-in-synapse-studio"></a>Vytvoření fondu SQL v synapse studiu

1. Na domovské stránce synapse studia přejděte na **Centrum pro správu** v levém navigačním panelu tak, že vyberete ikonu **Spravovat** .
![Domovská stránka synapse studia s centrem správy – zvýrazněný oddíl](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. V centru pro správu přejděte do části **fondy SQL** , abyste viděli aktuální seznam fondů SQL, které jsou k dispozici v pracovním prostoru.
![Vybraná navigace v centru pro správu synapse studia s vybranými fondy SQL](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. Vyberte **+ Nový** příkaz a zobrazí se Průvodce vytvořením nového fondu SQL. 
![Synapse Studio Management hub seznam fondů SQL.](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. Na kartě **základy** zadejte následující podrobnosti:

    | Nastavení | Navrhovaná hodnota | Popis |
    | :------ | :-------------- | :---------- |
    | **Název fondu SQL** | contosoedw | Jedná se o název, který bude mít fond SQL. |
    | **Úroveň výkonu** | DW100c | Nastavte na nejmenší velikost, aby se snížily náklady pro tento rychlý Start. |

    ![Fondy SQL vytvářejí kartu základy na základě toku.](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > Všimněte si, že existují určitá omezení pro názvy, které můžou fondy SQL použít. Názvy nesmí obsahovat speciální znaky, musí být delší než 15 znaků, nesmí obsahovat vyhrazená slova a v pracovním prostoru být jedinečné.

4. Na další kartě **Další nastavení**vyberte možnost **žádná** a zajistěte tak fond SQL bez dat. Ponechte výchozí kolaci vybranou.
![Postup vytvoření fondu SQL – karta Další nastavení](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. Nepřidáme teď žádné značky, takže vyberte **Další: zkontrolovat + vytvořit**.

1. Na kartě **Revize + vytvořit** se ujistěte, že podrobnosti vypadají správně na základě dříve zadaného obsahu, a pak stiskněte **vytvořit**. 
![Postup vytvoření fondu SQL – karta Nastavení revize](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. V tomto okamžiku se spustí tok zřizování prostředků.

1. Po dokončení zřizování se navigace zpátky do pracovního prostoru zobrazí nový záznam pro nově vytvořený fond SQL.
 ![Vytvoření fondu SQL – zřizování prostředků](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. Po vytvoření fondu SQL budou v pracovním prostoru k dispozici pro načítání dat, zpracování datových proudů, čtení ze jezera atd.

## <a name="clean-up-sql-pools-using-synapse-studio"></a>Vyčištění fondů SQL pomocí synapse studia    

Podle následujících pokynů odstraňte z pracovního prostoru SQL fond pomocí synapse studia.
> [!WARNING]
> Odstraněním fondu SQL dojde k odebrání analytického modulu z pracovního prostoru. Již nebude možné se připojit ke fondu a všechny dotazy, kanály a skripty, které používají tento fond SQL, nebudou nadále fungovat.

Pokud chcete odstranit fond SQL, postupujte následovně:

1. Přejděte na fondy SQL v centru pro správu v synapse studiu.
1. Vyberte tři tečky ve fondu SQL, které se mají odstranit (v tomto případě **contosoedw**), aby se zobrazily příkazy pro fond SQL: ![ Výpis fondů SQL s vybraným nedávno vytvořeným fondem.](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. Stiskněte klávesu **Delete**.
1. Potvrďte odstranění a stiskněte tlačítko **Odstranit** .
 ![Potvrzovací dialog pro odstranění vybraného fondu SQL](media/quickstart-create-sql-pool/create-sql-pool-studio-29.png)
1. Po úspěšném dokončení procesu již nebude fond SQL uveden v prostředcích pracovního prostoru.

## <a name="next-steps"></a>Další kroky 
- Další informace najdete v tématu [rychlý Start: vytvoření poznámkového bloku Apache Spark](quickstart-apache-spark-notebook.md).
- Další informace najdete v tématu [rychlý Start: vytvoření synapse fondu SQL pomocí Azure Portal](quickstart-create-sql-pool-portal.md).