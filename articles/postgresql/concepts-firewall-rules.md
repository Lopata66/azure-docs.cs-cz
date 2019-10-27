---
title: Pravidla brány firewall v Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje pravidla brány firewall pro Azure Database for PostgreSQL jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 6b4896d78bcc6e9fc1f5d9cd47e60e3df7eba325
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965269"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Pravidla brány firewall v Azure Database for PostgreSQL – jeden server
Brána firewall serveru Azure Database for PostgreSQL zabraňuje všem přístupům k vašemu databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Brána firewall uděluje přístup k serveru na základě zdrojové IP adresy jednotlivých požadavků.
Bránu firewall nakonfigurujete tak, že vytvoříte pravidla brány firewall určující rozsahy přípustných IP adres. Pravidla brány firewall můžete vytvořit na úrovni serveru.

**Pravidla brány firewall:** Tato pravidla umožňují klientům přístup k celému serveru Azure Database for PostgreSQL, tedy ke všem databázím v rámci stejného logického serveru. Pravidla brány firewall na úrovni serveru se dají nakonfigurovat pomocí Azure Portal nebo pomocí příkazů rozhraní příkazového řádku Azure. Chcete-li vytvořit pravidla brány firewall na úrovni serveru, musíte být vlastníkem předplatného nebo přispěvatelem předplatného.

## <a name="firewall-overview"></a>Přehled brány firewall
Všechna přístupová práva k serveru Azure Database for PostgreSQL jsou ve výchozím nastavení blokována branou firewall. Pokud chcete začít používat server z jiného počítače, musíte zadat jedno nebo více pravidel brány firewall na úrovni serveru, abyste mohli povolit přístup k vašemu serveru. Pomocí pravidel brány firewall určete, které rozsahy IP adres z Internetu mají být povoleny. Přístup k samotnému webu Azure Portal nemá vliv na pravidla brány firewall.
Pokusy o připojení z Internetu a Azure musí nejdřív projít přes bránu firewall, aby se mohli připojit k databázi PostgreSQL, jak je znázorněno v následujícím diagramu:

![Příklad toku fungování brány firewall](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Připojení z Internetu
Pravidla brány firewall na úrovni serveru se vztahují na všechny databáze na stejném serveru Azure Database for PostgreSQL. Pokud je IP adresa požadavku v jednom z rozsahů určených v pravidlech brány firewall na úrovni serveru, připojení je povoleno.
Pokud IP adresa požadavku není v rozsahu zadaném v žádném z pravidel brány firewall na úrovni serveru, požadavek na připojení se nezdařil.
Pokud se například vaše aplikace připojí pomocí ovladače JDBC pro PostgreSQL, může se při pokusu o připojení, když brána firewall blokuje připojení, setkat.
> Java. util. souběžné. ExecutionException: Java. lang. RuntimeException –: org. PostgreSQL. util. PSQLException: ZÁVAŽNá: No pg\_HBA. conf pro hostitele "123.45.67.890", User "AdminUser", Database "PostgreSQL", SSL

## <a name="connecting-from-azure"></a>Připojení z Azure
Pokud chcete aplikacím z Azure povolit připojení k vašemu Azure Database for PostgreSQL serveru, musí být povolená připojení Azure. Například pro hostování aplikace Web Apps v Azure nebo aplikace, která běží na virtuálním počítači Azure, nebo pro připojení z Azure Data Factory brány pro správu dat. Aby se tato připojení dala povolit, nemusíte být prostředky ve stejné Virtual Network (virtuální síti) nebo skupině prostředků pro pravidlo brány firewall. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. K dispozici je několik metod, jak tyto typy připojení povolit. Nastavení brány firewall s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou tato připojení povolená. Alternativně můžete na portálu v podokně **zabezpečení připojení** nastavit možnost **Povolení přístupu ke službám Azure** na **zapnuto** a potom kliknout na **Uložit**. Pokud se pokus o připojení nepovoluje, požadavek nedosáhne serveru Azure Database for PostgreSQL.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

![Konfigurace povolení přístupu ke službám Azure na portálu](media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Připojení z virtuální sítě
Pokud chcete bezpečně připojit k serveru Azure Database for PostgreSQL z virtuální sítě, zvažte použití [koncových bodů služby virtuální](./concepts-data-access-and-security-vnet.md)sítě. 

## <a name="programmatically-managing-firewall-rules"></a>Programová správa pravidel brány firewall
Kromě Azure Portal můžete pravidla brány firewall spravovat programově pomocí Azure CLI.
Viz také [Vytvoření a Správa pravidel brány firewall Azure Database for PostgreSQL pomocí Azure CLI](howto-manage-firewall-using-cli.md) .

## <a name="troubleshooting-firewall-issues"></a>Řešení potíží s bránou firewall
Vezměte v úvahu následující body, pokud se přístup k Microsoft Azure databázi pro službu serveru PostgreSQL nechová podle očekávání:

* **Změny v seznamu povolených se zatím neprojevily:** Změny konfigurace brány firewall serveru Azure Database for PostgreSQL se projeví až po dobu pěti minut.

* **Přihlášení není autorizováno nebo bylo použito nesprávné heslo:** Pokud přihlášení nemá oprávnění k serveru Azure Database for PostgreSQL nebo je použité heslo nesprávné, připojení k serveru Azure Database for PostgreSQL je odepřeno. Při vytváření nastavení brány firewall jsou jenom klienti s příležitostí k pokusu o připojení k vašemu serveru. Každý klient musí stále zadat potřebná zabezpečovací pověření.

   Například pomocí klienta JDBC se může zobrazit následující chyba.
   > Java. util. souběžné. ExecutionException: Java. lang. RuntimeException –: org. PostgreSQL. util. PSQLException: ZÁVAŽNá: ověřování hesla pro uživatele "uživatelské_jméno" se nezdařilo.

* **Dynamická IP adresa:** Pokud vaše internetové připojení používá dynamické přidělování IP adres a máte problémy dostat se přes bránu firewall, můžete zkusit jedno z následujících řešení:

   * Zeptejte se poskytovatele internetových služeb (ISP) na rozsah IP adres přiřazený ke klientským počítačům, které přistupují k serveru Azure Database for PostgreSQL, a pak přidejte rozsah IP adres jako pravidlo brány firewall.

   * Místo toho Získejte statické IP adresy pro klientské počítače a pak přidejte statickou IP adresu jako pravidlo brány firewall.

* **IP adresa serveru je pravděpodobně veřejná:** Připojení k Azure Database for PostgreSQL serveru jsou směrována prostřednictvím veřejně přístupné brány Azure. Skutečná IP adresa serveru je však chráněná bránou firewall. Další informace najdete v [článku věnovaném architektuře připojení](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Další kroky
Články o vytváření pravidel brány firewall na úrovni serveru a databáze najdete v těchto tématech:
* [Vytváření a Správa Azure Database for PostgreSQL pravidel brány firewall pomocí Azure Portal](howto-manage-firewall-using-portal.md)
* [Vytvoření a Správa pravidel brány firewall Azure Database for PostgreSQL pomocí rozhraní příkazového řádku Azure](howto-manage-firewall-using-cli.md)
- [Koncové body služby virtuální sítě v Azure Database for PostgreSQL](./concepts-data-access-and-security-vnet.md)
