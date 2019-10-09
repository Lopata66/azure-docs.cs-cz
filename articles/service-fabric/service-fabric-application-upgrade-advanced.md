---
title: Rozšířená témata týkající se upgradu aplikací | Microsoft Docs
description: Tento článek se věnuje několika pokročilým tématům, která se týkají upgradu aplikace Service Fabric.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: c37ee8177ba31ac8a5da90fef175a6fbd63a6d75
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167585"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Upgrade Service Fabric aplikace: Pokročilá témata
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>Přidání nebo odebrání typů služeb během upgradu aplikace
Pokud se do publikované aplikace v rámci upgradu přidá nový typ služby, do nasazené aplikace se přidá nový typ služby. Takový upgrade nemá vliv na žádné instance služby, které již jsou součástí aplikace, ale je nutné vytvořit instanci přidaného typu, aby byl nový typ služby aktivní (viz [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Podobně lze typy služeb odebrat z aplikace jako součást upgradu. Před pokračováním v upgradu je ale nutné odebrat všechny instance služby, které mají být odebrány. (viz [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="manual-upgrade-mode"></a>Režim ručního upgradu
> [!NOTE]
> Režim *monitorovaného* upgradu je doporučen pro všechny upgrady Service Fabric.
> Režim upgradu *UnmonitoredManual* by se měl považovat jenom za neúspěšné nebo pozastavené upgrady. 
>
>

V *monitorovaném* režimu Service Fabric používá zásady stavu, aby se zajistilo, že bude aplikace v pořádku, jak probíhá upgrade. Pokud dojde k porušení zásad stavu, upgrade se buď pozastaví, nebo se automaticky vrátí zpátky v závislosti na zadaném *FailureAction*.

V režimu *UnmonitoredManual* má správce aplikace celkovou kontrolu nad průběhem upgradu. Tento režim je užitečný při použití vlastních zásad hodnocení stavu nebo provádění nekonvenčních upgradů, které mají zcela obejít monitorování stavu (například aplikace je již v případě ztráty dat). Upgrade běžící v tomto režimu se sám po dokončení každého UD zastaví a musí se explicitně obnovit pomocí operace [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). V případě, že je upgrade pozastaven a je připraven k obnovení uživatelem, zobrazí se stav upgradu *RollforwardPending* (viz [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

*UnmonitoredAuto* režim je vhodný pro provádění iterací rychlého upgradu během vývoje nebo testování služby, protože není nutný žádný vstup uživatele a nejsou vyhodnoceny žádné zásady stavu aplikace.

## <a name="upgrade-with-a-diff-package"></a>Upgrade s rozdílovým balíčkem
Místo zřízení kompletního balíčku aplikace je možné upgrady provádět taky pomocí zřizovacích balíčků, které obsahují jenom aktualizované balíčky Code/config/data, a to společně s úplným manifestem aplikace a kompletními manifesty služby. Kompletní balíčky aplikací jsou nutné pouze pro počáteční instalaci aplikace do clusteru. Další upgrady můžou být buď z kompletních balíčků aplikací, nebo rozdílných balíčků.  

Jakýkoli odkaz v manifestu aplikace nebo manifestech služby rozdílového balíčku, který se nedá najít v balíčku aplikace, se automaticky nahradí aktuálně zřízenou verzí.

Scénáře použití rozdílového balíčku:

* Máte-li velký balíček aplikace, který odkazuje na několik souborů manifestu služby a/nebo několik balíčků kódu, konfigurační balíčky nebo datové balíčky.
* Pokud máte systém nasazení, který generuje rozložení sestavení přímo z procesu sestavení vaší aplikace. V tomto případě, i když se kód nezměnil, nově vytvořená sestavení získají jiný kontrolní součet. Použití úplného balíčku aplikace by vyžadovalo aktualizaci verze u všech balíčků kódu. Pomocí rozdílového balíčku zadejte jenom soubory, které se změnily, a soubory manifestu, ve kterých se verze změnila.

Při upgradu aplikace pomocí sady Visual Studio je automaticky publikován rozdílový balíček. Chcete-li vytvořit rozdílový balíček ručně, musí být aktualizován manifest aplikace a manifesty služby, ale do finálního balíčku aplikace by měly být zahrnuty pouze změněné balíčky.

Řekněme například, že začneme s následující aplikací (čísla verzí, která jsou poskytována pro snazší porozumění):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Předpokládejme, že jste chtěli aktualizovat pouze balíček kódu Service1 pomocí rozdílového balíčku. Aktualizovaná aplikace má následující změny verze:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

V tomto případě aktualizujete manifest aplikace na 2.0.0 a manifest služby pro Service1 tak, aby odrážel aktualizaci balíčku kódu. Složka pro váš balíček aplikace by měla mít následující strukturu:

```text
app1/
  service1/
    code/
```

Jinými slovy, vytvořit kompletní balíček aplikace normálně a pak odebrat všechny složky Code/config/data Package, pro které se verze nezměnila.

## <a name="upgrade-application-parameters-independently-of-version"></a>Upgradovat parametry aplikace nezávisle na verzi

V některých případech je žádoucí změnit parametry Service Fabric aplikace beze změny verze manifestu. To lze provést pohodlně pomocí příznaku **-ApplicationParameter** s rutinou **Start-ServiceFabricApplicationUpgrade** Azure Service Fabric PowerShellu. Předpokládejme Service Fabric aplikaci s následujícími vlastnostmi:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Nyní upgradujte aplikaci pomocí rutiny **Start-ServiceFabricApplicationUpgrade** . Tento příklad ukazuje monitorovaný upgrade, ale lze použít také nemonitorovaný upgrade. Úplný popis příznaků přijatých touto rutinou najdete v tématu [Přehled modulu Azure Service Fabric PowerShell](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters) .

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

Po upgradu ověřte, zda má aplikace aktualizované parametry a stejnou verzi:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="rolling-back-application-upgrades"></a>Vracení back-upgradech aplikací

I když se upgrady dají převádět v jednom ze tří režimů (*monitorované*, *UnmonitoredAuto*nebo *UnmonitoredManual*), dají se vrátit zpátky buď v režimu *UnmonitoredAuto* , nebo *UnmonitoredManual* . Vracení zpět v režimu *UnmonitoredAuto* funguje stejným způsobem jako při vracení s výjimkou, že výchozí hodnota *UpgradeReplicaSetCheckTimeout* je odlišná – viz [parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md). Vracení zpět v režimu *UnmonitoredManual* funguje stejně jako postupné dopředné – vrácení zpět se po dokončení každého ud odblokuje a musí se explicitně obnovit pomocí funkce [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) , aby bylo možné pokračovat s návrat.

Vrácení zpět se dá spustit automaticky, když se naruší zásady stavu upgradu v *monitorovaném* režimu s *FailureActionem* *vrácení zpět* (viz [parametry upgradu aplikace](service-fabric-application-upgrade-parameters.md)) nebo explicitně pomocí [. Start – ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Během vracení zpět lze hodnotu *UpgradeReplicaSetCheckTimeout* a režim kdykoli změnit pomocí funkce [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Další kroky
[Upgrade aplikace pomocí sady Visual Studio](service-fabric-application-upgrade-tutorial.md) vás provede upgradem aplikace pomocí sady Visual Studio.

[Upgrade aplikace pomocí PowerShellu](service-fabric-application-upgrade-tutorial-powershell.md) vás provede upgradem aplikace pomocí PowerShellu.

Pomocí [parametrů upgradu](service-fabric-application-upgrade-parameters.md)lze řídit, jak se vaše aplikace upgradují.

Vyjistěte, aby byly upgrady aplikací kompatibilní, pomocí učení, jak používat [serializaci dat](service-fabric-application-upgrade-data-serialization.md).

Pomocí postupu v části [řešení potíží s upgrady aplikací](service-fabric-application-upgrade-troubleshooting.md)můžete opravit běžné problémy s upgrady aplikací.
