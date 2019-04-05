---
title: Běžné dotazy týkající se Microsoft Azure Service Fabric | Dokumentace Microsoftu
description: Nejčastější dotazy k Service Fabric a jejich odpovědi
services: service-fabric
documentationcenter: .net
author: chackdan
manager: chackdan
editor: ''
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: chackdan
ms.openlocfilehash: f75236c826584d742fe3163a2fdac29c4030bf66
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045794"
---
# <a name="commonly-asked-service-fabric-questions"></a>Nejčastější dotazy k Service Fabric

Existuje mnoho nejčastější dotazy o co můžete dělat Service Fabric a jak by měla sloužit. Tento dokument popisuje tyto běžné otázky a odpovědi.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Nastavení clusteru a správy

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Jak vrátit změny zpět certifikát clusteru Service Fabric?

Vrácení zpět jakéhokoli upgradu pro vaši aplikaci vyžaduje stav detekce chyb před vaše kvorum clusteru Service Fabric provádění změn; potvrzené změny můžete pouze posunut dopředu. Eskalace inženýr prostřednictvím zákaznické podpory, může být potřeba zotavit cluster, pokud bylo zavedeno nemonitorované narušující změně certifikátu.  [Upgrade aplikace Service Fabric](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade?branch=master) platí [parametry upgradu aplikace](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master), a zajišťuje nula upgradu promise výpadek.  Následující našich doporučených aplikací monitorovaných režim upgradu, automatické průběh pomocí aktualizačních domén je založena na kontroly stavu, který úspěšné, postupné back automaticky, pokud aktualizace výchozí služby se nezdaří.
 
Pokud váš cluster je pořád využívat vlastnost classic kryptografický otisk certifikátu v šabloně Resource Manageru, doporučujeme vám [změnit cluster kryptografický otisk certifikátu běžný název](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn), chcete-li využívají moderní tajných kódů funkce správy.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Můžete vytvořit cluster, která zahrnuje víc oblastí Azure nebo vlastní datacentra?

Ano. 

Základní technologie clusteru Service Fabric je možné kombinovat počítače, které běží kdekoli na světě, tak dlouho, dokud mají síťové připojení k sobě navzájem. Však sestavováním a spouštěním takového clusteru může být složité.

Pokud vás zajímá v tomto scénáři, budeme rádi, můžete se s námi v kontaktu buď prostřednictvím [seznamu problémů na Githubu Service Fabric](https://github.com/azure/service-fabric-issues) nebo zástupce podpory, aby bylo možné získat další informace. K poskytování dalších nejasnostem, pokyny a doporučení pro tento scénář je práce týmu Service Fabric. 

Pár věcí k uvážení: 

1. Prostředek clusteru Service Fabric v Azure se vztahuje na oblast ještě dnes, jako jsou že Virtual machine scale sets s postavena clusteru. To znamená, že v případě regionálních selhání může ztratit možnost Správa clusteru pomocí Azure Resource Manageru nebo webu Azure portal. To může nastat, i když cluster zůstane spuštěný a bylo by možné pracovat s ním přímo. Kromě toho Azure ještě dnes nenabízí možnost používat jedné virtuální sítě, které lze použít napříč oblastmi. To znamená, že cluster pro více oblastí v Azure vyžaduje [veřejné IP adresy pro každý virtuální počítač ve VM Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) nebo [bran Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md). Tyto možnosti sítě mají jiný dopad na náklady, výkon, a některé míru návrhu aplikace, takže pečlivé analýzy a plánování je vyžadována před vztahu takové prostředí.
2. Údržba, Správa a monitorování těchto počítačů se může stát složité, zejména v případě, že rozložených napříč _typy_ prostředí, například jde o vztah mezi různými poskytovateli cloudových nebo mezi místními prostředky a Azure. Musí se dbát na to, že upgrady, monitorování, Správa a Diagnostika vyplývají z clusteru a aplikace před spuštěním úlohy v produkčním prostředí v takovém prostředí. Pokud už máte prostředí pro řešení těchto problémů v Azure nebo v rámci vlastních datových center, je pravděpodobné, že tyto stejné řešení lze použít při vytváření nebo spuštění vašeho clusteru Service Fabric. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Uzly Service Fabricu automaticky přijímat aktualizace operačního systému?

Můžete použít [virtuálního počítače Škálovací nastavit automatické aktualizace operačního systému Image](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) všeobecně dostupnou funkci ještě dnes.

Pro clustery, které nejsou spuštěné v Azure, máme [aplikace k dispozici](service-fabric-patch-orchestration-application.md) o opravu operačních systémů pod uzly Service Fabric.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Můžete použít velký virtuální počítač škálovací sady v mém clusteru SF? 

**Krátký odpovědí** – ne. 

**Dlouho Odpovědět** – i když se škálovacími sadami velkých virtuálních počítačů umožňují škálování virtuálního počítače škálovací sady až 1 000 instancí virtuálních počítačů, dělá to pomocí skupin umístění (PGs). Selhání (FD) a upgradovací domény (ud) jsou konzistentní pouze v rámci a umístění skupiny Service fabric pomocí doménami selhání a aktualizačními doménami rozhodování umístění repliky nebo služby instance vaší služby. Protože doménami selhání a aktualizačními doménami jsou srovnatelné pouze v rámci skupiny umístění, nelze ji použít SF. Například pokud VM1 v so1 topologie FD = 0 a VM9 do skupiny SO2 má topologie FD = 4, neznamená, že VM1 a VM2 jsou ve dvou různých skříních hardwaru, proto SF nelze použít FD hodnoty v tomto případě rozhodnutí o umístění.

Jako chybějící úrovně 4 načíst vyrovnávání podporu existují jiné problémy se škálovacími sadami velkých virtuálních počítačů v současné době. Vyhledejte [podrobnosti o velké škálovací sady](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Jaká je minimální velikost clusteru Service Fabric? Proč nemůže být menší?

Minimální podporovaná velikost pro cluster Service Fabric běžící úlohy v produkčním prostředí je pět uzlů. Pro scénáře, vývoj podporujeme jeden uzel (optimalizovaný pro rychlé vývojové prostředí v sadě Visual Studio) a clustery s pěti uzly.

Požadujeme, aby cluster pro produkční prostředí má minimálně 5 uzlů z následujících tří důvodů:
1. I v případě, že jsou spuštěny služby žádný uživatel, spustí cluster Service Fabric sadu stavové systémových služeb, včetně názvů služby a služby Správce převzetí služeb při selhání. Tyto systémové služby jsou nezbytné pro cluster tak, aby zůstat funkční.
2. Vždy klademe jedna replika služby podle počtu uzlů, tedy horní limit pro počet replik, které služby (ve skutečnosti na oddíl) může mít velikost clusteru.
3. Protože upgrade clusteru přinese dolů nejméně jeden uzel, chceme mít rezervu nejméně jeden uzel, proto se Chceme mít alespoň dva uzly clusteru pro produkční prostředí *kromě* úplné minimum. Minimální je velikost kvora systémová služba, jak je popsáno níže.  

Chceme, aby clusteru k dispozici i v případě selhání dvou uzlů. Pro cluster Service Fabric k dispozici musí být k dispozici systémových služeb. Stavový systém služby, jako jsou služba pojmenování a služby Správce převzetí služeb při selhání, sledování, které služby jsou nasazené do clusteru a kde se aktuálně hostuje, závisí na silnou konzistenci. Silná konzistence, pak závisí na možnost získat *kvora* pro jakékoli dané aktualizaci na stav těchto služeb, kde kvorum představuje striktní většinou repliky (N/2 + 1) pro danou službu. Proto pokud chcete být odolný proti ztrátě souběžných dvou uzlů (tedy souběžných ztráty dvě repliky služby system), jsme musí mít parametr ClusterSize - QuorumSize > = 2, která vynutí minimální velikosti na pět. Pokud chcete zobrazit, vzít v úvahu cluster má N uzly a jsou repliky N systému služby – jednu na každý uzel. Velikost kvora pro službu system je (N/2 + 1). Výše uvedené nerovnost vypadá jako N - (N/2 + 1) > = 2. Existují dva případy, které byste měli zvážit: i po N a N je liché. Pokud se N je sudé, Dejme tomu, že N = 2\*m kde m > = 1, nerovnost vypadá 2\*m - (2\*m/2 + 1) > = 2 nebo m > = 3. Minimum pro N 6 a dosáhnout při m = 3. Na druhé straně Pokud N je liché, Řekněme, že N = 2\*m + 1, pokud m > = 1, nerovnost vypadá 2\*m + 1 - ((2\*m + 1) / 2 + 1) > = 2, nebo 2\*m + 1 - (m + 1) > = 2 nebo m > = 2. Minimum pro N 5 a který je dosaženo při m = 2. Proto se mezi všechny hodnoty N, které splňují nerovnost parametr ClusterSize - QuorumSize > = 2, minimální hodnota je 5.

Poznámka: výše uvedené argumentu, kterou budeme mít předpokládá, že každý uzel má replika systémová služba, proto velikost kvora je vypočítán na základě počtu uzlů v clusteru. Ale změnou *TargetReplicaSetSize* bychom mohli kvora velikost menší než (N / 2 + 1) která může přidělit dojem, že jsme může mít menší než 5 uzlů clusteru a ještě další 2 navíc uzlů překračuje velikost kvora. Například ve 4 uzly clusteru, pokud jsme nastavili TargetReplicaSetSize 3, kvora velikost podle TargetReplicaSetSize je (3/2 + 1) nebo 2, proto budeme mít parametr ClusterSize - QuorumSize = 4-2 > = 2. Však nelze zaručit, že systémová služba bude účtovat s nebo vyšší než kvora jsme ztrátě nějaká dvojice uzlů najednou, může to být, že dva uzly přijdeme byly hostování dvě repliky, tak služba system začnou ztráty kvora (pouze jednu repliku s vlevo) ND nebude k dispozici.

Tedy Podívejme se na některé konfigurace možné clusteru:

**Jeden uzel**: Tato možnost nebude poskytovat vysokou dostupnost od ztráty jeden uzel pro z jakéhokoli důvodu znamená, že ke ztrátě celého clusteru.

**Dva uzly**: kvora pro služby nasazené ve dvou uzlů (N = 2) je 2 (2/2 + 1 = 2). Když dojde ke ztrátě jen jednu repliku, není možné vytvořit kvorum. Protože provedení upgradu služby vyžaduje dočasně zastavovat tu repliku, to není užitečné konfigurace.

**Tři uzly**: se třemi uzly (N = 3), požadavek na vytvoření kvora je stále dva uzly (3/2 + 1 = 2). To znamená, že můžete ztratit jednotlivých uzlů a stále zůstalo zachované kvorum, ale selhání dvou uzlů, se bude řídit systémové služby do ztráty kvora a způsobí, že clusteru přestanou být dostupné.

**Čtyři uzly**: se čtyřmi uzly (N = 4), požadavek na vytvoření kvora se třemi uzly (4/2 + 1 = 3). To znamená, že můžete ztratit jednotlivých uzlů a stále zůstalo zachované kvorum, ale selhání dvou uzlů, se bude řídit systémové služby do ztráty kvora a způsobí, že clusteru přestanou být dostupné.

**Pět uzlů**: s pěti uzly (N = 5), požadavek na vytvoření kvora je stále tři uzly (5/2 + 1 = 3). To znamená, že můžete ztratit dva uzly ve stejnou dobu a přitom zachovává kvora pro systémové služby.

Pro produkční úlohy musíte být odolné vůči selhání alespoň dva uzly (například jeden z důvodu upgradu clusteru, jeden z nějakého jiného důvodu), tak, aby byly požadovaných pěti uzlů.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Můžu v noci nebo o víkendech vám ušetří náklady vypnout clusteru?

V obecně to možné není. Service Fabric ukládá stav na místní dočasné disky, což znamená, že pokud se virtuální počítač přesune na jiného hostitele, data nepřesouvá s ním. V běžném provozu, to není problém ostatní uzly budou přeneseny nový uzel aktuální. Ale pokud zastavení všech uzlů a jejich počítač restartovat později, je důležité možnosti, že většinu uzlů spustit na nového hostitele a ověřte nelze provést obnovení systému.

Pokud chcete vytvářet clustery pro testování vašich aplikací předtím, než je nasazen, doporučujeme dynamicky vytvořit tyto clustery jako součást vaší [kanál průběžné integrace a nasazování](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Jak můžu upgradovat mého operačního systému (třeba z Windows serveru 2012 na Windows Server 2016)?

Pracujeme na vylepšení, ještě dnes, zodpovídáte za upgrade. Je nutné upgradovat image operačního systému na virtuální počítače z jednoho clusteru virtuálních počítačů najednou. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Můžete šifrovat připojené datové disky v typu uzlu clusteru (škálovací sady virtuálních počítačů)?
Ano.  Další informace najdete v tématu [vytvořit cluster s připojenými datovými disky](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks), [šifrování disků (PowerShell)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md), a [šifrování disků (rozhraní příkazového řádku)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-cli.md).

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Můžete použít virtuální počítače s nízkou prioritou v typu uzlu clusteru (škálovací sady virtuálních počítačů)?
Ne. Virtuální počítače s nízkou prioritou se nepodporují. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Co jsou adresáře a procesy, které je potřeba vyloučit při spuštění antivirového programu v mém clusteru?

| **Antivirové Vyloučené adresáře** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (od konfigurace clusteru) |
| FabricLogRoot (od konfigurace clusteru) |

| **Antivirové vyloučené procesy** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |
 
### <a name="how-can-my-application-authenticate-to-keyvault-to-get-secrets"></a>Jak můžete svoji aplikaci ověřovat pro trezor klíčů, aby tajné kódy?
Tady jsou prostředky pro vaše aplikace za účelem získání přihlašovacích údajů pro ověření v trezoru klíčů:

A. Během úlohy sestavení a zabalení aplikace můžete vyžádat certifikát do vaší aplikace SF data balíčku a použijte ji k ověření do trezoru klíčů.
B. Pro škálovací sady virtuálních počítačů hostitele MSI povolené, vám umožní vytvářet jednoduché SetupEntryPoint prostředí PowerShell pro vaši aplikaci SF získat [přístupový token z koncového bodu MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)a potom [načíst tajné kódy z trezoru klíčů](https://docs.microsoft.com/powershell/module/az.keyvault/Get-AzureKeyVaultSecret)

## <a name="application-design"></a>Návrh aplikace

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Co je nejlepší způsob, jak data dotazu napříč oddíly spolehlivé kolekce?

Spolehlivé kolekce jsou obvykle [dělené](service-fabric-concepts-partitioning.md) umožňující horizontální navýšení kapacity pro vyšší výkon a propustnost. To znamená, že může být stavu pro určitou službu rozloženy desítky nebo stovky počítačů. K provádění operací v této úplné sady dat, máte několik možností:

- Vytvoření služby, který se dotazuje všech oddílů z jiné služby, aby se načetla požadovaná data.
- Vytvoření služby, které můžou přijímat data ze všech oddílů z jiné služby.
- Pravidelné zadávání dat z každé služby na externím úložišti. Tento přístup je jenom vhodné v případě dotazů, které provádíte nejsou součástí základní obchodní logiku.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Co je nejlepší způsob, jak dotazy na data napříč Moje actors?

Actors jsou navržené tak, jako nezávislé jednotek stavu a výpočetního výkonu, takže nedoporučujeme provádět dotazy široká stavu objektu actor v době běhu. Pokud máte třeba dotaz celé sady stavu objektu actor, měli byste zvážit buď:

- Nahrazení služby objektu actor stavovém modelu reliable services tak, aby počet síťových požadavků ke shromažďování všech dat ze počet objektů actor počtu oddílů ve službě.
- Navrhování vaší koncepce actorů je naprosto pravidelné zadávání jejich stav na externí úložiště pro jednodušší dotazování. Jako výše, tento přístup je jenom přijatelné, pokud při provádění dotazu nejsou požadována pro vaše chování za běhu.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Kolik dat můžu ukládat do spolehlivé kolekce?

Reliable services jsou obvykle rozdělená na oddíly, aby hodnota, kterou můžete ukládat je omezen pouze podle počtu počítačů, které máte v clusteru a množství paměti k dispozici na těchto počítačích.

Jako příklad předpokládejme, že máte spolehlivé kolekce v služba se 100 oddíly a 3 repliky ukládání objektů, které Průměrná velikost 1 kb. Nyní předpokládejme, že máte cluster 10 počítačů s 16gb paměti na jeden počítač. Pro zjednodušení a chcete-li buďte konzervativní, se předpokládá, že operační systém a systémové služby, modulu runtime Service Fabric a služeb využívat 6gb paměti, že byste museli opustit 10gb k dispozici na počítač, nebo 100 gb pro cluster.

Dodržujte při tom, že každý objekt musí být uloženy tři časy (jeden primární a dvě repliky), by mít dostatek paměti pro asi 35 miliónů objektů v kolekci, při fungování v celém rozsahu. Doporučujeme však je odolný vůči souběžných ztráty domény selhání a upgradovací doména, která představuje přibližně 1/3 kapacity a snižuje počet na přibližně 23 milionů.

Poznámka: Tento výpočet také předpokládá:

- Distribuci dat napříč oddíly je přibližně jednotné a že hlásíte zatížení metriky pro Cluster Resource Manager. Ve výchozím nastavení Service Fabric načte zůstatek na základě počtu repliky. V předchozím příkladu, který vložili 10 primární repliky a 20 sekundárních replik na každém uzlu v clusteru. To funguje dobře pro zatížení, která se rovnoměrně distribuuje napříč oddíly. Pokud není zatížení dokonce, musíte hlásit zatížení tak, aby Resource Manageru můžete společně pack menší repliky a povolit větší replik spotřebovávat více paměti na jednotlivých uzlů.

- Je nejistá spolehlivé služby pouze jednoho ukládání stavu v clusteru. Protože do clusteru můžete nasadit několik služeb, musíte být prostředky s vědomím, že každý musí spustit a spravovat svůj stav.

- Že samotného clusteru není stále se rozšiřující nebo zmenšení. Pokud chcete přidat více počítačů, Service Fabric se obnovit rovnováhu repliky využívat dodatečnou kapacitu, dokud počet počítačů převyšuje počet oddílů ve službě, protože jednotlivé repliky nemůžou zahrnovat počítače. Naopak pokud zmenšit velikost clusteru tak, že odeberete počítače, jsou zkomprimována těsněji repliky a mají nižší celkové kapacity.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Kolik dat můžu ukládat v prvek "actor"?

Stejně jako u modelu reliable services množství dat, které můžete ukládat v rámci služby objektu actor je omezen pouze celkové místo na disku a dostupné paměti mezi uzly v clusteru. Jednotlivé objekty actor jsou však nejúčinnější, když se používají k zapouzdření malé množství stavu a přidružené obchodní logiku. Jako obecné pravidlo musí mít prvek "actor" jednotlivých stavu, který se měří v kilobajtech.

## <a name="other-questions"></a>Další otázky

### <a name="how-does-service-fabric-relate-to-containers"></a>Jak Service Fabric souvisí s kontejnery?

Kontejnery nabízejí jednoduchý způsob, jak balíček služeb a jejich závislosti tak, spusťte konzistentně ve všech prostředích a mohou pracovat způsobem izolované na jednom počítači. Service Fabric nabízí způsob, jak nasadit a spravovat služby, včetně [služby, které byla zabalena v kontejneru](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Plánujete open source Service Fabric?

Máme open source části Service Fabric ([reliable services v rámci](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [reliable actors v rámci](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [integrace knihovny ASP.NET Core](https://github.com/Azure/service-fabric-aspnetcore), [ Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer), a [Service Fabric CLI](https://github.com/Azure/service-fabric-cli)) na Githubu a přijímat příspěvky komunity na těchto projektech. 

Jsme [nedávno oznámili](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/) , plánujeme open source modul runtime Service Fabric. V tuto chvíli máme [úložiště Service Fabric](https://github.com/Microsoft/service-fabric/) až na Githubu s Linuxem sestavení a testovací nástroje, což znamená, že můžete naklonujte úložiště, sestavení Service Fabric pro Linux, spusťte základní testy, hlásit problémy a odeslání žádosti o přijetí změn. Intenzivně pracujeme zobrazíte Windows sestavovacím prostředím migrovat, spolu s kompletního prostředí pro položky konfigurace.

Postupujte podle [Service Fabric blogu](https://blogs.msdn.microsoft.com/azureservicefabric/) další podrobnosti, jako jste oznámili.

## <a name="next-steps"></a>Další postup

Další informace o [základní koncepty Service Fabric](service-fabric-technical-overview.md) a [osvědčené postupy](service-fabric-best-practices-overview.md) ice concepts](service-fabric-technical-overview.md) prostředků infrastruktury a [osvědčené postupy](service-fabric-best-practices-overview.md)
