---
title: Postup obnovení serveru ve službě Azure Database for PostgreSQL – jeden Server
description: Tento článek popisuje, jak obnovit server ve službě Azure Database for PostgreSQL – jeden Server pomocí webu Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 1950b43e0922eebe34463c06db9a5d67dce76f56
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068880"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Zálohování a obnovení serveru ve službě Azure Database for PostgreSQL – jeden Server pomocí webu Azure portal

## <a name="backup-happens-automatically"></a>Zálohování se automaticky stane
Azure Database for PostgreSQL servery se pravidelně zálohují k povolení funkce obnovení. Pomocí této funkce může obnovení serveru a jeho databázím do starší v daném okamžiku, na novém serveru.

## <a name="set-backup-configuration"></a>Nastavit konfiguraci zálohování

Zkontrolujte výběr mezi konfigurací v serveru pro místně redundantní zálohy nebo geograficky redundantní zálohy při vytváření serveru **cenová úroveň** okna.

> [!NOTE]
> Po vytvoření serveru, typ redundance, které obsahuje, není možné přepnout místně redundantní, geograficky redundantní vs.
>

Při vytváření serveru na webu Azure portal, **cenová úroveň** je okno, kde můžete buď vybrat **místně redundantní** nebo **geograficky redundantní** zálohy váš server. Toto okno se také vybíráte **období uchování zálohy** – jak dlouho (ve dnech) má server zálohy uložené pro.

   ![Cenová úroveň - zvolte redundance zálohy](./media/howto-restore-server-portal/pricing-tier.png)

Další informace o nastavení tyto hodnoty během vytváření, najdete v článku [– Azure Database for postgresql – Rychlý začátek server](quickstart-create-server-database-portal.md).

Období uchování zálohy serveru lze změnit pomocí následujících kroků:
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte svůj server Azure Database for PostgreSQL. Tato akce otevře **přehled** stránky.
3. Vyberte **cenová úroveň** v nabídce v části **nastavení**. Použijte jezdce můžete změnit **období uchování zálohy** dle požadavků mezi 7 až 35 dnů.
V následujícím snímku obrazovky to bylo zvýšeno na 34 dnů.
![Zvýšení doby uchovávání záloh](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Klikněte na tlačítko **OK** pro potvrzení změny.

Období uchování zálohy se řídí jak daleko zpět v čase, které mohou být načteny obnovení bodu v čase, protože je založen na dostupné zálohy. Obnovení k určitému bodu v čase je popsána dále v následující části. 

## <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase
Azure Database for PostgreSQL umožňuje obnovení serveru zpět v daném okamžiku a do novou kopii na serveru. Můžete použít tento nový server pro obnovení dat nebo mít vaší klientské aplikace, přejděte na tento nový server.

Například pokud tabulku omylem v poledne v současné době můžete obnovit do doby před polednem a načíst chybějící tabulku a data z této nové kopie serveru. Obnovení k určitému bodu v čase není na serveru úrovně, na úrovni databáze.

Následující kroky obnoví ukázkový server k určitému bodu v čase:
1. Na webu Azure Portal vyberte váš server Azure Database for PostgreSQL. 

2. Na panelu nástrojů na server **přehled** stránce **obnovení**.

   ![Azure Database for PostgreSQL – přehled – obnovení tlačítko](./media/howto-restore-server-portal/2-server.png)

3. Vyplňte formulář obnovit požadované údaje:

   ![Azure Database for PostgreSQL – informace o obnovení](./media/howto-restore-server-portal/3-restore.png)
   - **Bod obnovení**: Vyberte v daném okamžiku, kterou chcete obnovit.
   - **Cílový server**: Zadejte název pro nový server.
   - **Umístění**: Nejde vyberte oblast. Ve výchozím nastavení je stejná jako u zdrojového serveru.
   - **Cenová úroveň**: Při obnovení bodu v čase nelze změnit tyto parametry. Je stejná jako u zdrojového serveru. 

4. Klikněte na tlačítko **OK** k obnovení serveru k obnovení v daném okamžiku. 

5. Po dokončení obnovení vyhledejte nový server, který je vytvořen, chcete-li ověřit, že se že data obnovila podle očekávání.

>[!Note]
>Nový server vytvořil obnovení k určitému bodu v čase má stejné přihlašovací jméno správce serveru a zvolili hesla, která byla platná pro existující server v bodu v čase. Můžete změnit heslo z nového serveru **přehled** stránky.

## <a name="geo-restore"></a>Geografické obnovení
Pokud jste nakonfigurovali server pro geograficky redundantní zálohy, lze vytvořit nový server ze zálohy existujícího serveru. Tento nový server lze vytvořit v libovolné oblasti Azure Database for PostgreSQL je k dispozici.  

1. Vyberte tlačítko **Vytvořit prostředek** (+) v levém horním rohu portálu. Vyberte **Databáze** > **Azure Database for PostgreSQL**.

   ![Možnost Azure Database for PostgreSQL](./media/howto-restore-server-portal/1-navigate-to-postgres.png)

2. Do formuláře **vybrat zdroj** rozevíracím seznamu zvolte **zálohování**. Tato akce načte seznam serverů, které mají geograficky redundantní zálohy povolena. Vyberte jednu z těchto záloh sloužit jako zdroj pro nový server.
   ![Vyberte zdroj: Zálohování a seznam geograficky redundantní zálohy](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > Při prvním vytvoření serveru nemusí být hned dostupné pro geografické obnovení. Může trvat několik hodin nezbytných metadat, který se má naplnit.
   >

3. Vyplňte zbývající část Formulář s předvolby. Můžete vybrat libovolný **umístění**. Až vyberete umístění, můžete vybrat **cenová úroveň**. Ve výchozím nastavení se zobrazují parametry obnovovaný z existující server. Můžete kliknout na **OK** bez jakýchkoli změn pro dědění nastavení. Nebo můžete změnit **výpočetní generaci** (Pokud k dispozici v oblasti jste vybrali), počet **virtuálních jader**, **období uchování zálohy**, a **zálohování Možnosti redundance**. Změna **cenová úroveň** (Basic, obecné účely nebo k paměťově optimalizovaným) nebo **úložiště** velikost během obnovení se nepodporuje.

>[!Note]
>Nový server vytvořil geografické obnovení má stejný přihlašovací jméno správce serveru a hesla, která byla platná pro existující server v době, kdy bylo zahájeno obnovení. Heslo lze změnit z nového serveru **přehled** stránky.


## <a name="next-steps"></a>Další postup
- Další informace týkající se služby [zálohy](concepts-backup.md).
- Další informace o [kontinuita podnikových procesů](concepts-business-continuity.md) možnosti.
