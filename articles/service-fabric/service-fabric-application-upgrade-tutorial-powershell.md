---
title: Service Fabric upgrade aplikace pomocí PowerShellu | Microsoft Docs
description: Tento článek vás provede nasazením aplikace Service Fabric, změnou kódu a zavedením upgradu pomocí prostředí PowerShell.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 9bc75748-96b0-49ca-8d8a-41fe08398f25
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: db0627c72ea6e7e3b272b818697b8e61b485be78
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167517"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Service Fabric upgrade aplikace pomocí PowerShellu
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Nejčastěji používaným a doporučeným přístupem k upgradu je monitorovaný postupný upgrade.  Azure Service Fabric sleduje stav upgradované aplikace na základě sady zásad stavu. Po upgradu aktualizační domény (UD) Service Fabric vyhodnotí stav aplikace a buď pokračuje v další aktualizační doméně, nebo se upgrade nezdařil v závislosti na zásadách stavu.

Upgrade monitorovaných aplikací se dá provést pomocí spravovaných nebo nativních rozhraní API, PowerShellu, Azure CLI, Java nebo REST. Pokyny k provedení upgradu pomocí sady Visual Studio naleznete v tématu [Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md).

Díky Service Fabric sledovaných upgradů může správce aplikace nakonfigurovat zásady hodnocení stavu, které Service Fabric používá k určení, jestli je aplikace v pořádku. Kromě toho může správce nakonfigurovat akci, která se má provést, když se vyhodnocení stavu nepovede (například provedení automatického vrácení zpět). Tato část vás provede monitorovaným upgradem pro jeden ze vzorků sady SDK, které používají PowerShell. 

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Krok 1: sestavení a nasazení ukázky vizuálních objektů
Sestavte a publikujte aplikaci kliknutím pravým tlačítkem na projekt aplikace, **VisualObjectsApplication** a výběrem příkazu **publikovat** .  Další informace najdete v tématu [Service Fabric kurzu upgradu aplikace](service-fabric-application-upgrade-tutorial.md).  Alternativně můžete použít PowerShell k nasazení aplikace.

> [!NOTE]
> Předtím, než se dá v PowerShellu použít kterýkoli z Service Fabric příkazů, musíte se nejdřív připojit ke clusteru pomocí rutiny `Connect-ServiceFabricCluster`. Podobně se předpokládá, že cluster už je nastavený na vašem místním počítači. Přečtěte si článek o [Nastavení vývojového prostředí Service Fabric](service-fabric-get-started.md).
> 
> 

Po sestavení projektu v aplikaci Visual Studio můžete k zkopírování balíčku aplikace do úložiště bitových kopií použít příkaz PowerShell [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) . Pokud chcete balíček aplikace ověřit lokálně, použijte rutinu [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) . Dalším krokem je registrace aplikace do modulu runtime Service Fabric pomocí rutiny [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) . V následujícím kroku spustíte instanci aplikace pomocí rutiny [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) .  Tyto tři kroky jsou podobné použití položky nabídky **nasadit** v aplikaci Visual Studio.  Po dokončení zřizování byste měli kopírovaný balíček aplikace vyčistit z úložiště imagí, aby se snížily spotřebované prostředky.  Pokud již typ aplikace není vyžadován, je třeba zrušit jeho registraci ze stejného důvodu. Další informace najdete v tématu [nasazení a odebrání aplikací pomocí PowerShellu](service-fabric-application-upgrade-tutorial-powershell.md) .

Nyní můžete použít [Service Fabric Explorer k zobrazení clusteru a aplikace](service-fabric-visualizing-your-cluster.md). Aplikace má webovou službu, na kterou se dá přejít v Internet Exploreru zadáním [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) na adresním řádku.  Měli byste vidět, že se některé plovoucí vizuální objekty v obrazovce pohybují.  Kromě toho můžete ke kontrole stavu aplikace použít [příkaz Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) .

## <a name="step-2-update-the-visual-objects-sample"></a>Krok 2: aktualizace ukázky vizuálních objektů
Můžete si všimnout, že ve verzi, která byla nasazena v kroku 1, se neotáčí vizuální objekty. Pojďme tuto aplikaci upgradovat na jednu, kde se také otočí vizuální objekty.

V řešení VisualObjects vyberte projekt VisualObjects. ActorService a otevřete soubor StatefulVisualObjectActor.cs. V tomto souboru přejděte do metody `MoveObject`, odkomentujte `this.State.Move()`a odkomentujte `this.State.Move(true)`. Tato změna po upgradu služby tyto objekty otočí.

Musíme také aktualizovat soubor *ServiceManifest. XML* (pod PackageRoot) projektu **VisualObjects. ActorService**. Aktualizujte *CodePackage* a verzi služby na 2,0 a odpovídající řádky v souboru *ServiceManifest. XML* .
Po kliknutí pravým tlačítkem na řešení můžete použít možnost *upravit soubory manifestu* aplikace Visual Studio, aby se soubor manifestu změnil.

Po provedení změn by měl manifest vypadat jako následující (zvýrazněné části obsahují změny):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Nyní se soubor *souboru ApplicationManifest. XML* (v rámci projektu **VisualObjects** pod řešením **VisualObjects** ) aktualizuje na verzi 2,0 projektu **VisualObjects. ActorService** . Kromě toho je verze aplikace aktualizována na 2.0.0.0 z 1.0.0.0. *Souboru ApplicationManifest. XML* by měl vypadat jako následující fragment kódu:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Nyní Sestavte projekt tak, že vyberete pouze projekt **ActorService** , kliknete pravým tlačítkem a vyberete možnost **sestavení** v aplikaci Visual Studio. Pokud vyberete možnost **znovu sestavit vše**, měli byste aktualizovat verze pro všechny projekty, protože by došlo ke změně kódu. Teď pokaždé, když kliknete pravým tlačítkem na ***VisualObjectsApplication***, zabalíme aktualizovanou aplikaci, vybereme nabídku Service Fabric a zvolíte **balíček**. Tato akce vytvoří balíček aplikace, který lze nasadit.  Vaše aktualizovaná aplikace je připravená k nasazení.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Krok 3: rozhodnutí o zásadách stavu a parametrech upgradu
Seznamte se s [parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md) a s [procesem upgradu](service-fabric-application-upgrade.md) , abyste získali dobré znalosti o různých parametrech upgradu, časových limitech a kritériu stavu. V tomto návodu je kritérium vyhodnocení stavu služby nastavené na výchozí (a doporučené) hodnoty, což znamená, že všechny služby a instance by měly být po upgradu *v pořádku* .  

Můžeme ale prodloužit *HealthCheckStableDuration* na 180 sekund (aby byly služby v pořádku aspoň 120 sekund před tím, než upgrade pokračuje k další aktualizační doméně).  Pojďme také nastavit *UpgradeDomainTimeout* na 1200 sekund a *UpgradeTimeout* na 3000 sekund.

Nakonec také nastavíme *UpgradeFailureAction* na vrácení zpět. Tato možnost vyžaduje, Service Fabric Pokud chcete vrátit aplikaci do předchozí verze, pokud při upgradu dojde k nějakým problémům. Proto při zahájení upgradu (v kroku 4) jsou zadány následující parametry:

FailureAction = vrácení zpět

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Krok 4: Příprava aplikace pro upgrade
Nyní je aplikace sestavená a připravená k upgradu. Pokud otevřete okno PowerShellu jako správce a zadáte [příkaz Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps), měli byste si být vědomi, že se jedná o typ aplikace 1.0.0.0 z **VisualObjects** , který je nasazený.  

Balíček aplikace je uložený v následující relativní cestě, do které jste nekomprimovaný Service Fabric SDK – *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. V adresáři, ve kterém je uložený balíček aplikace, byste měli najít složku "Package". Zkontrolujte časová razítka, abyste měli jistotu, že se jedná o nejnovější sestavení (možná budete muset vhodně upravit i cesty).

Teď nakopírujete aktualizovaný balíček aplikace na Service Fabric úložiště bitových kopií (kde jsou balíčky aplikací uložené pomocí Service Fabric). Parametr *ApplicationPackagePathInImageStore* informuje Service Fabric, kde může najít balíček aplikace. Aktualizovali jsme aplikaci v "VisualObjects\_v2" pomocí následujícího příkazu (možná budete muset znovu upravit cesty).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Dalším krokem je registrace aplikace pomocí Service Fabric, kterou můžete provést pomocí příkazu [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) :

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Pokud předchozí příkaz selže, je pravděpodobně nutné znovu sestavit všechny služby. Jak je uvedeno v kroku 2, možná budete muset aktualizovat také verzi WebService.

Doporučuje se odebrat balíček aplikace po úspěšné registraci aplikace.  Odstranění balíčků aplikace z úložiště imagí uvolní systémové prostředky.  Udržování nepoužívaných balíčků aplikací spotřebovává diskové úložiště a vede k problémům s výkonem aplikací.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Krok 5: zahájení upgradu aplikace
Teď je vše nastavené na spuštění upgradu aplikace pomocí příkazu [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) :

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Název aplikace je stejný, jako byl popsán v souboru *souboru ApplicationManifest. XML* . Service Fabric používá tento název k identifikaci aplikace, která se aktualizuje. Pokud nastavíte časový limit na příliš krátký, může se zobrazit zpráva o selhání s informací o problému. Přečtěte si část věnované řešení potíží nebo zvyšte časový limit.

Teď, když upgrade aplikace pokračuje, můžete ho monitorovat pomocí Service Fabric Explorer nebo pomocí příkazu PowerShellu [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) : 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Během několika minut by se stav, který jste získali pomocí předchozího příkazu PowerShellu, měl stát, že všechny aktualizační domény byly upgradovány (dokončeno). A měli byste zjistit, že se v okně prohlížeče začali otáčet objekty vizuálů!

Můžete zkusit provést upgrade z verze 2 na verzi 3 nebo z verze 2 na verzi 1 jako cvičení. Přesun z verze 2 na verzi 1 se také považuje za upgrade. Můžete hrát s časovými limity a zásadami stavu, abyste s nimi mohli s nimi dobře pracovat. Pokud nasazujete do clusteru Azure, je nutné parametry nastavit správně. Nastavení časových limitů je dobré.

## <a name="next-steps"></a>Další kroky
[Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgradem aplikace pomocí sady Visual Studio.

Pomocí [parametrů upgradu](service-fabric-application-upgrade-parameters.md)lze řídit, jak se vaše aplikace upgradují.

Vyjistěte, aby byly upgrady aplikací kompatibilní, pomocí učení, jak používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Naučte se používat pokročilé funkce při upgradu vaší aplikace, které odkazují na [Pokročilá témata](service-fabric-application-upgrade-advanced.md).

Pomocí postupu v části [řešení potíží s upgrady aplikací](service-fabric-application-upgrade-troubleshooting.md)můžete opravit běžné problémy s upgrady aplikací.

