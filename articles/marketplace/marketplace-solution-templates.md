---
title: Šablona řešení aplikace Azure nabízejí Průvodce publikováním | Azure Marketplace
description: Tento článek popisuje požadavky na webu Azure Marketplace publikovat šablony řešení.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 11/15/2018
ms.author: ellacroi
ms.openlocfilehash: c2393b6ea9f1a2c2b35be63272743e081f4ae240
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64937754"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Aplikace Azure: Průvodce publikováním nabídky šablony řešení

Šablony řešení jsou jedním z hlavních způsobů, jak publikování řešení na webu Marketplace. Tento průvodce vám porozumět všem požadavkům pro v rámci této nabídky. 

Pomocí aplikace Azure: typ nabídky šablony řešení, když vaše řešení nevyžaduje další automatizaci nasazení a konfigurace nad rámec jednoho virtuálního počítače. Může automatizovat zřizování jeden nebo více virtuálních počítačů pomocí aplikace Azure: šablony řešení. Může také zřizovat prostředky sítě a úložiště. Aplikace Azure: šablony řešení nabízí typ poskytuje výhody služby automation jednotlivých virtuálních počítačů a celé řešení založeného na IaaS.

Tyto šablony řešení jsou transakce nabídek, které se nasazují a prostřednictvím webu Marketplace účtují. Volání akce, které uživatel uvidí je "Získat."


## <a name="requirements-for-solution-templates"></a>Požadavky pro šablony řešení

| **Požadavky** | **Podrobnosti**  |
| ---------------  | -----------  |
|K fakturaci a měření    |  Prostředky se zřídí v rámci předplatného Azure zákazníka. Průběžné platby (PAYGO) virtuálních počítačů se nepodporuje transakce u zákazníka prostřednictvím Microsoft účtuje prostřednictvím předplatného Azure zákazníkem (PAYGO).  <br/> V případě používání your vlastní licence (BYOL) zatímco Microsoft vám bude účtovat náklady na infrastrukturu v rámci předplatného zákazníka se transact licenční poplatky zákazníkovi přímo váš software.   |
|Kompatibilní s Azure virtuálního pevného disku (VHD)  |   Virtuální počítače musí být sestaveny jako ve Windows nebo Linuxu.  Další informace najdete [najdete v části Vytvoření virtuálního pevného disku kompatibilního s Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Využití Attribution zákazníka | Povolení attribution využití zákazník musí být ve všech šablon řešení na webu Azure Marketplace publikovat. Další informace o využití attribution zákazníka a jak se dá povolit, najdete v části [partnerů Azure zákazníka využití attribution](./azure-partner-customer-usage-attribution.md).  |
|  |  |

## <a name="next-steps"></a>Další postup
Pokud jste tak již neučinili, [zaregistrovat](https://azuremarketplace.microsoft.com/sell) na webu Marketplace.

Pokud budete zaregistrováni a vytváříte novou nabídku nebo pracující na existující, přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com) k vytvoření nebo dokončení vaší nabídky.
