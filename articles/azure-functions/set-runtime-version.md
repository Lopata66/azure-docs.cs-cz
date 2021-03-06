---
title: Jak cílit na verze modulu runtime Azure Functions
description: Azure Functions podporuje více verzí modulu runtime. Naučte se, jak určit běhovou verzi aplikace Function App hostované v Azure.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 46bf7849888033b2bbb7e9b9669ee3eae4de10e9
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916520"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Jak cílit na verze modulu runtime Azure Functions

Aplikace Function App běží na konkrétní verzi Azure Functions runtime. Existují tři hlavní verze: [1. x, 2. x a 3. x](functions-versions.md). Ve výchozím nastavení se aplikace Function App vytvoří ve verzi 3. x modulu runtime. Tento článek vysvětluje, jak nakonfigurovat aplikaci Function App v Azure tak, aby běžela na zvolené verzi. Informace o tom, jak nakonfigurovat místní vývojové prostředí pro konkrétní verzi, naleznete v tématu [Code and test Azure Functions lokálně](functions-run-local.md).

Postup ručního cílení na konkrétní verzi závisí na tom, zda používáte systém Windows nebo Linux.

## <a name="automatic-and-manual-version-updates"></a>Automatické a ruční aktualizace verze

_Tato část se nevztahuje na spuštění aplikace Function App [v systému Linux](#manual-version-updates-on-linux)._

Azure Functions umožňuje cílit na konkrétní verzi modulu runtime ve Windows pomocí `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace ve Function App. Aplikace Function App je udržována v zadané hlavní verzi, dokud nebudete explicitně chtít přejít na novou verzi. Pokud zadáte pouze hlavní verzi, aplikace Function App se automaticky aktualizuje na nové podverze modulu runtime, jakmile budou k dispozici. Nové podverze by neměly zavádět podstatné změny. 

Pokud zadáte vedlejší verzi (například "2.0.12345"), aplikace Function App se přiřadí k dané konkrétní verzi, dokud ji explicitně nezměníte. Starší dílčí verze se pravidelně odstraňují z produkčního prostředí. V takovém případě funguje aplikace Function App na nejnovější verzi namísto sady verzí v nástroji `FUNCTIONS_EXTENSION_VERSION` . Z tohoto důvodu byste měli rychle vyřešit všechny problémy s aplikací Function App, které vyžadují konkrétní dílčí verzi, takže můžete cílit na hlavní verzi. Odebrání podverze jsou oznámeny v [App Service oznámení](https://github.com/Azure/app-service-announcements/issues).

> [!NOTE]
> Pokud připnete k určité hlavní verzi Azure Functions a potom se pokusíte publikovat do Azure pomocí sady Visual Studio, zobrazí se dialogové okno s výzvou k aktualizaci na nejnovější verzi nebo zrušení publikování. Pokud se tomu chcete vyhnout, přidejte `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` do `.csproj` souboru vlastnost.

Když je k dispozici veřejně dostupná nová verze, zobrazí se výzva na portálu, která vám umožní přejít až k této verzi. Po přesunu na novou verzi můžete `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace vždy použít k přechodu zpět na předchozí verzi.

V následující tabulce jsou uvedeny `FUNCTIONS_EXTENSION_VERSION` hodnoty pro jednotlivé hlavní verze, které umožňují automatické aktualizace:

| Hlavní verze | `FUNCTIONS_EXTENSION_VERSION` osa |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| verze  | `~1` |

Změna verze modulu runtime způsobuje restartování aplikace Function App.

## <a name="view-and-update-the-current-runtime-version"></a>Zobrazení a aktualizace aktuální verze modulu runtime

_Tato část se nevztahuje na spuštění aplikace Function App [v systému Linux](#manual-version-updates-on-linux)._

Můžete změnit verzi modulu runtime, kterou používá aplikace Function App. Z důvodu potenciálních změn můžete změnit pouze verzi modulu runtime ještě před vytvořením funkcí ve vaší aplikaci Function App. 

> [!IMPORTANT]
> I když je verze modulu runtime určena `FUNCTIONS_EXTENSION_VERSION` nastavením, měli byste tuto změnu provést v Azure Portal a nikoli změnou nastavení přímo. Důvodem je to, že portál ověří vaše změny a v případě potřeby provede další související změny.

# <a name="portal"></a>[Azure Portal](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Pomocí Azure Portal nemůžete změnit verzi modulu runtime aplikace Function App, která již obsahuje funkce.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Můžete také zobrazit a nastavit v rozhraní příkazového `FUNCTIONS_EXTENSION_VERSION` řádku Azure CLI.  

Pomocí Azure CLI zobrazte aktuální verzi modulu runtime pomocí příkazu [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

V tomto kódu nahraďte `<function_app>` názvem vaší aplikace Function App. Nahraďte také `<my_resource_group>` názvem skupiny prostředků vaší aplikace Function App. 

Zobrazí se `FUNCTIONS_EXTENSION_VERSION` následující výstup, který byl zkrácen pro přehlednost:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

`FUNCTIONS_EXTENSION_VERSION`Nastavení v aplikaci Function App můžete aktualizovat pomocí příkazu [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

Nahraďte `<FUNCTION_APP>` názvem vaší aplikace Function App. Nahraďte také `<RESOURCE_GROUP>` názvem skupiny prostředků vaší aplikace Function App. Nahraďte také `<VERSION>` buď konkrétní verzí, nebo `~3` , `~2` nebo `~1` .

Tento příkaz můžete spustit z [Azure Cloud Shell](../cloud-shell/overview.md) výběrem možnosti **vyzkoušet** v předchozím příkladu kódu. Pomocí rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) můžete také spustit tento příkaz po provedení příkazu [AZ Login](/cli/azure/reference-index#az-login) pro přihlášení.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete kontrolovat Azure Functions runtime, použijte tuto rutinu: 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

Nahraďte `<FUNCTION_APP>` názvem vaší aplikace Function App a `<RESOURCE_GROUP>` . Aktuální hodnota `FUNCTIONS_EXTENSION_VERSION` nastavení se vrátí v zatřiďovací tabulce.

Pomocí následujícího skriptu změňte modul runtime Functions:

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

Stejně jako dřív nahraďte `<FUNCTION_APP>` názvem vaší aplikace Function App a `<RESOURCE_GROUP>` názvem skupiny prostředků. Nahraďte také `<VERSION>` konkrétní verzí nebo hlavní verzí, například `~2` nebo `~3` . V vrácené zatřiďovací tabulce můžete ověřit aktualizovanou hodnotu `FUNCTIONS_EXTENSION_VERSION` nastavení. 

---

Aplikace Function App se restartuje po provedení změny v nastavení aplikace.

## <a name="manual-version-updates-on-linux"></a>Ruční aktualizace verze v systému Linux

Pokud chcete aplikaci funkce systému Linux připnout na konkrétní verzi hostitele, zadejte adresu URL obrázku do pole ' LinuxFxVersion ' v konfiguraci lokality. Příklad: Pokud chceme připnout aplikaci funkcí Node 10 k vyslovení verze 3.0.13142 hostitele –

Pro **Linux App Service nebo elastické aplikace úrovně Premium** – nastavte `LinuxFxVersion` na `DOCKER|mcr.microsoft.com/azure-functions/node:3.0.13142-node10-appservice` .

Pro **aplikace pro Linux spotřeby** – `LinuxFxVersion` nastavte `DOCKER|mcr.microsoft.com/azure-functions/mesh:3.0.13142-node10` na.


# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-linux)

Z Azure CLI můžete zobrazit a nastavit `LinuxFxVersion` .  

Pomocí Azure CLI zobrazte aktuální verzi modulu runtime pomocí příkazu [AZ functionapp config show](/cli/azure/functionapp/config) .

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group>
```

V tomto kódu nahraďte `<function_app>` názvem vaší aplikace Function App. Nahraďte také `<my_resource_group>` názvem skupiny prostředků vaší aplikace Function App. 

Zobrazí se `linuxFxVersion` následující výstup, který byl zkrácen pro přehlednost:

```output
{
  ...

  "kind": null,
  "limits": null,
  "linuxFxVersion": <LINUX_FX_VERSION>,
  "loadBalancing": "LeastRequests",
  "localMySqlEnabled": false,
  "location": "West US",
  "logsDirectorySizeLimit": 35,
   ...
}
```

`linuxFxVersion`Nastavení v aplikaci Function App můžete aktualizovat pomocí příkazu [AZ functionapp config set](/cli/azure/functionapp/config) .

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

Nahraďte `<FUNCTION_APP>` názvem vaší aplikace Function App. Nahraďte také `<RESOURCE_GROUP>` názvem skupiny prostředků vaší aplikace Function App. Nahraďte také `<LINUX_FX_VERSION>` hodnotami, které jsou vysvětleny výše.

Tento příkaz můžete spustit z [Azure Cloud Shell](../cloud-shell/overview.md) výběrem možnosti **vyzkoušet** v předchozím příkladu kódu. Pomocí rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) můžete také spustit tento příkaz po provedení příkazu [AZ Login](/cli/azure/reference-index#az-login) pro přihlášení.


Podobně bude aplikace Function App restartována po provedení změny v konfiguraci lokality.

> [!NOTE]
> Všimněte si, že nastavení `LinuxFxVersion` na adresu URL obrázku přímo pro aplikace pro spotřebu se odsouhlasí se zástupnými symboly a dalšími optimalizacemi pro studený start.

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Cílení na modul runtime 2,0 ve vašem místním vývojovém prostředí](functions-run-local.md)

> [!div class="nextstepaction"]
> [Verze modulu runtime najdete v poznámkách k verzi.](https://github.com/Azure/azure-webjobs-sdk-script/releases)
