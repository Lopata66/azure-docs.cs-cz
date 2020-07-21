---
title: Přesunout konfiguraci údržby do jiné oblasti Azure
description: Přečtěte si, jak přesunout konfiguraci údržby virtuálních počítačů do jiné oblasti Azure.
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 4cff7eb4a69005f2e74747b6e58447f100c69b60
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86501598"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Přesunutí konfigurace řízení údržby do jiné oblasti

Podle tohoto článku můžete přesunout konfiguraci řízení údržby do jiné oblasti Azure. Je možné, že budete chtít přesunout konfiguraci z několika důvodů. Například pro využití nové oblasti, k nasazení funkcí nebo služeb dostupných v konkrétní oblasti, k splnění požadavků na požadavky na interní zásady a zásady správného řízení nebo v reakci na plánování kapacity.

Řízení údržby s přizpůsobenými konfiguracemi údržby umožňuje řídit, jak se aktualizace platforem aplikují na virtuální počítače s [Windows](./maintenance-control-cli.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) a [Linux](./maintenance-control-cli.md?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) , a na vyhrazené hostitele Azure. Pro přesun řízení údržby mezi oblastmi je k dispozici několik scénářů:

- Pokud chcete přesunout konfiguraci řízení údržby, ale ne prostředky přidružené ke konfiguraci, postupujte podle pokynů v tomto článku.
- Chcete-li přesunout prostředky spojené s konfigurací údržby, ale ne samotnou konfiguraci, postupujte podle [těchto pokynů](move-region-maintenance-configuration-resources.md).
- Pokud chcete přesunout jak konfiguraci údržby, tak i prostředky, které jsou k němu přidružené, postupujte nejprve podle pokynů v tomto článku. Pak postupujte podle [těchto pokynů](move-region-maintenance-configuration-resources.md).

## <a name="prerequisites"></a>Předpoklady

Než začnete přesouvat konfiguraci řízení údržby:

- Konfigurace údržby jsou přidružené k virtuálním počítačům Azure nebo vyhrazeným hostitelům Azure. Než začnete, ujistěte se, že prostředky virtuálních počítačů nebo hostitelů existují v nové oblasti.
- Identifikace 
    - Existující konfigurace řízení údržby.
    - Skupiny prostředků, ve kterých se aktuálně nacházejí existující konfigurace. 
    - Skupiny prostředků, do kterých budou přidány konfigurace po přesunu do nové oblasti. 
    - Prostředky přidružené k konfiguraci údržby, kterou chcete přesunout.
    - Ověřte, zda jsou prostředky v nové oblasti stejné jako ty, které jsou přidruženy k aktuálním konfiguracím údržby. Konfigurace můžou mít stejné názvy v nové oblasti, jako kdyby byly ve starém, ale to se nevyžaduje.

## <a name="prepare-and-move"></a>Příprava a přesun 

1. Načte všechny konfigurace údržby v každém předplatném. Spusťte příkaz CLI [AZ Maintenance Configuration list](/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list) a nahraďte $subId identifikátorem vašeho předplatného.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Projděte si seznam vrácených tabulek záznamů konfigurace v rámci předplatného. Tady je příklad. Seznam bude obsahovat hodnoty pro konkrétní prostředí.

    **Název** | **Umístění** | **Skupina prostředků**
    --- | --- | ---
    Přeskočit údržbu | eastus2 | Konfigurace-Resource-Group
    IgniteDemoConfig | eastus2 | Konfigurace-Resource-Group
    defaultMaintenanceConfiguration – eastus | eastus | Konfigurace testu
    

3. Uložte si seznam pro referenci. Při přesunu konfigurací vám pomůže ověřit, jestli se všechno přesunulo.
4. Jako referenci namapujte každou konfiguraci/skupinu prostředků na novou skupinu prostředků v nové oblasti.
5. Pomocí [PowerShellu](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)nebo rozhraní příkazového [řádku](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)vytvořte nové konfigurace údržby v nové oblasti.
6. Přidružte konfigurace k prostředkům v nové oblasti, pomocí [PowerShellu](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)nebo rozhraní příkazového [řádku](../virtual-machines/maintenance-control-cli.md#assign-the-configuration).


## <a name="verify-the-move"></a>Ověřit přesunutí

Po přesunutí konfigurací Porovnejte konfigurace a prostředky v nové oblasti se seznamem tabulek, který jste připravili.


## <a name="clean-up-source-resources"></a>Vyčištění zdrojových prostředků

Po přesunutí Zvažte odstranění přesunutých konfigurací údržby ve zdrojové oblasti, [PowerShellu](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)nebo rozhraní příkazového [řádku](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration).


## <a name="next-steps"></a>Další kroky

Pokud potřebujete přesunout prostředky spojené s konfigurací údržby, postupujte podle [těchto pokynů](move-region-maintenance-configuration-resources.md) . 
