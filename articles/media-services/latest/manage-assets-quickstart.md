---
title: Použití portálu k nahrávání, kódování a streamování obsahu – Azure
description: V tomto rychlém startu se dozvíte, jak pomocí portálu nahrávat, kódovat a streamovat obsah pomocí Azure Media Services.
ms.topic: quickstart
ms.date: 06/08/2020
ms.openlocfilehash: 374e8a22bf8fdcb14101bffc26f9d1b27480ce88
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232470"
---
# <a name="quickstart-upload-encode-and-stream-content-with-portal"></a>Rychlý Start: nahrání, kódování a streamování obsahu pomocí portálu

V tomto rychlém startu se dozvíte, jak používat Azure Portal k nahrání, kódování a streamování obsahu pomocí Azure Media Services.

> [!NOTE]
> Nezapomeňte zkontrolovat: [omezení Azure Portal pro Media Services V3](frequently-asked-questions.md#what-are-the-azure-portal-limitations-for-media-services-v3).
  
## <a name="overview"></a>Přehled

* Pokud chcete začít spravovat, šifrovat, kódovat, analyzovat a streamovat mediální obsah v Azure, musíte vytvořit účet Media Services a nahrát vysoce kvalitní digitální mediální soubor do **assetu**. 
    
    > [!NOTE]
    > Pokud se vaše video dřív nahrálo na účet Media Services pomocí rozhraní Media Services V3 API nebo se obsah vygeneroval na základě živého výstupu, neuvidíte v Azure Portal tlačítka **kódování**, **Analýza**ani **šifrování** . K provedení těchto úloh použijte rozhraní API Media Services V3.

    Zkontrolujte následující: 

  * [Nahrávání do cloudu a úložiště](storage-account-concept.md)
  * [Koncept prostředků](assets-concept.md)
  * [Správa prostředků](manage-asset-concept.md)
* Po nahrání vysoce kvalitního digitálního mediálního souboru do assetu (vstupní Asset) ho můžete zpracovat (kódovat nebo analyzovat). Zpracovaný obsah přechází do jiného prostředku (výstupní prostředek). 
    * [Zakódovat](encoding-concept.md) nahraný soubor do formátů, které se dají přehrávat na nejrůznějších prohlížečích a zařízeních.
    * [Analyzujte](analyzing-video-audio-files-concept.md) nahraný soubor. 

        V současné době při použití Azure Portal můžete provést následující: vygenerujte soubory titulků TTML a WebVTT. Soubory v těchto formátech lze použít k zpřístupnění zvukových souborů a videosouborů lidem s postižením sluchu. Můžete také extrahovat klíčová slova z vašeho obsahu.

        Pro bohatou práci, která umožňuje extrahovat přehledy z vašich videosouborů a zvukových souborů, použijte přednastavení Media Services V3 (jak je popsáno v tématu [kurz: analýza videí pomocí Media Services V3](analyze-videos-tutorial-with-api.md)). <br/>Pokud potřebujete podrobnější přehled, použijte [video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) přímo.    
* Po zpracování obsahu můžete multimediální obsah doručovat přehrávačům klientů. Chcete-li zpřístupnit video výstupnímu prostředku pro klienty pro přehrávání, je nutné vytvořit **Lokátor streamování**. Při vytváření **lokátoru streamování**je potřeba zadat **zásady streamování**. **Zásady streamování** umožňují definovat protokoly streamování a možnosti šifrování (pokud existují) pro vaše **Lokátory streamování**.
    
    Zrevidujte

    * [Lokátory streamování](streaming-locators-concept.md)
    * [Zásady streamování](streaming-policy-concept.md)
    * [Balení a doručování](dynamic-packaging-overview.md)
    * [Filtry](filters-concept.md)
* Svůj obsah můžete chránit šifrováním pomocí standard AES (Advanced Encryption Standard) (AES-128) nebo/a kteréhokoliv ze tří hlavních systémů DRM: Microsoft PlayReady, Google Widevine a Apple FairPlay. [Šifrování obsahu pomocí Azure Portal](encrypt-content-quickstart.md) rychlý Start ukazuje, jak nakonfigurovat ochranu obsahu.
        
## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[Vytvoření účtu Media Services](create-account-howto.md#use-the-azure-portal)

## <a name="upload"></a>Nahrávání

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyhledejte účet Media Services a klikněte na něj.
1. Vyberte **assety (nové)**.
1. V horní části okna klikněte na **nahrát** . 
1. Přetáhněte nebo vyhledejte soubor, který chcete nahrát.

Pokud přejdete do okna assety, uvidíte, že se do seznamu přidal nový Asset:

![Nahrávání](./media/manage-assets-quickstart/upload.png)

## <a name="encode"></a>Kódování

1. Vyberte **assety (nové)**.
1. Vyberte nový Asset (přidaný v posledním kroku).
1. V horní části okna klikněte na **kódování** .

    Stisknutí tohoto tlačítka spustí úlohu kódování. Po úspěšném dokončení vygeneruje výstupní Asset, který obsahuje kódovaný obsah.

Pokud přejdete do okna assety, uvidíte, že se do seznamu přidal výstupní Asset:

![Kódování](./media/manage-assets-quickstart/encode.png)

## <a name="monitor-the-job-progress"></a>Sledování průběhu úlohy

Pokud chcete zobrazit stav úlohy, přejděte na **úlohy**. Úloha obvykle prochází následujícími stavy: naplánované, zařazeno do fronty, zpracování, dokončeno (konečný stav). Pokud během provádění úlohy dojde k chybě, přejde úloha do stavu Chyba.

![Stav](./media/manage-assets-quickstart/job-status.png)

## <a name="publish-and-stream"></a>Publikování a streamování

K publikování assetu teď musíte do assetu přidat Lokátor streamování.

### <a name="streaming-locator"></a>Lokátor streamování 

1. V části **Lokátor streamování** stiskněte **+ Přidat Lokátor streamování**.
    Tím se publikuje Asset a vygeneruje se adresy URL streamování.

    > [!NOTE]
    > Pokud chcete, aby byl datový proud zašifrovaný, je nutné vytvořit zásadu klíče obsahu a nastavit ji na lokátoru streamování. Podrobnosti najdete v tématu [šifrování obsahu pomocí Azure Portal](encrypt-content-quickstart.md).
1. V okně **Přidat Lokátor streamování** zvolíte jednu z předdefinovaných zásad streamování. Podrobné informace najdete v tématu [zásady streamování](streaming-policy-concept.md) .

    ![Lokátor streamování](./media/manage-assets-quickstart/streaming-locator.png)

Po publikování assetu ho můžete streamovat přímo na portálu. 

![Zobrazovaný](./media/manage-assets-quickstart/publish.png)

Nebo zkopírujte adresu URL streamování a použijte ji v klientském přehrávači.

> [!NOTE]
> Ujistěte se, že [koncový bod streamování](streaming-endpoint-concept.md) běží. Při prvním vytvoření účtu Media Service se vytvoří výchozí koncový bod streamování, který je v zastaveném stavu, takže ho budete muset spustit, než budete moct streamovat obsah.<br/>Fakturuje se vám jenom v případě, že je koncový bod streamování ve stavu spuštěno.

## <a name="cleanup-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu vyzkoušet ostatní rychlé starty, měli byste se přidržet k vytvořeným prostředkům. V opačném případě přejděte do Azure Portal, přejděte do skupin prostředků, vyberte skupinu prostředků, pod kterou jste spustili tento rychlý Start, a odstraňte všechny prostředky.

## <a name="next-steps"></a>Další kroky

[Použití portálu k šifrování obsahu](encrypt-content-quickstart.md)
