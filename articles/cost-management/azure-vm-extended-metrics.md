---
title: Přidání rozšířené metriky pro virtuální počítače Azure | Dokumentace Microsoftu
description: Tento článek vám pomůže povolit a nakonfigurovat rozšířené diagnostických metrik pro virtuální počítače Azure.
services: cost-management
keywords: ''
author: bandersmsft
manager: vitavor
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
ms.custom: seodec18
ms.openlocfilehash: 6a4f7f5671562679a245d97ad8491764657cbb34
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242452"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Přidání rozšířené metriky pro virtuální počítače Azure

Cloudyn používá Azure metriky data z vašich virtuálních počítačů Azure zobrazíte podrobné informace o jejich prostředků. Data metriky, čítače výkonu, nazývané také používá ke generování sestav Cloudyn. Nicméně Cloudyn není shromažďovat automaticky všech dat metriky Azure z virtuálních počítačů hosta, je nutné povolit shromažďování metrik. Tento článek vám pomůže povolit a nakonfigurovat dalších diagnostických metrik pro virtuální počítače Azure.

Po povolení shromažďování metrik můžete:

- Vědět, když vaše virtuální počítače jsou tam dostupné pro jejich omezení procesoru, disku a paměti.
- Detekujte trendy využití a anomálie.
- Řídit své náklady podle velikosti podle využití.
- Získání nákladů efektivní velikosti doporučení optimalizace Cloudyn.

Například můžete chtít sledovat % procesoru a paměti % virtuální počítače Azure. Metriky virtuálního počítače Azure odpovídají _využití CPU_ a _\Memory\% využití svěřených bajtů_.

> [!NOTE]
> Kolekce rozšířených dat metriky se podporuje jenom s využitím Azure monitoru úrovni hosta. Cloudyn není kompatibilní s rozšířením virtuálního počítače protokoly Azure monitoru.

## <a name="determine-whether-extended-metrics-are-enabled"></a>Určení, zda je povoleno rozšířené metriky

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
2. V části **virtuálních počítačů**, vyberte virtuální počítač a potom v části **monitorování**vyberte **metriky**. Se zobrazí seznam dostupných metrik.
3. Vyberte několik metrik a graf zobrazí data pro ně.  
    ![Příklad metrika – využití CPU na hostiteli](./media/azure-vm-extended-metrics/metric01.png)

V předchozím příkladu omezenou sadu standardní metriky jsou k dispozici pro hostitele, ale nejsou paměti metriky. Metriky paměti jsou součástí rozšířené metriky. V takovém případě nejsou povolené rozšířené metriky pro virtuální počítač. Musíte provést další kroky k povolení rozšířené metriky. Následující informace vám pomůže povolovat.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Povolit rozšířené metriky na webu Azure Portal

Standardní metriky jsou metriky hostitele počítače. _Využití CPU_ metrika je jedním z příkladů. Existují také základní metriky pro hostované virtuální počítače a také jsou volány rozšířené metriky. Příklady rozšířené metriky _\Memory\% využití svěřených bajtů_ a _\Memory\Available bajtů_.

Povolení rozšířené metriky je jednoduché. Pro každý virtuální počítač povolte monitorování na úrovni hosta. Když povolíte monitorování na úrovni hosta, instalaci agenta Azure diagnostics na virtuálním počítači. Ve výchozím nastavení se přidají základní sadu rozšířené metriky. Následující postup je stejný pro virtuální počítače classic a běžné a stejný pro Windows a virtuální počítače s Linuxem.

Uvědomte si, že Azure a Linuxu monitorování na úrovni hosta se vyžaduje účet úložiště. Když povolíte monitorování na úrovni hosta, pokud se rozhodnete nemáte existující účet úložiště, pak je jedna vytvořená za vás.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Povolit monitorování na úrovni hosta na stávajících virtuálních počítačů

1. V **virtuálních počítačů**, zobrazit seznam vašich virtuálních počítačů a potom vyberte virtuální počítač.
2. V části **monitorování**vyberte **nastavení diagnostiky**.
3. Na stránce nastavení diagnostiky, klikněte na tlačítko **povolit monitorování na úrovni hosta**.  
    ![Povolení úrovně monitorování hostovaného na stránce s přehledem](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. Po několika minutách se nainstaluje agent diagnostiky Azure na virtuálním počítači. Základní nastavení metriky jsou přidány. Aktualizujte stránku. Na kartě Přehled se zobrazí přidané čítače výkonu.
5. V části monitorování zvolte **metriky**.
6. V grafu metrik v části **metrika Namespace**vyberte **hosta (Classic)** .
7. V seznamu metrik můžete zobrazit všechny dostupných čítačů výkonu pro hosta virtuálního počítače.  
    ![seznam příklad rozšířené metriky](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Povolit monitorování na úrovni hosta na nové virtuální počítače

Při vytváření nové virtuální počítače, na kartě Správa, vyberte **na** pro **operačního systému hosta diagnostiky**.

![Nastavte na hodnotu On Diagnostika hostovaného operačního systému](./media/azure-vm-extended-metrics/new-enable-diag.png)

Další informace o povolení rozšířené metriky pro virtuální počítače Azure najdete v tématu [porozumění a pomocí agenta Azure Linux](../virtual-machines/extensions/agent-linux.md) a [agenta virtuálního počítače Azure přehled](../virtual-machines/extensions/agent-windows.md).

## <a name="resource-manager-credentials"></a>Přihlašovací údaje správce prostředků

Po povolení rozšířené metriky, ujistěte se, Cloudyn má přístup k vaší [Resource Manageru přihlašovacími údaji](activate-subs-accounts.md). Vaše přihlašovací údaje jsou požadovány pro Cloudyn ke shromáždění a zobrazení dat výkonu pro virtuální počítače. Také slouží k vytvoření doporučení pro optimalizaci nákladů. Cloudyn vyžaduje alespoň tři dny údaje o výkonu z instance k určení, zda je mezi kandidáty pro doporučení downsizing.

## <a name="enable-vm-metrics-with-a-script"></a>Zapnutí metrik virtuálního počítače pomocí skriptu

Metriky virtuálního počítače pomocí skriptů prostředí Azure PowerShell můžete povolit. Pokud máte mnoho virtuálních počítačů, které chcete k zapnutí metrik na, můžete použít skript k automatizaci procesu. Příklady skriptů najdete na Githubu v [povolení diagnostiky Azure](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Zobrazit metriku výkonu Azure

Pokud chcete zobrazit metriky výkonu na vaše instance Azure na portálu Cloudyn, přejděte na **prostředky** > **Compute** > **instanci Průzkumníka**. V seznamu instancí virtuálních počítačů rozbalte instanci a potom rozbalte prostředků k zobrazení podrobností.

![Příklad informace zobrazené v Průzkumníku Instance](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Další postup

- Pokud jste ještě nepovolili přístup přes rozhraní API Azure Resource Manageru pro vaše účty, pokračujte [účtů a předplatných Azure aktivovat](activate-subs-accounts.md).
