---
title: Vyzkoušejte hlasové služby zdarma
titleSuffix: Azure Cognitive Services
description: Začínáme se službou Speech je jednoduché a cenově dostupné. 30denní bezplatná zkušební verze vám umožní zjistit, co může služba provádět a rozhodnout, jestli je vhodná pro potřeby vaší aplikace.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 203526b23a9571f0cccf3ebed432978772282418
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226427"
---
# <a name="try-speech-services-for-free"></a>Vyzkoušejte hlasové služby zdarma

Začínáme se službou Speech je jednoduché a cenově dostupné. 30denní bezplatná zkušební verze vám umožní zjistit, co může služba provádět a rozhodnout, jestli je vhodná pro potřeby vaší aplikace.

Pokud potřebujete více času, zaregistrujte si účet Microsoft Azure – jde o s 200 USD v kredit služby, které můžete použít na placené předplatné hlasové služby po dobu až 30 dnů.

A konečně hlasových služeb nabízí na úrovni free, s nízkým objemem, který je vhodný pro vývoj aplikací. Abyste mohli tuto bezplatnou registraci i po vypršení platnosti vašeho kreditu služby.

## <a name="free-trial"></a>Bezplatná zkušební verze

30denní bezplatná zkušební verze poskytuje přístup k po omezenou dobu cenová úroveň standard.

Chcete-li zaregistrovat 30denní bezplatnou zkušební verzi:

1. Přejděte na [vyzkoušet služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

1. Vyberte **rozhraní API pro rozpoznávání řeči** kartu.

   ![Karta služby řeči](media/index/try-speech-api-free-trial1.png)

1. V části **hlasové služby**, vyberte **získat klíč rozhraní API** tlačítko.

   ![Klíč rozhraní API](media/index/try-speech-api-free-trial2.png)

1. Souhlas s podmínkami a z rozevírací nabídky vyberte národní prostředí.

   ![Souhlas s podmínkami](media/index/try-speech-api-free-trial3.png)

1. Přihlaste se pomocí účtu Microsoft, Facebook, LinkedIn nebo GitHub.

    Můžete se zaregistrovat si bezplatný účet Microsoft na [portálu účtů Microsoft](https://account.microsoft.com/account). Abyste mohli začít, klikněte na tlačítko **přihlásit se účtem Microsoft** a když se zobrazí výzva k přihlášení, klepněte na tlačítko **ho vytvořit.** Postupujte podle kroků k vytvoření a ověřte svůj nový účet Microsoft.

Po přihlášení k zkuste služby Cognitive Services, začne bezplatné zkušební verze. Zobrazená webová stránka obsahuje seznam všech služeb Azure Cognitive Services, pro které máte aktuálně zkušebních verzí předplatného. Vedle položky jsou uvedeny dva klíče předplatného **hlasové služby**. Ani jeden klíč můžete použít ve svých aplikacích.

> [!NOTE]
> Všechny bezplatných zkušebních verzí předplatného se v oblasti západní USA. Pokud provedete požadavky, je potřeba použít `westus` koncového bodu.

## <a name="new-azure-account"></a>Nový účet Azure

Nové účty Azure získáte kredit 200 USD, služby, který je k dispozici po dobu až 30 dnů. Tento kredit můžete podrobněji prozkoumat hlasové služby nebo začátek vývoje aplikace.

Si zaregistrovat nový účet Azure, přejděte [Azure registrační stránku](https://azure.microsoft.com/free/ai/), klikněte na tlačítko **začít zdarma** a vytvořit nový účet Azure pomocí účtu Microsoft.

Můžete se zaregistrovat si bezplatný účet Microsoft na [portálu účtů Microsoft](https://account.microsoft.com/account). Abyste mohli začít, klikněte na tlačítko **přihlásit se účtem Microsoft** a když se zobrazí výzva k přihlášení, klepněte na tlačítko **ho vytvořit.** Postupujte podle kroků k vytvoření a ověřte svůj nový účet Microsoft.

Po vytvoření účtu Azure, postupujte podle kroků v další části, kde začít předplatné hlasové služby.

## <a name="create-a-speech-resource-in-azure"></a>Vytvořit prostředek řeči v Azure

Chcete-li přidat prostředek hlasové služby (bezplatné nebo placené úrovně) ke svému účtu Azure:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) pomocí účtu Microsoft.

1. Vyberte **vytvořit prostředek** v levém horním rohu portálu.

    ![Vytvoření prostředku](media/index/try-speech-api-create-speech1.png)

1. V **nový** okna, vyhledejte **řeči**.

1. Ve výsledcích hledání vyberte **řeči**.

    ![Výběr řeči](media/index/try-speech-api-create-speech2.png)

1. V části **řeči**, vyberte **vytvořit** tlačítko.

    ![Vyberte tlačítko Vytvořit](media/index/try-speech-api-create-speech3.png)

1. V části **vytvořit**, zadejte:

   * Název pro nový prostředek. Název umožňuje rozlišit několik předplatných ve stejné službě.
   * Zvolte předplatné Azure, se nový prostředek je přidružený k určení, jak se účtují poplatky.
   * Zvolte [oblasti](regions.md) použití prostředku.
   * Zvolte buď bezplatné i placené cenové úrovně. Klikněte na tlačítko **zobrazit všechny podrobnosti o cenách** podrobnější informace o cenách a využití kvóty pro jednotlivé úrovně.
   * Vytvořit novou skupinu prostředků pro toto předplatné řeči nebo předplatné přiřadit existující skupinu prostředků. Pomáhají udržovat vaše různých předplatných Azure, které jsou uspořádány skupiny zdrojů.
   * Pohodlný přístup k vašemu předplatnému v budoucnu, vyberte **připnout na řídicí panel** zaškrtávací políčko.
   * Vyberte **vytvořit.**

     ![Vyberte tlačítko Vytvořit](media/index/try-speech-api-create-speech4.png)

     Trvá okamžiku vytvoření a nasazení vašeho nového prostředku řeči. Vyberte **rychlý Start** zobrazíte informace o nový prostředek.

     ![Rychlý start panel](media/index/try-speech-api-create-speech5.png)

1. V části **rychlý Start**, klikněte na tlačítko **klíče** odkaz v kroku 1, chcete-li zobrazit klíče předplatného. Každé předplatné má dva klíče; ani jeden klíč můžete použít ve vaší aplikaci. Klikněte na tlačítko vedle každého klíče ho zkopírovat do schránky pro vkládání do kódu.

> [!NOTE]
> Neomezený počet předplatných úrovně standard můžete vytvořit v jedné nebo víc oblastech. Můžete však vytvořit jenom jedno předplatné bezplatné úrovně. Nasazení modelu na úrovni free, která nepoužíval. po dobu 7 dní se vyřadil automaticky.

## <a name="switch-to-a-new-subscription"></a>Přepnout do nového předplatného

Přepínání z jednoho předplatného do jiného, například kdy vyprší platnost bezplatné zkušební verze nebo když publikujete aplikaci, nahraďte oblast a předplatné klíče ve vašem kódu oblast a předplatné klíč nového prostředku Azure.

> [!NOTE]
> Bezplatná zkušební verze klíče vytvořené v oblasti západní USA (`westus`) oblasti. Předplatné vytvořili prostřednictvím řídicího panelu Azure může být v některé oblasti, pokud se pro to rozhodnete.

* Pokud vaše aplikace používá [sadou SDK pro řeč](speech-sdk.md), zadejte kód oblasti, jako třeba `westus`, při vytváření konfigurace řeči.
* Pokud vaše aplikace používá jednu z hlasové služby [rozhraní REST API](rest-apis.md), oblast je součástí koncového bodu identifikátor URI, které používáte při zasílání požadavků.

Klíče vytvořené pro oblasti jsou platné pouze v dané oblasti. Pokus o použití s jinými oblastmi způsobí chyby ověřování.

## <a name="next-steps"></a>Další postup

Proveďte jeden z našich šablon rychlý start během 10 minut nebo projděte si naše ukázky SDK:

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Rozpoznávání řeči v C# ](quickstart-csharp-dotnet-windows.md) 
>  [ukázky sadou SDK pro řeč](speech-sdk.md#get-the-samples)
