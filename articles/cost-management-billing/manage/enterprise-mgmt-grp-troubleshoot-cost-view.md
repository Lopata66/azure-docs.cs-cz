---
title: Řešení potíží s podnikovými zobrazeními nákladů Azure
description: Přečtěte si, jak vyřešit všechny problémy, které můžete mít při zobrazení nákladů organizace v rámci webu Azure Portal.
author: bandersmsft
ms.reviewer: amberb
ms.service: cost-management-billing
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 6c4fe563057e8d7fedbd68280bc8059867d997ed
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120241"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Řešení potíží s podnikovými zobrazeními nákladů

V rámci podnikových prováděcích smluv je k dispozici několik nastavení, která by mohla způsobit, že uživatelé v rámci prováděcí smlouvy neuvidí náklady.  Tato nastavení spravuje správce prováděcí smlouvy. Nebo pokud se prováděcí smlouva nezakoupila přímo od Microsoftu, nastavení spravuje partner.  Tento článek vám pomůže pochopit, co jsou tato nastavení a jaký mají vliv na prováděcí smlouvu. Tato nastavení nezávisí na rolích řízení přístupu na základě rolí (RBAC) Azure.

## <a name="enable-access-to-costs"></a>Povolení přístupu k nákladům

Zobrazuje se zpráva Neautorizováno nebo *Zobrazení nákladů je v prováděcí smlouvě zakázané* během hledání informací o nákladech?
![Snímek obrazovky, který zobrazuje zprávu Neautorizováno v poli Aktuální náklady pro předplatné](./media/enterprise-mgmt-grp-troubleshoot-cost-view/unauthorized.png)

Příčinou může být jeden z následujících důvodů:

1. Koupili jste Azure přes podnikového partnera a tento partner dosud nevydal ceník. Kontaktujte svého partnera, aby aktualizoval nastavení cen v rámci webu [Enterprise Portal](https://ea.azure.com).
2. Pokud jste zákazníkem se smlouvou EA Direct, máte několik možností:
    * Jste vlastníkem účtu a váš správce prováděcí smlouvy zakázal nastavení **AO view charges** (Vlastník účtu může zobrazit náklady).  
    * Jste správcem oddělení a váš správce prováděcí smlouvy zakázal nastavení **DA view charges** (Správce oddělení může zobrazit náklady).
    * Požádejte správce prováděcí smlouvy o přístup. Správce prováděcí smlouvy může aktualizovat nastavení na webu [Enterprise Portal](https://ea.azure.com/manage/enrollment).

      ![Snímek obrazovky zobrazující nastavení webu Enterprise Portal pro zobrazení poplatků](./media/enterprise-mgmt-grp-troubleshoot-cost-view/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>Prostředek není k dispozici

Pokud se během pokusu o přístup k předplatnému nebo skupině pro správu zobrazí chybová zpráva s oznámením **Tento prostředek není k dispozici**, nemáte správnou roli k zobrazení této položky.  

![Snímek obrazovky zobrazující zprávu Prostředek není k dispozici](./media/enterprise-mgmt-grp-troubleshoot-cost-view/asset-not-found.png)

Požádejte o přístup svého správce předplatného Azure nebo správce skupiny pro správu. Další informace najdete v tématu [Správa přístupu pomocí RBAC a webu Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další kroky
- Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
