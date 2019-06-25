---
title: Kurz k objednávky disku Azure Data Box | Dokumentace Microsoftu
description: V tomto kurzu se naučíte zaregistrovat a objednat službu Azure Data Box Disk, abyste mohli importovat data do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 06/17/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 5f8fd9b2ecb58b34476bf8ecca7aa08dfc446040
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273392"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>Kurz: Pořadí disku Azure Data Box

Azure Data Box Disk je řešení hybridního cloudu, které vám umožňuje rychle, snadno a spolehlivě importovat místní data do Azure. Svoje data přesunete na disky SSD (Solid-State Disk), které dostanete od Microsoftu, a tyto disky pak pošlete zpět. Data se potom nahrají do Azure.

Tento kurz popisuje, jak Azure Data Box Disk objednat. V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Objednání služby Data Box Disk
> * Sledování objednávky
> * Zrušení objednávky

## <a name="prerequisites"></a>Požadavky

Před nasazením, zajistěte splnění následujících požadavků konfigurace pro službu Data Box a disku Data Box.

### <a name="for-service"></a>Služba

Než začnete, ujistěte se, že:
- Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.
- Typ předplatného, které používáte pro službu Data Box, musí být jeden z následujících:
    - Smlouva Microsoft Enterprise (EA). Další informace o [předplatných se smlouvou Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Cloud Solution Provider (CSP). Další informace o [programu Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
- Pokud chcete vytvořit objednávku Data Boxu, potřebujete mít u předplatného přístup vlastníka nebo přispěvatele.

### <a name="for-device"></a>Zařízení

Než začnete, ujistěte se, že:
- Budete mít klientský počítač, který je k dispozici, ze kterého můžete zkopírovat data. Klientský počítač musí splňovat tyto požadavky:
    - Musí na něm běžet [podporovaný operační systém](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
    - Musí na něm být nainstalovaný [požadovaný software](data-box-disk-system-requirements.md#other-required-software-for-windows-clients), pokud jde o klienta pro Windows.  

## <a name="order-data-box-disk"></a>Objednat Data Box Disk

Přihlášení k aplikaci:

- Na webu Azure portal na této adrese URL: https://portal.azure.com objednávky disku Data Box.
- Nebo na portálu Azure Government na této adrese URL: https://portal.azure.us. Další podrobnosti najdete v části [připojit se k portálu Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

Objednávku Data Box Disk, proveďte následující kroky.

1. V levém horním rohu portálu klikněte na **+ Vytvořit prostředek** a vyhledejte *Azure Data Box*. Klikněte na **Azure Data Box**.
    
   ![Vyhledání Azure Data Box 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Klikněte na možnost **Vytvořit**.

3. Podívejte se, zda je služba Data Box dostupná ve vaší oblasti. Zadejte nebo vyberte následující informace a pak klikněte na **Použít**.

    ![Možnost výběru služby Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Nastavení|Hodnota|
    |---|---|
    |Předplatné|Vyberte předplatné, pro které je služba Data Box povolena.<br> Předplatné je propojeno s vaším fakturačním účtem. |
    |Typ přenosu| Import do Azure|
    |Zdrojová země | Vyberte zemi nebo oblast, kde se aktuálně nachází vaše data.|
    |Cílová oblast Azure|Vyberte oblast Azure, do které chcete data přenést.|

  
5.  Vyberte **Data Box Disk**. Maximální kapacita řešení u jedné objednávky 5 disků je 35 TB. V případě větších objemů dat můžete vytvořit více objednávek.

     ![Možnost výběru služby Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  V části **Objednávka** zadejte **Podrobnosti objednávky**. Zadejte nebo vyberte následující informace.

    |Nastavení|Hodnota|
    |---|---|
    |Name|Zadejte popisný název pro sledování objednávky.<br> Název může být tvořen 3 až 24 písmeny, číslicemi a spojovníky. <br> Název musí začínat a končit písmenem nebo číslicí. |
    |Skupina prostředků| Použijte existující skupinu prostředků, nebo vytvořte novou. <br> Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně. |
    |Cílová oblast Azure| K účtu úložiště přiřaďte oblast.<br> V současné době se podporují účty úložiště ve všech oblastech USA, Západní Evropě, Severní Evropě, Kanadě a Austrálii. |
    |Odhadovaná velikost dat v TB| Zadejte odhad v TB. <br>Podle objemu dat vám Microsoft pošle odpovídající počet 8TB disků SSD (s využitelnou kapacitou 7 TB). <br>Maximální využitelná kapacita 5 disků je 35 TB. |
    |Klíč pro disky| Pokud zaškrtnete možnost **Použít namísto klíče vygenerovaného službou Azure vlastní klíč**, zadejte klíč pro disky. <br> Zadejte klíč alfanumerické 12 až 32 znaků, který má alespoň jedna číslice a jeden speciální znak. Povolené speciální znaky jsou `@?_+`. <br> Tuto možnost můžete volitelně přeskočit a k odemknutí disků použít klíč vygenerovaný službou Azure.|
    |Cílové úložiště     | Vyberte si z účtu úložiště, spravované disky nebo obojí. <br> Založené na zadanou oblast Azure, vyberte účet úložiště z filtrovaný seznam existující účet úložiště. Data Box je možné propojit až s 10 účty úložiště. <br> Můžete také vytvořit novou **pro obecné účely v1**, **pro obecné účely v2**, nebo **účtu úložiště objektů Blob**. <br>Nelze použít účty úložiště, které mají nakonfigurovaná pravidla. Účty úložiště musí **povolit přístup ze všech sítí** v části virtuální sítě a brány firewall.|

    Pokud používáte účet úložiště jako cílové úložiště, vidíte na následujícím snímku obrazovky:

    ![Data Box Disk pořadí pro účet úložiště](media/data-box-disk-deploy-ordered/order-storage-account.png)

    Pokud používáte Data Box Disk pro vytvoření spravovaných disků z místních virtuálních pevných disků, musíte také zadejte následující informace:

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Skupina prostředků     | Pokud máte v úmyslu vytvoření spravovaných disků z místní virtuální pevné disky, vytvořte novou skupinu prostředků. Použijte existující skupinu prostředků, pouze pokud byl vytvořen pro objednávku Data Box Disk pro spravovaný disk pomocí služby Data Box. <br> Je podporován pouze jedné skupiny prostředků.|

    ![Data Box Disk pořadí spravovaného disku](media/data-box-disk-deploy-ordered/order-managed-disks.png)

    Účet úložiště zadaný pro spravované disky se používá jako přípravného účtu úložiště. Služba Data Box nahraje virtuální pevné disky do přípravného účtu úložiště a pak převede na spravované disky a přesune do skupiny prostředků. Další informace najdete v tématu [ověřit data nahrát do Azure](data-box-disk-deploy-picked-up.md#verify-data-upload-to-azure).

13. Klikněte na **Další**.

    ![Zadání podrobností objednávky](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. Na kartě **Dodací adresa** zadejte jméno a příjmení, název a poštovní adresu společnosti a platné telefonní číslo. Klikněte na **Ověřit adresu**. Služba ověří, zda je dodací adresa pro službu dostupná. Pokud je pro zadanou dodací adresu služba k dispozici, obdržíte o tom oznámení. 

    ![Zadání dodací adresy](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. Na kartě **Podrobnosti o oznámení** zadejte e-mailové adresy. Na tyto e-mailové adresy vám služba bude posílat e-mailová oznámení o všech aktualizacích stavu objednávky. 

    Doporučujeme použít skupinový e-mail, abyste oznámení mohli dostávat i tehdy, když odejde správce skupiny.

16. Na kartě **Souhrn** zkontrolujte informace týkající se objednávky, kontaktu, oznámení a podmínkách ochrany osobních údajů. Zaškrtněte políčko, že souhlasíte s podmínkami ochrany osobních údajů.

17. Klikněte na **Objednat**. Vytvoření objednávky trvá několik minut.

 
## <a name="track-the-order"></a>Sledování objednávky

Po odeslání objednávky můžete její stav sledovat na webu Azure Portal. Přejděte na objednávku a potom do části **Přehled**, kde se můžete podívat na stav. Na portálu se úloha zobrazuje ve stavu **Objednáno**.

![Služba Data Box Disk ve stavu Objednáno](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

Pokud disky nejsou k dispozici, obdržíte oznámení. Pokud jsou disky k dispozici, Microsoft určí disky k odeslání a připraví balíček s disky. Při přípravě disků dojde k těmto akcím:

- Disky se zašifrují pomocí šifrování AES-128 nástrojem BitLocker.  
- Disky jsou uzamknuty, aby se zabránilo neoprávněnému přístupu k nim.
- Během tohoto procesu se vygeneruje klíč k odemknutí disků.

Jakmile budou disky připraveny, stav objednávky na portálu se změní na **Zpracováno**.

Microsoft potom připraví a odešle disky přes místní přepravní službu. Po odeslání disků obdržíte číslo pro sledování zásilky. Stav objednávky na portálu se změní na **Odesláno**.

## <a name="cancel-the-order"></a>Zrušení objednávky

Pokud chcete tuto objednávku zrušit, přejděte na webu Azure Portal do části **Přehled** a na panelu příkazů klikněte na **Zrušit**.

Objednávku můžete zrušit pouze při objednávání disků a při zpracování objednávky k odeslání. Jakmile je objednávka zpracována, už ji nelze zrušit.

![Zrušení objednávky](media/data-box-disk-deploy-ordered/cancel-order1.png)

Pokud chcete odstranit zrušenou objednávku, přejděte do části **Přehled** a na panelu příkazů klikněte na **Odstranit**.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Objednat Data Box Disk
> * Sledování objednávky
> * Zrušení objednávky

V dalším kurzu se dozvíte, jak službu Data Box Disk nastavit.

> [!div class="nextstepaction"]
> [Nastavení služby Azure Data Box Disk](./data-box-disk-deploy-set-up.md)
