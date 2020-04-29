---
title: Měření reálných uživatelů s Visual Studio Mobile Center – Azure Traffic Manager
description: Nastavte mobilní aplikaci vyvinutou pomocí aplikace Visual Studio Mobile Center k odeslání Měření reálných uživatelů do Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: 3106334e1fb3e3000cbd09e00e413b34a1b55e54
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76939190"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Postup odeslání Měření reálných uživatelů pro Traffic Manager v aplikaci Visual Studio Mobile Center

Můžete nastavit mobilní aplikaci vyvinutou pomocí aplikace Visual Studio Mobile Center k odeslání Měření reálných uživatelů k Traffic Manager pomocí následujících kroků:

>[!NOTE]
> Odesílání Měření reálných uživatelů do Traffic Manageru se v současné době podporuje jenom pro Android.

Pokud chcete nakonfigurovat Měření reálných uživatelů, potřebujete získat klíč a instrumentovat svoji aplikaci pomocí balíčku rumu.

## <a name="step-1-obtain-a-key"></a>Krok 1: získání klíče
    
Měření, která provedete a odesíláte Traffic Manager z klientské aplikace, jsou identifikována službou pomocí jedinečného řetězce, který se nazývá klíč Měření reálných uživatelů (RUM). Klíč rumu můžete získat pomocí Azure Portal, REST API nebo pomocí rozhraní PowerShell/CLI.

K získání klíče rumu pomocí Azure Portal použijte následující postup:
1. V prohlížeči se přihlaste k webu Azure Portal. Pokud ještě účet nemáte, můžete si zaregistrovat zkušební verzi na měsíc zdarma.
2. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který chcete upravit, a pak na tento profil služby Traffic Manager klikněte v zobrazených výsledcích.
3. Na stránce Traffic Manager profil klikněte v části **Nastavení**na **měření reálných uživatelů** .
4. Klikněte na **vygenerovat klíč** a vytvořte nový klíč rumu.
        
   ![Generovat klíč Měření reálných uživatelů](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Obrázek 1: Měření reálných uživatelů generování klíčů**

5. Stránka zobrazuje vygenerovaný klíč rumu a fragment kódu JavaScriptu, který musí být vložen do stránky HTML.
 
   ![Kód JavaScriptu pro Měření reálných uživatelů klíč](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Obrázek 2: Měření reálných uživatelů klíč a měření JavaScriptu**
 
6. Kliknutím na tlačítko **Kopírovat** ZKOPÍRUJTE klíč rumu. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Krok 2: instrumentace aplikace pomocí balíčku rumu sady Mobile Center SDK

Pokud s Visual Studio Mobile Center začínáte, přejděte na jeho [Web](https://mobile.azure.com). Podrobné pokyny k integraci sady SDK najdete v tématu [Začínáme s Android SDK](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

Pokud chcete použít Měření reálných uživatelů, proveďte následující postup:

1.  Přidat sadu SDK do projektu

    V rámci verze Preview technologie ATM rumu SDK musíte explicitně odkazovat na úložiště balíčků.

    V souboru **App/Build. Gradle** přidejte následující řádky:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    V souboru **App/Build. Gradle** přidejte následující řádky:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Spustit sadu SDK

    Otevřete hlavní třídu aktivity vaší aplikace a přidejte následující příkazy importu:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Vyhledejte `onCreate` zpětné volání ve stejném souboru a přidejte následující kód:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Další kroky
- Další informace o [měření reálných uživatelů](traffic-manager-rum-overview.md)
- Informace [o tom, jak Traffic Manager funguje](traffic-manager-overview.md)
- Další informace o [Mobile Center](https://docs.microsoft.com/mobile-center/)
- [Zaregistrovat](https://mobile.azure.com) se v Mobile Center
- Další informace o [metodách směrování provozu](traffic-manager-routing-methods.md) , které podporuje Traffic Manager
- Informace o tom, jak [vytvořit profil Traffic Manager](traffic-manager-create-profile.md)

