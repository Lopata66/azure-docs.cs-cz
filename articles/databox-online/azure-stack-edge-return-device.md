---
title: Vrácení zařízení Azure Stack Edge | Microsoft Docs
description: Přečtěte si, jak vymazat data a vrátit Azure Stack hraniční zařízení a odstranit prostředek přidružený k zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/27/2020
ms.author: alkohli
ms.openlocfilehash: aa917361ad3c967a697421e86d232e1a206c403e
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87923947"
---
# <a name="return-your-azure-stack-edge-device"></a>Vrácení zařízení Azure Stack Edge

Tento článek popisuje, jak vymazat data a vrátit zařízení Azure Stack Edge. Po vrácení zařízení můžete také odstranit prostředek přidružený k zařízení.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Vymazání dat z datových disků v zařízení
> * Iniciace návratového zařízení v Azure Portal
> * Sbalení zařízení a naplánování výdeje
> * Odstranit prostředek v Azure Portal

## <a name="erase-data-from-the-device"></a>Vymaže data ze zařízení.

Pokud chcete vymazat data z datových disků vašeho zařízení, musíte zařízení resetovat. Zařízení můžete obnovit pomocí místního webového uživatelského rozhraní nebo rozhraní PowerShell.

Než obnovíte tovární nastavení, v případě potřeby vytvořte v zařízení kopii místních dat. Data ze zařízení můžete zkopírovat do kontejneru Azure Storage.

Můžete iniciovat, že se zařízení bude vracet ještě předtím, než se zařízení resetuje. 

Pokud chcete zařízení obnovit pomocí místního webového uživatelského rozhraní, proveďte následující kroky.

1. V místním webovém uživatelském rozhraní přejdete do části **údržba > resetování zařízení**.
2. Vyberte **obnovit zařízení**.

    ![Resetování zařízení](media/azure-stack-edge-return-device/device-reset-1.png)

3. Po zobrazení výzvy k potvrzení si přečtěte upozornění a pokračujte výběrem **Ano** .

    ![Potvrdit resetování](media/azure-stack-edge-return-device/device-reset-2.png)  

Resetování smaže data z datových disků zařízení. V závislosti na množství dat v zařízení tento proces trvá přibližně 30-40 minut.

Případně se připojte k rozhraní PowerShell zařízení a pomocí `Reset-HcsAppliance` rutiny smažte data z datových disků. Další informace najdete v tématu [resetování zařízení](azure-stack-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Pokud vyměňujete nebo upgradujete na nové zařízení, doporučujeme, abyste zařízení obnovili až po obdržení nového zařízení.
> - Resetování zařízení odstraní jenom všechna místní data v zařízení. Data, která jsou v cloudu, se neodstraňují a shromažďují se [poplatky](https://azure.microsoft.com/pricing/details/storage/). Tato data se musí odstranit samostatně pomocí nástroje pro správu cloudového úložiště, jako je [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="initiate-device-return"></a>Iniciace návratového zařízení

Chcete-li zahájit proces návratu, proveďte následující kroky.

1. V Azure Portal přejít na Azure Stack hraniční/Data Box Gateway prostředek. V **přehledu**přejděte na panel příkazů v pravém podokně a vyberte **návratové zařízení**. 

    ![Vrátit zařízení 1](media/azure-stack-edge-return-device/return-device-1.png)  

2. V okně **návratové zařízení** v části **základní podrobnosti**:

    1. Zadejte sériové číslo zařízení. Sériové číslo zařízení získáte tak, že přejdete do místního webového uživatelského rozhraní zařízení a pak přejdete na **Přehled**.  
    
    ![Sériové číslo zařízení 1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. Zadejte číslo označení služby, které je pro vaše zařízení jedinečné. Značka služby se nachází v pravém dolním rohu zařízení (při zařízení). Vyžádejte si informační značku (je to panel s popisem snímku). Tento panel obsahuje systémové informace, jako je například značka služby, síťová karta, adresa MAC atd. 
    
    ![Označení služby – číslo 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. V rozevíracím seznamu vyberte důvod pro vrácení.

    ![Vrátit zařízení 2](media/azure-stack-edge-return-device/return-device-2.png) 

3. V části **Podrobnosti o dopravě**:

    1. Zadejte své jméno, název společnosti a úplnou adresu společnosti. Zadejte telefon do zaměstnání včetně kódu oblasti a ID e-mailu pro oznámení.
    2. Pokud potřebujete pole pro návrat do expedice, můžete si ho vyžádat. Odpověď **Ano** na otázku **vyžaduje prázdné pole, které se má vrátit**.

    ![Návratový zařízení 3](media/azure-stack-edge-return-device/return-device-3.png)

4. Přečtěte si **podmínky ochrany osobních údajů** a zaškrtněte políčko proti poznámce, kterou jste si přečetli a souhlasíte s podmínkami ochrany osobních údajů.

5. Vyberte možnost **Spustit návrat**.

    ![Návratový zařízení 4](media/azure-stack-edge-return-device/return-device-4.png) 

6. Po zaznamenání podrobností o tom, že se vaše zařízení bude shromažďovat, můžete Azure Stack hraničnímu týmu informovat prostřednictvím e-mailu. E-mailová aplikace se dá použít za předpokladu, že je nainstalovaná a nakonfigurovaná aplikace. Můžete také zkopírovat data pro vytvoření a odeslání e-mailu.

    ![Vrátit zařízení 5](media/azure-stack-edge-return-device/return-device-5.png) 

7. Jakmile tým Azure Stack hraničních operací obdrží e-mail, pošle vám popisek zpětné dodávky. Když obdržíte tento popisek, můžete naplánovat vyzvednutí zařízení u přepravce. 

## <a name="schedule-a-pickup"></a>Naplánování výdeje

Chcete-li naplánovat vyzvednutí, proveďte následující kroky.

1. Vypněte zařízení. V místním webovém uživatelském rozhraní přejdete do části **údržba > nastavení napájení**.
2. Vyberte **vypnout**. Po zobrazení výzvy k potvrzení klikněte na **Ano** a pokračujte. Další informace najdete v tématu [Správa napájení](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Odpojte napájecí kabely a odeberte ze zařízení všechny síťové kabely.
4. Připravte balíček dodávek pomocí vlastního pole nebo prázdného pole, které jste dostali z Azure. Umístěte zařízení a napájecí šňůry, které byly dodávány se zařízením v poli.
5. Připevněte expediční štítek, který jste dostali z Azure v balíčku.
6. Naplánujte vyzvednutí místní přepravní službou. Pokud se zařízení v nám vrátí, může být váš dopravce zdrojem nebo FedEx. Naplánování vyzvednutí pomocí zdroje UPS:

    1. Zavolejte do místní pobočky UPS (bezplatné číslo pro příslušnou zemi/oblast).
    2. Ve svém volání citujete číslo sledování zpětné dodávky, jak je znázorněno na vytištěném popisku.
    3. Pokud číslo sledování není v uvozovkách, bude zdroj napájení vyžadovat, abyste během vyzvednutí zaplatili dodatečné poplatky.

    Místo plánování vyzvednutí můžete také Azure Stack okraj vyřadit z nejbližšího rozevíracího umístění.

## <a name="delete-the-resource"></a>Odstranit prostředek

Po přijetí zařízení v datovém centru Azure se v zařízení zkontroluje poškození nebo jakékoli případné známky manipulace.

- Pokud zařízení dorazí na nedotčené a je v dobrém tvaru, měřič účtování se zastaví pro daný prostředek. Tým Azure Stack hraničních operací vás bude kontaktovat, abyste ověřili, že se zařízení vrátilo. Pak můžete odstranit prostředek přidružený k zařízení v Azure Portal.
- Pokud zařízení dorazí k výraznému poškození, mohou se vztahovat pokuty. Podrobnosti najdete v [nejčastějších dotazech o ztracených nebo poškozených](https://azure.microsoft.com/pricing/details/databox/edge/) [licenčních službách](https://www.microsoft.com/licensing/product-licensing/products)zařízení a produktu.  


Zařízení můžete v Azure Portal odstranit:

- Po umístění objednávky a před tím, než je zařízení připraveno společností Microsoft.
- Po tom, co zařízení vrátíte do Microsoftu, projde fyzickou kontrolu v datacentru Azure a zavolá Azure Stack hraničního týmu, aby se ověřilo, že se zařízení vrátilo.

Pokud jste zařízení aktivovali v jiném předplatném nebo umístění, Microsoft přesune vaši objednávku na nové předplatné nebo umístění během jednoho pracovního dne. Po přesunutí objednávky můžete tento prostředek odstranit.


Proveďte následující kroky a odstraňte zařízení a prostředek v Azure Portal.

1. V Azure Portal přejdete do svého prostředku a pak na **Přehled**. Na panelu příkazů vyberte **Odstranit**.

    ![Vybrat odstranit](media/azure-stack-edge-return-device/delete-resource-1.png)

2. V okně **Odstranit zařízení** zadejte název zařízení, které chcete odstranit, a vyberte **Odstranit**.

    ![Potvrzení odstranění](media/azure-stack-edge-return-device/delete-resource-2.png)

Po úspěšném odstranění zařízení a přidruženého prostředku budete upozorněni.


## <a name="next-steps"></a>Další kroky

- Naučte se, jak [získat náhradní Azure Stack hraniční zařízení](azure-stack-edge-replace-device.md).
