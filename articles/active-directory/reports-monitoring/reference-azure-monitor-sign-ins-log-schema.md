---
title: Schéma přihlašovacího protokolu v Azure Monitor | Microsoft Docs
description: Popište schéma protokolu přihlášení Azure AD pro použití v Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d7c9713f27643e792ea381e1a2419cbc4b67a99
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82129199"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Interpretujte schéma přihlašovacích protokolů služby Azure AD v Azure Monitor

Tento článek popisuje schéma protokolu přihlašování Azure Active Directory (Azure AD) v Azure Monitor. Většina informací, které se vztahují k přihlašování, je k dispozici v atributu *Properties* `records` objektu.


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>Popisy polí

| Název pole | Popis |
|------------|-------------|
| Time | Datum a čas ve standardu UTC. |
| ResourceId | Tato hodnota není mapována a můžete toto pole bezpečně ignorovat.  |
| OperationName | Pro přihlášení je tato hodnota vždy *aktivita přihlášení*. |
| OperationVersion | Verze REST API, kterou klient požaduje. |
| Kategorie | U přihlášení se tato *hodnota vždy*přihlašuje. | 
| TenantId | Identifikátor GUID tenanta, který je přidružený k protokolům. |
| Hodnotu | Výsledkem operace přihlášení může být *úspěch* nebo *neúspěch*. | 
| ResultSignature | Obsahuje kód chyby, pokud existuje, pro operaci přihlášení. |
| ResultDescription | Poskytuje popis chyby pro operaci přihlášení. |
| riskDetail | riskDetail | Poskytuje "důvod" za konkrétní stav rizikového uživatele, přihlášení nebo zjišťování rizik. Možné hodnoty `none`jsou:, `adminGeneratedTemporaryPassword`, `userPerformedSecuredPasswordChange`, `userPerformedSecuredPasswordReset`, `adminConfirmedSigninSafe`, `aiConfirmedSigninSafe`, `userPassedMFADrivenByRiskBasedPolicy`, `adminDismissedAllRiskForUser`, `adminConfirmedSigninCompromised`,. `unknownFutureValue` Hodnota `none` znamená, že uživatel ani se přihlásila žádná akce. <br>**Poznámka:** Podrobnosti této vlastnosti vyžadují licenci Azure AD Premium P2. Další licence vrací hodnotu `hidden`. |
| riskEventTypes | riskEventTypes | Typy detekce rizik spojené s přihlášením Možné hodnoty `unlikelyTravel`jsou:, `anonymizedIPAddress`, `maliciousIPAddress`, `unfamiliarFeatures`, `malwareInfectedIPAddress`, `suspiciousIPAddress`, `leakedCredentials`, `investigationsThreatIntelligence`, `generic`a. `unknownFutureValue` |
| riskLevelAggregated | riskLevel | Agregovaná úroveň rizika. Možné hodnoty jsou `none`:, `low`, `medium`, `high`, `hidden`a. `unknownFutureValue` Hodnota `hidden` znamená, že uživatel nebo přihlášení nebylo povoleno Azure AD Identity Protection. **Poznámka:** Podrobnosti této vlastnosti jsou k dispozici pouze pro zákazníky Azure AD Premium P2. Vrátí `hidden`se všichni ostatní zákazníci. |
| riskLevelDuringSignIn | riskLevel | Úroveň rizika během přihlašování Možné hodnoty jsou `none`:, `low`, `medium`, `high`, `hidden`a. `unknownFutureValue` Hodnota `hidden` znamená, že uživatel nebo přihlášení nebylo povoleno Azure AD Identity Protection. **Poznámka:** Podrobnosti této vlastnosti jsou k dispozici pouze pro zákazníky Azure AD Premium P2. Vrátí `hidden`se všichni ostatní zákazníci. |
| riskState | riskState | Oznamuje stav rizikového uživatele, přihlášení nebo zjišťování rizik. Možné `none`hodnoty jsou:, `confirmedSafe`, `remediated`, `dismissed`, `atRisk`, `confirmedCompromised`,. `unknownFutureValue` |
| Trvání v MS |  Tato hodnota není mapována a můžete toto pole bezpečně ignorovat. |
| CallerIpAddress | IP adresa klienta, který odeslal požadavek. | 
| CorrelationId | Volitelný identifikátor GUID, který je předán klientem. Tato hodnota může pomoct sladit operace na straně klienta s operacemi na straně serveru a je užitečná při sledování protokolů, které zahrnují služby. |
| Identita | Identita z tokenu, který byl předložen při podání žádosti. Může to být uživatelský účet, systémový účet nebo instanční objekt. |
| Úroveň | Poskytuje typ zprávy. Pro audit je vždy *informativní*. |
| Umístění | Poskytuje umístění přihlašovací aktivity. |
| Vlastnosti | Zobrazí seznam všech vlastností, které jsou spojeny s přihlášeními. Další informace najdete v tématu [Microsoft Graph Reference k rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). Toto schéma používá pro účely čitelnosti stejné názvy atributů jako přihlašovací prostředek.

## <a name="next-steps"></a>Další kroky

* [Interpretace schématu protokolů auditu v Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Další informace o protokolech platforem Azure](../../azure-monitor/platform/platform-logs-overview.md)
