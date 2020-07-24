---
title: Připojení k pracovnímu prostoru synapse pomocí privátních odkazů
description: Tento článek vás seznámí s tím, jak se připojit k pracovnímu prostoru Azure synapse pomocí privátních odkazů.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 17636bf993df5105093ca690e36db22493a2472e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87005969"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Připojení k pracovnímu prostoru Azure synapse pomocí privátních odkazů (Preview)

Tento článek vás seznámí s postupem vytvoření privátního koncového bodu pro váš pracovní prostor Azure synapse. Další informace najdete v tématu [soukromé odkazy a soukromé koncové body](https://docs.microsoft.com/azure/private-link/) .

## <a name="step-1-register-network-resource-provider"></a>Krok 1: registrace poskytovatele síťových prostředků

Pokud jste to ještě neudělali, zaregistrujte poskytovatele síťových prostředků. Když zaregistrujete poskytovatele prostředků, nakonfigurujete vaše předplatné, aby fungovalo s poskytovatelem prostředků. Po [registraci](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)vyberte v seznamu poskytovatelů prostředků možnost *Microsoft. Network* . Pokud je poskytovatel síťových prostředků již zaregistrován, přejděte ke kroku 2.


## <a name="step-2-open-your-azure-synapse-workspace-in-azure-portal"></a>Krok 2: otevřete pracovní prostor Azure synapse v Azure Portal

V části **zabezpečení** vyberte **připojení privátního koncového bodu** a pak vyberte **+ privátní koncový bod**.
![Otevřete pracovní prostor Azure synapse v Azure Portal](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-3-select-your-subscription-and-region-details"></a>Krok 3: Vyberte si podrobnosti o předplatném a oblasti.

Na kartě **základy** v okně **Vytvoření privátního koncového bodu** vyberte **předplatné** a **skupinu prostředků**. Zadejte **název** privátního koncového bodu, který chcete vytvořit. Vyberte **oblast** , ve které chcete vytvořit soukromý koncový bod.

V podsíti jsou vytvořeny privátní koncové body. U předplatného, skupiny prostředků a oblasti vyberte možnost filtrovat podsítě privátních koncových bodů. Po dokončení vyberte **Další: prostředek >** .
![Vybrat podrobnosti o předplatném a oblasti](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-4-select-your-azure-synapse-workspace-details"></a>Krok 4: výběr podrobností v pracovním prostoru Azure synapse

V části adresář na kartě **prostředek** vyberte **připojit k prostředku Azure** . Vyberte **předplatné** , které obsahuje váš pracovní prostor Azure synapse. **Typ prostředku** pro vytváření privátních koncových bodů do pracovního prostoru Azure synapse je *Microsoft. synapse/Workspaces*.

Jako **prostředek**vyberte pracovní prostor Azure synapse. Každý pracovní prostor Azure synapse má tři **cílové dílčí prostředky** , které můžete vytvořit pomocí privátního koncového bodu: SQL, SqlOnDemand a dev.

Vyberte **Další: konfigurační>** , které chcete přejít k další části instalace.
![Vybrat podrobnosti o předplatném a oblasti](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

Na kartě **Konfigurace** vyberte **virtuální síť** a **podsíť** , ve které se má vytvořit privátní koncový bod. Také je nutné vytvořit záznam DNS, který se mapuje na soukromý koncový bod.

Vyberte **Ano** pro **integraci s privátní zónou DNS** pro integraci privátního koncového bodu s privátní zónou DNS. Pokud nemáte přidruženou privátní zónu DNS k vašemu Microsoft Azure Virtual Network, vytvoří se nová privátní zóna DNS. Po dokončení vyberte **zkontrolovat + vytvořit** .

![Vybrat podrobnosti o předplatném a oblasti](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Po dokončení nasazení otevřete pracovní prostor Azure synapse v Azure Portal a vyberte **připojení privátního koncového bodu**. Zobrazí se nový privátní koncový bod a název připojení privátního koncového bodu přidružený k privátnímu koncovému bodu.

![Vybrat podrobnosti o předplatném a oblasti](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Další kroky

Další informace o [spravovaném pracovním prostoru Virtual Network](./synapse-workspace-managed-vnet.md)

Další informace o [spravovaných privátních koncových bodech](./synapse-workspace-managed-private-endpoints.md)

[Vytvoření spravovaných privátních koncových bodů pro vaše zdroje dat](./how-to-create-managed-private-endpoints.md)
