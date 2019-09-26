---
title: zahrnout soubor
description: zahrnout soubor
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 09/12/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: fadbcf04f1cd474cf2d23963e88016d240272263
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71279883"
---
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Vytvoření Azure Database for PostgreSQL – Citus (škálování)

Server Azure Database for PostgreSQL vytvoříte pomocí tohoto postupu:
1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Na stránce **Nový** vyberte **Databáze** a na stránce **Databáze** vyberte **Azure Database for PostgreSQL**.
3. U možnosti nasazení klikněte na tlačítko **vytvořit** v části **Skupina serverů Citus (škálování na serveru) – Preview.**
4. Do formuláře podrobností o novém serveru zadejte následující informace:
   - Skupina prostředků: klikněte na odkaz **vytvořit nový** pod textovým polem pro toto pole. Zadejte název, například **myresourcegroup**.
   - Název skupiny serverů: Zadejte jedinečný název pro novou skupinu serverů, která se bude používat i pro subdoménu serveru.
   - Uživatelské jméno správce: v tuto chvíli se musí jednat o hodnotu **citus**a nedá se změnit.
   - Heslo: musí mít aspoň osm znaků a musí obsahovat znaky ze tří z následujících kategorií – velká písmena anglické abecedy, malá písmena anglické abecedy, číslice (0-9) a jiné než alfanumerické znaky (!, $, #,% a tak dále).
   - Umístění: použijte umístění, které je nejblíže vašim uživatelům, a poskytněte jim tak nejrychlejší přístup k datům.

   > [!IMPORTANT]
   > Heslo správce serveru, které tady zadáte, se vyžaduje pro přihlášení k serveru a jeho databázím. Tyto informace si zapamatujte nebo poznamenejte pro pozdější použití.

5. Klikněte na **Konfigurovat skupinu serverů**. Nastavení v této části ponechte beze změny a klikněte na **Uložit**.
6. Klikněte na **Další: >** Sítě v dolní části obrazovky.

7. Na kartě **síť** klikněte na přepínač **veřejný koncový bod** .
   ![Vybrán veřejný koncový bod](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Klikněte na odkaz **+ Přidat aktuální IP adresu klienta**.
   ![Přidaná IP adresa klienta](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png)

   > [!NOTE]
   > Server Azure PostgreSQL komunikuje přes port 5432. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 5432 bránou firewall vaší sítě povolený. Pokud je to tak, nebudete se moct připojit k serveru služby Azure SQL Database, dokud vaše IT oddělení neotevře port 5432.
   >

9. Kliknutím na tlačítko **zkontrolovat + vytvořit** a **vytvořit** Server zřiďte. Zřizování trvá několik minut.
10. Stránka se přesměruje na monitorování nasazení. Až se stav živé změny z **vašeho nasazení probíhají** po **nasazení**, klikněte na položku nabídky **výstupy** na levé straně stránky.
11. Stránka s výstupy bude obsahovat název hostitele koordinátora s tlačítkem vedle něj pro zkopírování hodnoty do schránky. Tyto informace si poznamenejte pro pozdější použití.

## <a name="connect-to-the-database-using-psql"></a>Připojení k databázi pomocí psql

Při vytváření serveru Azure Database for PostgreSQL se vytvoří výchozí databáze s názvem **citus** . Abyste se mohli připojit k databázovému serveru, potřebujete připojovací řetězec a heslo správce.

1. Získejte připojovací řetězec. Na stránce skupiny serverů klikněte na položku nabídky **připojovací řetězce** . (Jedná se o **Nastavení**.) Vyhledejte řetězec označený  **C++ (libpq)** . Bude mít formu:

   ```
   host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require
   ```

   Zkopírujte řetězec. Heslo pro správu, které jste si zvolili dříve, budete muset nahradit {Your\_Password}. Systém neuloží vaše heslo ve formátu prostého textu, a proto ho nemůže zobrazit v připojovacím řetězci.

2. Otevřete okno terminálu na místním počítači.

3. Na příkazovém řádku se připojte k serveru Azure Database for PostgreSQL pomocí nástroje [psql](https://www.postgresql.org/docs/current/app-psql.html) . Předá připojovací řetězec v uvozovkách, ujistěte se, že obsahuje vaše heslo:
   ```bash
   psql "{connection_string}"
   ```

   Například následující příkaz se připojí k uzlu koordinátora skupiny serveru **mydemoserver**:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
