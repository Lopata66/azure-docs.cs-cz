---
title: 'Kurz: vytvoření nové aplikace Unity pro HoloLens'
description: V tomto kurzu se naučíte, jak vytvořit novou aplikaci pro HoloLens s využitím prostorových kotev Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 07/05/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e1abb759c80e770f1e650c232b6b2e21232b7e6f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "75457724"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Kurz: podrobné pokyny k vytvoření nové aplikace Unity pro HoloLens pomocí prostorových kotev Azure

V tomto kurzu se dozvíte, jak vytvořit novou aplikaci Unity pro HoloLens pomocí prostorových kotev Azure.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující:

1. Počítač se systémem Windows se sadou <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> nainstalovaný s úlohou **vývoje Univerzální platforma Windows** a komponentou **Windows 10 SDK (10.0.18362.0 nebo novější)** a <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a>.
2. [Rozšíření Visual Studio/WinRT v jazyce C++](https://aka.ms/cppwinrt/vsix) pro Visual Studio by se mělo nainstalovat z [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Zařízení HoloLens s povoleným [vývojářským režimem](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) . Tento článek vyžaduje zařízení HoloLens s [Windows 10 říjen 2018 Update](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (označuje se také jako RS5). Chcete-li provést aktualizaci na nejnovější verzi na HoloLens, otevřete aplikaci **Nastavení** , pokračujte na **aktualizace & zabezpečení**a pak vyberte tlačítko **Vyhledat aktualizace** .

## <a name="getting-started"></a>Začínáme

Nejdřív nastavíme náš projekt a scénu Unity:
1. Spusťte Unity.
2. Vyberte **Nový**.
4. Ujistěte se, že je vybraná možnost **3D** .
5. Pojmenujte projekt a zadejte **umístění**pro uložení.
6. Klikněte na **vytvořit projekt**.
7. Uložit prázdnou výchozí scénu do nového souboru pomocí: **soubor** > **Uložit jako**.
8. Pojmenujte novou **scénu** a stiskněte tlačítko **Uložit** .

**Nastavení projektu**

Teď nastavíme některá nastavení projektů Unity, která nám pomůžou zaměřit se na vývoj pro Windows holografické sady Windows.

Nejprve umožňuje nastavit nastavení kvality pro naši aplikaci.
1. Výběr možnosti **Upravit** > **Nastavení** > projektu**kvalita**
2. Ve sloupci loga **Windows Store** klikněte na šipku na **výchozím** řádku a vyberte **velmi nízká**. Víte, že nastavení se použije správně, když je pole ve sloupci **Windows Store** a **velmi málo** řádku zelené.

Musíme nám poznat, že aplikace, kterou se snažíme exportovat, by měla vytvořit moderní zobrazení místo 2D zobrazení. Vytvoříme moderní zobrazení tím, že se povolí podpora virtuální reality na Unity cílící na sadu Windows 10 SDK.

1. Přejít na **Upravit** > **Nastavení** > projektu**Player**.
2. Na **panelu Inspektor** pro **nastavení přehrávače**vyberte ikonu **Windows Store** .
3. Rozbalte skupinu **Nastavení XR** .
4. V části **vykreslování** zaškrtněte políčko **podporované virtuálními realitami** a přidejte nový seznam **sady SDK pro Virtual Reality** .
5. Ověřte, že se v seznamu zobrazuje **Windows Mixed reality** . Pokud ne, vyberte **+** tlačítko v dolní části seznamu a zvolte **Windows Mixed reality**.

> [!NOTE]
> Pokud nevidíte ikonu Windows Storu, před instalací se ujistěte, že jste vybrali back-end skriptování .NET pro Windows Store. V takovém případě možná budete muset znovu nainstalovat Unity se správnou instalací Windows.

**Ověřit konfiguraci skriptovacího back-endu**
1. Přejít na **Upravit** > **Nastavení** > projektu**přehrávač** (můžete mít stále otevřený **přehrávač** z předchozího kroku).
2. Na **panelu Inspektor** pro **nastavení přehrávače**vyberte ikonu **Windows Store** .
3. V části **Další konfigurace nastavení** se ujistěte, že je pro **skriptování back-end** nastavená možnost **IL2CPP**.

**Nastavit možnosti**
1. Přejít na **Upravit** > **Nastavení** > projektu**přehrávač** (můžete mít stále otevřený **přehrávač** z předchozího kroku).
2. Na **panelu Inspektor** pro **nastavení přehrávače**vyberte ikonu **Windows Store** .
3. V části konfigurace **Nastavení publikování** ověřte **InternetClientServer** a **SpatialPerception**.

**Nastavení hlavní virtuální kamery**
1. Na **panelu hierarchie**vyberte **hlavní kamera**.
2. V **inspektoru**nastavte jeho pozici transformace na **0, 0, 0**.
3. Vyhledejte vlastnost **Vymazat příznaky** a změňte rozevírací seznam z **Skybox** na **Solid Color**.
4. Kliknutím na tlačítko v poli **pozadí** otevřete výběr barvy.
5. Nastavte **R, G, B a** a na **0**.
6. Vyberte **Přidat součást** a vyhledejte a přidejte **kolizi prostorového mapování**.

**Vytvoření našeho skriptu**
1. V podokně **projekt** vytvořte ve složce **assets (prostředky** ) novou složku a **skripty**.
2. Klikněte pravým tlačítkem na složku a vyberte **vytvořit >**, **skript C#**. **AzureSpatialAnchorsScript**název.
3. Přejít na **GameObject** -> **vytvořit prázdné**
4. Vyberte ho a v **inspektoru** ho přejmenujte z **GameObject** na **MixedRealityCloud**. Vyberte **Přidat komponentu** a vyhledejte a přidejte **AzureSpatialAnchorsScript**.

**Vytvoření Prefab koule**
1. Přejít na **GameObject** -> **3D objekt** -> **sphere**.
2. V **inspektoru**nastavte jeho měřítko na **0,25, 0,25, 0,25**.
3. V podokně **hierarchie** Najděte objekt **sphere** . Klikněte na něj a přetáhněte ho do složky **assets (prostředky** ) v podokně **projekt** .
4. Klikněte pravým tlačítkem a **odstraňte** původní oblast, kterou jste vytvořili v podokně **hierarchie** .

Nyní byste měli mít v podokně **projektu** Prefab sphere.

## <a name="trying-it-out"></a>Vyzkoušení
Chcete-li otestovat, že vše funguje, sestavte aplikaci v **Unity** a nasaďte ji ze sady **Visual Studio**. Postupujte podle kapitoly 6 ze [ **základních základů pro Mr 100: Začínáme s** kurzem Unity](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) . Měli byste vidět úvodní obrazovku Unity a pak Vymazat displej.

## <a name="place-an-object-in-the-real-world"></a>Umístit objekt do reálného světa
Pojďme vytvořit & umístit objekt pomocí vaší aplikace. Otevřete řešení sady Visual Studio, které jsme vytvořili při [nasazení naší aplikace](#trying-it-out).

Nejdřív přidejte následující importy do `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Pak do své `AzureSpatialAnchorsScript` třídy přidejte následující proměnné členů:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-42,48-52,60-79)]

Než budeme pokračovat, musíme nastavit sphere Prefab, kterou jsme vytvořili v naší členské proměnné spherePrefab. Vraťte se zpět do **Unity**.
1. V části **Unity**vyberte objekt **MixedRealityCloud** v podokně **hierarchie** .
2. Klikněte na Prefab **koule** , kterou jste uložili v podokně **projektu** . Přetáhněte **koulí** , na kterou jste klikli, do oblasti **sphere Prefab** v části **skriptové kotvy Azure (skript)** v podokně **inspektor** .

Nyní byste měli mít nastavenou **koule** jako Prefab na vašem skriptu. Sestavte z **Unity** a pak znovu otevřete výsledné řešení sady **Visual Studio** , stejně jako jste [to právě zkoušeli](#trying-it-out).

V **aplikaci Visual Studio**otevřete `AzureSpatialAnchorsScript.cs` znovu. Do své `Start()` metody přidejte následující kód. Tento kód se zaplní `GestureRecognizer`, který se detekuje, když dojde k klepnutí vzduchu a volání `HandleTap`.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-90,93&highlight=4-10)]

Nyní je nutné přidat následující `HandleTap()` metodu níže. `Update()` Provede přetypování do ray a získá bod přístupů, na který se má koule umístit.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-277,299-300,304-312)]

Teď je potřeba vytvořit sphere. Koule bude zpočátku bílá, ale tato hodnota se upraví později. Přidejte následující `CreateAndSaveSphere()` metodu:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-325,390)]

Spusťte aplikaci ze sady **Visual Studio** a znovu ji ověřte. Tentokrát můžete klepnutím na obrazovku vytvořit & umístit svou bílou plochu na plochu podle vašeho výběru.

## <a name="set-up-the-dispatcher-pattern"></a>Nastavení modelu dispečera

Při práci s Unity je potřeba, aby všechna rozhraní API Unity, například rozhraní API, pomocí kterých se provádí aktualizace uživatelského rozhraní, mohla probíhat v hlavním vlákně. V kódu, který zapíšeme, ale budeme zpětně nakládat zpětná volání na jiných vláknech. Chceme aktualizovat uživatelské rozhraní v těchto zpětných voláních, takže potřebujeme způsob, jak přejít z vlákna z boku do hlavního vlákna. Chcete-li spustit kód v hlavním vlákně z vedlejšího vlákna, použijeme vzor dispečera.

Pojďme přidat členskou proměnnou dispatchQueue, což je fronta akcí. Do fronty přiřadíme akce a pak se vyřadí a spustí akce v hlavním vlákně.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=38-51&highlight=6-9)]

Nyní přidáme způsob, jak do fronty přidat akci. Přidat `QueueOnUpdate()` práva po `Update()` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=107-117)]

Teď používáme smyčku Update () ke kontrole, jestli je nějaká akce zařazená do fronty. Pokud ano, vyřadíme akci a spustíme ji.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=95-105&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Získat sadu SDK prostorových kotev Azure

## <a name="via-unity-package"></a>[Přes balíček Unity](#tab/UnityPackage)

Teď si stáhneme sadu SDK prostorových kotev Azure. Přejít na [stránku vydání GitHubu prostorových kotev Azure](https://github.com/Azure/azure-spatial-anchors-samples/releases). V části Assety (prostředky) stáhněte **AzureSpatialAnchors. unitypackage**. V Unity přejděte na **prostředky**, klikněte na **importovat balíček** > **vlastní balíček...**. Přejděte k balíčku a vyberte **otevřít**.

V okně Nový **balíček Unity balíčku** , které se zobrazí, zrušte výběr **modulů plug-in** a v pravém dolním rohu klikněte na **importovat** .

Teď je potřeba obnovit balíčky NuGet, aby bylo možné získat sadu SDK prostorových kotev Azure. Sestavte z **Unity** a pak znovu otevřete a sestavte výsledné řešení sady **Visual Studio** , jak je podrobně popsáno v části [o jeho vyzkoušení](#trying-it-out).

## <a name="via-nugetforunity"></a>[Přes NuGetForUnity](#tab/NuGetForUnity)

Nejdřív musíme nainstalovat NuGetForUnity. Přejít na [stránku vydání NuGetForUnity GitHubu](https://github.com/GlitchEnzo/NuGetForUnity/releases). V části Assety Stáhněte poslední **NuGetForUnity. unitypackage**. V Unity přejděte na **prostředky**, klikněte na **importovat balíček** > **vlastní balíček...**. Přejděte k balíčku a vyberte **otevřít**. Unity teď bude instalovat NugetForUnity. Pokud se v Unity nezobrazuje nové rozevírací seznam **NuGet** , možná budete muset kliknout pravým tlačítkem v části **projekty** > **prostředky**. Pak vyberte znovu **Importovat vše**.

Po nainstalování NuGetForUnity vyberte **NuGet** > **Spravovat balíčky NuGet**. Pak vyhledejte Microsoft. Azure. SpatialAnchors. Unity a vyberte **nainstalovat**.

Teď je potřeba sestavit, abychom získali skutečnou sadu SDK pro prostorové kotvy Azure, protože balíček NuGet, který jsme právě stáhli, obsahuje pomocné skripty. Sestavte z **Unity** a pak znovu otevřete a sestavte výsledné řešení sady **Visual Studio** , jak je podrobně popsáno v části [o jeho vyzkoušení](#trying-it-out).

---

Do řešení sady **Visual Studio** přidejte následující import do `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Pak do své `AzureSpatialAnchorsScript` třídy přidejte následující proměnné členů:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=48-63&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Připojte k místnímu ukotvení místní prostorové ukotvení Azure

Pojďme nastavit CloudSpatialAnchorSession prostorového ukotvení Azure. Začneme přidáním následující `InitializeSession()` metody do vaší `AzureSpatialAnchorsScript` třídy. Po volání se zajistí, že se vytvoří relace prostorových kotev Azure, která se při spuštění vaší aplikace správně inicializuje.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=174-202,205-209)]

Nyní potřebujeme napsat kód pro zpracování volání delegátů. Až budeme pokračovat, přidáme k nim další.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=211-226)]

Nyní zapojte `initializeSession()` metodu do vaší `Start()` metody.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-93&highlight=12)]

Nakonec do své `CreateAndSaveSphere()` metody přidejte následující kód. Připojí se k oblasti, kterou umísťujeme do reálného světa, místní prostor Azure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-338,390&highlight=14-25)]

Než budete pokračovat, budete muset vytvořit identifikátor účtu a klíč prostorových kotev Azure, pokud je ještě nemáte. Pokud je chcete získat, postupujte podle následujících částí.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Nahrání místního kotvy do cloudu

Jakmile budete mít identifikátor účtu a klíč prostorových ukotvení Azure, přejdete do části `Account Id` `SpatialAnchorsAccountId` `Account Key` a vložíte do `SpatialAnchorsAccountKey`.

Nakonec připojovat všechno dohromady. Do `SpawnNewAnchoredObject()` metody přidejte následující kód. Vyvolá `CreateAnchorAsync()` metodu hned po vytvoření vaší koule. Jakmile se metoda vrátí, kód níže provede jednu poslední aktualizaci vaší koule, přičemž změna barvy na modrou.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-391&highlight=26-77)]

Spusťte aplikaci ze sady **Visual Studio** , a to ještě jednou. Pohybujte kolem své hlavy a potom klepněte v duchu na umístění své koule. Jakmile máme dostatek snímků, koule se změní na žlutou a spustí se nahrávání do cloudu. Až se nahrávání dokončí, vaše koule se změní na modrou. V případě potřeby můžete také použít okno výstup v **aplikaci Visual Studio** a monitorovat zprávy protokolu, které vaše aplikace posílá. Budete moct sledovat Doporučené průběh vytváření a také identifikátor kotvy, který Cloud vrátí po dokončení nahrávání.

> [!NOTE]
> Pokud se zobrazí "DllNotFoundException –: nejde načíst knihovnu DLL" AzureSpatialAnchors ": zadaný modul se nepovedlo najít.", měli byste řešení **vyčistit** a znovu **sestavit** .

## <a name="locate-your-cloud-spatial-anchor"></a>Najděte své cloudové kotvy

Jedna vaše kotva se nahraje do cloudu, můžeme se pokusit o jejich vyhledání znovu. Pojďme do vaší `HandleTap()` metody přidat následující kód. Tento kód bude:

* Volání `ResetSession()`, které zastaví `CloudSpatialAnchorSession` a odebere z obrazovky existující modrou koule.
* Znovu `CloudSpatialAnchorSession` inicializovat. To provedeme, abychom si zajistili, že kotva, kterou vyhledáme, pochází z cloudu, a ne jako místní kotvu, kterou jsme vytvořili.
* Vytvořte **sledovací** proces, který bude hledat kotvu, kterou jsme nahráli do prostorových kotev Azure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-305&highlight=13-31,35-36)]

Pojďme teď přidat naše `ResetSession()` a `CleanupObjects()` metody. Můžete je umístit níže.`QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=119-172)]

Teď je potřeba, abyste připojili kód, který se vyvolá, když se na něj nachází kotva, na které se dotazuje. `InitializeSession()`Do přidejte následující zpětná volání:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=200-206&highlight=4-5)]


Nyní umožňuje přidat kód, který vytvoří & umístit zelenou koule po umístění CloudSpatialAnchor. Také se znovu aktivuje obrazovka, takže můžete celý scénář zopakovat, a to ještě jednou: vytvořte další místní kotvu, nahrajte ho a znovu ho vyhledejte.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=228-265)]

A to je vše! Spusťte aplikaci ze sady **Visual Studio** jednou, abyste si vyzkoušeli celý scénář od konce do konce. Pohybujte kolem zařízení a umístěte svou bílou plochu. Pak pokračujte v přesouvání vašeho hlavního prostředí, dokud se koule nezmění žlutě. Vaše místní kotva se nahraje a vaše koule se zachová modře. Nakonec klepněte na obrazovku ještě jednou, aby se vaše místní kotva odebrala, a pak se podíváme na svůj cloudový protějšek. Pokračujte v přesouvání zařízení, dokud se neumístí cloudové ukotvení. Zelená koule by se měla zobrazit ve správném umístění a můžete ji vypláchněte & opakujte celý scénář.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]