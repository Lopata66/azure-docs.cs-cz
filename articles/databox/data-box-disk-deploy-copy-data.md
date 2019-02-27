---
title: Kopírování dat na Microsoft Azure Data Box Disk | Microsoft Docs
description: V tomto výukovém kurzu se naučíte, jak zkopírovat data na Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 01/09/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 75a78e303991e5426c97b8ceb0eb1375e03be2a2
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56868183"
---
# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Kurz: Kopírování dat na disku Azure Data Box a ověřit

Tento kurz popisuje, jak zkopírovat data z hostitelského počítače a potom vygenerovat kontrolní součty k ověření jejich integrity.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kopírování dat na Data Box Disk
> * Ověření dat

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:
- Dokončili jste [kurzu: Instalace a konfigurace vašeho disku Azure Data Box](data-box-disk-deploy-set-up.md).
- Vaše disky jsou odemknuté a připojené ke klientskému počítači.
- Klientský počítač, který se používá ke kopírování dat na disky, musí používat [podporovaný operační systém](data-box-disk-system-requirements.md##supported-operating-systems-for-clients).
- Ujistěte se, že zamýšlený typ úložiště pro vaše data odpovídá [podporovaným typům úložiště](data-box-disk-system-requirements.md#supported-storage-types).


## <a name="copy-data-to-disks"></a>Kopírování dat na disky

Pokud chcete připojit počítač k Data Box Disku a zkopírovat z něj data, proveďte následující kroky.

1. Zobrazte obsah odemknuté jednotky.

    ![Zobrazení obsahu jednotky](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Zkopírujte data, která chcete importovat, jako objekty blob bloku do složky BlockBlob. Data jako VHD/VHDX podobně zkopírujte do složky PageBlob. 

    V účtu služby Azure Storage se pro každou podsložku ve složkách BlockBlob a PageBlob vytvoří zvláštní kontejner. Všechny soubory ve složkách BlockBlob a PageBlob se zkopírují do výchozího kontejneru `$root` v účtu Azure Storage. Všechny soubory v kontejneru `$root` se vždycky nahrají jako objekty blob bloku.

    Pokud jsou v kořenovém adresáři nějaké soubory a složky, před zahájením kopírování dat je potřeba je přesunout do jiné složky.

    Při volbě názvů kontejnerů a objektů blob se řiďte požadavky na názvy v Azure.

    #### <a name="azure-naming-conventions-for-container-and-blob-names"></a>Zásady vytváření názvů kontejnerů a objektů blob v Azure
    |Entita   |Zásady  |
    |---------|---------|
    |Názvy kontejnerů pro objekty blob bloku a objekty blob stránky     |Musí začínat písmenem nebo číslicí a smí obsahovat jenom písmena, číslice a pomlčky (-). Každé pomlčce (-) musí bezprostředně předcházet číslice (0–9) nebo malé písmeno (a–z) a také po ní musí následovat. Názvy nesmí obsahovat po sobě jdoucí pomlčky. <br>Musí se jednat o platný název DNS o délce 3 až 63 znaků.          |
    |Názvy objektů blob bloku a objektů blob stránky    |Názvy objektů blob rozlišují velká a malá písmena a smí obsahovat libovolnou kombinaci znaků. <br>Název objektu blob musí mít délku 1 až 1024 znaků.<br>Vyhrazené znaky v adresách URL musí být správně uzavřené do uvozovek.<br>Počet segmentů cesty, ze kterých se název objektu blob skládá, nesmí překročit 254. Segment cesty je řetězec mezi po sobě jdoucími znaky oddělovače (třeba lomítko „/“), který odpovídá názvu virtuálního adresáře.         |

    > [!IMPORTANT] 
    > Všechny kontejnery a objekty blob by měly být v souladu se [zásadami vytváření názvů v Azure](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions). Při nedodržení těchto pravidel se odesílání dat do Azure nezdaří.

3. Při kopírování souborů se ujistěte, že velikost souborů nepřekračuje ~4,7 TiB v případě objektů blob bloku a 8 TiB v případě objektů blob stránky. 
4. Ke kopírování dat můžete použít funkci přetahování v Průzkumníku souborů. Ke kopírování dat můžete taky použít jakýkoli nástroj pro kopírování souborů kompatibilní s protokolem SMB, třeba Robocopy. Pomocí následujícího příkazu Robocopy se dá zahájit několik úloh kopírování najednou:

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    Následující tabulka obsahuje parametry a možnosti příkazu:
    
    |Parametry/možnosti  |Popis |
    |--------------------|------------|
    |Zdroj            | Určuje cestu ke zdrojovému adresáři.        |
    |Cíl       | Určuje cestu k cílovému adresáři.        |
    |/E                  | Zkopíruje podadresáře včetně prázdných adresářů. |
    |/MT[:N]             | Vytvoří vícevláknové kopie s N vlákny, kde N je celé číslo mezi 1 a 128. <br>Výchozí hodnota N je 8.        |
    |/R: <N>             | Určuje počet opakovaných pokusů při neúspěšném kopírování. Výchozí hodnota N je 1 000 000 (jeden milion opakování).        |
    |/W: <N>             | Určuje dobu čekání mezi opakovanými pokusy v sekundách. Výchozí hodnota N je 30 (čeká se 30 sekund).        |
    |/NFL                | Určuje, že se nemají protokolovat názvy souborů.        |
    |/NDL                | Určuje, že se nemají protokolovat názvy adresářů.        |
    |/FFT                | Přebírá časy systému souborů FAT (s přesností na 2 sekundy).        |
    |/Log:<Log File>     | Zapíše výstup stavu do souboru protokolu (přepíše existující soubor protokolu).         |

    Je možné použít více disků současně a na každém disku může běžet několik úloh. 

6. Během provádění úlohy můžete zkontrolovat stav kopírování. Následující příklad ukazuje výstup příkazu robocopy, který slouží ke kopírování souborů na Data Box Disk.

    ```
    C:\Users>robocopy
        -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:53 PM
           Simple Usage :: ROBOCOPY source destination /MIR
    
                 source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                   /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                   Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
       Files :        17        17         0         0         0         0
       Bytes :     3.9 k     3.9 k         0         0         0         0
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    Pokud chcete optimalizovat výkon, použijte při kopírování dat následující parametry robocopy.

    |    Platforma    |    Vetšinou malé soubory (< 512 kB)                           |    Většinou středně velké soubory (512 kB až 1 MB)                      |    Většinou velké soubory (> 1 MB)                             |   
    |----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
    |    Data Box Disk        |    4 Robocopy relace * <br> 16 vláken na relaci    |    2 Robocopy relace * <br> 16 vláken na relaci    |    2 Robocopy relace * <br> 16 vláken na relaci    |  |
    
    **Každou relaci nástroje Robocopy můžou mít maximálně 7 000 adresářů a souborů milionů 150.*
    
    >[!NOTE]
    > Parametry navrhované výše jsou založené na prostředí použité při inhouse testování.
    
    Další informace o příkazu Robocopy najdete v článku [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy a několik příkladů).

6. Otevřete cílovou složku, zobrazte zkopírované soubory a ověřte je. Pokud během procesu kopírování došlo k nějakým chybám, stáhněte si soubory protokolu, abyste mohli vyřešit případné potíže. Soubory protokolu se nachází uvedená v příkazu robocopy.
 
> [!IMPORTANT]
> - Je vaší povinností ujistit se, že jste data zkopírovali do složek odpovídajících příslušnému formátu dat. Data objektů blob bloku je například potřeba zkopírovat do složky určené pro objekty blob bloku. Pokud formát dat neodpovídá příslušné složce (typu úložiště), pozdější nahrávání dat do Azure se nezdaří.
> -  Při kopírování dat se ujistěte, že velikost dat odpovídá omezením velikosti popsaným v článku [Omezení Azure Storage a Data Box Disku](data-box-disk-limits.md).
> - Pokud data nahrávaná Data Box Diskem zároveň nahrávají jiné aplikace mimo Data Box Disk, může to způsobit selhání úlohy nahrávání a poškození dat.

### <a name="split-and-copy-data-to-disks"></a>Rozdělení a kopírování dat na disky

Tento volitelný postup můžete použít v případě, že používáte více disků a máte velkou datovou sadu, kterou je potřeba rozdělit a zkopírovat mezi všechny disky. Nástroj Data Box Split Copy pomáhá rozdělit a kopírovat data na počítači s Windows.

>[!IMPORTANT]
> Nástroj pro kopírování rozdělit pole data také ověří vaše data. Pokud použijete nástroj pro kopírování dat pole rozdělení pro kopírování dat, můžete přeskočit [krok ověření](#validate-data).

1. Ujistěte se, že na počítači s Windows máte stažený nástroj Data Box Split Copy, který je extrahovaný v místní složce. Tento nástroj se stáhnul při stahování sady nástrojů Data Box Disk pro Windows.
2. Otevřete Průzkumníka souborů. Poznamenejte si písmeno jednotky zdroje dat a písmeno jednotky přiřazené k Data Box Disku. 

     ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. Identifikujte zdrojová data, která se mají zkopírovat. Například v tomto případě:
    - Identifikovala se následující data objektu blob bloku.

         ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - Identifikovala se následující data objektu blob stránky.

         ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. Přejděte do složky s extrahovaným softwarem. Vyhledejte `SampleConfig.json` soubor v této složce. Jedná se o soubor jen pro čtení, který můžete upravit a uložit.

   ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. Upravit `SampleConfig.json` souboru.
 
    - Zadejte název úlohy. Tím se na Data Box Disku vytvoří složka, ze které se nakonec stane kontejner v účtu úložiště Azure přidruženém k těmto diskům. Název úlohy musí být v souladu se zásadami vytváření názvů kontejnerů v Azure. 
    - Zadejte cestu ke zdroji a poznamenejte si formát cesty v `SampleConfigFile.json`. 
    - Zadejte písmena jednotek odpovídající cílovým diskům. Data se načtou ze zdrojové cesty a zkopírují se mezi několik disků.
    - Zadejte cestu pro soubory protokolů. Ve výchozím nastavení, je odeslána do aktuálního adresáře kde `.exe` nachází.

     ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. Pokud chcete ověřovat formát souboru, přejděte na `JSONlint`. Uložte soubor jako `ConfigFile.json`. 

     ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. Otevřete okno příkazového řádku. 

8. Spustit `DataBoxDiskSplitCopy.exe`. Type

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. Stiskněte Enter a pokračujte v provádění skriptu.

    ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. Jakmile se datová sada rozdělí a zkopíruje, zobrazí se souhrn nástroje Split Copy pro relaci kopírování. Ukázkový výstup najdete níž.

    ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. Ověřte, že se data rozdělila mezi cílové disky. 
 
    ![Kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    Je-li zkontrolovat obsah `n:` jednotka další, uvidíte, že se vytvoří dvě podsložky odpovídající objekt blob bloku a objektů blob stránky formát data.
    
     ![Rozdělení kopírování dat](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. Pokud operace kopírování selže, použijte k obnovení a pokračování následující příkaz:

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`

Jakmile se kopírování dat dokončí, můžete přejít k ověření vaše data. Pokud jste použili nástroj pro kopírování rozdělení, přeskočit ověření (nástroj také ověří rozdělení kopie) a přejděte k dalšímu kurzu.


## <a name="validate-data"></a>Ověření dat

Pokud nástroj pro kopírování rozdělení není použili ke kopírování dat, je potřeba ověřit vaše data. Data ověříte následujícím postupem.

1. Spusťte soubor `DataBoxDiskValidation.cmd` pro ověření kontrolního součtu ve složce *DataBoxDiskImport* na jednotce.
    
    ![Výstup ověřovacího nástroje Data Box Disku](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. Zvolte příslušnou možnost. **Doporučujeme vždy ověřit soubory a vygenerovat kontrolní součty výběrem možnosti 2**. V závislosti na velikosti dat může tento krok nějakou dobu trvat. Po dokončení skriptu ukončete příkazové okno. Pokud během ověřování a generování kontrolního součtu došlo k chybě, budete na to upozorněni a obdržíte odkaz na protokoly chyb.

    ![Výstup kontrolního součtu](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - Resetujte nástroj mezi dvěma spuštěními.
    > - Možnost 1 použijte, pokud pracujete s rozsáhlou sadou dat obsahující malých souborů (~ znalostní báze). Tuto možnost pouze ověří soubory, protože kontrolní součet generování může trvat velmi dlouho a výkon může být velmi pomalé.

3. Pokud používáte víc disků, spusťte příkaz pro každý disk.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených s Azure Data Box Diskem, například jste se naučili:

> [!div class="checklist"]
> * Kopírování dat na Data Box Disk
> * Ověření integrity dat

V dalším kurzu se dozvíte, jak poslat Data Box Disk zpátky a ověřit nahrání dat do Azure.

> [!div class="nextstepaction"]
> [Odeslání Azure Data Boxu zpátky Microsoftu](./data-box-disk-deploy-picked-up.md)