---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: bbcf38ab54632144920b729f433c222f426d96dc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66111545"
---
1. Přihlaste se na web [Azure Portal][Azure portal].
2. V nabídce vlevo vyberte **+ Vytvořit prostředek**. Pak vyberte **Integrace** > **Přenos**. Pokud se v seznamu **Přenos** nezobrazuje, vyberte v pravém horním rohu **Zobrazit vše**. 
3. V části **Vytvořit obor názvů** zadejte název oboru názvů. Systém okamžitě kontroluje, jestli je název dostupný.
4. V poli **Předplatné** vyberte předplatné Azure, ve kterém chcete obor názvů vytvořit.
5. V poli [Skupina prostředků](../articles/azure-resource-manager/manage-resource-groups-portal.md) vyberte existující skupinu prostředků, do které se obor názvů umístí, nebo vytvořte novou.  
6. V části **Umístění** vyberte zemi nebo oblast, ve které by se měl váš oboru názvů hostovat.
   
    ![Vytvořit obor názvů][create-namespace]
7. Vyberte **Vytvořit**. Systém vytvoří obor názvů a povolí ho. Po několika minutách systém zřídí prostředky pro váš účet.

### <a name="get-management-credentials"></a>Získání přihlašovacích údajů pro správu

1. Vyberte **Všechny prostředky** a pak klikněte na název nově vytvořeného oboru názvů.
2. V části Obor názvů Relay vyberte **Zásady sdíleného přístupu**.  
3. V části **Zásady sdíleného přístupu** vyberte **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. V části **zásad: RootManageSharedAccessKey**, vyberte **kopírování** vedle **připojovací řetězec – primární klíč**. Tím se připojovací řetězec zkopíruje do schránky pro pozdější použití. Vložte tuto hodnotu do Poznámkového bloku nebo jiného dočasného umístění.
   
    ![connection-string][connection-string]

5. Zopakujte předchozí krok, zkopírujte si hodnotu **primárního klíče** a vložte ji do dočasného umístění pro pozdější použití.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
