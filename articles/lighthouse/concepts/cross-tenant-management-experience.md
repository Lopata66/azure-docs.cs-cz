---
title: Prostředí pro správu napříč tenanty
description: Správa delegovaných prostředků v Azure umožňuje prostředí pro správu mezi klienty.
ms.date: 08/07/2020
ms.topic: conceptual
ms.openlocfilehash: 9ec3896b85f825b22dc9b57d4220e1cdcdf3e390
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003612"
---
# <a name="cross-tenant-management-experiences"></a>Prostředí pro správu napříč tenanty

Jako poskytovatel služeb můžete pomocí [Azure Lighthouse](../overview.md) spravovat prostředky pro více zákazníků v rámci vašeho vlastního tenanta v [Azure Portal](https://portal.azure.com). V rámci spravovaných klientů se dá provádět mnoho úloh a služeb pomocí [delegované správy prostředků Azure](../concepts/azure-delegated-resource-management.md).

> [!NOTE]
> Správu delegovaných prostředků Azure je možné použít i [v rámci podniku, který má více tenantů Azure AD vlastní](enterprise.md) pro zjednodušení správy mezi klienty.

## <a name="understanding-customer-tenants"></a>Porozumění klientům zákazníka

Tenant Azure Active Directory (Azure AD) je zastoupení organizace. Jedná se o vyhrazenou instanci služby Azure AD, kterou organizace obdrží, když vytvoří relaci se společností Microsoft tím, že se přihlásí k Azure, Microsoft 365 nebo jiným službám. Každý tenant služby Azure AD je jedinečný a oddělený od ostatních tenantů Azure AD a má své vlastní ID tenanta (identifikátor GUID). Další informace najdete v tématu [co je Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

Aby bylo možné spravovat prostředky Azure pro zákazníka, poskytovatelé služeb by se museli přihlašovat k Azure Portal pomocí účtu přidruženého k tenantovi daného zákazníka a vyžadovat od správce v tenantovi zákazníka vytváření a správu uživatelských účtů pro poskytovatele služeb.

Pomocí Azure Lighthouse proces připojování Určuje uživatele v rámci tenanta poskytovatele služeb, kteří budou moci pracovat na delegovaných předplatných a skupinách prostředků v tenantovi zákazníka. Tito uživatelé se pak mohou přihlašovat k Azure Portal pomocí vlastních přihlašovacích údajů. V rámci Azure Portal mohou spravovat prostředky patřící všem zákazníkům, ke kterým mají přístup. To se dá udělat tak, že navštívíte stránku [moji Customers](../how-to/view-manage-customers.md) v Azure Portal, nebo přímo v rámci svého předplatného daného zákazníka, a to buď v Azure Portal nebo prostřednictvím rozhraní API.

Azure Lighthouse umožňuje větší flexibilitu při správě prostředků pro různé zákazníky bez nutnosti přihlašovat se k různým účtům v různých klientech. Poskytovatel služeb může mít například dva zákazníky s různými zodpovědnostmi a úrovněmi přístupu. Pomocí Azure Lighthouse se autorizovaní uživatelé můžou přihlásit k tenantovi poskytovatele služeb, aby měli přístup k těmto prostředkům.

![Prostředky zákazníka spravované prostřednictvím jednoho tenanta poskytovatele služeb](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Podpora rozhraní API a nástrojů pro správu

Úlohy správy můžete u delegovaných prostředků provádět buď přímo na portálu, nebo pomocí rozhraní API a nástrojů pro správu (například Azure CLI a Azure PowerShell). Všechna existující rozhraní API se dají použít při práci s delegovanými prostředky, pokud je tato funkce podporovaná pro správu mezi klienty a uživatel má příslušná oprávnění.

[Rutina Azure PowerShell Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) zobrazuje `HomeTenantId` atributy a `ManagedByTenantIds` pro každé předplatné, což vám umožní určit, jestli vracený odběr patří do spravovaného tenanta zákazníka nebo do vašeho tenanta pro správu.

Podobně příkazy rozhraní příkazového řádku Azure, jako je například [AZ Account list](/cli/azure/account?view=azure-cli-latest#az-account-list) , zobrazí `homeTenantId` `managedByTenants` atributy a.

> [!TIP]
> Pokud nevidíte tyto hodnoty při použití rozhraní příkazového řádku Azure, zkuste vyprázdnit mezipaměť spuštěním příkazu `az account clear` a `az login --identity` .

Poskytujeme také rozhraní API, která jsou specifická pro provádění úloh Azure Lighthouse. Další informace najdete v části **reference** .

## <a name="enhanced-services-and-scenarios"></a>Rozšířené služby a scénáře

Většinu úkolů a služeb je možné provádět na delegovaných prostředcích ve spravovaných klientech. Níže jsou uvedeny některé z klíčových scénářů, ve kterých může být Správa mezi klienty obzvláště efektivní.

[ARC Azure](../../azure-arc/index.yml):

- Správa hybridních serverů ve velkém měřítku – [Azure ARC pro servery (Preview)](../../azure-arc/servers/overview.md):
  - [Připojení počítačů se systémem Windows Server nebo Linux mimo Azure](../../azure-arc/servers/onboard-portal.md) k delegovaným předplatným nebo skupinám prostředků v Azure
  - Spravujte připojené počítače pomocí konstrukcí Azure, například Azure Policy a označování.
  - Zajistěte, aby se stejná sada zásad používala v hybridních prostředích zákazníků.
  - Pomocí Azure Security Center můžete monitorovat dodržování předpisů v hybridních prostředích zákazníků.
- Správa hybridních clusterů Kubernetes ve velkém měřítku – [Kubernetes s podporou ARC Azure (Preview)](../../azure-arc/kubernetes/overview.md):
  - [Připojení clusteru Kubernetes ke službě Azure ARC](../../azure-arc/kubernetes/connect-cluster.md) k delegovaným předplatným nebo skupinám prostředků v Azure
  - [Použití GitOps](../../azure-arc/kubernetes/use-gitops-connected-cluster.md) pro připojené clustery
  - Vynutilit zásady mezi připojenými clustery

[Azure Automation](../../automation/index.yml):

- Použití účtů Automation pro přístup k prostředkům delegovaného zákazníka a práce s nimi

[Azure Backup](../../backup/index.yml):

- Zálohování a obnovení zákaznických dat v klientech zákazníků
- Pomocí [Průzkumníka zálohování](../../backup/monitor-azure-backup-with-backup-explorer.md) si můžete zobrazit provozní informace o zálohovaných položkách (včetně prostředků Azure, které ještě nejsou nakonfigurované pro zálohování), a informace o monitorování (úlohy a výstrahy) pro delegované předplatné. Průzkumník zálohování je momentálně dostupný jenom pro data virtuálních počítačů Azure.
- [Sestavy zálohování](../../backup/configure-reports.md) v rámci delegovaných předplatných vám umožní sledovat historické trendy, analyzovat spotřebu úložiště záloh a auditovat a obnovovat zálohy.

[Azure cost management + fakturace](../../cost-management-billing/index.yml):

- Od tenanta pro správu můžou partneři CSP zobrazovat, spravovat a analyzovat náklady na předběžnou spotřebu (bez nákupů) pro zákazníky, kteří jsou v rámci plánu Azure. Náklady budou založené na prodejních tarifech a na řízení přístupu na základě role Azure (Azure RBAC), které má partner pro předplatné zákazníka.

[Služba Azure Kubernetes (AKS)](../../aks/index.yml):

- Správa hostovaných prostředí Kubernetes a nasazení a Správa kontejnerových aplikací v rámci zákaznických tenantů

[Azure monitor](../../azure-monitor/index.yml):

- Zobrazit výstrahy pro delegovaná předplatná s možností zobrazovat výstrahy napříč všemi předplatnými
- Zobrazit podrobnosti protokolu aktivit pro delegované odběry
- Log Analytics: dotazování dat ze vzdálených pracovních prostorů zákazníka ve více klientech
- Vytváření výstrah v klientech zákazníka, kteří spouštějí automatizaci, například Azure Automation Runbooky nebo Azure Functions v tenantovi poskytovatele služeb prostřednictvím webhooků
- Pro úlohy SAP [Sledujte metriky řešení SAP s agregovaným zobrazením napříč klienty zákazníka](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/using-azure-lighthouse-and-azure-monitor-for-sap-solutions-to/ba-p/1537293) .

[Sítě Azure](../../networking/networking-overview.md):

- Nasazení a Správa [Azure Virtual Network](../../virtual-network/index.yml) a karet virtuální síťové adaptéry (Virtual Network Interface) v rámci zákaznických tenantů
- Nasazení a konfigurace [Azure firewall](../../firewall/overview.md) pro ochranu prostředků Virtual Network zákazníků
- Správa služeb připojení, jako je [Azure Virtual WAN](../../virtual-wan/virtual-wan-about.md), [ExpressRoute](../../expressroute/expressroute-introduction.md)a [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) pro zákazníky
- Použití Azure Lighthouse k podpoře klíčových scénářů pro [program MSP sítě Azure](../../networking/networking-partners-msp.md)


[Azure Policy](../../governance/policy/index.yml):

- Snímky dodržování předpisů zobrazují podrobnosti o přiřazených zásadách v rámci delegovaných předplatných.
- Vytvoření a úprava definic zásad v rámci delegovaného předplatného
- Přiřazení definic zásad definovaných zákazníkem v rámci delegovaného předplatného
- Zákazníci uvidí zásady vytvořené poskytovatelem služeb společně se všemi zásadami, které sami vytvořily.
- Může [napravit deployIfNotExists nebo upravit přiřazení v rámci tenanta zákazníka](../how-to/deploy-policy-remediation.md) .

[Graf prostředků Azure](../../governance/resource-graph/index.yml):

- Teď obsahuje ID tenanta v vrácených výsledcích dotazu, které vám umožní určit, jestli předplatné patří klientovi zákazníka nebo poskytovateli služeb.

[Azure Security Center](../../security-center/index.yml):

- Viditelnost mezi klienty
  - Monitorování dodržování zásad zabezpečení a zajištění pokrytí zabezpečení napříč prostředky všech tenantů
  - Nepřetržité monitorování dodržování předpisů v rámci více zákazníků v jednom zobrazení
  - Monitorování, třídění a stanovení priorit s napadnutelnými doporučeními zabezpečení pomocí výpočtu zabezpečeného skóre
- Správa stav zabezpečení mezi klienty
  - Správa zásad zabezpečení
  - Provést akci s prostředky, které nedodržují předpisy s užitečnými doporučeními zabezpečení
  - Shromažďování a ukládání dat týkajících se zabezpečení
- Detekce a ochrana hrozeb mezi klienty
  - Zjišťování hrozeb napříč prostředky tenantů
  - Použití pokročilých ovládacích prvků ochrany před hrozbami, jako je například přístup k virtuálnímu počítači za běhu (JIT)
  - Posílení konfigurace skupiny zabezpečení sítě pomocí adaptivního posílení sítě
  - Zajistěte, aby na serverech běžely jenom aplikace a procesy, které by měly být s adaptivními řízeními aplikací.
  - Sledování změn důležitých souborů a položek registru pomocí monitorování integrity souborů (FIM)

[Sentinel Azure](../../sentinel/multiple-tenants-service-providers.md):

- Správa prostředků Azure Sentinel [v klientech zákazníků](../../sentinel/multiple-tenants-service-providers.md)
- [Sledování útoků a zobrazování výstrah zabezpečení napříč klienty s více zákazníky](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- [Zobrazení incidentů](../../sentinel/multiple-workspace-view.md) v několika pracovních prostorech Sentinel rozmístěných mezi klienty zákazníka

[Azure Service Health](../../service-health/index.yml):

- Monitorujte stav zákaznických prostředků pomocí Azure Resource Health
- Sledujte stav služeb Azure používaných vašimi zákazníky.

[Azure Site Recovery](../../site-recovery/index.yml):

- Spravujte možnosti zotavení po havárii pro virtuální počítače Azure v klientech zákazníků (Všimněte si, že účty nemůžete použít `RunAs` ke kopírování rozšíření virtuálních počítačů).

[Virtual Machines Azure](../../virtual-machines/index.yml):

- Použití rozšíření virtuálních počítačů k zajištění konfigurace po nasazení a úloh automatizace na virtuálních počítačích Azure v klientech zákazníků
- Řešení potíží s virtuálními počítači Azure v klientech zákazníků pomocí diagnostiky spouštění
- Přístup k virtuálním počítačům pomocí sériové konzoly v klientech zákazníků
- Integrujte virtuální počítače s Azure Key Vault pro hesla, tajné klíče nebo kryptografické klíče pro šifrování disků pomocí [spravované identity prostřednictvím zásad](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret)a ujistěte se, že tajné klíče jsou uložené v Key Vault v klientech zákazníka.
- Všimněte si, že nemůžete použít Azure Active Directory pro vzdálené přihlášení k virtuálním počítačům v klientech zákazníků.

Žádosti o podporu:

- [Otevřete žádosti o podporu z **nápovědu a podpory** ](../../azure-portal/supportability/how-to-create-azure-support-request.md#getting-started) v Azure Portal delegovaných prostředků (výběr plánu podpory dostupného pro delegovaný obor).

## <a name="current-limitations"></a>Aktuální omezení
U všech scénářů Prosím mějte na paměti následující stávající omezení:

- Požadavky, které jsou zpracovávány Azure Resource Manager, lze provádět pomocí delegované správy prostředků Azure. Identifikátory URI operací pro tyto požadavky začínají na `https://management.azure.com` . Nicméně požadavky, které jsou zpracovávány instancí typu prostředku (například přístup k přístupu k datovým Key Vault nebo přístup k datům úložiště), se nepodporují se správou delegovaných prostředků Azure. Identifikátory URI operací pro tyto požadavky obvykle začínají adresou, která je pro vaši instanci jedinečná, například `https://myaccount.blob.core.windows.net` nebo `https://mykeyvault.vault.azure.net/` . Druhá z nich také obvykle slouží k operacím s daty a nikoli k operacím správy.
- Přiřazení rolí musí používat [předdefinované role](../../role-based-access-control/built-in-roles.md)řízení přístupu na základě role (RBAC). Všechny předdefinované role se v současné době podporují se správou delegovaných prostředků Azure s výjimkou vlastníka nebo jakýchkoli integrovaných rolí s [`DataActions`](../../role-based-access-control/role-definitions.md#dataactions) oprávněním. Role správce přístupu uživatelů je podporovaná jenom pro omezené použití při [přiřazování rolí ke spravovaným identitám](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  Vlastní role a [role správců pro klasický odběr](../../role-based-access-control/classic-administrators.md) nejsou podporovány.
- I když můžete připojit odběry, které používají Azure Databricks, uživatelé v tenantovi pro správu nemůžou v současné době spouštět Azure Databricks pracovní prostory na delegovaném předplatném.
- I když můžete připojit odběry a skupiny prostředků, které mají zámky prostředků, tyto zámky nebudou bránit v provádění akcí uživatelům v tenantovi. [Zakažte přiřazení](../../role-based-access-control/deny-assignments.md) , která chrání systémem spravované prostředky, jako jsou ty, které vytvořily spravované aplikace Azure nebo plány Azure (přiřazení zamítnutí přiřazení systémem), zabrání uživatelům ve správě tenanta na těchto prostředcích. Nicméně v tomto okamžiku nemohou uživatelé v tenantovi zákazníka vytvořit vlastní přiřazení odepřít (přiřazení odepřít uživateli).

## <a name="next-steps"></a>Další kroky

- Připojte vaše zákazníky do správy delegovaných prostředků Azure, a to buď [pomocí šablon Azure Resource Manager](../how-to/onboard-customer.md) , nebo [publikováním nabídky privátních nebo veřejných spravovaných služeb, které Azure Marketplace](../how-to/publish-managed-services-offers.md).
- V **Azure Portal můžete** [Zobrazit a spravovat zákazníky](../how-to/view-manage-customers.md) .
