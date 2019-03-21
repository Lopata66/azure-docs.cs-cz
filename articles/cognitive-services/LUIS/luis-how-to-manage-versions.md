---
title: Správa verzí
titleSuffix: Language Understanding - Azure Cognitive Services
description: Verze umožňují sestavovat a publikovat různých modelů. Dobrým postupem je klonovat aktuální active vzor na jinou verzi aplikace před prováděním změn do modelu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 73f962cc8e0d4605c0c5f0bace79553033ebe082
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110898"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Verze můžete upravit a testování bez dopadu na pracovní nebo produkční aplikace

Verze umožňují sestavovat a publikovat různých modelů. Dobrým postupem je klonovat aktuální active vzor na jiný [verze](luis-concept-version.md) aplikace před prováděním změn do modelu. 

Pro práci s verzí, otevřete aplikaci tak, že vyberete jeho název na **Moje aplikace** stránce a pak vyberte **spravovat** v horním pruhu vyberte **verze** v levém navigačním panelu. 

Seznam verzí zobrazit, jaké verze jsou publikovány, kde jsou publikovány, a kterou verzi je aktuálně aktivní. 

[![Části Správa, verze stránky](./media/luis-how-to-manage-versions/versions-import.png "části Správa, verze stránky")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Klonovat verze

1. Vyberte verzi, které chcete naklonovat vyberte **klonování** z panelu nástrojů. 

2. V **klonování verze** dialogové okno, zadejte název pro novou verzi, jako je například "0.2".

   ![Dialogové okno verze klonování](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Verze ID může obsahovat pouze znaky, číslice nebo '.' a nesmí být delší než 10 znaků.
 
   Nová verze se zadaným názvem je vytvořen a nastavit jako aktivní verze.

## <a name="set-active-version"></a>Nastavit aktivní verzi

Ze seznamu vyberte verzi a potom vyberte **zkontrolujte aktivní** z panelu nástrojů. 

[![Části Správa, verze stránky, zkontrolujte verzi akce](./media/luis-how-to-manage-versions/versions-other.png "části Správa, verze stránky, zkontrolujte verzi akce")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Import verze

1. Vyberte **Import verze** z panelu nástrojů. 

2. V **Import nové verze** automaticky otevírané okno, zadejte název nové verze deset znaků. Stačí nastavit ID verze, pokud už existuje verze v souboru JSON v aplikaci.

    ![Správa části, stránka verze, import nové verze](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Jakmile dokončíte import verze, nová verze stane aktivní verze.

<a name = "export-version"></a>

## <a name="other-actions"></a>Další akce

* K **odstranit** verze, ze seznamu vyberte verzi a potom vyberte **odstranit** z panelu nástrojů. Vyberte **OK**. 
* K **přejmenovat** verze, ze seznamu vyberte verzi a potom vyberte **přejmenovat** z panelu nástrojů. Zadejte nový název a vyberte **provádí**. 
* K **exportovat** verze, ze seznamu vyberte verzi a potom vyberte **Export aplikace** z panelu nástrojů. Soubor se stáhne do místního počítače. 

