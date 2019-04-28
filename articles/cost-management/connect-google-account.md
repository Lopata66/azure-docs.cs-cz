---
title: Připojení účtu Google Cloud Platform do Cloudyn v Azure | Dokumentace Microsoftu
description: Připojení účtu Google Cloud Platform a zobrazit data o využití a nákladů v sestavách Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 7f63293900e116fd3175b0ea6d704993a2dcf591
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576262"
---
# <a name="connect-a-google-cloud-platform-account"></a>Připojení účtu Google Cloud Platform

Můžete připojit váš existující účet Google Cloud Platform do Cloudyn. Po propojení účtu do Cloudyn, nákladů a využití dat je k dispozici v sestavách Cloudyn. Tento článek pomůže vám pomůže nakonfigurovat a připojte si účet Google s Cloudyn.


## <a name="collect-project-information"></a>Shromažďovat informace o projektu

Začnete tím, že shromažďování informací o projektu.

1. Přihlaste se ke konzole Google Cloud Platform na [ https://console.cloud.google.com ](https://console.cloud.google.com).
2. Projděte si informace o projektu, který chcete, aby se zapojit do Cloudyn a Všimněte si, **název projektu** a **ID projektu**. Zachovejte informace po ruce pro pozdější kroky.  
    ![Název projektu a ID projektu zobrazí v konzole Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. V případě fakturace není povolen a propojeny s projektem, vytvořte fakturační účet. Další informace najdete v tématu [vytvořit nové fakturační účet](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account).

## <a name="enable-storage-bucket-billing-export"></a>Povolit export fakturační kontejneru úložiště

Cloudyn načte vašich fakturačních dat Google z kontejneru úložiště. Zachovat **název sektoru** a **předpona sestavy** informace po ruce pro pozdější použití. při registraci do Cloudyn.

Použití Google Cloud Storage pro uložení sestavy o využití se účtují minimální poplatky. Další informace najdete v tématu [ceny za úložiště cloudu](https://cloud.google.com/storage/pricing).

1. Pokud jste ještě nepovolili fakturační export do souboru, postupujte podle pokynů na adrese [povolení fakturace export do souboru](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Můžete použít JSON nebo CSV fakturační formát exportu.
2. V opačném případě se v konzole Google Cloud Platform přejděte do **fakturace** > **fakturace export**. Všimněte si fakturace **název sektoru** a **předpona sestavy**.  
    ![Informace o exportu fakturace zobrazený na stránce fakturace exportu](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Povolte rozhraní API Google Cloud Platform

Cloudyn shromažďovat údaje o využití a prostředků, potřebuje následující Google Cloud Platform rozhraní API povoleno:

- BigQuery rozhraní API
- Google Cloud SQL
- Google Cloud Datastore. rozhraní API
- Google Cloud Storage
- Google Cloud Storage JSON API
- Google Compute Engine rozhraní API

### <a name="enable-or-verify-apis"></a>Povolení nebo ověření rozhraní API

1. V konzole Google Cloud Platform vyberte projekt, který chcete použít k registraci ve službě Cloudyn.
2. Přejděte do **rozhraní API a služby** > **knihovny**.
3. Pomocí hledání můžete najít jednotlivé dříve uvedené rozhraní API.
4. Pro každé rozhraní API, ověřte, že **rozhraní API povoleno** se zobrazí. V opačném případě klikněte na tlačítko **povolit**.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Přidání účtu Google Cloud Cloudyn

1. Otevřete portál Cloudyn z portálu Azure portal nebo přejděte na [ https://azure.cloudyn.com ](https://azure.cloudyn.com/) a přihlaste se.
2. Klikněte na tlačítko **nastavení** (symbol ozubeného kola) a pak vyberte **cloudové účty**.
3. V **Správa účtů**, vyberte **účty Google** kartu a potom klikněte na tlačítko **přidat nový +**.
4. V **název účtu služby Google**, zadejte e-mailovou adresu pro fakturační účet a pak klikněte na tlačítko **Další**.
5. V dialogovém okně ověřování Google, vyberte nebo zadejte účet Google a následně **povolit** cloudyn.com přístup k vašemu účtu.
6. Přidat žádost o informace o projektu, že jste měli předchozí jste si poznamenali. Patří mezi ně **ID projektu**, **projektu** název, **fakturační** název kontejneru a **fakturační souboru** předpona sestavy a potom klikněte na  **Uložit**.  
    ![Přidání projektu Google do účtu služby Cloudyn](./media/connect-google-account/add-project.png)

Váš účet Google se zobrazí v seznamu účtů a by mělo být uvedeno **ověřený**. V něm by měl název projektu Google a ID se zobrazí a mít symbol zelená značka zaškrtnutí. Stav účtu by mělo být uvedeno **dokončeno**.

Během pár hodin sestavách Cloudyn zobrazit Google nákladů a využití informací.

## <a name="next-steps"></a>Další postup

- Chcete-li další informace o Cloudyn, pokračujte [kontrola využití a nákladů](./tutorial-review-usage.md) kurz pro Cloudyn.
