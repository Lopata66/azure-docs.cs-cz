---
title: Streamování dat pomocí Azure SQL Database Stream Analytics Integration (Preview)
description: Použijte Azure Stream Analytics ke streamování dat do Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: fb889f14c3370e1297f98110903c64e93e09c946
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687059"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>Streamování dat pomocí Azure SQL Database Stream Analytics Integration (Preview)

Uživatelé teď můžou ingestovat, zpracovávat, zobrazovat a analyzovat streamovaná data v reálném čase do tabulky přímo z databáze SQL v Azure Portal pomocí [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md). Toto prostředí umožňuje širokou škálu scénářů, jako jsou připojené automobily, vzdálené monitorování, rozpoznávání podvodů a spousta dalších. V Azure Portal můžete vybrat zdroj událostí (centrum událostí/IoT Hub), zobrazit příchozí události v reálném čase a vybrat tabulku pro ukládání událostí. K transformaci příchozích událostí a jejich uložení do vybrané tabulky můžete také na portálu napsat dotazy dotazovacího jazyka Stream Analytics. Tento nový vstupní bod je kromě funkcí vytváření a konfigurace, které už existují v Stream Analytics. Toto prostředí se spouští z kontextu vaší databáze a umožňuje vám rychle nastavit Stream Analytics úlohu a plynule procházet mezi Azure SQL Database a Stream Analyticsmi prostředími.

![Tok Stream Analytics](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>Klíčové výhody

- Minimální přepínání kontextu: můžete začít z SQL Database na portálu a začít ingestovat data v reálném čase do tabulky, aniž byste museli přepnout na jinou službu. 
- Omezený počet kroků: kontext databáze a tabulky slouží k předkonfiguraci Stream Analytics úlohy.
- Další snadné použití s daty verze Preview: náhled příchozích dat ze zdroje událostí (centrum událostí/IoT Hub) v kontextu vybrané tabulky 


## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku budete potřebovat následující zdroje:

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/). 
- Databáze SQL. Podrobnosti najdete v tématu [Vytvoření izolované databáze v Azure SQL Database](sql-database-single-database-get-started.md).
- Pravidlo brány firewall, které umožňuje počítači připojit se k serveru SQL Azure. Podrobnosti najdete v tématu [Vytvoření pravidla brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md).


## <a name="configure-stream-analytics-integration"></a>Konfigurace integrace Stream Analytics

1. Přihlaste se k portálu Azure. 
2. Přejděte do databáze SQL, kde chcete ingestovat streamovaná data. Vyberte **Stream Analytics (Preview)** . 

    ![Stream Analytics](media/sql-database-stream-analytics/stream-analytics.png)

3. Chcete-li začít ingestovat vaše streamovaná data do této databáze SQL, vyberte **vytvořit** a pojmenujte úlohu streamování a potom vyberte **Další: vstup**. 

    ![vytvořit úlohu Stream Analytics](media/sql-database-stream-analytics/create-job.png)

4. Zadejte podrobnosti o zdroji událostí a potom vyberte **Další: výstup**.

   - **Typ vstupu**: centrum událostí/IoT Hub
   - **Vstupní alias**: zadejte název pro identifikaci zdroje událostí. 
   - **Předplatné**: stejné jako SQL Database předplatné 
   - **Obor názvů centra událostí**: název oboru názvů 
   - **Název centra událostí**: název centra událostí ve vybraném oboru názvů 
   - **Název zásad centra událostí** (výchozí vytvoření nového): zadejte název zásady. 
   - **Skupina uživatelů centra událostí** (výchozí vytvoření nového): zadejte název skupiny příjemců.  
     - Doporučujeme vytvořit skupinu příjemců a zásadu pro každou novou Azure Stream Analytics úlohu, kterou vytvoříte tady. Skupiny uživatelů umožňují jenom pět současných čtecích zařízení, takže pokud pro každou úlohu zadáte vyhrazenou skupinu uživatelů, vyhnete se případným chybám, které by mohly vzniknout nad rámec tohoto omezení. Vyhrazená zásada vám umožní otočit klíč nebo odvolat oprávnění, aniž by to mělo vliv na jiné prostředky.

    ![vytvořit úlohu Stream Analytics](media/sql-database-stream-analytics/create-job-output.png)

5. Vyberte, do které tabulky chcete ingestovat streamovaná data. Po dokončení vyberte **vytvořit**.
   - **Uživatelské jméno**, **heslo**: Zadejte svoje přihlašovací údaje pro ověřování SQL serveru. Vyberte **Ověřit**.
   - **Tabulka**: vyberte **vytvořit novou** nebo **použít existující**. V tomto toku Pojďme vybrat **vytvořit**. Tím se vytvoří nová tabulka při spuštění úlohy Stream Analytics.

    ![vytvořit úlohu Stream Analytics](media/sql-database-stream-analytics/create.png)

6. Otevře se stránka s dotazem s následujícími podrobnostmi:

   - Váš **vstup** (zdroj vstupních událostí), ze kterého budete ingestovat data  
   - **Výstup** (výstupní tabulka), ve kterém se budou ukládat transformovaná data 
   - Ukázkový [dotaz SAQL](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) s příkazem SELECT. 
   - **Input Preview**: zobrazuje snímek nejnovějších příchozích dat ze zdroje vstupních událostí.  
     - Typ serializace v datech se automaticky detekuje (JSON/CSV). Můžete ji ručně změnit i na JSON/CSV/AVRO. 
     - Můžete zobrazit náhled příchozích dat ve formátu tabulky nebo v nezpracovaném formátu. 
     - Pokud vaše data nejsou aktuální, vyberte **aktualizovat** , aby se zobrazily nejnovější události. 
     - Vyberte **možnost vyberte časový rozsah** pro otestování dotazu proti určitému časovému rozsahu příchozích událostí. 
     - Vyberte **Odeslat ukázkový vstup** a otestujte dotaz nahrajte tak, že nahrajete ukázkový soubor JSON/CSV. Další informace o testování dotazu SAQL najdete v tématu [test úlohy Azure Stream Analytics s ukázkovými daty](../stream-analytics/stream-analytics-test-query.md). 

    ![Testovat dotaz](media/sql-database-stream-analytics/test-query.png)


   - **Výsledky testu**: vyberte **dotaz testu** a uvidíte výsledky dotazu streamování. 

    ![výsledky testu](media/sql-database-stream-analytics/test-results.png)

   - **Schéma výsledků testů**: zobrazuje schéma výsledků dotazu streamování po otestování. Ujistěte se, že schéma výsledků testů odpovídá vašemu výstupnímu schématu. 

    ![schéma výsledků testů](media/sql-database-stream-analytics/test-results-schema.png)


   - **Výstupní schéma**: obsahuje schéma tabulky, kterou jste vybrali v kroku 5 (nové nebo existující).
     - Vytvořit novou: Pokud jste v kroku 5 vybrali tuto možnost, schéma se zatím nezobrazí, dokud nespustíte úlohu streamování. Při vytváření nové tabulky vyberte příslušný index tabulky. Další informace o indexování tabulek najdete v tématu věnovaném [clusterovaným a neclusterovaným indexům](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/).
    - Použít existující: Pokud jste v kroku 5 vybrali tuto možnost, zobrazí se schéma vybrané tabulky. 
 
7. Po dokončení vytváření & testování dotazu vyberte **Uložit dotaz**. Vyberte **spustit Stream Analytics úlohu** pro zahájení ingestování transformovaných dat do tabulky SQL. Po dokončení následujících polí **Spusťte** úlohu. 
   - **Čas spuštění výstupu**: definuje čas prvního výstupu úlohy.  
     - Nyní: úloha se spustí nyní a zpracuje nová příchozí data.
     - Vlastní: úloha se spustí teď, ale zpracuje data z určitého bodu v čase (může to být v minulosti nebo v budoucnosti). Další informace najdete v tématu [spuštění Azure Stream Analytics úlohy](../stream-analytics/start-job.md).
   - **Jednotky streamování**: Azure Stream Analytics se účtuje podle počtu jednotek streamování potřebných ke zpracování dat do služby. Další informace najdete v tématu [Azure Stream Analytics ceny](https://azure.microsoft.com/pricing/details/stream-analytics/). 
   - **Zpracování chyb výstupních dat**:  
     - Opakování: když dojde k chybě, Azure Stream Analytics pokusy o zápis události po neomezenou dobu, dokud zápis nebude úspěšný. Pro opakované pokusy není k dispozici žádný časový limit. Nakonec se všechny následné události zablokují ze zpracování událostí, která se opakuje. Tato možnost je výchozí zásadou zpracování chyb výstupu. 
     - Drop: Azure Stream Analytics vynechá událost výstupu, která má za následek chybu převodu dat. Zahozené události není možné obnovit pro pozdější opětovné zpracování. Všechny přechodné chyby (například chyby sítě) se zopakují bez ohledu na konfiguraci zásad zpracování chyb výstupu. 
   - **Nastavení výstupu SQL Database**: možnost dědění schématu dělení v předchozím kroku dotazu, která umožňuje úplnou paralelní topologii s několika zapisovači v tabulce. Další informace najdete v tématu [Azure Stream Analytics výstup do Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).
   - **Maximální počet dávek**: doporučený horní limit počtu záznamů odeslaných při každé hromadné vložené transakci.  
    Další informace o zpracování výstupních chyb najdete [v tématu výstupní zásady chyb v Azure Stream Analytics](../stream-analytics/stream-analytics-output-error-policy.md).  

    ![Spustit úlohu](media/sql-database-stream-analytics/start-job.png)

8. Po spuštění úlohy se v seznamu zobrazí spuštěná úloha a můžete provést následující akce: 
   - **Spustit/zastavit úlohu**: Pokud je úloha spuštěná, můžete úlohu zastavit. Pokud se úloha zastaví, můžete úlohu spustit. 
   - **Upravit úlohu**: dotaz můžete upravit. Pokud chcete v úloze udělat další změny, přidejte další vstupy a výstupy a pak otevřete úlohu v Stream Analytics. Možnost upravit je při běhu úlohy zakázána. 
   - **Náhled výstupní tabulky**: náhled tabulky můžete zobrazit v editoru dotazů SQL. 
   - **Otevřít v Stream Analytics**: Otevřete úlohu ve službě Stream Analytics, abyste zobrazili monitorování, podrobnosti o ladění úlohy. 


    ![úlohy Stream Analytics](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>Další kroky

- [Dokumentace k Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)
- [Azure Stream Analytics způsoby řešení](../stream-analytics/stream-analytics-solution-patterns.md)
