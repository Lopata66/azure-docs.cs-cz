---
title: Vytvoření Azure Functions na platformě Linux s použitím vlastní image
description: Naučte se vytvářet funkce služby Azure Functions běžící na vlastní imagi Linuxu.
ms.date: 03/30/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 65fa62e0e21eea44ebd1d0948e71ad1d552d4d68
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84345151"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Vytvoření funkce na platformě Linux pomocí vlastního kontejneru

V tomto kurzu vytvoříte a nasadíte kód, který se Azure Functions jako vlastní kontejner Docker pomocí základní image Linux. Vlastní image se obvykle používá, když vaše funkce vyžadují specifickou jazykovou verzi nebo mají konkrétní závislost nebo konfiguraci, která není poskytovaná integrovanou imagí.

Můžete také použít výchozí kontejner Azure App Service, jak je popsáno v tématu [Vytvoření první funkce hostované v systému Linux](functions-create-first-azure-function-azure-cli-linux.md). Podporované základní image pro Azure Functions najdete v [úložišti Azure Functions Base images](https://hub.docker.com/_/microsoft-azure-functions-base).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte aplikaci funkcí a souboru Dockerfile pomocí Azure Functions Core Tools.
> * Sestavit vlastní image pomocí Dockeru
> * Publikovat vlastní image do registru kontejneru
> * Vytvoření pomocných prostředků v Azure pro aplikaci Function App
> * Nasadit aplikaci Function App z Docker Hubu
> * Přidat do aplikace Function App nastavení aplikace
> * Povolte průběžné nasazování.
> * Povolte připojení SSH ke kontejneru.
> * Přidejte výstupní vazbu úložiště fronty. 

Můžete postupovat podle tohoto kurzu na jakémkoli počítači se systémem Windows, macOS nebo Linux. Dokončení kurzu vám bude účtovat v účtu Azure náklady na několik amerických dolarů.

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ [ID Docker](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Spusťte `docker login` , abyste se přihlásili k Docker. Tento příkaz se nepovede, pokud Docker není spuštěný, v takovém případě spusťte Docker a zkuste příkaz zopakovat.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Vytvoření a otestování projektu místní funkce

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
V terminálu nebo příkazovém řádku spusťte následující příkaz pro zvolený jazyk k vytvoření projektu Function App ve složce s názvem `LocalFunctionsProject` .  
::: zone-end  
::: zone pivot="programming-language-csharp"  
```
func init LocalFunctionsProject --worker-runtime dotnet --docker
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```
func init LocalFunctionsProject --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```
func init LocalFunctionsProject --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```
func init LocalFunctionsProject --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```
func init LocalFunctionsProject --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
Spuštěním následujícího příkazu v prázdné složce vygenerujte projekt Functions z [archetypu Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-Ddocker"
```
---

Maven vás vyzve k zadání hodnot potřebných k dokončení generování projektu při nasazení.   
Po zobrazení výzvy zadejte následující hodnoty:

| Výzva | Hodnota | Description |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Hodnota, která jednoznačně identifikuje váš projekt napříč všemi projekty, podle [pravidel pro pojmenovávání balíčků](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) pro Java. |
| **artifactId** | `fabrikam-functions` | Hodnota, která představuje název jar bez čísla verze. |
| **znění** | `1.0-SNAPSHOT` | Vyberte výchozí hodnotu. |
| **balíček** | `com.fabrikam.functions` | Hodnota, která je balíčkem Java pro vygenerovaný kód funkce. Použijte výchozí hodnotu. |

`Y`Potvrďte zadáním nebo stisknutím klávesy ENTER.

Maven vytvoří soubory projektu v nové složce s názvem _artifactId_, který je v tomto příkladu `fabrikam-functions` . 
::: zone-end
`--docker`Možnost generuje `Dockerfile` pro projekt, který definuje vhodný vlastní kontejner pro použití s Azure functions a vybraným modulem runtime.

Přejděte do složky projektu:
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Do projektu přidejte funkci pomocí následujícího příkazu, kde `--name` argument je jedinečný název vaší funkce a `--template` argument určuje Trigger funkce. `func new`Vytvořte podsložku, která odpovídá názvu funkce, který obsahuje soubor kódu, který je vhodný pro zvolený jazyk projektu, a konfiguračního souboru s názvem *Function. JSON*.

```
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end  
Pokud chcete funkci místně otestovat, spusťte místního hostitele modulu runtime Azure Functions v kořenovém adresáři složky projektu: 
::: zone pivot="programming-language-csharp"  
```
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
mvn clean package  
mvn azure-functions:run
```
::: zone-end
Jakmile se `HttpExample` ve výstupu zobrazí koncový bod, přejděte na `http://localhost:7071/api/HttpExample?name=Functions` . V prohlížeči by se měla zobrazit zpráva Hello, která vrací zpět `Functions` , hodnotu zadanou pro `name` parametr dotazu.

K zastavení hostitele použijte **kombinaci kláves CTRL +** - **C** .

## <a name="build-the-container-image-and-test-locally"></a>Sestavení image kontejneru a místní test

Volitelné Prověřte * souboru Dockerfile "v kořenovém adresáři složky projektu. Souboru Dockerfile popisuje požadované prostředí pro spuštění aplikace Function App v systému Linux.  Úplný seznam podporovaných základních imagí pro Azure Functions najdete na [stránce Azure Functions Base image](https://hub.docker.com/_/microsoft-azure-functions-base).
    
V kořenové složce projektu spusťte příkaz [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) a zadejte název, `azurefunctionsimage` , a značku `v1.0.0` . Položku `<DOCKER_ID>` nahraďte ID vašeho účtu Docker Hubu. Tento příkaz sestaví image Dockeru pro kontejner.

```
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Po dokončení příkazu můžete nový kontejner spustit místně.
    
K otestování sestavení spusťte image v místním kontejneru pomocí příkazu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , nahraďte znovu `<DOCKER_ID` číslem ID Docker a přidáním argumentu porty `-p 8080:80` :

```
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Po spuštění image v místním kontejneru otevřete prohlížeč `http://localhost:8080` , ve kterém se má zobrazit zástupný obrázek uvedený níže. V tomto okamžiku se zobrazí obrázek, protože funkce je spuštěná v místním kontejneru, stejně jako v Azure, což znamená, že je chráněná přístupovým klíčem definovaným v *Function. JSON* s `"authLevel": "function"` vlastností. Kontejner ještě není v Azure publikovaný do aplikace Function App, takže tento klíč ještě není dostupný. Pokud chcete testovat z místního kontejneru, zastavte Docker, změňte vlastnost Authorization na `"authLevel": "anonymous"` , znovu sestavte image a restartujte Docker. Pak resetujte `"authLevel": "function"` v *Function. JSON*. Další informace najdete v tématu [autorizační klíče](functions-bindings-http-webhook-trigger.md#authorization-keys).

![Zástupný obrázek označující, že je kontejner spuštěn místně](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
Jakmile je image spuštěná v místním kontejneru, přejděte na `http://localhost:8080/api/HttpExample?name=Functions` , která by měla zobrazit stejnou zprávu "Hello" jako předtím. Vzhledem k tomu, že Maven Archetype vygeneruje funkci aktivovanou protokolem HTTP, která používá anonymní autorizaci, můžete funkci volat i v případě, že je spuštěná v kontejneru. 
::: zone-end  

Po ověření aplikace Function App v kontejneru zastavte Docker pomocí **CTRL** + **C**.

## <a name="push-the-image-to-docker-hub"></a>Odeslat image do Docker Hub

Docker Hub je kontejner kontejneru, který je hostitelem imagí a poskytuje image a služby kontejneru. Pokud chcete sdílet image, která zahrnuje nasazení do Azure, je potřeba ji vložit do registru.

1. Pokud jste se ještě přihlásili k Docker, udělejte to pomocí příkazu [Docker Login](https://docs.docker.com/engine/reference/commandline/login/) , který nahradí `<docker_id>` ID Docker. Tento příkaz vás vyzve k zadání uživatelského jména a hesla. Zpráva o úspěšném přihlášení potvrzuje, že jste přihlášení.

    ```
    docker login
    ```
    
1. Až se přihlásíte, nahrajte image do Docker Hub pomocí příkazu [Docker push](https://docs.docker.com/engine/reference/commandline/push/) a znovu nahraďte `<docker_id>` ID Docker.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. V závislosti na rychlosti sítě se může při prvním vložení image trvat několik minut (vkládání dalších změn je mnohem rychlejší). Až budete čekat, můžete přejít k další části a vytvořit prostředky Azure v jiném terminálu.

## <a name="create-supporting-azure-resources-for-your-function"></a>Vytvoření podpory prostředků Azure pro vaši funkci

Pokud chcete kód funkce nasadit do Azure, musíte vytvořit tři prostředky:

- Skupina prostředků, což je logický kontejner pro související prostředky.
- Účet Azure Storage, který uchovává stav a další informace o vašich projektech.
- Aplikace Azure Functions, která poskytuje prostředí pro provádění kódu funkce. Aplikace Function App se mapuje na váš místní projekt funkce a umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

K vytvoření těchto položek použijete příkazy rozhraní příkazového řádku Azure. Každý příkaz poskytuje výstup JSON po dokončení.

1. Přihlaste se k Azure pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) :

    ```azurecli
    az login
    ```
    
1. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků s názvem `AzureFunctionsContainers-rg` v `westeurope` oblasti. (Obecně vytvoříte skupinu prostředků a prostředky v oblasti blízko vás pomocí dostupné oblasti z `az account list-locations` příkazu.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Nemůžete hostovat aplikace pro Linux a Windows ve stejné skupině prostředků. Pokud máte existující skupinu prostředků s názvem `AzureFunctionsContainers-rg` aplikace funkcí Windows nebo webové aplikace, musíte použít jinou skupinu prostředků.
    
1. Pomocí příkazu [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) vytvořte účet úložiště pro obecné účely ve vaší skupině prostředků a oblasti. V následujícím příkladu nahraďte `<storage_name>` globálně jedinečným názvem vhodným pro vás. Názvy musí obsahovat tři až 24 znaků a jenom malá písmena. `Standard_LRS`Určuje typický účet pro obecné účely.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Účet úložiště pro tento kurz vyhledá jenom několik centů za USD.
    
1. Pomocí příkazu můžete vytvořit plán Premium pro Azure Functions s názvem `myPremiumPlan` v oblasti **elastické Premium 1** – cenová úroveň ( `--sku EP1` ), v západní Evropa oblasti ( `-location westeurope` nebo použít vhodnou oblast poblíž) a v kontejneru Linux ( `--is-linux` ).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Hostování Linux pro kontejnery vlastních funkcí se podporuje na [vyhrazených plánech (App Service)](functions-scale.md#app-service-plan) a [plánech Premium](functions-premium-plan.md#features). Plán Premium používáme tady, který se může škálovat podle potřeby. Další informace o hostování najdete v [porovnání plánů hostování služby Azure Functions](functions-scale.md). Pokud chcete vypočítat náklady, přečtěte si [stránku s cenami funkcí](https://azure.microsoft.com/pricing/details/functions/).

    Příkaz taky zřídí přidruženou instanci služby Azure Application Insights ve stejné skupině prostředků, pomocí které můžete monitorovat aplikaci Function App a zobrazovat protokoly. Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md). Instance nenese žádné náklady, dokud ji neaktivujete.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Vytvoření a konfigurace aplikace Function App v Azure s imagí

Aplikace Function App v Azure spravuje spouštění vašich funkcí v plánu hostování. V této části použijete prostředky Azure z předchozí části k vytvoření aplikace Function App z image v Docker Hub a nakonfigurujete ji pomocí připojovacího řetězce, který Azure Storage.

1. Pomocí příkazu [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) vytvořte aplikaci Functions. V následujícím příkladu nahraďte `<storage_name>` názvem, který jste použili v předchozí části pro účet úložiště. Nahraďte také `<app_name>` globálně jedinečným názvem vhodným pro vás a `<docker_id>` s ID Docker.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    Parametr *Deployment-Container-image-Name* určuje obrázek, který má být použit pro aplikaci Function App. K zobrazení informací o imagi používané pro nasazení můžete použít příkaz [AZ functionapp config Container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) . K nasazení z jiné image můžete použít taky příkaz [AZ functionapp config Container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) .

1. Pomocí příkazu [AZ Storage Account show-Connection-String](/cli/azure/storage/account) načtěte připojovací řetězec pro účet úložiště, který jste vytvořili, a přiřaďte ho k proměnné prostředí `storageConnectionString` :

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Pomocí příkazu [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) přidejte toto nastavení do aplikace Function App. V následujícím příkazu nahraďte `<app_name>` názvem aplikace Function App a nahraďte `<connection_string>` připojovacím řetězcem z předchozího kroku (řetězec s dlouhým zakódovaným řetězcem začínající "DefaultEndpointProtocol ="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. Tato funkce teď může k účtu úložiště přistupovat pomocí tohoto připojovacího řetězce.

    > [!TIP]
    > V bash můžete použít proměnnou prostředí pro zachycení připojovacího řetězce místo použití schránky. Nejprve pomocí následujícího příkazu vytvořte proměnnou s připojovacím řetězcem:
    > 
    > ```bash
    > storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
    > ```
    > 
    > Potom v druhém příkazu použijte proměnnou:
    > 
    > ```azurecli
    > az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
    > ```

> [!NOTE]    
> Pokud publikujete vlastní image na účet privátního kontejneru, měli byste místo toho použít proměnné prostředí v souboru Dockerfile pro připojovací řetězec. Další informace najdete v [pokynech ENV](https://docs.docker.com/engine/reference/builder/#env). Měli byste také nastavit proměnné `DOCKER_REGISTRY_SERVER_USERNAME` a `DOCKER_REGISTRY_SERVER_PASSWORD` . Pokud chcete použít hodnoty, musíte znovu sestavit image, nasdílet image do registru a pak aplikaci Function App znovu spustit v Azure.

## <a name="verify-your-functions-on-azure"></a>Ověření funkcí v Azure

S imagí nasazenými do aplikace Function App v Azure teď můžete funkci vyvolat pomocí požadavků HTTP. Vzhledem k tomu, že definice *Function. JSON* zahrnuje vlastnost `"authLevel": "function"` , musíte nejdřív získat přístupový klíč (označovaný také jako "klíč funkce") a zahrnout ho jako parametr URL do všech požadavků na koncový bod.

1. Načtěte adresu URL funkce s klíčem Access (Function) pomocí Azure Portal, nebo pomocí příkazového řádku Azure CLI pomocí `az rest` příkazu.)

    # <a name="portal"></a>[Azure Portal](#tab/portal)

    1. Přihlaste se k Azure Portal, vyhledejte a vyberte **Function App**.

    1. Vyberte funkci, kterou chcete ověřit.

    1. V levém navigačním panelu vyberte **funkce**a potom vyberte funkci, kterou chcete ověřit.

        ![Příkaz Get URL funkce na Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-select-function.png)   

    
    1. Vyberte **získat adresu URL funkce**.

        ![Příkaz Get URL funkce na Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-function-url.png)   

    
    1. V automaticky otevíraném okně vyberte **výchozí (klíč funkce)** a potom zkopírujte adresu URL do schránky. Klíč je řetězec znaků, který následuje po `?code=` .

        ![Příkaz Get URL funkce na Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-copy-url.png)   


    > [!NOTE]  
    > Vzhledem k tomu, že je vaše aplikace Function App nasazena jako kontejner, nemůžete na portálu provádět změny kódu funkce. Místo toho je nutné projekt aktualizovat v místní imagi, znovu nahrajte image do registru a pak znovu nasadit do Azure. Průběžné nasazování můžete nastavit v pozdější části.
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

    1. Vytvořte řetězec adresy URL v následujícím formátu, nahraďte `<subscription_id>` , `<resource_group>` a `<app_name>` pomocí ID předplatného Azure, skupiny prostředků vaší aplikace Function App a názvu vaší aplikace Function App (v uvedeném pořadí):

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Adresa URL může například vypadat jako následující adresa:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Pro pohodlí můžete místo toho přiřadit adresu URL proměnné prostředí a použít ji v `az rest` příkazu.
    
    1. Spusťte následující `az rest` příkaz (k dispozici ve verzi Azure CLI 2.0.77 a novější) nahraďte `<uri>` řetězcem URI z posledního kroku, včetně uvozovek:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. Výstupem příkazu je klíč funkce. Úplná adresa URL funkce je pak `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>` náhrada `<app_name>` , `<function_name>` a `<key>` s konkrétními hodnotami.
    
        > [!NOTE]
        > Klíč, který se načte tady, je *hostitelský* klíč, který funguje pro všechny funkce aplikace Functions. Metoda zobrazená na portálu načítá klíč pouze pro jednu funkci.

    ---

1. Vložte adresu URL funkce do adresního řádku prohlížeče a přidejte parametr `&name=Azure` na konec této adresy URL. V prohlížeči by se měl objevit text, třeba Hello, Azure.

    ![Odezva funkce v prohlížeči.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Chcete-li otestovat autorizaci, odeberte `code=` z adresy URL parametr a ověřte, že funkce nezíská žádnou odpověď.


## <a name="enable-continuous-deployment-to-azure"></a>Povolení průběžného nasazování do Azure

Můžete povolit, aby Azure Functions automaticky aktualizovala nasazení image vždy, když aktualizujete image v registru.

1. Umožněte průběžné nasazování pomocí příkazu [AZ functionapp Deployment Container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) a nahraďte `<app_name>` názvem vaší aplikace Function App:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Tento příkaz umožňuje průběžné nasazování a vrací adresu URL Webhooku nasazení. (Tuto adresu URL můžete v pozdější době načíst pomocí příkazu [AZ functionapp Deployment Container show-CD-URL](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) .)

1. Zkopírujte adresu URL Webhooku nasazení do schránky.

1. Otevřete okno [Docker Hub](https://hub.docker.com/), přihlaste se a v navigačním panelu vyberte **úložiště** . Najděte a vyberte obrázek, vyberte kartu **Webhooky** , zadejte **název Webhooku**, vložte adresu URL do pole **Webhook URL**a pak vyberte **vytvořit**:

    ![Přidání Webhooku do úložiště Dockerhubu](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Při nastavení Webhooku Azure Functions znovu nasazovat image, kdykoli ji aktualizujete v Docker Hub.

## <a name="enable-ssh-connections"></a>Povolit připojení SSH

SSH umožňuje zabezpečenou komunikaci mezi kontejnerem a klientem. Když je povolený SSH, můžete se k kontejneru připojit pomocí nástrojů App Service Advanced Tools (Kudu). Aby se usnadnilo připojení ke kontejneru pomocí SSH, Azure Functions poskytuje základní image, která má již povolený SSH. Potřebujete jenom upravit souboru Dockerfile, pak image znovu sestavit a znovu nasadit. Pak se můžete připojit k kontejneru pomocí pokročilých nástrojů (Kudu).

1. V souboru Dockerfile přidejte řetězec `-appservice` k základní imagi ve vaší `FROM` instrukci:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk-appservice AS installer-env
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    Rozdíly mezi základními bitovými kopiemi jsou popsány v [kurzu App Services-vlastní image Docker](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

1. Znovu sestavte bitovou kopii pomocí `docker build` příkazu znovu a nahraďte `<docker_id>` ID Docker:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Nahrajte aktualizovanou bitovou kopii do dokovacího centra, které by mělo trvat mnohem méně času než první nabízená oznámení. je potřeba nahrát jenom aktualizované segmenty image.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions automaticky znovu nasadí image do vaší aplikace Functions. proces probíhá za méně než minutu.

1. V prohlížeči otevřete `https://<app_name>.scm.azurewebsites.net/` a nahraďte `<app_name>` jedinečným názvem. Tato adresa URL je koncový bod pokročilých nástrojů (Kudu) pro váš kontejner aplikace Function App.

1. Přihlaste se ke svému účtu Azure a pak vyberte **SSH** , aby se navázalo připojení ke kontejneru. Pokud Azure stále aktualizuje image kontejneru, může chvíli trvat, než se připojení.

1. Po navázání připojení s vaším kontejnerem spusťte `top` příkaz pro zobrazení aktuálně spuštěných procesů. 

    ![Horní příkaz pro Linux běžící v relaci SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Zapsat do fronty Azure Storage

Azure Functions umožňuje připojit vaše funkce k dalším službám a prostředkům Azure, aniž byste museli psát vlastní kód pro integraci. Tyto *vazby*, které představují vstupní i výstupní, jsou deklarovány v rámci definice funkce. Data z vazeb jsou k dispozici funkci jako parametry. *Trigger* je speciální typ vstupní vazby. I když má funkce pouze jednu Trigger, může mít více vstupních a výstupních vazeb. Další informace najdete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md).

V této části se dozvíte, jak integrovat funkci do fronty Azure Storage. Výstupní vazba, kterou přidáte do této funkce, zapisuje data z požadavku HTTP do zprávy ve frontě.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

## <a name="add-code-to-use-the-output-binding"></a>Přidat kód pro použití výstupní vazby

Je-li definována vazba fronty, můžete nyní aktualizovat funkci tak, aby přijímala `msg` výstupní parametr, a zapisovat zprávy do fronty.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

### <a name="update-the-image-in-the-registry"></a>Aktualizace image v registru

1. V kořenové složce spusťte `docker build` znovu a tentokrát aktualizujte verzi značky na `v1.0.1` . Stejně jako dřív nahraďte `<docker_id>` ID vašeho účtu Docker Hub:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. Nahrajte aktualizovaný obrázek zpátky do úložiště pomocí `docker push` :

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Vzhledem k tomu, že jste nakonfigurovali průběžné doručování, aktualizace image v registru znovu automaticky aktualizuje aplikaci Function App v Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Zobrazení zprávy ve frontě Azure Storage

V prohlížeči použijte stejnou adresu URL jako před voláním funkce. V prohlížeči by se měla zobrazit stejná odpověď jako předtím, protože jste tuto část kódu funkce nezměnili. Přidaný kód však zapsal zprávu pomocí `name` parametru URL do `outqueue` fronty úložiště.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v práci s funkcí Azure pomocí prostředků, které jste vytvořili v tomto kurzu, můžete ponechat všechny tyto prostředky na místě. Vzhledem k tomu, že jste pro Azure Functions vytvořili plán Premium, za průběžné náklady se vám bude účtovat jedna nebo dvě USD za den.

Abyste se vyhnuli průběžným nákladům, odstraňte `AzureFunctionsContainer-rg` skupinu prostředků, abyste mohli vyčistit všechny prostředky v této skupině: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Další kroky

+ [Funkce monitorování](functions-monitoring.md)
+ [Možnosti škálování a hostování](functions-scale.md)
+ [Hostování bez serveru založené na Kubernetes](functions-kubernetes-keda.md)
