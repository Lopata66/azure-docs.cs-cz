---
title: Odstraňování běžných chyb
description: Naučte se řešit problémy s vytvářením definic zásad, různých SDK a doplňku pro Kubernetes.
ms.date: 05/22/2020
ms.topic: troubleshooting
ms.openlocfilehash: 868b8c53a120ebdb6a35806538e02af39e25c338
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970837"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Řešení chyb pomocí Azure Policy

Při vytváření definic zásad, práci se sadou SDK nebo nastavení [Azure Policy pro doplněk Kubernetes](../concepts/policy-for-kubernetes.md) můžete narazit na chyby. Tento článek popisuje různé chyby, ke kterým může dojít, a jejich řešení.

## <a name="finding-error-details"></a>Hledání podrobností o chybě

Umístění podrobností o chybě závisí na akci, která způsobuje chybu.

- Pokud pracujete s vlastními zásadami, zkuste to v Azure Portal, abyste získali zpětnou vazbu ke schématu nebo zkontrolovali výsledná [data dodržování předpisů](../how-to/get-compliance-data.md) , abyste viděli, jak byly vyhodnoceny prostředky.
- Při práci s různými SDK poskytuje sada SDK podrobné informace o tom, proč se funkce nezdařila.
- Při práci s doplňkem pro Kubernetes začněte [protokolováním](../concepts/policy-for-kubernetes.md#logging) clusteru.

## <a name="general-errors"></a>Obecné chyby

### <a name="scenario-alias-not-found"></a>Scénář: alias nebyl nalezen.

#### <a name="issue"></a>Problém

Azure Policy používá k mapování Azure Resource Manager vlastností [aliasy](../concepts/definition-structure.md#aliases) .

#### <a name="cause"></a>Příčina

V definici zásad se používá nesprávný nebo neexistující alias.

#### <a name="resolution"></a>Řešení

Nejprve ověřte, zda má vlastnost Správce prostředků alias. K vyhledání dostupných aliasů použijte [rozšíření Azure Policy pro Visual Studio Code](../how-to/extension-for-vscode.md), [Azure Resource Graph](../../resource-graph/samples/starter.md#distinct-alias-values)nebo SDK. Pokud alias pro vlastnost Správce prostředků neexistuje, vytvořte lístek podpory.

### <a name="scenario-evaluation-details-not-up-to-date"></a>Scénář: podrobnosti vyhodnocení nejsou aktuální.

#### <a name="issue"></a>Problém

Prostředek je ve stavu "nespuštěno" nebo podrobnosti o dodržování předpisů nejsou aktuální.

#### <a name="cause"></a>Příčina

Použití nového přiřazení zásady nebo iniciativy trvá přibližně 30 minut. Nové nebo aktualizované prostředky v rozsahu stávajícího přiřazení budou k dispozici přibližně 15 minut později. Standardní kontrola dodržování předpisů proběhne každých 24 hodin. Další informace najdete v tématu [aktivační události vyhodnocení](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Řešení

Nejdřív počkejte odpovídající dobu, než se vyhodnocení dokončí, a výsledky dodržování předpisů budou k dispozici v Azure Portal nebo SDK. Chcete-li zahájit novou zkušební kontrolu pomocí Azure PowerShell nebo REST API, přečtěte si téma [Kontrola vyhodnocení na vyžádání](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-evaluation-not-as-expected"></a>Scénář: vyhodnocení není podle očekávání

#### <a name="issue"></a>Problém

Prostředek není ve stavu vyhodnocení, buď _kompatibilní_ , nebo _nekompatibilní_, který je pro tento prostředek očekávaný.

#### <a name="cause"></a>Příčina

Prostředek není ve správném oboru pro přiřazení zásady nebo definice zásady nefunguje tak, jak má.

#### <a name="resolution"></a>Řešení

- U nekompatibilního prostředku, který se očekával jako kompatibilní, začněte tím, že [určíte důvody nedodržení předpisů](../how-to/determine-non-compliance.md). Porovnání definice s hodnotou vyhodnocené vlastnosti indikuje, proč prostředek nebyl kompatibilní.
- U kompatibilního prostředku, který očekával, že nedodržují předpisy, přečtěte si podmínku definice zásad podle podmínky a vyhodnoťte proti vlastnostem prostředků. Ověřte, zda logické operátory seskupují správné podmínky spolu s tím, že vaše podmínky nejsou obráceny.

Pokud dodržování předpisů pro přiřazení zásady zobrazuje `0/0` prostředky, neurčily se v rámci oboru přiřazení žádné prostředky, které by bylo možné použít. Ověřte definici zásad i obor přiřazení.

### <a name="scenario-enforcement-not-as-expected"></a>Scénář: vynucování není podle očekávání

#### <a name="issue"></a>Problém

Prostředek, na kterém se očekává, Azure Policy není a v [protokolu aktivit Azure](../../../azure-monitor/platform/platform-logs-overview.md)neexistuje žádný záznam.

#### <a name="cause"></a>Příčina

Přiřazení zásad bylo nakonfigurováno pro [EnforcementMode](../concepts/assignment-structure.md#enforcement-mode) _zakázané_. I když je režim vynucení zakázaný, není účinek zásad vynucený a v protokolu aktivit není žádný záznam.

#### <a name="resolution"></a>Řešení

Aktualizujte **enforcementMode** na _povoleno_. Tato změna umožňuje Azure Policy působit na prostředky v přiřazení zásad a odesílat položky do protokolu aktivit. Pokud je už **enforcementMode** povolený, přečtěte si téma věnované akci [vyhodnocení neočekávaných](#scenario-evaluation-not-as-expected) kurzů.

### <a name="scenario-denied-by-azure-policy"></a>Scénář: zamítnutý Azure Policy

#### <a name="issue"></a>Problém

Vytvoření nebo aktualizace prostředku se zamítly.

#### <a name="cause"></a>Příčina

Přiřazení zásady do oboru, ve kterém je nový nebo aktualizovaný prostředek, splňuje kritéria definice zásady s efektem [odepření](../concepts/effects.md#deny) . Zdroje informací o těchto definicích je znemožněno vytvářet ani aktualizovat.

#### <a name="resolution"></a>Řešení

Chybová zpráva z přiřazení zásady odepření zahrnuje ID přiřazení definice zásad a zásad. Pokud informace o chybě ve zprávě chybí, je také k dispozici v [protokolu aktivit](../../../azure-monitor/platform/activity-log-view.md). Tyto informace slouží k získání dalších informací, které vám pomohou pochopit omezení prostředků a upravit vlastnosti prostředku v žádosti tak, aby odpovídaly povoleným hodnotám.

## <a name="template-errors"></a>Chyby šablon

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scénář: funkce podporované zásadou zpracovávané šablonou

#### <a name="issue"></a>Problém

Azure Policy podporuje řadu funkcí a funkcí šablony Azure Resource Manager (šablony ARM), které jsou k dispozici pouze v definici zásady. Správce prostředků tyto funkce zpracovává jako součást nasazení, ne jako součást definice zásady.

#### <a name="cause"></a>Příčina

Použití podporovaných funkcí, jako je například `parameter()` nebo `resourceGroup()` , vede ke zpracování výsledku funkce v době nasazení místo ukončení funkce pro účely definice zásad a Azure Policyho stroje.

#### <a name="resolution"></a>Řešení

Chcete-li předat funkci až po součást definice zásady, vydejte celý řetězec `[` tak, aby vlastnost vypadala jako `[[resourceGroup().tags.myTag]` . Řídicí znak způsobí, že Správce prostředků při zpracování šablony zacházet s hodnotou jako s řetězcem. Azure Policy pak funkci umístí do definice zásady, což umožní její dynamické fungování podle očekávání. Další informace najdete v tématu [syntaxe a výrazy v šablonách Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-installation-errors"></a>Chyby instalace doplňku

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>Scénář: instalace pomocí grafu Helm v hesle se nezdařila

#### <a name="issue"></a>Problém

`helm install azure-policy-addon`Příkaz se nezdařil s jednou z následujících zpráv:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Příčina

Vygenerované heslo obsahuje čárku ( `,` ), na které se Helm graf.

#### <a name="resolution"></a>Řešení

`,`Při spuštění `helm install azure-policy-addon` s zpětným lomítkem () zařídí čárku () v hodnotě hesla `\` .

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>Scénář: instalace pomocí grafu Helm se nezdařila, protože název už existuje.

#### <a name="issue"></a>Problém

`helm install azure-policy-addon`Příkaz se nezdařil s následující zprávou:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Příčina

Graf Helm s názvem `azure-policy-addon` již byl nainstalován nebo částečně nainstalován.

#### <a name="resolution"></a>Řešení

Postupujte podle pokynů k [odebrání Azure Policy pro doplněk Kubernetes](../concepts/policy-for-kubernetes.md#remove-the-add-on)a pak znovu spusťte `helm install azure-policy-addon` příkaz.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků prostřednictvím [Microsoft Q&A](/answers/topics/azure-policy.html).
- Spojte se s nástrojem [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure s správnými zdroji: odpověďmi, podporou a odborníky.
- Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
