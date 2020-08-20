---
title: Co je Azure Lighthouse?
description: Azure Lighthouse umožňuje poskytovatelům služeb poskytovat spravované služby pro zákazníky s vyšším stupněm automatizace a efektivitou.
ms.date: 08/19/2020
ms.topic: overview
ms.openlocfilehash: d21d11d1316b177f38b6bce197dec4f2c3d3fac8
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660729"
---
# <a name="what-is-azure-lighthouse"></a>Co je Azure Lighthouse?

Azure Lighthouse umožňuje správu napříč více klienty, což umožňuje vyšší automatizaci, škálovatelnost a rozšířené zásady správného řízení napříč prostředky a klienty. S Azure Lighthouse můžou poskytovatelé služeb poskytovat spravované služby pomocí komplexních a robustních nástrojů pro správu, které jsou integrované v platformě Azure. Tato nabídka může také těžit z podnikových IT organizací, které spravují prostředky napříč více klienty.

![Diagram s přehledem Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Výhody

Azure Lighthouse pomáhá efektivně a efektivně sestavovat a doručovat spravované služby. Nabízí například tyto výhody:

- **Správa ve velkém měřítku**: zapojení zákazníků a operace životního cyklu pro správu zákaznických prostředků jsou jednodušší a lépe škálovatelné. Existující rozhraní API, nástroje pro správu a pracovní postupy lze použít u delegovaných prostředků bez ohledu na oblasti, ve kterých se nacházejí.
- **Lepší viditelnost a přesnost pro zákazníky**: zákazníci budou mít lepší přehled o vašich akcích a přesnou kontrolu nad oborem, který je delegovaný pro správu, včetně možnosti odebrat přístup kompletně, i když se vaše IP adresa zachová.
- **Komplexní a sjednocené nástroje pro platformy**: naše prostředí nástrojů řeší scénáře poskytovatele služby Key, včetně několika modelů licencování, jako jsou EA, CSP a průběžné platby. Nové funkce fungují s existujícími nástroji a rozhraními API, licenčními modely a partnerskými programy, jako je například [program Cloud Solution Provider (CSP)](/partner-center/csp-overview). Azure Lighthouse se dá integrovat do stávajících pracovních postupů a aplikací a svůj dopad na zapojení zákazníků můžete sledovat [propojením ID partnera](../cost-management-billing/manage/link-partner-id.md).

K používání Azure Lighthouse ke správě prostředků Azure se nevztahují žádné další poplatky. Azure Lighthouse může používat libovolný zákazník nebo partner Azure.

## <a name="capabilities"></a>Možnosti

Azure Lighthouse nabízí několik způsobů, jak zjednodušit zapojení a správu:

- **Správa delegovaných prostředků Azure**: zabezpečená Správa prostředků Azure pro zákazníky z vašeho vlastního tenanta bez nutnosti přepínat mezi nimi a řídicími rovinami kontextu. Předplatná a skupiny prostředků se dají delegovat na konkrétní uživatele a role v tenantovi, který má oprávnění k odebrání přístupu podle potřeby. Další informace najdete v tématu [Správa delegovaných prostředků Azure](concepts/azure-delegated-resource-management.md).
- **Nové prostředí Azure Portal**: zobrazení informací o vzájemném tenantovi na [stránce **moji zákazníci** ](./how-to/view-manage-customers.md) v Azure Portal. Odpovídající stránka [ **poskytovatelé služeb** ](how-to/view-manage-service-providers.md) umožňuje zákazníkům zobrazit a spravovat jejich přístup k poskytovateli služeb.
- **Šablony Azure Resource Manager**: Naše šablony vám pomůžou provádět úlohy správy mezi klienty a začlenit prostředky delegovaných zákazníků. Další informace najdete v našem [úložišti ukázek](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) a [začlenení zákazníka do Azure Lighthouse](how-to/onboard-customer.md).
- **Nabídky spravovaných služeb v Azure Marketplace**: nabízet vaše služby zákazníkům prostřednictvím soukromých nebo veřejných nabídek a automaticky je připravovat do Azure Lighthouse. Další informace najdete v tématu [nabídky spravovaných služeb v Azure Marketplace](concepts/managed-services-offers.md).

## <a name="next-steps"></a>Další kroky

- Další informace o [správě delegovaných prostředků Azure](concepts/azure-delegated-resource-management.md)
- Prozkoumejte [prostředí pro správu mezi klienty](concepts/cross-tenant-management-experience.md).
- Podívejte se, jak [používat Azure Lighthouse v rámci podniku](concepts/enterprise.md).
