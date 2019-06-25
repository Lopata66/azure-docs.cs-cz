---
title: Jak vyplnit formulář technické informace
description: Vysvětluje, jak zadat hodnoty pro ve formuláři technické informace pro novou aplikaci Dynamics 365 Business Central.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: dbc38fab5bd8e55f6dd280ecc46af1b1a5ae7ede
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935071"
---
<a name="how-to-fill-out-the-technical-info-form"></a>Jak vyplnit formulář technické informace
===========================================

1.  V **zvolte typ aplikace** části, nahrát soubor balíčku rozšíření (.app) a všechny soubory balíčku rozšíření vaše rozšíření obsahuje závislost na.

    ![Balíček aplikace informace](./media/d365-financials/image015.png)

-   **Soubor balíčku rozšíření** --povinné – přípona souboru balíčku (.app).

-   **Soubor balíčku závislost** – vyžaduje, pokud aplikace obsahuje závislost na jiné aplikace publikovat v AppSource. Tento soubor .app už byla publikována rozšíření v AppSource, kde je závislá aktuální aplikaci. 

-   **Soubor balíčku knihovny** – vyžaduje, pokud aplikace obsahuje závislost na jiné aplikace, která je *není* publikovat v AppSource. Tato .app soubor existující aplikace, ale jeden, který nebyl a nebudou publikovány v AppSource.

-   **Automatizace testování aplikace** --povinné – VS programového testu balíček, který je třeba vytvořit pro automatizované testování rozšíření.

1. V **Další informace o rozšíření** části, odeslat další informace o rozšíření. Tyto informace se používají při ověřování.

   ![Další informace o formuláři rozšíření aplikace](./media/d365-financials/image016.png)


-   **Adresa URL pro dokumentaci k produktu** --požadované – adresa URL pro dokumentaci pro rozšíření.

-   **Scénáře použití klíče** --povinné – dokument, který obsahuje krok za krokem instalace a použití podrobnosti pro rozšíření. Příklad najdete v článku [dokumentaci pro uživatele scénář](https://docs.microsoft.com/dynamics-nav/compliance/apptest-userscenario/).

-   **Cílová verze** --povinné – vyberte verzi, na kterém chcete aplikaci nasadit. Vyberte **aktuální** nasazení v aktuální verze na trhu. Vyberte **vedle dílčí** nasazení s další podverze uvolnit. Vyberte **další hlavní verze** nasazení s další hlavní verze uvolnit.

-   **Vyžaduje SKU úrovně Premium** --nepovinné – vyberte Premium tlačítko, pokud aplikace vyžaduje SKU úrovně Premium. Správa služeb a výroby jsou k dispozici pouze na premium. Podrobné informace o základní vs Premium najdete v článku [změna které funkce jsou zobrazeny](https://docs.microsoft.com/dynamics365/financials/ui-experiences).

-   **Vysvětlení chyby analýzy kódu** --nepovinné – dokument, který obsahuje seznam a opravňuje veškerý kód, který nesplňuje požadavky.

-   **Vysvětlení vliv základní funkce** --nepovinné – dokument, který uvádí a vysvětluje všechny základní funkce, která je omezena rozšíření.

-   **Testovací účty** --nepovinné – uživatelské účty pro vzdálené služby, webové servery, atd., které bude potřeba k dokončení testu koncového použití.

-   **Výjimky požadavky UX** --nepovinné – dokument, který obsahuje seznam a opravňuje jakékoli uživatelské prostředí požadavky nesplněny rozšíření.

Dalším krokem je přidání prezentace podrobností pro vaši nabídku.
