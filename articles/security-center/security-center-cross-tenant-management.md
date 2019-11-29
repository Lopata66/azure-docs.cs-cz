---
title: Správa více tenantů v Azure Security Center | Microsoft Docs
description: Naučte se, jak nastavit správu mezi klienty pro správu stav zabezpečení pro více tenantů v Security Center pomocí delegované správy prostředků Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: 5c654610df8c00e1e80382687bd30dca0c02b69f
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558956"
---
# <a name="cross-tenant-management-in-security-center"></a>Správa mezi klienty v Security Center

Správa mezi klienty umožňuje zobrazit a spravovat stav zabezpečení pro více tenantů v Security Center využitím [delegované správy prostředků Azure](../lighthouse/concepts/azure-delegated-resource-management.md) . Spravujte více tenantů efektivně z jednoho zobrazení, aniž byste se museli přihlašovat ke všem adresářům tenanta.

- Poskytovatelé služeb mohou spravovat stav zabezpečení prostředků pro více zákazníků z jejich vlastního tenanta.

- Bezpečnostní týmy organizací s více klienty můžou zobrazit a spravovat stav zabezpečení z jednoho místa.

## <a name="set-up-cross-tenant-management"></a>Nastavení správy mezi klienty

Pomocí [delegované správy prostředků Azure](../lighthouse/concepts/azure-delegated-resource-management.md)můžete nastavit správu mezi klienty tím, že delegujete přístup k prostředkům spravovaných klientů do vašeho vlastního tenanta.

> [!NOTE]
> Správa delegovaných prostředků Azure je jednou z klíčových součástí Azure Lighthouse.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Jak funguje Správa mezi klienty v Security Center

Můžete kontrolovat a spravovat odběry napříč více klienty stejným způsobem, jakým spravujete více předplatných v jednom tenantovi.

V horním řádku nabídek klikněte na ikonu filtru a vyberte odběry z adresáře každého tenanta, který chcete zobrazit.

  ![Filtrovat klienty](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

Zobrazení a akce jsou v podstatě stejné. Zde je několik příkladů:

- **Správa zásad zabezpečení**: z jednoho zobrazení spravujte stav zabezpečení mnoha prostředků pomocí [zásad](tutorial-security-policy.md), provedete akce s doporučeními zabezpečení a shromáždíte a spravujete data související se zabezpečením.
- **Vylepšení zabezpečeného skóre a stav dodržování předpisů**: Díky viditelnosti mezi klienty můžete zobrazit celkové stav zabezpečení všech vašich klientů a kde a jak nejlépe vylepšit zabezpečení [skóre](security-center-secure-score.md) a [dodržování předpisů stav](security-center-compliance-dashboard.md) pro každé z nich.
- **Opravit doporučení**: Sledujte a opravte [doporučení](security-center-recommendations.md) pro mnoho prostředků od různých tenantů najednou. Pak můžete okamžitě řešit ohrožení zabezpečení, která představují nejvyšší riziko ve všech klientech.
- **Spravovat výstrahy**: zjišťovat [výstrahy](security-center-alerts-overview.md) v různých klientech. Proveďte akci s prostředky, které nedodržují předpisy pro [nápravné kroky](security-center-managing-and-responding-alerts.md).

- **Spravujte pokročilé funkce ochrany cloudu a další informace**: Spravujte různé služby zjišťování a ochrany před hrozbami, jako je například [přístup k virtuálnímu počítači JIT (just-in-time)](security-center-just-in-time.md), [adaptivní posílení sítě](security-center-adaptive-network-hardening.md), [Adaptivní řízení aplikací](security-center-adaptive-application.md)a další.
 
## <a name="next-steps"></a>Další kroky
Tento článek vysvětluje, jak funguje Správa mezi klienty v Security Center. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Posílit stav zabezpečení pomocí Azure Security Center](security-center-monitoring.md) – Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Nejčastější](security-center-faq.md) dotazy k Azure Security Center – Přečtěte si nejčastější dotazy týkající se používání služby.
* [Přečtěte si o Azure Lighthouse v podnikových scénářích](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise) – Zjistěte, jak může Azure Lighthouse zjednodušit správu mezi klienty v rámci podniku, který používá víc TENANTŮ Azure AD.