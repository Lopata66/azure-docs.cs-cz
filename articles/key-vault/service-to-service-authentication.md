---
title: Ověřování služba-služba pro Azure Key Vault pomocí .NET
description: K ověření Azure Key Vault používání .NET použijte knihovnu Microsoft. Azure. Services. AppAuthentication.
keywords: místní přihlašovací údaje pro ověřování trezoru klíčů Azure Key
author: msmbaldwin
manager: rkarlin
services: key-vault
ms.author: mbaldwin
ms.date: 08/28/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: 6996a5965454dfd5997f0c0404e0c348c68b626f
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177471"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Ověřování služba-služba pro Azure Key Vault pomocí .NET

K ověření pro Azure Key Vault potřebujete přihlašovací údaje Azure Active Directory (Azure AD), buď sdílený tajný klíč, nebo certifikát.

Správa takových přihlašovacích údajů může být obtížná. Setrvání přihlašovacích údajů do aplikace na základě jejich zahrnutí do zdrojového nebo konfiguračního souboru. Tento problém usnadňuje `Microsoft.Azure.Services.AppAuthentication` pro knihovnu .NET. Používá pověření vývojáře k ověřování během místního vývoje. Když je řešení později nasazené do Azure, knihovna se automaticky přepne na přihlašovací údaje aplikace. Použití přihlašovacích údajů pro vývojáře během místního vývoje je bezpečnější, protože nemusíte vytvářet přihlašovací údaje Azure AD ani sdílet přihlašovací údaje mezi vývojáři.

Knihovna `Microsoft.Azure.Services.AppAuthentication` spravuje ověřování automaticky, což zase umožňuje soustředit se na vaše řešení, nikoli na vaše přihlašovací údaje. Podporuje místní vývoj pomocí Microsoft Visual Studio, Azure CLI nebo integrovaného ověřování Azure AD. Při nasazení do prostředku Azure, který podporuje spravovanou identitu, knihovna automaticky používá [spravované identity pro prostředky Azure](../active-directory/msi-overview.md). Nejsou vyžadovány žádné změny kódu nebo konfigurace. Knihovna také podporuje přímé použití [přihlašovacích údajů klienta](../azure-resource-manager/resource-group-authenticate-service-principal.md) Azure AD, když spravovaná identita není k dispozici, nebo když není možné určit kontext zabezpečení vývojáře během místního vývoje.

## <a name="prerequisites"></a>Požadavky

- [Visual studio 2019](https://www.visualstudio.com/downloads/) nebo [Visual Studio 2017 v 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- Rozšíření pro ověřování aplikací pro Visual Studio, které je dostupné jako samostatné rozšíření pro Visual Studio 2017 Update 5 a je součástí produktu v Update 6 nebo novějším. S aktualizací Update 6 nebo novější můžete ověřit instalaci rozšíření ověřování aplikace výběrem nástrojů pro vývoj pro Azure v instalačním programu sady Visual Studio.

## <a name="using-the-library"></a>Použití knihovny

V případě aplikací .NET je nejjednodušší způsob práce se spravovanou identitou prostřednictvím balíčku `Microsoft.Azure.Services.AppAuthentication`. Tady je postup, jak začít:

1. Vyberte **nástroje** > **správce balíčků NuGet** > **Spravovat balíčky NuGet pro řešení** , aby bylo možné přidat odkazy na balíčky NuGet [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) a [Microsoft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) váš projekt.

1. Přidejte následující kód:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

Třída `AzureServiceTokenProvider` ukládá token do paměti a načítá ho z Azure AD těsně před vypršením platnosti. Takže už nemusíte před voláním metody `GetAccessTokenAsync` kontrolu platnosti zrušit. Stačí zavolat metodu, pokud chcete použít token.

Metoda `GetAccessTokenAsync` vyžaduje identifikátor prostředku. Další informace o službě Microsoft Azure Services najdete v tématu [co jsou spravované identity pro prostředky Azure](../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Místní ověřování při vývoji

Pro místní vývoj existují dva scénáře primárního ověřování: [ověřování ve službách Azure](#authenticating-to-azure-services)a [ověřování vlastních služeb](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Ověřování ve službách Azure

Místní počítače nepodporují spravované identity prostředků Azure. V důsledku toho knihovna `Microsoft.Azure.Services.AppAuthentication` používá vaše přihlašovací údaje pro vývojáře ke spuštění ve vašem místním vývojovém prostředí. Když se řešení nasadí do Azure, knihovna pomocí spravované identity přepne na tok udělení přihlašovacích údajů klienta OAuth 2,0. Tento přístup znamená, že můžete stejný kód otestovat místně a vzdáleně bez obav.

Pro místní vývoj `AzureServiceTokenProvider` načte tokeny pomocí sady **Visual Studio**, **rozhraní příkazového řádku** (CLI) Azure nebo **integrovaného ověřování Azure AD**. Každá možnost se zkouší postupně a knihovna používá první možnost, která je úspěšná. Pokud žádná možnost nefunguje, je vyvolána výjimka `AzureServiceTokenProviderException` s podrobnými informacemi.

#### <a name="authenticating-with-visual-studio"></a>Ověřování pomocí sady Visual Studio

Ověření pomocí sady Visual Studio:

1. Přihlaste se k aplikaci Visual Studio a pomocí **nástrojů**&nbsp; @ no__t-2 @ no__t **-3 Otevřete** **Možnosti**.

1. Vyberte **ověřování služby Azure**, zvolte účet pro místní vývoj a vyberte **OK**.

Pokud narazíte na problémy pomocí sady Visual Studio, jako jsou chyby, které zahrnují soubor poskytovatele tokenů, pečlivě zkontrolujte předchozí kroky.

Možná budete muset znovu ověřit váš token pro vývojáře. Provedete to tak, že vyberete **nástroje**&nbsp; @ no__t-2 @ no__t-3**Možnosti**a pak vyberete **Azure @ no__t-6Service @ no__t-7Authentication**. Vyhledejte v rámci vybraného účtu odkaz **znovu ověřit** . Vyberte ji pro ověření.

#### <a name="authenticating-with-azure-cli"></a>Ověřování pomocí Azure CLI

Pokud chcete použít rozhraní příkazového řádku Azure pro místní vývoj, ujistěte se, že máte verzi rozhraní příkazového [řádku Azure CLI v 2.0.12](/cli/azure/install-azure-cli) nebo novější.

Použití rozhraní příkazového řádku Azure:

1. Na hlavním panelu Windows vyhledejte Azure CLI a otevřete **příkazový řádek Microsoft Azure**.

1. Přihlaste se k Azure Portal: *AZ Login* , abyste se přihlásili do Azure.

1. Zadání *AZ Account Get-Access-token--resource https://vault.azure.net* ověřte přístup. Pokud se zobrazí chyba, ověřte, že je správně nainstalovaná správná verze rozhraní příkazového řádku Azure.

   Pokud rozhraní příkazového řádku Azure není nainstalované ve výchozím adresáři, může se zobrazit zpráva o chybách, kterou `AzureServiceTokenProvider`. cestu k rozhraní příkazového řádku Azure CLI nejde najít. K definování instalační složky Azure CLI použijte proměnnou prostředí **AzureCLIPath** . `AzureServiceTokenProvider` přidá adresář zadaný v proměnné prostředí **AzureCLIPath** do proměnné prostředí **path** v případě potřeby.

1. Pokud jste přihlášeni do Azure CLI pomocí několika účtů nebo má váš účet přístup k několika předplatným, je nutné zadat předplatné, které chcete použít. Zadejte příkaz *AZ Account set--subscription < Subscription-id >* .

Tento příkaz generuje výstup pouze při selhání. Pokud chcete ověřit aktuální nastavení účtu, zadejte příkaz `az account list`.

#### <a name="authenticating-with-azure-ad-authentication"></a>Ověřování pomocí ověřování Azure AD

Pokud chcete používat ověřování Azure AD, ověřte, že:

- Vaše místní služba Active Directory se synchronizuje do Azure AD. Další informace najdete v tématu [co je hybridní identita s Azure Active Directory?](../active-directory/connect/active-directory-aadconnect.md).

- Váš kód je spuštěn na počítači připojeném k doméně.

### <a name="authenticating-to-custom-services"></a>Ověřování pro vlastní služby

Když služba volá služby Azure, předchozí postup funguje, protože služby Azure umožňují přístup k uživatelům i aplikacím.

Při vytváření služby, která volá vlastní službu, použijte přihlašovací údaje klienta Azure AD pro místní ověřování při vývoji. K dispozici jsou dvě možnosti:

- Přihlaste se k Azure pomocí instančního objektu:

    1. Vytvoření instančního objektu. Další informace najdete v tématu [Vytvoření instančního objektu Azure pomocí Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

    1. Pomocí rozhraní příkazového řádku Azure se přihlaste pomocí následujícího příkazu:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Vzhledem k tomu, že objekt služby nemusí mít přístup k předplatnému, použijte argument `--allow-no-subscriptions`.

- K určení podrobností instančního objektu použijte proměnné prostředí. Další informace najdete v tématu [spuštění aplikace pomocí instančního objektu](#running-the-application-using-a-service-principal).

Až se přihlásíte k Azure, `AzureServiceTokenProvider` použije instanční objekt k získání tokenu pro místní vývoj.

Tento přístup se vztahuje pouze na místní vývoj. Když se vaše řešení nasadí do Azure, knihovna se přepne na spravovanou identitu pro ověřování.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Spuštění aplikace pomocí spravované identity nebo identity přiřazené uživatelem

Když spustíte kód na Azure App Service nebo VIRTUÁLNÍm počítači Azure se zapnutou spravovanou identitou, knihovna automaticky použije spravovanou identitu. Nevyžadují se žádné změny kódu, ale spravovaná identita musí mít oprávnění *získat* pro Trezor klíčů. Spravované identitě můžete udělit oprávnění *získat* prostřednictvím *zásad přístupu*trezoru klíčů.

Případně se můžete ověřit pomocí uživatelsky přiřazené identity. Další informace o identitách přiřazených uživateli najdete v tématu [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Chcete-li provést ověření pomocí uživatelsky přiřazené identity, je třeba zadat ID klienta identity přiřazené uživatelem v připojovacím řetězci. Připojovací řetězec je zadán v [podpoře připojovacích řetězců](#connection-string-support).

## <a name="running-the-application-using-a-service-principal"></a>Spuštění aplikace pomocí instančního objektu

Pro ověření může být nutné vytvořit přihlašovací údaje klienta Azure AD. K této situaci může dojít v následujících příkladech:

- Váš kód běží na místním vývojovém prostředí, ale ne v rámci identity vývojáře. Service Fabric například používá [účet NetworkService](../service-fabric/service-fabric-application-secret-management.md) pro místní vývoj.

- Váš kód běží na místním vývojovém prostředí a Vy se ověřujete ve vlastní službě, takže nemůžete použít vaši identitu vývojáře.

- Váš kód běží na výpočetním prostředku Azure, který zatím nepodporuje spravované identity pro prostředky Azure, jako je Azure Batch.

Existují tři primární metody použití instančního objektu ke spuštění vaší aplikace. Chcete-li použít některý z nich, je nutné nejprve vytvořit instanční objekt. Další informace najdete v tématu [Vytvoření instančního objektu Azure pomocí Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Přihlášení k Azure AD pomocí certifikátu v místním úložišti klíčů

1. Pomocí příkazu Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) vytvořte certifikát instančního objektu.

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Tento příkaz vytvoří soubor. pem (privátní klíč), který je uložený v domovském adresáři. Tento certifikát můžete nasadit do úložiště *LocalMachine* nebo *CurrentUser* .

    > [!Important]
    > Příkaz CLI vygeneruje soubor. pem, ale systém Windows poskytuje pouze nativní podporu certifikátů PFX. K vygenerování certifikátu PFX použijte příkazy PowerShellu, které jsou tady uvedené: [Vytvoření instančního objektu s certifikátem podepsaným svým držitelem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Tyto příkazy také automaticky nasadí certifikát.

1. Nastavte proměnnou prostředí s názvem **AzureServicesAuthConnectionString** na následující hodnotu:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Nahraďte hodnoty *{AppID}* , *{TenantId}* a *{kryptografický otisk}* hodnotami generovanými v kroku 1. Nahraďte *{CertificateStore}* buď *LocalMachine*, nebo *CurrentUser*, a to na základě vašeho plánu nasazení.

1. Spusťte aplikaci.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Přihlášení ke službě Azure AD pomocí sdíleného tajného pověření

1. Vytvořte certifikát instančního objektu pomocí hesla pomocí příkazu Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) s parametrem--SDK-auth.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. Nastavte proměnnou prostředí s názvem **AzureServicesAuthConnectionString** na následující hodnotu:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Nahraďte hodnoty _{AppID}_ , _{TenantId}_ a _{ClientSecret}_ hodnotami generovanými v kroku 1.

1. Spusťte aplikaci.

Jakmile se všechno nastaví správně, nemusíte dělat žádné další změny kódu. @no__t – 0 používá k ověření ve službě Azure AD proměnnou prostředí a certifikát.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Přihlášení k Azure AD pomocí certifikátu v Key Vault

Tato možnost umožňuje uložit klientský certifikát instančního objektu do Key Vault a použít ho pro ověřování instančního objektu. Tuto možnost lze použít pro následující scénáře:

- Místní ověřování, kde se chcete ověřit pomocí explicitního instančního objektu a chcete bezpečně uchovávat přihlašovací údaje instančního objektu v trezoru klíčů. Vývojářský účet musí mít přístup k trezoru klíčů.

- Ověřování z Azure, ve kterém chcete používat explicitní přihlašovací údaje a chcete bezpečně uchovávat přihlašovací údaje instančního objektu v trezoru klíčů. Tuto možnost můžete použít pro scénář pro více tenantů. Spravovaná identita musí mít přístup k trezoru klíčů.

Spravovaná identita nebo vaše identita vývojáře musí mít oprávnění k načtení certifikátu klienta z Key Vault. Knihovna AppAuthentication používá načtený certifikát jako přihlašovací údaje klienta instančního objektu.

Použití klientského certifikátu pro ověřování instančního objektu:

1. Vytvořte hlavní certifikát služby a automaticky ho uložte do svého Key Vault. Použijte rozhraní příkazového řádku Azure CLI [AZ AD SP Create-for-RBAC--klíčů \<keyvaultname >--cert \<certificatename >--Create-CERT--Skip-Assignment](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) příkaz:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    Identifikátor certifikátu bude adresa URL ve formátu `https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`.

1. Nahraďte `{KeyVaultCertificateSecretIdentifier}` v tomto připojovacím řetězci identifikátorem certifikátu:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Například pokud váš Trezor klíčů byl nazvaný *myKeyVault* a vytvořili jste certifikát nazvaný *myCert*, identifikátor certifikátu by byl:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Podpora připojovacích řetězců

Ve výchozím nastavení používá `AzureServiceTokenProvider` k získání tokenu více metod.

Chcete-li řídit proces, použijte připojovací řetězec předaný konstruktoru `AzureServiceTokenProvider` nebo zadaný v proměnné prostředí *AzureServicesAuthConnectionString* .

Podporovány jsou následující možnosti:

| Možnost připojovacího řetězce | Scénář | Komentáře|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Místní vývoj | @no__t – 0 používá k získání tokenu Azure CLI. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Místní vývoj | @no__t – 0 používá k získání tokenu Visual Studio. |
| `RunAs=CurrentUser` | Místní vývoj | @no__t – 0 používá k získání tokenu integrované ověřování Azure AD. |
| `RunAs=App` | [Spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/index.yml) | @no__t – 0 používá k získání tokenu spravovanou identitu. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Uživatelsky přiřazená identita pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | `AzureServiceTokenProvider` používá k získání tokenu uživatelem přiřazenou identitu. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Ověřování vlastních služeb | `KeyVaultCertificateSecretIdentifier` je tajný identifikátor certifikátu. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Instanční objekt | @no__t – 0 používá k získání tokenu z Azure AD certifikát. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Instanční objekt | @no__t – 0 používá k získání tokenu z Azure AD certifikát.|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Instanční objekt |@no__t – 0 používá tajný klíč k získání tokenu z Azure AD. |

## <a name="samples"></a>Ukázky

Chcete-li zobrazit knihovnu `Microsoft.Azure.Services.AppAuthentication` v akci, podívejte se na následující ukázky kódu.

- [Použití spravované identity k načtení tajného klíče z Azure Key Vault za běhu](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Programové nasazení Azure Resource Manager šablony z virtuálního počítače Azure se spravovanou identitou](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

- [Použijte ukázku .NET Core a spravovanou identitu pro volání služeb Azure z virtuálního počítače Azure Linux](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="appauthentication-troubleshooting"></a>Řešení potíží s AppAuthentication

### <a name="common-issues-during-local-development"></a>Běžné problémy při místním vývoji

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>Rozhraní příkazového řádku Azure není nainstalované, nejste přihlášeni nebo nemáte nejnovější verzi.

Spuštěním *AZ Account Get-Access-token* zjistíte, jestli Azure CLI zobrazuje token pro vás. Pokud **žádný takový program nenalezne**, nainstalujte [nejnovější verzi rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Může se zobrazit výzva k přihlášení.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider nemůže najít cestu pro rozhraní příkazového řádku Azure CLI.

AzureServiceTokenProvider vyhledá Azure CLI ve svých výchozích umístěních pro instalaci. Pokud nenalezne rozhraní příkazového řádku Azure CLI, nastavte proměnnou prostředí **AzureCLIPath** na instalační SLOŽKU Azure CLI. AzureServiceTokenProvider přidá proměnnou prostředí do proměnné prostředí PATH.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>K rozhraní příkazového řádku Azure jste přihlášeni pomocí více účtů, stejný účet má přístup k předplatným ve více klientech nebo při pokusu o provedení volání během místního vývoje dojde k chybě odepření přístupu.

Pomocí Azure CLI nastavte výchozí předplatné na jeden, který má účet, který chcete použít. Předplatné musí být ve stejném tenantovi jako prostředek, ke kterému chcete získat přístup: **AZ Account set--Subscription [ID předplatného]** . Pokud se žádný výstup nezobrazuje, byl úspěšný. Ověřte, že je teď správný účet ve výchozím nastavení pomocí **AZ Account list**.

### <a name="common-issues-across-environments"></a>Běžné problémy v různých prostředích

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Neoprávněný přístup, přístup odepřen, zakázaný nebo podobná chyba

Použitý objekt zabezpečení nemá přístup k prostředku, ke kterému se pokouší získat přístup. Udělte vašemu uživatelskému účtu nebo přispěvateli služby MSI pro App Service přístup k prostředku. Tato možnost závisí na tom, jestli je ukázka spuštěná na místním počítači nebo nasazená v Azure do vašeho App Service. Některé prostředky, jako jsou například trezory klíčů, mají také vlastní [zásady přístupu](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) , které můžete použít k udělení přístupu k objektům zabezpečení, jako jsou uživatelé, aplikace a skupiny.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Běžné problémy při nasazení na Azure App Service

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>Spravovaná identita není nastavená na App Service

Ověřte, že proměnné prostředí MSI_ENDPOINT a MSI_SECRET existují pomocí [konzoly ladění Kudu](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Pokud tyto proměnné prostředí neexistují, spravovaná identita není na App Service povolena.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Běžné problémy při nasazení v místním prostředí se službou IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Nejde načíst tokeny při ladění aplikace ve službě IIS.

Ve výchozím nastavení AppAuth běží v jiném uživatelském kontextu služby IIS. To je důvod, proč nemá přístup k získání přístupových tokenů pomocí vaší identity vývojáře. Službu IIS můžete nakonfigurovat tak, aby běžela s vaším uživatelským kontextem, a to pomocí následujících dvou kroků:
- Nakonfigurujte fond aplikací webové aplikace tak, aby běžel jako aktuální uživatelský účet. Další informace najdete [tady](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities) .
- Nakonfigurujte "setProfileEnvironment" na "true". Další informace najdete [tady](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Přejít na%windir%\System32\inetsrv\config\applicationHost.config
    - Vyhledejte "setProfileEnvironment". Pokud je nastavené na false, změňte ho na true (pravda). Pokud není k dispozici, přidejte jej jako atribut do elementu processModel (/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment) a nastavte jej na hodnotu "true".

- Přečtěte si další informace o [spravovaných identitách pro prostředky Azure](../active-directory/managed-identities-azure-resources/index.yml).
- Přečtěte si další informace o [scénářích ověřování Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).
