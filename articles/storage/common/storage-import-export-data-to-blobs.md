---
title: Použití Azure import/export k přenosu dat do objektů blob Azure | Microsoft Docs
description: Naučte se vytvářet úlohy importu a exportu v Azure Portal pro přenos dat do a z objektů blob Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/06/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 723cd78b1c7325300513664b64f7ca77ac71bcdd
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978547"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Použití služby Azure import/export k importu dat do Azure Blob Storage

Tento článek poskytuje podrobné pokyny, jak pomocí služby importu a exportu v Azure bezpečně importovat velké objemy dat do úložiště objektů BLOB v Azure. Aby bylo možné importovat data do objektů blob Azure, služba vyžaduje, abyste dodali šifrované diskové jednotky obsahující vaše data do datacentra Azure.  

## <a name="prerequisites"></a>Požadavky

Než vytvoříte úlohu importu pro přenos dat do Azure Blob Storage, pečlivě zkontrolujte a dokončete následující seznam požadavků pro tuto službu.
Musíte:

- Mít aktivní předplatné Azure, které se dá použít pro službu import/export.
- Musí mít aspoň jeden Azure Storage účet s kontejnerem úložiště. Podívejte se na seznam [podporovaných účtů úložiště a typů úložiště pro službu import/export](storage-import-export-requirements.md).
    - Informace o vytvoření nového účtu úložiště najdete v tématu [způsob vytvoření účtu úložiště](storage-account-create.md). 
    - Informace o kontejneru úložiště najdete v pro [vytvoření kontejneru úložiště](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
- Mít dostatečný počet disků [podporovaných typů](storage-import-export-requirements.md#supported-disks).
- Má systém Windows s [podporovanou verzí operačního systému](storage-import-export-requirements.md#supported-operating-systems).
- Povolte nástroj BitLocker v systému Windows. Přečtěte si téma [Jak povolit nástroj BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- [Stáhněte si WAImportExport verze 1](https://www.microsoft.com/download/details.aspx?id=42659) v systému Windows. Rozbalte do výchozí složky `waimportexportv1`. Například, `C:\WaImportExportV1`.
- Mít účet FedEx/DHL. Pokud chcete použít nosný operátor jiný než FedEx/DHL, obraťte se na Azure Data Box provozní tým na `adbops@microsoft.com`.  
    - Účet musí být platný, měl by mít zůstatek a musí mít možnosti vrácení expedice.
    - Vygenerujte sledovací číslo pro úlohu exportu.
    - Každá úloha by měla mít samostatné sledovací číslo. Více úloh se stejným číslem sledování se nepodporuje.
    - Pokud nemáte účet dopravce, přečtěte si:
        - [Vytvořte účet FedEx](https://www.fedex.com/en-us/create-account.html)nebo
        - [Vytvořte účet DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Krok 1: Příprava jednotek

Tento krok generuje soubor deníku. Soubor deníku ukládá základní informace, jako jsou sériové číslo jednotky, šifrovací klíč a podrobnosti účtu úložiště.

K přípravě jednotek proveďte následující kroky.

1.  Připojte diskové jednotky k systému Windows přes konektory SATA.
1.  Na každé jednotce vytvořte jeden svazek NTFS. Přiřaďte ke svazku písmeno jednotky. Nepoužívejte mountpoints.
2.  Povolte šifrování BitLockeru na svazku NTFS. Pokud používáte systém Windows Server, postupujte podle pokynů v tématu [Postup povolení nástroje BitLocker v systému Windows server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
3.  Kopírovat data do šifrovaného svazku. Použijte přetažení nebo příkaz Robocopy nebo jakýkoli takový nástroj pro kopírování.
4.  Otevřete okno PowerShellu nebo příkazového řádku s oprávněními správce. Chcete-li změnit adresář na složku unzip, spusťte následující příkaz:

    `cd C:\WaImportExportV1`
5.  Klíč BitLockeru jednotky získáte spuštěním následujícího příkazu:

    `manage-bde -protectors -get <DriveLetter>:`
6.  Pokud chcete disk připravit, spusťte následující příkaz. **V závislosti na velikosti dat to může trvat několik hodin až dnů.**

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```
    Soubor deníku se vytvoří ve stejné složce, ve které jste spustili nástroj. Vytvoří se také další dva soubory – soubor *. XML* (složka, ve které jste spustili nástroj), a soubor *Drive-manifest. XML* (složka, ve které jsou data uložena).

    Použité parametry jsou popsány v následující tabulce:

    |Možnost  |Popis  |
    |---------|---------|
    |/j:     |Název souboru deníku s příponou. jrn. Vygeneruje se soubor deníku na jednotku. Doporučujeme použít sériové číslo disku jako název souboru deníku.         |
    |/ID     |ID relace Pro každou instanci příkazu použijte jedinečné číslo relace.      |
    |/t:     |Písmeno jednotky disku, který má být dodán. Například jednotku `D`.         |
    |/bk:     |Klíč BitLockeru pro jednotku. Jeho číselné heslo z výstupu `manage-bde -protectors -get D:`      |
    |/srcdir:     |Písmeno jednotky disku, který se má odeslat, a `:\`. Například, `D:\`.         |
    |/dstdir:     |Název cílového kontejneru v Azure Storage.         |
    |/blobtype:     |Tato možnost určuje typ objektů blob, do kterých chcete importovat data. Pro objekty blob bloku je to `BlockBlob` a pro objekty blob stránky je `PageBlob`.         |
    |/skipwrite:     |Možnost, která určuje, že se nevyžadují žádná nová data ke zkopírování a stávající data na disku se připravují.          |
    |/enablecontentmd5:     |Možnost, pokud je povolená, zajistí, že se algoritmus MD5 vypočítá a nastaví jako `Content-md5` vlastnost u každého objektu BLOB. Tuto možnost použijte pouze v případě, že po nahrání dat do Azure chcete použít pole `Content-md5`. <br> Tato možnost nemá vliv na kontrolu integrity dat (ke které dochází ve výchozím nastavení). Nastavení zvyšuje čas potřebný k nahrání dat do cloudu.          |
7. Opakujte předchozí krok pro každý disk, který je třeba odeslat. Soubor deníku se zadaným názvem se vytvoří pro každé spuštění příkazového řádku.

    > [!IMPORTANT]
    > - Spolu se souborem deníku je soubor `<Journal file name>_DriveInfo_<Drive serial ID>.xml` také vytvořen ve stejné složce, ve které se nástroj nachází. Soubor. XML se používá místo souboru deníku při vytváření úlohy, pokud je soubor deníku příliš velký.

## <a name="step-2-create-an-import-job"></a>Krok 2: vytvoření úlohy importu

Provedením následujících kroků vytvořte v Azure Portal úlohu importu.

1. Přihlaste se k https://portal.azure.com/.
2. **> Úlohy import/export přejít na všechny služby > úložiště**.

    ![Přejít na úlohy importu/exportu](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klikněte na **vytvořit úlohu importu/exportu**.

    ![Klikněte na vytvořit úlohu importu/exportu.](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. **Základní informace**:

   - Vyberte **importovat do Azure**.
   - Zadejte popisný název úlohy importu. Pomocí názvu můžete sledovat průběh úloh.
       - Název může obsahovat jenom malá písmena, číslice a spojovníky.
       - Název musí začínat písmenem a nesmí obsahovat mezery.
   - Vyberte předplatné.
   - Zadejte nebo vyberte skupinu prostředků.  

     ![Vytvořit úlohu importu – krok 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. V **podrobnostech úlohy**:

    - Nahrajte soubory deníku jednotky, které jste získali během kroku přípravy na jednotku. Pokud byl použit `waimportexport.exe version1`, nahrajte jeden soubor pro každou diskovou jednotku, kterou jste připravili. Pokud velikost souboru deníku přesáhne 2 MB, můžete použít také `<Journal file name>_DriveInfo_<Drive serial ID>.xml` vytvořené se souborem deníku.
    - Vyberte cílový účet úložiště, ve kterém se budou data nacházet.
    - Umístění dropoff se automaticky vyplní podle oblasti vybraného účtu úložiště.

   ![Vytvoření úlohy importu – krok 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. V **informace o expedici zpět**:

   - V rozevíracím seznamu vyberte přepravce. Pokud chcete použít operátor jiného než FedEx/DHL, vyberte z rozevíracího seznamu existující možnost. Kontaktujte Azure Data Box provozní tým na `adbops@microsoft.com` s informacemi týkajícími se nosiče, který plánujete použít.
   - Zadejte platné číslo účtu dopravce, který jste vytvořili pomocí tohoto dopravce. Společnost Microsoft používá tento účet k dodávání jednotek zpátky po dokončení úlohy importu. Pokud nemáte číslo účtu, vytvořte účet dopravce [FedEx](https://www.fedex.com/us/oadr/) nebo [DHL](https://www.dhl.com/) .
   - Zadejte celé a platné kontaktní jméno, telefonní číslo, e-mail, ulici, město, PSČ, kraj a zemi/oblast.

       > [!TIP]
       > Místo zadání e-mailové adresy pro jednoho uživatele zadejte skupinu. Tím zajistíte, že budete dostávat oznámení i v případě, že správce opustí.

     ![Vytvořit úlohu importu – krok 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

5. V **souhrnu**:

   - Projděte si informace o úloze uvedené v souhrnu. Poznamenejte si název úlohy a dodací adresu datacentra Azure k dodávání disků zpátky do Azure. Tyto informace se používají později u expedičního štítku.
   - Kliknutím na tlačítko **OK** vytvořte úlohu importu.

     ![Vytvořit úlohu importu – krok 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives"></a>Krok 3: dodání jednotek

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>Krok 4: aktualizace úlohy pomocí informací o sledování

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Krok 5: ověření nahrávání dat do Azure

Sledujte úlohu k dokončení. Po dokončení úlohy ověřte, že se data nahrála do Azure. Místní data odstraňte až po ověření úspěšného odeslání.

## <a name="next-steps"></a>Další kroky

* [Zobrazení stavu úlohy a jednotky](storage-import-export-view-drive-status.md)
* [Kontrola požadavků na import/export](storage-import-export-requirements.md)
