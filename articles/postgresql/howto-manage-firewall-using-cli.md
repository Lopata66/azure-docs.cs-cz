---
title: Vytvoření a správě Azure Database for postgresql – pravidla brány firewall pomocí Azure CLI
description: Tento článek popisuje, jak vytvořit a spravovat Azure Database for postgresql – pravidla brány firewall pomocí příkazového řádku Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 9a96361d3fb155ea5b400990690e3c2b1f65f819
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492443"
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Vytvoření a správě Azure Database for postgresql – pravidla brány firewall pomocí Azure CLI
Pravidla brány firewall na úrovni serveru můžete použít ke správě přístupu ke službě Azure Database for PostgreSQL Server z konkrétní IP adresu nebo rozsah IP adres. Použití vhodné příkazy rozhraní příkazového řádku Azure, můžete vytvořit, aktualizovat, odstranit, seznam a zobrazit pravidla brány firewall pro správu serveru. Přehled služby Azure Database pro PostgreSQL pravidla brány firewall, naleznete v tématu [– Azure Database for PostgreSQL Server pravidla brány firewall](concepts-firewall-rules.md).

Pravidel virtuální sítě (VNet) je také možné zabezpečit přístup k vašemu serveru. Další informace o [vytváření a Správa virtuální sítě služby koncových bodů a pravidel pomocí Azure CLI](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v této příručce s postupy, musíte:
- Nainstalujte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) nástroj příkazového řádku nebo pomocí Azure Cloud Shell v prohlížeči.
- [– Azure Database for PostgreSQL server a databáze](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Konfigurace pravidel brány firewall pro službu Azure Database for PostgreSQL
[Az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) příkazy se používají ke konfiguraci pravidel brány firewall.

## <a name="list-firewall-rules"></a>Výpis pravidel brány firewall 
Chcete-li vypsat existující pravidla brány firewall serveru, spusťte [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule) příkazu.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Výstupu se zobrazí výpis pravidel brány firewall, pokud existuje, ve výchozím nastavení ve formátu JSON formátu. Můžete použít přepínač `--output table` pro přehlednějším tvaru tabulky jako výstup.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Vytvoření pravidla brány firewall
Chcete-li vytvořit nové pravidlo brány firewall na serveru, spusťte [az postgres server firewall-rule vytvořit](/cli/azure/postgres/server/firewall-rule) příkazu. 

```
To allow access to a singular IP address, provide the same address in the `--start-ip-address` and `--end-ip-address`, as in this example, replacing the IP shown here with your specific IP.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Pokud chcete umožnit aplikacím z Azure IP adres pro připojení k Azure Database for PostgreSQL server, zadejte IP adresu 0.0.0.0 jako počáteční IP adresa a koncová IP adresa, jako v následujícím příkladu.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

Po úspěšném nasazení výstup tohoto příkazu jsou uvedeny podrobnosti pravidla brány firewall, které jste vytvořili, ve výchozím nastavení ve formátu JSON. Pokud dojde k selhání, zobrazí výstup chybovou zprávu.

## <a name="update-firewall-rule"></a>Aktualizovat pravidlo brány firewall 
Aktualizovat stávající pravidlo brány firewall na serveru pomocí [az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule) příkazu. Zadejte název stávající pravidlo brány firewall jako vstup a počáteční IP adresy a koncové IP atributů k aktualizaci.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
Po úspěšném nasazení výstup tohoto příkazu jsou uvedeny podrobnosti pravidla brány firewall, které jste aktualizovali, ve výchozím nastavení ve formátu JSON. Pokud dojde k selhání, zobrazí výstup chybovou zprávu.
> [!NOTE]
> Pokud neexistuje pravidlo brány firewall, se vytvoří pomocí příkazu update.

## <a name="show-firewall-rule-details"></a>Zobrazí podrobnosti pravidla brány firewall
Můžete také zobrazit podrobnosti pro existující pravidlo brány firewall na úrovni serveru spuštěním [az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule) příkazu.
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Po úspěšném nasazení výstup tohoto příkazu jsou uvedeny podrobnosti pravidla brány firewall, které jste zadali, ve výchozím nastavení ve formátu JSON. Pokud dojde k selhání, zobrazí výstup chybovou zprávu.

## <a name="delete-firewall-rule"></a>Odstranit pravidlo brány firewall
Odvolání přístupu pro rozsah IP adres serveru odstranit stávající pravidlo brány firewall pomocí provádí [az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule) příkazu. Zadejte název stávající pravidlo brány firewall.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Po úspěšném nasazení neexistuje žádný výstup. Po selhání je vrácena text chybové zprávy.

## <a name="next-steps"></a>Další postup
- Podobně můžete použít webový prohlížeč na [vytvořit a spravovat Azure Database for postgresql – pravidla brány firewall pomocí webu Azure portal](howto-manage-firewall-using-portal.md).
- Zjistit více o [– Azure Database for PostgreSQL Server pravidla brány firewall](concepts-firewall-rules.md).
- Dále zabezpečit přístup k vašemu serveru pomocí [vytváření a Správa virtuální sítě služby koncových bodů a pravidel pomocí Azure CLI](howto-manage-vnet-using-cli.md).
- Pomoc při připojování k serveru Azure Database for PostgreSQL, naleznete v tématu [připojení knihoven pro službu Azure Database for PostgreSQL](concepts-connection-libraries.md).
