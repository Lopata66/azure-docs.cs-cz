---
title: Konfigurace hostovaného uživatelského rozhraní pro Vlastní vyhledávání Bingu | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Tento článek slouží ke konfiguraci a integraci hostovaného uživatelského rozhraní pro Vlastní vyhledávání Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: ae073e10331f07d9863da1d4ed97533f95b87c86
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405063"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurace prostředí hostovaného uživatelského rozhraní

Vlastní vyhledávání Bingu poskytuje hostované uživatelské rozhraní, které můžete snadno integrovat do webových stránek a webových aplikací jako fragment kódu JavaScriptu. Pomocí portálu Vlastní vyhledávání Bingu můžete nakonfigurovat možnosti rozložení, barvy a hledání uživatelského rozhraní.



## <a name="configure-the-custom-hosted-ui"></a>Konfigurace vlastního hostovaného uživatelského rozhraní

Pokud chcete nakonfigurovat hostované uživatelské rozhraní pro vaše webové aplikace, postupujte podle těchto kroků. Při provádění změn vám podokno na pravé straně poskytne náhled uživatelského rozhraní. Zobrazené výsledky hledání nejsou aktuálními výsledky pro vaši instanci.

1. Přihlaste se k [portálu](https://customsearch.ai)vlastní vyhledávání Bingu.  
  
2. Vyberte instanci Vlastní vyhledávání Bingu.

3. Klikněte na kartu **Hostované uživatelské rozhraní**.  
  
4. Vyberte rozložení.

    |  |  |
    |---------|---------|
    |Panel hledání a výsledky (výchozí)    | Zobrazí vyhledávací pole s výsledky hledání pod ním.         |
    |Pouze výsledky     | Zobrazí pouze výsledky hledání bez vyhledávacího pole. Při použití tohoto rozložení je nutné zadat vyhledávací dotaz (`&q=<query string>`). Přidejte parametr dotazu do adresy URL požadavku ve fragmentu kódu JavaScriptu nebo odkaz na koncový bod HTML.        |
    |Překryvný     | Poskytuje vyhledávací pole a zobrazuje výsledky hledání v klouzavém překrytí.        |
    
5. Vyberte barevný motiv. Barvy můžete přizpůsobit tak, aby odpovídaly vaší aplikaci, kliknutím na **Přizpůsobit motiv**. Chcete-li změnit barvu, buď zadejte hexadecimální hodnotu RGB barvy (například `#366eb8`), nebo klikněte na Náhled barvy.

   Můžete zobrazit náhled změn na pravé straně portálu. Kliknutím na **obnovit do výchozího nastavení** dojde k vrácení změn na výchozí barvy pro vybraný motiv.

   > [!NOTE]
   > Při volbě barev zvažte přístupnost.

6. V části **Další konfigurace**zadejte odpovídající hodnoty pro vaši aplikaci. Tato nastavení jsou volitelná. Pokud chcete zobrazit efekt použití nebo odebrání, přečtěte si v podokně náhledu na pravé straně. K dispozici jsou následující možnosti konfigurace:  

7. Zadejte klíč předplatného hledání nebo ho vyberte v rozevíracím seznamu. Rozevírací seznam se naplní klíči z předplatných vašeho účtu Azure. Viz [Cognitive Services účet rozhraní API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

8. Pokud jste povolili automatické návrhy, zadejte klíč předplatného pro automatické návrhy nebo ho vyberte z rozevíracího seznamu. Rozevírací seznam se naplní klíči z předplatných vašeho účtu Azure. Vlastní Automatický návrh vyžaduje konkrétní úroveň předplatného, viz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Využití vlastního uživatelského rozhraní

Pro využití hostovaného uživatelského rozhraní: 

- Zahrnutí skriptu do webové stránky  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- Nebo použijte následující adresu URL ve webovém prohlížeči.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Podle potřeby přidejte do adresy URL následující parametry dotazu. Informace o těchto parametrech naleznete v tématu [rozhraní API pro vlastní vyhledávání](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) reference.
  >
  > - q
  > - mkt
  > - bezpečné hledání
  > - setlang

  > [!IMPORTANT]
  > Stránka nemůže zobrazit vaše prohlášení o zásadách ochrany osobních údajů ani jiné oznámení a výrazy. Vhodnost pro vaše použití se může lišit.  

Další informace, včetně vlastního ID konfigurace, najdete v části **koncové body** na kartě **Výroba** .

## <a name="configuration-options"></a>Možnosti konfigurace

Chování hostovaného uživatelského rozhraní můžete nakonfigurovat kliknutím na **Další konfigurace**a zadáním hodnot. Tato nastavení jsou volitelná. Pokud chcete zobrazit efekt použití nebo odebrání, přečtěte si v podokně náhledu na pravé straně. 

### <a name="web-search-configurations"></a>Konfigurace hledání na webu

|  |  |
|---------|---------|
|Webové výsledky povoleny    | Určuje, zda je povoleno vyhledávání na webu (v horní části stránky se zobrazí karta web).        |
|Povolit automatické návrhy     | Určuje, jestli je povolený vlastní Automatický návrh (viz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) za další náklady).        |
|Webové výsledky na stránce    | Počet výsledků hledání na webu, které se mají zobrazit v čase (maximální počet je 50 výsledků na stránku).        |
|Titulek obrázku   | Určuje, zda se zobrazí obrázky s výsledky hledání.|


Pokud kliknete na **Zobrazit pokročilé konfigurace**, zobrazí se následující konfigurace:


|  | |
|---------|---------|
|Zvýrazňovat slova     | Určuje, zda jsou zobrazeny výsledky s vyhledávanými výrazy tučným písmem.         |
|Cíl propojení    |  Určuje, zda se webová stránka otevře na nové kartě prohlížeče (prázdné) nebo na stejné kartě prohlížeče (samo), když uživatel klikne na výsledek hledání.        |

### <a name="image-search-configurations"></a>Konfigurace hledání obrázků

| | |
|---------|---------|
|Výsledky obrázků povoleny     | Určuje, zda je povoleno vyhledávání obrázků (v horní části stránky se zobrazí karta obrázky).            |
|Výsledky obrázků na stránku     | Počet výsledků hledání obrázků, které se mají zobrazit v čase (maximální počet je 150 výsledků na stránku).          |

Pokud kliknete na **Zobrazit pokročilé konfigurace**, zobrazí se následující konfigurace.  
  
| | |
|---------|---------|
| Povolit filtry     | Přidá filtry, které může uživatel použít k filtrování imagí, které Bing vrátí. Uživatel může například filtrovat výsledky pouze pro animované soubory GIF.|

### <a name="video-search-configurations"></a>Konfigurace hledání videí

|  | |
|---------|---------|
|Výsledky videa povoleny     | Určuje, zda je povoleno vyhledávání videí (v horní části stránky se zobrazí karta videa).           |
|Výsledky videa na stránce   | Počet výsledků hledání videí, které se mají zobrazit v čase (maximální počet je 150 výsledků na stránku).        |

Pokud kliknete na **Zobrazit pokročilé konfigurace**, zobrazí se následující konfigurace.  
  
|  | |
|---------|---------|
|Povolit filtry    | Přidá filtry, které může uživatel použít k filtrování videí, které Bing vrátí. Uživatel může například filtrovat výsledky videí pomocí konkrétního řešení nebo videí zjištěných za posledních 24 hodin.          |

### <a name="miscellaneous-configurations"></a>Různé konfigurace


| |  |
|---------|---------|
|Název stránky   | Text zobrazený v oblasti nadpisu na stránce s výsledky hledání (ne pro rozložení překryvných oken)        |
|Motiv panelu nástrojů    | Určuje barvu pozadí oblasti nadpisu stránky výsledků hledání. |

Pokud kliknete na **Zobrazit pokročilé konfigurace**, zobrazí se následující konfigurace.  

|Sloupec1  |Column2  |
|---------|---------|
|Zástupný text pro text v poli hledání   | Text zobrazený v poli hledání před vstupem        |
|Adresa URL odkazu na název    |Cíl odkazu na název         |
|Adresa URL loga     | Obrázek zobrazený vedle názvu         |
|Favicon    | Ikona zobrazená v záhlaví prohlížeče          |

Následující konfigurace platí pouze v případě, že používáte hostované uživatelské rozhraní prostřednictvím koncového bodu jazyka HTML (neplatí, pokud používáte fragment kódu JavaScriptu).

- Název stránky
- Motiv panelu nástrojů
- Adresa URL odkazu na název
- Adresa URL loga
- Adresa URL Faviicon  

## <a name="next-steps"></a>Další postup

- [Zvýraznění textu pomocí dekoračních značek](./hit-highlighting.md)
- [Stránkování webových stránek](./page-webpages.md)
