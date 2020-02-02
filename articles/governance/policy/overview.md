---
title: Přehled zásad Azure
description: Azure Policy je služba v Azure, pomocí které vytváříte, přiřazujete a spravujete definice zásad ve svém prostředí Azure.
ms.date: 11/25/2019
ms.topic: overview
ms.custom: fasttrack-edit
ms.openlocfilehash: e886f37a8d7f1395b5c831e81e600ecc6e2dd20f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937820"
---
# <a name="what-is-azure-policy"></a>Co je Azure Policy?

Zásady správného řízení ověří, že vaše organizace může dosáhnout svých cílů, a to Díky efektivnímu a efektivnímu využití IT. Splňuje tyto potřeby vytvořením jasný vztah mezi obchodními cíli a projekty IT.

Potýká se vaše společnost s velkým množstvím problémů v oblasti IT, které se zdánlivě nikdy nevyřeší? Dobré zásady správného řízení v oblasti IT zahrnují plánování iniciativ a nastavení priorit na strategické úrovni, která pomáhají překonávat problémy a předcházet jim. Strategické potřebě se řeší Azure Policy.

Azure Policy je služba v Azure, která slouží k vytváření, přiřazování a správě zásad. Tyto zásady vynucují na vašich prostředcích různá pravidla a účinky, aby tyto prostředky zůstaly kompatibilní s vašimi firemními standardy a smlouvami o úrovni služeb. Služba Azure Policy splňuje tyto potřeby vyhodnocením prostředky nedodržení předpisů se zásadami přiřazený. Všechna data uložená ve Azure Policy jsou šifrovaná v klidovém stavu.

Například můžete mít zásadu, která ve vašem prostředí povoluje jenom určitou skladovou položku virtuálních počítačů. Jakmile je implementována tato zásada, nové a stávající prostředky se vyhodnotí dodržování předpisů. S správný typ zásad může být existující prostředky přenesena do dodržování předpisů. Později v této dokumentaci projdeme další podrobnosti o tom, jak vytvořit a implementovat zásady službou Azure Policy.

> [!IMPORTANT]
> Vyhodnocování dodržování předpisů ve službě Azure Policy je teď dostupné pro všechna přiřazení bez ohledu na cenovou úroveň. Pokud vaše přiřazení nezobrazují data dodržování předpisů, ujistěte se, že je předplatné zaregistrované u poskytovatele prostředků Microsoft.PolicyInsights.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-is-it-different-from-rbac"></a>Čím se to liší od RBAC?

Mezi Azure Policy a řízením přístupu na základě role (RBAC) existuje několik klíčových rozdílů. RBAC se zaměřuje na akce uživatelů v různých oborech. Můžete být přidáni do role přispěvatele pro skupinu prostředků, umožní vám provádět změny do této skupiny prostředků. Azure Policy se zaměřuje na vlastnosti prostředku během nasazování a už existující prostředky. Azure Policy řídí vlastnosti, jako jsou typy nebo umístění prostředků. Na rozdíl od RBAC je Azure Policy výchozím systémem povolení a explicitním odepřením.

### <a name="rbac-permissions-in-azure-policy"></a>Oprávnění RBAC ve službě Azure Policy

Služba Azure Policy má několik oprávnění, která se označují jako operace, ve dvou poskytovatelích prostředků:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Řada předdefinovaných rolí uděluje oprávnění k prostředkům Azure Policy. Role **Přispěvatel zásad prostředků** zahrnuje většinu operací Azure Policy. **Vlastník** má úplná oprávnění. **Přispěvatel** i **Čtenář** můžou použít všechny operace čtení Azure Policy, ale **Přispěvatel** může taky aktivovat nápravu.

Pokud žádná z předdefinovaných rolí nemá požadovaná oprávnění, vytvořte [vlastní roli](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Definice zásady

Postup vytváření a implementace zásady v Azure Policy začíná vytvořením definice zásady. Každá definice zásady obsahuje podmínky, za kterých se vynucuje. A nemá definovanou vliv, které u něho splnění podmínky.

Ve službě Azure Policy nabízíme několik integrovaných zásad, které jsou k dispozici ve výchozím nastavení. Příklad:

- **Povolené skladové položky účtu úložiště**: Určuje, zda je účet úložiště se nasazuje v sadě velikostí skladových položek. Jeho účinkem je zamítnutí všech účtů úložiště, které nedrží k sadě definovaných velikostí skladových položek.
- **Povolený typ prostředku**: Definuje typy prostředků, které můžete nasadit. Jeho účinkem je zamítnutí všech prostředků, které nejsou součástí tomto definovaném seznamu.
- **Povolená umístění**: omezuje dostupná umístění u nových prostředků. Účinkem je vynucení vašich požadavků na geografické dodržování předpisů.
- **Povolené skladové položky virtuálních počítačů**: Určuje sadu skladových položek, které můžete nasadit virtuální počítač.
- **Přidat značku k prostředkům**: použije povinnou značku a její výchozí hodnotu, pokud není zadána v žádosti o nasazení.
- **Vynutit značku a její hodnotu**: vynucuje povinnou značku a její hodnotu na prostředek.
- **Nepovolené typy prostředků**: zabraňuje seznam typů prostředků v nasazení.

Pokud chcete implementovat tyto definice zásad (předdefinované i vlastní definice), budete potřebovat pro jejich přiřazení. Jakékoli z těchto zásad můžete přiřadit prostřednictvím webu Azure Portal, PowerShellu nebo Azure CLI.

Vyhodnocení zásad se stane se několik různých úkonů, jako je například přiřazení zásad nebo aktualizací zásad. Úplný seznam najdete v tématu [aktivačních událostí vyhodnocení zásad](./how-to/get-compliance-data.md#evaluation-triggers).

Další informace o strukturách definic zásad najdete v článku [Struktura definic zásad](./concepts/definition-structure.md).

## <a name="policy-assignment"></a>Přiřazení zásad

Přiřazení zásady je definice zásady, která byla přiřazena, aby proběhla v rámci zadaného oboru. Tento rozsah může být v rozsahu od [skupiny pro správu](../management-groups/overview.md) k individuálnímu prostředku. Pojem *oboru* se vztahuje na všechny prostředky, skupiny prostředků, předplatná nebo skupiny pro správu, ke kterým je definice zásady přiřazená. Všechny podřízené prostředky dědí přiřazení zásad. Tento návrh znamená, že se zásady použijí pro skupinu prostředků platí také pro prostředky v příslušné skupině prostředků. Z přiřazení zásad však můžete vyloučit určitý podobor.

Například v oboru předplatného můžete přiřadit zásadu, která brání vytváření síťových prostředků. Může vyloučit skupiny prostředků v tomto předplatném, která je určená pro síťovou infrastrukturu. Potom můžete udělit přístup k této skupině síťových prostředků uživatelům, kterým důvěřujete s vytvářením síťových prostředků.

V jiném příkladu můžete chtít přiřadit zásady seznamu povolených typů prostředků na úrovni skupiny pro správu. A potom přiřadit mnohem mírnější zásady, které povolují více typů prostředků, u podřízené skupiny správy nebo dokonce přímo u předplatných. To ale nebude fungovat, protože zásady představují systém, kdy se explicitně zamítá. Místo toho musíte podřízenou skupinu správy nebo předplatné vyloučit z přiřazení zásad na úrovni skupiny správy. Potom přiřadíte mnohem mírnější zásady na úrovni podřízené skupiny správy nebo předplatného. Pokud je výsledkem nějaké zásady zamítnutí prostředku, pak jediný způsob, jak prostředek povolit, je změna zásad zamítnutí.

Další informace o nastavování definic a přiřazení zásad prostřednictvím portálu najdete v tématu [Vytvoření přiřazení zásady pro identifikaci neodpovídajících prostředků v prostředí Azure](assign-policy-portal.md). K dispozici jsou také kroky pro [PowerShell](assign-policy-powershell.md) a [Azure CLI](assign-policy-azurecli.md).

## <a name="policy-parameters"></a>Parametry zásad

Parametry zásad pomáhají zjednodušit správu zásad tím, že snižují počet definic zásad, které musíte vytvářet. Parametry můžete definovat při vytváření definice zásady a tím ji více zobecnit. Následně můžete tuto definici zásady použít opakovaně pro různé scénáře. Provedete to předáváním různých hodnot při přiřazování této definice zásady. Například můžete pro každé předplatné zadat jednu sadu umístění.

Parametry jsou určené při vytváření definice zásady. Při definování získá parametr má zadaný název a volitelně i hodnotu. Pro zásadu můžete například definovat parametr s názvem *location* (umístění). Následně mu můžete při přiřazování zásady předávat různé hodnoty, například *EastUS* nebo *WestUS*.

Další informace o parametrech zásad najdete v tématu [struktura definic – parametry](./concepts/definition-structure.md#parameters).

## <a name="initiative-definition"></a>Definice iniciativy

Definice iniciativy je kolekce definic zásad, které jsou přizpůsobené pro dosažení jednoho zastřešujícího cíle. Definice iniciativ zjednodušují správu a přiřazování definic zásad. Toto zjednodušení spočívá v seskupování sad zásad do jedné položky. Mohli byste například vytvořit iniciativu s názvem **Povolení monitorování v Azure Security Center** s cílem monitorovat všechna dostupná doporučení zabezpečení v Azure Security Center.

> [!NOTE]
> Sada SDK, jako je Azure CLI a Azure PowerShell, používá k odkazování na iniciativy vlastnosti a parametry s názvem **PolicySet** .

V rámci této iniciativy byste měli například tyto definice zásad:

- **Monitorování nešifrované služby SQL Database v Security Center** – pro monitorování nešifrovaných databází a serverů SQL.
- **Monitorování ohrožení zabezpečení operačního systému ve službě Security Center** – pro monitorování serverů, které není splňují nakonfigurovaným standardním hodnotám.
- **Monitorování chybějící služby Endpoint Protection v Security Center** – pro monitorování serverů bez nainstalovaného agenta služby Endpoint Protection.

## <a name="initiative-assignment"></a>Přiřazení iniciativy

Podobně jako přiřazení zásady je přiřazení iniciativy definicí iniciativy přiřazenou ke konkrétnímu oboru. Přiřazení iniciativ omezují potřebu vytváření několika definic iniciativ pro každý obor. Tento obor může také být v rozsahu od skupiny pro správu k jednotlivému prostředku.

Každý iniciativy je přiřadit k různým oborům. Je možné přiřadit jednoho initiative obě **subscriptionA** a **subscriptionB**.

## <a name="initiative-parameters"></a>Parametry iniciativ

Podobně jako parametry zásad pomáhají parametry iniciativ zjednodušit správu iniciativ tím, že snižují redundanci. Parametry iniciativ jsou parametrů používaných definicemi zásad v rámci této iniciativy.

Jako příklad může posloužit scénář, ve kterém máte definici iniciativy **initiativeC** s definicemi zásad **policyA** a **policyB**, z nichž každá očekává jiný typ parametru:

| Zásady | Název parametru |Typ parametru  |Poznámka |
|---|---|---|---|
| policyA | allowedLocations | pole  |Tento parametr jako hodnotu očekává seznam řetězců, protože typ parametru byl definovaný jako pole. |
| policyB | allowedSingleLocation |string |Tento parametr jako hodnotu očekává jedno slovo, protože typ parametru byl definovaný jako řetězec. |

V tomto scénáři máte při definování parametrů iniciativy pro **initiativeC** tři možnosti:

- Použít parametry definic zásad v rámci této iniciativy: V tomto příkladu se *allowedLocations* a *allowedSingleLocation* stanou parametry iniciativy pro **initiativeC**.
- Zadat hodnoty do parametrů definic zásad v rámci této definice iniciativy. V tomto příkladu můžete zadat seznam umístění pro **parametr zásad – allowedLocations** a **PolicyB parametr – allowedSingleLocation**. Hodnoty můžete zadat také při přiřazování této iniciativy.
- Zadat seznam možností *hodnot*, které se můžou použít při přiřazování této iniciativy. Když přiřadíte tuto iniciativu, zděděné parametry z definic zásad v rámci této iniciativy můžou mít pouze hodnoty z tohoto zadaného seznamu.

Při vytváření možnosti hodnoty v definici iniciativy, nemůžete zadat jinou hodnotu při přiřazení iniciativy, protože není na seznamu.

## <a name="maximum-count-of-azure-policy-objects"></a>Maximální počet Azure Policy objektů

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Doporučení pro správu zásad

Tady je pár ukazatele a tipy brát v úvahu:

- Začněte s efektem auditu místo efektu zamítnutí sledovat dopad definic zásad na prostředky ve vašem prostředí. Pokud máte skripty už pro automatické vaše aplikace, nastavení efektu zamítnutí může bránit ve snaze tyto úlohy automatizace už zavedené.

- Při vytváření definic a přiřazení vezměte v úvahu organizační hierarchie. Doporučujeme vytvářet definice na vyšší úrovně, jako je například skupiny pro správu nebo na úrovni předplatného. Vytvořte přiřazení na další podřízené úrovni. Pokud vytvoříte definici na skupinu pro správu, můžete být přiřazení v oboru předplatného nebo skupiny prostředků v rámci této skupiny pro správu.

- Doporučujeme vytvoření a přiřazení definice iniciativy i pro definici jedné zásady.
  Například máte definici zásady *policyDefA* a vytvořit v rámci definice iniciativy *initiativeDefC*. Je-li vytvořit další definici zásady později pro *policyDefB* s podobnými cíli *policyDefA*, můžete ho v části přidat *initiativeDefC* a sledovat jejich společně.

- Jakmile vytvoříte přiřazení iniciativy, definice zásad se přidá podnět také stanou součástí tohoto přiřazení iniciativy.

- Při vyhodnocování přiřazení iniciativy se vyhodnocují taky všechny zásady v rámci této iniciativy.
  Pokud je potřeba vyhodnotit zásadu samostatně, je lepší položky nebudou zahrnuty v iniciativy.

## <a name="video-overview"></a>Video – přehled

Následující přehled služby Azure Policy se týká sestavení 2018. Pro snímky nebo video stáhnout, navštivte [řízení prostředí Azure prostřednictvím Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) na webu Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>Další kroky

Získali jste přehled o službě Azure Policy a některých klíčových konceptech. Tady je návrh dalších kroků:

- [Přiřaďte definici zásady pomocí portálu](./assign-policy-portal.md).
- [Přiřaďte definici zásady pomocí Azure CLI](./assign-policy-azurecli.md).
- [Přiřaďte definici zásady pomocí PowerShellu](./assign-policy-powershell.md).
