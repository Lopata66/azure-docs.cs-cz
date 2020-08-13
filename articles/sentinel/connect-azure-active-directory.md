---
title: Připojit Azure Active Directory dat ke službě Azure Sentinel | Microsoft Docs
description: Naučte se shromažďovat data z Azure Active Directory a streamovat protokoly přihlášení do Azure AD a auditovat protokoly do Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a19ced1cb9496bddd1f9aa7ea9e3eb58ba7eea3d
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185685"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Připojení dat z Azure Active Directory (Azure AD)



Možnost Azure Sentinel umožňuje shromažďovat data z [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) a streamovat je do Azure Sentinel. Můžete zvolit streamování [protokolů přihlášení](../active-directory/reports-monitoring/concept-sign-ins.md) a [auditu](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Předpoklady

- Jakákoli licence Azure AD (Free/O365/P1/P2) se může integrovat s Azure Monitor a ingestovat protokoly přihlášení do Azure Sentinel. Další poplatky za GB budou platit pro Azure Monitor nebo Azure Sentinel.

- Uživatel s oprávněními globálního správce nebo správce zabezpečení u tenanta, ze kterého chcete protokoly streamovat.

- Aby bylo možné zobrazit stav připojení, musíte mít oprávnění pro přístup k diagnostickým protokolům Azure AD. 


## <a name="connect-to-azure-active-directory"></a>Připojení k Azure Active Directory

1. V Azure Sentinel vyberte **datové konektory** z navigační nabídky.

1. Z Galerie datových konektorů vyberte **Azure Active Directory** a potom klikněte na tlačítko **otevřít stránku konektoru** .

1. Zaškrtněte políčka vedle protokolů, které chcete streamovat do Azure Sentinel, a klikněte na **připojit**.

1. Můžete vybrat, jestli chcete, aby upozornění z Azure AD automaticky generovala incidenty ve službě Azure Sentinel. V části **vytvořit incidenty** vyberte **Povolit** , pokud chcete povolit výchozí analytické pravidlo, které automaticky vytvoří incidenty z výstrah vygenerovaných v připojené službě zabezpečení. Toto pravidlo pak můžete upravit v části **Analýza** a pak na **aktivní pravidla**.

1. Pokud chcete použít příslušné schéma v Log Analytics pro dotazování na výstrahy služby Azure AD, zadejte `SigninLogs` nebo `AuditLogs` v okně dotazu.




## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Azure Active Directory ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
