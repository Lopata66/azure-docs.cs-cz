---
title: Kurz k objednávky zařízení Azure Data Box | Dokumentace Microsoftu
description: Seznamte se s požadavky na nasazení a postupem objednání Azure Data Boxu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: 05d522550b96813c6b8326d83f09d7028466c835
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926219"
---
# <a name="tutorial-order-azure-data-box"></a>Kurz: Objednávky zařízení Azure Data Box

Azure Data Box Disk je hybridní řešení, které umožňuje rychle, snadno a spolehlivě importovat místní data do Azure. Svá data přenesete do 80TB (využitelná kapacita) úložného zařízení od Microsoftu a pak toto zařízení odešlete zpět. Data se potom nahrají do Azure.

Tento kurz popisuje, jak objednat Azure Data Box. V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Požadavky na nasazení Data Boxu
> * Objednání Data Boxu
> * Sledování objednávky
> * Zrušení objednávky

## <a name="prerequisites"></a>Požadavky

Před nasazením zařízení dokončete následující požadavky na konfiguraci služby a zařízení Data Box.

### <a name="for-service"></a>Služba

Než začnete, ujistěte se, že:
- Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.
- Typ předplatného, které používáte pro službu Data Box, musí být jeden z následujících:
    - Smlouva Microsoft Enterprise (EA). Další informace o [předplatných se smlouvou Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Cloud Solution Provider (CSP). Další informace o [programu Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure Sponsorship. Získejte další informace o [programu Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Pokud chcete vytvořit objednávku Data Boxu, potřebujete mít u předplatného přístup vlastníka nebo přispěvatele.

### <a name="for-device"></a>Zařízení

Než začnete, ujistěte se, že:
- Máte k síti datacentra připojený hostitelský počítač. Data Box zkopíruje data k tohoto počítače. Na vašem hostitelském počítači musí být podporovaný operační systém, jak je popsáno v [systémových požadavcích na Azure Data Box](data-box-system-requirements.md).
- Vaše datacentrum má přístup k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení alespoň 10 GbE. Pokud nemáte k dispozici připojení 10 GbE, je možné použít datové propojení 1 GbE, což ale bude mít vliv na rychlosti kopírování.


## <a name="order-data-box"></a>Objednání Data Boxu

Provedením následujících kroků na webu Azure Portal si objednejte zařízení.

1. Pomocí svých přihlašovacích údajů Microsoft Azure se přihlaste na následující adrese URL: [https://portal.azure.com](https://portal.azure.com).
2. Klikněte na **+ Vytvořit prostředek** a vyhledejte *Azure Data Box*. Klikněte na **Azure Data Box**.
    
   [![Vyhledání Azure Data Boxu 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Klikněte na možnost **Vytvořit**.

4. Podívejte se, zda je služba Data Box dostupná ve vaší oblasti. Zadejte nebo vyberte následující informace a pak klikněte na **Použít**. 

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Předplatné     | Vyberte předplatné EA, CSP nebo Azure sponsorship pro službu Data Box. <br> Předplatné je propojeno s vaším fakturačním účtem.       |
    |Typ přenosu     | Vyberte **Importovat do Azure**.        |
    |Zdrojová země     |   Vyberte zemi, ve které jsou data aktuálně umístěna.         |
    |Cílová oblast Azure     |     Vyberte oblast Azure, do které chcete data přenést.        |

5. Vyberte **Data Box**. Maximální využitelné kapacity na jednu objednávku je 80 TB. V případě větších objemů dat můžete vytvořit více objednávek.

      [![Výběr možnosti Data Boxu 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. V části **Objednávka** zadejte **Podrobnosti objednávky**. Zadejte nebo vyberte následující informace a klikněte na **Další**.
    
    |Nastavení  |Hodnota  |
    |---------|---------|
    |Název     |  Zadejte popisný název pro sledování objednávky. <br> Název může být tvořen 3 až 24 písmeny, číslicemi a spojovníky. <br> Název musí začínat a končit písmenem nebo číslicí.      |
    |Skupina prostředků     |   Použijte existující skupinu prostředků, nebo vytvořte novou. <br> Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně.         |
    |Cílová oblast Azure     | K účtu úložiště přiřaďte oblast. <br> Další informace najdete v části věnované [dostupnosti v oblastech](data-box-overview.md#region-availability).        |
    |Cílové úložiště     | Vyberte si z účtu úložiště, spravované disky nebo obojí. <br> Na základě zadané oblasti Azure vyberte z filtrovaného seznamu existujících účtů úložiště jeden nebo více účtů úložiště. Data Box je možné propojit až s 10 účty úložiště. <br> Můžete také vytvořit novou **pro obecné účely v1**, **pro obecné účely v2**, nebo **účtu úložiště objektů Blob**. <br>Účty úložiště s virtuálními sítěmi se podporují. Služba Data Box pro práci s účty úložiště zabezpečené, povolte důvěryhodným službám v nastavení brány firewall sítě účtu úložiště. Další informace najdete v tématu Jak [přidat zařízení Azure Data Box jako důvěryhodné služby](../storage/common/storage-network-security.md#exceptions).|

    Pokud používáte účet úložiště jako cílové úložiště, vidíte na následujícím snímku obrazovky:

    ![Pořadí dat pole pro účet úložiště](media/data-box-deploy-ordered/order-storage-account.png)

    Pokud používáte zařízení Data Box pro vytvoření spravovaných disků z místních virtuálních pevných disků, musíte také zadat následující informace:

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Skupiny prostředků     | Pokud máte v úmyslu vytvoření spravovaných disků z místních virtuálních pevných disků, vytvořte nové skupiny prostředků. Můžete použít existující skupinu prostředků, pouze v případě, že skupina prostředků se vytvořila dříve při vytváření objednávka zařízení Data Box pro spravovaný disk pomocí služby Data Box. <br> Zadejte více skupin prostředků, které jsou odděleny středníky. Podporují se maximálně 10 skupin prostředků.|

    ![Pole data, aby spravovaný disk](media/data-box-deploy-ordered/order-managed-disks.png)

    Účet úložiště zadaný pro spravované disky se používá jako přípravného účtu úložiště. Nahrávání služby Data Box virtuálních pevných disků jako stránka objekty BLOB do přípravného účtu úložiště před převod na spravované disky a přesunete do skupiny prostředků. Další informace najdete v tématu [ověřit data nahrát do Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. V části **Dodací adresa** zadejte jméno a příjmení, název a poštovní adresu společnosti a platné telefonní číslo. Klikněte na **Ověřit adresu**. Služba ověří, zda je dodací adresa pro službu dostupná. Pokud je pro zadanou dodací adresu služba k dispozici, obdržíte o tom oznámení. Klikněte na **Další**.

8. Na kartě **Podrobnosti o oznámení** zadejte e-mailové adresy. Na tyto e-mailové adresy vám služba bude posílat e-mailová oznámení o všech aktualizacích stavu objednávky.

    Doporučujeme použít skupinový e-mail, abyste oznámení mohli dostávat i tehdy, když odejde správce skupiny.

9. Na kartě **Souhrn** zkontrolujte informace týkající se objednávky, kontaktu, oznámení a podmínkách ochrany osobních údajů. Zaškrtněte políčko, že souhlasíte s podmínkami ochrany osobních údajů.

10. Klikněte na **Objednat**. Vytvoření objednávky trvá několik minut.


## <a name="track-the-order"></a>Sledování objednávky

Po odeslání objednávky můžete její stav sledovat na webu Azure Portal. Přejděte na svoji objednávku Data Box a potom přejděte ke **přehled** k zobrazení stavu. Na portálu se objednávka zobrazuje ve stavu **Objednáno**.

Pokud zařízení není k dispozici, dostanete oznámení. Pokud je zařízení k dispozici, Microsoft určí zařízení k odeslání a připraví zásilku. Při přípravě zařízení dojde k těmto akcím:

- Pro každý účet úložiště přidružený k zařízení se vytvoří sdílené složky SMB.
- Pro každou sdílenou složku se vygenerují přihlašovací údaje pro přístup, například uživatelské jméno a heslo.
- Také se vygeneruje heslo zařízení, které pomáhá odemknout zařízení.
- Data Box se uzamkne, aby se zabránilo jakémukoli neoprávněnému přístupu k zařízení.

Jakmile bude zařízení připravené, stav objednávky na portálu se změní na **Zpracováno**.

![Zpracovaná objednávka Data Boxu](media/data-box-overview/data-box-order-status-processed.png)

Microsoft potom připraví a odešle vaše zařízení přes místní přepravní službu. Po odeslání zařízení obdržíte číslo pro sledování zásilky. Stav objednávky na portálu se změní na **Odesláno**.

![Odeslaná objednávka Data Boxu](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Zrušení objednávky

Pokud chcete tuto objednávku zrušit, přejděte na webu Azure Portal do části **Přehled** a na panelu příkazů klikněte na **Zrušit**.

Objednávku můžete po odeslání kdykoli zrušit, dokud se stav objednávky nezmění na Zpracováno.
 
Pokud chcete odstranit zrušenou objednávku, přejděte do části **Přehled** a na panelu příkazů klikněte na **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Požadavky na nasazení Data Boxu
> * Objednání Data Boxu
> * Sledování objednávky
> * Zrušení objednávky

V dalším kurzu se dozvíte, jak Data Box nastavit.

> [!div class="nextstepaction"]
> [Nastavení Azure Data Boxu](./data-box-deploy-set-up.md)


