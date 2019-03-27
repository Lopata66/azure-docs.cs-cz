---
title: Microsoft Azure Data Box Gateway přístup k zařízení, napájení a režim připojení | Dokumentace Microsoftu
description: Popisuje, jak spravovat přístup, napájení a režimu připojení pro zařízení Azure Data Box brány, že pomáhá přenášet data do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 72d3455f37d0ccef0dd5b7d8882f70670de07572
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497316"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Spravovat přístup, napájení a režimu připojení pro bránu Azure Data Box

Tento článek popisuje, jak spravovat režimu přístupu, power a připojení pro bránu Azure Data Box. Tato operace se provádí prostřednictvím místního webového uživatelského rozhraní nebo na webu Azure portal.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Spravovat přístup zařízení
> * Správa režim připojení
> * Správa napájení

## <a name="manage-device-access"></a>Spravovat přístup zařízení

Přístup k zařízení Data Box brány je řízen pomocí hesla zařízení. Můžete změnit heslo prostřednictvím místního webového uživatelského rozhraní. Můžou také resetovat heslo zařízení na portálu Azure portal.

### <a name="change-device-password"></a>Změna hesla zařízení

Postupujte podle těchto kroků v Uživatelském rozhraní místní, chcete-li změnit heslo zařízení.

1. V místním webovém uživatelském rozhraní, přejděte na **údržby > Změna hesla**.
2. Zadejte aktuální heslo a pak nové heslo. Zadané heslo musí být dlouhé 8 až 16 znaků. Heslo musí obsahovat 3 z následujících znaků: velká písmena, malá písmena, číselné a speciální znaky. Potvrzení nového hesla.

    ![Změnit heslo](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Klikněte na tlačítko **změnit heslo**.
 
### <a name="reset-device-password"></a>Resetovat heslo zařízení

Obnovení pracovního postupu není nutné, aby si možná Vzpomínáte staré heslo a je užitečné v případě ztráty hesla není. Tento pracovní postup se provádí na webu Azure Portal.

1. Na webu Azure Portal, přejděte na **přehled > resetovat heslo správce**.

    ![Resetování hesla](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Zadejte nové heslo a potvrďte jej. Zadané heslo musí být dlouhé 8 až 16 znaků. Heslo musí obsahovat 3 z následujících znaků: velká písmena, malá písmena, číselné a speciální znaky. Klikněte na tlačítko **resetování**.

    ![Resetování hesla](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>Správa režim připojení

Kromě výchozí normálním režimu můžete zařízení také spustit v částečně odpojený nebo odpojeném režimu. Každá z těchto režimech je popsaný níže:

- **Částečně odpojení** – v tomto režimu zařízení nemůžete nahrát žádné data do sdílených složek ale je možné spravovat prostřednictvím portálu Azure portal.

    Tento režim se obvykle používá při práci v síti satelitních Účtovaná podle objemu dat a cílem je minimalizovat využití šířky pásma sítě. Využití sítě minimální může stále dojít k pro monitorování provozu zařízení.

- **Odpojení** – v tomto režimu zařízení je plně odpojen od cloudu a oba cloudu nahrávání a stahování jsou zakázané. Zařízení je možné spravovat pouze prostřednictvím místního webového uživatelského rozhraní.

    Tento režim se obvykle používá, když chcete převést zařízení do režimu offline.

Chcete-li změnit režim zařízení, postupujte podle těchto kroků:

1. V místním webovém uživatelském rozhraní vašeho zařízení, přejděte na **konfigurace > Nastavení Cloud**.
2. Zakažte **cloudu nahrávání a stahování**.
3. Spustit v částečně odpojeném režimu zařízení, povolit **Azure portal management**.

    ![Režim připojení](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Ke spuštění zařízení v odpojeném režimu, zakažte **Azure portal management**. Teď můžete zařízení spravovat jenom prostřednictvím místního webového uživatelského rozhraní.

    ![Režim připojení](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Správa napájení

Můžete vypnout nebo restartovat fyzické a virtuální zařízení pomocí místního webového uživatelského rozhraní. Doporučujeme před restartováním přepnout sdílené složky na hostiteli a potom na zařízení do offline režimu. Tato akce minimalizuje možnost poškození dat.

1. V místním webovém uživatelském rozhraní, přejděte na **údržby > nastavení napájení**.
2. Klikněte na tlačítko **vypnutí** nebo **restartovat** v závislosti na tom, co chcete udělat.

    ![Nastavení napájení](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **Ano** pokračovat.

> [!NOTE]
> Pokud vypnete virtuální zařízení, je potřeba spustit zařízení prostřednictvím služby správy hypervisoru.
