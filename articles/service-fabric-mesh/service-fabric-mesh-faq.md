---
title: Běžné otázky týkající se sítě Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o nejčastější dotazy a odpovědi pro Azure Service Fabric mřížky.
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 06/25/2018
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: b32af29a123ce4d070e1bb68b5a43ba6d0d2c5e1
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218470"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Nejčastější dotazy služby prostředků infrastruktury sítě
Azure Service Fabric Mesh je plně spravovaná služba, která vývojářům umožňuje nasazovat aplikace zajišťující mikroslužby, aniž by museli spravovat virtuální počítače, úložiště nebo sítě. Tento článek obsahuje odpovědi na nejčastější dotazy.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Jak ohlásit problém nebo dotaz?

Ptejte se, získejte odpovědi od odborníků Microsoftu a hlášení problémů v [úložiště GitHub service-fabric sítě preview](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Kvóty a nákladů

**Kolik stojí účast ve verzi preview?**

Aktuálně se nic neplatí pro nasazování aplikací nebo kontejnerů sítě ve verzi Preview. Ale doporučujeme vám odstranit prostředky, nasadit a ne nechat běžet je aktivně testování.

**Existuje nějaké omezení kvóty počtu jader a paměti RAM?**

Ano, jsou kvóty pro každé předplatné nastavte následujícím způsobem:

- Počet aplikací – 5 
- Počet jader na aplikaci – 12 
- Celkové paměti RAM na aplikaci – 48 GB 
- Síť a příchozího přenosu dat koncové body – 5  
- Svazky s Azure, které můžete připojit - 10 
- Počet replik služby – 3 
- Největší kontejner, který můžete nasadit je omezena na 4 jádra, 16 GB paměti RAM.
- Kontejnery v přírůstcích po 0,5 jádra, až do maximálního počtu jader 6, kterou můžete přidělit částečné jader.

**Jak dlouho je můžete nechat nasazené pro Moje aplikace?**

Právě jsme mají omezenou životnost aplikace do dvou dnů. Je to kvůli maximalizovat využití volných jader přidělené ve verzi Preview. V důsledku toho můžete jsou povolené jenom pro spuštění daného nasazení průběžně po dobu 48 hodin, po dobu se odstraní v systému. Pokud se to stát, můžete ověřit, že systém vypnout spuštěním `az mesh app show` v rozhraní příkazového řádku Azure a kontrolu příkaz vrátí-li `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Příklad: 

```cli
chackdan@Azure:~$ az mesh app show --resource-group myResourceGroup --name helloWorldApp
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

## <a name="supported-container-os-images"></a>Image kontejnerů podporovaný operační systém
Následující Image kontejneru operačních systémů lze použít při nasazování služby.

- Windows – windowsservercore a nanoserver
    - Windows Server 2016
    - Windows Server verze 1709
- Linux
    - Žádné známé omezení

## <a name="features-gaps-and-known-issues"></a>Funkce mezer a známé problémy

**Po nasazení aplikace, síťový prostředek s ním spojená zřejmě není IP adresa**

Existuje známý problém ještě dnes s IP adresou, chystá se po prodlevě. Zkontrolujte stav síťový prostředek za pár minut zobrazíte přidruženou IP adresu.

**Moje aplikace selhává pro přístup k prostředku správné síti nebo svazku**

Ve vašem aplikačním modelu budete muset použít úplné ID prostředku pro sítě a svazků bude mít přístup k přidružený prostředek. Zde je, jak to vypadá v ukázce rychlý start:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

**Nevidím aktuální model aplikace podporující způsob, jak šifrování Moje tajné kódy**

Ano, šifrování tajných kódů není podporován v aktuální verzi Private Preview. 

**DNS nebude fungovat stejným způsobem jako v mé vývojový cluster Service Fabric a sítě**

Existuje známý problém, kde bude pravděpodobně nutné odkazovat odlišně v místního vývojového clusteru a sítě Azure. Ve vašem místním vývojovém clusteru pomocí {služby serviceName}. aplikace {applicationName}. V Azure Service Fabric sítě pomocí {služby servicename}. Síť Azure aktuálně nepodporuje překlad názvů dns v aplikacích.

Pro další známých problémů s DNS se spouštěním vývojový cluster Service Fabric ve Windows 10, najdete tady: [kontejnery Windows ladění](/azure/service-fabric/service-fabric-how-to-debug-windows-containers).

**Při použití rozhraní příkazového řádku modulu, ImportError zobrazí tato chyba: nelze importovat název sdk_no_wait**

Pokud používáte starší verzi rozhraní příkazového řádku, než 2.0.30, zobrazí tato chyba-

```
cannot import name 'sdk_no_wait'
Traceback (most recent call last):
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\cli.py", line 193, in invoke cmd_result = self.invocation.execute(args)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 241, in execute self.commands_loader.load_arguments(command)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 201, in load_arguments self.command_table[command].load_arguments() # this loads the arguments via reflection
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 142, in load_arguments super(AzCliCommand, self).load_arguments()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\commands.py", line 76, in load_arguments cmd_args = self.arguments_loader()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 332, in default_arguments_loader op = handler or self.get_op_handler(operation)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 375, in get_op_handler op = import_module(mod_to_import)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\importlib_init_.py", line 126, in import_module return _bootstrap._gcd_import(name[level:], package, level)
File "", line 978, in _gcd_import
File "", line 961, in _find_and_load
File "", line 950, in _find_and_load_unlocked
File "", line 655, in _load_unlocked
File "", line 678, in exec_module
File "", line 205, in _call_with_frames_removed
File "C:\Users\annayak.azure\cliextensions\azure-cli-sbz\azext_sbz\custom.py", line 18, in 
from azure.cli.core.util import get_file_json, shell_safe_json_parse, sdk_no_wait
ImportError: cannot import name 'sdk_no_wait'.
```

**Při instalaci balíčku rozšíření rozhraní příkazového řádku se zobrazí název neshoda distribuce**

To neznamená, že nebyl nainstalován rozšíření. Byste měli stále moct používat příkazy rozhraní příkazového řádku bez problémů.

**Když mám horizontální navýšení kapacity, vidím, že jsou ovlivněny Moje kontejnerů, včetně Moje spuštěné ty**

Jedná se chybu, by měl vyřešen v příští aktualizace modulu runtime.

## <a name="next-steps"></a>Další postup

Další informace o Service Fabric mřížky, [přehled](service-fabric-mesh-overview.md).
