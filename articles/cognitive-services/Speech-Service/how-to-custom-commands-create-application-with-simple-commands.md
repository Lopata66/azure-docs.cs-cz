---
title: 'Postupy: vytváření aplikací pomocí jednoduchých příkazů – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak vytvořit a otestovat hostovanou aplikaci vlastních příkazů pomocí jednoduchých příkazů.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 7f98b2c94659c083f5dbd2c43fd9015f8b60a9db
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307501"
---
# <a name="create-application-with-simple-commands"></a>Vytvoření aplikace pomocí jednoduchých příkazů

V tomto článku získáte informace o těchto tématech:
 - Vytvoření prázdné aplikace
 - Aktualizace prostředků LUIS
 - Přidání některých základních příkazů do aplikace Custom Commands

## <a name="create-empty-application"></a>Vytvořit prázdnou aplikaci
Vytvořte prázdnou aplikaci Custom Commands. Podrobnosti najdete v [rychlém](quickstart-custom-commands-application.md)startu. Tentokrát namísto importu projektu vytvoříte prázdný projekt.

   > [!div class="mx-imgBorder"]
   > ![Vytvoření projektu](media/custom-commands/create-new-project.png)

## <a name="update-luis-resources-optional"></a>Aktualizace prostředků LUIS (volitelné)

Můžete aktualizovat prostředek pro vytváření obsahu, který jste vybrali v okně **Nový projekt** , a nastavit předpověď prostředku. Prostředek předpovědi se používá pro rozpoznávání při publikování vlastní aplikace příkazů. Během fází vývoje a testování nepotřebujete předpověď prostředků.

## <a name="add-turnon-command"></a>Přidat příkaz TurnOn

V aplikaci Customer – vlastní příkazy v programu **Smart Room – Lite** přidejte jednoduchý příkaz, který zpracuje utterance, `turn on the tv` a odpoví zprávou `Ok, turning the tv on` .

1. Vytvořte nový příkaz výběrem **příkazu nový** v horní části levého podokna. Otevře se **nové okno příkazu** .
1. Zadejte hodnotu pole **název** jako **TurnOn**.
1. Vyberte **Vytvořit**.

V prostředním podokně jsou uvedeny různé vlastnosti příkazu. Nakonfigurujete následující vlastnosti příkazu. Vysvětlení všech vlastností konfigurace příkazu naleznete v [odkazech](./custom-commands-references.md).

| Konfigurace            | Description                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Příklady vět** | Příklad projevy, jak může uživatel aktivovat tento příkaz                                                                 |
| **Parametry**       | Informace potřebné k dokončení příkazu                                                                                |
| **Pravidla dokončení** | Akce, které mají být provedeny pro splnění příkazu. Například, pokud chcete reagovat na uživatele nebo komunikovat s jinou webovou službou. |
| **Pravidla interakce**   | Další pravidla pro zpracování složitějších nebo složitých situací                                                              |


> [!div class="mx-imgBorder"]
> ![Vytvoření příkazu](media/custom-commands/add-new-command.png)

### <a name="add-example-sentences"></a>Přidat ukázkové věty

Pojďme začít s **příkladem části věty** a zadat příklad, co může uživatel vyslovit.

1. V prostředním podokně vyberte část se **vzorovými větami** .
1. V pravém krajním podokně přidejte příklady:

    ```
    turn on the tv
    ```

1.  V horní části podokna vyberte **Uložit** .

Teď ale nemusíme mít parametry, takže se můžeme přesunout do oddílu **pravidla dokončení** .

### <a name="add-a-completion-rule"></a>Přidat pravidlo dokončení

V dalším kroku musí mít příkaz pravidlo dokončení. Toto pravidlo přikáže uživateli, že je prováděna akce plnění. Chcete-li si přečíst další informace o pravidlech a pravidlech dokončení, přejít na [odkazy](./custom-commands-references.md).

1. Vyberte **výchozí pravidlo dokončení** a upravte ho následujícím způsobem: 

    
    | Nastavení    | Navrhovaná hodnota                          | Popis                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Název**       | ConfirmationResponse                  | Název popisující účel pravidla          |
    | **Podmínky** | Žádné                                     | Podmínky, které určují, kdy se pravidlo dá spustit    |
    | **Akce**    | Odeslat odezvu na řeč > jednoduchý editor > první variaci >`Ok, turning the tv on` | Akce, která se má provést, když je podmínka pravidla pravdivá |
    

1. Alternativně můžete odstranit existující výchozí pravidlo dokončení a vytvořit nový, a to tak, že v horní části podokna prostředníku vyberete **Přidat** .
1. Zadejte hodnotu v části **Name (název** ).
1. Přidat akci.
   1. Vytvořte akci výběrem možnosti **přidat akci** v části **Akce** .
   1. V okně **Nová akce** v seznamu **typ** vyberte **Odeslat odezvu řeči**.
   1. V části **odpověď**vyberte **jednoduchý editor**.
   1. Do pole **první variace** zadejte hodnotu pro odezvu jako `Ok, turning the tv on` .

   > [!div class="mx-imgBorder"]
   > ![Vytvoření odpovědi na řeč](media/custom-commands/create-speech-response-action.png)

1. Akci uložte kliknutím na **Uložit** .
1. Zpátky v části **pravidla dokončování** vyberte **Uložit** a uložte všechny změny. 


### <a name="try-it-out"></a>Vyzkoušejte si to.

Testování chování pomocí panelu chat testu
1. Vyberte ikonu **výuky** v pravém horním rohu okna.
1. Po dokončení školení vyberte **test**. Vyzkoušejte následující utterance prostřednictvím hlasu nebo textu:
    - Vstup: zapnutí televizního vysílání
    - Výstup: OK, zapnutí televizního vysílání


> [!div class="mx-imgBorder"]
> ![Testování pomocí webového chatu](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> Na panelu Test můžete vybrat možnost **zapnout podrobnosti** pro informace o tom, jakým způsobem byl zpracován Tento hlasový/textový vstup.  

## <a name="add-settemperature-command"></a>Přidat příkaz SetTemperature

Teď přidejte ještě jeden **SetTemperature** příkazu, který bude trvat jeden utterance, `set the temperature to 40 degrees` a odpovědět se zprávou `Ok, setting temperature to 40 degrees` .

Použijte postup, jak je znázorněno v příkazu **TurnOn** k vytvoření nového příkazu pomocí ukázkové věty "**nastavení teploty na 40 stupňů**".

Pak upravte existující pravidla dokončení **dokončeno** následujícím způsobem:

| Nastavení    | Navrhovaná hodnota                          |
| ---------- | ---------------------------------------- |
| Name  | ConfirmationResponse                  |
| Podmínky | Žádné                                     |
| Akce    | Odeslat odezvu na řeč > jednoduchý editor > první variaci >`Ok, setting temperature to 40 degrees` |

Výběrem **Uložit** uložte všechny změny do příkazu.

## <a name="add-setalarm-command"></a>Přidat příkaz SetAlarm
Vytvořte nový příkaz **SetAlarm** pomocí ukázkové věty "**Nastavení alarmu pro 9 – zítra**". Pak upravte existující pravidla dokončení **dokončeno** následujícím způsobem:

| Nastavení    | Navrhovaná hodnota                          |
| ---------- | ---------------------------------------- |
| Název pravidla  | ConfirmationResponse                  |
| Podmínky | Žádné                                     |
| Akce    | Odeslat odezvu na řeč > jednoduchý editor > první variaci >`Ok, setting an alarm for 9 am tomorrow` |

Výběrem **Uložit** uložte všechny změny do příkazu.

## <a name="try-it-out"></a>Vyzkoušejte si to.

Testování chování pomocí panelu chat testu
1. Vyberte **vlak**. Po úspěšném školení vyberte **test** a vyzkoušejte:
    - Zadáte: Nastavte teplotu na 40 stupňů.
    - Očekávaná odpověď: OK, nastavení teploty na 40 stupňů
    - Zadáte: zapnout televizor
    - Očekávaná odpověď: OK, zapnutí televizního vysílání
    - Zadáte: nastavte alarm pro 9 – zítra
    - Očekávaná odpověď: OK, nastavení alarmu pro 9 – zítra

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postupy: Přidání parametrů k příkazům](./how-to-custom-commands-add-parameters-to-commands.md)
