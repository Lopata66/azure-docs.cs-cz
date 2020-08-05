---
title: Aktualizace pomocí Logic Apps pro Azure Analysis Services modely | Microsoft Docs
description: Tento článek popisuje, jak pomocí Azure Logic Apps kódovat asynchronní aktualizace pro Azure Analysis Services.
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.custom: references_regions
ms.openlocfilehash: 7412a28b53f3b17fb888e3877ecbe50a19c4a3d3
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552233"
---
# <a name="refresh-with-logic-apps"></a>Aktualizace pomocí Logic Apps

Pomocí Logic Apps a volání REST můžete provádět automatizované operace aktualizace dat ve vašich tabelárních modelech Azure Analysis, včetně synchronizace replik jen pro čtení pro škálování dotazů.

Další informace o použití rozhraní REST API s Azure Analysis Services najdete v tématu [asynchronní aktualizace s REST API](analysis-services-async-refresh.md).

## <a name="authentication"></a>Ověřování uživatelů

Všechna volání musí být ověřena pomocí platného tokenu Azure Active Directory (OAuth 2).  Příklady v tomto článku budou k ověření Azure Analysis Services použít instanční objekt (SPN). Další informace najdete v tématu [Vytvoření instančního objektu pomocí služby Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Návrh aplikace logiky

> [!IMPORTANT]
> V následujících příkladech se předpokládá, že je brána firewall Azure Analysis Services zakázaná. Pokud je povolená brána firewall, musí být v bráně Azure Analysis Services firewall povolená veřejná IP adresa iniciátoru žádosti. Další informace o Azure Logic Apps rozsahech IP adres na oblast najdete v tématu [omezení a informace o konfiguraci pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#configuration).

### <a name="prerequisites"></a>Předpoklady

#### <a name="create-a-service-principal-spn"></a>Vytvoření instančního objektu (SPN)

Další informace o vytváření instančního objektu najdete v tématu [Vytvoření instančního objektu pomocí Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurace oprávnění v Azure Analysis Services
 
Objekt služby, který vytvoříte, musí mít na serveru oprávnění správce serveru. Další informace najdete v tématu [Přidání instančního objektu k roli správce serveru](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Konfigurace aplikace logiky

V tomto příkladu je aplikace logiky navržena tak, aby se aktivovala při přijetí požadavku HTTP. Tím se povolí použití nástroje orchestrace, jako je například Azure Data Factory, k aktivaci Azure Analysis Services aktualizace modelu.

Jakmile vytvoříte aplikaci logiky:

1. V návrháři aplikace logiky vyberte první akci, jako **když se přijme požadavek HTTP**.

   ![Přidání aktivity přijaté protokolem HTTP](./media/analysis-services-async-refresh-logic-app/1.png)

Po uložení aplikace logiky se tento krok naplní adresou URL POST protokolu HTTP.

2. Přidejte nový krok a vyhledejte **http**.  

   ![Přidat aktivitu HTTP](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Přidat aktivitu HTTP](./media/analysis-services-async-refresh-logic-app/10.png)

3. Vyberte **http** a přidejte tuto akci.

   ![Přidat aktivitu HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Aktivitu HTTP nakonfigurujte následujícím způsobem:

|Vlastnost  |Hodnota  |
|---------|---------|
|**Metoda**     |POST         |
|**Identifikátor URI**     | https://*na server region*/Servers/*AAS název serveru*/Models/*název vaší databáze*/refreshes <br /> <br /> Příklad: https: \/ /westus.asazure.Windows.NET/Servers/MyServer/Models/AdventureWorks/refreshes|
|**Záhlaví**     |   Content-Type, Application/JSON <br /> <br />  ![Hlavičky](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Text**     |   Další informace o vytváření textu žádosti najdete v tématu [asynchronní aktualizace pomocí REST API-post/refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Ověřování uživatelů**     |Protokol OAuth pro Active Directory         |
|**Tenant**     |Vyplňte Azure Active Directory TenantId         |
|**Cílová skupina**     |https://*. asazure. Windows. NET         |
|**ID klienta**     |Zadejte své hlavní název služby ClientID.         |
|**Typ přihlašovacích údajů**     |Tajný kód         |
|**Tajný kód**     |Zadejte tajný klíč hlavního názvu služby.         |

Příklad:

![Dokončená aktivita HTTP](./media/analysis-services-async-refresh-logic-app/7.png)

Nyní otestujte aplikaci logiky.  V návrháři aplikace logiky klikněte na **Spustit**.

![Otestování aplikace logiky](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Využití aplikace logiky s Azure Data Factory

Po uložení aplikace logiky zkontrolujte, jestli **je přijatá žádost http** , a pak zkopírujte **adresu URL post protokolu HTTP** , který je teď vygenerovaný.  Toto je adresa URL, kterou může Azure Data Factory použít k tomu, aby asynchronní volání aktivovalo aplikaci logiky.

Tady je příklad Azure Data Factory aktivity webu, která provádí tuto akci.

![Data Factory aktivity webu](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Použití samostatně obsažené aplikace logiky

Pokud neplánujete použití nástroje Orchestration, jako je například Data Factory, aby se aktivovala aktualizace modelu, můžete nastavit aplikaci logiky tak, aby na základě plánu aktivovala aktualizaci.

Pomocí výše uvedeného příkladu odstraňte první aktivitu a nahraďte ji aktivitou **plánu** .

![Aktivita plánu](./media/analysis-services-async-refresh-logic-app/12.png)

![Aktivita plánu](./media/analysis-services-async-refresh-logic-app/13.png)

V tomto příkladu se použije **opakování**.

Po přidání aktivity nakonfigurujte interval a četnost a pak přidejte nový parametr a zvolte **v těchto hodinách**.

![Aktivita plánu](./media/analysis-services-async-refresh-logic-app/16.png)

Vyberte požadované hodiny.

![Aktivita plánu](./media/analysis-services-async-refresh-logic-app/15.png)

Uložte aplikaci logiky.

## <a name="next-steps"></a>Další kroky

[ukázky](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
