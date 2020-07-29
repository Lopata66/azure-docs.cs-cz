---
title: Použití Azure Security Center doporučení k vylepšení zabezpečení | Microsoft Docs
description: " Naučte se používat zásady zabezpečení a doporučení v Azure Security Center, abyste mohli zmírnit útok na zabezpečení. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: memildin
ms.openlocfilehash: 8039be7b69444cc32e763e9a1fb074e7dda4a5ba
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84783227"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Použití doporučení služby Azure Security Center k vylepšení zabezpečení
Můžete snížit pravděpodobnost významné události zabezpečení konfigurací zásad zabezpečení a následnou implementací doporučení Azure Security Center. V tomto článku se dozvíte, jak používat zásady zabezpečení a doporučení v Security Center, abyste mohli zmírnit útok na zabezpečení. 

Security Center automaticky spouští nepřetržité prohledávání a analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení, která vás provedou procesem konfigurace potřebných ovládacích prvků zabezpečení. Security Center aktualizuje doporučení během 24 hodin, s následujícími výjimkami:

- Doporučení konfigurace zabezpečení operačního systému jsou aktualizována během 48 hodin.
- Doporučení k problémům se Endpoint Protection jsou aktualizována během 8 hodin.

## <a name="scenario"></a>Scénář
V tomto scénáři se dozvíte, jak použít Security Center ke snížení pravděpodobnosti incidentu zabezpečení monitorováním Security Center doporučení a provedením akce. Scénář používá fiktivní společnost, contoso a role prezentované v [příručce Security Center plánování a provoz](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). V tomto scénáři se zaměřujeme na role následujících osoby:

![Role scénáře](./media/security-center-using-recommendations/scenario-roles.png)

Společnost Contoso nedávno provedla migraci některých místních prostředků do Azure. Společnost Contoso chce chránit své prostředky a snížit zranitelnost svých prostředků v cloudu.

## <a name="use-azure-security-center"></a>Používejte Azure Security Center.
David, od zabezpečení IT společnosti Contoso, se už rozhodla připojit Security Center k předplatným společnosti Contoso Azure Security Center, aby nedocházelo k chybám zabezpečení a detekci jejich zabezpečení. 

Security Center automaticky analyzuje stav zabezpečení prostředků Azure společnosti Contoso a použije výchozí zásady zabezpečení. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří **doporučení** na základě ovládacích prvků nastavených v zásadách zabezpečení. 

David spustí standardní úroveň zabezpečení Azure ve všech svých předplatných a získá kompletní sadu doporučení a funkcí zabezpečení, které jsou k dispozici. Jan také zaregistruje všechny existující místní servery, které ještě nebyly migrovány do cloudu, aby mohli využívat hybridní podporu Security Center napříč svými servery s [Windows](quick-onboard-windows-computer.md) a [Linux](quick-onboard-linux-computer.md) .

Jan je vlastníkem úlohy v cloudu. Jan zodpovídá za použití bezpečnostních ovládacích prvků v souladu se zásadami zabezpečení společnosti Contoso. 

Jan provede následující úlohy:

- Monitorování doporučení zabezpečení, která poskytuje Security Center
- Vyhodnoťte doporučení zabezpečení a rozhodněte se, jestli by tato doporučení měla použít nebo zrušit.
- Použít doporučení zabezpečení

### <a name="remediate-threats-using-recommendations"></a>Náprava hrozeb pomocí doporučení
V rámci každodenních aktivit monitorování se Jan přihlásí k Azure a otevře Security Center. 

1. Jan vybere předplatné úlohy.

2. Jan zkontroluje **zabezpečené skóre** , aby získal celkový přehled o tom, jak se přizpůsobuje zabezpečení předplatných a zda je skóre 548.

3. Jan se musí rozhodnout, která doporučení se mají zpracovat jako první. Takže Jan klikne na zabezpečené skóre a začne zpracovávat doporučení na základě toho, kolik IT vylepšuje své [zabezpečené skóre](secure-score-security-controls.md).

4. Vzhledem k tomu, že Jan má spoustu připojených virtuálních počítačů a serverů, se Jan rozhodne soustředit se na **výpočetní výkon a aplikace**.

5. Když Jan klikne na **výpočty a aplikace**, uvidí seznam doporučení a zpracuje je podle dopadu na zabezpečený výsledek.

6. Má Jan spousta internetových virtuálních počítačů a protože jejich porty jsou vystavené, jsou obavy, že by útočník mohl získat kontrolu nad servery. Takže Jan rozhodne použít [**přístup k virtuálnímu počítači za běhu**](security-center-just-in-time.md).

Jan nadále přechází přes doporučení s vysokou prioritou a střední prioritou a provede rozhodnutí o implementaci. U každého doporučení si Jan podíváme na podrobné informace, které Security Center, a dozvíte se, které prostředky jsou ovlivněny, co je to vliv na zabezpečení skóre, co jednotlivé doporučení znamená a nápravné kroky, jak tento problém zmírnit.

## <a name="conclusion"></a>Závěr
Doporučení pro monitorování v Security Center pomáhají eliminovat ohrožení zabezpečení před tím, než dojde k útoku. Když opravíte doporučení, vaše zabezpečené skóre a vaše zatížení stav vylepšit. Security Center automaticky zjistí nové prostředky, které nasadíte, posuzuje je na základě zásad zabezpečení a poskytuje nová doporučení pro jejich zabezpečení.


## <a name="next-steps"></a>Další kroky
Ujistěte se, že máte proces monitorování, ve kterém pravidelně kontrolujete doporučení v Security Center, abyste mohli zajistit, aby se prostředky v průběhu času udržovaly v bezpečí.

Tento scénář ukazuje, jak používat zásady zabezpečení a doporučení v Security Center ke zmírnění útoku zabezpečení.

Naučte se reagovat na hrozby se [správou a reakcí na výstrahy zabezpečení](security-center-managing-and-responding-alerts.md).
