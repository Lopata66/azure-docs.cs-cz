---
title: Kurz – zálohování služby SAP HANA DB v Azure pomocí rozhraní příkazového řádku
description: V tomto kurzu se naučíte zálohovat SAP HANA databáze běžící na virtuálním počítači Azure do trezoru služby Azure Backup Recovery Services pomocí Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: a0b6683183d6bf73b5376c6320106373ffd4ba78
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2020
ms.locfileid: "88762398"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Kurz: zálohování SAP HANA databází ve virtuálním počítači Azure pomocí Azure CLI

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo prostřednictvím skriptů. Tato dokumentace podrobně popisuje, jak zálohovat databázi SAP HANA a aktivovat zálohy na vyžádání – to všechno pomocí Azure CLI. Tyto kroky můžete provést také pomocí [Azure Portal](./backup-azure-sap-hana-database.md).

V tomto dokumentu se předpokládá, že už máte na virtuálním počítači Azure nainstalovanou databázi SAP HANA. ( [Virtuální počítač můžete vytvořit taky pomocí Azure CLI](../virtual-machines/linux/quick-create-cli.md)). Na konci tohoto kurzu budete moci:

> [!div class="checklist"]
>
> * Vytvoření trezoru Služeb zotavení
> * Registrovat instanci SAP HANA a vyhledat v ní databáze
> * Povolení zálohování v databázi SAP HANA
> * Aktivace zálohování na vyžádání

Podívejte se na [scénáře, které aktuálně podporujeme](./sap-hana-backup-support-matrix.md#scenario-support) pro SAP HANA.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete nainstalovat a používat rozhraní příkazového řádku místně, musíte spustit Azure CLI verze xx. xxx. x nebo novější. Verzi rozhraní příkazového řádku zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Recovery Services trezor je logický kontejner, ve kterém jsou uložena zálohovaná data pro každý chráněný prostředek, jako jsou například virtuální počítače Azure nebo úlohy běžící na virtuálních počítačích Azure, jako jsou databáze SQL nebo HANA. Úloha zálohování pro chráněný prostředek při spuštění vytvoří uvnitř trezoru služby Recovery Services bod obnovení. Pomocí některého z těchto bodů obnovení pak můžete obnovit data k danému bodu v čase.

Vytvořte trezor služby Recovery Services pomocí příkazu [az backup vault create](/cli/azure/backup/vault#az-backup-vault-create). Zadejte stejnou skupinu prostředků a umístění, jako má virtuální počítač, který chcete chránit. Naučte se vytvořit virtuální počítač pomocí Azure CLI s tímto [rychlým startem virtuálního počítače](../virtual-machines/linux/quick-create-cli.md).

V tomto kurzu budeme používat následující:

* Skupina prostředků s názvem *saphanaResourceGroup*
* virtuální počítač s názvem *saphanaVM*
* prostředky v umístění *westus2* .

Vytvoříme trezor s názvem *saphanaVault*.

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

Ve výchozím nastavení je trezor služby Recovery Services nastavený pro geograficky redundantní úložiště. Geograficky redundantní úložiště zajišťuje replikaci vašich zálohovaných dat do sekundární oblasti Azure, která je od primární oblasti vzdálená stovky kilometrů. Pokud je potřeba upravit nastavení redundance úložiště, použijte rutinu [AZ Backup trezor-Properties set](/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) .

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Pokud chcete zjistit, jestli se váš trezor úspěšně vytvořil, použijte rutinu [AZ Backup trezor list](/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-list) . Zobrazí se následující odpověď:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Registrace a ochrana SAP HANA instance

V případě instance SAP HANA (virtuální počítač s SAP HANA nainstalované), který mají být zjištěny službami Azure, musí být na SAP HANA počítači spuštěn [skript před registrací](https://aka.ms/scriptforpermsonhana) . Před spuštěním skriptu se ujistěte, že jsou splněné všechny [požadavky](./tutorial-backup-sap-hana-db.md#prerequisites) . Další informace o tom, co skript dělá, najdete v části [co je to skript](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) předběžného zápisu.

Po spuštění skriptu se instance SAP HANA dá zaregistrovat v trezoru Recovery Services, který jsme vytvořili dříve. Pokud chcete instanci zaregistrovat, použijte rutinu [AZ Backup Container Registry](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-register) . *VMResourceId* je ID prostředku virtuálního počítače, který jste vytvořili pro instalaci SAP HANA.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Pokud virtuální počítač není ve stejné skupině prostředků jako trezor, pak *saphanaResourceGroup* odkazuje na skupinu prostředků, ve které se vytvořil trezor.

Registrace instance SAP HANA automaticky zjistí všechny aktuální databáze. Chcete-li však zjistit, jaké nové databáze mohou být v budoucnu přidány, přečtěte si téma zjišťování [nových databází přidaných do oddílu registrované SAP HANA](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance) instance.

Pokud chcete zjistit, jestli se instance SAP HANA úspěšně zaregistrovala v trezoru, použijte rutinu [AZ Backup Container list](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) . Zobrazí se následující odpověď:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> Sloupec "Name" ve výše uvedeném výstupu odkazuje na název kontejneru. Tento název kontejneru se použije v dalších částech k povolení zálohování a jeho aktivaci. V tomto případě je to *VMAppContainer; COMPUTE; saphanaResourceGroup; saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Povolit zálohování v SAP HANA databázi

Rutinu [AZ Backup Protected-Item list](/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) vypíše všechny databáze zjištěné v instanci SAP HANA, kterou jste zaregistrovali v předchozím kroku.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

V tomto seznamu byste měli najít databázi, kterou chcete zálohovat, a to bude vypadat takto:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Jak vidíte z výše uvedeného výstupu, SID SAP HANA systému je HXE. V tomto kurzu nakonfigurujeme zálohování pro databázi *saphanadatabase; hxe; hxe* , která se nachází na serveru *hxehost* .

Pokud chcete v databázi chránit a konfigurovat zálohování, použijte k tomu rutinu [AZ Backup Protection Enable-for-azurewl](/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurewl) . Zadejte název zásady, kterou chcete použít. Pokud chcete vytvořit zásadu pomocí rozhraní příkazového řádku, použijte rutinu [AZ Backup Policy Create](/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) . V tomto kurzu budeme používat zásady *sapahanaPolicy* .

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name saphanadatabase;hxe;hxe  \
    --protectable-item-type SAPHANADatabse \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

Pomocí rutiny [seznamu úloh AZ Backup](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) můžete zjistit, jestli je výše uvedená konfigurace zálohování dokončená. Výstup se zobrazí takto:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

Rutina [seznam úloh AZ Backup](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) obsahuje seznam všech úloh zálohování (plánovaných i na vyžádání), které jsou v současné době spuštěny nebo aktuálně spuštěné v chráněné databázi. Kromě dalších operací, jako je registrace, konfigurace zálohování, odstraňování zálohovaných dat atd.

>[!NOTE]
>Azure Backup se při zálohování databáze SAP HANA běžící na virtuálním počítači Azure automaticky neupraví na letní čas při ukládání.
>
>Zásadu podle potřeby upravte ručně.

## <a name="trigger-an-on-demand-backup"></a>Aktivace zálohování na vyžádání

I když výše uvedená část podrobně popisuje, jak nakonfigurovat naplánované zálohování, Tato část pojednává o aktivaci zálohování na vyžádání. K tomu použijeme rutinu [AZ Backup Protection Backup-Now](/cli/azure/backup/protection#az-backup-protection-backup-now) .

>[!NOTE]
> Zásady uchovávání informací pro zálohování na vyžádání jsou určené základními zásadami uchovávání informací pro databázi.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

Výstup se zobrazí takto:

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

Odpověď vám poskytne název úlohy. Tento název úlohy můžete použít ke sledování stavu úlohy pomocí rutiny [AZ Backup Job show](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

>[!NOTE]
>Kromě plánování úplné nebo rozdílové zálohy je také možné aktuálně aktivovat ručně. Zálohy protokolu se automaticky spouštějí a spravují SAP HANA interně.
>
> Azure Backup v současné době nepodporuje přírůstkové zálohování.

## <a name="next-steps"></a>Další kroky

* Postup obnovení databáze SAP HANA na virtuálním počítači Azure pomocí rozhraní příkazového řádku, pokračování v kurzu – [obnovení databáze SAP HANA na virtuálním počítači Azure pomocí](tutorial-sap-hana-restore-cli.md) rozhraní PŘÍKAZového řádku

* Informace o tom, jak zálohovat SAP HANA databázi běžící na virtuálním počítači Azure pomocí Azure Portal, najdete [v tématu Zálohování databáze SAP HANA na virtuálních počítačích Azure](./backup-azure-sap-hana-database.md) .
