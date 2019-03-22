---
title: Připojte se k systémům souborů v místním prostředí – Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace úloh a pracovních postupů, které se připojují k místním systémům souborů pomocí konektoru systému souborů prostřednictvím místní brány dat v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: a933824e9553ee7f638da495b46ebed19e04169d
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316962"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Připojení k místním systémům souborů pomocí Azure Logic Apps

Pomocí konektoru systému souborů a Azure Logic Apps můžete vytvořit automatizovaných úloh a pracovních postupů, které vytvořit a spravovat soubory na soubor v místním sdílet, například:  

- Vytvořit, získat, přidat, aktualizovat a odstraňovat soubory.
- Seznam souborů ve složkách nebo kořenových složek.
- Získáte obsah souboru a metadata.

Tento článek popisuje, jak můžete připojit k systému souborů v místním jak je popsáno v tomto ukázkovém scénáři: Zkopírujte soubor do Dropboxu nahraje do sdílené složky a pak pošle e-mailu. K zabezpečenému připojení a přístup k místním systémům, použijte aplikace logiky [na místní bránu dat](../logic-apps/logic-apps-gateway-connection.md). Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

V příkladu, budete potřebovat tyto položky:

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Než se aplikace logiky můžete připojit k místním systémům například souborového serveru systému, je potřeba [instalace a nastavení místní brány dat](../logic-apps/logic-apps-gateway-install.md). Tímto způsobem můžete používat instalaci brány při vytváření připojení systému souborů z aplikace logiky.

* A [účtu Dropbox](https://www.dropbox.com/), které můžete se zaregistrovat zadarmo. Svoje přihlašovací údaje jsou nezbytné pro vytvoření připojení mezi vaší aplikace logiky a účtu Dropbox. 

* Přístup k počítači, který se má systém souborů, které chcete použít. Například pokud se instalace brány dat na stejném počítači jako systém souborů, potřebujete přihlašovací údaje účtu pro daný počítač. 

* E-mailový účet od poskytovatele podporovaného v Logic Apps, jako je Office 365 Outlook, Outlook.com nebo Gmail. Pokud máte jiného poskytovatele, [tady se podívejte na seznam konektorů](https://docs.microsoft.com/connectors/). Tato aplikace logiky používá účet Office 365 Outlook. Pokud použijete jiný e-mailový účet, celkový postup bude stejný, ale vaše uživatelské rozhraní se může mírně lišit. 

* Základní znalosti o [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). V tomto příkladu je třeba prázdné aplikace logiky.

## <a name="add-trigger"></a>Přidat trigger

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. Do vyhledávacího pole zadejte jako filtr "dropboxu". Ze seznamu triggerů vyberte tento trigger: **Když se vytvoří soubor**

   ![Vyberte trigger Dropboxu](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Přihlaste se pomocí přihlašovacích údajů k účtu Dropbox a autorizaci přístupu k vašim datům Dropboxu pro Azure Logic Apps. 

1. Zadejte požadované informace pro trigger.

   ![Aktivační události Dropboxu](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Přidat akce

1. Pod triggerem zvolte **další krok**. Do vyhledávacího pole zadejte jako filtr "systém souborů". Ze seznamu akcí vyberte tuto akci: **Vytvořte soubor – systém souborů**

   ![Vyhledání konektoru systému souborů](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Pokud ještě nemáte připojení k systému souborů, zobrazí se výzva k vytvoření připojení.

   ![Vytvoření připojení](media/logic-apps-using-file-connector/file-system-connection.png)

   | Vlastnost | Požaduje se | Value | Popis | 
   | -------- | -------- | ----- | ----------- | 
   | **Název připojení** | Ano | <*Název připojení*> | Název, který chcete použít pro připojení | 
   | **Kořenová složka** | Ano | <*název kořenové složky*> | Kořenová složka pro systém souborů, například, pokud jste nainstalovali bránu místní data jako je například místní složka na počítači, kde je nainstalován na místní bránu dat, nebo složku pro sdílené síťové složky můžete přistupovat k počítači. <p>Příklad: `\\PublicShare\\DropboxFiles` <p>Kořenová složka je hlavní nadřazené složky, který se používá pro relativní cesty pro všechny akce související se soubory. | 
   | **Typ ověřování** | Ne | <*auth-type*> | Typ ověřování, který využívá systém souborů, například **Windows** | 
   | **Uživatelské jméno** | Ano | <*domény*>\\<*uživatelské jméno*> | Uživatelské jméno pro počítač, kde máte systému souborů | 
   | **Heslo** | Ano | <*your-password*> | Heslo k počítači, kde máte systému souborů | 
   | **gateway** | Ano | <*installed-gateway-name*> | Název pro dříve nainstalovanou bránu | 
   ||| 

1. Jakmile budete hotoví, vyberte **Vytvořit**.

   Logic Apps nakonfiguruje a otestuje připojení, ujistěte se, že připojení funguje správně. 
   Pokud je připojení správně nastavené, zobrazí se možnosti pro akci, kterou jste dříve vybrali. 

1. V **vytvořit soubor** akce, zadejte podrobnosti pro kopírování souborů z Dropboxu do kořenové složky ve sdílené složce v místním prostředí. Chcete-li přidat výstupy z předchozích kroků, klikněte do pole a vyberte z dostupných polí, jakmile se zobrazí v seznamu dynamického obsahu.

   ![Vytvoření souboru akce](media/logic-apps-using-file-connector/create-file-filled.png)

1. Teď přidejte akci Outlooku, která odešle e-mail, aby příslušné uživatele upozorní na nový soubor. Zadejte příjemce, název a text e-mailu. Pro účely testování můžete použít svou vlastní e-mailovou adresu.

   ![Odeslání e-mailové akce](media/logic-apps-using-file-connector/send-email.png)

1. Uložte svou aplikaci logiky. Otestujte aplikaci stisknutím po nahrání souboru do Dropboxu. 

   Aplikace logiky by měl zkopírujte soubor do místní sdílené složce a poslat příjemcům e-mailu zkopírovaný soubor.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/fileconnector/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Chcete-li pomoci při vylepšování Azure Logic Apps a konektorů, Hlasujte nebo Zanechte své nápady na [webu User Voice pro Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [připojení k místním datům](../logic-apps/logic-apps-gateway-connection.md) 
* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
