---
title: Vytvoření nebo Správa účtů pro integraci B2B – Azure Logic Apps
description: Vytváření, propojení a Správa integračních účtů pro podnikovou integraci s Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.workload: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: de184b189c00cce64932a3ab17d41ed84e197a7e
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534088"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Vytváření a Správa integračních účtů pro integraci B2B Enterprise v Azure Logic Apps

Abyste mohli začít vytvářet [řešení podnikové integrace a B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) s využitím [Azure Logic Apps](../logic-apps/logic-apps-overview.md), musíte vytvořit účet integrace, což je samostatný prostředek Azure, který poskytuje zabezpečený, škálovatelný a spravovatelný kontejner pro artefakty integrace, které definujete a používáte v rámci pracovních postupů vašich aplikací logiky.

Můžete například vytvářet, ukládat a spravovat artefakty B2B, jako jsou obchodní partneři, smlouvy, mapy, schémata, certifikáty a konfigurace služby Batch. Před tím, než aplikace logiky může s těmito artefakty pracovat a používat Logic Apps B2B konektory, je také nutné [propojit účet pro integraci](#link-account) s vaší aplikací logiky. Váš účet pro integraci i aplikace logiky musí existovat ve *stejném* umístění nebo oblasti.

> [!TIP]
> Pokud chcete vytvořit účet pro integraci v [prostředí integrační služby](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), přečtěte si téma [Vytvoření integračních účtů v ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

V tomto tématu se dozvíte, jak provádět tyto úlohy:

* Vytvořte účet pro integraci.
* Propojit účet pro integraci s aplikací logiky.
* Změňte cenovou úroveň účtu pro integraci.
* Odpojte účet pro integraci z aplikace logiky.
* Přesuňte účet pro integraci do jiné skupiny prostředků nebo předplatného Azure.
* Odstraňte účet pro integraci.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Vytvoření účtu integrace

Pro tuto úlohu můžete použít buď Azure Portal podle kroků v této části, [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)nebo [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. V hlavní nabídce Azure vyberte **Vytvořit prostředek**. Do vyhledávacího pole zadejte jako filtr "účet pro integraci" a vyberte účet pro **integraci**.

   ![Vytvořit nový účet pro integraci](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. V části **účet integrace**vyberte **vytvořit**.

   ![Pokud chcete vytvořit účet pro integraci, vyberte Přidat.](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Zadejte informace o účtu pro integraci:

   ![Zadání podrobností o účtu pro integraci](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název** | Ano | <*Integration-Account-name*> | Název vašeho účtu pro integraci, který může obsahovat jenom písmena, číslice, spojovníky (`-`), podtržítka (`_`), kulaté závorky (`(`, `)`) a tečky (`.`). V tomto příkladu se používá "Fabrikam-Integration". |
   | **Předplatné** | Ano | <*název_předplatného_Azure*> | Název vašeho předplatného Azure |
   | **Skupina prostředků** | Ano | <*Azure-Resource-Group-name*> | Název [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) , která se má použít pro uspořádání souvisejících prostředků V tomto příkladu vytvořte novou skupinu prostředků s názvem "FabrikamIntegration-RG". |
   | **Cenová úroveň** | Ano | <> *na úrovni cen* | Cenová úroveň pro účet pro integraci, kterou můžete později změnit. V tomto příkladu vyberte **Free (zdarma**). Další informace najdete v těchto tématech: <p>[cenový model - Logic Apps](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>[omezení a konfigurace - Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>[ceny - Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Umístění** | Ano | <*Azure – oblast*> | Oblast, kam se mají ukládat metadata účtu pro integraci Buď vyberte stejné umístění jako aplikace logiky, nebo vytvořte své aplikace logiky ve stejném umístění jako váš účet pro integraci. V tomto příkladu použijte "Západní USA". <p>**Poznámka**: Pokud chcete vytvořit účet pro integraci v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), vyberte tento ISE jako umístění. Další informace najdete v tématu [Vytvoření integračních účtů v ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Log Analytics** | Ne | Vypnuto, zapnuto | Pro tento příklad nechejte nastavení **vypnuto** . |
   |||||

1. Až budete hotovi, vyberte **vytvořit**.

   Po dokončení nasazení Azure otevře účet pro integraci.

   ![Azure otevře účet pro integraci](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Předtím, než může vaše aplikace logiky použít váš účet pro integraci, postupujte podle dalších kroků a propojte účet pro integraci a aplikaci logiky.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Odkaz na aplikaci logiky

Aby vaše aplikace logiky měla přístup k účtu pro integraci, který obsahuje vaše artefakty B2B, musíte nejdřív propojit účet pro integraci s vaší aplikací logiky. Aplikace logiky i účet pro integraci musí existovat ve stejné oblasti. K dokončení této úlohy můžete použít Azure Portal. Pokud používáte Visual Studio a vaše aplikace logiky je v [projektu skupiny prostředků Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md), můžete [propojit aplikaci logiky s účtem pro integraci pomocí sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. V Azure Portal Najděte a otevřete aplikaci logiky.

1. V [Azure Portal](https://portal.azure.com)otevřete existující aplikaci logiky nebo vytvořte novou aplikaci logiky.

1. V nabídce aplikace logiky v části **Nastavení**vyberte **Nastavení pracovního postupu**. V části **účet integrace**otevřete seznam **Vybrat účet pro integraci** . Vyberte účet pro integraci, který chcete propojit s vaší aplikací logiky.

   ![Vyberte účet pro integraci.](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Propojení dokončíte výběrem **Uložit**.

   ![Vyberte účet pro integraci.](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Po úspěšném propojení účtu pro integraci zobrazí Azure zprávu s potvrzením.

   ![Azure potvrdí úspěšné propojení](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Aplikace logiky teď může použít artefakty v účtu integrace a konektory B2B, jako je ověřování XML a kódování a dekódování plochého souboru.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Změna cenové úrovně

Pokud chcete zvýšit [omezení](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) pro účet pro integraci, můžete [upgradovat na vyšší cenovou úroveň](#upgrade-pricing-tier), pokud je dostupná. Můžete například upgradovat z úrovně Free na úroveň Basic nebo na úroveň Standard. Můžete také [downgradovat na nižší úroveň](#downgrade-pricing-tier), pokud je k dispozici. Další informace o cenách najdete v těchto tématech:

* [Ceny Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Logic Apps cenový model](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Upgradovat cenovou úroveň

Tuto změnu můžete provést buď pomocí Azure Portal podle kroků v této části, nebo pomocí [Azure CLI](#upgrade-tier-azure-cli).

#### <a name="azure-portal"></a>portál Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. Do hlavního pole Azure Search zadejte jako filtr "účty pro integraci" a vyberte **účty pro integraci**.

   ![Najít účet pro integraci](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure zobrazí všechny účty pro integraci ve vašich předplatných Azure.

1. V části **účty integrace**vyberte účet pro integraci, který chcete přesunout. V nabídce účtu pro integraci vyberte **Přehled**.

   ![V nabídce účet pro integraci vyberte přehled.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. V podokně Přehled vyberte možnost **upgradovat cenovou úroveň**, která obsahuje seznam dostupných vyšších úrovní. Když vyberete určitou úroveň, projeví se okamžitě změna.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>Azure CLI

1. Pokud jste to ještě neudělali, [nainstalujte požadavky rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. V Azure Portal otevřete prostředí Azure [**Cloud Shell**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) .

   ![Otevření služby Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Na příkazovém řádku zadejte [příkaz **AZ Resource** command](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)a nastavte `skuName` na vyšší úroveň, kterou chcete.

   ```Azure CLI
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Pokud máte například úroveň Basic, můžete nastavit `skuName` na `Standard`:

   ```Azure CLI
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Cenová úroveň downgrade

Pokud chcete tuto změnu provést, použijte rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Pokud jste to ještě neudělali, [nainstalujte požadavky rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. V Azure Portal otevřete prostředí Azure [**Cloud Shell**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) .

   ![Otevření služby Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Na příkazovém řádku zadejte [příkaz **AZ Resource** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update) a nastavte `skuName` na nižší úroveň, kterou chcete.

   ```Azure CLI
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Pokud máte například úroveň Standard, můžete nastavit `skuName` na `Basic`:

   ```Azure CLI
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Zrušení propojení z aplikace logiky

Pokud chcete aplikaci logiky propojit s jiným účtem pro integraci nebo už nepoužíváte účet pro integraci s vaší aplikací logiky, odstraňte odkaz pomocí Azure Resource Explorer.

1. Otevřete okno prohlížeče a přejít na [Azure Resource Explorer (https://resources.azure.com)](https://resources.azure.com). Přihlaste se pomocí stejných přihlašovacích údajů k účtu Azure.

   ![Průzkumník prostředků Azure](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Do vyhledávacího pole zadejte název vaší aplikace logiky, abyste mohli najít a vybrat aplikaci logiky.

   ![Hledání a výběr aplikace logiky](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. V záhlaví Průzkumníka vyberte možnost **čtení/zápis**.

   ![Zapnout režim pro čtení a zápis](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Na kartě **data** vyberte **Upravit**.

   ![Na kartě data vyberte Upravit.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. V editoru vyhledejte objekt `integrationAccount` a odstraňte tuto vlastnost, která má tento formát:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Příklad:

   ![Najít objekt "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Na kartě **data** klikněte **na tlačítko Uložit** a uložte provedené změny.

   ![Pokud chcete změny uložit, vyberte PUT.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. V Azure Portal vyhledejte a vyberte svou aplikaci logiky. V části **Nastavení pracovního postupu**vaší aplikace ověřte, zda je vlastnost **účet integrace** nyní zobrazená jako prázdná.

   ![Ověřte, že účet pro integraci není propojený.](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Přesunout účet pro integraci

Účet pro integraci můžete přesunout do jiné skupiny prostředků Azure nebo do předplatného Azure. Když přesunete prostředky, Azure vytvoří nová ID prostředků, takže nezapomeňte místo toho použít nová ID a aktualizovat skripty nebo nástroje přidružené k přesunutým prostředkům. Pokud chcete změnit předplatné, musíte zadat taky existující nebo novou skupinu prostředků.

Pro tuto úlohu můžete použít buď Azure Portal podle kroků v této části nebo v [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. Do hlavního pole Azure Search zadejte jako filtr "účty pro integraci" a vyberte **účty pro integraci**.

   ![Najít účet pro integraci](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure zobrazí všechny účty pro integraci ve vašich předplatných Azure.

1. V části **účty integrace**vyberte účet pro integraci, který chcete přesunout. V nabídce účtu pro integraci vyberte **Přehled**.

   ![V nabídce účet pro integraci vyberte přehled.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Vedle pole název **skupiny prostředků** nebo **předplatného**vyberte **změnit**.

   ![Změna skupiny prostředků nebo předplatného](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Vyberte všechny související prostředky, které chcete přesunout.

1. V závislosti na vašem výběru použijte následující postup ke změně skupiny prostředků nebo předplatného:

   * Skupina prostředků: v seznamu **Skupina prostředků** vyberte cílovou skupinu prostředků. Nebo pokud chcete vytvořit jinou skupinu prostředků, vyberte **vytvořit novou skupinu prostředků**.

   * Předplatné: v seznamu **předplatné** vyberte cílové předplatné. V seznamu **Skupina prostředků** vyberte cílovou skupinu prostředků. Nebo pokud chcete vytvořit jinou skupinu prostředků, vyberte **vytvořit novou skupinu prostředků**.

1. Abyste potvrdili, že všechny skripty nebo nástroje přidružené k přesunutým prostředkům nebudou fungovat, dokud je neaktualizujete pomocí nových ID prostředků, zaškrtněte políčko potvrzení a pak vyberte **OK**.

1. Po dokončení nezapomeňte aktualizovat všechny skripty s novými ID prostředků pro přesunuté prostředky.  

## <a name="delete-integration-account"></a>Odstranit účet pro integraci

Pro tuto úlohu můžete použít buď Azure Portal podle kroků v této části, [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete)nebo [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. Do hlavního pole Azure Search zadejte jako filtr "účty pro integraci" a vyberte **účty pro integraci**.

   ![Najít účet pro integraci](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure zobrazí všechny účty pro integraci ve vašich předplatných Azure.

1. V části **účty integrace**vyberte účet pro integraci, který chcete odstranit. V nabídce účtu pro integraci vyberte **Přehled**.

   ![V nabídce účet pro integraci vyberte přehled.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. V podokně Přehled vyberte **Odstranit**.

   ![V podokně Přehled vyberte Odstranit.](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Pokud chcete potvrdit, že chcete účet pro integraci odstranit, vyberte **Ano**.

   ![Kliknutím na tlačítko Ano potvrďte odstranění.](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Další kroky

* [Vytváření obchodních partnerů v účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Vytváření smluv mezi partnery v účtu integrace](../logic-apps/logic-apps-enterprise-integration-agreements.md)
