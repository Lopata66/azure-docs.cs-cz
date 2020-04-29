---
title: Rychlý Start Azure Media Player
description: Seznamte se se základními kroky pro nastavení Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: ac81832765f674e58ad6b3213238e9c68e04d2dc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81726463"
---
# <a name="azure-media-player-quickstart"></a>Rychlé zprovoznění Azure Media Playeru
Nastavení Azure Media Player je snadné. Získání základního přehrávání mediálního obsahu z vašeho účtu Azure Media Services trvá jenom několik minut. Tato část ukazuje základní kroky a nezachází do podrobností. Následující části vám poskytnou konkrétní informace o tom, jak nastavit a nakonfigurovat Azure Media Player.  Stačí přidat následující příkazy include do části `<head>` vašeho dokumentu:

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> Tuto `latest` verzi byste **neměli používat v** produkčním prostředí, protože se tato verze může změnit na vyžádání. Nahraďte `latest` verzí Azure Media Player; například nahraďte `latest` parametr `1.0.0`. Z [tohoto místa](azure-media-player-changelog.md)se dá dotazovat na Azure Media Player verze.

## <a name="use-the-video-element"></a>Použití prvku video

V dalším kroku jednoduše použijte `<video>` prvek, jako byste normálně, ale s dodatečným `data-setup` atributem, který obsahuje všechny možnosti. Tyto možnosti mohou zahrnovat libovolnou možnost Azure Media Services v platném objektu JSON.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Pokud nechcete použít automatické nastavení, můžete `data-setup` atribut vynechat a inicializovat prvek videa ručně.

```html
    var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            }
          }
    );
    myPlayer.src([{
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>Další kroky ##

- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)