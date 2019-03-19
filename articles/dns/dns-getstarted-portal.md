---
title: Rychlý start – vytvoření zóny DNS a záznamu pomocí webu Azure portal
description: Použijte tuto příručku podrobné rychlém startu se naučíte vytvářet zóny DNS a záznamu pomocí webu Azure portal.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: feb46114b3cf1b04e6a181f84bcdc41c17f1c0ba
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119067"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-the-azure-portal"></a>Rychlý start: Vytvoření Azure zóny a záznamu DNS pomocí webu Azure portal

Azure DNS můžete nakonfigurovat k překladu názvů hostitelů ve veřejné doméně. Například, pokud jste zakoupili *contoso.xyz* název domény registrátora názvu domény, můžete nakonfigurovat Azure DNS na hostitele *contoso.xyz* domény a vyřešení *www.contoso.xyz* IP adresu vašeho webového serveru nebo webové aplikace.

V tomto rychlém startu vytvoříte testovací doménu a pak vytvořte záznam adres k vyřešení *www* na IP adresu *10.10.10.10*.

>[!IMPORTANT]
>Všechny názvy a IP adresy v rámci tohoto rychlého startu jsou příklady, které nepředstavují reálné situace.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

U všech postupů portálu přihlásit [webu Azure portal](https://portal.azure.com).

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

Zóna DNS obsahuje záznamy DNS pro doménu. Pokud chcete začít hostovat svou doménu v Azure DNS, můžete vytvořit zónu DNS pro daný název domény. 

**Vytvoření zóny DNS:**

1. Vlevo nahoře vyberte **vytvořit prostředek**, pak **sítě**a potom **zónu DNS**.

1. Na **vytvořit zónu DNS** stránky zadejte nebo vyberte následující hodnoty:

   - **Název**: Typ *contoso.xyz* pro účely tohoto příkladu rychlého startu. Název zóny DNS může být libovolná hodnota, která již není nakonfigurovaná na servery Azure DNS. Ve skutečné situaci byste použili doménu zakoupenou od registrátora názvu domény.
   - **Skupina prostředků**: Vyberte **vytvořit nový**, zadejte *MyResourceGroup*a vyberte **OK**. Název skupiny prostředků musí být jedinečný v rámci předplatného Azure. 

1. Vyberte **Vytvořit**.

   ![Zóna DNS](./media/dns-getstarted-portal/openzone650.png)

Vytvoření zóny může trvat několik minut.

## <a name="create-a-dns-record"></a>Vytvoření záznamu DNS

Vytvoření položky DNS nebo záznamy pro vaši doménu v zóně DNS. Vytvořte nový záznam adresy nebo "A" záznam přeložit název hostitele na adresu IPv4.

**Chcete-li vytvořit záznam "A":**

1. Na webu Azure Portal v části **všechny prostředky**, otevřete **contoso.xyz** zónu DNS **MyResourceGroup** skupinu prostředků. Můžete zadat *contoso.xyz* v **filtrovat podle názvu** pole našli snadněji.

1. V horní části **zónu DNS** stránce **+ sada záznamů**.

1. Na **přidat sadu záznamů** stránky zadejte nebo vyberte následující hodnoty:

   - **Název**: Typ *www*. Název záznamu je název hostitele, který chcete přeložit na zadaná IP adresa.
   - **Typ**: Vyberte **A**. "A" záznamy jsou nejčastěji používané, ale existují další typy záznamů pro servery e-mailu ("MX"), IP adresy IPv6 (AAAA) a tak dále. 
   - **TTL**: Typ *1*. *Time-to-live* DNS požadavku Určuje servery DNS a klienty jak dlouho může ukládat do mezipaměti odpovědi.
   - **Jednotka hodnoty TTL**: Vyberte **hodin**. Toto je jednotka času **TTL** hodnotu. 
   - **IP adresa**: V tomto příkladu rychlého startu zadejte *10.10.10.10*. Tato hodnota je, že překládá název záznamu IP adresu. Ve skutečném scénáři by zadejte veřejnou IP adresu pro váš webový server.

V tomto rychlém startu je jen pro rychlé testování, není nutné nakonfigurovat názvové servery Azure DNS u registrátora názvu domény. Skutečné produkční doménu měli byste kdokoli na Internetu, abychom mohli přeložit název hostitele pro připojení k webu nebo aplikaci. Budete navštivte web registrátora názvu domény nahraďte názvové servery Azure DNS záznamy názvového serveru. Další informace najdete v tématu [kurzu: Hostování domény v Azure DNS](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="test-the-name-resolution"></a>Testování překladu IP adres

Teď, když máte testovací zóna DNS se záznam testu "A", můžete otestovat překlad názvů s nástroj zvaný *nslookup*. 

**K otestování překlad názvů DNS:**

1. Na webu Azure Portal v části **všechny prostředky**, otevřete **contoso.xyz** zónu DNS **MyResourceGroup** skupinu prostředků. Můžete zadat *contoso.xyz* v **filtrovat podle názvu** pole našli snadněji.

1. Zkopírujte jeden z názvů názvových serverů ze seznamu na název serveru na **přehled** stránky. 

   ![zóna](./media/dns-getstarted-portal/viewzonens500.png)

1. Otevřete příkazový řádek a spusťte následující příkaz:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Příklad:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   By měl vypadat přibližně jako na následujícím obrázku:

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

Název hostitele **www\.contoso.xyz** přeloží na **10.10.10.10**, stejně jako jste nakonfigurovali. Tento výsledek ověří, že překlad názvů funguje správně. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky vytvořené v rámci tohoto rychlého startu, odeberte je tak, že odstraníte **MyResourceGroup** skupinu prostředků. Otevřít **MyResourceGroup** prostředku, skupiny a vyberte **odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)