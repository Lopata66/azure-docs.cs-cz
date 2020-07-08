---
title: 'Importovat data: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul importovat data v Azure Machine Learning k načtení dat do kanálu strojového učení z existujících cloudových datových služeb.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: e47cb1180bbc6eaaaffd79b78563ab1b1d5e016a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85611811"
---
# <a name="import-data-module"></a>Importovat data modul

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Pomocí tohoto modulu můžete načíst data do kanálu strojového učení z existujících cloudových datových služeb. 

> [!Note]
> Všechny funkce, které tento modul poskytuje, může udělat **úložiště dat** a **datové sady** na úvodní stránce pracovního prostoru. Doporučujeme používat **úložiště** dat a **datovou sadu** , které zahrnují další funkce, jako je monitorování dat. Další informace najdete v článku [Jak získat přístup k datům](../how-to-access-data.md) a [jak zaregistrovat datové sady](../how-to-create-register-datasets.md) .
> Po registraci můžete datovou sadu najít v kategorii **DataSets**  ->  **Moje datové sady** v rozhraní návrháře. Tento modul je vyhrazený pro uživatele v rámci studia (Classic) pro známé prostředí. 
>

Modul **Import dat** podporuje čtení dat z následujících zdrojů:

- Adresa URL prostřednictvím protokolu HTTP
- Cloudové úložiště Azure přes [**úložiště dat**](../how-to-access-data.md))
    - Kontejner objektů blob Azure
    - Sdílená složka Azure
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL Database
    - PostgreSQL Azure    

Než použijete cloudové úložiště, musíte nejdřív zaregistrovat úložiště dat v pracovním prostoru Azure Machine Learning. Další informace najdete v tématu [Jak získat přístup k datům](../how-to-access-data.md). 

Jakmile definujete požadovaná data a připojíte se ke zdroji, **[importujte data](./import-data.md)** datový typ každého sloupce v závislosti na hodnotách, které obsahuje, a načte data do kanálu návrháře. Výstupem **importu dat** je datová sada, která se dá použít s libovolným kanálem návrháře.

Pokud se vaše zdrojová data změní, můžete datovou sadu aktualizovat a přidat nová data tak, že znovu spustíte [importovaná data](./import-data.md).

> [!WARNING]
> Pokud je váš pracovní prostor ve virtuální síti, musíte nakonfigurovat úložiště dat tak, aby používalo funkce vizualizace dat daného návrháře. Další informace o tom, jak používat úložiště dat a datové sady ve virtuální síti, najdete v tématu [izolace sítě během školení & odvození s privátními virtuálními sítěmi](../how-to-enable-virtual-network.md#machine-learning-studio).


## <a name="how-to-configure-import-data"></a>Jak konfigurovat importovaná data

1. Přidejte do svého kanálu modul **Import dat** . Tento modul můžete najít v kategorii **vstup a výstup dat** v návrháři.

1. Výběrem modulu otevřete pravé podokno.

1. Vyberte **zdroj dat**a vyberte typ zdroje dat. Může se jednat o protokol HTTP nebo úložiště dat.

    Pokud zvolíte úložiště dat, můžete vybrat existující úložiště dat, která jsou už zaregistrovaná v pracovním prostoru Azure Machine Learning, nebo vytvořit nové úložiště dat. Pak definujte cestu k datům, která chcete importovat do úložiště dat. Cestu můžete snadno procházet kliknutím na **Procházet cesta** ![ import-data-cesta.](media/module/import-data-path.png)

1. Vyberte schéma verze Preview pro filtrování sloupců, které chcete zahrnout. V možnostech analýzy můžete také definovat upřesňující nastavení, jako je oddělovač.

    ![import-data-Preview](media/module/import-data.png)

1. Zaškrtávací políčko **znovu vygenerovat výstup**určuje, zda se má spustit modul pro opětovné vygenerování výstupu za běhu. 

    Ve výchozím nastavení je Nevybraná, což znamená, že pokud byl modul spuštěn se stejnými parametry dřív, systém použije výstup z posledního spuštění k omezení doby běhu. 

    Pokud je vybraná, systém znovu spustí modul a obnoví výstup. Tuto možnost vyberte, pokud se budou zdrojová data v úložišti aktualizovat, může vám to usnadnit získání nejnovějších dat.


1. Odešlete kanál.

    Když import dat načte data do návrháře, odvodí datový typ každého sloupce na základě hodnot, které obsahuje, buď číselného, nebo kategorií.

    Pokud je hlavička k dispozici, záhlaví se použije k pojmenování sloupců výstupní datové sady.

    Pokud v datech nejsou žádná existující záhlaví sloupců, generují se nové názvy sloupců pomocí formátu Sloupec1, col2,... , coln*.

## <a name="results"></a>Výsledky

Po dokončení importu klikněte na výstupní datovou sadu a vyberte **vizualizovat** , abyste viděli, jestli se data úspěšně importovala.

Pokud chcete data uložit pro opakované použití, místo importu nové sady dat pokaždé, když je kanál spuštěný, vyberte ikonu **zaregistrovat datovou sadu** na kartě **výstupy** na pravém panelu modulu. Vyberte název datové sady. Uložená datová sada zachovává data v době uložení, datová sada se při opětovném spuštění kanálu neaktualizuje, i když se datová sada v kanálu změní. To může být užitečné při pořizování snímků dat.

Po importu dat možná budete potřebovat další přípravy na modelování a analýzu:

- Pomocí možnosti [Upravit metadata](./edit-metadata.md) můžete změnit názvy sloupců, zpracovat sloupec jako jiný datový typ nebo označit, že některé sloupce jsou popisky nebo funkce.

- Pomocí [Vybrat sloupce v datové sadě](./select-columns-in-dataset.md) můžete vybrat podmnožinu sloupců pro transformaci nebo použití při modelování. Transformované nebo odebrané sloupce lze snadno znovu připojit k původní datové sadě pomocí modulu [Přidat sloupce](./add-columns.md) .  

- Pomocí [oddílu a vzorku](./partition-and-sample.md) rozdělte datovou sadu, proveďte vzorkování nebo získejte horních n řádků.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
