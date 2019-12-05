---
title: Co je vlastní hlas? – Speech Service
titleSuffix: Azure Cognitive Services
description: Vlastní hlas je sada online nástrojů, která umožňuje vytvořit rozpoznatelný hlas pro vaši značku. Vše, co vše trvá, je několik zvukových souborů a přidružených přepisů. Pomocí odkazů níže můžete začít vytvářet vlastní prostředí pro text v textu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 0e40ca55a9cd198e1b086362a2559ab2fc0ba95e
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805956"
---
# <a name="get-started-with-custom-voice"></a>Začínáme se službou Custom Voice

[Vlastní hlas](https://aka.ms/customvoice) je sada online nástrojů, která umožňuje vytvořit rozpoznatelný hlas pro vaši značku. Vše, co vše trvá, je několik zvukových souborů a přidružených přepisů. Pomocí odkazů níže můžete začít vytvářet vlastní prostředí pro převod textu na řeč.

## <a name="whats-in-custom-voice"></a>Co je ve vlastním hlasu?

Než začnete s vlastním hlasem, budete potřebovat účet Azure a předplatné služby Speech. Po vytvoření účtu můžete připravit vaše data, naučit a testovat vaše modely, hodnotit kvalitu hlasu a nakonec nasazovat svůj vlastní hlasový model.

Diagram níže popisuje kroky pro vytvoření vlastního hlasového modelu pomocí [vlastního hlasového portálu](https://aka.ms/customvoice). Další informace získáte pomocí odkazů.

![Vlastní diagram architektury hlasu](media/custom-voice/custom-voice-diagram.png)

1.  [Přihlaste se k odběru a vytvořte projekt](#set-up-your-azure-account) – vytvořte účet Azure a vytvořte předplatné služby Speech. Toto jednotné předplatné vám umožní přístup k převodu řeči na text, převod textu na řeč, rozpoznávání řeči a vlastní hlasový portál. Pak pomocí svého předplatného služby Speech vytvořte svůj první vlastní hlasový projekt.

2.  [Nahrajte data](how-to-custom-voice-create-voice.md#upload-your-datasets) – nahrajte data (zvuk a text) pomocí vlastního hlasového portálu nebo vlastního hlasového rozhraní API. Z portálu můžete prozkoumat a vyhodnotit jejich skóre a poměry hluku. Další informace najdete v tématu [Příprava dat pro vlastní hlas](how-to-custom-voice-prepare-data.md).

3.  [Využijte svoje data](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) a vytvořte si vlastní hlasový model pro převod textu na řeč. Model můžete vyškolit v různých jazycích. Po školení, testování modelu a pokud jste s výsledkem spokojeni, můžete model nasadit.

4.  [Nasazení modelu](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) – vytvořte si vlastní koncový bod pro hlasový model pro převod textu na řeč a použijte ho pro syntézu řeči ve svých produktech, nástrojích a aplikacích.

## <a name="custom-neural-voices"></a>Vlastní hlasy neuronové

Funkce pro přizpůsobení hlasu neuronové je aktuálně ve verzi Public Preview omezená na vybrané zákazníky. Vyplňte formulář této [aplikace](https://go.microsoft.com/fwlink/?linkid=2108737) a začněte.

> [!NOTE]
> V rámci snahy Microsoftu o návrh zodpovědného AI je naším záměrem chránit práva jednotlivců a společnosti a podporovat transparentní interakce v rámci lidských počítačů. Z tohoto důvodu není vlastní neuronové hlas všeobecně dostupný všem zákazníkům. Přístup k technologii můžete získat až po zkontrolování vašich aplikací a s potvrzením, že je budete používat v souladu s našimi principy etiky. Přečtěte si další informace o našich [procesech uzavírání aplikací](https://aka.ms/custom-neural-gating-overview).

## <a name="set-up-your-azure-account"></a>Nastavení účtu Azure

Aby bylo možné použít portál Custom Speech k vytvoření vlastního modelu, je nutné předplatné služby Speech Service. Podle těchto pokynů vytvořte v Azure předplatné služby Speech. Pokud nemáte účet Azure, můžete se zaregistrovat k novému.  

Po vytvoření účtu Azure a předplatného služby pro rozpoznávání řeči se budete muset přihlásit k vlastnímu hlasovému portálu a připojit své předplatné.

1. Získejte klíč předplatného služby Speech z Azure Portal.
2. Přihlaste se k [vlastnímu hlasovému portálu](https://aka.ms/custom-voice).
3. Vyberte své předplatné a vytvořte projekt řeči.
4. Pokud byste chtěli přepnout na jiné předplatné řeči, použijte ikonu ozubeného kola umístěnou v horním navigačním panelu.

> [!NOTE]
> Vlastní hlasová služba nepodporuje 30denní bezplatný zkušební klíč. Předtím, než budete moci službu používat, musíte mít v Azure vytvořený klíč F0 nebo S0.

## <a name="how-to-create-a-project"></a>Jak vytvořit projekt

Obsah, který je podobný datům, modelům, testům a koncovým bodům, je uspořádán do **projektů** ve vlastním hlasovém portálu. Každý projekt je specifický pro zemi nebo jazyk a pohlaví hlasu, který chcete vytvořit. Můžete například vytvořit projekt pro žena Voice pro chat roboty vašeho centra volání, který používá angličtinu v USA (EN-US).

Pokud chcete vytvořit svůj první projekt, vyberte kartu **Převod textu na řeč/vlastní hlas** a pak klikněte na **Nový projekt**. Postupujte podle pokynů průvodce a vytvořte projekt. Po vytvoření projektu se zobrazí čtyři karty: **data**, **školení**, **testování**a **nasazení**. Pomocí odkazů uvedených v [následujících krocích](#next-steps) se dozvíte, jak používat jednotlivé karty.

## <a name="next-steps"></a>Další kroky

- [Příprava vlastních hlasových dat](how-to-custom-voice-prepare-data.md)
- [Vytvoření vlastního hlasu](how-to-custom-voice-create-voice.md)
- [Příručka: záznam ukázek hlasu](record-custom-voice-samples.md)
