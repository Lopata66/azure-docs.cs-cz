---
title: Ukázka podrobného plánu Cílová zóna migrace CAF – přehled
description: Přehled a architektura přechodu na cloud pro Azure (CAF) pro ukázkový podrobný plán Cílová zóna migrace.
ms.date: 08/20/2019
ms.topic: sample
ms.openlocfilehash: 08171a39c83ea6822243d7239882a19b053f213e
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74545496"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Přehled architektury přechodu na cloud pro Azure od Microsoftu pro ukázkový podrobný plán Cílová zóna migrace

Ukázkový podrobný plán Cílová zóna migrace architektury přechodu na cloud pro Azure od Microsoftu (CAF) nasadí sadu prostředků infrastruktury, které vám pomůžou nastavit migraci vaší první úlohy a spravovat cloudová aktiva v souladu s CAF.

Ukázkový podrobný plán [Základy CAF](../caf-foundation/index.md) na tento podrobný plán navazuje a rozšiřuje ho.

## <a name="architecture"></a>Architektura

Ukázkový podrobný plán Cílová zóna migrace CAF nasadí do Azure základní prostředky infrastruktury, které organizace můžou využít k přípravě svých předplatných pro migraci virtuálních počítačů. Pomáhá také zavést ovládací prvky zásad správného řízení, které jsou nezbytné pro správu jejich cloudových aktiv. Tato ukázka nasadí a vynutí prostředky, zásady a šablony, které organizacím umožní, aby s Azure mohli bez obav začít.

![Cílová zóna migrace CAF: Obrázek popisuje, co se nainstaluje v rámci pokynů CAF pro počáteční cílovou zónu. ](../../media/caf-blueprints/caf-migration-landing-zone-architecture.png)

Toto prostředí je tvořené několika službami Azure, které se využívají k zajištění zabezpečených a plně monitorovaných zásad správného řízení na podnikové úrovni. Toto prostředí tvoří:

- Instance služby [Azure Key Vault](../../../../key-vault/key-vault-overview.md), která slouží k hostování tajných kódů používaných pro certifikáty, klíče a tajné kódy nasazené v prostředí sdílených služeb.
- Nasazení služby [Log Analytics](../../../../azure-monitor/overview.md) pro zajištění, že od okamžiku, kdy zahájíte migraci, se všechny akce a služby připojují k centrálnímu umístění.
- Nasazení služby [Azure Security Center](../../../../security-center/security-center-intro.md) (standardní verze) zajišťující ochranu před hrozbami pro vaše migrované úlohy.
- Nasazení služby [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md) poskytující izolovanou síť a podsítě pro váš virtuální počítač.
- Nasazení [projektu Azure Migrate](../../../..//migrate/migrate-overview.md) pro zjišťování a hodnocení. Přidáváme nástroje pro vyhodnocování serverů, migraci serverů, vyhodnocení databází a migraci databází.  


Všechny tyto prvky dodržují prověřené postupy publikované v článku zaměřeném na [Centrum architektury Azure – referenční architektury](/azure/architecture/reference-architectures/).

> [!NOTE]
> Podrobný plán Migrace CAF vymezuje cílovou zónu pro vaše úlohy. Nad rámec této základní architektury je stále potřeba provést posouzení a migraci vašich virtuálních počítačů a databází.

Další informace najdete v tématu [Architektura přechodu na cloud pro Azure od Microsoftu – migrace](/azure/architecture/cloud-adoption/migrate/).

## <a name="next-steps"></a>Další kroky

Prošli jste si přehled a architekturu ukázkového podrobného plánu Cílová zóna migrace CAF.

> [!div class="nextstepaction"]
> [Podrobný plán Cílová zóna migrace CAF – kroky nasazení](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)