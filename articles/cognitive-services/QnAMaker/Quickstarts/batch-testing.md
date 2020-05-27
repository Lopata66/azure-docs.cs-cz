---
title: 'Rychlý Start: testování znalostní báze s dotazy služby Batch'
description: Pomocí nástroje QnA Maker Batch Test otestujete znalostní bázi v prostředku QnA Maker pro očekávané odpovědi, hodnocení spolehlivosti a výzvy k vícenásobnému zapnutí.
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: 9845b7c7cc19550c450a1eb00ec02731ef2d9d44
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873662"
---
# <a name="quickstart-test-knowledge-base-with-batch-questions-and-expected-answers"></a>Rychlý Start: testování znalostní báze s dotazy služby Batch a očekávanými odpověďmi

Pomocí nástroje QnA Maker Batch Test otestujete znalostní bázi v prostředku QnA Maker pro očekávané odpovědi, hodnocení spolehlivosti a výzvy k vícenásobnému zapnutí.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* Buď [vytvořte službu QnA maker](create-publish-knowledge-base.md) , nebo použijte existující službu, která používá anglický jazyk.
* Stažení [ukázkového `.docx` souboru s vícenásobným zapnete](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* Stáhněte si [Nástroj Batch test](https://aka.ms/qnamakerbatchtestingtool), extrahujte spustitelný soubor ze `.zip` souboru.

## <a name="sign-into-qna-maker-portal"></a>Přihlaste se QnA Maker portálu

[Přihlaste](https://www.qnamaker.ai/) se k portálu QnA maker.

## <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Vytvoří novou znalostní bázi z ukázkového souboru. docx s vícenásobným zapnete.

1. Na panelu nástrojů vyberte **vytvořit znalostní bázi** .
1. Přeskočte **Krok 1** , protože byste už měli mít QnA maker prostředek, přesunete se ke **kroku 2** a vyberete své existující informace o zdroji:
    * ID Azure Active Directory
    * Název předplatného Azure
    * Název služby Azure QnA
    * Jazyk – anglický jazyk
1. `Multi-turn batch test quickstart`Jako název vaší znalostní báze zadejte název.

1. V **kroku 4**nakonfigurujte nastavení pomocí následující tabulky:

    |Nastavení|Hodnota|
    |--|--|
    |**Povolí vícenásobné extrakce z adres URL, souborů PDF a DOCX.**|Zaškrtnuté|
    |**Výchozí text odpovědi**| `Batch test - default answer not found.`|
    |**+ Přidat soubor**|Vyberte stažený `.docx` soubor výpisu v části požadavky.|
    |**CHITEST – chat**|Vybrat **Professional**|

1. V **kroku 5**vyberte **vytvořit znalostní báze**.

    Po dokončení procesu vytváření se na portálu zobrazí upravitelná znalostní báze.

## <a name="save-train-and-publish-knowledge-base"></a>Uložení, výuka a publikování znalostní báze

1. Pro uložení znalostní báze vyberte možnost **Uložit a výuka** z panelu nástrojů.
1. Vyberte **publikovat** z panelu nástrojů a potom znovu vyberte **publikovat** a publikujte znalostní bázi. Publikování zpřístupňuje znalostní bázi pro dotazy z koncového bodu veřejné adresy URL. Po dokončení publikování uložte adresu URL hostitele a klíč koncového bodu, které jsou zobrazené na stránce **publikovat** .

    |Požadovaná data| Příklad|
    |--|--|
    |Publikovaný hostitel|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Publikovaný klíč|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`(32 řetězec znaků zobrazený po `Endpoint` )|
    |ID aplikace|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`(36 řetězec znaků zobrazený jako součást `POST` ) |

## <a name="create-batch-test-file-with-question-ids"></a>Vytvořit soubor dávkového testu s ID dotazů

Chcete-li použít nástroj Batch test, vytvořte soubor s názvem `batch-test-data-1.tsv` s textovým editorem. Soubor musí mít následující sloupce oddělené tabulátorem.

|Pole vstupního souboru TSV|Poznámky|Příklad|
|--|--|--|
|ID znalostní báze|ID vašeho znalostní báze bylo nalezeno na stránce publikování. Pomocí různých ID znalostní báze v jednom souboru otestujete několik znalostí ve stejné službě najednou v jednom souboru.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`(36 řetězec znaků zobrazený jako součást `POST` ) |
|Otázka|Text otázky, kterou uživatel zadal. maximální počet znaků: 1 000|`How do I sign out?`|
|Značky metadat|optional|`topic:power`používá formát _klíč: hodnota_|
|První parametr|optional|`25`|
|Očekávané ID odpovědi|optional|`13`|

Pro tuto databázi Knowledge Base přidejte do souboru tři řádky, které mají jen dva požadované sloupce. První sloupec je vaše ID znalostní báze a druhý sloupec by měl být následující seznam otázek:

|Sloupec 2 – otázky|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Tyto otázky jsou přesným formulací ze znalostní báze a měly by jako hodnocení spolehlivosti vracet 100.

V dalším kroku přidejte pár otázek, podobně jako tyto otázky, ale ne přesně stejné na 3 více řádků, a to pomocí stejného ID znalostní báze:

|Sloupec 2 – otázky|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Ujistěte se, že jsou všechny sloupce oddělené oddělovačem karet. Mezery na začátku nebo na konci jsou přidány do dat sloupce a způsobí, že program vyvolá výjimky, pokud je nesprávný typ nebo velikost.

Soubor dávkového testu, při otevření v aplikaci Excel, vypadá jako na následujícím obrázku. ID znalostní báze bylo nahrazeno z `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` důvodu zabezpečení. Pro vlastní test Batch se ujistěte, že sloupec zobrazuje ID znalostní báze.

> [!div class="mx-imgBorder"]
> ![Zadání první verze souboru. TSV z dávkového testu](../media/batch-test/batch-test-1-input.png)

## <a name="test-the-batch-file"></a>Test dávkového souboru

Spusťte program Batch test pomocí následujícího formátu CLI na příkazovém řádku.

Nahraďte `YOUR-RESOURCE-NAME` a `ENDPOINT-KEY` vlastními hodnotami pro název služby a klíč koncového bodu. Tyto hodnoty najdete na stránce **Nastavení** na portálu QnA maker.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
Test se dokončí a vygeneruje `out.tsv` soubor:

> [!div class="mx-imgBorder"]
> ![Výstup první verze souboru. TSV z dávkového testu](../media/batch-test/batch-test-1-output.png)

ID znalostní báze bylo nahrazeno z `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` důvodu zabezpečení. Pro vlastní test Batch se ve sloupci zobrazí vaše ID znalostní báze.

Výstup testu skóre spolehlivosti ve sloupci 4 zobrazuje prvních 3 otázek vrátilo skóre 100, jak je očekáváno, protože každá otázka je přesně stejná jako ve znalostní bázi. Poslední 3 otázky s novou formulací otázky nevrátí 100 jako hodnocení spolehlivosti. Aby bylo možné zvýšit skóre pro test a uživatele, musíte do znalostní báze přidat další alternativní otázky.

## <a name="testing-with-the-optional-fields"></a>Testování s volitelnými poli

Jakmile porozumíte formátu a procesu, můžete vygenerovat testovací soubor, který se má ve znalostní bázi spustit ze zdroje dat, jako jsou protokoly chatu.

Vzhledem k tomu, že jsou zdroje a procesy dat automatizované, lze testovací soubor spustit několikrát s různými nastaveními, abyste určili správné hodnoty.

Například pokud máte protokol chatu a chcete určit, který text protokolu chatu se vztahuje na která pole metadat, vytvořit testovací soubor a nastavit pole metadat pro každý řádek. Spusťte test a pak zkontrolujte řádky, které odpovídají metadatům. Obvykle by shoda měla být kladná, ale měli byste zkontrolovat výsledky falešně pozitivních výsledků. Falešně pozitivní hodnota je řádek, který odpovídá metadatům, ale na základě textu by neměl odpovídat.

## <a name="using-optional-fields-in-the-input-batch-test-file"></a>Použití volitelných polí v vstupním souboru testu dávky

Pomocí následujícího grafu pochopíte, jak najít hodnoty polí pro volitelná data.

|Číslo sloupce|Volitelný sloupec|Umístění dat|
|--|--|--|
|3|zprostředkovatele identity|Exportujte stávající znalostní bázi pro existující _klíč: páry hodnot_ .|
|4|top|`25`Doporučuje se výchozí hodnota.|
|5|ID sady otázek a odpovědí|Exportujte existující znalostní bázi pro hodnoty ID. Všimněte si také, že se ve výstupním souboru vrátila ID.|

## <a name="add-metadata-to-the-knowledge-base"></a>Přidat metadata do znalostní báze

1. Na portálu QnA na stránce **Upravit** Přidejte metadata `topic:power` na následující otázky:

    |Dotazy|
    |--|
    |Naúčtuje se na Surface pro 4.|
    |Kontrolovat úroveň baterie|

    Existují dva páry QnA sady metadat.

    > [!TIP]
    > Pokud chcete zobrazit metadata a ID QnA jednotlivých sad, exportujte znalostní bázi. Vyberte stránku **Nastavení** a pak vyberte **exportovat** jako `.xls` soubor. Najít tento stažený soubor a otevřít v aplikaci Excel revize metadat a ID.

1. Vyberte **Uložit a výuka**, pak vyberte stránku **publikování** a pak vyberte tlačítko **publikovat** . Tyto akce provedou změnu v dávkovém testu. Stáhněte si znalostní bázi ze stránky **Nastavení** .

    Stažený soubor má správný formát pro metadata a správné ID sady otázek a odpovědí. Tato pole použijte v následující části.

    > [!div class="mx-imgBorder"]
    > ![Exportovaná znalostní báze s metadaty](../media/batch-test/exported-knowledge-base-with-metadata.png)

## <a name="create-a-second-batch-test"></a>Vytvoření druhého dávkového testu

Pro dávkové testování existují dva hlavní scénáře:
* **Soubory protokolu chatovací konverzace** – určete nejlepší odpověď na dříve nezobrazenou otázku – nejběžnější je situace, kdy potřebujete zpracovat soubory dotazů, jako jsou například otázky uživatele robota v programu chat. Vytvoří test dávkového souboru s pouze požadovanými sloupci. Test vrátí nejvyšší odpověď pro každou otázku. To neznamená, že se jedná o správnou odpověď nejvyšší odpovědi. Po dokončení tohoto testu přejděte k ověřovacímu testu.
* **Ověřovací test** – ověřte očekávanou odpověď. Tento test vyžaduje, aby všechny otázky a odpovídající očekávané odpovědi v dávkovém testu byly ověřeny. To může vyžadovat ruční proces.

Následující postup předpokládá, že se jedná o zpracování protokolů chatu pomocí

1. Vytvořte nový soubor dávkového testu, který bude zahrnovat volitelná data `batch-test-data-2.tsv` . Přidejte 6 řádků z původního vstupního souboru dávkového testu a pak přidejte ID páru metadata, Top a QnA pro každý řádek.

    Chcete-li simulovat automatizovaný proces kontroly nového textu v protokolech konverzace proti znalostní bázi, nastavte metadata pro každý sloupec na stejnou hodnotu: `topic:power` .

    > [!div class="mx-imgBorder"]
    > ![Vstupní druhá verze souboru. TSV z dávkového testu](../media/batch-test/batch-test-2-input.png)

1. Spusťte test znovu a změňte názvy vstupních a výstupních souborů tak, aby označovaly, že se jedná o druhý test.

    > [!div class="mx-imgBorder"]
    > ![Výstupní druhá verze souboru. TSV z dávkového testu](../media/batch-test/batch-test-2-output.png)

## <a name="test-results-and-an-automated-test-system"></a>Výsledky testů a automatizovaný testovací systém

Tento testovací výstupní soubor lze analyzovat jako součást automatizovaného plynulého testovacího kanálu.

Tento konkrétní výstup testu by měl být čten jako: každý řádek byl filtrován s metadaty, protože každý řádek neodpovídal metadatům ve znalostní bázi, výchozí odpověď pro tyto neshodné řádky se vrátila ("v KB" se nenašly žádné dobré shody). Z těch řádků, které se shodovaly, bylo vráceno ID QnA a skóre.

Všechny řádky vrátily chybný popisek, protože se očekával žádný řádek s ID odpovědi.

Měli byste být schopni se podívat na tyto výsledky, které můžete použít jako dotaz na jednotlivé řádky v protokolu chatu. V případě, že data o datech neznáte, vám výsledky sdělí spoustu dat, která pak můžete použít k posunutí.

* meta data
* ID QnA
* skóre

Bylo filtrování s metadaty dobrým nápadem pro test? Ano a ne. Testovací systém by měl vytvořit testovací soubory pro každý pár meta-dat a také test s žádnými páry meta-data.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat v testování znalostní báze, odstraňte nástroj soubor Batch a testovací soubory.

Pokud nebudete nadále používat tuto znalostní bázi, odstraňte znalostní bázi pomocí následujících kroků:

1. Na portálu QnA Maker v horní nabídce vyberte **Moje znalostní** báze.
1. V seznamu základů znalostní báze vyberte ikonu **Odstranit** na řádku znalostní báze tohoto rychlého startu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
