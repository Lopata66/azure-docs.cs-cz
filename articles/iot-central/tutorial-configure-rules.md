---
title: Konfigurace pravidel a akcí v Azure IoT Central | Microsoft Docs
description: Tento kurz ukazuje, jak vy jako tvůrce nakonfigurujete v aplikaci Azure IoT Central pravidla a akce založené na telemetrii.
author: ankitgupta
ms.author: ankitgup
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: fbe9e1fbd0891f2f39b05fa7ba53653188ef8e03
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158070"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Kurz: Konfigurace pravidel a akcí pro vaše zařízení v Azure IoT Central

*Tento článek je pro operátory, tvůrce a správce.*

V tomto kurzu vytvoříte pravidlo, které pošle e-mail, když teplota zařízení připojené klimatizace překročí 90&deg; F.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření pravidla založeného na telemetrii
> * Přidání akce

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte dokončit kurz [definování nového typu zařízení ve vaší aplikaci](tutorial-define-device-type.md) a vytvořit šablonu zařízení **Connected Air Conditioner** (Připojená klimatizace), se kterou budete pracovat.

## <a name="create-a-telemetry-based-rule"></a>Vytvoření pravidla založeného na telemetrii

1. Chcete-li do vaší aplikaci přidat nové pravidlo založené na telemetrii, v levé navigační nabídce zvolte **Device Explorer**:

    ![Stránka Device Explorer](media/tutorial-configure-rules/explorerpage1.png)

    Zobrazí se šablona zařízení **Connected Air Conditioner (1.0.0)** a zařízení **Connected Air Conditioner-1**, které jste vytvořili v předchozím kurzu.

2. Pokud chcete zahájit přizpůsobení zařízení připojené klimatizace, vyberte zařízení, které jste vytvořili v předchozím kurzu:

    ![Stránka připojené klimatizace](media/tutorial-configure-rules/builderdevicelist1.png)

3. Když chcete v zobrazení **Pravidla** začít přidávat pravidlo, zvolte **Pravidla** a klikněte na **Upravit šablonu**:

    ![Zobrazení pravidel](media/tutorial-configure-rules/builderedittemplate.png)

4. Pokud chcete vytvořit telemetrické pravidlo založené na prahové hodnotě, klikněte na **Nové pravidlo** a pak na **Telemetrie**.

    ![Úprava šablony](media/tutorial-configure-rules/buildernewrule.png)

5. Při definování pravidla použijte informace v následující tabulce:

    | Nastavení                                      | Hodnota                             |
    | -------------------------------------------- | ------------------------------    |
    | Název                                         | Upozornění klimatizace na teplotu |
    | Enable rule for all devices of this template (Povolit pravidlo pro všechna zařízení této šablony) | Zapnuto                                |
    | Enable rule on this device (Povolit pravidlo na tomto zařízení)                   | Zapnuto                                |
    | Podmínka                                    | Teplota je větší než 90    |
    | Agregace                                  | Žádný                              |

    ![Podmínka pravidla teploty](media/tutorial-configure-rules/buildertemperaturerule1.png)

## <a name="add-an-action"></a>Přidání akce

Když definujete pravidlo, také definujete akci, kterou chcete spustit, když jsou podmínky pravidla splněny. V tomto kurzu přidáte akci odesílání e-mailu jako oznámení, že se pravidlo aktivovalo.

1. Pokud chcete přidat **akci**, napřed pravidlo **uložte** a pak se posuňte dolů na panel **Configure Telemetry Rule** (Konfigurovat telemetrické pravidlo), vyberte **+** vedle **Actions** (Akce) a pak zvolte **Email** (E-mail):

    ![Akce pravidla teploty](media/tutorial-configure-rules/builderaddaction1.png)

2. Při definování akce použijte informace v následující tabulce:

    | Nastavení   | Hodnota                          |
    | --------- | ------------------------------ |
    | Akce        | Vaše e-mailová adresa             |
    | Poznámky     | Teplota klimatizace překročila prahovou hodnotu. |

    > [!NOTE]
    > Pokud chcete dostávat e-mailové oznámení, e-mailová adresa musí být [ID uživatele v aplikaci](howto-administer.md) a tento uživatel se musel k aplikaci alespoň jednou přihlásit.

    ![Akce teploty Application Builder (Tvůrce aplikací)](media/tutorial-configure-rules/buildertemperatureaction.png)

3. Zvolte **Uložit**. Pravidlo je uvedené na stránce **Rules** (Pravidla):

    ![Pravidla Application Builder (Tvůrce aplikací)](media/tutorial-configure-rules/builderrules1.png)

4. Volbou **Done** (Hotovo) ukončete režim **úpravy šablony**.
 

## <a name="test-the-rule"></a>Testování pravidla

Krátce po uložení se pravidlo aktivuje. Pokud jsou splněny podmínky definované v pravidle, vaše aplikace pošle zprávu na e-mailovou adresu, kterou jste zadali v akci.

![Akce e-mailu](media/tutorial-configure-rules/email.png)

> [!NOTE]
> Po dokončení testování pravidlo vypněte, aby vám do schránky nepřicházela upozornění. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Vytvoření pravidla založeného na telemetrii
> * Přidání akce

Teď, když jste definovali pravidlo založené na prahové hodnotě, dalším doporučeným krokem je [Přizpůsobení zobrazení operátora](tutorial-customize-operator.md).

Další informace o různých typech pravidel v Azure IoT Central a postup parametrizace definice pravidla najdete v tématech:
* [Vytvoření pravidla telemetrie a nastavení oznámení](howto-create-telemetry-rules.md)
* [Vytvoření pravidla událostí a nastavení oznámení](howto-create-event-rules.md)

<!-- Next tutorials in the sequence -->
