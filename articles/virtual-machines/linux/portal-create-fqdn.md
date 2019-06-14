---
title: Vytvoření plně kvalifikovaného názvu domény pro virtuální počítač s Linuxem na webu Azure Portal | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit plně kvalifikovaný název domény, nebo plně kvalifikovaný název domény, pro správce prostředků na základě virtuálního počítače na webu Azure Portal.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 54d1f77823b982ee8f49122c46b0a01cb27390f2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62129413"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Vytvořit použitím plně kvalifikovaného názvu domény pro virtuální počítač s Linuxem na webu Azure Portal

Při vytváření virtuálního počítače (VM) v [webu Azure portal](https://portal.azure.com), automaticky se vytvoří prostředek veřejné IP pro virtuální počítač. Tuto IP adresu použijete vzdálený přístup k virtuálnímu počítači. I když na portálu se nevytvoří [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), nebo plně kvalifikovaný název domény, můžete ho přidat po vytvoření virtuálního počítače. Tento článek ukazuje postup vytvoření názvu DNS nebo plně kvalifikovaný název domény.

## <a name="create-a-fqdn"></a>Vytvoření plně kvalifikovaný název domény
Tento článek předpokládá, že jste již vytvořili virtuální počítač. V případě potřeby můžete [vytvoření virtuálního počítače na portálu](quick-create-portal.md) nebo [pomocí Azure CLI](quick-create-cli.md). Jakmile se váš virtuální počítač je v provozu, postupujte takto:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Nyní se můžete připojit vzdáleně k virtuálnímu počítači pomocí tento název DNS, jako s `ssh azureuser@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Další postup
Teď, když váš virtuální počítač má název veřejné IP adresy a DNS, můžete nasadit běžné architektury aplikací nebo služeb, jako je server nginx, MongoDB, Docker, atd.

Také další informace o [pomocí Resource Manageru](../../azure-resource-manager/resource-group-overview.md) tipy na vytváření nasazení Azure.

