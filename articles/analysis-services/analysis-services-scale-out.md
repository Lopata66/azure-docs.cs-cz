---
title: Horizontální navýšení kapacity Azure Analysis Services | Dokumentace Microsoftu
description: Replikace služby Azure Analysis Services serverů se Škálováním
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8f253d150a5073d2d19daf51c12180c9f7b3660b
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734519"
---
# <a name="azure-analysis-services-scale-out"></a>Horizontální navýšení kapacity Azure Analysis Services

S horizontálním navýšením dotazy klientů můžou distribuovat mezi více *replikami dotazů* ve fondu dotazů, snížení doby odezvy během dotazu vysoké zatížení. Můžete oddělit i zpracování od fondu dotazů, zajistit, že dotazy klientů neměly nepříznivý vliv na zpracování. Horizontální navýšení kapacity se dá nakonfigurovat na webu Azure portal nebo pomocí rozhraní REST API pro Analysis Services.

## <a name="how-it-works"></a>Jak to funguje

V typické server nasazení jednoho serveru slouží jako server pro zpracování a dotazu serveru. Počet klientů dotazy na modely na vašem serveru překračuje dotaz zpracování jednotky (QPU) pro váš server plán nebo zpracování modelu nastane ve stejnou dobu jako dotaz vysoké zatížení, může se snížit výkon. 

S horizontální navýšení kapacity můžete vytvořit fond dotazů s až sedmi prostředky repliky dalších dotazů (celkově osmi, včetně vašeho serveru). Můžete škálovat počet replik dotazu s cílem splnit požadavky QPU v kritické dobu a server pro zpracování od fondu dotazů můžete oddělit kdykoli. Všechny repliky dotazu se vytvoří ve stejné oblasti jako váš server.

Bez ohledu na počet replik dotazu, které máte ve fondu dotazů zpracování úloh nejsou distribuovány mezi replikami dotazu. Jeden server slouží jako server pro zpracování. Repliky dotazů sloužit pouze dotazy na modely synchronizovány mezi všech replik dotazu ve fondu dotazů. 

Při horizontálním navýšení kapacity, nové repliky dotazů se postupně přidají do fondu dotazů. Může trvat až pět minut, než u nových prostředků repliky dotazů mají být zahrnuty do fondu dotazů. Když všechny nové repliky dotazů nastavené a spuštěné, nová připojení klientů jsou vyrovnávat zatížení napříč všechny prostředky fondu dotazů. Existující připojení klienta se nezmění z prostředků, které jsou aktuálně připojené k.  Při horizontálním škálování v, jsou ukončeny všechny existující připojení klienta pro prostředek fondu dotaz, který má být odebrán z fondu dotazů. Bude přesměrován na zbývající prostředek fondu dotazů při škálování v operaci byla dokončena, to může trvat až pět minut.

Během zpracování po dokončení operace zpracování modelů, je nutné provést synchronizaci mezi serverem pro zpracování a replikami dotazu. Při automatizaci operace zpracování, je potřeba nakonfigurovat operace synchronizace po úspěšném dokončení operace zpracování. Synchronizace lze ručně provést na portálu nebo pomocí Powershellu nebo rozhraní REST API. 

### <a name="separate-processing-from-query-pool"></a>Samostatné zpracování od fondu dotazů

Pro maximální výkon pro zpracování a operace dotazů můžete oddělit serveru zpracování od fondu dotazů. V případě oddělení, stávající i nové připojení klientů přiřazených k replikami dotazu ve fondu dotazů. Pokud operace zpracování trvat pouze krátkou dobu, můžete oddělit serveru zpracování od fondu dotazů objem čas potřebný k provedení operace zpracování a synchronizaci a pak ho zahrňte zpět do fondu dotazů. 

> [!NOTE]
> Horizontální navýšení kapacity je k dispozici pro servery v cenovou úroveň Standard. Každé repliky dotazů se účtuje stejná sazba jako váš server.

> [!NOTE]
> Horizontální navýšení kapacity nezvyšuje velikost dostupné paměti pro váš server. Pro zvýšení paměti, budete muset upgradovat svůj plán.

## <a name="region-limits"></a>Omezení oblasti

Počet replik dotazu, které můžete nakonfigurovat se uplatňuje limit vycházející oblast, kterou je server v. Další informace najdete v tématu [dostupnost podle oblasti](analysis-services-overview.md#availability-by-region).

## <a name="monitor-qpu-usage"></a>Monitorování využití QPU

 Chcete-li zjistit, zda horizontální navýšení kapacity pro váš server, je nezbytné, sledování serveru na webu Azure portal pomocí metrik. Pokud vaše QPU pravidelně navyšuje navýšení kapacity, znamená to, že počet dotazů vůči vašich modelů je překročení limitu QPU pro váš plán. Délka metrika dotazu fondu úlohy fronty také zvýší v případě, že k dispozici QPU překračuje počet dotazů ve frontě fondu vláken dotazů. Další informace najdete v tématu [Monitorování metrik serveru](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Konfigurace horizontální navýšení kapacity

### <a name="in-azure-portal"></a>Na webu Azure portal

1. Na portálu klikněte na tlačítko **horizontální navýšení kapacity**. Pomocí posuvníku vyberte počet serverů replik dotazu. Počet replik, které zvolíte, je kromě existující server.

2. V **oddělte server pro zpracování od fondu dotazů**, vyberte Ano. Pokud chcete vyloučit ze serverů dotazu serveru zpracování. Připojení klienta pomocí výchozí připojovací řetězec (bez: rw) se přesměrují do replik ve fondu dotazů. 

   ![Horizontální navýšení kapacity posuvníku](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Klikněte na tlačítko **Uložit** ke zřízení nových serverů repliky dotazu. 

Tabulkové modely na primárním serveru jsou synchronizovány se serverem repliky. Po dokončení synchronizace fondu dotazů začne distribuci příchozích dotazů mezi serverem repliky. 

## <a name="synchronization"></a>Synchronizace 

Při zřizování nové repliky dotazů Azure Analysis Services automaticky replikuje vaše modely přes všechny repliky. Můžete provést také ruční synchronizaci pomocí portálu nebo rozhraní REST API. Při zpracování vašich modelů, měli byste provést synchronizaci tak synchronizaci aktualizací mezi repliky dotazů.

### <a name="in-azure-portal"></a>Na webu Azure portal

V **přehled** > model > **synchronizovat model**.

![Horizontální navýšení kapacity posuvníku](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

Použití **synchronizace** operace.

#### <a name="synchronize-a-model"></a>Synchronizovat model   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Získat stav synchronizace  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před použitím prostředí PowerShell, [nainstalovat nebo aktualizovat nejnovější modul Azure PowerShell](/powershell/azure/install-az-ps). 

Pokud chcete nastavit počet replik dotazu, použijte [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Zadejte nepovinný `-ReadonlyReplicaCount` parametru.

Chcete-li spustit synchronizaci, použijte [synchronizace AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

## <a name="connections"></a>Připojení

Na stránce přehled vašeho serveru jsou dva názvy serverů. Pokud jste ještě nenakonfigurovali horizontální navýšení kapacity pro server, oba názvy serverů fungují stejně. Jakmile nakonfigurujete horizontální navýšení kapacity pro server, musíte zadat název příslušného serveru v závislosti na typu připojení. 

Pro připojení klienta koncového uživatele, jako jsou Power BI Desktopu, Excelu a vlastní aplikace, použijte **název serveru**. 

Pro aplikace SSMS, SSDT a připojovací řetězce v prostředí PowerShell, aplikace Azure Function App a nástroji AMO, použijte **název serveru pro správu**. Název serveru pro správu obsahuje speciální `:rw` kvalifikátor (čtení a zápis). Všechny operace zpracování dojít na serveru pro správu.

![Názvy serverů](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>Řešení potíží

**Problém:** Uživatelé získají chyba **nelze nalézt server '\<název serveru > "instance v režimu připojení 'ReadOnly'.**

**Řešení:** Při výběru **oddělte server pro zpracování od fondu dotazů** možnost připojení klienta pomocí výchozí připojovací řetězec (bez: rw) se přesměrují na fond replikami dotazu. Pokud repliky ve fondu dotazů jsou online, ale protože synchronizace nebyl dosud nebylo dokončeno, přesměrované klientská připojení může selhat. Pokud chcete zabránit neúspěšná připojení, musí existovat alespoň dva servery ve fondu dotaz při provedení synchronizace. Každý server se synchronizuje jednotlivě, ostatní budou i nadále online. Pokud se rozhodnete, že nebudete chtít server zpracování ve fondu dotaz během zpracování, můžete ho odebrat z fondu pro zpracování a pak ho přidat zpět do fondu, jakmile se zpracování dokončí, ale před synchronizací. Použití paměti a QPU metriky pro monitorování stavu synchronizace.

## <a name="related-information"></a>Související informace

[Monitorování metrik serveru](analysis-services-monitor.md)   
[Správa služby Azure Analysis Services](analysis-services-manage.md) 

