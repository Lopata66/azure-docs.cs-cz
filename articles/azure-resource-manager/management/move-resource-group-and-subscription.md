---
title: Přesunutí prostředků do nového předplatného nebo skupiny prostředků
description: K přesunutí prostředků do nové skupiny prostředků nebo předplatného použijte Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cd05fe045532ee1b1f1fb88e502d786daabf9365
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319550"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Přesunutí prostředků do nové skupiny prostředků nebo předplatného

V tomto článku se dozvíte, jak přesunout prostředky Azure do jiného předplatného Azure nebo do jiné skupiny prostředků v rámci stejného předplatného. K přesunu prostředků můžete použít Azure Portal, Azure PowerShell, Azure CLI nebo rozhraní REST API.

Během operace přesunutí dojde ke zamčení zdrojové skupiny i cílové skupiny. Operace zápisu a odstranění jsou ve skupinách prostředků blokované, dokud se přesun nedokončí. Tento zámek znamená, že nemůžete přidat, aktualizovat nebo odstranit prostředky ve skupinách prostředků. Neznamená to, že se prostředky zmrazují. Pokud například přesunete SQL Server a jeho databázi do nové skupiny prostředků, aplikace, která využívá databázi, nebude mít žádný výpadek. Může i nadále číst a zapisovat do databáze. Zámek může být poslední po dobu maximálně čtyř hodin, ale většina přesunů se dokončí za mnohem kratší dobu.

Přesunutím prostředku dojde pouze k jeho přesunu do nové skupiny prostředků nebo do nového předplatného. Umístění prostředku se nezmění.

## <a name="checklist-before-moving-resources"></a>Kontrolní seznam před přesunutím prostředků

Před přesunem prostředků je potřeba provést několik důležitých kroků. Ověřením těchto podmínek se můžete vyhnout chybám.

1. Prostředky, které chcete přesunout, musí podporovat operaci přesunu. Seznam prostředků, které podporují přesun, najdete v tématu [Podpora operací přesunutí pro prostředky](move-support-resources.md).

1. Některé služby mají při přesouvání prostředků specifická omezení nebo požadavky. Pokud přesouváte některou z následujících služeb, před přesunem si Projděte tyto pokyny.

   * Pokud používáte centrum Azure Stack, nemůžete přesouvat prostředky mezi skupinami.
   * [Pokyny pro přesunutí App Services](./move-limitations/app-service-move-limitations.md)
   * [Pokyny pro přesunutí Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Model nasazení Classic – pokyny pro přesun](./move-limitations/classic-model-move-limitations.md) – klasický výpočetní prostředí, klasické úložiště, klasické virtuální sítě a Cloud Services
   * [Pokyny k přesunu sítě](./move-limitations/networking-move-limitations.md)
   * [Pokyny pro přesunutí Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Pokyny pro přesunutí Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)

1. Pokud přesunete prostředek, který má přiřazenou roli Azure, přímo k prostředku (nebo podřízenému prostředku), přiřazení role se nepřesune a bude osamocené. Po přesunutí musíte znovu vytvořit přiřazení role. Nakonec se automaticky odebere přiřazení osamocené role, ale je osvědčeným postupem odebrání přiřazení role před přesunutím prostředku.

    Informace o tom, jak spravovat přiřazení rolí, najdete v tématech [seznam přiřazení rolí Azure](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) a [Přidání nebo odebrání přiřazení rolí Azure](../../role-based-access-control/role-assignments-portal.md).

1. Zdrojové a cílové odběry musí být aktivní. Pokud máte potíže s povolením zakázaného účtu, [vytvořte žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Jako typ problému vyberte **Správa předplatného** .

1. Zdrojové a cílové odběry musí existovat v rámci stejného [Azure Active Directory tenanta](../../active-directory/develop/quickstart-create-new-tenant.md). Pokud chcete ověřit, že obě předplatná mají stejné ID tenanta, použijte Azure PowerShell nebo Azure CLI.

   Pro Azure PowerShell použijte:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Pokud používáte Azure CLI, použijte:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Pokud se ID klientů pro zdrojové a cílové odběry neshodují, použijte k sjednocení ID klientů následující metody:

   * [Přenos vlastnictví předplatného služby Azure na jiný účet](../../cost-management-billing/manage/billing-subscription-transfer.md)
   * [Přiřazení nebo přidání předplatného Azure do služby Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Cílové předplatné musí být registrováno pro poskytovatele přesouvaného prostředku. V takovém případě se zobrazí chyba s oznámením, že **předplatné není zaregistrované pro typ prostředku**. Tato chyba se může zobrazit při přesunu prostředku do nového předplatného, ale toto předplatné se pro tento typ prostředku nikdy nepoužilo.

   Pro prostředí PowerShell použijte k získání stavu registrace následující příkazy:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Chcete-li zaregistrovat poskytovatele prostředků, použijte:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Pro Azure CLI pomocí následujících příkazů Získejte stav registrace:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Chcete-li zaregistrovat poskytovatele prostředků, použijte:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Účet, který přesouvá prostředky, musí mít alespoň následující oprávnění:

   * **Microsoft. Resources/Subscriptions/resourceGroups/moveResources/Action** ve zdrojové skupině prostředků.
   * **Microsoft. Resources/Subscriptions/resourceGroups/Write** v cílové skupině prostředků.

1. Před přesunutím prostředků ověřte kvóty předplatného pro předplatné, na které prostředky přesouváte. Pokud se prostředky přesunou, znamená to, že předplatné překročí své limity, budete muset zkontrolovat, jestli můžete požádat o zvýšení kvóty. Seznam omezení a informace o tom, jak požádat o zvýšení, najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).

1. **Pro přesun mezi předplatnými musí být prostředek a jeho závislé prostředky umístěny ve stejné skupině prostředků a musí být přesunuty dohromady.** Například virtuální počítač se spravovanými disky by vyžadoval, aby se virtuální počítač a spravované disky přesunuly společně s dalšími závislými prostředky.

   Pokud přesouváte prostředek do nového předplatného, zkontrolujte, zda prostředek obsahuje nějaké závislé prostředky a zda se nachází ve stejné skupině prostředků. Pokud prostředky nejsou ve stejné skupině prostředků, zkontrolujte, jestli se prostředky dají zkombinovat do stejné skupiny prostředků. Pokud ano, převeďte všechny tyto prostředky do stejné skupiny prostředků pomocí operace přesunutí napříč skupinami prostředků.

   Další informace najdete v tématu [scénář pro přesun mezi předplatnými](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Scénář pro přesun mezi předplatnými

Přesunutí prostředků z jednoho předplatného na jiný je proces se třemi kroky:

![scénář přesunutí mezi předplatnými](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

Pro ilustraci máme jenom jeden závislý prostředek.

* Krok 1: Pokud jsou závislé prostředky distribuované napříč různými skupinami prostředků, přesuňte je nejdřív do jedné skupiny prostředků.
* Krok 2: přesunutí prostředku a závislých prostředků ze zdrojového předplatného do cílového předplatného.
* Krok 3: Volitelně můžete závislé prostředky znovu distribuovat do různých skupin prostředků v rámci cílového předplatného.

## <a name="validate-move"></a>Ověřit přesun

[Operace ověřit přesunutí](/rest/api/resources/resources/validatemoveresources) vám umožní otestovat scénář přesunutí bez skutečného přesunu prostředků. Pomocí této operace zkontrolujete, zda se přesun nezdaří. Ověřování je automaticky voláno při odeslání žádosti o přesunutí. Tuto operaci použijte pouze v případě, že potřebujete předem určit výsledky. K provedení této operace potřebujete:

* název zdrojové skupiny prostředků
* ID prostředku cílové skupiny prostředků
* ID prostředku pro každý prostředek, který se má přesunout
* [přístupový token](/rest/api/azure/#acquire-an-access-token) pro váš účet

Odeslat následující požadavek:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

S textem žádosti:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Pokud je požadavek správně naformátovaný, operace vrátí:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

Stavový kód 202 indikuje, že žádost o ověření byla přijata, ale ještě nebyla určena, pokud operace přesunutí proběhne úspěšně. `location`Hodnota obsahuje adresu URL, kterou použijete ke kontrole stavu dlouhotrvající operace.  

Chcete-li zjistit stav, odešlete následující požadavek:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

I když je operace stále spuštěná, budete nadále dostávat stavový kód 202. Počkejte, než se počet sekund značí v `retry-after` hodnotě, a potom operaci opakujte. Pokud se operace přesunutí úspěšně ověří, obdržíte stavový kód 204. Pokud se ověření přesunutí nepovede, zobrazí se chybová zpráva, například:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Použití portálu

Chcete-li přesunout prostředky, vyberte skupinu prostředků, která obsahuje tyto prostředky.

Když si zobrazíte skupinu prostředků, možnost přesunout je zakázaná.

:::image type="content" source="./media/move-resource-group-and-subscription/move-first-view.png" alt-text="možnost přesunutí zakázána":::

Chcete-li povolit možnost přesunout, vyberte prostředky, které chcete přesunout. Chcete-li vybrat všechny prostředky, zaškrtněte políčko v horní části seznamu. Případně vyberte prostředky jednotlivě. Po výběru prostředků je povolena možnost přesunout.

:::image type="content" source="./media/move-resource-group-and-subscription/select-resources.png" alt-text="možnost přesunutí zakázána":::

Vyberte tlačítko **přesunout** .

:::image type="content" source="./media/move-resource-group-and-subscription/move-options.png" alt-text="možnost přesunutí zakázána":::

Toto tlačítko nabízí tři možnosti:

* Přejděte do nové skupiny prostředků.
* Přejděte k novému předplatnému.
* Přesunout do nové oblasti Pokud chcete změnit oblasti, přečtěte si téma [Přesunutí prostředků mezi oblastmi (ze skupiny prostředků)](../../resource-mover/move-region-within-resource-group.md?toc=/azure/azure-resource-manager/management/toc.json).

Vyberte, jestli přesouváte prostředky do nové skupiny prostředků nebo do nového předplatného.

Vyberte cílovou skupinu prostředků. Potvrďte, že potřebujete aktualizovat skripty pro tyto prostředky a vyberte **OK**. Pokud jste vybrali možnost přesunout se k novému předplatnému, musíte také vybrat cílové předplatné.

:::image type="content" source="./media/move-resource-group-and-subscription/move-destination.png" alt-text="možnost přesunutí zakázána":::

Po ověření, že se prostředky dají přesunout, se zobrazí oznámení o běhu operace přesunutí.

:::image type="content" source="./media/move-resource-group-and-subscription/move-notification.png" alt-text="možnost přesunutí zakázána":::

Po dokončení budete upozorněni na výsledek.

Pokud se zobrazí chyba, přečtěte si téma [řešení potíží s přesunutím prostředků Azure do nové skupiny prostředků nebo předplatného](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Použití Azure Powershell

Pokud chcete přesunout existující prostředky do jiné skupiny prostředků nebo předplatného, použijte příkaz [Move-AzResource](/powershell/module/az.resources/move-azresource) . Následující příklad ukazuje, jak přesunout několik prostředků do nové skupiny prostředků.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Chcete-li přejít k novému předplatnému, zahrňte hodnotu `DestinationSubscriptionId` parametru.

Pokud se zobrazí chyba, přečtěte si téma [řešení potíží s přesunutím prostředků Azure do nové skupiny prostředků nebo předplatného](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Použití Azure CLI

Pokud chcete přesunout existující prostředky do jiné skupiny prostředků nebo předplatného, použijte příkaz [AZ Resource Move](/cli/azure/resource#az-resource-move) . Zadejte ID prostředků, které se mají přesunout. Následující příklad ukazuje, jak přesunout několik prostředků do nové skupiny prostředků. V `--ids` parametru zadejte mezerami oddělený seznam ID prostředků, který chcete přesunout.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Pokud chcete přejít k novému předplatnému, zadejte `--destination-subscription-id` parametr.

Pokud se zobrazí chyba, přečtěte si téma [řešení potíží s přesunutím prostředků Azure do nové skupiny prostředků nebo předplatného](troubleshoot-move.md).

## <a name="use-rest-api"></a>Použití rozhraní REST API

Pokud chcete přesunout existující prostředky do jiné skupiny prostředků nebo předplatného, použijte operaci [přesunout prostředky](/rest/api/resources/Resources/MoveResources) .

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

V textu žádosti zadáte cílovou skupinu prostředků a prostředky, které se mají přesunout.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Pokud se zobrazí chyba, přečtěte si téma [řešení potíží s přesunutím prostředků Azure do nové skupiny prostředků nebo předplatného](troubleshoot-move.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: moje operace přesunutí prostředků, která obvykle trvá několik minut, je spuštěná skoro po celou hodinu. Je něco špatné?**

Přesunutí prostředku je složitá operace, která má různé fáze. Může zahrnovat víc než jenom poskytovatele prostředků u prostředku, který se pokoušíte přesunout. Vzhledem k závislostem mezi poskytovateli prostředků Azure Resource Manager umožňuje dokončení operace 4 hodiny. Toto časové období dává poskytovatelům prostředků možnost obnovení z přechodných problémů. Pokud je vaše žádost o přesun v průběhu čtyř hodin, operace se bude pokoušet dokončit a může být stále úspěšná. Zdrojové a cílové skupiny prostředků jsou během této doby uzamčeny, aby nedocházelo k problémům s konzistencí.

**Otázka: Proč se skupina prostředků uzamkl po dobu 4 hodin během přesunu prostředků?**

Žádost o přesunutí je povolená maximálně na čtyři hodiny. Aby se zabránilo změnám přesouvaných prostředků, jsou zdrojové i cílové skupiny prostředků zamčené po dobu trvání přesunutí prostředku.

V žádosti o přesunutí jsou dvě fáze. V první fázi se prostředek přesune. Ve druhé fázi se oznámení odesílají jiným poskytovatelům prostředků závislým na přemístění prostředku. Skupina prostředků může být uzamčena po dobu celé čtyři hodiny, když poskytovatel prostředků dojde k chybě v obou fázích. Během povoleného času se Správce prostředků opakuje neúspěšný krok.

Pokud se prostředek nedá přesunout do čtyř hodin, Správce prostředků odemkne obě skupiny prostředků. Prostředky, které se úspěšně přesunuly, se nacházejí v cílové skupině prostředků. Prostředky, které se nepodařilo přesunout, jsou ponechány ve zdrojové skupině prostředků.

**Otázka: Jaké jsou důsledky uzamčení zdrojových a cílových skupin prostředků během přesunu prostředku?**

Zámek vám znemožní odstranit buď skupinu prostředků, vytvoření nového prostředku v obou skupinách prostředků, nebo odstranit některý z prostředků, které jsou součástí přesunutí.

Následující obrázek ukazuje chybovou zprávu z Azure Portal, když se uživatel pokusí odstranit skupinu prostředků, která je součástí probíhajícího přesunu.

![Přesunout chybovou zprávu pokus o odstranění](./media/move-resource-group-and-subscription/move-error-delete.png)

**Otázka: co znamená kód chyby "MissingMoveDependentResources"?**

Při přesunu prostředku musí být závislé prostředky buď v cílové skupině prostředků nebo předplatném, nebo být součástí žádosti o přesun. Kód chyby MissingMoveDependentResources se zobrazí, když závislý prostředek nesplňuje tento požadavek. Chybová zpráva obsahuje podrobnosti o závislém prostředku, který musí být zahrnut v žádosti o přesunutí.

Například přesun virtuálního počítače může vyžadovat přesun sedmi typů prostředků se třemi různými poskytovateli prostředků. Poskytovatelé prostředků a typy jsou:

* Microsoft.Compute

  * virtualMachines
  * disků
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

Další běžný příklad zahrnuje přesun virtuální sítě. Možná budete muset přesunout několik dalších prostředků přidružených k této virtuální síti. Žádost o přesun by mohla vyžadovat přesunutí veřejných IP adres, směrovacích tabulek, bran virtuální sítě, skupin zabezpečení sítě a dalších.

**Otázka: Proč nemůžu přesunout některé prostředky v Azure?**

V současné době se nepodporují přesun všech prostředků v Azure. Seznam prostředků, které podporují přesun, najdete v tématu [o podpoře operací přesunutí pro prostředky](move-support-resources.md).

## <a name="next-steps"></a>Další kroky

Seznam prostředků, které podporují přesun, najdete v tématu [Podpora operací přesunutí pro prostředky](move-support-resources.md).
