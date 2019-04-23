---
title: Portál OMS do Azure | Dokumentace Microsoftu
description: Na portálu OMS se ještě sunsetted se všemi funkcemi přesouvá na portál Azure portal. Tento článek poskytuje podrobné informace o tomto přechodu.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: bwren
ms.openlocfilehash: c4950d03449f2b293a87ab88f1ea3f49eee29557
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790090"
---
# <a name="oms-portal-moving-to-azure"></a>Portál OMS do Azure

> [!NOTE]
> Tento článek se týká veřejného cloudu Azure i v cloudu pro státní správu není uvedeno jinak.

Na webu Azure portal je Centrum pro všechny služby Azure a nabízí bohaté možnosti správy prostředí s funkcemi, jako jsou řídicí panely pro Připnutí prostředků, inteligentní vyhledávání, vyhledání prostředků a označí pro správu prostředků. Ke konsolidaci a zjednodušte pracovní postupy monitorování a správu, bodu jsme začali přidávat možnosti portálu OMS na web Azure Portal. Všechny funkce na portálu OMS jsou teď součástí na webu Azure portal. Ve skutečnosti některé nové funkce, jako je například analýza provozu jsou k dispozici pouze na webu Azure Portal. Budete moct provádět vše, co jste dělali v portálu OMS pomocí webu Azure portal a dalších. Pokud jste tak již neučinili, měli byste začít pomocí webu Azure portal ještě dnes!

**Na portálu OMS se oficiálně vyřadí z provozu 15. ledna 2019** komerčním cloudu Azure a Azure US Government cloud, na portálu OMS **oficiálně skončí 30. března 2019.** Jsme nadšeni, přejděte na webu Azure portal a očekávají, že přechod na snadno. Ale chápeme změny je složité a můžou působit rušivě. Odesílat dotazy, názory a připomínky k **LAUpgradeFeedback\@microsoft.com**. Zbývající část tohoto článku prochází přes klíčové scénáře a plán pro tento přechod.

## <a name="what-is-changing"></a>Co se mění? 
Tyto změny jsou právě oznámili vyřazení podpory na portálu OMS. Každá z těchto změn je popsána podrobněji v následujících částech.

- Můžete vytvořit nová [pouze pracovní prostory](#new-workspaces) na webu Azure Portal.
- Nové prostředí pro správu výstrah [nahradí řešení pro správu upozornění](#changes-to-alerts).
- [Správa přístupu uživatelů](#user-access-and-role-migration) teď provádí na webu Azure Portal pomocí řízení přístupu na základě rolí Azure.
- [Application Insights Connector se už nevyžaduje](#application-insights-connector-and-solution) od stejné funkce je povolená díky dotazy napříč pracovního prostoru.
- [Mobilní aplikaci OMS](#oms-mobile-app) je zastaralé. 
- [Řešení NSG se nahrazuje](#azure-network-security-group-analytics) s vylepšené funkce, které jsou k dispozici prostřednictvím řešení pro analýzu provozu.
- Nové připojení z nástroje System Center Operations Manager ke službě Log Analytics vyžadují [aktualizovat sady management Pack](#system-center-operations-manager).
- V tématu [proveďte migraci vašich nasazení aktualizace OMS do Azure](../../automation/migrate-oms-update-deployments.md) pro podrobnosti o změnách [Update Management](../../automation/automation-update-management.md).


## <a name="what-should-i-do-now"></a>Co mám teď?
Zatímco většina funkcí budou nadále fungovat bez provádění jakékoli migrace, musíte provést následující úkoly:

- Je potřeba [migrovat vaše uživatelská oprávnění](#user-access-and-role-migration) k webu Azure portal.
- Zobrazit [proveďte migraci vašich nasazení aktualizace OMS do Azure](../../automation/migrate-oms-update-deployments.md) podrobnosti týkající se přechodu řešení Update Management.

Odkazovat na [běžné otázky týkající se přechodu z portálu OMS na webu Azure portal pro uživatele Log Analytics](oms-portal-faq.md) informace o tom, jak přechod na webu Azure portal. Odeslat žádné zpětnou vazbu, otázky nebo připomínky k **LAUpgradeFeedback\@microsoft.com**.

## <a name="user-access-and-role-migration"></a>Migrace přístup a role uživatele
Správa přístupu na portálu Azure je bohatší a výkonnější než správu přístupu na portálu OMS. Zobrazit [Správa pracovních prostorů](manage-access.md#manage-accounts-and-users) podrobné řízení přístupu ve službě Log Analytics.

> [!NOTE]
> Předchozí verze tohoto článku je uvedeno, že oprávnění by automaticky převést z portálu OMS na webu Azure portal. Tento automatický převod se už plánované a je třeba provést převod, sami.

Možná už máte správný přístup na webu Azure Portal v takovém případě nemusíte nic měnit. Existuje několik případů, kdy nemusí mít odpovídající přístup v takovém případě správce musíte přiřadit jste oprávnění.

- Máte oprávnění jen pro čtení na portálu OMS, ale žádná oprávnění na webu Azure Portal. 
- Máte oprávnění přispěvatele na portálu OMS, ale čtečky přístup jenom na webu Azure Portal.
 
V obou těchto případech musí správce ručně přiřadit vám příslušné role z následující tabulky. Doporučujeme, abyste přiřadili této role na úrovni skupiny nebo předplatného resource.  Podrobnější pokyny bude poskytována krátce obou těchto případech.

| Oprávnění na portálu OMS | Azure Role |
|:---|:---|
| ReadOnly | Čtenář Log Analytics |
| Přispěvatel | Přispěvatel Log Analytics |
| Správce | Vlastník | 
 

## <a name="new-workspaces"></a>Nové pracovní prostory
Jsou už nebude možné vytvořit nové pracovní prostory na portálu OMS. Postupujte podle pokynů v [vytvořit pracovní prostor Log Analytics na portálu Azure portal](../learn/quick-create-workspace.md) vytvořte nový pracovní prostor na webu Azure Portal.

## <a name="changes-to-alerts"></a>Změny výstrah

### <a name="alert-extension"></a>Oznámení rozšíření  

> [!NOTE]
> Výstrahy teď plně rozšířit do portálu Azure pro veřejný cloud. Stávající pravidla upozornění můžete zobrazit na portálu OMS, ale může být pouze spravované na portálu Azure portal. Rozšíření upozornění na web Azure Portal spustí pro cloud Azure government. února 2019.

Byl [rozšíří do portálu Azure portal](alerts-extend.md). Po jejím dokončení akce správy na výstrahy budou k dispozici pouze na webu Azure portal. Existující výstrahy bude dál zobrazovat na portálu OMS. Pokud k upozorněním přistupujete programově s využitím Log Analytics výstrah REST API nebo šablonu oznámení prostředku Log Analytics, budete muset použít skupiny akcí místo akcí ve volání rozhraní API, šablon Azure Resource Manageru a příkazech Powershellu.

### <a name="alert-management-solution"></a>Řešení pro správu výstrah
Jako změny z předchozí oznámení [řešení Alert managementu](alert-management-solution.md) budou nadále dostupné a plně se podporuje na webu Azure Portal. Můžete pokračovat v instalaci řešení z Azure Marketplace.

I když řešení pro správu výstrah zůstává k dispozici, doporučujeme vám použít [unified Azure Monitor výstrah rozhraní](alerts-overview.md) vizualizovat a spravovat všechna upozornění v Azure. Toto nové prostředí nativně agreguje výstrahy z více zdrojů v rámci Azure včetně upozornění protokolů ze služby Log Analytics. Pokud používáte jednotné rozhraní výstrahy monitorování Azure, pak do řešení pro správu výstrah je potřeba jenom povolení integrace výstrah ze System Center Operation Manageru do Azure. V jednotném rozhraní výstrahy monitorování Azure naleznete v tématu distribuce upozornění, využít výhod automatizovaného seskupení souvisejících výstrah pomocí inteligentní skupin a zobrazení výstrah napříč několika předplatnými při použití filtrů bohaté. Budoucí rozvoj při správě výstrah primárně budou k dispozici tyto nové možnosti. 

Údaje shromážděné tímto řešením Správa výstrah (záznamů s typem upozornění) i nadále v Log Analytics, dokud je řešení nainstalováno pro pracovní prostor. 

## <a name="oms-mobile-app"></a>Mobilní aplikaci OMS
Mobilní aplikaci OMS bude sunsetted spolu se na portálu OMS. Místo mobilní aplikaci OMS přístup k informacím o vašich IT infrastruktury, řídicí panely a uložené dotazy, můžete přistupovat na webu Azure portal přímo z prohlížeče v mobilním zařízení. Abyste mohli dostávat upozornění, měli byste nakonfigurovat [skupiny akcí Azure](action-groups.md) pro příjem oznámení ve formě SMS nebo hlasový hovor

## <a name="application-insights-connector-and-solution"></a>Application Insights Connector a řešení
[Application Insights Connector](app-insights-connector.md) poskytuje způsob, jak zahrnout data Application Insights do pracovního prostoru Log Analytics. Tato duplikace dat nebyla nutná k zajištění viditelnost napříč dat infrastruktury a aplikací. Rozšířená podpora uchovávání dat v březnu. května 2019 Application Insights a schopnost provádět [dotazy napříč prostředky](../log-query/cross-workspace-query.md) vedle možnosti [zobrazení více prostředků služby Azure Monitor Application Insights ](../log-query/unify-app-resource-data.md), není nutné duplikovat data z vašich prostředků Application Insights a odesílat do Log Analytics. Kromě toho konektor odešle podmnožinu vlastností aplikace ke službě Log Analytics, zatímco dotazy napříč prostředky poskytuje lepší flexibilitu.  

V důsledku toho Application Insights Connector bude zastaralé a odebrána z Azure Marketplace spolu s vyřazení portálu OMS na 30. března 2019 v době, kdy existující připojení budou i nadále fungovat až do 30. června 2019. S vyřazení portálu OMS neexistuje žádný způsob, jak nakonfigurovat a odeberte existující připojení z portálu. Tato funkčnost bude podporovaná pomocí rozhraní REST API, která bude k dispozici v lednu, 2019 a oznámení se publikuje na [aktualizace Azure](https://azure.microsoft.com/updates/). 

## <a name="azure-network-security-group-analytics"></a>Analýzy skupin zabezpečení sítě Azure
[Řešení analýzy skupin zabezpečení sítě Azure](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) nahradí s nedávno uvedená [analýzu provozu](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) který poskytuje přehled o aktivitě uživatelů a aplikací v cloudových sítích. Analýza provozu umožňuje auditovat aktivitu sítě vaší organizace, zabezpečených aplikací a dat, optimalizovat výkon úloh a zajistěte dodržování předpisů. 

Toto řešení analyzuje protokoly toků NSG a poskytuje přehledy o následující.

- Provoz probíhá mezi vaší sítí mezi Azure a Internet, veřejných cloudových oblastech, virtuálních sítí a podsítí.
- Aplikace a protokoly ve vaší síti bez potřeby vyhrazených tok kolekce zařízení nebo zjištění je snazší.
- Hlavní vyjádření, přetížení aplikací, konverzace virtuálního počítače v cloudu, hotspotům provoz.
- Zdroje a cíle provozu mezi virtuálními sítěmi, vzájemných vztahů mezi nejdůležitější obchodní služby a aplikace.
- Zabezpečení, včetně škodlivý provoz, porty otevřené pro Internet, aplikace nebo virtuální počítače pokusu o přístup k Internetu.
- Využití kapacity, která pomáhá eliminovat problémy průběhu zřizování nebo nízkého využití.

Můžete nadále závisí na nastavení diagnostiky pro odesílání protokolů skupiny zabezpečení sítě ke službě Log Analytics, takže stávající uložit hledání, upozornění, řídicí panely se budou nadále fungovat. Zákazníci, kteří už jste nainstalovali řešení můžete ho nadále používat až do dalšího upozornění. Od 5. září se řešení analýzy skupin zabezpečení sítě budou odebrány z webu marketplace a k dispozici prostřednictvím komunity jako [šablonu pro rychlý start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Pokud jste [připojené skupině pro správu Operations Manageru k Log Analytics](om-agents.md), bude dál fungovat beze změny. Pro nová připojení, je nutné postupovat podle pokynů v [Microsoft System Center Operations Manager Management Pack nakonfigurovat Operations Management Suite](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/).

## <a name="next-steps"></a>Další postup
- Zobrazit [běžné otázky týkající se přechodu z portálu OMS na webu Azure portal pro uživatele Log Analytics](oms-portal-faq.md) pro doprovodné materiály k přesunutí z portálu OMS na webu Azure portal.
