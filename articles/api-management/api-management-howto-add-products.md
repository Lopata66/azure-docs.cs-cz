---
title: Vytvoření a publikování produktu ve službě Azure API Management
description: Zjistěte, jak vytvářet a publikovat produkty ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
origin.date: 08/10/2018
ms.date: 12/03/2018
ms.author: apimpm
ms.openlocfilehash: 0f2b45685d2976c567c16666e2ca89d334914b63
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112525"
---
# <a name="create-and-publish-a-product"></a>Vytvoření a publikování produktu  

Ve službě Azure API Management obsahuje produkt jedno nebo více rozhraní API a také kvótu využití a podmínky použití. Jakmile je projekt publikovaný, vývojáři se můžou přihlásit k jeho odběru a začít používat jeho rozhraní API.  

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření a publikování produktu
> * Přidání rozhraní API do produktu

![Kurz přidání produktu](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>Požadavky

+ Seznamte se s [terminologií služby Azure API Management](api-management-terminology.md).
+ Projděte si následující rychlý start: [Vytvoření instance Azure API Management](get-started-create-service-instance.md).
+ Také dokončení následujícího kurzu: [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Vytvoření a publikování produktu

![Přidání produktu](media/api-management-howto-add-products/02-create-publish-product-01.png)

1. Kliknutím na **Produkty** v nabídce na levé straně zobrazte stránku **Produkty**.
2. Klikněte na tlačítko **+ Přidat**.

    Při přidávání produktu je potřeba zadat následující informace: 

    | Název                     | Popis                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Zobrazované jméno             | Název, který se má zobrazit na **portálu pro vývojáře**.                                                                                                                                                                                                                                                        |
    | Název                     | Popisný název produktu.                                                                                                                                                                                                                                                                                      |
    | Popis              | Do pole **Popis** můžete zadat podrobné informace o produktu, například jeho účel, rozhraní API, ke kterým poskytuje přístup, a další užitečné informace.                                                                                                                                               |
    | Stav                    | Pokud chcete produkt publikovat, stiskněte **Publikováno**. Předtím, než bude možné volat rozhraní API v produktu, je nutné produkt publikovat. Ve výchozím nastavení jsou nové produkty nepublikované a viditelné pouze pro skupinu **Správci**.                                                                                      |
    | Vyžaduje předplatné    | Zaškrtněte políčko **Vyžadovat předplatné**, pokud se k používání produktu vyžaduje přihlášení uživatele k odběru.                                                                                                                                                                                                                                   |
    | Vyžaduje schválení        | Zaškrtněte políčko **Vyžadovat schválení**, pokud chcete, aby pokusy o přihlášení k odběru produktu kontroloval a následně přijímal nebo odmítal správce. Pokud toto políčko není zaškrtnuté, pokusy o přihlášení k odběru se automaticky schvalují.                                                                                                                         |
    | Omezení počtu předplatných | Pokud chcete omezit počet více souběžných předplatných, zadejte omezení předplatných.                                                                                                                                                                                                                                |
    | Právní podmínky              | Pro produkt můžete zahrnout podmínky použití, které musí předplatitelé přijmout, aby mohli produkt využívat.                                                                                                                                                                                                             |
    | Rozhraní API                     | Produkty jsou sdruženími jednoho nebo více rozhraní API. Můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. <br/> Při vytváření produktu můžete přidat existující rozhraní API. Rozhraní API můžete do produktu přidat i později, a to na stránce **Nastavení** produktu nebo při vytváření rozhraní API. |

3. Kliknutím na **Vytvořit** vytvořte nový produkt.

### <a name="add-more-configurations"></a>Přidání více konfigurací

Po uložení produktu můžete pokračovat v jeho konfiguraci zvolením karty **Nastavení**. 

Na kartě **Předplatná** můžete zobrazit předplatitele produktu nebo je přidat.

Na kartě **Řízení přístupu** můžete nastavit viditelnost produktu pro vývojáře nebo hosty.

## <a name="add-apis"> </a>Přidání rozhraní API do produktu

Produkty jsou sdruženími jednoho nebo více rozhraní API. Můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Při vytváření produktu můžete přidat existující rozhraní API. Rozhraní API můžete do produktu přidat i později, a to na stránce **Nastavení** produktu nebo při vytváření rozhraní API.

Vývojáři se nejprve musí přihlásit k odběru produktu, a teprve pak získají přístup k rozhraní API. Po přihlášení k odběru získají klíč předplatného, který je možné použít pro jakékoli rozhraní API v příslušném produktu. Pokud jste vytvořili instanci služby APIM, již jste správcem, takže jste ve výchozím nastavení přihlášeni k odběru všech produktů.

### <a name="add-an-api-to-an-existing-product"></a>Přidání rozhraní API do existujícího produktu

![přidání rozhraní API produktu](media/api-management-howto-add-products/02-create-publish-product-02.png)

1. Na kartě **Produkty** vyberte produkt.
2. Přejděte na kartu **Rozhraní API**.
3. Klikněte na tlačítko **+ Přidat**.
4. Zvolte rozhraní API a klikněte na **Vybrat**.

> [!TIP]
> Uživatelské předplatné *produktu* můžete vytvořit nebo aktualizovat s použitím vlastních klíčů předplatného [prostřednictvím rozhraní REST API](https://docs.microsoft.com/rest/api/apimanagement/subscription/createorupdate) nebo pomocí příkazu PowerShellu.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření a publikování produktu
> * Přidání rozhraní API do produktu

Přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Vytvoření prázdného rozhraní API a napodobení odpovědí rozhraní API](mock-api-responses.md)
