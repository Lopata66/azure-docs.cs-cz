---
title: Použití Průzkumník služby Azure Storage s Azure Data Lake Storage Gen2
description: Pomocí Průzkumník služby Azure Storage můžete spravovat adresáře a seznamy řízení přístupu (ACL) souborů a adresářů v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: fca9fa8a964c6c9d69ffbb3036bd4774e0d1bd34
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381926"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Správa adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2 pomocí Průzkumník služby Azure Storage

V tomto článku se dozvíte, jak pomocí [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) vytvářet a spravovat adresáře, soubory a oprávnění v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](data-lake-storage-quickstart-create-account.md) pokynů.
> * Průzkumník služby Azure Storage nainstalované na místním počítači. Instalaci Průzkumníka služby Azure Storage pro Windows, Macintosh nebo Linux popisuje článek [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Přihlášení k Průzkumník služby Storage

Když poprvé spustíte Průzkumníka služby Storage, objeví se okno **Průzkumník služby Microsoft Azure Storage – Připojení**. I když Průzkumník služby Storage poskytuje několik způsobů, jak se připojit k účtům úložiště, v současné době se podporuje jenom jeden způsob správy seznamů ACL.

|Úloha|Účel|
|---|---|
|Přidat účet Azure | Vás přesměruje na přihlašovací stránku vaší organizace a provede ověření v Azure. V současné době se jedná o jedinou podporovanou metodu ověřování, pokud chcete spravovat a nastavovat seznamy ACL.|
|Použití připojovacího řetězce nebo sdíleného přístupového podpisu URI | Ty můžete použít k přímému přístupu ke kontejneru nebo účtu úložiště pomocí tokenu SAS nebo sdíleného připojovacího řetězce. |
|Použít klíč a název účtu úložiště| Pro připojení k úložišti Azure můžete použít název a klíč.|

Vyberte **Přidat účet Azure** a klikněte na **Přihlásit se.** .. Podle pokynů na obrazovce se přihlaste k účtu Azure.

![Průzkumník služby Microsoft Azure Storage – okno Připojení](media/storage-quickstart-blobs-storage-explorer/connect.png)

Po dokončení připojení se Průzkumník služby Azure Storage otevře se zobrazenou kartou **Průzkumník**. V tomto zobrazení uvidíte místní úložiště i přehled všech svých účtů úložiště Azure nakonfigurovaných pomocí účtů [emulátoru úložiště Azure](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) nebo prostředí [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

![Průzkumník služby Microsoft Azure Storage – okno Připojení](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Vytvoření kontejneru

Kontejner obsahuje adresáře a soubory. Pokud ho chcete vytvořit, rozbalte účet úložiště, který jste vytvořili v kroku pokračování. Klikněte pravým tlačítkem na **Kontejnery objektů blob** a vyberte **Vytvořit kontejner objektů blob**. Zadejte název kontejneru. Seznam pravidel a omezení pro pojmenování kontejnerů najdete v části [vytvoření kontejneru](storage-quickstart-blobs-dotnet.md#create-a-container) . Po dokončení stiskněte klávesu **ENTER** a kontejner se vytvoří. Jakmile se kontejner úspěšně vytvoří, zobrazí se ve složce **kontejnery objektů BLOB** pro vybraný účet úložiště.

![Průzkumník služby Microsoft Azure Storage – vytváření kontejneru](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Vytvoření adresáře

Chcete-li vytvořit adresář, vyberte kontejner, který jste vytvořili v kroku pokračování. Na pásu karet kontejner klikněte na tlačítko **Nová složka** . Zadejte název adresáře. Po dokončení stiskněte klávesu **ENTER** a vytvořte adresář. Po úspěšném vytvoření adresáře se zobrazí v okně editoru.

![Průzkumník služby Microsoft Azure Storage – vytváření adresáře](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Nahrajte objekty blob do adresáře.

Na pásu karet adresář zvolte tlačítko **nahrát** . Tato operace umožňuje nahrát složku nebo soubor.

Vyberte soubory nebo složku k nahrání.

![Průzkumník služby Microsoft Azure Storage – nahrání objektu blob](media/data-lake-storage-explorer/upload-file.png)

Když vyberete **OK**, vybrané soubory se zařadí do fronty a postupně se nahrají. Po dokončení nahrávání se výsledky zobrazí v okně **Aktivity**.

## <a name="view-blobs-in-a-directory"></a>Zobrazení objektů BLOB v adresáři

V aplikaci **Průzkumník služby Azure Storage** vyberte adresář pod účtem úložiště. Hlavní podokno zobrazuje seznam objektů BLOB ve vybraném adresáři.

![Průzkumník služby Microsoft Azure Storage – vypíše objekty BLOB v adresáři.](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Stáhnout objekty blob

Pokud chcete soubory stáhnout pomocí **Průzkumník služby Azure Storage**s vybraným souborem, na pásu karet vyberte **Stáhnout** . Otevře se dialogové okno pro výběr souboru, kde můžete zadat umístění a název staženého souboru. Výběrem **Uložit** zahájíte stahování souboru do místního umístění.

## <a name="managing-access"></a>Správa přístupu

Oprávnění můžete nastavit v kořenu vašeho kontejneru. Abyste to mohli udělat, musíte být přihlášeni k Průzkumník služby Azure Storage s vaším individuálním účtem s právy k tomu, aby to bylo možné (na rozdíl od připojovacího řetězce). Klikněte pravým tlačítkem na svůj kontejner a vyberte **Spravovat oprávnění**a v dialogovém okně **Spravovat oprávnění** .

![Průzkumník služby Microsoft Azure Storage – Správa přístupu k adresáři](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

Dialog **Spravovat oprávnění** umožňuje spravovat oprávnění pro vlastníka a skupinu vlastníci. Umožňuje také přidat nové uživatele a skupiny do seznamu řízení přístupu, pro který pak můžete spravovat oprávnění.

Chcete-li přidat nového uživatele nebo skupinu do seznamu řízení přístupu, vyberte pole **Přidat uživatele nebo skupinu** .

Zadejte odpovídající položku Azure Active Directory (AAD), kterou chcete přidat do seznamu, a pak vyberte **Přidat**.

Uživatel nebo skupina se nyní zobrazí v poli **Uživatelé a skupiny:** , což vám umožní začít spravovat jejich oprávnění.

> [!NOTE]
> Osvědčeným postupem je a doporučujeme vytvořit v AAD skupinu zabezpečení a zachovat oprávnění pro skupinu, nikoli jednotlivé uživatele. Podrobnosti o tomto doporučení a další osvědčené postupy najdete v tématu [osvědčené postupy pro data Lake Storage Gen2](data-lake-storage-best-practices.md).

Existují dvě kategorie oprávnění, které můžete přiřadit: přístup k ACL a výchozí seznamy ACL.

* **Přístup**: přístup k ovládacímu prvku ACL přístup k objektu. Přístupové seznamy ACL mají přístup k souborům a adresářům.

* **Výchozí**: Šablona seznamů ACL přidružených k adresáři, které určují přístupové seznamy ACL pro všechny podřízené položky, které jsou vytvořeny v tomto adresáři. Soubory nemají výchozí seznamy ACL.

V obou těchto kategoriích máte tři oprávnění, která pak můžete přiřadit k souborům nebo adresářům: **čtení**, **zápis**a **spouštění**.

>[!NOTE]
> Výběrem těchto možností se nenastaví oprávnění k žádným aktuálně existujícím položkám v adresáři. Je nutné přejít na každou jednotlivou položku a nastavit oprávnění ručně, pokud soubor již existuje.

Můžete spravovat oprávnění pro jednotlivé adresáře a také jednotlivé soubory, což vám umožní jemně odstupňované řízení přístupu. Proces pro správu oprávnění pro adresáře i soubory je stejný, jak je popsáno výše. Klikněte pravým tlačítkem na soubor nebo adresář, u kterého chcete spravovat oprávnění, a postupujte podle stejného procesu.

## <a name="next-steps"></a>Další kroky

Přečtěte si seznam řízení přístupu v Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Řízení přístupu ve službě Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
