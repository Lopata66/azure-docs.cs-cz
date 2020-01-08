---
title: Windows Virtual Desktop Diagnostics Log Analytics – Azure
description: Jak používat Log Analytics s funkcí diagnostiky virtuálních klientů Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
ms.openlocfilehash: f50e5144080ffb1c91e834c757082bb57eeaffcb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479225"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Použití Log Analytics pro diagnostickou funkci

Virtuální plocha Windows nabízí diagnostické funkce, které správci umožňují identifikovat problémy přes jedno rozhraní. Tato funkce zaznamená diagnostické informace vždy, když někdo přiřazená role virtuálního klienta Windows používá službu. Každý protokol obsahuje informace o tom, která role virtuálního počítače s Windows se v aktivitě účastnila, všechny chybové zprávy, které se zobrazí během relace, informace o tenantovi a informace o uživateli. Funkce diagnostiky vytvoří protokoly aktivit pro uživatele i pro akce správy. Každý protokol aktivit spadá do tří hlavních kategorií: 

- Aktivity předplatného informačního kanálu: když se uživatel pokusí připojit k informačnímu kanálu prostřednictvím Vzdálená plocha Microsoftch aplikací.
- Aktivity připojení: když se uživatel pokusí připojit k desktopu nebo k vzdálené aplikaci RemoteApp prostřednictvím Vzdálená plocha Microsoftch aplikací.
- Aktivity správy: Když správce provádí operace správy v systému, jako je vytváření fondů hostitelů, přiřazování uživatelů ke skupinám aplikací a vytváření přiřazení rolí.

Připojení, která nedosáhnou virtuálního klienta Windows, se nezobrazí ve výsledcích diagnostiky, protože samotná služba role diagnostiky je součástí virtuálního klienta Windows. Problémy s připojením k virtuálnímu počítači s Windows se můžou vyskytnout, když uživatel dochází k problémům se síťovým připojením.

## <a name="why-you-should-use-log-analytics"></a>Proč byste měli použít Log Analytics

Doporučujeme použít Log Analytics k analýze diagnostických dat v klientovi Azure, který překračuje řešení potíží pro jednoho uživatele. Jak můžete načíst čítače výkonu virtuálních počítačů do Log Analytics máte jeden nástroj pro shromáždění informací pro vaše nasazení.

## <a name="before-you-get-started"></a>Než začnete

Než budete moci použít Log Analytics s diagnostikou funkce, budete muset [vytvořit pracovní prostor](../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace).

Po vytvoření pracovního prostoru postupujte podle pokynů v tématu [připojení počítačů se systémem Windows k Azure monitor](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) k získání následujících informací: 

- ID pracovního prostoru
- Primární klíč vašeho pracovního prostoru

Tyto informace budete potřebovat později v procesu instalace.

## <a name="push-diagnostics-data-to-your-workspace"></a>Vložení diagnostických dat do pracovního prostoru 

Diagnostická data můžete z klienta virtuální plochy Windows nahrajte do Log Analytics pro váš pracovní prostor. Tuto funkci můžete nastavit hned, když vytvoříte tenanta tak, že ho propojíte s vaším klientem, nebo ho můžete nastavit později s existujícím klientem.

Pokud chcete klienta propojit s pracovním prostorem Log Analytics během nastavování nového tenanta, spusťte následující rutinu pro přihlášení k virtuálnímu počítači s Windows pomocí uživatelského účtu TenantCreator: 

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com 
```

Pokud budete chtít propojit existujícího tenanta místo nového tenanta, spusťte tuto rutinu: 

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Tyto rutiny budete muset spustit pro každého tenanta, kterého chcete propojit Log Analytics. 

>[!NOTE]
>Pokud nechcete při vytváření tenanta propojit Log Analytics pracovní prostor, spusťte rutinu `New-RdsTenant`. 

## <a name="cadence-for-sending-diagnostic-events"></a>Tempo pro odesílání diagnostických událostí

Události diagnostiky se odesílají do Log Analytics, když se dokončí.  

## <a name="example-queries"></a>Příklady dotazů

Následující ukázkové dotazy ukazují, jak funkce diagnostiky generuje sestavu pro nejčastěji používané aktivity ve vašem systému:

Tento první příklad ukazuje aktivity připojení iniciované uživateli s podporovanými klienty vzdálené plochy:

```powershell
WVDActivityV1_CL 

| where Type_s == "Connection" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```

Tento další ukázkový dotaz ukazuje aktivity správy správců v klientech:

```powershell
WVDActivityV1_CL 

| where Type_s == "Management" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```
 
## <a name="stop-sending-data-to-log-analytics"></a>Zastavit odesílání dat do Log Analytics 

Pokud chcete zastavit odesílání dat ze stávajícího tenanta do Log Analytics, spusťte následující rutinu a nastavte prázdné řetězce:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Tuto rutinu budete muset spustit pro každého tenanta, ze kterého chcete zastavit odesílání dat. 

## <a name="next-steps"></a>Další kroky 

Pokud si chcete projít běžné chybové scénáře, které vám diagnostická funkce může zjistit, přečtěte si téma [identifikace a Diagnostika problémů](diagnostics-role-service.md#common-error-scenarios).
