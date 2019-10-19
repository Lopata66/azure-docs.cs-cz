---
title: Zakázat monitorování v Azure Monitor pro virtuální počítače (Preview) | Microsoft Docs
description: Tento článek popisuje, jak zastavit monitorování virtuálních počítačů v Azure Monitor pro virtuální počítače.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/05/2018
ms.openlocfilehash: 70e71688fde5aff4002c7d49b1408bcefeab1eed
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555161"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>Zakázání monitorování virtuálních počítačů v Azure Monitor pro virtuální počítače (Preview)

Jakmile povolíte monitorování virtuálních počítačů, můžete se později rozhodnout zakázat monitorování v Azure Monitor pro virtuální počítače. Tento článek popisuje, jak zakázat monitorování pro jeden nebo více virtuálních počítačů.  

V současné době Azure Monitor pro virtuální počítače nepodporuje selektivní zakázání monitorování virtuálních počítačů. Pracovní prostor Log Analytics může podporovat Azure Monitor pro virtuální počítače a další řešení. Může také shromažďovat další data monitorování. Pokud vaše Log Analytics pracovní prostor poskytuje tyto služby, je potřeba pochopit účinek a metody zakázání monitorování před tím, než začnete.

Azure Monitor pro virtuální počítače spoléhá na následující komponenty, které doručí své zkušenosti:

* Log Analytics pracovní prostor, který ukládá data monitorování z virtuálních počítačů a jiných zdrojů.
* Kolekce čítačů výkonu nakonfigurovaných v pracovním prostoru. Kolekce aktualizuje konfiguraci monitorování na všech virtuálních počítačích připojených k pracovnímu prostoru.
* `InfrastructureInsights` a `ServiceMap`, která jsou monitorováním řešení nakonfigurovaných v pracovním prostoru. Tato řešení aktualizují konfiguraci monitorování na všech virtuálních počítačích připojených k pracovnímu prostoru.
* `MicrosoftMonitoringAgent` a `DependencyAgent`, což jsou rozšíření virtuálních počítačů Azure. Tato rozšíření shromažďují a odesílají data do pracovního prostoru.

Při přípravě na zakázání monitorování virtuálních počítačů mějte na paměti tyto skutečnosti:

* Pokud jste vyhodnotili s jedním virtuálním počítačem a použili jste přednastavené výchozí Log Analytics pracovní prostor, můžete monitorování zakázat odinstalováním agenta závislostí z virtuálního počítače a odpojením agenta Log Analytics z tohoto pracovního prostoru. Tento přístup je vhodný, pokud máte v úmyslu použít virtuální počítač pro jiné účely a později se rozhodnout znovu připojit k jinému pracovnímu prostoru.
* Pokud jste vybrali stávající pracovní prostor Log Analytics, který podporuje jiná řešení monitorování a shromažďování dat z jiných zdrojů, můžete součásti řešení odebrat z pracovního prostoru bez přerušení nebo ovlivnění pracovního prostoru.  

>[!NOTE]
> Po odebrání součástí řešení z pracovního prostoru můžete dál zobrazovat stav z vašich virtuálních počítačů Azure. Konkrétně se zobrazí data o výkonu a mapování, když na portálu přejdete na některý pohled. Data se nakonec přestanou zobrazovat v zobrazeních **výkon** a **Mapa** . Ale zobrazení **stavu** bude i nadále zobrazovat stav virtuálních počítačů. Možnost **vyzkoušet nyní** bude k dispozici z vybraného virtuálního počítače Azure, takže v budoucnu můžete monitorování znovu povolit.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Zcela odebrat Azure Monitor pro virtuální počítače

Pokud stále potřebujete Log Analytics pracovní prostor, postupujte podle těchto kroků a úplně odeberte Azure Monitor pro virtuální počítače. Z pracovního prostoru odeberete `InfrastructureInsights` a `ServiceMap` řešení.  

>[!NOTE]
>Pokud jste řešení Service Map monitoring používali před tím, než povolíte Azure Monitor pro virtuální počítače a pořád na něj budete spoléhat, toto řešení neodstraňujte, jak je popsáno v posledním kroku tohoto postupu.  
>

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. Na webu Azure Portal vyberte **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Po zahájení psaní seznam vyfiltruje návrhy na základě vašeho vstupu. Vyberte **Log Analytics**.
3. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor, který jste zvolili, když jste povolili Azure Monitor pro virtuální počítače.
4. Na levé straně vyberte **řešení**.  
5. V seznamu řešení vyberte **InfrastructureInsights (název pracovního prostoru)** . Na stránce **Přehled** pro řešení vyberte **Odstranit**. Po zobrazení výzvy k potvrzení vyberte **Ano**.  
6. V seznamu řešení vyberte **ServiceMap (název pracovního prostoru)** . Na stránce **Přehled** pro řešení vyberte **Odstranit**. Po zobrazení výzvy k potvrzení vyberte **Ano**.  

Pokud jste v pracovním prostoru [neshromáždili čítače výkonu](vminsights-enable-overview.md#performance-counters-enabled) pro virtuální počítače se systémem Windows nebo Linux, [zakažte tato pravidla](../platform/data-sources-performance-counters.md#configuring-performance-counters) pro systémy Windows a Linux v případě, že jste povolili Azure monitor pro virtuální počítače.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Zakázat monitorování a zachovat pracovní prostor  

Pokud má váš Log Analytics pracovní prostor stále podporovat monitorování z jiných zdrojů, pomocí následujících kroků zakažte monitorování na virtuálním počítači, který jste použili k vyhodnocení Azure Monitor pro virtuální počítače. Pro virtuální počítače Azure odeberete rozšíření virtuálního počítače agenta závislosti a rozšíření virtuálního počítače Log Analytics agenta pro Windows nebo Linux přímo z virtuálního počítače. 

>[!NOTE]
>Agenta Log Analytics neodstraňujte, pokud: 
>
> * Azure Automation spravuje virtuální počítač pro orchestraci procesů nebo ke správě konfigurace nebo aktualizací. 
> * Azure Security Center spravuje virtuální počítač z důvodu zabezpečení a detekce hrozeb. 
>
> Pokud odeberete agenta Log Analytics, zabráníte tím, aby tyto služby a řešení aktivně spravovaly váš virtuální počítač. 

1. Přihlaste se na web [Azure Portal](https://portal.azure.com). 
2. V Azure Portal vyberte **Virtual Machines**. 
3. V seznamu vyberte virtuální počítač. 
4. Na levé straně vyberte **rozšíření**. Na stránce **rozšíření** vyberte **DependencyAgent**.
5. Na stránce vlastnosti rozšíření vyberte **odinstalovat**.
6. Na stránce **rozšíření** vyberte **MicrosoftMonitoringAgent**. Na stránce vlastnosti rozšíření vyberte **odinstalovat**.  
