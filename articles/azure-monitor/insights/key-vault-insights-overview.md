---
title: Monitorovat Key Vault pomocí Azure Monitor pro Key Vault (Preview) | Microsoft Docs
description: Tento článek popisuje Azure Monitor pro trezory klíčů.
services: azure-monitor
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/13/2019
ms.openlocfilehash: 97bea90e67b9449a8f5fd7b333b9ac149abef2f8
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84945456"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault-preview"></a>Monitorování služby trezoru klíčů pomocí Azure Monitor pro Key Vault (Preview)
Azure Monitor for Key Vault (Preview) poskytuje ucelený přehled o vašich trezorech klíčů tím, že nabízí jednotný pohled na vaše Key Vault požadavky, výkon, chyby a latenci.
Tento článek vám pomůže porozumět tomu, jak se zaregistruje a jak přizpůsobit prostředí Azure Monitor pro Key Vault (Preview).

## <a name="introduction-to-azure-monitor-for-key-vault-preview"></a>Úvod do Azure Monitor pro Key Vault (Preview)

Před přechodem na prostředí byste měli pochopit, jak prezentuje a vizualizují informace.
-    **V perspektivě stupnice** se zobrazuje zobrazení snímku výkonu na základě požadavků, rozpisu selhání a přehledu operací a latence.
-   **Přechod k podrobnostem o analýze** konkrétního trezoru klíčů za účelem provedení podrobné analýzy.
-    **Přizpůsobitelné** , kde můžete změnit metriky, které chcete zobrazit, upravit nebo nastavit prahové hodnoty, které odpovídají vašim limitům, a uložit vlastní sešit. Grafy v sešitě lze připnout na řídicí panely Azure.

Azure Monitor pro Key Vault kombinuje protokoly a metriky, aby poskytovaly globální řešení monitorování. Všichni uživatelé mají přístup k datům monitorování založeným na metrikách, ale zahrnutí vizualizací založených na protokolech může vyžadovat, aby uživatelé [povolili protokolování jejich Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging).

## <a name="configuring-your-key-vaults-for-monitoring"></a>Konfigurace trezorů klíčů pro monitorování

> [!NOTE]
> Povolení protokolů je placená služba, která poskytuje další možnosti monitorování.

1. Karta latence & v operaci vám pomůže určit, kolik trezorů klíčů je povolených. Chcete-li začít s shromažďováním, vyberte tlačítko **Povolit** , které vás přenese do samostatného sešitu, který obsahuje seznam trezorů klíčů, které vyžadují povolení diagnostických protokolů.

    ![Snímek obrazovky s kartou operace a latence se zobrazeným tlačítkem Blue Enable](./media/key-vaults-insights-overview/enable-logging.png)

2. Chcete-li povolit protokoly diagnostiky, klikněte na odkaz **Povolit** pod sloupcem akce a vytvořte nové nastavení diagnostiky, které odesílá protokoly do Log Analytics pracovního prostoru. Doporučuje se odesílat všechny protokoly do stejného pracovního prostoru.

3. Po uložení nastavení diagnostiky budete moct zobrazit všechny grafy založené na protokolech a vizualizace pod Key Vault Insights. Upozorňujeme, že k zahájení načítání protokolů může trvat několik minut.

4. Další pomoc týkající se povolení protokolů diagnostiky pro vaši službu Key Vault najdete v [úplné příručce](https://docs.microsoft.com/azure/key-vault/key-vault-logging).

## <a name="view-from-azure-monitor"></a>Zobrazit z Azure Monitor

Z Azure Monitor můžete zobrazit podrobnosti žádosti, latence a chyby z několika trezorů klíčů v rámci vašeho předplatného a identifikovat problémy s výkonem a omezování scénářů.

Pokud chcete zobrazit využití a operace vašich účtů úložiště napříč všemi vašimi předplatnými, proveďte následující kroky:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/)

2. V Azure Portal v levém podokně vyberte **monitor** a v části přehledy vyberte **trezory klíčů (Preview)**.

![Snímek obrazovky s přehledem možností více grafů](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>Sešit s přehledem

V sešitu s přehledem pro vybrané předplatné tabulka zobrazuje interaktivní metriky trezoru klíčů pro trezory klíčů seskupené v rámci předplatného. Výsledky můžete filtrovat podle vybraných možností z následujících rozevíracích seznamů:

* Předplatná – seznam obsahuje jenom odběry, které obsahují trezory klíčů.

* Trezory klíčů – ve výchozím nastavení jsou předem vybrané jenom 5 trezory klíčů. Pokud v voliči oboru vyberete všechny trezory klíčů, budou vráceny až 200 trezory klíčů. Pokud jste například v třech předplatných, které jste vybrali, nastavili celkový počet trezorů klíčů 573, zobrazí se pouze 200 trezory.

* Časový rozsah – ve výchozím nastavení zobrazuje posledních 24 hodin informací na základě odpovídajících výběrů.

Dlaždice počítadla v rozevíracím seznamu shrnuje celkový počet trezorů klíčů ve vybraných předplatných a odráží počet vybraných. Pro sloupce sešitu, který hlásí požadavky, selhání a metriky latence, existují podmíněná barevně kódované Heat mapy. Hluboká barva má nejvyšší hodnotu a světlejší barva je založena na nejnižších hodnotách.

## <a name="failures-workbook"></a>Sešit s chybami

V horní části stránky vyberte **selhání** a otevře se karta selhání. Zobrazuje počet přístupů k rozhraní API, četnost v čase a také množství určitých kódů odpovědí.

![Snímek obrazovky s sešitem selhání](./media/key-vaults-insights-overview/failures.png)

Pro sloupce v sešitu je podmíněné kódování barev nebo Heat mapy, které vykazují metriky přístupů rozhraní API na modrou hodnotu. Hluboká barva má nejvyšší hodnotu a světlejší barva je založena na nejnižších hodnotách.

Sešit zobrazuje úspěchy (stavové kódy 2xx), chyby ověřování (stavové kódy 401/403), omezování (429 stavových kódů) a další selhání (stavové kódy 4xx).

Pro lepší pochopení toho, co jednotlivé stavové kódy představují, doporučujeme přečíst si dokumentaci k [Azure Key Vault kódů stavu a odpovědi](https://docs.microsoft.com/azure/key-vault/authentication-requests-and-responses).

## <a name="operations--latency-workbook"></a>Sešit latence & operací

V horní části stránky vyberte možnost **operations & latence** a otevře se karta **Operations & latence** . Tato karta umožňuje začlenit trezory klíčů pro monitorování. Podrobnější postup najdete v části [Konfigurace trezorů klíčů pro monitorování](#configuring-your-key-vaults-for-monitoring) .

Vidíte, kolik vašich trezorů klíčů je pro protokolování povolené. Pokud je aspoň jeden trezor správně nakonfigurovaný, budete moct zobrazit tabulky, které zobrazují operace a stavové kódy pro každý z vašich trezorů klíčů. Můžete kliknout na část podrobností pro řádek a získat další informace o jednotlivých operacích.

![Snímek obrazovky s grafy operací a latencí](./media/key-vaults-insights-overview/logs.png)

Pokud se vám v této části nezobrazí žádná data, podívejte se na horní část týkající se povolení protokolů pro Azure Key Vault, nebo v části řešení potíží níže.

## <a name="view-from-a-key-vault-resource"></a>Zobrazit z prostředku Key Vault

Přístup k Azure Monitor pro Key Vault přímo z trezoru klíčů:

1. V Azure Portal vyberte trezory klíčů.

2. V seznamu vyberte Trezor klíčů. V části monitorování vyberte přehledy (Preview).

Tato zobrazení jsou přístupná i tak, že v sešitu na úrovni Azure Monitor vyberete název prostředku trezoru klíčů.

![Snímek obrazovky zobrazení z prostředku trezoru klíčů](./media/key-vaults-insights-overview/key-vault-resource-view.png)

V sešitě s **přehledem** pro Trezor klíčů se zobrazuje několik metrik výkonu, které vám pomůžou rychle vyhodnotit:

- Interaktivní grafy výkonu zobrazující nejdůležitější podrobnosti týkající se transakcí trezoru klíčů, latence a dostupnosti.

- Metriky a stavové dlaždice zvýrazňují dostupnost služby, celkový počet transakcí do prostředku trezoru klíčů a celkovou latenci.

Výběr kterékoli z dalších karet pro **chyby** nebo **operace** otevře příslušné sešity.

![Snímek obrazovky s zobrazením chyb](./media/key-vaults-insights-overview/resource-failures.png)

Sešit selhání rozděluje výsledky všech požadavků trezoru klíčů ve vybraném časovém rámci a poskytuje kategorizaci na Úspěchech (2xx), chyby ověřování (401/403), omezování (429) a další chyby.

![Snímek obrazovky s zobrazením operací](./media/key-vaults-insights-overview/operations.png)

Pracovní sešit umožňuje uživatelům hluboko podrobně do úplných podrobností všech transakcí, které je možné filtrovat podle stavu výsledku pomocí dlaždic nejvyšší úrovně.

![Snímek obrazovky s zobrazením operací](./media/key-vaults-insights-overview/info.png)

Uživatelé mohou také vystavovat zobrazení na základě konkrétních typů transakcí v horní tabulce, která dynamicky aktualizuje spodní tabulku, kde mohou uživatelé zobrazit podrobnosti úplné operace v podokně místní nabídky.

>[!NOTE]
> Všimněte si, že uživatelé musí mít povolené nastavení diagnostiky pro zobrazení tohoto sešitu. Další informace o povolení nastavení diagnostiky najdete v článku o [protokolování Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/logging).

## <a name="pin-and-export"></a>Připnout a exportovat

Libovolný oddíl metriky můžete připnout na řídicí panel Azure tak, že vyberete ikonu připínáčku v pravém horním rohu oddílu.

Přehledy a trezory klíčů pro více předplatných nebo neúspěšné sešity podporují Export výsledků ve formátu aplikace Excel výběrem ikony pro stažení vlevo od ikony připínáčku.

![Snímek obrazovky s vybranou ikonou připnutí](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>Přizpůsobení Azure Monitor pro Key Vault

Tato část popisuje běžné scénáře úprav sešitu pro přizpůsobení v rámci podpory vašich potřeb analýzy dat:
*  Určení rozsahu sešitu pro vždycky výběr konkrétního předplatného nebo trezorů klíčů
* Změna metrik v mřížce
* Změna prahové hodnoty požadavků
* Změnit vykreslování barev

Přizpůsobení můžete zahájit úpravou režimu úprav tak, že v horním panelu nástrojů vyberete tlačítko **přizpůsobit** .

![Snímek obrazovky s tlačítkem přizpůsobit](./media/key-vaults-insights-overview/customize.png)

Vlastní nastavení se ukládají do vlastního sešitu, aby se zabránilo přepsání výchozí konfigurace v našem publikovaném sešitu. Sešity se ukládají v rámci skupiny prostředků, a to buď v části Mé sestavy, která je pro vás nebo ve sdílených sestavách přístupná pro všechny s přístupem ke skupině prostředků. Po uložení vlastního sešitu musíte přejít do galerie sešitů a spustit ho.

![Snímek obrazovky Galerie sešitů](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>Určení předplatného nebo trezoru klíčů

Pomocí následujících kroků můžete nakonfigurovat předplatné s více předplatnými a trezory klíčů a neúspěšné sešity pro obor na konkrétní odběry nebo trezory klíčů při každém spuštění:

1. Na portálu vyberte **monitor** a v levém podokně vyberte **trezory klíčů (Preview)** .
2. V sešitu **přehledu** na panelu příkazů vyberte **Upravit**.
3. V rozevíracím seznamu **odběry** vyberte jedno nebo více předplatných, které má Jo použít jako výchozí. Nezapomeňte, že sešit podporuje výběr až 10 předplatných.
4. V rozevíracím seznamu **trezory klíčů** vyberte jeden nebo více účtů, které chcete použít jako výchozí. Nezapomeňte, že sešit podporuje výběr až na celkem 200 účtů úložiště.
5. Na panelu příkazů vyberte **Uložit jako** a uložte kopii sešitu s vlastními úpravami a potom kliknutím na **hotové úpravy** vraťte do režimu čtení.

## <a name="troubleshooting"></a>Poradce při potížích

Obecné pokyny k odstraňování potíží najdete v článku věnovaném [řešení problémů](troubleshoot-workbooks.md)na základě vyhrazeného sešitu.

Tato část vám pomůže s diagnostikou a řešením potíží s některými běžnými problémy, se kterými se můžete setkat při použití Azure Monitor pro Key Vault (Preview). K vyhledání informací týkajících se konkrétního problému použijte níže uvedený seznam.

### <a name="resolving-performance-issues-or-failures"></a>Řešení potíží s výkonem nebo selhání

Chcete-li pomoct řešit problémy související s trezorem klíčů, které identifikujete pomocí Azure Monitor pro Key Vault (Preview), přečtěte si [dokumentaci Azure Key Vault](https://docs.microsoft.com/azure/key-vault/).

### <a name="why-can-i-only-see-200-key-vaults"></a>Proč můžu zobrazit jenom trezory klíčů 200

Je povolený limit 200 trezorů klíčů, které se dají vybrat a zobrazit. Bez ohledu na počet vybraných předplatných má počet vybraných trezorů klíčů limit 200.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Proč ve výběru předplatného nevidím všechna předplatná

Zobrazujeme jenom odběry, které obsahují trezory klíčů, zvolené z vybraného filtru předplatného, které jsou v hlavičce Azure Portal vybrané v adresáři a předplatném.

![Snímek obrazovky s filtrem předplatných](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-am-getting-an-error-message-that-the-query-exceeds-the-maximum-number-of-workspacesregions-allowed-what-to-do-now"></a>Zobrazuje se chybová zpráva oznamující, že "dotaz překračuje maximální povolený počet pracovních prostorů/oblastí", co dělat hned

V současné době existuje omezení na 25 oblastí a 200 pracovních prostorů, aby bylo možné zobrazit vaše data, budete muset snížit počet předplatných nebo skupin prostředků.

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Chci udělat změny nebo přidat další vizualizace Key Vault Insights, jak to mám udělat

Chcete-li provést změny, vyberte úpravou "režim úprav" a upravte sešit. potom můžete svou práci uložit jako nový sešit, který je svázán s určeným předplatným a skupinou prostředků.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Jaký je časový interval, po kterém se připnout k libovolné části sešitů

Používáme časový interval "automatického", proto závisí na tom, jaký časový rozsah je vybraný.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Jaký je časový rozsah, kdy je připnuté část sešitu

Časový rozsah bude záviset na nastavení řídicího panelu.

### <a name="why-do-i-not-see-any-data-for-my-key-vault-under-the-operations--latency-sections"></a>Proč se v sekcích Operations & latence nezobrazují žádná data pro moje Key Vault

Pokud chcete zobrazit data založená na protokolech, budete muset povolit protokoly pro všechny trezory klíčů, které chcete monitorovat. To se dá udělat v nastavení diagnostiky pro každý Trezor klíčů. Data budete muset poslat do určeného Log Analytics pracovního prostoru.

### <a name="i-have-already-enabled-logs-for-my-key-vault-why-am-i-still-unable-to-see-my-data-under-operations--latency"></a>Už jsem povolil protokoly pro moje Key Vault, proč se mi v operacích & latence pořád nedaří zobrazit moje data.

Diagnostické protokoly v současné době nefungují zpětně, takže se data začnou zobrazovat, jenom když dojde k provedeným akcím pro vaše trezory klíčů. V závislosti na tom, jak je Trezor klíčů aktivní, může trvat nějakou dobu v rozsahu od hodin až po jeden den.

Navíc pokud máte vybraný vysoký počet trezorů klíčů a předplatných, možná nebudete moct zobrazit data z důvodu omezení dotazů. Aby bylo možné zobrazit data, možná budete muset snížit počet vybraných předplatných nebo trezorů klíčů. 

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>Co když chci zobrazit další data nebo vytvořit vlastní vizualizace? Jak mohu dělat změny v Key Vault Insights

Existující sešit můžete upravit pomocí režimu úprav a pak uložit práci jako nový sešit, který bude mít všechny nové změny.

## <a name="next-steps"></a>Další kroky

Seznamte se s scénáři, které jsou navržené tak, aby podporovaly, vytváření nových a přizpůsobení existujících sestav a další informace najdete v tématu [vytváření interaktivních sestav pomocí Azure Monitorch sešitů](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview).
