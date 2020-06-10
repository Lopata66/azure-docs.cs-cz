---
title: Řešení potíží
description: Informace o řešení potíží pro vzdálené vykreslování Azure
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 0aa09ffe5b5dd9dd0f49204495ecdd7179a0f36f
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84660023"
---
# <a name="troubleshoot"></a>Řešení potíží

Tato stránka obsahuje seznam běžných problémů, které mají vliv na vzdálené vykreslování Azure, a způsoby, jak je vyřešit.

## <a name="cant-link-storage-account-to-arr-account"></a>Nejde propojit účet úložiště s účtem ARR.

V některých případech se během [propojování účtu úložiště](../how-tos/create-an-account.md#link-storage-accounts) v seznamu nenachází účet vzdáleného vykreslování. Pokud chcete tento problém vyřešit, v Azure Portal klikněte na účet ARR a ve skupině **Nastavení** na levé straně vyberte **Identita** . Ujistěte se, že **stav** je **zapnuto**.
![Ladicí program rámce Unity](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>Klient se nemůže připojit k serveru.

Ujistěte se, že brány firewall (na zařízení, ve směrovačích atd.) neblokují následující porty:

* **50051 (TCP)** – vyžaduje se pro počáteční připojení (http handshake)
* **8266 (TCP + UDP)** – vyžadováno pro přenos dat
* **5000 (TCP)**, **5433 (tcp)**, **8443 (TCP)** – požadováno pro [ArrInspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Chyba: `Disconnected: VideoFormatNotAvailable` ' '

Ověřte, že grafický procesor podporuje dekódování hardwarových videí. Viz [vývojový počítač](../overview/system-requirements.md#development-pc).

Pokud pracujete na přenosném počítači se dvěma grafickými procesory, je možné, že GPU, ve kterém pracujete, ve výchozím nastavení neposkytuje funkci dekódování hardwarových videí. Pokud ano, zkuste aplikaci vynutit, aby používala jiný GPU. To je často možné v nastavení ovladače GPU.

## <a name="h265-codec-not-available"></a>Kodek h265 není k dispozici.

Existují dva důvody, proč může server odmítat připojení s `codec not available` chybou.

**Kodek h265 není nainstalován:**

Nejdřív se ujistěte, že jste nainstalovali **rozšíření videa HEVC** , jak je uvedeno v části [software](../overview/system-requirements.md#software) v tématu požadavky na systém.

Pokud stále dochází k problémům, ujistěte se, že grafická karta podporuje h265 a máte nainstalovaný nejnovější ovladač grafiky. Informace o požadavcích specifických pro dodavatele najdete v části [vývojového počítače](../overview/system-requirements.md#development-pc) v tématu požadavky na systém.

**Kodek je nainstalován, ale nelze jej použít:**

Důvodem tohoto problému je nesprávné nastavení zabezpečení v knihovnách DLL. Tento problém se nemanifestuje při pokusu o sledování videí kódovaných pomocí h265. Opětovná instalace kodeku neopraví problém buď. Místo toho proveďte následující kroky:

1. Otevřete **PowerShell s právy správce** a spusťte

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Tento příkaz by měl výstup `InstallLocation` z kodeku, například:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Otevření této složky v Průzkumníkovi Windows
1. Měla by existovat podsložka **x86** a **x64** . Klikněte pravým tlačítkem na jednu ze složek a vyberte **vlastnosti** .
    1. Vyberte kartu **zabezpečení** a klikněte na tlačítko **Upřesnit** nastavení.
    1. Klikněte na **změnit** pro **vlastníka** .
    1. Zadejte do textového pole **Administrators** .
    1. Klikněte na **kontrolu názvů** a **OK** .
1. Opakujte výše uvedené kroky u druhé složky.
1. Také opakujte výše uvedené kroky v každém souboru DLL v obou složkách. Měly by být zcela čtyři knihovny DLL.

Pokud chcete ověřit, že jsou nastavení správná, udělejte to pro každou ze čtyř knihoven DLL:

1. Vyberte **vlastnosti > zabezpečení > upravit** .
1. Projděte si seznam všech **skupin nebo uživatelů** a ujistěte se, že každý z nich má nastavenou možnost **číst & spustit** vpravo (zaškrtnutí políčka ve sloupci **povoleno** musí být zaškrtne).

## <a name="low-video-quality"></a>Nízká kvalita videa

Kvalitu videa je možné ohrozit buď pomocí kvality sítě, nebo chybějícího H265ého kodeku videa.

* Podívejte se na postup pro [identifikaci problémů se sítí](#unstable-holograms).
* Podívejte se na [požadavky na systém](../overview/system-requirements.md#development-pc) pro instalaci nejnovějšího ovladače grafiky.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>Video zaznamenané v rámci MRC neodráží kvalitu živého prostředí.

Video se dá zaznamenat na HoloLens prostřednictvím aplikace [MRC (Mixed reality Capture)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers). Výsledné video má však horší kvalitu než živé prostředí ze dvou důvodů:
* Snímková frekvence videa se omezené na 30 Hz na rozdíl od 60 Hz.
* Obrázky videa neprojde krok zpracování [promítnout v pozdní fázi](../overview/features/late-stage-reprojection.md) , takže se video zdá být choppier.

Obě jsou základními omezeními techniky záznamu.

## <a name="black-screen-after-successful-model-loading"></a>Černá obrazovka po úspěšném načtení modelu

Pokud jste připojeni k modulu runtime vykreslování a úspěšně jste načetli model, ale po jeho zobrazení se zobrazí jenom černá obrazovka, může to mít několik různých příčin.

Než provedete podrobnější analýzu, doporučujeme, abyste provedli testování těchto věcí:

* Je kodek h265 nainstalován? I když by existovala záloha kodeku H264, zjistili jsme případy, kdy tato záloha nefungovala správně. Podívejte se na [požadavky na systém](../overview/system-requirements.md#development-pc) pro instalaci nejnovějšího ovladače grafiky.
* Při použití projektu Unity zavřete Unity, odstraňte dočasnou *knihovnu* a složky *obj* v adresáři projektu a znovu načtěte nebo sestavte projekt. V některých případech data uložená v mezipaměti způsobila, že ukázka nebude správně fungovat bez zjevné příčiny.

Pokud vám tyto dva kroky nepomohly, je nutné zjistit, zda jsou snímky videa přijaty klientem nebo ne. To se dá dotazovat programově, jak je vysvětleno v kapitole [dotazy na výkon na straně serveru](../overview/features/performance-queries.md) . `FrameStatistics struct`Má člena, který určuje, kolik snímků videa bylo přijato. Pokud je toto číslo větší než 0 a v průběhu času se zvyšuje čas, klient obdrží skutečné video snímky ze serveru. V důsledku toho musí být problém na straně klienta.

### <a name="common-client-side-issues"></a>Běžné problémy na straně klienta

**Model překračuje limity vybraného virtuálního počítače, konkrétně maximální počet mnohoúhelníků:**

Podívejte se na určitá [omezení velikosti virtuálních počítačů](../reference/limits.md#overall-number-of-polygons).

**Model není uvnitř frustum kamery:**

V mnoha případech se model zobrazuje správně, ale je umístěný mimo frustum kamery. Běžným důvodem je to, že model byl exportován s mnohem mimo špičku, takže ho ořízne daleko ořezovou rovinou kamery. Pomáhá při programovém dotazování ohraničujícího rámečku modelu a vizualizaci pole pomocí Unity jako pole řádku nebo vytištění jeho hodnot do protokolu ladění.

Kromě toho proces převodu generuje [výstupní soubor JSON](../how-tos/conversion/get-information.md) společně s převedeným modelem. Chcete-li ladit problémy s polohou modelu, Prohlédněte si `boundingBox` položku v [části outputStatistics](../how-tos/conversion/get-information.md#the-outputstatistics-section):

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

Ohraničovací rámeček je v metrech popsaný jako `min` a `max` pozice v prostorovém prostoru. Proto souřadnice 1000,0 znamená, že je 1 kilometer od počátku.

Existují dva problémy s tímto ohraničujícím polem, které vedou k neviditelné geometrii:
* **Pole může být mimo střed**, takže objekt je zcela oříznutý z důvodu nadstřihu roviny. `boundingBox`Hodnoty v tomto případě by vypadaly takto: `min = [-2000, -5,-5], max = [-1990, 5,5]` jako příklad použijte velký posun na ose x. Chcete-li tento typ problému vyřešit, povolte `recenterToOrigin` možnost v [konfiguraci převodu modelu](../how-tos/conversion/configure-model-conversion.md).
* **Pole se dá zarovnat na střed, ale jeho pořadí je moc velké**. To znamená, že i když fotoaparát začíná uprostřed modelu, jeho geometrie se v všech směrech ořízne. Typické `boundingBox` hodnoty v tomto případě by vypadaly takto: `min = [-1000,-1000,-1000], max = [1000,1000,1000]` . Důvodem pro tento typ problému je obvykle neshoda škálování jednotky. Chcete-li kompenzovat, určete [hodnotu škálování během převodu](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) nebo označte zdrojový model správnými jednotkami. Při načítání modelu za běhu je také možné použít škálování na kořenový uzel.

**Kanál vykreslování Unity neobsahuje zavěšení vykreslování:**

Vzdálené vykreslování Azure se zapojte do kanálu vykreslování Unity a provede kompozici snímků s videem a provede reprojekci. Chcete-li ověřit, zda tyto zavěšení existují, otevřete nabídku *:::no-loc text="Window > Analysis > Frame debugger":::* . Povolte ji a ujistěte se, že v kanálu existují dvě položky `HolographicRemotingCallbackPass` :

![Ladicí program rámce Unity](./media/troubleshoot-unity-pipeline.png)

## <a name="the-rendered-image-in-unity-is-upside-down"></a>Vykreslený obraz v Unity je souběžný

Nezapomeňte postupovat přesně podle [Průvodce nastavením projektu](../tutorials/unity/project-setup.md) . Obrázek na více stranách indikuje, že Unity je nutný k vytvoření cíle vykreslování mimo obrazovku. Toto chování se v současné době nepodporuje a na HoloLens 2 vytváří velký dopad na výkon.

Důvodem pro tento problém může být MSAA, HDR nebo povolení následného zpracování. Ujistěte se, že je vybraný profil nízké kvality a v Unity nastavte jako výchozí. Provedete to tak, že přejdete na *upravit > nastavení projektu... > kvalitu*.

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Kód Unity používající rozhraní API pro vzdálené vykreslování není zkompilován

### <a name="use-debug-when-compiling-for-unity-editor"></a>Při kompilaci pro Editor Unity použít ladění

Přepněte *typ sestavení* řešení Unity pro **ladění**. Při testování ARR v editoru Unity je tato definice `UNITY_EDITOR` dostupná pouze v sestavení ladění. Všimněte si, že se nevztahují k typu sestavení používanému pro [nasazené aplikace](../quickstarts/deploy-to-hololens.md), kde byste měli preferovat sestavení Release.

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Selhání kompilace při kompilování vzorků Unity pro HoloLens 2

Při pokusu o kompilaci ukázek Unity (rychlý Start, ShowCaseApp,..) pro HoloLens 2 jsme zjistili spurious chyby. Stížnosti sady Visual Studio nebudou moct kopírovat některé soubory, i když jsou tam. Pokud se tento problém bude opakovat:
* Odeberte všechny dočasné soubory Unity z projektu a zkuste to znovu. To znamená, že zavřete Unity, odstraňte dočasnou *knihovnu* a složky *obj* v adresáři projektu a znovu načtěte nebo sestavte projekt.
* Zajistěte, aby se projekty nacházely v adresáři na disku s dostatečně krátkým umístěním, protože se krok kopírování někdy zdá být v potížích s dlouhými názvy souborů.
* Pokud to nemůžete, může to být tím, že společnost Microsoft má vliv na krok kopírování. Chcete-li nastavit výjimku, spusťte tento příkaz registru z příkazového řádku (vyžaduje oprávnění správce):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
## <a name="unstable-holograms"></a>Nestabilní hologramy

V případě, že se vygenerované objekty budou pohybovat spolu s pohyby hlav, může docházet k potížím s fází LSR ( *opožděné reprojekce* ). Pokyny k tomu, jak se tyto situace týkají, najdete v části o [reprojekci v pozdní fázi](../overview/features/late-stage-reprojection.md) .

Dalším důvodem pro nestabilní hologramy (wobbling, reformace, kolísání nebo přechodové hologramy) může být špatné připojení k síti, zejména nedostatečná šířka pásma sítě nebo příliš vysoká latence. Dobrým indikátorem pro kvalitu síťového připojení je hodnota [statistiky výkonu](../overview/features/performance-queries.md) `ARRServiceStats.VideoFramesReused` . Znovu používané snímky označují situace, kdy se na straně klienta má znovu použít starý snímek videa, protože není k dispozici žádný nový snímek videa – například kvůli ztrátě paketů nebo kvůli variacím latence sítě. Pokud `ARRServiceStats.VideoFramesReused` je často větší než nula, znamená to, že dojde k potížím se sítí.

Další hodnota, kterou chcete prohledat `ARRServiceStats.LatencyPoseToReceiveAvg` , je. Mělo by se konzistentně nacházet pod 100 ms. Pokud vidíte vyšší hodnoty, znamená to, že jste připojení k datovému centru, které je příliš daleko.

Seznam možných rizik najdete v [pokynech k připojení k síti](../reference/network-requirements.md#guidelines-for-network-connectivity).

## <a name="z-fighting"></a>Z-boj

V případě, že šipka nabízí [funkce pro zmírnění boje proti chybám](../overview/features/z-fighting-mitigation.md), je možné, že se v rámci scény stále zobrazuje z-boje. Tato příručka se zaměřuje na řešení těchto zbývajících problémů.

### <a name="recommended-steps"></a>Doporučené kroky

Pomocí následujícího pracovního postupu můžete zmírnit řešení z boje proti:

1. Otestování scény s výchozími nastaveními ARR (omezení z hlediska boje proti hrozbám)

1. Zakázat zmírnění z boje proti riziku prostřednictvím [rozhraní API](../overview/features/z-fighting-mitigation.md) 

1. Změna kamery v blízkosti a daleko rovině na užší rozsah

1. Řešení potíží s scénou prostřednictvím další části

### <a name="investigating-remaining-z-fighting"></a>Prošetření zbývajících z-boje

Pokud byly výše uvedené kroky vyčerpány a zbývající z-boje je nepřijatelná, je nutné prozkoumat základní příčinu z-boje. Jak je uvedeno na [stránce funkce zmírňování proti](../overview/features/z-fighting-mitigation.md)útokům, existují dva hlavní důvody pro odboje: na konci rozsahu hloubky a oblasti, které se protínají při coplanarí. Ztrátová přesnost hloubky je matematickou a je možné ji zmírnit jenom pomocí kroku 3 výše. Coplanar povrchy označují chybu zdrojového prostředku a jsou ve zdrojových datech lépe opraveny.

Vlastnost ARR má funkci pro určení, zda by povrchy mohly z boje: [šachovnice zvýrazňování](../overview/features/z-fighting-mitigation.md). Můžete také určit vizuálně co způsobuje z-boje. Následující první animace ukazuje příklad ztráty hloubky ve vzdálenosti a druhá ukazuje příklad téměř coplanarch ploch:

![Hloubka a přesnost z-boje](./media/depth-precision-z-fighting.gif)  ![coplanar-z-boj](./media/coplanar-z-fighting.gif)

Tyto příklady porovnejte s vaším z-bojeem a určete příčinu nebo volitelně postupujte podle kroků tohoto podrobného pracovního postupu:

1. Umístěte kameru nad zařízení z-boje, aby vypadala přímo na povrchu.
1. Pomalu přesouváte kameru vpřed směrem od povrchu.
1. Pokud je z-boje viditelná celá doba, povrchy jsou dokonale coplanar. 
1. Pokud se z-boje zobrazuje většinou v čase, jsou povrchy téměř coplanar.
1. Je-li z-boje viditelné pouze z daleko od začátku, příčinou je nedostatečná přesnost hloubky.

Coplanar povrchy můžou mít řadu různých příčin:

* Objekt byl duplikován exportem aplikace z důvodu chyby nebo různých přístupů k pracovnímu postupu.

    Podívejte se na tyto problémy s příslušnou podporou aplikace a aplikace.

* Povrchy jsou duplikovány a převráceny, aby se v zobrazovacích zařízeních používaly při použití přední nebo zadní strany.

    Import prostřednictvím [převodu modelu](../how-tos/conversion/model-conversion.md) určuje hlavní sidedness modelu. Jako výchozí se předpokládá Double-sidedness. Povrch se vykreslí jako tenká stěna s fyzicky správným osvětlením z obou stran. Jeden-sidedness může být odvozen příznaky ve zdrojovém prostředku nebo explicitně vynucen během [konverze modelu](../how-tos/conversion/model-conversion.md). Kromě toho je možné, že [jeden jednostranný režim](../overview/features/single-sided-rendering.md) může být nastaven na hodnotu "normální".

* Objekty protínají ve zdrojových prostředcích.

     Objekty transformované způsobem, že se některé z jejich povrchů překrývají, vytváří i z boje proti. Tento problém může také vytvořit transformace částí stromu scény v importované scéně v rámci ARR.

* Povrchy jsou záměrně vytvořené tak, aby se dotkly, jako je decals nebo text na stěnách.



## <a name="next-steps"></a>Další kroky

* [Požadavky na systém](../overview/system-requirements.md)
* [Požadavky sítě](../reference/network-requirements.md)
