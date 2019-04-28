---
title: Předdefinované role pro prostředky Azure | Dokumentace Microsoftu
description: Popisuje předdefinované role pro řízení přístupu na základě role (RBAC) a prostředky Azure. Zobrazí seznam akcí, NotActions, DataActions a NotDataActions.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/01/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: f505f922685cd192525814df25cca1a1401d2913
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749321"
---
# <a name="built-in-roles-for-azure-resources"></a>Předdefinované role pro prostředky Azure

[Řízení přístupu na základě role (RBAC)](overview.md) má několik předdefinovaných rolí pro prostředky Azure, které můžete přiřadit uživatelům, skupinám, instančních objektů a spravovaných identit. Přiřazení rolí představují způsob, jak řídit přístup k prostředkům Azure. Pokud předdefinované role není splnili specifické požadavky vaší organizace, můžete vytvořit vlastní [vlastních rolí pro prostředky Azure](custom-roles.md).

Tento článek uvádí předdefinované role pro prostředky Azure, které jsou vždy se vyvíjejí. Pokud chcete získat nejnovější role, použít [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) nebo [az role definition list](/cli/azure/role/definition#az-role-definition-list). Pokud chcete pro role Správce služby Azure Active Directory, přečtěte si téma [oprávnění role správce v Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Popisy předdefinovaná role

Následující tabulka obsahuje stručný popis jednotlivých předdefinovaných rolí. Klikněte na název role, které chcete zobrazit seznam `Actions`, `NotActions`, `DataActions`, a `NotDataActions` pro každou roli. Informace o významu těchto akcí a jak se vztahují na roviny správy a data, najdete v části [pochopení definic rolí pro prostředky Azure](role-definitions.md).


| Předdefinovaná role | Popis |
| --- | --- |
| [Vlastník](#owner) | Umožňuje správu všech položek včetně přístupu k prostředkům. |
| [Přispěvatel](#contributor) | Umožňuje správu všech položek kromě přístupu k prostředkům. |
| [Čtenář](#reader) | Umožňuje zobrazit všechny položky, ale ne provádět změny. |
| [AcrDelete](#acrdelete) | acr delete |
| [AcrImageSigner](#acrimagesigner) | podepisující osoba image ACR |
| [AcrPull](#acrpull) | acr pull |
| [AcrPush](#acrpush) | acr push |
| [AcrQuarantineReader](#acrquarantinereader) | čtenář dat karantény ACR |
| [AcrQuarantineWriter](#acrquarantinewriter) | zapisovatel dat karantény ACR |
| [Přispěvatel služby API Management](#api-management-service-contributor) | Může spravovat službu a rozhraní API |
| [Role operátor služby API Management](#api-management-service-operator-role) | Může spravovat službu, ale ne rozhraní API. |
| [Role Čtenář služby API Management](#api-management-service-reader-role) | Přístup ke službě a rozhraním API jen ke čtení |
| [Application Insights Component Contributor](#application-insights-component-contributor) | Může spravovat součásti Application Insights. |
| [Application Insights Snapshot debuggeru.](#application-insights-snapshot-debugger) | Uděluje oprávnění uživatele zobrazovat a stahovat ladicí snímky shromážděná pomocí Application Insights Snapshot debuggeru. Všimněte si, že tato oprávnění nejsou součástí [vlastníka](#owner) nebo [Přispěvatel](#contributor) role. |
| [Operátor úloh Automation](#automation-job-operator) | Vytváření a správa úloh pomocí runbooků služby Automation |
| [Operátor služby Automation](#automation-operator) | Operátoři Automation můžou spouštět, zastavovat, pozastavovat a obnovovat úlohy. |
| [Operátor Runbooků Automation](#automation-runbook-operator) | Čtení vlastností runbooků – aby se daly vytvářet úlohy daného runbooku |
| [Přispěvatel Avere](#avere-contributor) | Můžete vytvářet a spravovat Avere vFXT cluster. |
| [Avere – operátor](#avere-operator) | Avere vFXT cluster používat ke správě clusteru |
| [Role Správce služby Azure Kubernetes Cluster](#azure-kubernetes-service-cluster-admin-role) | Akce výpisu přihlašovacích údajů správce clusteru |
| [Role uživatele Cluster Azure Kubernetes Service](#azure-kubernetes-service-cluster-user-role) | Akce výpisu přihlašovacích údajů uživatele clusteru |
| [Vlastník registrace Azure Stack](#azure-stack-registration-owner) | Umožňuje spravovat registrace Azure Stack. |
| [Přispěvatel zálohování](#backup-contributor) | Umožňuje spravovat službu zálohování, ale neumožňuje vytvářet trezory a udělovat přístup ostatním uživatelům. |
| [Operátor zálohování](#backup-operator) | Umožňuje spravovat služby zálohování s výjimkou odebírání záloh, vytváření trezorů a udělování přístupu jiným uživatelům. |
| [Čtenář záloh](#backup-reader) | Může zobrazovat služby zálohování, ale nemůže provádět změny. |
| [Čtenář fakturace](#billing-reader) | Povolí přístup pro čtení fakturačních údajů. |
| [BizTalk Contributor](#biztalk-contributor) | Umožňuje správu služeb BizTalk, ale ne přístup k nim. |
| [Přispěvatel koncového bodu CDN](#cdn-endpoint-contributor) | Může spravovat koncové body CDN, ale nemůže udělovat přístup jiným uživatelům. |
| [Čtenář koncového bodu CDN](#cdn-endpoint-reader) | Může zobrazovat koncové body CDN, ale nemůže je měnit. |
| [Přispěvatel profilu CDN](#cdn-profile-contributor) | Může spravovat profily CDN a jejich koncové body, ale nemůže udělovat přístup jiným uživatelům. |
| [Čtenář profilu CDN](#cdn-profile-reader) | Může zobrazovat profily CDN a jejich koncové body, ale nemůže je měnit. |
| [Přispěvatel klasických sítí](#classic-network-contributor) | Umožňuje správu klasických sítí, ale ne přístup k nim. |
| [Přispěvatel klasických účtů úložiště](#classic-storage-account-contributor) | Umožňuje správu klasických účtů úložiště, ale ne přístup k nim. |
| [Role služby operátor klíčů účtů klasického úložiště.](#classic-storage-account-key-operator-service-role) | Operátoři klíčů klasických účtů úložiště můžou vypisovat a znovu generovat klíče pro klasické účty úložiště. |
| [Přispěvatel klasických virtuálních počítačů](#classic-virtual-machine-contributor) | Umožňuje správu klasických virtuálních počítačů, ale ne přístup k nim ani k virtuální síti nebo účtu úložiště, ke kterým jsou připojené. |
| [Přispěvatel služby cognitive Services](#cognitive-services-contributor) | Umožňuje vytvořit, přečíst, aktualizovat, odstranit a spravovat klíče služeb Cognitive Services. |
| [Cognitive Services Data Reader (Preview)](#cognitive-services-data-reader-preview) | Umožní vám čtení dat Cognitive Services. |
| [Cognitive Services User](#cognitive-services-user) | Umožňuje přečíst a vypsat klíče služeb Cognitive Services. |
| [Role čtenáře účtu cosmos DB](#cosmos-db-account-reader-role) | Může číst data účtu služby Azure Cosmos DB. Zobrazit [Přispěvatel účtů DocumentDB](#documentdb-account-contributor) ke správě účtů službu Azure Cosmos DB. |
| [CosmosBackupOperator](#cosmosbackupoperator) | Může odesílat požadavky na obnovení databáze Cosmos DB nebo kontejneru pro určitý účet. |
| [Služba Cost Management přispěvatele](#cost-management-contributor) | Může zobrazovat náklady a spravovat jejich konfiguraci (např. rozpočty, exporty). |
| [Služba Cost Management čtečky](#cost-management-reader) | Může zobrazovat data a konfiguraci nákladů (např. rozpočty, exporty). |
| [Přispěvatel data Box](#data-box-contributor) | Umožňuje spravovat vše ve službě Data Box Service kromě udělení přístupu jiným uživatelům. |
| [Čtecí modul dat pole](#data-box-reader) | Umožňuje spravovat službu Data Box Service kromě vytváření objednávky nebo úpravy podrobností o objednávce a udělení přístupu jiným uživatelům. |
| [Přispěvatel data Factory](#data-factory-contributor) | Umožňuje vytvářet a spravovat datové továrny i podřízené prostředky v nich. |
| [Vývojář data Lake Analytics](#data-lake-analytics-developer) | Umožňuje odesílat, monitorovat a spravovat vlastní úlohy, ale neumožňuje vytvářet ani odstraňovat účty Data Lake Analytics. |
| [Data Purger](#data-purger) | Může vyprázdnit analytická data. |
| [DevTest Labs User](#devtest-labs-user) | Umožňuje spouštět, restartovat a vypínat virtuální počítače v Azure DevTest Labs a připojovat se k nim. |
| [Přispěvatel zóny DNS](#dns-zone-contributor) | Umožňuje spravovat zóny DNS a sady záznamů v Azure DNS, ale neumožňuje řídit, kdo k nim má přístup. |
| [Přispěvatel účtů DocumentDB](#documentdb-account-contributor) | Můžete spravovat účty služby Azure Cosmos DB. Azure Cosmos DB je dříve DocumentDB. |
| [Přispěvatel EventSubscription EventGrid](#eventgrid-eventsubscription-contributor) | Umožňuje spravovat operace předplatných událostí Event Gridu. |
| [Čtečka EventSubscription EventGrid](#eventgrid-eventsubscription-reader) | Umožňuje číst předplatná událostí Event Gridu. |
| [HDInsight Domain Services Contributor](#hdinsight-domain-services-contributor) | Může číst, vytvářet, upravovat a odstraňovat operace, které souvisí s Domain Services a jsou nezbytné pro Balíček zabezpečení podniku HDInsight. |
| [Přispěvatel účtů inteligentních systémů](#intelligent-systems-account-contributor) | Umožňuje správu účtů inteligentních systémů, ale ne přístup k nim. |
| [Přispěvatel Key Vaultu](#key-vault-contributor) | Umožňuje spravovat trezory klíčů, ale ne přistupovat k nim. |
| [Autor testovacího prostředí](#lab-creator) | Umožňuje vytvářet, spravovat a odstraňovat spravovaná testovací prostředí v účtech Azure Lab. |
| [Přispěvatel log Analytics](#log-analytics-contributor) | Přispěvatel log Analytics může číst všechna data monitorování a upravit nastavení monitorování. Úprava nastavení monitorování, zahrnuje přidání rozšíření virtuálního počítače na virtuální počítače; čtení klíčů účtů úložiště, abyste mohli konfigurovat shromažďování protokolů ze služby Azure Storage; Vytvoření a konfigurace účtů služby Automation; přidávání řešení a konfigurace diagnostik Azure na všech prostředcích Azure. |
| [Čtenář log Analytics](#log-analytics-reader) | Čtenář Log Analytics si může zobrazit a vyhledat všechna data monitorování a jeho nastavení. Může si zobrazit konfiguraci diagnostiky Azure na všech prostředcích Azure. |
| [Přispěvatel aplikace logiky](#logic-app-contributor) | Umožňuje správu aplikace logiky, ale ne přístup k ní. |
| [Operátor aplikace logiky](#logic-app-operator) | Umožňuje číst, povolovat a zakazovat aplikaci logiky. |
| [Role operátora spravované aplikace](#managed-application-operator-role) | Umožňuje číst a provádět akce s prostředky spravované aplikace. |
| [Čtečka spravované aplikace](#managed-applications-reader) | Umožňuje číst prostředky ve spravované aplikace a žádat o přístup podle potřeby. |
| [Přispěvatel spravovaných identit](#managed-identity-contributor) | Vytváření, čtení, aktualizace a odstraňování identity přiřazené uživateli |
| [Operátor spravovaných identit](#managed-identity-operator) | Čtení a přiřazování identity přiřazené uživateli |
| [Přispěvatel skupiny pro správu](#management-group-contributor) | Role Přispěvatel skupiny pro správu |
| [Čtečka skupiny pro správu](#management-group-reader) | Role Čtenář skupiny pro správu |
| [Přispěvatel monitorování](#monitoring-contributor) | Může číst všechna data monitorování a upravit nastavení monitorování. Viz také [role, oprávnění a zabezpečení prostřednictvím služby Azure Monitor vám začít](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Monitorování metrik vydavatele](#monitoring-metrics-publisher) | Umožňuje publikovat metriky proti prostředkům Azure. |
| [Čtenář monitorování](#monitoring-reader) | Může číst všechna data monitorování (metriky, protokoly atd.). Viz také [role, oprávnění a zabezpečení prostřednictvím služby Azure Monitor vám začít](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Přispěvatel sítě](#network-contributor) | Umožňuje správu sítí, ale ne přístup k nim. |
| [Nové Přispěvatel účtů Relic APM](#new-relic-apm-account-contributor) | Umožňuje správu účtů a aplikací New Relic Application Performance Management, ale ne přístup k nim. |
| [Čtenář a přístup k datům](#reader-and-data-access) | Umožňuje zobrazit všechno, ale neumožní odstraňovat ani vytvářet účet úložiště nebo obsažený prostředek. To vám také umožní přístup pro čtení a zápis ke všem datům obsaženým v účtu úložiště prostřednictvím přístupu ke klíčům účtu úložiště. |
| [Přispěvatel služby redis Cache](#redis-cache-contributor) | Umožňuje správu mezipamětí Redis, ale ne přístup k nim. |
| [Přispěvatel zásad prostředků (Preview)](#resource-policy-contributor-preview) | (Preview) Obnovení uživatelé z EA s oprávněními vytvářet a upravovat zásady prostředků, vytvářet lístky podpory a číst prostředky a hierarchii |
| [Přispěvatel kolekcí úloh scheduleru](#scheduler-job-collections-contributor) | Umožňuje správu kolekcí úloh Scheduleru, ale ne přístup k nim. |
| [Přispěvatel služby Search](#search-service-contributor) | Umožňuje správu služeb Search, ale ne přístup k nim. |
| [Správce zabezpečení](#security-admin) | Ve službě Security Center jenom: Můžete zobrazit zásady zabezpečení, zobrazení stavu zabezpečení, upravit zásady zabezpečení, zobrazení výstrah a doporučení, Zavřít upozornění a doporučení |
| [Správce zabezpečení (starší verze)](#security-manager-legacy) | Toto je starší verze role. Místo toho použijte Správce zabezpečení |
| [Čtenář zabezpečení](#security-reader) | Ve službě Security Center jenom: Můžete zobrazit doporučení a výstrahy, zásady zabezpečení, zobrazit stavy zabezpečení, ale nemůže provádět změny zobrazení |
| [Přispěvatel Site Recovery](#site-recovery-contributor) | Umožňuje spravovat službu Site Recovery, ale ne vytvářet trezory a přiřazovat role. |
| [Operátor Site Recovery](#site-recovery-operator) | Umožňuje provádět převzetí služeb při selhání a navrácení služeb po obnovení, ale žádné jiné operace správy služby Site Recovery. |
| [Čtenář Site Recovery](#site-recovery-reader) | Umožňuje zobrazovat stav služby Site Recovery, ale ne provádět jiné operace správy. |
| [Přispěvatel účtů prostorových kotvy](#spatial-anchors-account-contributor) | Umožní vám správu prostorových kotev na vašem účtu, ale ne jejich odstranění. |
| [Vlastník účtu prostorových kotvy](#spatial-anchors-account-owner) | Umožní vám správu prostorových kotev na vašem účtu, včetně jejich odstranění. |
| [Účet čtečky prostorových kotvy](#spatial-anchors-account-reader) | Umožní vám vyhledávání a čtení vlastností prostorových kotev na vašem účtu. |
| [Přispěvatel databází SQL](#sql-db-contributor) | Umožňuje správu databází SQL, ale ne přístup k nim. Kromě toho nemůžete spravovat jejich zásady související se zabezpečením nebo nadřazené servery SQL. |
| [SQL Security Manager](#sql-security-manager) | Umožňuje vám spravovat zásady vztahující se k zabezpečení serverů a databází SQL, ale ne přístup k nim. |
| [Přispěvatel SQL serveru](#sql-server-contributor) | Umožňuje vám spravovat servery a databáze SQL, ale ne přístup k nim ani jejich zásady vztahující se k zabezpečení. |
| [Přispěvatel účtů úložiště](#storage-account-contributor) | Umožňuje správu účtů úložiště, ale ne přístup k nim. |
| [Role služby operátor klíčů účtů úložiště](#storage-account-key-operator-service-role) | Operátoři klíčů účtů úložiště můžou vypisovat a znovu generovat klíče pro účty úložiště. |
| [Storage Blob Data Contributor](#storage-blob-data-contributor) | Umožňuje číst, zapisovat a odstraňovat kontejnery a data objektů blob služby Azure Storage. |
| [Vlastník dat úložiště objektů Blob](#storage-blob-data-owner) | Umožňuje úplný přístup k kontejnery objektů blob v Azure Storage a data, včetně přiřazování řízení přístupu POSIX. |
| [Čtenář dat objektu Blob služby Storage](#storage-blob-data-reader) | Umožňuje číst kontejnery a data objektů blob služby Azure Storage. |
| [Přispěvatel dat fronty služby Storage](#storage-queue-data-contributor) | Umožňuje číst, zapisovat a odstraňovat fronty služby Azure Storage a jejich zprávy. |
| [Zpracovatelem zprávu fronty úložiště](#storage-queue-data-message-processor) | Umožňuje zobrazovat, přijímat a odstraňovat zprávy ve frontách služby Azure Storage. |
| [Odesílatel zprávy Data fronty úložiště](#storage-queue-data-message-sender) | Umožňuje odesílání zpráv ve frontách služby Azure Storage. |
| [Čtenář dat fronty služby Storage](#storage-queue-data-reader) | Umožňuje číst fronty služby Azure Storage a jejich zprávy. |
| [Přispěvatel žádostí o podporu](#support-request-contributor) | Umožňuje vytvořit a spravovat žádosti o podporu. |
| [Přispěvatel Traffic Manageru](#traffic-manager-contributor) | Umožňuje spravovat profily Traffic Manageru, ale neumožňuje řídit, kdo k nim má přístup. |
| [Správce uživatelských přístupů](#user-access-administrator) | Umožňuje správu přístupu uživatelů k prostředkům Azure. |
| [Přihlášení správce virtuálního počítače](#virtual-machine-administrator-login) | Zobrazit virtuální počítače na portálu a přihlásit se jako správce |
| [Přispěvatel virtuálních počítačů](#virtual-machine-contributor) | Umožňuje správu virtuálních počítačů, ale ne přístup k nim ani k virtuální síti nebo účtu úložiště, ke kterým jsou připojené. |
| [Přihlášení uživatele virtuálního počítače](#virtual-machine-user-login) | Zobrazit virtuální počítače na portálu a přihlásit se jako běžný uživatel |
| [Přispěvatel webových plánů](#web-plan-contributor) | Umožňuje správu webových plánů pro weby, ale ne přístup k nim. |
| [Přispěvatel webů](#website-contributor) | Umožňuje správu webů (ne webových plánů), ale ne přístup k nim. |


## <a name="owner"></a>Vlastník
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu všech položek včetně přístupu k prostředkům. |
> | **ID** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Akce** |  |
> | * | Vytváření a správě prostředků všech typů. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="contributor"></a>Přispěvatel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu všech položek kromě přístupu k prostředkům. |
> | **ID** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Akce** |  |
> | * | Vytváření a správě prostředků všech typů. |
> | **notActions** |  |
> | Microsoft.Authorization/*/Delete | Odstranit role a přiřazení rolí |
> | Microsoft.Authorization/*/Write | Vytvoření role a přiřazení rolí |
> | Microsoft.Authorization/elevateAccess/Action | Udělí volajícímu přístup Správce uživatelských přístupů v oboru tenanta. |
> | Microsoft.Blueprint/blueprintAssignments/write | Vytvoří nebo aktualizuje všechny artefakty podrobného plánu. |
> | Microsoft.Blueprint/blueprintAssignments/delete | Odstraní všechny artefakty podrobného plánu. |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="reader"></a>Čtenář
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje zobrazit všechny položky, ale ne provádět změny. |
> | **ID** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Akce** |  |
> | * / čtení | Číst prostředky všech typů, s výjimkou tajných kódů. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="acrdelete"></a>AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | acr delete |
> | **ID** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Akce** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | Odstranění artefaktů v registru kontejneru. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | podepisující osoba image ACR |
> | **ID** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Akce** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | Vyžádanou metadata obsahu vztahu důvěryhodnosti pro službu container registry. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | acr pull |
> | **ID** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Akce** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | O přijetí změn nebo získat Image z registru kontejneru. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | acr push |
> | **ID** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Akce** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | O přijetí změn nebo získat Image z registru kontejneru. |
> | Microsoft.ContainerRegistry/registries/push/write | Vložit nebo zapíšete Image do registru kontejneru. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | čtenář dat karantény ACR |
> | **ID** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Akce** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | O přijetí změn nebo získat v karanténě imagí z registru kontejneru |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | zapisovatel dat karantény ACR |
> | **ID** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Akce** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | O přijetí změn nebo získat v karanténě imagí z registru kontejneru |
> | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Zápis nebo upravit stav karantény v karanténě imagí |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="api-management-service-contributor"></a>Přispěvatel služby API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může spravovat službu a rozhraní API |
> | **ID** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Akce** |  |
> | Microsoft.ApiManagement/service/* | Vytvoření a Správa služby API Management |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="api-management-service-operator-role"></a>Role Operátor služby API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může spravovat službu, ale ne rozhraní API. |
> | **ID** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Akce** |  |
> | Microsoft.ApiManagement/service/*/read | Instance služby API Management pro čtení |
> | Microsoft.ApiManagement/service/backup/action | Zálohování služby API Management do zadaného kontejneru v uživatelem zadaný účet úložiště |
> | Microsoft.ApiManagement/service/delete | Odstranění instance služby API Management |
> | Microsoft.ApiManagement/service/managedeployments/action | Změna skladové položky a jednotek, přidat nebo odebrat místní nasazení služby API Management |
> | Microsoft.ApiManagement/service/read | Čtení metadat pro instanci služby API Management |
> | Microsoft.ApiManagement/service/restore/action | Obnovení ze zadaného kontejneru v účtu úložiště zadanou uživatelem služby API Management |
> | Microsoft.ApiManagement/service/updatecertificate/action | Nahrát certifikát SSL pro službu API Management |
> | Microsoft.ApiManagement/service/updatehostname/action | Instalační program, aktualizovat nebo odebrat vlastní názvy domén pro službu API Management |
> | Microsoft.ApiManagement/service/write | Vytvořit novou instanci služby API Management |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Získá seznam klíčů uživatelů |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="api-management-service-reader-role"></a>Role Čtenář služby API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Přístup ke službě a rozhraním API jen ke čtení |
> | **ID** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Akce** |  |
> | Microsoft.ApiManagement/service/*/read | Instance služby API Management pro čtení |
> | Microsoft.ApiManagement/service/read | Čtení metadat pro instanci služby API Management |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Získá seznam klíčů uživatelů |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="application-insights-component-contributor"></a>Přispěvatel součástí Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může spravovat součásti Application Insights. |
> | **ID** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.Insights/components/* | Vytváření a správě součásti Insights |
> | Microsoft.Insights/webtests/* | Vytvoření a Správa webových testů |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Uděluje oprávnění uživatele zobrazovat a stahovat ladicí snímky shromážděná pomocí Application Insights Snapshot debuggeru. Všimněte si, že tato oprávnění nejsou součástí [vlastníka](#owner) nebo [Přispěvatel](#contributor) role. |
> | **ID** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="automation-job-operator"></a>Operátor úloh Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Vytváření a správa úloh pomocí runbooků služby Automation |
> | **ID** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Čte prostředky Hybrid Runbook Worker |
> | Microsoft.Automation/automationAccounts/jobs/read | Načte úlohu Azure Automation. |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Obnoví běh úlohy Azure Automation. |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Zastaví úlohu Azure Automation. |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Načte stream úloh Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Pozastaví úlohu Azure Automation. |
> | Microsoft.Automation/automationAccounts/jobs/write | Vytvoří úlohu Azure Automation. |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Získá výstup úlohy |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="automation-operator"></a>Operátor služby Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Operátoři Automation můžou spouštět, zastavovat, pozastavovat a obnovovat úlohy. |
> | **ID** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Čte prostředky Hybrid Runbook Worker |
> | Microsoft.Automation/automationAccounts/jobs/read | Načte úlohu Azure Automation. |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Obnoví běh úlohy Azure Automation. |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Zastaví úlohu Azure Automation. |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Načte stream úloh Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Pozastaví úlohu Azure Automation. |
> | Microsoft.Automation/automationAccounts/jobs/write | Vytvoří úlohu Azure Automation. |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Získá plán úloh Azure Automation |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Vytvoří plán úloh Azure Automation |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Získá pracovní prostor propojený s účtem služby automation |
> | Microsoft.Automation/automationAccounts/read | Získá účet Azure Automation |
> | Microsoft.Automation/automationAccounts/runbooks/read | Načte runbook služby Azure Automation. |
> | Microsoft.Automation/automationAccounts/schedules/read | Načte prostředek plánování Azure Automation. |
> | Microsoft.Automation/automationAccounts/schedules/write | Vytvoří nebo aktualizuje prostředek plánování Azure Automation. |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Získá výstup úlohy |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="automation-runbook-operator"></a>Operátor runbooků Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Čtení vlastností runbooků – aby se daly vytvářet úlohy daného runbooku |
> | **ID** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Automation/automationAccounts/runbooks/read | Načte runbook služby Azure Automation. |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="avere-contributor"></a>Přispěvatel Avere
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Můžete vytvářet a spravovat Avere vFXT cluster. |
> | **ID** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Compute/*/read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disks/* |  |
> | Microsoft.Network/*/read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Microsoft.Network/virtualNetworks/subnets/read | Získá definici podsítě virtuální sítě |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Připojí virtuální sítě. Výstražné. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Prostředků, jako je například účet úložiště nebo databáze SQL se připojí k podsíti. Výstražné. |
> | Microsoft.Network/networkSecurityGroups/join/action | Spojí skupinu zabezpečení sítě. Výstražné. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Storage/*/read |  |
> | Microsoft.Storage/storageAccounts/* |  |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | Načte prostředky pro skupinu prostředků. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Vrátí výsledek odstraňování objektu blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Vrátí objekt blob nebo seznam objektů blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Vrátí výsledek zápisu objektu blob. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="avere-operator"></a>Avere – operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Avere vFXT cluster používat ke správě clusteru |
> | **ID** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Akce** |  |
> | Microsoft.Compute/virtualMachines/read | Umožňuje získat vlastnosti virtuálního počítače. |
> | Microsoft.Network/networkInterfaces/read | Získá definici rozhraní sítě.  |
> | Microsoft.Network/networkInterfaces/write | Vytvoří síťové rozhraní nebo aktualizuje existující síťové rozhraní.  |
> | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Microsoft.Network/virtualNetworks/subnets/read | Získá definici podsítě virtuální sítě |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Připojí virtuální sítě. Výstražné. |
> | Microsoft.Network/networkSecurityGroups/join/action | Spojí skupinu zabezpečení sítě. Výstražné. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Vrátí výsledek odstraňování kontejneru. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Vrátí seznam kontejnerů. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Vrátí výsledek vložení objektů blob v kontejneru |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Vrátí výsledek odstraňování objektu blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Vrátí objekt blob nebo seznam objektů blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Vrátí výsledek zápisu objektu blob. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Role správce clusteru služby Azure Kubernetes Service
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Akce výpisu přihlašovacích údajů správce clusteru |
> | **ID** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Akce** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Vypsat přihlašovací údaje správce spravovaného clusteru |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Role uživatele clusteru služby Azure Kubernetes Service
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Akce výpisu přihlašovacích údajů uživatele clusteru |
> | **ID** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Akce** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Vypsat přihlašovací údaje uživatele spravovaného clusteru |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="azure-stack-registration-owner"></a>Vlastník registrace Azure Stack
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat registrace Azure Stack. |
> | **ID** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Akce** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Načte podrobnosti o produkt poskytovaný Azure Stack Marketplace rozšířené |
> | Microsoft.AzureStack/registrations/products/read | Získá vlastnosti o produkt poskytovaný Azure Stack Marketplace |
> | Microsoft.AzureStack/registrations/read | Získá vlastnosti registrace Azure Stack |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="backup-contributor"></a>Přispěvatel zálohování
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat službu zálohování, ale neumožňuje vytvářet trezory a udělovat přístup ostatním uživatelům. |
> | **ID** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Spravovat výsledky operace správy zálohování |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Vytvoření a Správa zálohování kontejnery v rámci zálohování Fabric trezoru služby Recovery Services |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Aktualizuje seznam kontejnerů. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Vytvoření a Správa úloh zálohování |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportovat úlohy |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Vytvoření a správa metadat týkající se správy zálohování |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Vytvářet a spravovat výsledky operací správy zálohování |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Vytvoření a Správa zásad zálohování |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Vytvoření a Správa položek, které se dají zálohovat |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Vytvoření a správa zálohované položky. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Vytváření a správu kontejnerů, která uchovává zálohované položky |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Vrátí souhrny pro chráněné položky a chráněné servery služby Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Vytváření a správě certifikátů související se zálohy v trezoru služby Recovery Services |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Vytvoření a správa rozšířené informace týkající se trezoru |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá upozornění pro trezor služby Recovery services. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Operace získat úložiště získá objekt, představuje prostředek Azure typu "úložiště. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Vytvoření a správa registrovaných identit |
> | Microsoft.RecoveryServices/Vaults/usages/* | Vytvořit a spravovat využití trezoru služby Recovery Services |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Ověření operace na chráněné položky |
> | Microsoft.RecoveryServices/Vaults/write | Operace Vytvořit trezor vytvoří prostředek Azure typu trezor. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Vrátí operace zálohování trezoru služby stavu pro obnovení. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Vrátí všechny servery správy zálohování zaregistrované s trezorem. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Získat všechny kontejnery s podporou ochrany |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Kontrola stavu zálohování pro trezory služby Recovery Services |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Ověření funkcí |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Vyřeší upozornění. |
> | Microsoft.RecoveryServices/operations/read | Operace vrátí seznam operací pro poskytovatele prostředků. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Získá stav operace pro danou operaci |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Seznam všech záloh záměry ochrany |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="backup-operator"></a>Operátor zálohování
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat služby zálohování s výjimkou odebírání záloh, vytváření trezorů a udělování přístupu jiným uživatelům. |
> | **ID** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Vrátí stav operace |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Načte výsledky operace provedené na kontejneru ochrany. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Provede zálohování chráněné položky. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Načte výsledky operace provedené na chráněných položkách. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Načte stav operace provedené na chráněných položkách. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Vrátí podrobnosti objektu chráněné položky. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Zřízení okamžitého obnovení položky pro chráněnou položku |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Načíst body obnovení pro chráněné položky |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Obnoví body obnovení pro chráněné položky |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Odvolání okamžitého obnovení položky pro chráněnou položku |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Vytvořit položku chráněnou zálohováním. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Vrátí všechny registrované kontejnery. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Aktualizuje seznam kontejnerů. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Vytvoření a Správa úloh zálohování |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportovat úlohy |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Vytvářet a spravovat výsledky operací správy zálohování |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Načte výsledky operace zásad. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Vrátí všechny zásady ochrany |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Vytvoření a Správa položek, které se dají zálohovat |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Vrátí seznam všech chráněných položek. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Vrátí všechny kontejnery, které patří k předplatnému |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Vrátí souhrny pro chráněné položky a chráněné servery služby Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Operace aktualizovat certifikát prostředku aktualizuje certifikát přihlašovacích údajů prostředku nebo úložišti. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá upozornění pro trezor služby Recovery services. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Operace získat úložiště získá objekt, představuje prostředek Azure typu "úložiště. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operaci získat výsledky operace je možné získat stav operace a výsledku asynchronně odeslané operace. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnery, operace se dá použít k získání kontejnerů zaregistrovaných u. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Operaci zaregistrovat kontejner služby lze použít k registraci kontejneru u služeb zotavení. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Ověření operace na chráněné položky |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Vrátí operace zálohování trezoru služby stavu pro obnovení. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Získáte stav operace zásad. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Vytvoří registrovaný kontejner. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Provést dotaz pro úlohy uvnitř kontejneru |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Vrátí všechny servery správy zálohování zaregistrované s trezorem. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Vytvořit záložní záměr ochrany |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Získat záložní záměr ochrany |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Získat všechny kontejnery s podporou ochrany |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Získat všechny položky v kontejneru |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Kontrola stavu zálohování pro trezory služby Recovery Services |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Ověření funkcí |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Vyřeší upozornění. |
> | Microsoft.RecoveryServices/operations/read | Operace vrátí seznam operací pro poskytovatele prostředků. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Získá stav operace pro danou operaci |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Seznam všech záloh záměry ochrany |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="backup-reader"></a>Čtenář záloh
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může zobrazovat služby zálohování, ale nemůže provádět změny. |
> | **ID** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Vrátí stav operace |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Načte výsledky operace provedené na kontejneru ochrany. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Načte výsledky operace provedené na chráněných položkách. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Načte stav operace provedené na chráněných položkách. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Vrátí podrobnosti objektu chráněné položky. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Načíst body obnovení pro chráněné položky |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Vrátí všechny registrované kontejnery. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Vrátí výsledek operace úlohy. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Vrátí všechny objekty úloh. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportovat úlohy |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Vrátí výsledek operace zálohování trezoru Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Načte výsledky operace zásad. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Vrátí všechny zásady ochrany |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Vrátí seznam všech chráněných položek. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Vrátí všechny kontejnery, které patří k předplatnému |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Vrátí souhrny pro chráněné položky a chráněné servery služby Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá upozornění pro trezor služby Recovery services. |
> | Microsoft.RecoveryServices/Vaults/read | Operace získat úložiště získá objekt, představuje prostředek Azure typu "úložiště. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operaci získat výsledky operace je možné získat stav operace a výsledku asynchronně odeslané operace. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnery, operace se dá použít k získání kontejnerů zaregistrovaných u. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Trezor služby Services vrátí konfiguraci úložiště pro obnovení. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Trezor služby vrátí konfiguraci pro Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Vrátí operace zálohování trezoru služby stavu pro obnovení. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Získáte stav operace zásad. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Vrátí všechny servery správy zálohování zaregistrované s trezorem. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Získat záložní záměr ochrany |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Získat všechny položky v kontejneru |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Kontrola stavu zálohování pro trezory služby Recovery Services |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Vyřeší upozornění. |
> | Microsoft.RecoveryServices/operations/read | Operace vrátí seznam operací pro poskytovatele prostředků. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Získá stav operace pro danou operaci |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Seznam všech záloh záměry ochrany |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="billing-reader"></a>Čtenář fakturace
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Povolí přístup pro čtení fakturačních údajů. |
> | **ID** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Billing/*/read | Přečtěte si informace o fakturaci |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Seznam skupin pro správu pro ověřeného uživatele. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="biztalk-contributor"></a>Přispěvatel BizTalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu služeb BizTalk, ale ne přístup k nim. |
> | **ID** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.BizTalkServices/BizTalk/* | Vytvoření a Správa služby BizTalk services |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cdn-endpoint-contributor"></a>Přispěvatel koncového bodu CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může spravovat koncové body CDN, ale nemůže udělovat přístup jiným uživatelům. |
> | **ID** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cdn-endpoint-reader"></a>Čtenář koncového bodu CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může zobrazovat koncové body CDN, ale nemůže je měnit. |
> | **ID** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cdn-profile-contributor"></a>Přispěvatel profilu CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může spravovat profily CDN a jejich koncové body, ale nemůže udělovat přístup jiným uživatelům. |
> | **ID** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cdn-profile-reader"></a>Čtenář profilu CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může zobrazovat profily CDN a jejich koncové body, ale nemůže je měnit. |
> | **ID** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="classic-network-contributor"></a>Přispěvatel klasických sítí
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu klasických sítí, ale ne přístup k nim. |
> | **ID** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.ClassicNetwork/* | Vytváření a správě klasických sítí |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="classic-storage-account-contributor"></a>Přispěvatel klasických účtů úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu klasických účtů úložiště, ale ne přístup k nim. |
> | **ID** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.ClassicStorage/storageAccounts/* | Vytvoření a Správa účtů úložiště |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>Role služby Operátor klíčů klasických účtů úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Operátoři klíčů klasických účtů úložiště můžou vypisovat a znovu generovat klíče pro klasické účty úložiště. |
> | **ID** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Akce** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Umožňuje vypsat seznam přístupových klíčů pro účty úložiště. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Umožňuje znovu vytvořit existující přístupové klíče pro účet úložiště. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="classic-virtual-machine-contributor"></a>Přispěvatel klasických virtuálních počítačů
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu klasických virtuálních počítačů, ale ne přístup k nim ani k virtuální síti nebo účtu úložiště, ke kterým jsou připojené. |
> | **ID** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.ClassicCompute/domainNames/* | Vytvoření a správa názvů domén klasické výpočetní prostředky |
> | Microsoft.ClassicCompute/virtualMachines/* | Vytvoření a správa virtuálních počítačů |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Odkaz na rezervovanou IP adresu |
> | Microsoft.ClassicNetwork/reservedIps/read | Umožňuje načíst vyhrazené IP adresy. |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Připojí se k virtuální síti. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Umožňuje načíst virtuální síť. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Vrátí disk účtu úložiště. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Vrátí image účtu storage. (Zastaralé. Použití "Microsoft.ClassicStorage/storageAccounts/vmImages.) |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Umožňuje vypsat seznam přístupových klíčů pro účty úložiště. |
> | Microsoft.ClassicStorage/storageAccounts/read | Umožňuje načíst účet úložiště se zadaným účtem. |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cognitive-services-contributor"></a>Přispěvatel Cognitive Services
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje vytvořit, přečíst, aktualizovat, odstranit a spravovat klíče služeb Cognitive Services. |
> | **ID** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Získá funkce předplatného. |
> | Microsoft.Features/providers/features/read | Získá funkci předplatného v daném zprostředkovateli prostředků. |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Insights/diagnosticSettings/* | Vytvoří, aktualizuje nebo načte nastavení diagnostiky pro Analysis Server |
> | Microsoft.Insights/logDefinitions/read | Číst definice protokolů |
> | Microsoft.Insights/metricdefinitions/read | Číst definice metrik |
> | Microsoft.Insights/metrics/read | Čtení metrik |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/deployments/operations/read | Načte nebo vypíše operace nasazení. |
> | Microsoft.Resources/subscriptions/operationresults/read | Načte výsledky operací s předplatným. |
> | Microsoft.Resources/subscriptions/read | Získá výpis předplatných. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cognitive-services-data-reader-preview"></a>Čtenář dat Cognitive Services (Preview)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožní vám čtení dat Cognitive Services. |
> | **ID** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Akce** |  |
> | *None* |  |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cognitive-services-user"></a>Uživatel Cognitive Services
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje přečíst a vypsat klíče služeb Cognitive Services. |
> | **ID** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Akce** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Zobrazit seznam klíčů |
> | Microsoft.Insights/alertRules/read | Přečte klasické upozornění na metriku. |
> | Microsoft.Insights/diagnosticSettings/read | Přečte nastavení diagnostiky prostředků. |
> | Microsoft.Insights/logDefinitions/read | Číst definice protokolů |
> | Microsoft.Insights/metricdefinitions/read | Číst definice metrik |
> | Microsoft.Insights/metrics/read | Čtení metrik |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/operations/read | Načte nebo vypíše operace nasazení. |
> | Microsoft.Resources/subscriptions/operationresults/read | Načte výsledky operací s předplatným. |
> | Microsoft.Resources/subscriptions/read | Získá výpis předplatných. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cosmos-db-account-reader-role"></a>Role čtenáře účtu Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může číst data účtu služby Azure Cosmos DB. Zobrazit [Přispěvatel účtů DocumentDB](#documentdb-account-contributor) ke správě účtů službu Azure Cosmos DB. |
> | **ID** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí může číst oprávnění udělená pro jednotlivé uživatele |
> | Microsoft.DocumentDB/*/read | Číst všechny kolekce |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Čte databázi, klíče účtu jen pro čtení. |
> | Microsoft.Insights/MetricDefinitions/read | Číst definice metrik |
> | Microsoft.Insights/Metrics/read | Čtení metrik |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může odesílat požadavky na obnovení databáze Cosmos DB nebo kontejneru pro určitý účet. |
> | **ID** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Akce** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Odeslat žádost o konfiguraci zálohování |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Odeslat žádost o obnovení |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cost-management-contributor"></a>Přispěvatel Cost Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může zobrazovat náklady a spravovat jejich konfiguraci (např. rozpočty, exporty). |
> | **ID** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Akce** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read | Zobrazí seznam dostupných fakturační období |
> | Microsoft.Resources/subscriptions/read | Získá výpis předplatných. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="cost-management-reader"></a>Čtenář Cost Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může zobrazovat data a konfiguraci nákladů (např. rozpočty, exporty). |
> | **ID** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Akce** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read | Zobrazí seznam dostupných fakturační období |
> | Microsoft.Resources/subscriptions/read | Získá výpis předplatných. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="data-box-contributor"></a>Přispěvatel Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat vše ve službě Data Box Service kromě udělení přístupu jiným uživatelům. |
> | **ID** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | Microsoft.Databox/* |  |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="data-box-reader"></a>Čtenář Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat službu Data Box Service kromě vytváření objednávky nebo úpravy podrobností o objednávce a udělení přístupu jiným uživatelům. |
> | **ID** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Vypíše nešifrované přihlašovací údaje související s objednávkou. |
> | Microsoft.Databox/locations/availableSkus/action | Tato metoda vrací seznam dostupných skladových položek. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="data-factory-contributor"></a>Přispěvatel Data Factory
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje vytvářet a spravovat datové továrny i podřízené prostředky v nich. |
> | **ID** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení role |
> | Microsoft.DataFactory/dataFactories/* | Vytvářejte a spravujte datové továrny a podřízené prostředky v nich. |
> | Microsoft.DataFactory/factories/* | Vytvářejte a spravujte datové továrny a podřízené prostředky v nich. |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="data-lake-analytics-developer"></a>Vývojář Data Lake Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje odesílat, monitorovat a spravovat vlastní úlohy, ale neumožňuje vytvářet ani odstraňovat účty Data Lake Analytics. |
> | **ID** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Odstranění účtu DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Udělení oprávnění pro zrušení úlohy od jiných uživatelů. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Vytvořit nebo aktualizovat účet DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Vytvořit nebo aktualizovat propojený účet DataLakeStore DataLakeAnalytics účtu. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Zrušení propojení účtu DataLakeStore z DataLakeAnalytics účtu. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Vytvořit nebo aktualizovat DataLakeAnalytics účtu propojeného účtu úložiště. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Zrušit propojení účtu úložiště z účtu DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Vytvořit nebo aktualizovat pravidla brány firewall. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Odstraňte pravidlo brány firewall. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Vytvořit nebo aktualizujete zásady pro výpočetní prostředky. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Odstranění zásad pro výpočetní prostředky. |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="data-purger"></a>Nástroj pro vyprázdnění dat
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může vyprázdnit analytická data. |
> | **ID** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Akce** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Vyprázdní data z Application Insights. |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Odstranit zadaná data z pracovního prostoru |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="devtest-labs-user"></a>Uživatel služby DevTest Labs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spouštět, restartovat a vypínat virtuální počítače v Azure DevTest Labs a připojovat se k nim. |
> | **ID** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení role |
> | Microsoft.Compute/availabilitySets/read | Umožňuje získat vlastnosti sady dostupnosti. |
> | Microsoft.Compute/virtualMachines/*/read | Čtení vlastností virtuálního počítače (velikosti virtuálních počítačů, stav modulu runtime, rozšíření virtuálních počítačů, atd.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Umožňuje vypnout virtuální počítač a uvolnit výpočetní prostředky. |
> | Microsoft.Compute/virtualMachines/read | Umožňuje získat vlastnosti virtuálního počítače. |
> | Microsoft.Compute/virtualMachines/restart/action | Umožňuje restartovat virtuální počítač. |
> | Microsoft.Compute/virtualMachines/start/action | Umožňuje spustit virtuální počítač. |
> | Microsoft.DevTestLab/*/read | Číst vlastnosti služby testovacího prostředí |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Deklarace identity náhodný nárokovatelný virtuální počítač v testovacím prostředí. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Vytvoření virtuálních počítačů v testovacím prostředí. |
> | Microsoft.DevTestLab/labs/formulas/delete | Odstraňte vzorce. |
> | Microsoft.DevTestLab/labs/formulas/read | Další vzorce. |
> | Microsoft.DevTestLab/labs/formulas/write | Přidat nebo upravit vzorce. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Vyhodnotí zásady testovacího prostředí. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Převzít vlastnictví existujícího virtuálního počítače |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | Obsahuje příslušné spustit/zastavit plány. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Získá řetězec, který představuje obsah souboru RDP pro virtuální počítač |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Spojí pro fond adres back-endu nástroje pro vyrovnávání zatížení. Výstražné. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Připojí pravidla příchozích pravidel nat služby load balancer. Výstražné. |
> | Microsoft.Network/networkInterfaces/*/read | Čtení vlastností síťového rozhraní (třeba všechny nástroje pro vyrovnávání zatížení, které se síťové rozhraní je součástí) |
> | Microsoft.Network/networkInterfaces/join/action | Virtuální počítač připojí k síťovému rozhraní. Výstražné. |
> | Microsoft.Network/networkInterfaces/read | Získá definici rozhraní sítě.  |
> | Microsoft.Network/networkInterfaces/write | Vytvoří síťové rozhraní nebo aktualizuje existující síťové rozhraní.  |
> | Microsoft.Network/publicIPAddresses/*/read | Umožňuje číst vlastnosti veřejné IP adresy |
> | Microsoft.Network/publicIPAddresses/join/action | Spojí veřejnou ip adresu. Výstražné. |
> | Microsoft.Network/publicIPAddresses/read | Získá definici veřejnou ip adresu. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Připojí virtuální sítě. Výstražné. |
> | Microsoft.Resources/deployments/operations/read | Načte nebo vypíše operace nasazení. |
> | Microsoft.Resources/deployments/read | Načte nebo vypíše nasazení. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | **notActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Umožňuje zobrazit seznam dostupných velikostí, na které je možné virtuální počítač aktualizovat. |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="dns-zone-contributor"></a>Přispěvatel zóny DNS
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat zóny DNS a sady záznamů v Azure DNS, ale neumožňuje řídit, kdo k nim má přístup. |
> | **ID** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.Network/dnsZones/* | Vytvářet a spravovat záznamy a zóny DNS |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="documentdb-account-contributor"></a>Přispěvatel účtů DocumentDB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Můžete spravovat účty služby Azure Cosmos DB. Azure Cosmos DB je dříve DocumentDB. |
> | **ID** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení role |
> | Microsoft.DocumentDb/databaseAccounts/* | Vytváření a správě účtů službu Azure Cosmos DB |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>Přispěvatel EventSubscription Event Gridu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat operace předplatných událostí Event Gridu. |
> | **ID** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Seznam předplatných globální události podle typu tématu |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Seznam předplatných místní události |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Seznam odběrů událostí regionální topictype |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="eventgrid-eventsubscription-reader"></a>Čtenář EventSubscription Event Gridu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje číst předplatná událostí Event Gridu. |
> | **ID** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.EventGrid/eventSubscriptions/read | Přečtěte si eventSubscription |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Seznam předplatných globální události podle typu tématu |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Seznam předplatných místní události |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Seznam odběrů událostí regionální topictype |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="hdinsight-domain-services-contributor"></a>Přispěvatel Doménových služeb HDInsight
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může číst, vytvářet, upravovat a odstraňovat operace, které souvisí s Domain Services a jsou nezbytné pro Balíček zabezpečení podniku HDInsight. |
> | **ID** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Akce** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="intelligent-systems-account-contributor"></a>Přispěvatel účtů Inteligentních systémů
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu účtů inteligentních systémů, ale ne přístup k nim. |
> | **ID** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení role |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.IntelligentSystems/accounts/* | Vytvoření a Správa účtů inteligentních systémů |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="key-vault-contributor"></a>Přispěvatel Key Vaultu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat trezory klíčů, ale ne přistupovat k nim. |
> | **ID** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Trvale odstraní trezor klíčů, který se nejdříve odstranil obnovitelně. |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="lab-creator"></a>Autor testovacího prostředí
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje vytvářet, spravovat a odstraňovat spravovaná testovací prostředí v účtech Azure Lab. |
> | **ID** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Vytvoření testovacího prostředí v účtu testovacího prostředí. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Získejte informace o místní dostupnosti pro každou kategorii velikost konfigurují v rámci účtu testovacího prostředí |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="log-analytics-contributor"></a>Přispěvatel Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Přispěvatel log Analytics může číst všechna data monitorování a upravit nastavení monitorování. Úprava nastavení monitorování, zahrnuje přidání rozšíření virtuálního počítače na virtuální počítače; čtení klíčů účtů úložiště, abyste mohli konfigurovat shromažďování protokolů ze služby Azure Storage; Vytvoření a konfigurace účtů služby Automation; přidávání řešení a konfigurace diagnostik Azure na všech prostředcích Azure. |
> | **ID** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Akce** |  |
> | * / čtení | Číst prostředky všech typů, s výjimkou tajných kódů. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Umožňuje vypsat seznam přístupových klíčů pro účty úložiště. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Insights/diagnosticSettings/* | Vytvoří, aktualizuje nebo načte nastavení diagnostiky pro Analysis Server |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="log-analytics-reader"></a>Čtenář Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Čtenář Log Analytics si může zobrazit a vyhledat všechna data monitorování a jeho nastavení. Může si zobrazit konfiguraci diagnostiky Azure na všech prostředcích Azure. |
> | **ID** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Akce** |  |
> | * / čtení | Číst prostředky všech typů, s výjimkou tajných kódů. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Hledejte pomocí nového modulu. |
> | Microsoft.OperationalInsights/workspaces/search/action | Spouští vyhledávací dotaz |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Načte sdílené klíče pracovního prostoru. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="logic-app-contributor"></a>Přispěvatel aplikace logiky
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu aplikace logiky, ale ne přístup k ní. |
> | **ID** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Umožňuje vypsat seznam přístupových klíčů pro účty úložiště. |
> | Microsoft.ClassicStorage/storageAccounts/read | Umožňuje načíst účet úložiště se zadaným účtem. |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Insights/diagnosticSettings/* | Vytvoří, aktualizuje nebo načte nastavení diagnostiky pro Analysis Server |
> | Microsoft.Insights/logdefinitions/* | Toto oprávnění je nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivit na portálu. Seznam kategorií protokolu v protokolu aktivit. |
> | Microsoft.Insights/metricDefinitions/* | Přečíst definice metrik (seznam dostupných typů metriky pro prostředek). |
> | Microsoft.Logic/* | Spravuje prostředky Logic Apps. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/operationresults/read | Načte výsledky operací s předplatným. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | Microsoft.Web/connectionGateways/* | Vytvořit a spravovat připojení brány. |
> | Microsoft.Web/connections/* | Vytvoření a spravuje připojení. |
> | Microsoft.Web/customApis/* | Vytváří a spravuje vlastní rozhraní API. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Získá vlastnosti na plán služby App Service |
> | Microsoft.Web/sites/functions/listSecrets/action | Seznam tajných kódů webové aplikace funkcí. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="logic-app-operator"></a>Operátor aplikace logiky
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje číst, povolovat a zakazovat aplikaci logiky. |
> | **ID** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Insights/alertRules/*/read | Pravidla upozornění přehledy pro čtení |
> | Microsoft.Insights/diagnosticSettings/*/read | Získá nastavení diagnostiky pro Logic Apps |
> | Microsoft.Insights/metricDefinitions/*/read | Získá dostupné metriky pro Logic Apps. |
> | Microsoft.Logic/*/read | Čte prostředky Logic Apps. |
> | Microsoft.Logic/workflows/disable/action | Zakáže pracovní postup. |
> | Microsoft.Logic/workflows/enable/action | Povolí pracovní postup. |
> | Microsoft.Logic/workflows/validate/action | Ověří pracovní postup. |
> | Microsoft.Resources/deployments/operations/read | Načte nebo vypíše operace nasazení. |
> | Microsoft.Resources/subscriptions/operationresults/read | Načte výsledky operací s předplatným. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | Microsoft.Web/connectionGateways/*/read | Číst připojení brány. |
> | Microsoft.Web/connections/*/read | Číst připojení. |
> | Microsoft.Web/customApis/*/read | Přečtěte si vlastní rozhraní API. |
> | Microsoft.Web/serverFarms/read | Získá vlastnosti na plán služby App Service |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="managed-application-operator-role"></a>Role operátora spravované aplikace
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje číst a provádět akce s prostředky spravované aplikace. |
> | **ID** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Akce** |  |
> | * / čtení | Číst prostředky všech typů, s výjimkou tajných kódů. |
> | Microsoft.Solutions/applications/read | Načte seznam aplikací. |
> | Microsoft.Solutions/*/action |  |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="managed-applications-reader"></a>Čtenář Managed Applications
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje číst prostředky ve spravované aplikace a žádat o přístup podle potřeby. |
> | **ID** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Akce** |  |
> | * / čtení | Číst prostředky všech typů, s výjimkou tajných kódů. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Solutions/jitRequests/* |  |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="managed-identity-contributor"></a>Přispěvatel spravovaných identit
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Vytváření, čtení, aktualizace a odstraňování identity přiřazené uživateli |
> | **ID** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Akce** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="managed-identity-operator"></a>Operátor spravovaných identit
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Čtení a přiřazování identity přiřazené uživateli |
> | **ID** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Akce** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="management-group-contributor"></a>Přispěvatel skupiny pro správu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Role Přispěvatel skupiny pro správu |
> | **ID** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Akce** |  |
> | Microsoft.Management/managementGroups/delete | Odstraňte skupinu pro správu. |
> | Microsoft.Management/managementGroups/read | Seznam skupin pro správu pro ověřeného uživatele. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Přidruží zrušit předplatné ze skupiny pro správu. |
> | Microsoft.Management/managementGroups/subscriptions/write | Přiřadí existující předplatné skupině pro správu. |
> | Microsoft.Management/managementGroups/write | Vytvořit nebo aktualizovat skupinu pro správu. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="management-group-reader"></a>Čtenář skupiny pro správu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Role Čtenář skupiny pro správu |
> | **ID** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Akce** |  |
> | Microsoft.Management/managementGroups/read | Seznam skupin pro správu pro ověřeného uživatele. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="monitoring-contributor"></a>Přispěvatel monitorování
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může číst všechna data monitorování a upravit nastavení monitorování. Viz také [role, oprávnění a zabezpečení prostřednictvím služby Azure Monitor vám začít](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **ID** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Akce** |  |
> | * / čtení | Číst prostředky všech typů, s výjimkou tajných kódů. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Čtení, zápisu a odstranění pravidla upozornění. |
> | Microsoft.Insights/components/* | Čtení, zápisu a odstranění komponenty Application Insights. |
> | Microsoft.Insights/DiagnosticSettings/* | Nastavení diagnostiky pro čtení/zápis/delete. |
> | Microsoft.Insights/eventtypes/* | Vypsat události protokolu aktivit (události správy) v rámci předplatného. Toto oprávnění se vztahuje na programování a portálu přístup k protokolům aktivit. |
> | Microsoft.Insights/LogDefinitions/* | Toto oprávnění je nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivit na portálu. Seznam kategorií protokolu v protokolu aktivit. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Přečíst definice metrik (seznam dostupných typů metriky pro prostředek). |
> | Microsoft.Insights/Metrics/* | Číst metriky pro prostředek. |
> | Microsoft.Insights/Register/Action | Zaregistruje poskytovatele platformy Microsoft Insights. |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Application Insights pro čtení, zápisu nebo odstranění webové testy. |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Čtení/zápis/delete log analytics řešení balíčky. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Čtení/zápis/delete log analytics uložená hledání. |
> | Microsoft.OperationalInsights/workspaces/search/action | Spouští vyhledávací dotaz |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Načte sdílené klíče pracovního prostoru. Tyto klíče se používají k připojení agentů Microsoft Operational Insights k pracovnímu prostoru. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Čtení/zápis/delete log analytics úložiště insight konfigurace. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="monitoring-metrics-publisher"></a>Vydavatel metrik monitorování
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje publikovat metriky proti prostředkům Azure. |
> | **ID** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Akce** |  |
> | Microsoft.Insights/Register/Action | Zaregistruje poskytovatele platformy Microsoft Insights. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | Zapíše metriky. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="monitoring-reader"></a>Čtenář monitorování
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může číst všechna data monitorování (metriky, protokoly atd.). Viz také [role, oprávnění a zabezpečení prostřednictvím služby Azure Monitor vám začít](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **ID** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Akce** |  |
> | * / čtení | Číst prostředky všech typů, s výjimkou tajných kódů. |
> | Microsoft.OperationalInsights/workspaces/search/action | Spouští vyhledávací dotaz |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="network-contributor"></a>Přispěvatel sítě
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu sítí, ale ne přístup k nim. |
> | **ID** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení role |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.Network/* | Vytvoření a Správa sítě |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="new-relic-apm-account-contributor"></a>Přispěvatel účtů New Relic APM
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu účtů a aplikací New Relic Application Performance Management, ale ne přístup k nim. |
> | **ID** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | NewRelic.APM/accounts/* |  |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="reader-and-data-access"></a>Čtenář a přístup k datům
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje zobrazit všechno, ale neumožní odstraňovat ani vytvářet účet úložiště nebo obsažený prostředek. To vám také umožní přístup pro čtení a zápis ke všem datům obsaženým v účtu úložiště prostřednictvím přístupu ke klíčům účtu úložiště. |
> | **ID** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="redis-cache-contributor"></a>Přispěvatel služby Redis Cache
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu mezipamětí Redis, ale ne přístup k nim. |
> | **ID** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení role |
> | Microsoft.Cache/redis/* | Vytvoření a Správa mezipaměti Redis |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="resource-policy-contributor-preview"></a>Přispěvatel zásad prostředků (Preview)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | (Preview) Obnovení uživatelé z EA s oprávněními vytvářet a upravovat zásady prostředků, vytvářet lístky podpory a číst prostředky a hierarchii |
> | **ID** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Akce** |  |
> | * / čtení | Číst prostředky všech typů, s výjimkou tajných kódů. |
> | Microsoft.Authorization/policyassignments/* | Vytvářet a spravovat přiřazení zásad |
> | Microsoft.Authorization/policydefinitions/* | Vytvářet a spravovat definice zásad |
> | Microsoft.Authorization/policysetdefinitions/* | Vytváření a správě sady zásad |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="scheduler-job-collections-contributor"></a>Přispěvatel kolekcí úloh Scheduleru
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu kolekcí úloh Scheduleru, ale ne přístup k nim. |
> | **ID** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení role |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Scheduler/jobcollections/* | Vytvoření a Správa kolekce úloh |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="search-service-contributor"></a>Přispěvatel služby Search
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu služeb Search, ale ne přístup k nim. |
> | **ID** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení role |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Search/searchServices/* | Vytvoření a Správa služby search |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="security-admin"></a>Správce zabezpečení
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Ve službě Security Center jenom: Můžete zobrazit zásady zabezpečení, zobrazení stavu zabezpečení, upravit zásady zabezpečení, zobrazení výstrah a doporučení, Zavřít upozornění a doporučení |
> | **ID** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Authorization/policyAssignments/* | Vytvářet a spravovat přiřazení zásad |
> | Microsoft.Authorization/policyDefinitions/* | Vytvářet a spravovat definice zásad |
> | Microsoft.Authorization/policySetDefinitions/* | Vytváření a správě sady zásad |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.Management/managementGroups/read | Seznam skupin pro správu pro ověřeného uživatele. |
> | Microsoft.operationalInsights/workspaces/*/read | Zobrazení data služby log analytics |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Security/* |  |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="security-manager-legacy"></a>Správce zabezpečení (starší verze)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Toto je starší verze role. Místo toho použijte Správce zabezpečení |
> | **ID** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.ClassicCompute/*/read | Přečtěte si informace o konfiguraci klasických virtuálních počítačů |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Zapsat konfiguraci pro klasické virtuální počítače |
> | Microsoft.ClassicNetwork/*/read | Přečtěte si informace o konfiguraci o klasickou síť |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Security/* | Vytvoření a Správa zabezpečení komponenty a zásady |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="security-reader"></a>Čtenář zabezpečení
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Ve službě Security Center jenom: Můžete zobrazit doporučení a výstrahy, zásady zabezpečení, zobrazit stavy zabezpečení, ale nemůže provádět změny zobrazení |
> | **ID** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.operationalInsights/workspaces/*/read | Zobrazení data služby log analytics |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Security/*/read | Čtení bezpečnostních komponent a zásad |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | Microsoft.Management/managementGroups/read | Seznam skupin pro správu pro ověřeného uživatele. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="site-recovery-contributor"></a>Přispěvatel Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat službu Site Recovery, ale ne vytvářet trezory a přiřazovat role. |
> | **ID** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp je vnitřní operace, které používá služba |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Operace aktualizovat certifikát prostředku aktualizuje certifikát přihlašovacích údajů prostředku nebo úložišti. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Vytvoření a správa rozšířené informace týkající se trezoru |
> | Microsoft.RecoveryServices/Vaults/read | Operace získat úložiště získá objekt, představuje prostředek Azure typu "úložiště. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Vytvoření a správa registrovaných identit |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Vytvořit nebo aktualizovat nastavení upozornění replikace |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Číst všechny události |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Vytvoření a Správa prostředků infrastruktury replikace |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Vytvoření a správa úlohy replikace |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Vytváření a Správa zásad replikace |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Vytvoření a Správa plánů obnovení |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Vytvoření a správě konfigurace úložiště z trezoru služby Recovery Services |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operaci Token trezoru můžete použít k získání tokenu trezoru pro trezor úrovně back-endové operace. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Přečtěte si upozornění pro trezor služby Recovery services |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="site-recovery-operator"></a>Operátor Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje provádět převzetí služeb při selhání a navrácení služeb po obnovení, ale žádné jiné operace správy služby Site Recovery. |
> | **ID** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp je vnitřní operace, které používá služba |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Microsoft.RecoveryServices/Vaults/read | Operace získat úložiště získá objekt, představuje prostředek Azure typu "úložiště. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operaci získat výsledky operace je možné získat stav operace a výsledku asynchronně odeslané operace. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnery, operace se dá použít k získání kontejnerů zaregistrovaných u. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Čtení nastavení výstrahy |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Číst všechny události |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Zkontroluje konzistenci prostředí infrastruktury. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Načíst všechny prostředky infrastruktury |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Znovu přidružit bránu |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Obnovit certifikát pro prostředky infrastruktury |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Přečtěte si žádné sítě |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Přečtěte si všechna mapování sítě |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Číst všechny kontejnery ochrany |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Načíst Chránitelné položky |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Použít bod obnovení |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Potvrzení převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Plánované převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Číst všechny chráněné položky |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Číst všechny body obnovení replikace |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Opravit replikaci |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Znovu zapnout ochranu položky |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testovací převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Vyčištění testovacího převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aktualizace služby Mobility |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Číst všechny mapování kontejnerů ochrany |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Číst všechny zprostředkovatele služeb zotavení |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Aktualizujte zprostředkovatele |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Číst všechny klasifikace úložiště |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Číst všechny mapování klasifikací úložišť |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Číst všechny vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Vytvoření a správa úlohy replikace |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Číst všechny zásady |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plán obnovení potvrzení převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plán obnovení plánovaného převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Číst všechny plány obnovení |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Zpětná replikace plánu obnovení |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plán obnovení převzetí služeb při selhání testu |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plán obnovení testovacího převzetí služeb při selhání čištění |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plán obnovení převzetí služeb při selhání |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Přečtěte si upozornění pro trezor služby Recovery services |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operaci Token trezoru můžete použít k získání tokenu trezoru pro trezor úrovně back-endové operace. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="site-recovery-reader"></a>Čtenář Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje zobrazovat stav služby Site Recovery, ale ne provádět jiné operace správy. |
> | **ID** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá upozornění pro trezor služby Recovery services. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | Operace získat úložiště získá objekt, představuje prostředek Azure typu "úložiště. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operaci získat výsledky operace je možné získat stav operace a výsledku asynchronně odeslané operace. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnery, operace se dá použít k získání kontejnerů zaregistrovaných u. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Čtení nastavení výstrahy |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Číst všechny události |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Načíst všechny prostředky infrastruktury |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Přečtěte si žádné sítě |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Přečtěte si všechna mapování sítě |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Číst všechny kontejnery ochrany |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Načíst Chránitelné položky |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Číst všechny chráněné položky |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Číst všechny body obnovení replikace |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Číst všechny mapování kontejnerů ochrany |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Číst všechny zprostředkovatele služeb zotavení |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Číst všechny klasifikace úložiště |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Číst všechny mapování klasifikací úložišť |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Číst všechny vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Číst všechny úlohy |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Číst všechny zásady |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Číst všechny plány obnovení |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operaci Token trezoru můžete použít k získání tokenu trezoru pro trezor úrovně back-endové operace. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="spatial-anchors-account-contributor"></a>Přispěvatel účtů prostorových kotvy
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožní vám správu prostorových kotev na vašem účtu, ale ne jejich odstranění. |
> | **ID** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Akce** |  |
> | *None* |  |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Prostorový ukotvení vytvářet |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Zjišťovat okolní prostorových kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Získá vlastnosti prostorových kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Vyhledejte prostorových kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Odeslat diagnostická data ke zlepšování kvality služeb Azure prostorových kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Aktualizovat vlastnosti prostorových kotvy |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="spatial-anchors-account-owner"></a>Vlastník účtu prostorových kotvy
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožní vám správu prostorových kotev na vašem účtu, včetně jejich odstranění. |
> | **ID** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Akce** |  |
> | *None* |  |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Prostorový ukotvení vytvářet |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Odstranění prostorových kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Zjišťovat okolní prostorových kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Získá vlastnosti prostorových kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Vyhledejte prostorových kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Odeslat diagnostická data ke zlepšování kvality služeb Azure prostorových kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Aktualizovat vlastnosti prostorových kotvy |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="spatial-anchors-account-reader"></a>Účet čtečky prostorových kotvy
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožní vám vyhledávání a čtení vlastností prostorových kotev na vašem účtu. |
> | **ID** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Akce** |  |
> | *None* |  |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Zjišťovat okolní prostorových kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Získá vlastnosti prostorových kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Vyhledejte prostorových kotvy |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Odeslat diagnostická data ke zlepšování kvality služeb Azure prostorových kotvy |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="sql-db-contributor"></a>Přispěvatel databází SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu databází SQL, ale ne přístup k nim. Kromě toho nemůžete spravovat jejich zásady související se zabezpečením nebo nadřazené servery SQL. |
> | **ID** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení role |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Vytvoření a Správa databází SQL Database |
> | Microsoft.Sql/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | Microsoft.Insights/metrics/read | Čtení metrik |
> | Microsoft.Insights/metricDefinitions/read | Číst definice metrik |
> | **notActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Upravit zásady auditu |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Upravit nastavení auditu |
> | Microsoft.Sql/servers/databases/auditRecords/read | Načtení záznamů auditu databáze objektů blob |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Upravit zásady připojení |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Upravit zásady maskování dat |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Upravit upozornění zásad zabezpečení |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Úprava metrik zabezpečení |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="sql-security-manager"></a>Správce zabezpečení SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje vám spravovat zásady vztahující se k zabezpečení serverů a databází SQL, ale ne přístup k nim. |
> | **ID** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Autorizace Microsoft pro čtení |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Prostředků, jako je například účet úložiště nebo databáze SQL se připojí k podsíti. Výstražné. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Vytvářejte a spravujte zásady auditování SQL serveru |
> | Microsoft.Sql/servers/auditingSettings/* | Vytvářet a spravovat nastavení auditování serveru SQL |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Načíst podrobnosti o objektu blob rozšířený server auditování zásady nakonfigurované na daném serveru |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Vytvářet a spravovat zásady auditování databáze SQL serveru |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Vytvářet a spravovat nastavení auditování databáze SQL serveru |
> | Microsoft.Sql/servers/databases/auditRecords/read | Záznamy auditu pro čtení |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Vytvoření a Správa zásad připojení databáze serveru SQL |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Vytvoření a Správa zásad maskování dat z databáze SQL serveru |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Načíst podrobnosti o rozšířený objekt blob auditu zásady, které jsou nakonfigurované na danou databázi |
> | Microsoft.Sql/servers/databases/read | Vrátí seznam databází nebo získá vlastnosti pro určenou databázi. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Získáte schéma databáze. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Získáte sloupce databáze. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Získejte tabulku databáze. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Vytvoření a Správa výstrah zásad zabezpečení serveru SQL server databáze |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Vytvoření a správa metrik zabezpečení databáze SQL serveru |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Vytvoření a Správa výstrah zásad zabezpečení SQL serveru |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="sql-server-contributor"></a>Přispěvatel SQL Serveru
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje vám spravovat servery a databáze SQL, ale ne přístup k nim ani jejich zásady vztahující se k zabezpečení. |
> | **ID** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Vytvoření a správa serverů SQL |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | Microsoft.Insights/metrics/read | Čtení metrik |
> | Microsoft.Insights/metricDefinitions/read | Číst definice metrik |
> | **notActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Upravit zásady auditování SQL serveru |
> | Microsoft.Sql/servers/auditingSettings/* | Upravit nastavení auditování serveru SQL |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Upravit zásady auditování databáze SQL serveru |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Upravit nastavení auditování databáze SQL serveru |
> | Microsoft.Sql/servers/databases/auditRecords/read | Záznamy auditu pro čtení |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Upravit zásady připojení databáze serveru SQL |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Upravit zásady maskování dat z databáze SQL serveru |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Upravit upozornění zásad zabezpečení serveru SQL server databáze |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Upravit metrik zabezpečení databáze SQL serveru |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Upravit upozornění zásad zabezpečení SQL serveru |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-account-contributor"></a>Přispěvatel účtů úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu účtů úložiště. Neposkytuje přístup k datům v účtu úložiště. |
> | **ID** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Číst všechny autorizace |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Insights/diagnosticSettings/* | Spravovat nastavení diagnostiky |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Prostředků, jako je například účet úložiště nebo databáze SQL se připojí k podsíti. Výstražné. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Storage/storageAccounts/* | Vytvoření a Správa účtů úložiště |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-account-key-operator-service-role"></a>Role služby Operátor klíčů účtů úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Povolení výpis a opětovné vygenerování přístupových klíčů účtu úložiště. |
> | **ID** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Znovu vygenerujete přístupové klíče pro zadaný účet úložiště. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-blob-data-contributor"></a>Přispěvatel dat v objektech blob služby Storage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Čtení, zápisu a odstranění služby Azure Storage kontejnerům a objektům BLOB. Další akce, které jsou požadovány pro dané datové operace, najdete v článku [oprávnění pro volání operace s daty objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **ID** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Odstraňte kontejner. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Vrátí kontejner nebo seznam kontejnerů. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Upravte vlastnosti nebo metadata kontejneru. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Odstraňte objekt blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Vrátí objekt blob nebo seznam objektů BLOB. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Zápis do objektu blob. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-blob-data-owner"></a>Vlastník dat v objektech blob služby Storage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Poskytuje úplný přístup ke kontejnery objektů blob v Azure Storage a data, včetně přiřazování řízení přístupu POSIX. Další akce, které jsou požadovány pro dané datové operace, najdete v článku [oprávnění pro volání operace s daty objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **ID** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Úplná oprávnění v kontejnerech.  |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Úplná oprávnění pro objekty BLOB. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-blob-data-reader"></a>Čtenář dat v objektech blob služby Storage 
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Čtení a zápis kontejnery služby Azure Storage a objekty BLOB. Další akce, které jsou požadovány pro dané datové operace, najdete v článku [oprávnění pro volání operace s daty objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **ID** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Vrátí kontejner nebo seznam kontejnerů. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Vrátí objekt blob nebo seznam objektů BLOB. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-queue-data-contributor"></a>Přispěvatel dat fronty služby Storage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Čtení, zápisu a odstranění fronty Azure Storage a jejich zprávy. Další akce, které jsou požadovány pro dané datové operace, najdete v článku [oprávnění pro volání operace s daty objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **ID** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Odstranění fronty. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Vrátí frontu nebo seznam front. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Úprava fronty metadat nebo vlastností. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Odstraníte jednu nebo více zpráv z fronty. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Náhled nebo načíst jednu nebo více zpráv z fronty. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Přidání zprávy do fronty. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-queue-data-message-processor"></a>Zpracovatel datových zpráv ve frontě služby Storage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Náhled, načíst a odstranit zprávy z fronty služby Azure Storage. Další akce, které jsou požadovány pro dané datové operace, najdete v článku [oprávnění pro volání operace s daty objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **ID** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Akce** |  |
> | *None* |  |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Náhled zprávy. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Získání a odstranění zprávy. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-queue-data-message-sender"></a>Odesílatel datových zpráv ve frontě služby Storage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Přidání zpráv do fronty služby Azure Storage. Další akce, které jsou požadovány pro dané datové operace, najdete v článku [oprávnění pro volání operace s daty objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **ID** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Akce** |  |
> | *None* |  |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Přidání zprávy do fronty. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="storage-queue-data-reader"></a>Čtenář datových zpráv ve frontě služby Storage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Čtení a zápis fronty Azure Storage a jejich zprávy. Další akce, které jsou požadovány pro dané datové operace, najdete v článku [oprávnění pro volání operace s daty objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **ID** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Vrátí frontu nebo seznam front. |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Náhled nebo načíst jednu nebo více zpráv z fronty. |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="support-request-contributor"></a>Přispěvatel žádostí o podporu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje vytvořit a spravovat žádosti o podporu. |
> | **ID** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="traffic-manager-contributor"></a>Přispěvatel Traffic Manageru
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat profily Traffic Manageru, ale neumožňuje řídit, kdo k nim má přístup. |
> | **ID** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Další role a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="user-access-administrator"></a>Správce přístupu uživatelů
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu přístupu uživatelů k prostředkům Azure. |
> | **ID** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Akce** |  |
> | * / čtení | Číst prostředky všech typů, s výjimkou tajných kódů. |
> | Microsoft.Authorization/* | Správa autorizací |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="virtual-machine-administrator-login"></a>Přihlášení správce virtuálního počítače
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Zobrazit virtuální počítače na portálu a přihlásit se jako správce |
> | **ID** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Akce** |  |
> | Microsoft.Network/publicIPAddresses/read | Získá definici veřejnou ip adresu. |
> | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Microsoft.Network/loadBalancers/read | Získá definice nástroje pro vyrovnávání zatížení |
> | Microsoft.Network/networkInterfaces/read | Získá definici rozhraní sítě.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Přihlášení k virtuálnímu počítači jako běžný uživatel |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Přihlášení k virtuálnímu počítači s oprávněními správce ve Windows nebo kořenovými oprávněními na Linuxu |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="virtual-machine-contributor"></a>Přispěvatel virtuálních počítačů
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu virtuálních počítačů, ale ne přístup k nim ani k virtuální síti nebo účtu úložiště, ke kterým jsou připojené. |
> | **ID** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.Compute/availabilitySets/* | Vytváření a správě výpočetních skupiny dostupnosti |
> | Microsoft.Compute/locations/* | Vytváření a správě výpočetních umístění |
> | Microsoft.Compute/virtualMachines/* | Vytvoření a správa virtuálních počítačů |
> | Microsoft.Compute/virtualMachineScaleSets/* | Vytvoření a správa škálovací sady virtuálních počítačů |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Spojí fond adres back-endu aplikace brány. Výstražné. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Spojí pro fond adres back-endu nástroje pro vyrovnávání zatížení. Výstražné. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Připojí nástroj pro vyrovnávání zatížení příchozí fond NAT. Výstražné. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Připojí pravidla příchozích pravidel nat služby load balancer. Výstražné. |
> | Microsoft.Network/loadBalancers/probes/join/action | Umožňuje použití sondy nástroje pro vyrovnávání zatížení. S touto vlastností sondu stavu oprávnění z virtuálního počítače škálovací sady můžete odkazovat, sondy. Výstražné. |
> | Microsoft.Network/loadBalancers/read | Získá definice nástroje pro vyrovnávání zatížení |
> | Microsoft.Network/locations/* | Vytváření a správě síťových umístění |
> | Microsoft.Network/networkInterfaces/* | Vytvoření a Správa síťových rozhraní |
> | Microsoft.Network/networkSecurityGroups/join/action | Spojí skupinu zabezpečení sítě. Výstražné. |
> | Microsoft.Network/networkSecurityGroups/read | Získá definici skupiny zabezpečení sítě |
> | Microsoft.Network/publicIPAddresses/join/action | Spojí veřejnou ip adresu. Výstražné. |
> | Microsoft.Network/publicIPAddresses/read | Získá definici veřejnou ip adresu. |
> | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Připojí virtuální sítě. Výstražné. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Vytvořit záložní záměr ochrany |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Vrátí podrobnosti objektu chráněné položky. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Vytvořit položku chráněnou zálohováním. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Vrátí všechny zásady ochrany |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Vytvoří zásady ochrany |
> | Microsoft.RecoveryServices/Vaults/read | Operace získat úložiště získá objekt, představuje prostředek Azure typu "úložiště. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/write | Operace Vytvořit trezor vytvoří prostředek Azure typu trezor. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="virtual-machine-user-login"></a>Přihlášení uživatele virtuálního počítače
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Zobrazit virtuální počítače na portálu a přihlásit se jako běžný uživatel |
> | **ID** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Akce** |  |
> | Microsoft.Network/publicIPAddresses/read | Získá definici veřejnou ip adresu. |
> | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Microsoft.Network/loadBalancers/read | Získá definice nástroje pro vyrovnávání zatížení |
> | Microsoft.Network/networkInterfaces/read | Získá definici rozhraní sítě.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Přihlášení k virtuálnímu počítači jako běžný uživatel |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="web-plan-contributor"></a>Přispěvatel webových plánů
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu webových plánů pro weby, ale ne přístup k nim. |
> | **ID** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | Microsoft.Web/serverFarms/* | Vytvoření a správa serverové farmy |
> | Microsoft.Web/hostingEnvironments/Join/Action | Spojí služby App Service Environment |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="website-contributor"></a>Přispěvatel webů
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu webů (ne webových plánů), ale ne přístup k nim. |
> | **ID** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.Insights/alertRules/* | Vytvoření a Správa pravidel výstrah Insights |
> | Microsoft.Insights/components/* | Vytváření a správě součásti Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytváření a správě nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvořit a spravovat lístky podpory |
> | Microsoft.Web/certificates/* | Vytvoření a správa certifikátů webu |
> | Microsoft.Web/listSitesAssignedToHostName/read | Získáte názvy lokalit, které jsou přiřazeny k názvu hostitele. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Získá vlastnosti na plán služby App Service |
> | Microsoft.Web/sites/* | Vytvářet a spravovat weby (vytvoření webu také vyžaduje oprávnění k zápisu pro přidružený plán služby App Service) |
> | **notActions** |  |
> | *None* |  |
> | **DataActions** |  |
> | *None* |  |
> | **NotDataActions** |  |
> | *None* |  |

## <a name="next-steps"></a>Další postup

- [Vlastní role pro prostředky Azure](custom-roles.md)
- [Správa přístupu k prostředkům Azure pomocí RBAC a webu Azure Portal](role-assignments-portal.md)
- [Oprávnění ve službě Azure Security Center](../security-center/security-center-permissions.md)
