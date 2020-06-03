---
title: Vykreslení modelu s využitím Unity
description: Rychlý Start, který provede uživatele prostřednictvím kroků pro vykreslení modelu
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: ed533f571ada4cacf053157b397cca07cd65e6f8
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300568"
---
# <a name="quickstart-render-a-model-with-unity"></a>Rychlý Start: vykreslení modelu pomocí Unity

V tomto rychlém startu se dozvíte, jak spustit vzorek Unity, který vykreslí vestavěný model vzdáleně pomocí služby Azure Remote rendering (ARR).

Nebudeme se k tomu přihlédnout přímo k samotnému rozhraní API ARR nebo k nastavení nového projektu Unity. Tato témata jsou popsaná v tématu [kurz: nastavení projektu Unity od začátku](../tutorials/unity/project-setup.md).

V tomto rychlém startu se dozvíte, jak:
> [!div class="checklist"]
>
>* Nastavení místního vývojového prostředí
>* Získat a sestavit ukázkovou aplikaci s rychlým startem pro Unity
>* Vykreslení modelu v ukázkové aplikaci rychlý Start pro ARR

## <a name="prerequisites"></a>Požadavky

Pokud chcete získat přístup ke službě vzdáleného vykreslování Azure, musíte nejdřív [vytvořit účet](../how-tos/create-an-account.md).

Musí být nainstalovaný následující software:

* Windows SDK 10.0.18362.0 [(Stáhnout)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Nejnovější verzi sady Visual Studio 2019 [(Stáhnout)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio Tools pro Mixed reality](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). Konkrétně jsou nutné následující instalace *úloh* :
  * **Vývoj desktopových aplikací v C++**
  * **Vývoj Univerzální platforma Windows (UWP)**
* GIT [(Stáhnout)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(Stáhnout)](https://unity3d.com/get-unity/download)
  * Nainstalujte tyto moduly v Unity:
    * Podpora **UWP** – Univerzální platforma Windows sestavení
    * **IL2CPP** – podpora sestavení pro Windows (IL2CPP)

## <a name="clone-the-sample-app"></a>Klonování ukázkové aplikace

Otevřete příkazový řádek (typ `cmd` v nabídce Start systému Windows) a přejděte do adresáře, kam chcete uložit vzorový projekt Arr.

Spusťte následující příkazy:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

Poslední příkaz vytvoří podadresář v adresáři ARR, který obsahuje různé ukázkové projekty pro vzdálené vykreslování Azure.

Ukázková aplikace pro rychlý Start pro Unity se nachází v podadresáři *Unity/rychlý Start*.

## <a name="rendering-a-model-with-the-unity-sample-project"></a>Vykreslení modelu pomocí ukázkového projektu Unity

Otevřete centrum Unity a přidejte vzorový projekt, což je složka *ARR\azure-Remote-rendering\Unity\Quickstart* .
Otevřete projekt. V případě potřeby povolte Unity upgrade projektu na vaši nainstalovanou verzi.

Výchozí model, který vykreslíme, je [vestavěný Vzorový model](../samples/sample-model.md). Ukážeme, jak převést vlastní model pomocí převodní služby ARR v [dalším rychlém](convert-model.md)startu.

### <a name="enter-your-account-info"></a>Zadejte informace o účtu.

1. V prohlížeči assetů Unity přejděte do složky *scény* a otevřete scénu **rychlý Start** .
1. Z *hierarchie*vyberte objekt **RemoteRendering** Game.
1. V *inspektoru*zadejte své [přihlašovací údaje k účtu](../how-tos/create-an-account.md). Pokud ještě nemáte účet, [vytvořte ho](../how-tos/create-an-account.md).

![Informace o účtu ARR](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> Azure Portal zobrazí doménu vašeho účtu jenom jako *mixedreality.Azure.com*. To není pro úspěšné připojení dostatečné.
> Nastavte **AccountDomain** na `<region>.mixedreality.azure.com` , kde `<region>` je [jedna z dostupných oblastí poblíž](../reference/regions.md).

Později chceme nasadit tento projekt na HoloLens a připojit se ke službě vzdáleného vykreslování z tohoto zařízení. Vzhledem k tomu, že jsme nemuseli na zařízení zadat přihlašovací údaje, ukázka pro rychlý Start **uloží přihlašovací údaje v rámci scény Unity**.

> [!WARNING]
> Ujistěte se, že jste projekt nezkontrolovali s uloženými přihlašovacími údaji v některém úložišti, kde by došlo k úniku tajných přihlašovacích informací.

### <a name="create-a-session-and-view-the-default-model"></a>Vytvoření relace a zobrazení výchozího modelu

Kliknutím na tlačítko **Přehrát** v Unity spustíte relaci. V dolní části zobrazení na panelu *hry* by se měla zobrazit překryvný text stavu. Relace se bude podrobit sérii přechodů mezi stavy. Ve **výchozím** stavu je vzdálený virtuální počítač vyspuninstý, což trvá několik minut. Po úspěšném přechodu přejde do stavu **připraveno** . Nyní relace vstoupí do stavu **připojení** , kde se pokusí získat přístup k modulu runtime vykreslování na daném virtuálním počítači. Po úspěšném přechodu se ukázka přepne do stavu **připojeno** . V tuto chvíli začne stahovat model pro vykreslování. Kvůli velikosti modelu může stahování trvat několik minut. Pak se zobrazí vzdálený vykreslený model.

![Výstup z ukázky](media/arr-sample-output.png)

Gratulujeme! Nyní prohlížíte vzdáleně vykreslený model.

## <a name="inspecting-the-scene"></a>Kontrola scény

Po spuštění připojení vzdáleného vykreslování se panel inspektor aktualizuje o další informace o stavu:

![Přehrávání ukázek Unity](./media/arr-sample-configure-session-running.png)

Graf scény teď můžete prozkoumat tak, že vyberete nový uzel a v inspektoru kliknete na **Zobrazit podřízené objekty** .

![Hierarchie Unity](./media/unity-hierarchy.png)

Ve scéně je objekt [vyjmuté plochy](../overview/features/cut-planes.md) . Zkuste ho povolit ve vlastnostech a přesunout ho kolem:

![Změna plochy řezu](media/arr-sample-unity-cutplane.png)

Pokud chcete transformovat transformace, klikněte buď na **synchronizovat** , nebo zaškrtněte políčko **synchronizovat všechny snímky** . V případě vlastností komponenty stačí změnit jejich počet.

## <a name="next-steps"></a>Další kroky

V dalším rychlém startu nasadíme ukázku na HoloLens a zobrazí se vzdáleně vykreslený model v původní velikosti.

> [!div class="nextstepaction"]
> [Rychlý Start: nasazení ukázky Unity do HoloLens](deploy-to-hololens.md)

Alternativně lze ukázku také nasadit do stolního počítače.

> [!div class="nextstepaction"]
> [Rychlý Start: nasazení ukázky Unity do desktopu](deploy-to-desktop.md)