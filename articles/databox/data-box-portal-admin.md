---
title: Správa Azure Data Box, Azure Data Box Heavy přes Azure Portal | Microsoft Docs
description: Popisuje, jak použít Azure Portal ke správě Azure Data Box a Azure Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 08/07/2019
ms.author: alkohli
ms.openlocfilehash: 22fd67797bbec516317aadaa4b33371c5d335b36
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84609450"
---
# <a name="use-the-azure-portal-to-administer-your-azure-data-box-and-azure-data-box-heavy"></a>Pomocí Azure Portal můžete spravovat Azure Data Box a Azure Data Box Heavy

Tento článek se týká Azure Data Box i Azure Data Box Heavy. Tento článek popisuje některé komplexní pracovní postupy a úlohy správy, které je možné provádět na Azure Data Boxm zařízení. Zařízení Data Box můžete spravovat přes Azure Portal nebo prostřednictvím místního webového uživatelského rozhraní.

Tento článek se zaměřuje na úlohy, které můžete provést pomocí webu Azure Portal. Pomocí Azure Portal můžete spravovat objednávky, spravovat Data Box zařízení a sledovat stav objednávky v průběhu jejich dokončení.


## <a name="cancel-an-order"></a>Zrušení objednávky

Může se stát, že po zadání objednávky budete muset tuto objednávku z různých důvodů zrušit. Objednávku je možné zrušit jen do doby, než je zpracována. Po zpracování objednávky a přípravné Data Box zařízení není možné objednávku zrušit.

Když chcete zrušit objednávku, postupujte následovně.

1.  Přejděte na **Přehled > Zrušit**.

    ![Zrušení objednávky 1](media/data-box-portal-admin/cancel-order1.png)

2.  Zadejte důvod zrušení objednávky.  

    ![Zrušení objednávky 2](media/data-box-portal-admin/cancel-order2.png)

3.  Po zrušení objednávky se na portálu aktualizuje její stav na **Zrušeno**.

## <a name="clone-an-order"></a>Klonování objednávky

V některých situacích může být užitečné klonování. Uživatel například používá Data Box k přenosu dat. Když se generují víc dat, potřebuje další Data Box zařízení pro přenos těchto dat do Azure. V takovém případě se dá stejná objednávka jednoduše naklonovat.

Pokud chcete naklonovat objednávku, proveďte následující kroky.

1.  Přejděte na **Přehled > Klonovat**. 

    ![Klonování objednávky 1](media/data-box-portal-admin/clone-order1.png)

2.  Všechny podrobnosti o objednávce zůstávají stejné. Název objednávky má podobu názvu původní objednávky s příponou *-Clone*. Zaškrtnutím políčka potvrďte, že jste si přečetli informace o ochraně osobních údajů. Klikněte na **Vytvořit**.

Během pár minut se vytvoří klon, portál se aktualizuje a zobrazí se na něm nová objednávka.


## <a name="delete-order"></a>Odstranění objednávky

Po dokončení objednávky můžete chtít objednávku odstranit. Objednávka obsahuje vaše osobní údaje jako jméno, adresu a kontaktní údaje. Při odstranění objednávky se tyto osobní údaje odstraní.

Odstranit se dají jenom dokončené nebo zrušené objednávky. Pokud chcete odstranit objednávku, proveďte následující kroky.

1. Přejděte na **Všechny prostředky**. Vyhledejte svoji objednávku.

2. Klikněte na objednávku, kterou chcete odstranit, a přejděte na **Přehled**. Na panelu příkazů klikněte na **Odstranit**.

    ![Odstranění první objednávky Data Boxu](media/data-box-portal-admin/delete-order1.png)

3. Po zobrazení výzvy k potvrzení odstranění objednávky zadejte název objednávky. Klikněte na **Odstranit**.

## <a name="download-shipping-label"></a>Stažení expedičního štítku

Pokud displej E-ink vašeho Data Boxu nefunguje a nezobrazuje zpětný expediční štítek, budete možná muset expediční štítek stáhnout. Na Data Box Heavy se nezobrazuje žádný displej s tiskem, takže tento pracovní postup neplatí pro Data Box Heavy.

Pokud chcete stáhnout expediční štítek, proveďte následující kroky.

1.  Přejděte na **Přehled > Stáhnout expediční štítek**. Tato možnost je dostupná teprve po odeslání zařízení. 

    ![Stažení expedičního štítku](media/data-box-portal-admin/download-shipping-label.png)

2.  Tato akce stáhne následující zpětný expediční štítek. Uložte popisek a vytiskněte ho. Skládání a vložení popisku do prostého rukávu na zařízení. Ujistěte se, že je štítek vidět. Odstraňte všechny nálepky, které jsou na zařízení z předchozí expedice.

    ![Ukázkový expediční štítek](media/data-box-portal-admin/example-shipping-label.png)

## <a name="edit-shipping-address"></a>Úprava dodací adresy

Po zadání objednávky může být potřeba upravit dodací adresu. Tato možnost je dostupná jenom do odeslání zařízení. Po odeslání zařízení už tato možnost dostupná není.

Pokud chcete upravit objednávku, proveďte následující kroky.

1. Přejděte na **Podrobnosti objednávky > Upravit dodací adresu**.

    ![Úprava dodací adresy 1](media/data-box-portal-admin/edit-shipping-address1.png)

2. Upravte a zkontrolujte dodací adresu a pak uložte změny.

    ![Úprava dodací adresy 2](media/data-box-portal-admin/edit-shipping-address2.png)

## <a name="edit-notification-details"></a>Úprava podrobností o oznámení

Může být potřeba změnit uživatele, kteří mají dostávat e-maily se stavem objednávky. Některý uživatel například může potřebovat informaci o doručení nebo vyzvednutí zařízení. Po dokončení kopírování dat může být potřeba informovat jiného uživatele, aby před jeho odstraněním ze zdroje mohl ověřit data v účtu služby Azure Storage. V takových případech můžete upravit podrobnosti o oznámení.

Pokud chcete upravit podrobnosti o oznámení, proveďte následující kroky.

1. Přejděte na **Podrobnosti objednávky > Upravit podrobnosti o oznámení**.

    ![Úprava podrobností o oznámení 1](media/data-box-portal-admin/edit-notification-details1.png)

2. Teď můžete upravit podrobnosti o oznámení a uložit změny.
 
    ![Úprava podrobností o oznámení 2](media/data-box-portal-admin/edit-notification-details2.png)


## <a name="download-order-history"></a>Stažení historie objednávky

Po dokončení objednávky Data Boxu se vymažou data na discích zařízení. Po vyčištění zařízení si na webu Azure Portal můžete stáhnout historii objednávky.

Pokud si chcete stáhnout historii objednávky, proveďte následující kroky.

1. Ve vaší objednávce Data Boxu přejděte do části **Přehled**. Ujistěte se, že je objednávka dokončená. Pokud je objednávka dokončená a zařízení vyčištěné, přejděte do části **Podrobnosti objednávky**. Máte k dispozici možnost **Stáhnout historii objednávky**.

    ![Stažení historie objednávky](media/data-box-portal-admin/download-order-history-1.png)

2. Klikněte na **Stáhnout historii objednávky**. Ve stažené historii uvidíte záznam protokolů dopravců o sledování zásilky. Budou existovat dvě sady protokolů odpovídající dvěma uzlům na Data Box Heavy. Pokud se posunete do dolní části tohoto protokolu, zobrazí se následující odkazy:
    
   - **Copy logs** (Protokoly kopírování) – obsahují seznam souborů, u kterých během kopírování dat z Data Boxu do vašeho účtu úložiště Azure došlo k chybě.
   - **Protokoly auditu** – obsahují informace o tom, jak zapnout a přistupovat ke sdíleným složkám na data box, pokud se nachází mimo datové centrum Azure.
   - **BOM files** (Soubory BOM) – obsahují seznam souborů (označovaný také jako soubor manifestu), které si můžete stáhnout během **přípravy k odeslání**, a zahrnují názvy, velikosti a kontrolní součty souborů.

       ```
       -------------------------------
       Microsoft Data Box Order Report
       -------------------------------
       Name                                               : DataBoxTestOrder                              
       StartTime(UTC)                                     : 10/31/2018 8:49:23 AM +00:00                       
       DeviceType                                         : DataBox                                           
       -------------------
       Data Box Activities
       -------------------
       Time(UTC)                 | Activity                       | Status          | Description  
       
       10/31/2018 8:49:26 AM     | OrderCreated                   | Completed       |                                                   
       11/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |                                                   
       11/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 11/3/2018 1:36:43        PM at AMSTERDAM-NLD                                                                                
       11/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 11/4/2018        8:23:30 PM at AMSTERDAM-NLD                                                                        
       11/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time :          11/4/2018 11:43:34 PM at AMSTERDAM-NLD                                                               
       11/5/2018 1:38:20 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LEIPZIG-DEU. Local Time :        11/5/2018 1:38:20 AM at LEIPZIG-DEU                                                                
       11/5/2018 2:31:07 AM      | ShippingToCustomer             | InProgress      | Processed at LEIPZIG-DEU. Local Time : 11/5/2018          2:31:07 AM at LEIPZIG-DEU                                                                            
       11/5/2018 4:05:58 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LEIPZIG-DEU. Local Time :            11/5/2018 4:05:58 AM at LEIPZIG-DEU                                                                    
       11/5/2018 4:35:43 AM      | ShippingToCustomer             | InProgress      | Transferred through LUTON-GBR. Local Time :              11/5/2018 4:35:43 AM at LUTON-GBR                                                                         
       11/5/2018 4:52:15 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LUTON-GBR. Local Time :              11/5/2018 4:52:15 AM at LUTON-GBR                                                                        
       11/5/2018 5:47:58 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 10/5/2018 5:47:58 AM at LONDON-HEATHROW-GBR                                                
       11/5/2018 6:27:37 AM      | ShippingToCustomer             | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            11/5/2018 6:27:37 AM at LONDON-HEATHROW-GBR                                                            
       11/5/2018 6:39:40 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 11/5/2018 6:39:40 AM at LONDON-HEATHROW-GBR                                                    
       11/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in LAMBETH-GBR. Local        Time : 11/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
       11/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 11/5/2018            9:13:24 AM at LAMBETH-GBR                                                                               
       11/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 11/5/2018        12:03:04 PM at LAMBETH-GBR                                                                          
       1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25        PM at LAMBETH-GBR                                                                                       
       1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at LAMBETH-GBR. Local Time : 1/25/2019          8:03:55 PM at LAMBETH-GBR                                                                            
       1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in LAMBETH-GBR. Local Time :            1/25/2019 8:04:58 PM at LAMBETH-GBR                                                                    
       1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
       1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
       1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR                                                   
       1/26/2019 2:17:10 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility BRUSSELS-BEL. Local Time        : 1/26/2019 2:17:10 PM at BRUSSELS-BEL                                                              
       1/26/2019 2:31:57 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        2:31:57 PM at BRUSSELS-BEL                                                                          
       1/26/2019 3:37:53 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        3:37:53 PM at BRUSSELS-BEL                                                                          
       1/27/2019 11:01:45 AM     | ShippingToDataCenter           | InProgress      | Departed Facility in BRUSSELS-BEL. Local Time :          1/27/2019 11:01:45 AM at BRUSSELS-BEL                                                                 
       1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD.            Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
       1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019            9:07:57 AM at AMSTERDAM-NLD                                                                             
       1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019            1:35:56 PM at AMSTERDAM-NLD                                                                            
       1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019        2:57:48 PM at AMSTERDAM-NLD                                                                         
       1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |                                              
       1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully                  
       1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.                               
       1/29/2019 4:55:00 PM      | DataCopy                       | Started         |                                                 
       2/2/2019 7:07:34 PM       | DataCopy                       | Completed       | Copy Completed.                                   
       2/4/2019 7:47:32 PM       | SecureErase                    | Started         |                                                  
       2/4/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:DEVICESERIALNO has been sanitized          according to NIST 800-88 Rev 1.                                                                       

       ------------------
       Data Box Log Links
       ------------------

       Account Name         : Gus                                                       
       Copy Logs Path       : databoxcopylog/DataBoxTestOrder_CHC533180024_CopyLog_73a81b2d613547a28ecb7b1612fe93ca.xml
       Audit Logs Path      : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024
       BOM Files Path       : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024      
       ```
     Potom můžete přejít ke svému účtu úložiště a zobrazit protokoly kopírování.

![Protokoly v účtech úložiště](media/data-box-portal-admin/logs-in-storage-acct-2.png)

Můžete také zobrazit řetězec protokolů opatrovnictví, mezi které patří protokoly auditu a soubory BOM.

![Protokoly v účtech úložiště](media/data-box-portal-admin/logs-in-storage-acct-1.png)

## <a name="view-order-status"></a>Zobrazení stavu objednávky

Když se stav zařízení na portálu změní, dostanete upozornění prostřednictvím e-mailu.

|Stav objednávky |Popis |
|---------|---------|
|Objednáno     | Došlo k úspěšnému zadání objednávky. <br>Pokud je zařízení dostupné, určí Microsoft zařízení k odeslání a toto zařízení připraví. <br> Pokud zařízení není dostupné okamžitě, bude objednávka zpracována, až bude zařízení dostupné. Zpracování objednávky může trvat od několika dnů až do dvou měsíců. Pokud objednávku nelze vyřídit do 90 dnů, je objednávka zrušena a budete upozorněni.         |
|Zpracováno     | Proběhlo zpracování objednávky. Zařízení je připravené k odeslání v datovém centru podle objednávky.         |
|Odesláno     | Došlo k odeslání objednávky. Zásilku můžete sledovat pomocí ID pro sledování, které je uvedené ve vaší objednávce na portálu.        |
|Doručeno     | Zásilka byla doručena na adresu uvedenou v objednávce.        |
|Vyzvednuto     |Zpáteční zásilka byla vyzvednuta a naskenována dopravcem.         |
|Přijato     | Zařízení bylo přijato a naskenováno v datovém centru Azure. <br> Po kontrole zásilky začne nahrávání dat ze zařízení.      |
|Kopírování dat     | Probíhá kopírování dat. Sledujte průběh kopírování u vaší objednávky na portálu Azure Portal. <br> Počkejte, dokud se kopírování dat nedokončí. |
|Dokončeno       |Objednávka se úspěšně dokončila.<br> Než odstraníte data z místních serverů, zkontrolujte, jestli se data objevila v Azure.         |
|Dokončeno s chybami| Kopírování dat se dokončilo, ale během kopírování došlo k chybám. <br> Zkontrolujte protokoly kopírování pomocí cesty uvedené na portálu Azure Portal. Podívejte [se na příklady kopírování protokolů po dokončení nahrávání s chybami](https://docs.microsoft.com/azure/databox/data-box-logs#upload-completed-with-errors).   |
|Dokončeno s upozorněními| Kopírování dat se dokončilo, ale data se změnila. Data obsahovala nekritické chyby objektů BLOB nebo souborů, které byly opraveny změnou názvu souboru nebo objektu BLOB. <br> Zkontrolujte protokoly kopírování pomocí cesty uvedené na portálu Azure Portal. Poznamenejte si úpravy vašich dat. Podívejte [se na příklady kopírování protokolů po dokončení nahrávání s upozorněními](https://docs.microsoft.com/azure/databox/data-box-logs#upload-completed-with-warnings).   |
|Zrušeno            |Došlo ke zrušení objednávky. <br> Buď jste objednávku zrušili, nebo došlo k chybě a objednávku zrušila služba. Pokud objednávku nelze vyřídit do 90 dnů, je objednávka také zrušena a budete upozorněni.     |
|Vyčištění | Vymažou se data na discích zařízení. Vyčištění zařízení se považuje za dokončené, jakmile je na webu Azure Portal k dispozici ke stažení historie objednávky.|



## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [řešit problémy s data box a data box Heavy](data-box-troubleshoot.md).
