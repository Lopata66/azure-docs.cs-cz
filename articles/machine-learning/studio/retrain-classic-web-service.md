---
title: Opětovné natrénování a nasazení klasické webové služby
titleSuffix: Azure Machine Learning Studio (classic)
description: Naučte se, jak přeškolit model a aktualizovat klasickou webovou službu tak, aby používala nově vyškolený model v Azure Machine Learning Studio (Classic).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: peterclu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: 477e099bb759e09402a245b693d95f3b3980699e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73480204"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>Přeučení a nasazení webové služby klasického studia (Classic)

Přeškolení modelů strojového učení je jedním ze způsobů, jak zajistit, aby byly přesné a na základě nejdůležitějších dostupných dat. V tomto článku se dozvíte, jak přeškolit webovou službu klasického studia (Classic). Návod, jak přeškolit novou webovou službu studia (Classic), najdete v [tomto článku s postupem.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že už máte experiment s přeškolením i prediktivní experiment. Tyto kroky jsou vysvětleny v tématu [přeučení a nasazení modelu strojového učení.](/azure/machine-learning/studio/retrain-machine-learning-model) Místo toho, abyste model strojového učení nasadili jako novou webovou službu, můžete prediktivní experiment nasadit jako klasickou webovou službu.
     
## <a name="add-a-new-endpoint"></a>Přidat nový koncový bod

Prediktivní webová služba, kterou jste nasadili, obsahuje výchozí hodnoticí koncový bod, který je synchronizovaný s původní výukou a vyhodnocování experimentů. Chcete-li aktualizovat webovou službu na nový vyškolený model, je nutné vytvořit nový koncový bod bodování.

Existují dva způsoby, jak můžete přidat nový koncový bod do webové služby:

* Prostřednictvím kódu programu
* Použití portálu webových služeb Azure

> [!NOTE]
> Ujistěte se, že přidáváte koncový bod do prediktivní webové služby, nikoli do webové služby školení. Pokud jste správně nasadili školení i prediktivní webovou službu, měli byste vidět ze seznamu dvě samostatné webové služby. Prediktivní webová služba by měla končit řetězcem "[prediktivního EXP.]".
>

### <a name="programmatically-add-an-endpoint"></a>Programové přidání koncového bodu

Můžete přidat koncové body bodování pomocí ukázkového kódu, který je k dispozici v tomto [úložišti GitHub](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Použití portálu webových služeb Azure k přidání koncového bodu

1. V Machine Learning Studio (Classic) v levém navigačním sloupci klikněte na webové služby.
1. V dolní části řídicího panelu webové služby klikněte na možnost **Spravovat koncové body verze Preview**.
1. Klikněte na tlačítko **Přidat**.
1. Zadejte název a popis nového koncového bodu. Vyberte úroveň protokolování a jestli jsou povolená ukázková data. Další informace o protokolování najdete v tématu [Povolení protokolování pro Machine Learning webové služby](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Aktualizace proučeného modelu přidaného koncového bodu

### <a name="retrieve-patch-url"></a>Načíst adresu URL opravy

### <a name="option-1-programmatically"></a>Možnost 1: programově

Správnou adresu URL opravy získáte pomocí následujících kroků:

1. Spusťte vzorový kód [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .
1. Z výstupu AddEndpoint Najděte hodnotu *HelpLocation* a zkopírujte adresu URL.

   ![HelpLocation ve výstupu ukázky addEndpoint.](./media/retrain-classic/addEndpoint-output.png)
1. Vložte adresu URL do prohlížeče a přejděte na stránku, která poskytuje odkazy na nápovědu pro webovou službu.
1. Kliknutím na odkaz **aktualizovat prostředek** otevřete stránku s popisem opravy.

### <a name="option-2-use-the-azure-machine-learning-web-services-portal"></a>Možnost 2: použití portálu Azure Machine Learning Web Services

Použijte následující postup, chcete-li získat správnou adresu URL opravy pomocí webového portálu:

1. Přihlaste se k portálu [Azure Machine Learning Web Services](https://services.azureml.net/) .
1. V horní části klikněte na **webové služby** nebo **klasické webové služby** .
1. Klikněte na webovou službu bodování, se kterou pracujete (Pokud jste nezměnili výchozí název webové služby, ukončí se "[bodování EXP.]").
1. Klikněte na **+ Nový**.
1. Po přidání koncového bodu klikněte na název koncového bodu.
1. V části Adresa URL **opravy** otevřete stránku s popisem aktualizace kliknutím na tlačítko **nápovědě k rozhraní API** .

> [!NOTE]
> Pokud jste přidali koncový bod do webové služby školení místo prediktivní webové služby, zobrazí se po kliknutí na odkaz **aktualizovat prostředek** následující chyba, ale tato funkce není v tomto kontextu podporována nebo není dostupná. Tato webová služba nemá žádné aktualizovatelné prostředky. Omlouváme se za nepříjemnosti a pracujeme na vylepšení tohoto pracovního postupu. "
>

Stránka s informacemi o opravě obsahuje adresu URL opravy, kterou je nutné použít a poskytuje vzorový kód, který můžete použít k jeho volání.

![Oprava URL](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Aktualizace koncového bodu

Nyní můžete použít trained model pro aktualizaci koncového bodu, který jste vytvořili dříve.

Následující vzorový kód ukazuje, jak pomocí adresy URL *BaseLocation*, *RelativeLocation*, *SasBlobToken*a patch aktualizovat koncový bod.

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
                    }
                }
            }
        };

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);

                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }

                // Do what you want with a successful response here.
            }
        }
    }

*ApiKey* a *endpointUrl* pro volání lze získat z řídicího panelu koncových bodů.

Hodnota parametru *Name* v *prostředcích* by měla odpovídat názvu prostředku uloženého výukového modelu v prediktivním experimentu. Získání názvu prostředku:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. V nabídce vlevo klikněte na **Machine Learning**.
1. V části název klikněte na pracovní prostor a pak klikněte na **webové služby**.
1. Pod položkou Název klikněte na položku **model sčítání [prediktivní EXP.]** .
1. Klikněte na nový koncový bod, který jste přidali.
1. Na řídicím panelu koncového bodu klikněte na **aktualizovat prostředek**.
1. Na stránce dokumentace k aktualizačnímu prostředku rozhraní API pro webovou službu můžete najít **název prostředku** v části **aktualizovatelné prostředky**.

Pokud váš token SAS vyprší před dokončením aktualizace koncového bodu, musíte provést příkaz GET s ID úlohy, abyste získali nový token.

Po úspěšném spuštění kódu by nový koncový bod měl začít používat převlakový model přibližně 30 sekund.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak spravovat webové služby nebo sledovat více experimentálních běhů, najdete v následujících článcích:

* [Prozkoumejte portál Web Services](manage-new-webservice.md)
* [Správa iterací experimentu](manage-experiment-iterations.md)