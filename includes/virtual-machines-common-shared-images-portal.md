---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 729e757c69887bbdce324e2d8383c970995dc94a
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903672"
---
## <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

> [!NOTE]
> Pokud jste se zaregistrovali pro použití Galerie sdílených imagí v rámci verze Preview, může být nutné znovu zaregistrovat poskytovatele `Microsoft.Compute`. Otevřete [Cloud Shell](https://shell.azure.com/bash) a zadejte: `az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>Vytvoření galerie imagí

Galerie imagí je primární prostředek, který slouží k povolení sdílení obrázků. Povolenými znaky pro název galerie jsou velká a malá písmena, číslice, tečky a tečky. Název galerie nesmí obsahovat pomlčky.  Názvy galerií musí být v rámci vašeho předplatného jedinečné. 

Následující příklad vytvoří galerii s názvem *myGallery* ve skupině prostředků *myGalleryRG* .

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.
1. Do vyhledávacího pole použijte **galerii Shared Image** a v části výsledky vyberte **sdílená Galerie imagí** .
1. Na stránce **Galerie sdílených imagí** klikněte na **vytvořit**.
1. Vyberte správné předplatné.
1. V části **Skupina prostředků**vyberte **vytvořit novou** a jako název zadejte *myGalleryRG* .
1. Do pole **název**zadejte *myGallery* pro název galerie.
1. Pro **oblast**ponechte výchozí hodnotu.
1. Můžete zadat krátký popis galerie, jako je *Moje galerie obrázků pro testování.* a pak klikněte na tlačítko **zkontrolovat + vytvořit**.
1. Po úspěšném ověření vyberte **vytvořit**.
1. Po dokončení nasazení vyberte **Přejít k prostředku**.
   
## <a name="create-an-image-definition"></a>Vytvoření definice obrázku 

Definice obrázků vytvoří logické seskupení obrázků. Slouží ke správě informací o verzích imagí, které jsou v nich vytvořeny. Názvy definic obrázků mohou být tvořeny velkými a malými písmeny, číslicemi, tečkami, pomlčkami a tečkami. Další informace o hodnotách, které můžete zadat pro definici obrázku, najdete v tématu [definice imagí](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Vytvořte definici image galerie v galerii. V tomto příkladu má image galerie název *myImageDefinition*.

1. Na stránce pro novou galerii imagí vyberte v horní části stránky **Přidat novou definici obrázku** . 
1. Jako **název definice obrázku**zadejte *myImageDefinition*.
1. V části **operační systém**vyberte správnou možnost v závislosti na vašem ZDROJovém virtuálním počítači.
1. V případě **generování virtuálních počítačů**vyberte možnost na základě vašeho zdrojového virtuálního počítače. Ve většině případů to bude 1. *generace*. Další informace najdete v tématu [Podpora pro virtuální počítače 2. generace](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2).
1. V části **stav operačního systému**vyberte možnost v závislosti na vašem ZDROJovém virtuálním počítači. Další informace naleznete v tématu [generalizovaná a specializovaná](../articles/virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images).
1. Pro **vydavatele**zadejte *myPublisher*. 
1. Pro **nabídku**zadejte *myOffer*.
1. Jako **SKU**zadejte *mySKU*.
1. Po dokončení vyberte **zkontrolovat + vytvořit**.
1. Poté, co definice obrázku projde ověřením, vyberte **vytvořit**.
1. Po dokončení nasazení vyberte **Přejít k prostředku**.


## <a name="create-an-image-version"></a>Vytvoření verze image

Vytvoří verzi image ze spravované image. V tomto příkladu je verze image *1.0.0* a replikuje se do datových center *středozápadní USA* i *střed USA – jih* . Při výběru cílových oblastí pro replikaci nezapomeňte, že je také nutné zahrnout *zdrojovou* oblast jako cíl pro replikaci.

Povolené znaky pro verzi obrázku jsou čísla a tečky. Čísla musí být v rozsahu 32 celé číslo. Formát: *MajorVersion*. *Podverze.* *Oprava*.

Postup pro vytvoření verze image se mírně liší v závislosti na tom, jestli je zdrojem zobecněná Image nebo snímek specializovaného virtuálního počítače. 

### <a name="option-generalized"></a>Možnost: generalizovaná

1. Na stránce Definice obrázku vyberte **Přidat verzi** v horní části stránky.
1. V části **oblast**vyberte oblast, ve které je uložená spravovaná image. Verze image se musí vytvořit ve stejné oblasti jako spravovaná image, ze které se vytvářejí.
1. Jako **název**zadejte *1.0.0*. Název verze Image by měl následovat po *závažnosti*. *vedlejší*. *opravný* formát pomocí celých čísel. 
1. V části **zdrojová image**vyberte z rozevíracího seznamu zdrojovou spravovanou bitovou kopii.
1. V části **vyloučit z nejnovější**ponechte výchozí hodnotu *ne*.
1. V poli **Datum konce životnosti**vyberte datum z kalendáře, který je v budoucnu několik měsíců.
1. V části **replikace**ponechte **výchozí počet repliky hodnotu** 1. Je potřeba replikovat do zdrojové oblasti, proto nechte první repliku jako výchozí a pak vyberte druhou oblast repliky, která se má *východní USA*.
1. Až budete hotovi, vyberte **zkontrolovat + vytvořit**. Azure ověří konfiguraci.
1. Když verze image projde ověřením, vyberte **vytvořit**.
1. Po dokončení nasazení vyberte **Přejít k prostředku**.

Replikace obrázku do všech cílových oblastí může chvíli trvat.

### <a name="option-specialized"></a>Možnost: specializované

1. Na stránce Definice obrázku vyberte **Přidat verzi** v horní části stránky.
1. V **oblasti oblast**vyberte oblast, ve které je uložený snímek. Verze bitové kopie je třeba vytvořit ve stejné oblasti jako zdroj, ze kterého jsou vytvořeny.
1. Jako **název**zadejte *1.0.0*. Název verze Image by měl následovat po *závažnosti*. *vedlejší*. *opravný* formát pomocí celých čísel. 
1. V části **snímek disku s operačním systémem**vyberte z rozevíracího seznamu snímek ze zdrojového virtuálního počítače. Pokud má zdrojový virtuální počítač datový disk, který chcete zahrnout, vyberte v rozevíracím seznamu správné číslo **logické jednotky** a pak vyberte snímek datového disku pro **snímek datového disku**. 
1. V části **vyloučit z nejnovější**ponechte výchozí hodnotu *ne*.
1. V poli **Datum konce životnosti**vyberte datum z kalendáře, který je v budoucnu několik měsíců.
1. V části **replikace**ponechte **výchozí počet repliky hodnotu** 1. Je potřeba replikovat do zdrojové oblasti, proto nechte první repliku jako výchozí a pak vyberte druhou oblast repliky, která se má *východní USA*.
1. Až budete hotovi, vyberte **zkontrolovat + vytvořit**. Azure ověří konfiguraci.
1. Když verze image projde ověřením, vyberte **vytvořit**.
1. Po dokončení nasazení vyberte **Přejít k prostředku**.

## <a name="share-the-gallery"></a>Sdílení galerie

Doporučujeme sdílet přístup na úrovni Galerie imagí. Následující postup vás provede sdílením galerie, kterou jste právě vytvořili.

1. Otevřete [portál Azure](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků**. 
1. V seznamu skupin prostředků vyberte **myGalleryRG**. Otevře se okno pro vaši skupinu prostředků.
1. V nabídce na levé straně stránky **myGalleryRG** vyberte **řízení přístupu (IAM)** . 
1. V části **Přidat přiřazení role**vyberte **Přidat**. Otevře se podokno **Přidat přiřazení role** . 
1. V části **role**vyberte **Čtenář**.
1. V části **přiřadit přístup k**nechejte výchozí nastavení **uživatele, skupiny nebo instančního objektu služby Azure AD**.
1. V části **Vybrat**zadejte e-mailovou adresu osoby, kterou chcete pozvat.
1. Pokud je uživatel mimo vaši organizaci, zobrazí se mu zpráva **, že se uživateli pošle e-mail, který jim umožní spolupracovat s Microsoftem.** Vyberte uživatele s e-mailovou adresou a pak klikněte na **Uložit**.

Pokud je uživatel mimo vaši organizaci, zobrazí se mu e-mailová pozvánka k připojení k organizaci. Uživatel musí pozvánku přijmout, takže uvidí galerii a všechny definice a verze imagí v jejich seznamu prostředků.

