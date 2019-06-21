---
title: Připojte se k účtu SFTP – Azure Logic Apps
description: Automatizace úloh a procesů, které monitorování, vytvářet, spravovat, odesílat a přijímat soubory pro server SFTP přes SSH pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/18/2019
tags: connectors
ms.openlocfilehash: 5b80339e90947b7cc45b1edb5df00652a13291ac
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293475"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Monitorování, vytvářet a spravovat SFTP soubory pomocí Azure Logic Apps

Automatizace úloh, které monitorování, vytvářet, odesílat a přijímat soubory na [rozhraní Secure File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/) server, můžete vytvořit a automatizovat pracovní postupy integrace s využitím Azure Logic Apps a konektoru SFTP. SFTP je síťový protokol, který poskytuje přístup k souborům, přenosu souborů a správu přes jakýkoli spolehlivý datový proud. Tady je příklad úlohy, které můžete automatizovat:

* Monitorování, když jsou soubory přidány nebo změněny.
* Získat, vytvářet, zkopírujte, seznam, aktualizovat a odstraňovat soubory.
* Získáte obsah souboru a metadata.
* Extrahujte archivy do složek.

Můžete použít aktivační události, které sledovat události na vašem serveru SFTP a zpřístupnit výstup dalších akcí. Můžete použít akce, které provádění různých úloh na vašem serveru SFTP. Také můžete mít další akce ve vaší aplikaci logiky použít výstup z akcí SFTP. Například pokud pravidelně Načtení souborů ze serveru SFTP, můžete odeslat e-mailová upozornění o těchto souborech a jejich obsah s použitím konektoru Office 365 Outlook nebo konektor Outlook.com. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limits

Konektor SFTP zpracovává pouze soubory, které jsou *50 MB nebo méně* a nepodporuje [bloků zpráv](../logic-apps/logic-apps-handle-large-messages.md). Pro větší soubory, použijte [konektoru SFTP-SSH](../connectors/connectors-sftp-ssh.md). Rozdíly v konektoru SFTP a konektor SFTP-SSH, najdete v tématu [porovnání SFTP SSH a SFTP](../connectors/connectors-sftp-ssh.md#comparison) v článku SFTP-SSH.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* SFTP server adresu a účet pověření, které umožňují přístup k účtu SFTP aplikace logiky. Použít [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) protokolu, budete potřebovat přístup k privátní klíč SSH a heslo privátního klíče SSH.

  > [!NOTE]
  >
  > Konektor SFTP podporuje tyto formáty privátního klíče: OpenSSH, ssh.com a PuTTY
  >
  > Při vytváření aplikace logiky po přidání SFTP triggeru nebo akce, které chcete, budete muset zadat informace o připojení k serveru SFTP. 
  > Pokud používáte privátní klíč SSH, ujistěte se, že jste ***kopírování*** klíče z vašeho SSH soubor privátního klíče, a ***vložte*** tento klíč do podrobností připojení ***není ručně zadat nebo upravit klíč***, což může způsobit selhání připojení. 
  > Další informace najdete v dalších krocích v tomto článku.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k účtu SFTP. Spuštění pomocí aktivační událost SFTP [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci SFTP, spusťte svou aplikaci logiky s další aktivační události, například, **opakování** aktivační události.

## <a name="how-sftp-triggers-work"></a>Jak SFTP aktivuje pracovní

SFTP aktivuje pracovní dotazování systém souborů protokolu SFTP a vyhledáním každý soubor, který se změnil od posledního cyklického dotazování. Některé nástroje umožňují zachovat časové razítko, když se změní soubory. V těchto případech budete muset zakázat tuto funkci, aktivační událost můžete pracovat. Tady jsou některé běžné nastavení:

| Klient protokolu SFTP | Akce |
|-------------|--------|
| Winscp | Přejděte na **možnosti** > **Předvolby** > **přenos** > **upravit**  >  **Zachovat časové razítko** > **zakázat** |
| FileZilla | Přejděte na **přenos** > **zachovat časová razítka přenášených souborů** > **zakázat** |
|||

Když aktivační události vyhledá nový soubor, trigger zkontroluje, zda nový soubor úplné a částečně napsané. Soubor může například mít změny v průběhu při trigger bude kontrolovat souborového serveru. Aktivační událost se pokud chcete vyhnout, vrací částečně napsané souborů, poznámky časové razítko pro soubor, který obsahuje poslední změny, ale nevrací okamžitě tento soubor. Aktivační událost vrátí soubor pouze v případě, že dotazování serveru znovu. Toto chování může způsobit zpoždění, které je až dvakrát triggeru interval dotazování.

## <a name="connect-to-sftp"></a>Připojení k protokolu SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. V případě prázdné logic apps do vyhledávacího pole zadejte jako filtr "sftp". V seznamu triggerů vyberte trigger, který chcete.

   -nebo-

   Pro existující aplikace logiky v posledním kroku, ve které chcete přidat akci, zvolte **nový krok**. Do vyhledávacího pole zadejte jako filtr "sftp". V seznamu akcí vyberte požadovanou akci.

   Přidání akce mezi kroky, přesuňte ukazatel nad šipku mezi kroky. Vyberte znaménko plus ( **+** ), který se zobrazí a pak vyberte **přidat akci**.

1. Zadejte potřebné podrobnosti o připojení.

   > [!IMPORTANT]
   >
   > Když zadáte privátní klíč SSH v **privátní klíč SSH** vlastnosti těchto dodatečných kroků, které pomohou Ujistěte se, že poskytují kompletní a správný hodnotu pro tuto vlastnost. 
   > Neplatný klíč způsobí selhání připojení.

   I když můžete použít libovolný textový editor, tady jsou ukázkové kroky, které ukazují, jak správně zkopírujte a vložte váš klíč pomocí Notepad.exe jako příklad.

   1. Otevření souboru privátního klíče SSH v textovém editoru. Tyto kroky používají stejně jako v příkladu programu Poznámkový blok.

   1. Na poznámkového bloku **upravit** nabídce vyberte možnost **Vybrat vše**.

   1. Vyberte **upravit** > **kopírování**.

   1. V protokolu SFTP triggeru nebo akce, které jste přidali, vložte *kompletní* klíče, které jste zkopírovali do **privátní klíč SSH** vlastnost, která podporuje více řádků. ***Ujistěte se, že vložíte*** klíč. ***Není ručně zadat nebo upravit klíč***.

1. Až budete mít zadání podrobností o připojení, zvolit **vytvořit**.

1. Zadejte potřebné podrobnosti o vybrané aktivační události nebo akce a pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="examples"></a>Příklady

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP aktivační události: Při přidání nebo změně souboru

Tato aktivační událost se spustí pracovní postup aplikace logiky souboru při přidání nebo změně na SFTP server. Například můžete přidat podmínku, která zkontroluje obsah souboru a získá obsah založen na tom, jestli obsah splňují zadanou podmínku. Potom přidáte akci, která získá obsah souboru a umístí tento obsah do složky na serveru SFTP.

**Příklad organizace**: Tato aktivační událost můžete použít k monitorování složky aplikace SFTP pro nové soubory, které představují objednávek zákazníků. Můžete pak použít akci SFTP jako **získat obsah souboru** tak získat obsah pořadí pro další zpracování a uložení do databáze objednávek tohoto pořadí.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>SFTP akce: Získat obsah

Tato akce načte obsah ze souboru na SFTP server. Takže například můžete přidat aktivační událost z předchozího příkladu a podmínku, která musí splňovat obsahu souboru. Pokud je podmínka pravdivá, můžete spustit akci, která získá obsah.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/sftpconnector/).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
