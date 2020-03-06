---
title: Přidělení údajů o partnerovi a využití zákazníky v Azure | Azure Marketplace
description: Přehled toho, jak sledovat zákaznickou spotřebu Azure Marketplace řešení
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 9/23/2019
ms.author: pabutler
ms.openlocfilehash: c745a37805862b77e7893788d9d089c8447ae465
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302861"
---
# <a name="azure-partner-customer-usage-attribution"></a>Přisuzování využití ze strany zákazníků partnerům Azure

Jako softwarový partner pro Azure vaše řešení vyžadují součásti Azure nebo musí být nasazeny přímo v infrastruktuře Azure. Zákazníci, kteří nasazují Partnerské řešení a zřídí vlastní prostředky Azure, můžou najít obtížné získat přehled o stavu nasazení a dostat se do dopadu na růst Azure. Když přidáte vyšší úroveň viditelnosti, zarovnáte se s prodejními týmy Microsoftu a získáte kredit pro partnerské programy Microsoftu.

Microsoft teď nabízí metodu, která pomáhá partnerům lépe sledovat využití Azure zákaznických nasazení jejich softwaru v Azure. Nová metoda používá Azure Resource Manager k orchestraci nasazení služeb Azure.

Jako partner Microsoftu můžete k využití Azure přidružit všechny prostředky Azure, které zřídíte jménem zákazníka. Přidružení můžete vytvořit prostřednictvím Azure Marketplace, úložiště pro rychlé zprovoznění, privátních úložišť GitHub a zapojení zákazníka na jednu možnost. Označení zákaznického využití podporuje tři možnosti nasazení:

- Azure Resource Manager šablony: partneři můžou pomocí šablon Správce prostředků nasadit služby Azure, aby mohli provozovat software partnera. Partneři můžou vytvořit šablonu Správce prostředků k definování infrastruktury a konfigurace jejich řešení Azure. Šablona Správce prostředků umožňuje vašim zákazníkům nasadit vaše řešení v celém životním cyklu. Máte jistotu, že se prostředky nasazují v konzistentním stavu.
- Rozhraní Azure Resource Manager API: partneři můžou volat rozhraní API Správce prostředků přímo, aby mohli nasadit šablonu Správce prostředků nebo aby generovala volání rozhraní API, která přímo zřídí služby Azure.
- Terraformu: partneři můžou pomocí cloudového nástroje Orchestrator, jako je Terraformu, nasadit šablonu Správce prostředků nebo přímo nasadit služby Azure.

Přidělení zákaznického využití je pro nové nasazení a nepodporuje označení existujících prostředků, které už byly nasazené.

V [aplikaci Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer)se vyžaduje označení zákaznického využívání: nabídka šablon řešení se publikovala do Azure Marketplace.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-resource-manager-templates"></a>Použití šablon Resource Manageru
Mnohé z partnerských řešení se nasazují v rámci předplatného zákazníka pomocí Správce prostředků šablon. Pokud máte šablonu Správce prostředků, která je k dispozici v Azure Marketplace, na GitHubu nebo v rychlém startu, proces změny šablony tak, aby bylo umožněno přidělení zákaznického využití, by mělo být rovné.

Další informace o vytváření a publikování šablon řešení najdete v tématu.

* [Vytvořte a nasaďte první šablonu správce prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
* [Nabídka aplikací Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer)
* Video: [sestavování šablon řešení a spravovaných aplikací pro Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


## <a name="add-a-guid-to-your-template"></a>Přidání identifikátoru GUID do šablony

Chcete-li přidat globálně jedinečný identifikátor (GUID), proveďte jednu úpravu souboru hlavní šablony:

1. [Vytvořte identifikátor GUID](#create-guids) pomocí navrhované metody a [zaregistrujte identifikátor GUID](#register-guids-and-offers).

1. Otevřete šablonu Správce prostředků.

1. Přidejte nový prostředek do hlavního souboru šablony. Prostředek musí být v souboru **mainTemplate. JSON** nebo **azuredeploy. JSON** , a ne v žádné vnořené nebo propojené šabloně.

1. Zadejte hodnotu identifikátoru GUID za předponou **PID-** prefix (např. PID-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. V šabloně vyhledejte případné chyby.

1. Znovu publikujte šablonu v příslušných úložištích.

1. [Ověřte úspěch identifikátoru GUID v nasazení šablony](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Ukázka kódu Správce prostředků šablony

Chcete-li povolit sledování prostředků pro šablonu, je třeba přidat následující další prostředek do části prostředky. Nezapomeňte prosím, abyste při přidávání do hlavního souboru šablony upravili následující vzorový kód s vlastními vstupy.
Prostředek se musí přidat jenom v souboru **mainTemplate. JSON** nebo **azuredeploy. JSON** , a ne v žádné vnořené nebo propojené šabloně.

```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```

## <a name="use-the-resource-manager-apis"></a>Použití rozhraní API pro Správce prostředků

V některých případech můžete chtít volat přímo proti Správce prostředků rozhraní REST API pro nasazení služeb Azure. [Azure podporuje několik sad SDK](https://docs.microsoft.com/azure/?pivot=sdkstools) , aby tato volání mohla povolit. Můžete použít jednu ze sad SDK nebo volat rozhraní REST API přímo k nasazení prostředků.

Pokud používáte šablonu Správce prostředků, měli byste označit své řešení podle pokynů popsaných výše. Pokud nepoužíváte šablonu Správce prostředků a provádíte Přímá volání rozhraní API, můžete si i nadále označit nasazení, aby bylo možné přidružit využití prostředků Azure.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Označení nasazení pomocí Správce prostředků rozhraní API

Pokud chcete při návrhu volání rozhraní API povolit jeho přihlašování, uveďte v žádosti identifikátor GUID v hlavičce uživatelského agenta. Přidejte identifikátor GUID pro každou nabídku nebo SKU. Naformátujte řetězec pomocí předpony **PID-** prefix a ZAHRŇTE identifikátor GUID generovaný partnerem. Tady je příklad formátu identifikátoru GUID pro vložení do uživatelského agenta:

![Příklad formátu GUID](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> Formát řetězce je důležitý. Pokud není k dispozici předpona **PID-** prefix, není možné zadat dotaz na data. Různé sady SDK sledují různě. Pokud chcete implementovat tuto metodu, přečtěte si přístup k podpoře a sledování pro preferovanou sadu Azure SDK.

#### <a name="example-the-python-sdk"></a>Příklad: sada Python SDK

Pro Python použijte atribut **config** . Atribut lze přidat pouze k vlastnosti UserAgent. Tady je příklad:

![Přidání atributu do uživatelského agenta](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Přidejte atribut pro každého klienta. Neexistuje žádná globální statická konfigurace. Můžete označit objekt pro vytváření klienta, aby se ujistil, že každý klient sleduje sledování. Další informace najdete v tématu tato [Ukázka klienta v GitHubu](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Označení nasazení pomocí Azure PowerShell

Pokud prostředky nasazujete prostřednictvím Azure PowerShell, přidejte svůj identifikátor GUID pomocí následující metody:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Označení nasazení pomocí Azure CLI

Když k připojení svého GUID použijete rozhraní příkazového řádku Azure CLI, nastavte proměnnou prostředí **AZURE_HTTP_USER_AGENT** . Tuto proměnnou můžete nastavit v rámci oboru skriptu. Pro rozsah prostředí můžete také nastavit proměnnou globálně:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Další informace najdete v tématu [Azure SDK pro go](https://docs.microsoft.com/azure/go/).

## <a name="use-terraform"></a>Použití Terraformu

Podpora pro Terraformu je dostupná prostřednictvím 1.21.0 verze poskytovatele Azure: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Tato podpora se vztahuje na všechny partnery, kteří nasazují své řešení prostřednictvím Terraformu, a všechny nasazené prostředky a měřené poskytovatelem Azure (verze 1.21.0 nebo novější).

Poskytovatel Azure pro Terraformu přidal nové volitelné pole s názvem [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) , kde můžete zadat sledovací identifikátor GUID, který používáte pro vaše řešení. Hodnota tohoto pole může být také zdrojová z proměnné prostředí *ARM_PARTNER_ID* .

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Partneři, kteří chtějí získat své nasazení prostřednictvím Terraformuu, které sledují označení zákaznického využití, musí provádět tyto akce:

* Vytvořte identifikátor GUID (identifikátor GUID by měl být přidán pro každou nabídku nebo SKU).
* Aktualizujte svého poskytovatele Azure a nastavte hodnotu *partner_id* na identifikátor GUID (neopravte GUID pomocí "PID-", stačí ho nastavit na skutečný identifikátor GUID).

## <a name="create-guids"></a>Vytvořit GUID

Identifikátor GUID je jedinečné referenční číslo, které má 32 hexadecimálních číslic. Chcete-li vytvořit identifikátory GUID pro sledování, měli byste použít generátor GUID. Tým Azure Storage vytvořil [formulář generátoru GUID](https://aka.ms/StoragePartners) , který pošle e-mailem identifikátor GUID správného formátu a bude možné ho znovu použít v různých sledovacích systémech.

> [!Note]
> Důrazně doporučujeme, abyste pomocí [formuláře generátoru identifikátoru guid Azure Storage](https://aka.ms/StoragePartners) vytvořili identifikátor GUID. Další informace najdete v našich [nejčastějších dotazech](#faq).

Pro každou nabídku a distribuční kanál pro každý produkt doporučujeme vytvořit jedinečný identifikátor GUID. Pokud nechcete, aby vytváření sestav bylo rozděleno, můžete použít jeden identifikátor GUID pro více distribučních kanálů produktu.

Pokud nasadíte produkt pomocí šablony a je k dispozici na Azure Marketplace i na GitHubu, můžete vytvořit a zaregistrovat 2 jedinečné identifikátory GUID:

*   Produkt A v Azure Marketplace
*   Produkt A na GitHubu

Vytváření sestav se provádí na základě hodnoty partnera (ID partnera Microsoftu) a identifikátorů GUID.

Můžete také sledovat identifikátory GUID na podrobnější úrovni, jako je SKU, kde SKU jsou varianty nabídky.

## <a name="register-guids-and-offers"></a>Registrace identifikátorů GUID a nabídek

Aby bylo možné povolit přidělení zákaznického využití, musí být zaregistrované identifikátory GUID.

Všechny registrace identifikátorů GUID šablon se provádí v partnerském centru.

Po přidání identifikátoru GUID do šablony nebo uživatelského agenta a registraci identifikátoru GUID v partnerském centru jsou sledována všechna nasazení.

1. Zaregistrujte se jako [komerční Vydavatel na webu Marketplace](https://aka.ms/JoinMarketplace).

   * Partneři musí [mít profil v partnerském centru](https://docs.microsoft.com/azure/marketplace/become-publisher). Doporučujeme zobrazit seznam nabídek v Azure Marketplace nebo AppSource.
   * Partneři můžou registrovat víc identifikátorů GUID.
   * Partneři můžou zaregistrovat identifikátor GUID pro šablony řešení a nabídky mimo Marketplace.

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard).

1. V pravém horním rohu vyberte ikonu ozubeného kola nastavení a pak vyberte **Nastavení vývojáře**.

1. Na **stránce nastavení účtu**vyberte **Přidat identifikátor GUID sledování.**

1. Do pole **identifikátor GUID** zadejte identifikátor GUID sledování. Zadejte jenom identifikátor GUID bez předpony **PID-** prefix. Do pole **Popis** zadejte název nebo popis vaší nabídky.

1. Pokud chcete zaregistrovat více než jeden identifikátor GUID, vyberte znovu **Přidat identifikátor GUID sledování** . Na stránce se zobrazí další pole.

1. Vyberte **Save** (Uložit).


## <a name="verify-the-guid-deployment"></a>Ověření nasazení identifikátoru GUID

Po úpravě šablony a spuštění testovacího nasazení použijte následující skript prostředí PowerShell k načtení prostředků, které jste nasadili a označili.

Pomocí skriptu můžete ověřit, že identifikátor GUID je úspěšně přidaný do šablony Správce prostředků. Tento skript se nevztahuje na Správce prostředků nasazení API nebo Terraformu.

Přihlaste se k Azure. Před spuštěním skriptu vyberte předplatné s nasazením, které chcete ověřit. Spusťte skript v rámci předplatného nasazení.

**Identifikátor GUID** a název **zdrojové** sady nasazení jsou povinné parametry.

[Původní skript](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) můžete získat na GitHubu.

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>Sestava

Sestavu pro přidělení zákaznického využití najdete na řídicím panelu analyzovat v partnerském centru. ([https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)). Pokud chcete zobrazit sestavu, musíte se přihlásit pomocí přihlašovacích údajů partnerského centra. Pokud narazíte na nějaké problémy se sestavou nebo přihlášením, vytvořte žádost o podporu podle pokynů v části získat podporu.

Kliknutím na sledovanou šablonu v rozevíracím seznamu typ přidružení partnera sestavu zobrazíte.

![Sestava pro přidělení zákaznického využití](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Upozorněte vaše zákazníky

Partneři by měli informovat své zákazníky o nasazeních, která používají označení zákaznického využití. Microsoft oznamuje partnerovi využití Azure, které je k těmto nasazením přidružené. Následující příklady zahrnují obsah, který můžete použít k informování zákazníků o těchto nasazeních. V příkladech nahraďte \<PARTNERská > názvem vaší společnosti. Partneři by se měli ujistit, že se oznámení zarovnají se zásadami ochrany osobních údajů a kolekcí dat, včetně možností pro vyloučení zákazníků ze sledování.

### <a name="notification-for-resource-manager-template-deployments"></a>Oznámení pro nasazení šablon Správce prostředků

Když nasadíte tuto šablonu, společnost Microsoft dokáže identifikovat instalaci \<> softwaru pro partnery s nasazenými prostředky Azure. Společnost Microsoft je schopná korelovat prostředky Azure, které se používají k podpoře softwaru. Společnost Microsoft tyto informace shromažďuje, aby poskytovala co nejvíc zkušeností s produkty a pracovala s jejich podnikáním. Data se shromažďují a řídí zásadami ochrany osobních údajů od Microsoftu, které najdete na adrese https://www.microsoft.com/trustcenter.

### <a name="notification-for-sdk-or-api-deployments"></a>Oznámení pro nasazení SDK nebo rozhraní API

Když nasadíte software \<PARTNER >, společnost Microsoft dokáže identifikovat instalaci \<ho > softwaru pro partnery s nasazenými prostředky Azure. Společnost Microsoft je schopná korelovat prostředky Azure, které se používají k podpoře softwaru. Společnost Microsoft tyto informace shromažďuje, aby poskytovala co nejvíc zkušeností s produkty a pracovala s jejich podnikáním. Data se shromažďují a řídí zásadami ochrany osobních údajů od Microsoftu, které najdete na adrese https://www.microsoft.com/trustcenter.

## <a name="get-support"></a>Získat podporu

Existují dva kanály podpory v závislosti na problémech, které máte k dispozici.

Pokud narazíte na nějaké problémy v partnerském centru, jako je například zobrazení sestavy týkající se zákaznického používání nebo přihlašování, vytvořte žádost o podporu pomocí týmu podpory partnerského centra: [https://partner.microsoft.com/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Pokud potřebujete pomoc s registrací na tržišti a/nebo s uvedením údajů o využití zákazníka, jako je například nastavení označení zákaznického využití, postupujte podle následujících kroků:

1. Přejít na [stránku podpory](https://go.microsoft.com/fwlink/?linkid=844975).

1. V části **typ problému**vyberte možnost **připojování Marketplace**.

1. Vyberte **kategorii** problému:

   - V případě problémů s přidružením využití vyberte **jiné**.
   - Pro problémy s přístupem Azure Marketplace CPP vyberte problém s **přístupem**.

     ![Výběr kategorie problému](media/marketplace-publishers-guide/lu-article-incident.png)

1. Vyberte možnost **Spustit požadavek**.

1. Na další stránce zadejte požadované hodnoty. Vyberte **Pokračovat**.

1. Na další stránce zadejte požadované hodnoty.

   > [!Important]
   > Do pole **název incidentu** zadejte **sledování využívání ISV**. Podrobně popište svůj problém.

   ![Zadat sledování využití ISV pro název incidentu](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Vyplňte formulář a pak vyberte **Odeslat**.

Můžete také získat technické pokyny od Microsoft Partner Technical konzultanta k technickým scénářům pro účely předprodeji, nasazení a vývoje aplikací, které vám pomůžou pochopit a začlenit jejich přidělení.

### <a name="how-to-submit-a-technical-consultation-request"></a>Odeslání žádosti o technickou spolupráci

1. Navštivte [https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney).
1. Vyberte cloudovou infrastrukturu a správu a otevře se nová stránka, která vám umožní zobrazit technickou cestu.
1. V části služby nasazení klikněte na tlačítko Odeslat žádost.
1. Přihlaste se pomocí účtu MSA (MPN) nebo svého AAD (účet partnerského řídicího panelu). na základě přihlašovacích údajů pro přihlášení se otevře formulář online žádosti:
    * Dokončete/zkontrolujte kontaktní údaje.
    * Informace o konzultacích můžou být předem vyplněné nebo si můžete vybrat z rozevíracích seznamu.
    * Zadejte název a popis problému (uveďte co nejvíc podrobností).
1. Klikněte na Submit (Odeslat).

Zobrazte si podrobné pokyny s snímky obrazovky na [https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>Kam dál

Obraťte se na partnera Microsoftu, který vám poskytne odborného technického konzultanta k nastavení volání podle rozsahu vašich potřeb.

## <a name="faq"></a>Nejčastější dotazy

**Jaké jsou výhody přidání identifikátoru GUID do šablony?**

Microsoft poskytuje partnerům přehled o zákaznických nasazeních jejich řešení a přehledy o jejich ovlivněném využití. Microsoft i partner můžou tyto informace využít k tomu, aby proužívali užší zapojení mezi prodejními týmy. Microsoft i partner můžou data používat k získání jednotnějšího zobrazení dopadu jednotlivých partnerů na růst Azure.

**Po přidání identifikátoru GUID ho lze změnit?**

Ano, zákazník nebo implementační partner může šablonu přizpůsobit a může změnit nebo odebrat identifikátor GUID. Doporučujeme, aby partneři proaktivně popsali roli prostředku a identifikátor GUID svým zákazníkům a partnerům, aby se zabránilo odebrání nebo úpravám identifikátoru GUID. Změna identifikátoru GUID ovlivní jenom nové, neexistující, nasazení a prostředky.

**Můžu sledovat Šablony nasazené z jiného úložiště než od Microsoftu, jako je GitHub?**

Ano, pokud je k dispozici identifikátor GUID při nasazení šablony, je sledování využití sledováno. Partneři musí mít profil v CPP k registraci identifikátorů GUID používaných pro nasazení mimo Azure Marketplace.

**Obdrží zákazník hlášení také?**

Zákazníci mohou sledovat využití jednotlivých prostředků nebo skupin prostředků definovaných zákazníkem v rámci Azure Portal.

**Je tato metodika podobná digitálnímu partnerovi záznamu (partnera DPOR)?**

Tato nová metoda propojení nasazení a využití s řešením partnera poskytuje mechanismus pro propojení partnerského řešení s využitím Azure. PARTNERA DPOR má k přidružení konzultačního poradce (systémů) nebo správy (poskytovatele spravované služby) k předplatnému Azure zákazníka.

**S jakou výhodou je použití formuláře generátoru identifikátorů GUID Azure Storage?**

Je zaručeno, že formulář generátoru GUID Azure Storage vygeneruje identifikátor GUID požadovaného formátu. Pokud navíc používáte některé metody sledování roviny dat Azure Storage, můžete použít stejný identifikátor GUID pro sledování roviny ovládacího prvku Marketplace. Díky tomu můžete využít jeden sjednocený identifikátor GUID pro přidělení partnera, aniž byste museli udržovat samostatné identifikátory GUID.

**Můžu použít privátní vlastní virtuální pevný disk pro nabídku šablony řešení v Azure Marketplace?**

Ne, nemůžete. Image virtuálního počítače musí pocházet z Azure Marketplace, viz: [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines).

Nabídku virtuálních počítačů můžete na webu Marketplace vytvořit pomocí vlastního virtuálního pevného disku a označit ji jako soukromou, takže ji nikdo neuvidí. Pak na tento virtuální počítač na šablonu řešení odkazujte.

**Nepovedlo se aktualizovat vlastnost *contentversion –* pro hlavní šablonu?**

V některých případech pravděpodobně dojde k chybě při nasazení šablony pomocí TemplateLink z jiné šablony, která z nějakého důvodu očekává starší Contentversion –. Alternativním řešením je použití vlastnosti metadata:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
