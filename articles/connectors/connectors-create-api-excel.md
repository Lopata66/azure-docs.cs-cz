---
title: Připojení k Excelu Online – Azure Logic Apps
description: Správa dat pomocí aplikace Excel Online rozhraní REST API a Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
tags: connectors
ms.topic: article
ms.date: 08/23/2018
ms.openlocfilehash: 28739ad65462acc9f2d2ed7db1e9ed14d19f032c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60311960"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Spravovat data aplikace Excel Online s Azure Logic Apps

S Azure Logic Apps a konektoru Excelu Online můžete vytvořit automatizovaných úloh a pracovní postupy založené na vašich dat v Excelu Online pro firmy nebo OneDrive. Tento konektor zajišťuje akce, které vám pomohou pracovat s vašimi daty a spravovat tabulky, například:

* Vytvoření nových listů a tabulky.
* Získání a správě listy, tabulky a řádky.
* Přidáte jednoho řádky a sloupce klíče.

Pak můžete výstup z těchto akcí s akcemi pro ostatní služby. Například pokud použijete akci, která se vytvoří každý týden listy, můžete použít jinou akci, která odešle e-mail s potvrzením pomocí konektoru Office 365 Outlook.

Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> [Excelu Online pro firmy](/connectors/excelonlinebusiness/) a [Excelu Online, Onedrivu](/connectors/excelonline/) konektory fungují s Azure Logic Apps a se liší od [konektor aplikace Excel pro PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

* [Účet Office 365](https://www.office.com/) pro váš pracovní účet nebo osobní účet Microsoft

  Data aplikace Excel může existovat jako soubor hodnot oddělených čárkami (CSV) ve složce úložiště, třeba ve Onedrivu. 
  Můžete také použít stejný soubor CSV s [plochého souboru konektoru](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přistupovat k datům v Excelu Online. Tento konektor zajišťuje jenom akce, takže ke spuštění aplikace logiky vyberte samostatné aktivační událost, například, **opakování** aktivační události.

## <a name="add-excel-action"></a>Přidání akce aplikace Excel

1. V [webu Azure portal](https://portal.azure.com), otevření aplikace logiky v návrháři aplikace logiky, pokud není otevřen.

1. Pod triggerem zvolte **nový krok**.

1. Do vyhledávacího pole zadejte jako filtr "excel". V seznamu akcí vyberte požadovanou akci.

1. Pokud se zobrazí výzva k přihlášení ke svému účtu Office 365, zvolte **přihlášení**.

   Vaše přihlašovací údaje autorizaci aplikace logiky k vytvoření připojení k Excelu Online a přístup k vašim datům.

1. Pokračujte v poskytování nezbytné podrobnosti pro vybranou akci a vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou triggery, akce a omezení, jak je popsáno v konektoru OpenAPI (dříve Swagger) soubory, naleznete v tématu tyto stránky s referenčními informacemi konektoru:

* [Excel Online pro firmy](/connectors/excelonlinebusiness/)
* [Excel Online pro OneDrive](/connectors/excelonline/)

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
