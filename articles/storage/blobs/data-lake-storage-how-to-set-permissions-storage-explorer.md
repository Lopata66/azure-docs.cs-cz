---
title: Nastavení oprávnění pro Data Lake Storage Gen2 pomocí Průzkumníka služby Azure Storage
description: V tomto jak, se dozvíte, jak nastavit oprávnění pomocí Průzkumníka služby Azure Storage pro souborů a adresářů v účtu úložiště, které podporuje Azure Data Lake Storage Gen2.
services: storage
author: roygara
ms.custom: mvc
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: d1e941e41871de4a11641254f40c53285b096807
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546299"
---
# <a name="set-file-and-directory-level-permissions-using-azure-storage-explorer-with-azure-data-lake-storage-gen2"></a>Nastavení souborů a adresářů úrovně oprávnění pomocí Průzkumníka služby Azure Storage s Azure Data Lake Storage Gen2

Soubory uložené v Azure Data Lake Storage Gen2 podporují podrobné oprávnění a přístup k řízení seznamu ACL. Společně jemně odstupňovaná oprávnění a správy seznamu ACL umožňují spravovat přístup k vašim datům na velmi podrobné úrovni.

V tomto článku se dozvíte, jak používat Azure Storage Explorer:

> [!div class="checklist"]
> * Nastavte oprávnění na úrovni souborů
> * Nastavení úrovně oprávnění pro adresář
> * Přidat uživatele nebo skupiny na seznam řízení přístupu

## <a name="prerequisites"></a>Požadavky

Abyste mohli co nejlépe znázornění procesu, vyžadujeme, že provedete naše [rychlý start Azure Storage Explorer](data-lake-storage-Explorer.md). Tím se zajistí, že váš účet úložiště bude v nejvhodnější stav (systém souborů vytvořené a daty nahranými do něj).

## <a name="managing-access"></a>Správa přístupu

Oprávnění můžete nastavit v kořenovém adresáři vašeho systému souborů. Uděláte to tak, musíte být přihlášeni do Průzkumníka služby Azure Storage pomocí svého individuálního účtu s právy a provede akci dle (nikoli pomocí připojovacího řetězce). Klikněte pravým tlačítkem na váš systém souborů a vyberte **spravovat oprávnění**, přivedou nahoru **spravovat oprávnění** dialogové okno.

![Průzkumník služby Microsoft Azure Storage - spravovat přístup k adresáři](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

**Spravovat oprávnění** dialogové okno umožňuje spravovat oprávnění pro vlastníka a vlastníci skupiny. Umožňuje také můžete přidat nové uživatele a skupiny do seznamu řízení přístupu, pro kterého pak můžete spravovat oprávnění.

Chcete-li přidat nové uživatele nebo skupinu do seznamu řízení přístupu, **přidat uživatele nebo skupiny** pole.

Zadejte odpovídající položku Azure Active Directory (AAD) chcete přidat do seznamu a pak vyberte **přidat**.

Uživatel nebo skupina se teď budou zobrazovat v **uživatelů a skupin:** pole, abyste mohli začít spravovat jejich oprávnění.

> [!NOTE]
> Je to osvědčený postup a doporučujeme vytvořit skupinu zabezpečení ve službě AAD a Udržovat oprávnění skupině místo jednotlivým uživatelům. Podrobnosti o toto doporučení, stejně jako ostatní osvědčené postupy, najdete v části [osvědčené postupy pro službu Data Lake Storage Gen2](data-lake-storage-best-practices.md).

Existují dvě kategorie můžete přiřadit oprávnění: přístup k seznamy řízení přístupu a výchozí seznamy ACL.

* **Přístup**: Přístupové seznamy ACL řízení přístupu k objektu. Soubory a adresáře měli seznamy ACL.

* **Výchozí**: Šablony seznamů ACL asociovaná s adresářem, který určuje přístup seznamy ACL pro všechny podřízené položky, které jsou vytvořené v rámci tohoto adresáře. Výchozí seznamy ACL pro soubory není definován.

V obou těchto kategorií jsou k dispozici tři oprávnění, které pak můžete přiřadit na soubory nebo adresáře: **Čtení**, **zápisu**, a **provést**.

>[!NOTE]
> Výběr sem nenastaví oprávnění na všechny aktuálně existující položku do adresáře. Musíte přejít na jednotlivé položky a nastavit oprávnění ručně, pokud soubor již existuje.

Můžete spravovat oprávnění pro jednotlivé adresáře, stejně jako jednotlivé soubory, které jsou, co vám umožňuje řídit podrobné přístup. Proces pro správu oprávnění pro soubory a adresáře je stejný, jak je popsáno výše. Klikněte pravým tlačítkem na soubor nebo adresář, který chcete spravovat oprávnění na a opakujte stejný postup.

## <a name="next-steps"></a>Další postup

V tomto návodu jste zjistili, jak nastavit oprávnění pro soubory a adresáři používajícími **Průzkumníka služby Azure Storage**. Další informace týkající se seznamů ACL, včetně výchozí seznamy ACL, přístup k seznamy ACL, jejich chování a jejich odpovídající oprávnění, i nadále naše konceptuální článek k tomuto tématu.

> [!div class="nextstepaction"]
> [Řízení přístupu ve službě Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
