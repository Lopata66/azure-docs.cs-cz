---
title: Vytvoření služby Azure Application Gateway – klasické rozhraní příkazového řádku Azure
description: Informace o vytvoření služby Application Gateway pomocí Azure classic CLI v Resource Manageru
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: 7107f45253c4f13b3378489726bf5034e104fa30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62095978"
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Vytvoření služby application gateway pomocí Azure CLI

Služba Azure Application Gateway je nástroj pro vyrovnávání zatížení vrstvy 7. Poskytuje převzetí služeb při selhání, směrování výkonu požadavků HTTP mezi různými servery, ať už jsou místní nebo v cloudu. Application gateway poskytuje následující funkce doručování aplikací: Vyrovnávání zatížení HTTP, spřažení relace na základě souborů cookie a přesměrování zpracování Secure Sockets Layer (SSL), vlastních sond stavu a podpory více webů.

## <a name="prerequisite-install-the-azure-cli"></a>Předpoklad: Instalace rozhraní příkazového řádku Azure CLI

K provedení kroků v tomto článku, budete muset [instalace rozhraní příkazového řádku Azure](../xplat-cli-install.md) a budete muset [přihlášení Azure](/cli/azure/authenticate-azure-cli). 

> [!NOTE]
> Pokud nemáte účet Azure, budete potřebovat. Zde si můžete zaregistrovat [bezplatnou zkušební verzi](../active-directory/fundamentals/sign-up-organization.md).

## <a name="scenario"></a>Scénář

V tomto scénáři se dozvíte, jak vytvořit službu application gateway pomocí webu Azure portal.

Tento scénář bude:

* Vytvoření střední application gateway se dvěma instancemi.
* Vytvoření virtuální sítě s názvem ContosoVNET s vyhrazeným blokem CIDR 10.0.0.0/16.
* Vytvoříte podsíť s názvem subnet01 používající blokem CIDR 10.0.0.0/28.

> [!NOTE]
> Další konfigurace služby application gateway, včetně vlastních stavových testy, back-endový fond adres a další pravidla jsou nakonfigurována po dokončení konfigurace aplikační brány a ne během počátečního nasazení.

## <a name="before-you-begin"></a>Než začnete

Azure Application Gateway vyžaduje vlastní podsíť. Při vytváření virtuální sítě, zajistěte ponechat dostatek adresního prostoru přesměrují do několika podsítí. Po nasazení služby application gateway k podsíti, budou moct přidat do podsítě jenom další aplikační brány.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Otevřít **příkazového řádku Microsoft Azure**a přihlaste se.

```azurecli-interactive
az login
```

Jakmile zadáte v předchozím příkladu, je k dispozici kód. Přejděte na https://aka.ms/devicelogin v prohlížeči na znaménko pokračovat v procesu.

![přihlášení na zařízení cmd zobrazení][1]

V prohlížeči zadejte kód, který jste obdrželi. Budete přesměrováni na přihlašovací stránku.

![prohlížeče a zadejte kód][2]

Jakmile byl zadán kód se přihlásíte, zavřete prohlížeč a pokračujte ve scénáři.

![Úspěšné přihlášení][3]

## <a name="switch-to-resource-manager-mode"></a>Přepnout do režimu Resource Manageru

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením služby application gateway, je tak, aby obsahovala application gateway vytvoří skupinu prostředků. Následuje ukázka příkazu.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Jakmile se vytvoří skupina prostředků, je pro službu application gateway vytvoří virtuální síť.  V následujícím příkladu byla adresní prostor jako 10.0.0.0/16, jak jsou definovány v předchozím scénáři poznámky.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Vytvoření podsítě

Po vytvoření virtuální sítě, se přidá podsíť pro službu application gateway.  Pokud máte v plánu používat služba application gateway s webovou aplikací hostovaných ve stejné virtuální síti jako služba application gateway, je potřeba ponechat dostatek volného místa pro jinou podsíť.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Po vytvoření virtuální sítě a podsítě se splnily požadavky pro službu application gateway. Kromě toho jsou požadovány pro následující krok certifikátu dříve exportovaný PFX a heslo pro certifikát: IP adresy používané pro back-end jsou IP adresy pro back-end serveru. Tyto hodnoty lze privátních IP adres ve virtuální síti, veřejné IP adresy nebo plně kvalifikované názvy domény pro váš back-end serverů.

```azurecli-interactive
azure network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group ContosoRG \
--vnet-name ContosoVNET \
--subnet-name subnet01 \
--servers 134.170.185.46,134.170.188.221,134.170.185.50 \
--capacity 2 \
--sku-tier Standard \
--routing-rule-type Basic \
--frontend-port 80 \
--http-settings-cookie-based-affinity Enabled \
--http-settings-port 80 \
--http-settings-protocol http \
--frontend-port http \
--sku-name Standard_Medium
```

> [!NOTE]
> Seznam parametrů, které lze zadat při vytváření, spusťte následující příkaz: **azure network application-gateway create--pomáhají**.

Tento příklad vytvoří základní aplikační brána s výchozím nastavením pro naslouchací proces, back-endový fond, nastavení http back-endu a pravidla. Můžete upravit tato nastavení tak, aby odpovídala nasazení po úspěšném zřízení.
Pokud už máte definované s back-endového fondu v předchozích krocích, po vytvoření webové aplikace služby Vyrovnávání zatížení začne.

## <a name="next-steps"></a>Další postup

Informace o vytváření vlastních testů stavu návštěvou [vytvořte sondu stavu vlastní](application-gateway-create-probe-portal.md)

Zjistěte, jak nakonfigurovat snižování zátěže protokolu SSL a trvat nákladné SSL dešifrování, zbaví vaše webové servery návštěvou [konfigurovat přesměrování zpracování SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
