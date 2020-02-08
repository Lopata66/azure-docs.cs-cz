---
title: Zabalení aplikace Service Fabric v Azure
description: Přečtěte si o balení aplikace Service Fabric v Azure a o tom, jak připravit nasazení na cluster.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 7c99eec28ac06ecf666d6dda1015f889841a5dbf
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083613"
---
# <a name="package-an-application"></a>Balení aplikace

Tento článek popisuje, jak zabalit Service Fabric aplikaci a připravit ji pro nasazení.

## <a name="package-layout"></a>Rozložení balíčku

Manifest aplikace, jeden nebo více manifestů služby a další nezbytné soubory balíčku musí být uspořádány do konkrétního rozložení pro nasazení do clusteru Service Fabric. Příklady manifestů v tomto článku by měly být uspořádány do následující adresářové struktury:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

Složky jsou pojmenovány tak, aby odpovídaly atributům **názvu** každého odpovídajícího elementu. Například pokud manifest služby obsahuje dva balíčky kódu s názvy **MyCodeA** a **MyCodeB**, pak dvě složky se stejnými názvy budou obsahovat nezbytné binární soubory pro každý balíček kódu.

## <a name="use-setupentrypoint"></a>Použití SetupEntryPoint

Typické scénáře použití **SetupEntryPoint** jsou, pokud potřebujete spustit spustitelný soubor před spuštěním služby nebo potřebujete provést operaci se zvýšenými oprávněními. Příklad:

* Nastavení a inicializace proměnných prostředí, které vyžaduje spustitelný soubor služby. Není omezen pouze na spustitelné soubory napsané prostřednictvím Service Fabric programovacích modelů. Například npm. exe potřebuje některé proměnné prostředí nakonfigurované pro nasazení aplikace Node. js.
* Nastavení řízení přístupu instalací certifikátů zabezpečení.

Další informace o tom, jak nakonfigurovat **SetupEntryPoint**, najdete v tématu [Konfigurace zásad pro vstupní bod nastavení služby](service-fabric-application-runas-security.md) .

<a id="Package-App"></a>

## <a name="configure"></a>Konfigurace

### <a name="build-a-package-by-using-visual-studio"></a>Sestavení balíčku pomocí sady Visual Studio

Pokud jste k vytvoření aplikace použili sadu Visual Studio, můžete použít příkaz *Package* k automatickému vytvoření balíčku, který odpovídá rozložení popsané výše.

Chcete-li vytvořit balíček, klikněte pravým tlačítkem myši na projekt aplikace v *Průzkumník řešení* a vyberte příkaz **balíček** :

![Balení aplikace pomocí sady Visual Studio][vs-package-command]

Po dokončení balení můžete umístění balíčku najít v okně **výstup** . K balíčku balení dochází automaticky při nasazení nebo ladění aplikace v aplikaci Visual Studio.

### <a name="build-a-package-by-command-line"></a>Sestavení balíčku pomocí příkazového řádku

Je také možné programově zabalit aplikaci pomocí `msbuild.exe`. V digestoři je aplikace Visual Studio spuštěna, takže výstup je stejný.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Test balíčku

Strukturu balíčku můžete ověřit místně přes PowerShell pomocí příkazu [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) .
Tento příkaz zkontroluje problémy s analýzou manifestu a ověří všechny odkazy. Tento příkaz ověřuje pouze strukturální správnost adresářů a souborů v balíčku.
Neověřuje žádné obsahy obsahu ani balíčku dat mimo kontrolu, zda jsou k dispozici všechny potřebné soubory.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Tato chyba ukazuje, že v balíčku kódu chybí soubor *MySetup. bat* , na který odkazuje manifest služby **SetupEntryPoint** . Po přidání chybějícího souboru projde ověření aplikace:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
True
```

Pokud má vaše aplikace definované [parametry aplikace](service-fabric-manage-multiple-environment-app-configuration.md) , můžete je předat do [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) pro správné ověření.

Pokud znáte cluster, do kterého bude aplikace nasazena, doporučujeme předat parametr `ImageStoreConnectionString`. V tomto případě je balíček také ověřený proti předchozím verzím aplikace, které jsou již spuštěny v clusteru. Ověřování například může zjistit, zda byl balíček se stejnou verzí, ale s jiným obsahem již nasazen.  

Jakmile je aplikace zabalená správně a předá ověření, zvažte komprimaci balíčku pro rychlejší operace nasazení.

## <a name="compress-a-package"></a>Komprimace balíčku

Pokud je balíček rozsáhlý nebo má mnoho souborů, můžete ho zkomprimovat pro rychlejší nasazení. Komprese snižuje počet souborů a velikost balíčku.
Pro komprimovaný balíček aplikace může [nahrávání balíčku aplikace](service-fabric-deploy-remove-applications.md#upload-the-application-package) trvat déle, než se nahraje nekomprimovaný balíček, zejména pokud se komprese provádí jako součást kopie. Při kompresi je rychlejší [registrace](service-fabric-deploy-remove-applications.md#register-the-application-package) a [zrušení registrace typu aplikace](service-fabric-deploy-remove-applications.md#unregister-an-application-type) .

Mechanismus nasazení je stejný pro komprimované a nekomprimované balíčky. Pokud je balíček komprimovaný, je uložený v úložišti imagí clusteru a před spuštěním aplikace je v uzlu nekomprimovaný.
Komprese nahradí platný balíček Service Fabric komprimovanou verzí. Složka musí umožňovat oprávnění k zápisu. Spuštění komprese u již komprimovaného balíčku neposkytne žádné změny.

Balíček můžete zkomprimovat spuštěním příkazu PowerShellu [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) s přepínačem `CompressPackage`. Balíček můžete dekomprimovat pomocí stejného příkazu pomocí přepínače `UncompressPackage`.

Následující příkaz zkomprimuje balíček bez jeho zkopírování do úložiště imagí. Komprimovaný balíček můžete zkopírovat do jednoho nebo více Service Fabric clusterů, a to podle potřeby pomocí [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) bez příznaku `SkipCopy`.
Balíček teď obsahuje soubory zip pro balíčky `code`, `config`a `data`. Manifest aplikace a manifesty služby nejsou zip, protože jsou potřeba pro mnoho interních operací. Například sdílení balíčků, název typu aplikace a extrakce verzí pro určitá ověření všechny potřebují přístup k manifestům. Zipování manifesty by tyto operace neefektivně vedly.

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

Případně můžete balíček zkomprimovat a kopírovat pomocí [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) v jednom kroku.
Pokud je balíček velký, poskytněte dostatečně dlouhou časovou prodlevu, aby bylo možné použít kompresi balíčku i odeslání do clusteru.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Interně Service Fabric vypočítá kontrolní součty pro balíčky aplikací pro ověření. Při použití komprese jsou kontrolní součty vypočítány ve verzích zip každého balíčku. Generování nového souboru ZIP ze stejného balíčku aplikace vytvoří jiné kontrolní součty. Chcete-li zabránit chybám při ověřování, používejte [rozdílové zřizování](service-fabric-application-upgrade-advanced.md). Pomocí této možnosti nezahrnujte nezměněné balíčky v nové verzi. Místo toho je třeba odkázat přímo z nového manifestu služby.

Pokud rozdílové zřizování není možnost a musíte zahrnout balíčky, vygenerujte nové verze pro `code`, `config`a `data` balíčky, aby nedošlo k neshodě kontrolního součtu. Generování nových verzí pro nezměněné balíčky je nezbytné, pokud je použit komprimovaný balíček bez ohledu na to, zda předchozí verze používá kompresi nebo ne.

Balíček se teď zabalí správně, ověří a komprimuje (Pokud je potřeba), takže je připravený na [nasazení](service-fabric-deploy-remove-applications.md) do jednoho nebo víc Service Fabric clusterů.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Komprimovat balíčky při nasazení pomocí sady Visual Studio

Můžete aplikaci Visual Studio dát možnost Komprimovat balíčky při nasazení, a to přidáním prvku `CopyPackageParameters` do publikačního profilu a nastavením atributu `CompressPackage` na `true`.

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Vytvoření sfpkg

Počínaje verzí 6,1 Service Fabric umožňuje zřizování z externího úložiště.
S touto možností není nutné zkopírovat balíček aplikace do úložiště imagí. Místo toho můžete vytvořit `sfpkg` a nahrát ho do externího úložiště a pak zadat identifikátor URI pro stažení, který se Service Fabric při zřizování. Stejný balíček lze zřídit pro více clusterů. Zřizování z externího úložiště šetří čas potřebný ke zkopírování balíčku do každého clusteru.

`sfpkg` soubor je zip, který obsahuje úvodní balíček aplikace a má příponu ". sfpkg".
V souboru ZIP může být balíček aplikace komprimovaný nebo nekomprimovaný. Komprese balíčku aplikace uvnitř souboru ZIP je prováděna na úrovni kódu, konfigurace a balíčku dat, jak je [uvedeno výše](service-fabric-package-apps.md#compress-a-package).

Pokud chcete vytvořit `sfpkg`, začněte se složkou, která obsahuje původní balíček aplikace, komprimovaná nebo ne. Pak použijte libovolný nástroj pro zip složku s příponou ". sfpkg". Použijte například [podřízený ZipFile. CreateFromDirectory](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

`sfpkg` musí být nahrány do externího úložiště mimo IP síť mimo Service Fabric. Externím úložištěm může být jakékoli úložiště, které zveřejňuje koncový bod HTTP nebo HTTPS s REST. Při zřizování Service Fabric spustí operaci GET ke stažení balíčku aplikace `sfpkg`, takže úložiště musí pro balíček umožnit přístup pro čtení.

Pro zřízení balíčku použijte externí zřizování, které vyžaduje identifikátor URI pro stažení a informace o typu aplikace.

>[!NOTE]
> Zřizování na základě relativní cesty k úložišti imagí v současné době nepodporuje soubory `sfpkg`. Proto by `sfpkg` neměl být kopírován do úložiště imagí.

## <a name="next-steps"></a>Další kroky

[Nasazení a odebrání aplikací][10] popisuje použití prostředí PowerShell ke správě instancí aplikace

[Správa parametrů aplikace pro více prostředí][11] popisuje, jak nakonfigurovat parametry a proměnné prostředí pro různé instance aplikací.

[Konfigurace zásad zabezpečení pro aplikaci][12] popisuje, jak spustit služby v části zásady zabezpečení, abyste omezili přístup.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md