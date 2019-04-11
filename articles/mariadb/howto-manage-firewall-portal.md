---
title: Vytvoření a Správa pravidel brány firewall MariaDB ve službě Azure Database pro MariaDB
description: Vytváření a správa Azure Database pro MariaDB pravidla brány firewall pomocí webu Azure portal
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: e9ab243692f5a4a1ec7de25774f5bad867698fc3
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469994"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-portal"></a>Vytváření a správa Azure Database pro MariaDB pravidla brány firewall pomocí webu Azure portal
Pravidla brány firewall na úrovni serveru umožňuje spravovat přístup ke službě Azure Database pro MariaDB serveru ze zadaná IP adresa nebo rozsah IP adres.

Pravidel virtuální sítě (VNet) je také možné zabezpečit přístup k vašemu serveru. Další informace o [vytváření a Správa virtuální sítě služby koncových bodů a pravidel pomocí webu Azure portal](howto-manage-vnet-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Vytvoření pravidla brány firewall na úrovni serveru na webu Azure Portal

1. Na stránce server MariaDB, v části Nastavení záhlaví, klikněte na tlačítko **zabezpečení připojení** otevřete stránku zabezpečení připojení pro službu Azure Database pro MariaDB.

   ![Portál Azure – zabezpečení připojení klikněte na](./media/howto-manage-firewall-portal/1-connection-security.png)

2. Klikněte na tlačítko **přidat moji IP adresu** na panelu nástrojů. Tím automaticky se vytvoří pravidlo brány firewall s veřejnou IP adresu počítače, vnímanou systému Azure.

   ![Azure portal – klikněte na tlačítko Přidat Moje IP adresa](./media/howto-manage-firewall-portal/2-add-my-ip.png)

3. Před uložením konfiguraci ověřte vaši IP adresu. V některých případech IP adresu prostřednictvím webu Azure portal se liší od IP adresu používat při přístupu k Internetu a servery Azure. Proto budete muset změnit počáteční IP adresa a koncová IP adresa, aby pravidlo fungují podle očekávání.

   Pomocí vyhledávacího webu nebo jiný nástroj pro online zkontrolujte svou vlastní IP adresu. Například hledání "jaká je Moje IP adresa".

4. Přidejte další adresní rozsahy adres. V pravidlech brány firewall pro službu Azure Database pro MariaDB můžete zadat jednu IP adresu nebo rozsah adres. Pokud chcete omezit platnost pravidla pro jednu IP adresu, zadejte stejnou adresu do pole Počáteční IP adresa a koncová IP adresa. Otevření brány firewall umožňuje správci, uživatelé a aplikace pro přístup k libovolné databázi na serveru MariaDB, ke kterému mají platné přihlašovací údaje.

   ![Azure portal – pravidla brány firewall](./media/howto-manage-firewall-portal/4-specify-addresses.png)

5. Klikněte na tlačítko **Uložit** na panelu nástrojů uložte toto pravidlo brány firewall na úrovni serveru. Čekání na potvrzení, jestli aktualizace, která se pravidla brány firewall se úspěšně dokončila.

   ![Azure portal – klikněte na možnost Uložit](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Připojení z Azure
Chcete-li umožnit aplikacím z Azure připojení k Azure Database pro MariaDB server, musí být povolená připojení Azure. Například pro hostování aplikace Azure Web Apps nebo aplikace, která běží ve Virtuálním počítači Azure nebo k připojení ze Brána pro správu dat Azure Data Factory. Prostředky se nemusíte být ve stejné virtuální síti (VNet) nebo skupinu prostředků pro pravidlo brány firewall, aby tato připojení. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. Existuje několik metod, které chcete povolit tyto typy připojení. Nastavení brány firewall s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou tato připojení povolená. Alternativně můžete nastavit **povolit přístup ke službám Azure** umožňuje **ON** portálu ze **zabezpečení připojení** podokně a klepněte na **Uložit**. Pokud pokus o připojení není povolený, požadavek vůbec nedostane Azure Database pro MariaDB server.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

## <a name="manage-existing-firewall-rules-in-the-azure-portal"></a>Správa stávajících pravidel brány firewall na webu Azure Portal
Opakováním těchto kroků můžete spravovat pravidla brány firewall.
* Chcete-li přidat aktuální počítač, klikněte na tlačítko **+ přidat moji IP adresu**. Kliknutím na **Uložit** uložte změny.
* Chcete-li přidat další IP adresy, zadejte **název pravidla**, **počáteční IP adresa**, a **KONCOVÁ IP adresa**. Kliknutím na **Uložit** uložte změny.
* Pokud chcete upravit stávající pravidlo, klikněte na libovolné pole pravidla a potom upravte. Kliknutím na **Uložit** uložte změny.
* Pokud chcete odstranit stávající pravidlo, klikněte na tlačítko se třemi tečkami [...] a pak klikněte na **odstranit**. Kliknutím na **Uložit** uložte změny.

## <a name="next-steps"></a>Další postup
 - Podobně můžete používat skripty pro [vytvořit a spravovat Azure Database pro MariaDB pravidla brány firewall pomocí Azure CLI](howto-manage-firewall-cli.md).
 - Dále zabezpečit přístup k vašemu serveru pomocí [vytváření a Správa virtuální sítě služby koncových bodů a pravidel pomocí webu Azure portal](howto-manage-vnet-portal.md).