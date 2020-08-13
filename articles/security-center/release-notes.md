---
title: Poznámky k verzi pro Azure Security Center
description: Popis toho, co je nového a co se změnilo v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2020
ms.author: memildin
ms.openlocfilehash: caad034c2cc3403bc88ee5f50d101702ec692c35
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192249"
---
# <a name="whats-new-in-azure-security-center"></a>Co je nového v Azure Security Center?

Zabezpečení Azure je v aktivním vývoji a průběžně přijímá vylepšení. Tato stránka vám poskytne informace o tom, jak se můžete seznámit s nejnovějším vývojem.

- Nové funkce
- Opravy chyb
- Zastaralé funkce

Tato stránka se pravidelně aktualizuje, takže ji můžete často znovu navštěvovat. Pokud hledáte položky starší než šest měsíců, najdete je v archivu, kde najdete novinky [v Azure Security Center](release-notes-archive.md).


## <a name="august-2020"></a>Srpen 2020

### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Posouzení ohrožení zabezpečení na virtuálních počítačích – konsolidovaná doporučení a zásady

Security Center zkontroluje vaše virtuální počítače a zjistí, jestli neběží na řešení pro posouzení ohrožení zabezpečení. Pokud se nenajde žádné řešení posouzení ohrožení zabezpečení, Security Center poskytne doporučení ke zjednodušení nasazení.

Po nalezení ohrožení zabezpečení Security Center poskytuje doporučení k tomu, abyste zjistili, co je potřeba k prozkoumání a nápravě v případě potřeby.

Abychom zajistili konzistentní prostředí pro všechny uživatele, bez ohledu na typ skeneru, který používají, jsme sjednoceni čtyřmi doporučeními v následujících dvou příkladech:

|Sjednocené doporučení|Popis změny|
|----|:----|
|**Na virtuálních počítačích by mělo být povolené řešení posouzení ohrožení zabezpečení.**|Nahrazuje následující dvě doporučení:<br> **•** Povolit integrované řešení posouzení ohrožení zabezpečení na virtuálních počítačích (s využitím Qualys (teď zastaralé) (zahrnuté do úrovně Standard)<br> **•** Řešení posouzení ohrožení zabezpečení by se mělo nainstalovat na vaše virtuální počítače (teď zastaralé) (úrovně Standard a Free).|
|**Ohrožení zabezpečení ve vašich virtuálních počítačích by se mělo opravit.**|Nahrazuje následující dvě doporučení:<br>**•** Oprava ohrožení zabezpečení zjištěných na virtuálních počítačích (s technologií Qualys) (nyní zastaralé)<br>**•** Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení (teď zastaralé).|
|||

Nyní použijete stejné doporučení k nasazení rozšíření posouzení ohrožení zabezpečení Security Center nebo soukromě Licencovanému řešení ("BYOL") od partnera, jako je Qualys nebo Rapid7.

V případě, že dojde k nalezení a nahlášení ohrožení zabezpečení Security Center, se vám zobrazí jedno doporučení bez ohledu na řešení posouzení ohrožení zabezpečení, které je identifikovalo.

#### <a name="updating-dependencies"></a>Aktualizace závislostí

Pokud máte skripty, dotazy nebo automatizace odkazující na předchozí doporučení nebo klíče zásad nebo názvy, použijte následující tabulky k aktualizaci odkazů:

##### <a name="before-august-2020"></a>Před srpna 2020

|Doporučení|Rozsah|
|----|:----|
|**Povolení integrovaného řešení posouzení ohrožení zabezpečení na virtuálních počítačích (používá se Qualys)**<br>Klíč: 550e890b-e652-4d22-8274-60b3bdb24c63|Integrované|
|**Náprava ohrožení zabezpečení zjištěná na vašich virtuálních počítačích (používá se Qualys)**<br>Klíč: 1195afff-c881-495E-9bc5-1486211ae03f|Integrované|
|**Řešení posouzení ohrožení zabezpečení by se mělo nainstalovat na virtuální počítače.**<br>Klíč: 01b1ed4c-B733-4FEE-b145-f23236e70cf3|BYOL|
|**Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.**<br>Klíč: 71992a2a-D168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Zásada|Rozsah|
|----|:----|
|**Na virtuálních počítačích by mělo být povolené posouzení ohrožení zabezpečení**<br>ID zásady: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Integrované|
|**Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.**<br>ID zásady: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>Od srpna 2020

|Doporučení|Rozsah|
|----|:----|
|**Na virtuálních počítačích by mělo být povolené řešení posouzení ohrožení zabezpečení.**<br>Klíč: ffff0522-1e88-47fc-8382-2a80ba848f5d|Předdefinované + BYOL|
|**Ohrožení zabezpečení ve vašich virtuálních počítačích by se mělo opravit.**<br>Klíč: 1195afff-c881-495E-9bc5-1486211ae03f|Předdefinované + BYOL|
||||

|Zásada|Rozsah|
|----|:----|
|[**Na virtuálních počítačích by mělo být povolené posouzení ohrožení zabezpečení**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>ID zásady: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Předdefinované + BYOL|
||||



## <a name="july-2020"></a>Červenec 2020

Aktualizace v červenci zahrnují:
- [Pro Image mimo Marketplace je teď k dispozici posouzení ohrožení zabezpečení pro virtuální počítače.](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Ochrana před hrozbami pro Azure Storage rozšířena tak, aby zahrnovala soubory Azure a Azure Data Lake Storage Gen2 (Preview)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Osm nových doporučení pro povolení funkcí ochrany před hrozbami](#eight-new-recommendations-to-enable-threat-protection-features)
- [Vylepšení zabezpečení kontejnerů – rychlejší prohledávání registru a aktualizace dokumentace](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Adaptivní řízení aplikací je aktualizované o nové doporučení a podporu zástupných znaků v pravidlech cesty.](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Šest zásad pro zabezpečení rozšířených dat SQL zastaralé](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>Pro Image mimo Marketplace je teď k dispozici posouzení ohrožení zabezpečení pro virtuální počítače.

Při nasazení řešení posouzení ohrožení zabezpečení Security Center před nasazením dříve provedla kontrolu ověření. Kontrola měla potvrdit SKLADOVOU položku Marketplace cílového virtuálního počítače. 

Z této aktualizace jste odebrali kontrolu a teď můžete nasadit nástroje pro posouzení ohrožení zabezpečení do vlastních počítačů se systémem Windows a Linux. Vlastní image jsou ty, které jste změnili ve výchozím nastavení webu Marketplace.

I když teď můžete nasadit integrované rozšíření posouzení ohrožení zabezpečení (s technologií Qualys) na mnoho dalších počítačů, podpora je k dispozici jenom v případě, že používáte operační systém, který je uvedený v [části nasazení integrovaného skeneru zabezpečení Qualys](built-in-vulnerability-assessment.md#deploying-the-qualys-built-in-vulnerability-scanner).

Přečtěte si další informace o [integrovaném skeneru ohrožení zabezpečení pro virtuální počítače (jenom na úrovni Standard)](built-in-vulnerability-assessment.md).

Přečtěte si další informace o používání vlastního řešení posouzení ohrožení zabezpečení v privátní licenci z Qualys nebo Rapid7 v [nasazení řešení pro kontrolu ohrožení zabezpečení partnera](partner-vulnerability-assessment.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Ochrana před hrozbami pro Azure Storage rozšířena tak, aby zahrnovala soubory Azure a Azure Data Lake Storage Gen2 (Preview)

Ochrana před hrozbami pro Azure Storage detekuje na vašich Azure Storage účtech potenciálně škodlivé aktivity. Security Center zobrazuje výstrahy, když detekuje pokusy o přístup k účtům úložiště nebo jejich zneužití. 

Data je možné chránit bez ohledu na to, jestli jsou uložená jako kontejnery objektů blob, sdílené složky nebo datová jezera. 

Přečtěte si další informace o [ochraně před hrozbami pro Azure Storage](threat-protection.md#threat-protection-for-azure-storage-).




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Osm nových doporučení pro povolení funkcí ochrany před hrozbami

Přidali jsme osm nových doporučení, která poskytují jednoduchý způsob, jak povolit funkce ochrany před internetovými útoky v Azure Security Center pro následující typy prostředků: virtuální počítače, App Service plány, Azure SQL Database servery, SQL servery na počítačích, účty Azure Storage, clustery služby Azure Kubernetes, Azure Container Registry Registry a Azure Key Vault trezory.

Nová doporučení:

- **V Azure SQL Databasech serverech by mělo být povolené rozšířené zabezpečení dat.**
- **Rozšířené zabezpečení dat by mělo být povoleno na serverech SQL na počítačích**
- **V plánech Azure App Service by měla být povolena rozšířená ochrana před internetovými útoky.**
- **V Azure Container Registry Registry by měla být povolena rozšířená ochrana před internetovými útoky.**
- **V trezorech Azure Key Vault by měla být povolena rozšířená ochrana před internetovými útoky.**
- **V clusterech služby Azure Kubernetes by měla být povolena rozšířená ochrana před internetovými útoky.**
- **Pro účty Azure Storage by měla být povolena rozšířená ochrana před internetovými útoky.**
- **Na virtuálních počítačích by měla být povolena rozšířená ochrana před internetovými útoky.**

Tato nová doporučení patří k řízení zabezpečení **Povolit pokročilou ochranu před internetovými útoky** .

Doporučení také zahrnují funkci rychlé opravy. 

> [!IMPORTANT]
> Oprava některé z těchto doporučení bude mít za následek poplatky za ochranu relevantních prostředků. Tyto poplatky začnou platit okamžitě, pokud máte související prostředky v aktuálním předplatném. Případně můžete v budoucnu přidat data později.
> 
> Například pokud ve vašem předplatném nemáte žádné clustery služby Azure Kubernetes a povolíte ochranu před hrozbami, neúčtují se žádné poplatky. Pokud v budoucnu přidáte cluster do stejného předplatného, bude automaticky chráněn a v tomto čase začnou platit poplatky.

Přečtěte si další informace o každé z těchto informací na [referenční stránce doporučení zabezpečení](recommendations-reference.md).

Přečtěte si další informace o [ochraně před hrozbami v Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Vylepšení zabezpečení kontejnerů – rychlejší prohledávání registru a aktualizace dokumentace

V rámci kontinuálního investování v doméně zabezpečení kontejneru máme na začátku sdílení významného zlepšení výkonu Security Center dynamické kontroly imagí kontejnerů uložených v Azure Container Registry. Kontroly se teď většinou dokončí přibližně po dvou minutách. V některých případech může trvat až 15 minut.

Abychom vylepšili přehlednost a pokyny týkající se schopností zabezpečení kontejneru Azure Security Center, Aktualizovali jsme také stránky dokumentace k zabezpečení kontejnerů. 

Další informace o zabezpečení kontejneru Security Center najdete v následujících článcích:

- [Přehled funkcí zabezpečení kontejneru Security Center](https://docs.microsoft.com/azure/security-center/container-security)
- [Podrobnosti o integraci s Azure Container Registry](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)
- [Podrobnosti o integraci se službou Azure Kubernetes](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration)
- [Jak kontrolovat registry a posílit svoje hostitele Docker](https://docs.microsoft.com/azure/security-center/monitor-container-security)
- [Výstrahy zabezpečení z funkcí ochrany před hrozbami pro clustery služby Azure Kubernetes](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)
- [Výstrahy zabezpečení z funkcí ochrany před hrozbami pro hostitele služeb Azure Kubernetes](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)
- [Doporučení zabezpečení pro kontejnery](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Adaptivní řízení aplikací je aktualizované o nové doporučení a podporu zástupných znaků v pravidlech cesty.

Funkce Adaptivní řízení aplikací obdržela dvě důležité aktualizace:

* Nové doporučení identifikuje potenciálně legitimní chování, které nebylo dříve povoleno. Nové doporučení, **pravidla povolených v zásadách adaptivního řízení aplikací by se měla aktualizovat**, vyzve vás k přidání nových pravidel do stávajících zásad, aby se snížil počet falešně pozitivních výstrah v rámci adaptivních upozornění na porušení řízení aplikací.

* Pravidla cesty teď podporují zástupné znaky. Z této aktualizace můžete nakonfigurovat pravidla povolených cest pomocí zástupných znaků. Existují dva podporované scénáře:

    * Použití zástupného znaku na konci cesty pro povolení všech spustitelných souborů v této složce a podsložkách

    * Použití zástupných znaků uprostřed cesty k povolení známého názvu spustitelného souboru s názvem složky (například osobní složky uživatele se známým spustitelným souborem, automaticky generované názvy složek atd.).


[Přečtěte si další informace o adaptivních řízeních aplikací](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Šest zásad pro zabezpečení rozšířených dat SQL zastaralé

Šest zásad souvisejících s pokročilým zabezpečením dat pro počítače SQL je zastaralé:

- V rozšířených nastaveních zabezpečení dat spravované instance SQL by se měly nastavit rozšířené typy ochrany před internetovými útoky na ALL.
- V rozšířených nastaveních zabezpečení dat SQL serveru by se měly nastavit rozšířené typy ochrany před internetovými útoky na ALL.
- Pokročilá nastavení zabezpečení dat pro spravovanou instanci SQL by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení.
- Pokročilá nastavení zabezpečení dat pro SQL Server by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení.
- E-mailová oznámení správcům a vlastníkům předplatného by se měla povolit v rozšířených nastaveních zabezpečení dat spravované instance SQL
- E-mailová oznámení správcům a vlastníkům předplatného by měla být povolená v nastavení SQL Server Advanced Data Security

Přečtěte si další informace o [předdefinovaných zásadách](security-center-policy-definitions.md).





## <a name="june-2020"></a>Červen 2020

Aktualizace v červnu zahrnují:
- [Rozhraní API pro hodnocení zabezpečení (Preview)](#secure-score-api-preview)
- [Pokročilé zabezpečení dat pro počítače s SQL (Azure, ostatní cloudy a Prem) (Preview)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Dvě nová doporučení pro nasazení agenta Log Analytics do počítačů ARC v Azure (Preview)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nové zásady pro vytváření nepřetržitého exportu a konfigurace automatizace pracovních postupů ve velkém měřítku](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nové doporučení pro použití skupin zabezpečení sítě k ochraně virtuálních počítačů, které nejsou přístupné pro Internet](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nové zásady pro povolení ochrany před hrozbami a pokročilé zabezpečení dat](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Rozhraní API pro hodnocení zabezpečení (Preview)

K vašemu skóre teď můžete přistupovat prostřednictvím [rozhraní API pro zabezpečené skóre](https://docs.microsoft.com/rest/api/securitycenter/securescores/) (aktuálně ve verzi Preview). Metody rozhraní API poskytují flexibilitu pro dotazování na data a vytváření vlastních mechanismů generování sestav v rámci vašich zabezpečených výsledků v průběhu času. Můžete například použít rozhraní API pro **zabezpečení skóre** k získání skóre pro konkrétní předplatné. Kromě toho můžete použít rozhraní API **ovládacích prvků bezpečného řízení skóre** k vypsání ovládacích prvků zabezpečení a aktuálního skóre vašich předplatných.

Příklady externích nástrojů, které jsou dostupné s rozhraním API pro zabezpečené skóre, najdete v části [bezpečné skóre naší komunity GitHubu](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Přečtěte si další informace o [zabezpečeném řízení hodnocení a zabezpečení v Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>Pokročilé zabezpečení dat pro počítače s SQL (Azure, ostatní cloudy a Prem) (Preview)

Azure Security Center pokročilé zabezpečení dat pro počítače SQL teď chrání SQL servery hostované v Azure, v jiných cloudových prostředích i v místních počítačích. Tím se rozšiřuje ochrana vašich serverů SQL Azure Native tak, aby plně podporovala hybridní prostředí.

Rozšířené zabezpečení dat poskytuje posouzení ohrožení zabezpečení a rozšířenou ochranu před internetovými útoky pro vaše počítače SQL bez ohledu na jejich umístění.

Instalace se skládá ze dvou kroků:

1. Nasazení agenta Log Analytics do hostitelského počítače SQL Server, aby se zajistilo připojení k účtu Azure.

1. Povolení volitelného balíčku na stránce ceny a nastavení Security Center.

Přečtěte si další informace o [pokročilém zabezpečení dat pro počítače s SQL](security-center-iaas-advanced-data.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Dvě nová doporučení pro nasazení agenta Log Analytics do počítačů ARC v Azure (Preview)

Přidali jsme dvě nová doporučení, která vám pomůžou nasadit [agenta Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) do vašich počítačů s obloukem Azure ARC a zajistit, aby byly chráněné pomocí Azure Security Center:

- **Agent Log Analytics musí být nainstalovaný na počítačích se systémem Windows Azure ARC (Preview).**
- **Agent Log Analytics musí být nainstalovaný na počítačích se systémem Linux (Preview).**

Tato nová doporučení se zobrazí ve stejných čtyřech kontrolních prvcích zabezpečení jako stávající (související) doporučení, **Agent monitorování by měl být nainstalovaný na vašich počítačích**: opravit konfigurace zabezpečení, použít adaptivní řízení aplikací, použít aktualizace systému a povolit službu Endpoint Protection.

Doporučení také obsahují možnosti rychlé opravy, které vám pomůžou urychlit proces nasazení. 

Další informace o těchto dvou nových doporučeních najdete v tabulce s [doporučeními pro výpočty a aplikace](recommendations-reference.md#recs-computeapp) .

Přečtěte si další informace o tom, jak Azure Security Center používá agenta v tématu [co je agent Log Analytics?](https://docs.microsoft.com/azure/security-center/faq-data-collection-agents#what-is-the-log-analytics-agent).

Přečtěte si další informace o [rozšířeních pro počítače s obloukem Azure ARC](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal).



### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Nové zásady pro vytváření nepřetržitého exportu a konfigurace automatizace pracovních postupů ve velkém měřítku

Automatizace procesů monitorování a reakce na incidenty vaší organizace může výrazně zlepšit dobu potřebnou k prošetření a zmírnění incidentů zabezpečení.

K nasazení konfigurací automatizace napříč vaší organizací použijte tyto integrované zásady Azure DeployIfdNotExist a vytvořte a nakonfigurujte postupy automatizace pro [průběžné exportování](continuous-export.md) a zpracování [pracovního postupu](workflow-automation.md) :

Zásady najdete v části zásady Azure:


|Cíl  |Zásada  |ID zásady  |
|---------|---------|---------|
|Průběžný export do centra událostí|[Nasazení exportu do centra událostí pro Azure Security Center výstrahy a doporučení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Průběžný export do pracovního prostoru Log Analytics|[Nasazení exportu do Log Analytics pracovního prostoru pro Azure Security Center výstrahy a doporučení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Automatizace pracovního postupu pro výstrahy zabezpečení|[Nasazení automatizace pracovních postupů pro Azure Security Center výstrahy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automatizace pracovního postupu pro doporučení zabezpečení|[Nasazení automatizace pracovních postupů pro Azure Security Center doporučení](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Začínáme se [šablonami automatizace pracovních postupů](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Přečtěte si další informace o tom, jak pomocí těchto dvou zásad exportu [průběžně exportovat Azure Security Center upozornění a doporučení prostřednictvím zásad](https://techcommunity.microsoft.com/t5/azure-security-center/continuously-export-azure-security-center-alerts-and/ba-p/1440745).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nové doporučení pro použití skupin zabezpečení sítě k ochraně virtuálních počítačů, které nejsou přístupné pro Internet

Kontrola zabezpečení "implementovat osvědčené postupy zabezpečení" teď obsahuje následující nové doporučení:

- **Virtuální počítače, které nejsou přístupné z Internetu, by měly být chráněné pomocí skupin zabezpečení sítě**

Stávající doporučení: **internetové virtuální počítače by měly být chráněné pomocí skupin zabezpečení sítě**, nerozlišují se mezi internetovými a neinternetovými virtuálními počítači. U obou se vygenerovalo doporučení s vysokou závažností, pokud se virtuální počítač nepřiřadil do skupiny zabezpečení sítě. Toto nové doporučení odděluje nepřístupné počítače k Internetu, aby se snížily falešně pozitivní a vyhnuli se zbytečným výstrahám s vysokou závažností.

Další informace najdete v tabulce [doporučení sítě](recommendations-reference.md#recs-network) .




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Nové zásady pro povolení ochrany před hrozbami a pokročilé zabezpečení dat

Nové zásady níže byly přidány do výchozí iniciativy ASC a jsou určeny pro pomoc s povolením ochrany před hrozbami nebo pokročilým zabezpečením dat pro příslušné typy prostředků.

Zásady najdete v části zásady Azure:


| Zásada                                                                                                                                                                                                                                                                | ID zásady                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [V Azure SQL Databasech serverech by mělo být povolené rozšířené zabezpečení dat.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [Rozšířené zabezpečení dat by mělo být povoleno na serverech SQL na počítačích](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Pro účty Azure Storage by měla být povolena rozšířená ochrana před internetovými útoky.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [V trezorech Azure Key Vault by měla být povolena rozšířená ochrana před internetovými útoky.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [V plánech Azure App Service by měla být povolena rozšířená ochrana před internetovými útoky.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [V Azure Container Registry Registry by měla být povolena rozšířená ochrana před internetovými útoky.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [V clusterech služby Azure Kubernetes by měla být povolena rozšířená ochrana před internetovými útoky.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [V Virtual Machines by měla být povolena rozšířená ochrana před internetovými útoky.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Přečtěte si další informace o [ochraně před hrozbami v Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection).





## <a name="may-2020"></a>Květen 2020

Aktualizace v nástroji mohou zahrnovat:
- [Pravidla pro potlačení výstrah (Preview)](#alert-suppression-rules-preview)
- [Posouzení ohrožení zabezpečení virtuálního počítače je teď všeobecně dostupné](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Změny přístupu k virtuálnímu počítači JIT (just-in-time)](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Vlastní doporučení se přesunula do samostatného řízení zabezpečení.](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Přepínač přidaný pro zobrazení doporučení v ovládacích prvcích nebo jako nestrukturovaný seznam](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Rozšířené řízení zabezpečení "implementovat osvědčené postupy zabezpečení"](#expanded-security-control-implement-security-best-practices)
- [Vlastní zásady s vlastními metadaty jsou teď všeobecně dostupné.](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Funkce analýzy výpisu stavu systému při migraci na detekci útoku typu soubor](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Pravidla pro potlačení výstrah (Preview)

Tato nová funkce (aktuálně ve verzi Preview) pomáhá snižovat únavu výstrah. Pomocí pravidel můžete automaticky skrýt výstrahy, které se neškodného nebo které souvisejí s běžnými aktivitami ve vaší organizaci. To vám umožní soustředit se na nejrelevantnější hrozby. 

Budou se vygenerovat i výstrahy, které odpovídají povoleným pravidlům potlačení, ale jejich stav se nastaví na zastaveno. Můžete zobrazit stav v Azure Portal, nebo máte přístup k výstrahám zabezpečení Security Center.

Pravidla potlačení definují kritéria, pro která se mají automaticky odkládat výstrahy. Obvykle byste pravidlo potlačení používali k těmto akcím:

- potlačit výstrahy, které jste identifikovali jako falešně pozitivní

- potlačit výstrahy, které se spouštějí příliš často, aby byly užitečné

Přečtěte si další informace o [potlačení výstrah z ochrany před hrozbami v Azure Security Center](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>Posouzení ohrožení zabezpečení virtuálního počítače je teď všeobecně dostupné

Úroveň Standard Security Center nyní zahrnuje integrované posouzení ohrožení zabezpečení pro virtuální počítače bez dalších poplatků. Toto rozšíření používá Qualys, ale oznamuje své závěry přímo zpátky do Security Center. Nepotřebujete licenci Qualys ani účet Qualys – všechno se bez problémů zpracovává v Security Center.

Nové řešení může průběžně kontrolovat vaše virtuální počítače a odhalit tak ohrožení zabezpečení a prezentovat zjištění v Security Center. 

K nasazení řešení použijte nové doporučení zabezpečení:

"Povolte integrované řešení posouzení ohrožení zabezpečení na virtuálních počítačích (s technologií Qualys)."

Přečtěte si další informace o [integrovaném posouzení ohrožení zabezpečení Security Center pro virtuální počítače](built-in-vulnerability-assessment.md).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Změny přístupu k virtuálnímu počítači JIT (just-in-time)

Security Center obsahuje volitelnou funkci pro ochranu portů pro správu vašich virtuálních počítačů. To zajišťuje ochranu před nejběžnějšími formami útoků hrubou silou.

Tato aktualizace přináší následující změny této funkce:

- Doporučení, které doporučuje povolit JIT na virtuálním počítači, bylo přejmenováno. Dřív je potřeba na virtuálních počítačích použít řízení přístupu k síti za běhu: porty pro správu virtuálních počítačů by se měly chránit pomocí řízení přístupu k síti za běhu.

- Doporučení se aktivuje jenom v případě, že existují otevřené porty pro správu.

Přečtěte si další informace o [funkci přístupu JIT](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Vlastní doporučení se přesunula do samostatného řízení zabezpečení.

Jeden ovládací prvek zabezpečení představený pomocí zvýšeného zabezpečeného skóre byl "implementovat osvědčené postupy zabezpečení". Všechna vlastní doporučení vytvořená pro vaše předplatná se automaticky umístila do tohoto ovládacího prvku. 

Aby bylo snazší najít vaše vlastní doporučení, přesunuli jsme je do vyhrazeného řízení zabezpečení "Custom doporučení". Tento ovládací prvek nemá žádný vliv na vaše zabezpečené skóre.

Přečtěte si další informace o ovládacích prvcích zabezpečení v [rozšířeném zabezpečeném skóre (Preview) v Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Přepínač přidaný pro zobrazení doporučení v ovládacích prvcích nebo jako nestrukturovaný seznam

Řízení zabezpečení jsou logické skupiny souvisejících doporučení zabezpečení. Odrážejí vaše zranitelné plochy pro útok. Ovládací prvek je sada doporučení zabezpečení s pokyny, které vám pomůžou implementovat tato doporučení.

Pokud chcete hned zjistit, jak dobře vaše organizace zabezpečuje jednotlivé plochy pro útok, Projděte si skóre pro jednotlivé ovládací prvky zabezpečení.

Ve výchozím nastavení se vaše doporučení zobrazují v ovládacích prvcích zabezpečení. Z této aktualizace je můžete zobrazit také jako seznam. Pokud je chcete zobrazit jako jednoduchý seznam seřazený podle stavu ovlivněných prostředků, použijte nový přepínač seskupit podle ovládacích prvků. Přepínač je nad seznamem na portálu.

Ovládací prvky zabezpečení – a tento přepínač jsou součástí nového prostředí pro zajištění skóre. Nezapomeňte nám poslat svůj názor přímo z portálu.

Přečtěte si další informace o ovládacích prvcích zabezpečení v [rozšířeném zabezpečeném skóre (Preview) v Azure Security Center](secure-score-security-controls.md).

![Přepínač "seskupit podle ovládací prvky" pro doporučení](\media\secure-score-security-controls\recommendations-group-by-toggle.gif)

### <a name="expanded-security-control-implement-security-best-practices"></a>Rozšířené řízení zabezpečení "implementovat osvědčené postupy zabezpečení" 

Jeden ovládací prvek zabezpečení představený pomocí rozšířeného zabezpečeného skóre je "implementace osvědčených postupů zabezpečení". Pokud je v tomto ovládacím prvku doporučení, nemá to vliv na zabezpečené skóre. 

V této aktualizaci se tři doporučení přesunuly mimo ovládací prvky, ve kterých byly původně umístěné, a do tohoto osvědčeného postupu. Provedli jsme tento krok, protože jsme zjistili, že riziko těchto tří doporučení je nižší, než bylo původně v myšlenkě.

Kromě toho byla do tohoto ovládacího prvku zavedena dvě nová doporučení a přidána.

Existují tři doporučení, která jste přesunuli:

- **Pro účty s oprávněním ke čtení v předplatném** (původně v ovládacím prvku povolit MFA) by měla být povolená MFA.
- **Externí účty s oprávněním ke čtení by se měly z předplatného odebrat** (původně v ovládacím prvku Správa přístupu a oprávnění).
- **Pro vaše předplatné by se měla určit maximálně 3 vlastníci** (v ovládacím prvku Správa přístupu a oprávnění).

K ovládacímu prvku jsou přidána dvě nová doporučení:

- **Rozšíření konfigurace hosta by se mělo nainstalovat na virtuální počítače s Windows (verze Preview)** – pomocí [Azure Policy konfigurace hosta](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) poskytuje viditelnost ve virtuálních počítačích s nastavením serveru a aplikací (jenom Windows).

- **Na vašich počítačích by mělo být povolené ochrany před zneužitím v programu Windows Defender (Preview)** – ochrana před zneužitím v programu Windows defender využívá Azure Policyho agenta konfigurace hostů. Ochrana před zneužitím má čtyři komponenty, které jsou navržené tak, aby se zařízení zamkly na širokou škálu útoků, a to v případě, že se v případě malwaru povoluje vyrovnávat rizika zabezpečení a požadavky na produktivitu (jenom Windows).

Další informace o systému Windows Defender zneužití Guard v [vytváření a nasazení zásad pro ochranu před zneužitím](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Přečtěte si další informace o ovládacích prvcích zabezpečení ve [zvýšeném zabezpečeném skóre (Preview)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Vlastní zásady s vlastními metadaty jsou teď všeobecně dostupné.

Vlastní zásady jsou teď součástí prostředí Security Center doporučení, bezpečného skóre a řídicího panelu standardů dodržování předpisů regulativních předpisů. Tato funkce je teď všeobecně dostupná a umožňuje rozšíření pokrytí posouzení zabezpečení vaší organizace v Security Center. 

Vytvořte vlastní iniciativu v zásadách Azure, přidejte do ní zásady a připojte je k Azure Security Center a vizualizujte je jako doporučení.

Nyní jsme také přidali možnost Upravit metadata vlastního doporučení. Mezi možnosti metadat patří závažnost, nápravný postup, informace o hrozbách a další.  

Přečtěte si další [informace o vylepšení vlastních doporučení s podrobnými informacemi](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Funkce analýzy výpisu stavu systému při migraci na detekci útoku typu soubor 

Do [detekce útoku typu soubor](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless)se integruje možnosti detekce CDA (Windows Crash Analysis). Analýza detekce útoků bez souborů přináší vylepšené verze těchto výstrah zabezpečení pro počítače s Windows: zjištěné injektáže kódu, zjištěná maskující modul Windows, zjištěná skrytý spustitelný kód a zjištěn podezřelý segment kódu.

Některé z výhod tohoto přechodu:

- **Proaktivní a včasné zjišťování malwaru** – přístup k diskovým diskům (CDA) se zabývá tím, že se nevyskytne chyba a následně se spustí analýza, aby bylo možné najít škodlivé Při použití detekce útoků pomocí souborů dojde k proaktivní identifikaci hrozeb v paměti, které jsou spuštěny. 

- **Obohacené výstrahy** – výstrahy zabezpečení z detekce útoků bez souborů zahrnují rozšíření, která nejsou k dispozici ze služby CDA, například informace o aktivních síťových připojeních. 

- **Agregace výstrah** – Pokud disk CDA zjistil více vzorů útoku v rámci jednoho výpisu stavu systému, aktivovalo se několik výstrah zabezpečení. Detekce útoků bez souborů kombinuje všechny identifikované vzory útoků ze stejného procesu do jediné výstrahy. tím se eliminuje nutnost korelace více výstrah.

- **Omezené požadavky na pracovní prostor Log Analytics** – výpisy stavu systému obsahující potenciálně citlivá data se už nebudou nahrávat do vašeho pracovního prostoru Log Analytics.



## <a name="april-2020"></a>Duben 2020

Aktualizace v dubnu zahrnují:
- [Dynamické balíčky pro dodržování předpisů jsou teď všeobecně dostupné.](#dynamic-compliance-packages-are-now-generally-available)
- [Doporučení identity teď jsou součástí Azure Security Center úrovně Free.](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Dynamické balíčky pro dodržování předpisů jsou teď všeobecně dostupné.

Řídicí panel dodržování předpisů Azure Security Center nyní zahrnuje **dynamické balíčky kompatibility** (teď všeobecně dostupné) ke sledování dalších odvětví a regulativních standardů.

Dynamické balíčky pro dodržování předpisů můžete přidat do svého předplatného nebo skupiny pro správu ze stránky Security Center zásady zabezpečení. Po zprovoznění standardního nebo srovnávacího testu se standardně zobrazí na řídicím panelu dodržování předpisů, kde jsou všechna přidružená data o dodržování předpisů namapovaná jako posouzení. Zobrazí se souhrnná zpráva o všech standardech, které jsou k dispozici ke stažení.

Nyní můžete přidat standardy jako:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **Oficiální Velká Británie a Velká Británie NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (nový)** (což je ucelenější reprezentace Azure CIS 1.1.0)

Kromě toho jsme nedávno přidali **srovnávací testy zabezpečení Azure**, které jsou určené pro zabezpečení a osvědčené postupy pro dodržování předpisů v závislosti na běžných architekturách dodržování předpisů, a pokyny specifické pro Azure. Další standardy budou na řídicím panelu podporovány, jakmile budou k dispozici.  
 
Přečtěte si další informace o [přizpůsobení sady standardů na řídicím panelu dodržování legislativních předpisů](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Doporučení identity teď jsou součástí Azure Security Center úrovně Free.

Doporučení zabezpečení pro identitu a přístup na úrovni Free Azure Security Center jsou teď všeobecně dostupná. Tato funkce je součástí snahy o zpřístupnění funkcí CSPM (Cloud Security stav Management) zdarma. Až do této chvíle byly tato doporučení dostupná jenom na cenové úrovni Standard.

Příklady doporučení pro identitu a přístup zahrnují:

- U účtů s oprávněním vlastníka v předplatném by mělo být povoleno vícefaktorové ověřování.
- "Pro vaše předplatné by se měla určit maximálně tři vlastníci."
- "Zastaralé účty by se měly odebrat z vašeho předplatného".

Pokud máte předplatné na cenové úrovni Free, jejich zabezpečená skóre budou ovlivněna touto změnou, protože nebyly nikdy posuzovány pro jejich identitu a zabezpečení přístupu.

Další informace o [identitě a doporučeních pro přístup](recommendations-reference.md#recs-identity).

Přečtěte si další informace o [identitě a přístupu k monitorování](security-center-identity-access.md).


## <a name="march-2020"></a>Březen 2020

Aktualizace v březnu zahrnují:
- [Automatizace pracovních postupů je teď všeobecně dostupná.](#workflow-automation-is-now-generally-available)
- [Integrace Azure Security Center s centrem pro správu Windows](#integration-of-azure-security-center-with-windows-admin-center)
- [Ochrana pro službu Azure Kubernetes](#protection-for-azure-kubernetes-service)
- [Vylepšené prostředí za běhu](#improved-just-in-time-experience)
- [Dvě bezpečnostní doporučení pro webové aplikace, které jsou zastaralé](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>Automatizace pracovních postupů je teď všeobecně dostupná.

Funkce automatizace pracovního postupu Azure Security Center je teď všeobecně dostupná. Použijte ji k automatické aktivaci Logic Apps výstrah zabezpečení a doporučení. Kromě toho jsou k dispozici ruční aktivační události pro výstrahy a všechna doporučení, která mají možnost Rychlá oprava k dispozici.

Každý program zabezpečení zahrnuje několik pracovních postupů pro reakci na incidenty. Tyto procesy mohou zahrnovat oznamování relevantních účastníků, spuštění procesu správy změn a uplatnění specifických kroků k nápravě. Odborníci na zabezpečení doporučují automatizovat tolik kroků těchto postupů, jako je to možné. Automatizace snižuje režijní náklady a může zvýšit zabezpečení tím, že zajistí, že kroky procesu budou provedeny rychle, konzistentně a podle vašich předdefinovaných požadavků.

Další informace o funkcích automatických a ručních Security Center pro spouštění pracovních postupů najdete v tématu [automatizace pracovních postupů](workflow-automation.md).

Přečtěte si další informace o [vytváření Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integrace Azure Security Center s centrem pro správu Windows

Je teď možné přesunout místní servery Windows z centra pro správu Windows přímo na Azure Security Center. Security Center pak se v jednom podokně skla zobrazí informace o zabezpečení pro všechny prostředky centra pro správu systému Windows, včetně místních serverů, virtuálních počítačů a dalších PaaS úloh.

Po přesunutí serveru z centra pro správu Windows na Azure Security Center budete moct:

- Zobrazení výstrah zabezpečení a doporučení v rozšíření Security Center centra pro správu systému Windows.
- Prohlédněte si stav zabezpečení a načtěte další podrobné informace o spravovaných serverech centra pro správu Windows v Security Center v rámci Azure Portal (nebo přes rozhraní API).

Přečtěte si další informace o [tom, jak integrovat Azure Security Center s centrem pro správu Windows](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Ochrana pro službu Azure Kubernetes

Azure Security Center rozšiřuje své funkce zabezpečení kontejnerů, aby chránily službu Azure Kubernetes Service (AKS).

Oblíbená Open Source platforma Kubernetes byla přijata, takže je teď v oboru standard pro orchestraci kontejnerů. Navzdory této rozšířené implementaci se stále ještě nepředpokládá, jak zabezpečit Kubernetes prostředí. Ochrana předních ploch v kontejnerových aplikacích vyžaduje, aby bylo zajištěno zabezpečené a nepřetržité monitorování infrastruktury pro potenciální hrozby.

Security Center obrany zahrnuje:

- **Zjišťování a viditelnost** – nepřetržité zjišťování spravovaných instancí AKS v rámci předplatných registrovaných pro Security Center.
- **Doporučení zabezpečení** – doporučení pro dodržování předpisů, která vám pomůžou dodržovat osvědčené postupy zabezpečení pro AKS. Tato doporučení jsou zahrnutá ve vašem zabezpečeném skóre, abyste se ujistili, že se zobrazují jako součást stav zabezpečení vaší organizace. Příklad doporučení týkající se AKS najdete v tématu řízení přístupu na základě role by mělo sloužit k omezení přístupu ke clusteru služby Kubernetes.
- **Ochrana před hrozbami** – prostřednictvím průběžné analýzy nasazení AKS vás Security Center vás upozorní na hrozby a škodlivou aktivitu zjištěnou na úrovni clusteru host a AKS.

Přečtěte si další informace o [integraci služeb Azure Kubernetes Services s Security Center](azure-kubernetes-service-integration.md).

Přečtěte si další informace o [funkcích zabezpečení kontejnerů v Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Vylepšené prostředí za běhu

Funkce, operace a uživatelské rozhraní pro nástroje Azure Security Center za běhu, které zabezpečují vaše porty pro správu, jsou vylepšené následujícím způsobem: 

- **Pole odůvodnění** – při žádosti o přístup k virtuálnímu počítači (VM) na stránce Azure Portal za běhu je k dispozici nové volitelné pole, ve kterém můžete zadat odůvodnění žádosti. Informace zadané do tohoto pole lze sledovat v protokolu aktivit. 
- **Automatické čištění redundantních pravidel JIT (just-in-time)** – při každé aktualizaci zásad JIT se automaticky spustí nástroj pro vyčištění, který kontroluje platnost celého RuleSet. Nástroj vyhledá neshody mezi pravidly v zásadách a pravidly v NSG. Pokud nástroj pro vyčištění najde neshodu, určí příčinu a v případě, že je to bezpečné, odstraní předdefinovaná pravidla, která už nepotřebujete. Čisticí nikdy neodstraní pravidla, která jste vytvořili. 

Přečtěte si další informace o [funkci přístupu JIT](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Dvě bezpečnostní doporučení pro webové aplikace, které jsou zastaralé

Nepoužívá se dvě doporučení zabezpečení související s webovými aplikacemi: 

- Pravidla pro webové aplikace v IaaS skupin zabezpečení sítě by měla být zpřísněná.
    (Související zásady: pravidla skupin zabezpečení sítě pro webové aplikace v IaaS by měla být Posílená.)

- Přístup k App Services by měl být omezený.
    (Související zásady: přístup k App Services by měl být omezený [Preview])

Tato doporučení se už nebudou zobrazovat v seznamu Security Centerch doporučení. Související zásady již nebudou zahrnuty v iniciativě s názvem "Security Center výchozí".

Přečtěte si další informace o [doporučeních zabezpečení](recommendations-reference.md).

