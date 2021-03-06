---
title: Vytvoření pracovního prostoru s povolenou službou data exfiltrace Protection
description: V tomto článku se dozvíte, jak vytvořit pracovní prostor s ochranou dat exfiltrace ve službě Azure synapse Analytics.
author: NanditaV
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: f8ebbdf70836f3f2613183268f03dc43da1f0671
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590556"
---
# <a name="create-a-workspace-with-data-exfiltration-protection-enabled"></a>Vytvoření pracovního prostoru s povolenou službou data exfiltrace Protection
Tento článek popisuje, jak vytvořit pracovní prostor s povolenou službou data exfiltrace Protection a jak spravovat schválené klienty Azure AD pro tento pracovní prostor.

>[!Note]
>Po vytvoření pracovního prostoru nemůžete změnit konfiguraci pracovního prostoru pro spravovanou virtuální síť a ochranu dat exfiltrace.

## <a name="prerequisites"></a>Předpoklady
- Oprávnění k vytvoření prostředku pracovního prostoru v Azure.
- Synapse v pracovním prostoru oprávnění k vytváření spravovaných privátních koncových bodů.
- Odběry jsou zaregistrované pro poskytovatele síťových prostředků. [Další informace](../../azure-resource-manager/management/resource-providers-and-types.md)

Postupujte podle kroků uvedených v části [rychlý Start: vytvoření pracovního prostoru synapse](../quickstart-create-workspace.md) , abyste mohli začít vytvářet pracovní prostor. Před vytvořením pracovního prostoru použijte níže uvedené informace, abyste přidali data exfiltrace Protection do svého pracovního prostoru.

## <a name="add-data-exfiltration-protection-when-creating-your-workspace"></a>Přidání ochrany dat exfiltrace při vytváření pracovního prostoru
1. Na kartě Síť zaškrtněte políčko Povolit spravovanou virtuální síť.
1. Vyberte Ano pro možnost "povolí přenos odchozích dat pouze do schválených cílů".
1. Vyberte schválené klienty Azure AD pro tento pracovní prostor.
1. Zkontrolujte konfiguraci a vytvořte pracovní prostor.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-creation-data-exfiltration-protection.png" alt-text="Snímek obrazovky, který zobrazuje pracovní prostor vytvoření synapse s vybranou možností povolit správu virtuální sítě":::

## <a name="manage-approved-azure-active-directory-tenants-for-the-workspace"></a>Správa schválených klientů Azure Active Directory pro pracovní prostor
1. Z Azure Portal pracovního prostoru přejděte do části schválené klienty Azure AD. Tady se zobrazí seznam schválených tenantů Azure AD pro tento pracovní prostor. Tenant pracovního prostoru je ve výchozím nastavení zahrnutý a není uveden.
1. Pomocí příkazu "+ Add" přidejte nové klienty do seznamu schválených.
1. Pokud chcete odebrat tenanta Azure AD ze seznamu schválených, vyberte tenanta a vyberte Odstranit a pak uložit.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-manage-aad-tenant-list.png" alt-text="Vytvoření pracovního prostoru s exfiltrace data Protection":::


## <a name="connecting-to-azure-resources-in-approved-azure-ad-tenants"></a>Připojení k prostředkům Azure ve schválených klientech Azure AD

Můžete vytvořit spravované privátní koncové body pro připojení k prostředkům Azure, které se nacházejí v klientech Azure AD, které jsou schválené pro pracovní prostor. Postupujte podle kroků uvedených v průvodci pro [vytváření spravovaných privátních koncových bodů](./how-to-create-managed-private-endpoints.md).

>[!IMPORTANT]
>Prostředky v klientech jiných než tenant pracovního prostoru musí blokovat pravidla brány firewall, aby se k nim fondy SQL připojovaly. Prostředky v rámci spravované virtuální sítě pracovního prostoru, jako jsou například Clustery Spark, se mohou připojit prostřednictvím spravovaných privátních odkazů na prostředky chráněné bránou firewall.

## <a name="next-steps"></a>Další kroky

Další informace o [ochraně dat exfiltrace v pracovních prostorech synapse](./workspace-data-exfiltration-protection.md)

Další informace o [spravovaném pracovním prostoru Virtual Network](./synapse-workspace-managed-vnet.md)

Další informace o [spravovaných privátních koncových bodech](./synapse-workspace-managed-private-endpoints.md)

[Vytvoření spravovaných privátních koncových bodů pro vaše zdroje dat](./how-to-create-managed-private-endpoints.md)
