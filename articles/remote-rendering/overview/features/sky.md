---
title: Odrazy oblohy
description: Popisuje, jak nastavit mapy prostředí pro reflektore nebe.
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: be3dc2b113cb21c2dfb54a29e7f426e0d925c6d9
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759111"
---
# <a name="sky-reflections"></a>Odrazy oblohy

Ve vzdáleném vykreslování Azure se k světlování objektů používá nebeská textura. Pro rozšířené aplikace reality by tato textura měla vypadat jako v reálném okolí, aby se objekty zobrazovaly jako přesvědčivé. Tento článek popisuje, jak změnit texturu nebe.

> [!NOTE]
> Textura nebe se také označuje jako *Mapa prostředí*. Tyto výrazy se používají zaměnitelné.

## <a name="object-lighting"></a>Osvětlení objektů

Vzdálené vykreslování Azure využívá pro realistické výpočty světla *fyzicky založené vykreslování* (PBR). I když můžete do scény přidat [světlé zdroje](lights.md) , s využitím dobré textury nebe má největší dopad.

Následující obrázky znázorňují výsledky osvětlení různých povrchů pouze s texturou nebe:

| Hrubá  | 0                                        | 0,25                                          | 0,5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Jiný než kov  | ![Dielectric0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| Metal      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

Další informace o modelu osvětlení naleznete v kapitole [materiály](../../concepts/materials.md) .

> [!IMPORTANT]
> Vzdálené vykreslování Azure používá texturu nebe jenom pro modely osvětlení. Nevykresluje nebe jako pozadí, protože rozšíření aplikace pro rozšířené reality již mají správné pozadí – reálný svět.

## <a name="changing-the-sky-texture"></a>Změna textury nebe

Pokud chcete změnit mapu prostředí, stačí, když budete potřebovat [Načíst texturu](../../concepts/textures.md) a změnit relaci `SkyReflectionSettings` :

```cs
LoadTextureAsync _skyTextureLoad = null;
void ChangeEnvironmentMap(AzureSession session)
{
    _skyTextureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap));

    _skyTextureLoad.Completed += (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                try
                {
                    session.Actions.SkyReflectionSettings.SkyReflectionTexture = res.Result;
                }
                catch (RRException exception)
                {
                    System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
                }
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
        };
}
```

```cpp
void ChangeEnvironmentMap(ApiHandle<AzureSession> session)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUrl = "builtin://VeniceSunset";
    ApiHandle<LoadTextureAsync> skyTextureLoad = *session->Actions()->LoadTextureFromSASAsync(params);

    skyTextureLoad->Completed([&](ApiHandle<LoadTextureAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            ApiHandle<SkyReflectionSettings> settings = *session->Actions()->SkyReflectionSettings();
            settings->SkyReflectionTexture(*res->Result());
        }
        else
        {
            printf("Texture loading failed!");
        }
    });
}

```

Všimněte si, že je `LoadTextureFromSASAsync` použita varianta, protože je načtena integrovaná textura. V případě načítání z [propojených úložišť objektů BLOB](../../how-tos/create-an-account.md#link-storage-accounts)použijte `LoadTextureAsync` variantu.

## <a name="sky-texture-types"></a>Typy textury nebe

Jako mapy prostředí můžete použít jak *[cubemaps](https://en.wikipedia.org/wiki/Cube_mapping)* , tak *2D textury* .

Všechny textury musí být v [podporovaném formátu textury](../../concepts/textures.md#supported-texture-formats). Nemusíte zadávat mipmapy pro textury nebe.

### <a name="cube-environment-maps"></a>Mapy prostředí krychle

Pro referenci je zde uveden nezabalený cubemap:

![Nezabalená cubemap](media/Cubemap-example.png)

Použijte `AzureSession.Actions.LoadTextureAsync` /  `LoadTextureFromSASAsync` `TextureType.CubeMap` k načtení cubemap textur.

### <a name="sphere-environment-maps"></a>Mapy prostředí sphere

Při použití 2D textury jako mapy prostředí musí být obrázek v [kulové souřadnicovém prostoru](https://en.wikipedia.org/wiki/Spherical_coordinate_system).

![Obrázek nebe v kulových souřadnicích](media/spheremap-example.png)

Použijte `AzureSession.Actions.LoadTextureAsync` `TextureType.Texture2D` k načtení map sféry prostředí.

## <a name="built-in-environment-maps"></a>Předdefinované mapy prostředí

Vzdálené vykreslování Azure poskytuje několik předdefinovaných map prostředí, které jsou vždycky dostupné. Všechna integrovaná mapování prostředí jsou cubemaps.

|Identifikátor                         | Popis                                              | Obrázek                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Spektrum pruhů světla, jasného základního osvětlení interiéru    | ![Přikoupit](media/autoshop.png)
|builtin://BoilerRoom               | Světlé světlo – nastavení, více indikátorů okna      | ![BoilerRoom](media/boiler-room.png)
|builtin://ColorfulStudio           | Proměnlivé barevné světla v případě středně světlého nastavení interiéru  | ![ColorfulStudio](media/colorful-studio.png)
|builtin://Hangar                   | Středně jasné světlé prostředí okolí                     | ![SmallHangar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Tmavé nastavení vnitřního doběhu s kontrastem v tmavém světle              | ![IndustrialPipeAndValve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Denní okolní místnost – světlá, světlá oblast okna     | ![Lebombo](media/lebombo.png)
|builtin://SataraNight              | Tmavě noční nebe a uzemnění s mnoha okolními kvadranty   | ![SataraNight](media/satara-night.png)
|builtin://SunnyVondelpark          | Světlé světlo a stínový kontrast                      | ![SunnyVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Jasný Nebeský světlo se středním osvětlením            | ![Syferfontein](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Středně proměnlivý Sun a barevný stín                         | ![TearsOfSteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Dusk večer pro lehké přístupu                    | ![VeniceSunset](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Světlé, Lush – zelená a bílá světla, ztlumená země | ![WhippleCreekRegionalPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Daytime s jasným okolním světlem                 | ![WinterRiver](media/winter-river.png)
|builtin://DefaultSky               | Stejné jako TearsOfSteelBridge                               | ![DefaultSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>Další kroky

* [Světla](../../overview/features/lights.md)
* [Materiály](../../concepts/materials.md)
* [Textury](../../concepts/textures.md)
* [Nástroj příkazového řádku TexConv](../../resources/tools/tex-conv.md)
