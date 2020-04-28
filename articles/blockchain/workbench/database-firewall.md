---
title: Konfigurace brány firewall databáze Azure blockchain Workbench
description: Zjistěte, jak nakonfigurovat bránu firewall pro Azure blockchain Workbench Preview tak, aby umožňovala připojení externích klientů a aplikací.
ms.date: 09/09/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: e6739b7ead9299f020465d3c50ed01826334ca76
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74326005"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Konfigurace brány firewall databáze služby Azure Blockchain Workbench

Tento článek popisuje, jak nakonfigurovat pravidlo brány firewall pomocí Azure Portalu. Pravidla brány firewall umožňují externím klientům nebo aplikacím připojení k databázi služby Azure Blockchain Workbench.

## <a name="connect-to-the-blockchain-workbench-database"></a>Připojení k databázi Blockchain Workbench

K databázi, kde chcete nakonfigurovat pravidlo, se připojíte takto:

1. Přihlaste se k Azure Portal pomocí účtu, který má oprávnění **vlastníka** pro prostředky Azure blockchain Workbench.
2. V levém navigačním podokně zvolte **Skupiny prostředků**.
3. Zvolte název skupiny prostředků svého nasazení služby Blockchain Workbench.
4. Výběrem možnosti **Typ** tento seznam prostředků seřaďte a pak zvolte **SQL Server**.
5. V příkladu seznamu prostředků na následujícím snímku obrazovky se zobrazují dvě databáze: *master* a *lsgn-sdk*. Pravidlo brány firewall budete konfigurovat u databáze *lsgn-sdk*.

![Seznam prostředků služby Blockchain Workbench](./media/database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Vytvoření pravidla brány firewall databáze

Pravidlo brány firewall vytvoříte takto:

1. Zvolte odkaz na databázi „lsgn-sdk“.
2. V řádku nabídek vyberte **Nastavit bránu firewall serveru**.

   ![Nastavení brány firewall serveru](./media/database-firewall/configure-server-firewall.png)

3. Pravidlo pro svou organizaci vytvoříte takto:

   * Zadejte **NÁZEV PRAVIDLA**.
   * Zadejte IP adresu do pole **POČÁTEČNÍ IP ADRESA** rozsahu adres.
   * Zadejte IP adresu do pole **KONCOVÁ IP ADRESA** rozsahu adres.

   ![Vytvoření pravidla brány firewall](./media/database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Pokud chcete přidat jen IP adresu svého počítače, zvolte **+ Přidat IP adresu klienta**.
        
1. Výběrem možnosti **Uložit** konfiguraci brány firewall uložte.
2. Připojením z aplikace nebo nástroje vyzkoušejte rozsah IP adres, který jste pro databázi nakonfigurovali. Použijte například SQL Server Management Studio.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zobrazení databáze ve službě Azure Blockchain Workbench](database-views.md)