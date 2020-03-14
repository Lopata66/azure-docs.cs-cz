---
title: Kódy chyb REST API – Azure Machine Learning Studio (Classic) | Microsoft Docs
description: Tyto kódy mohou být vráceny operace webové služby Azure Machine Learning.
keywords: ''
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: 9e25f2fbc10eb07cc71f2a7bd34247c0191f61b9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217763"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Kódy chyb REST API Azure Machine Learning Studio (Classic)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
 
Následující chybové kódy by mohly být vráceny operací na webové službě Azure Machine Learning Studio (Classic).
 
## <a name="badargument-http-status-code-400"></a>BadArgument (stavový kód HTTP 400)
 
Neplatný argument uvedený.
 
Tato třída chyby znamená, že poskytuje někde argument byl neplatný. To může být přihlašovací údaje nebo umístění služby Azure storage na něco předaný k webové službě. Pokud chcete diagnostikovat, který konkrétní argument byl neplatný, podívejte se prosím do pole kód chyby v části Podrobnosti.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| BadParameterValue | Zadaná hodnota parametru nesplňuje podmínky pravidla parametru u parametru |
| BadSubscriptionId | Id, které slouží ke stanovení skóre pro předplatné není ta, která je k dispozici v prostředku |
| BadVersionCall | Během volání rozhraní API se předal neplatný parametr verze: {0}. Zaškrtněte na stránce nápovědy rozhraní API pro předávání správnou verzi a zkuste to znovu. |
| BatchJobInputsNotSpecified | Následující požadované vstupy nebyly zadány s požadavkem: {0}. Zkontrolujte, zda je zadán veškerá vstupní data a zkuste to znovu. |
| BatchJobInputsTooManySpecified | Žádost zadat více vstupů, než je definováno ve službě. Seznam přijatého vstupu (ů): {0}. Zkontrolujte, zda je správně zadána veškerá vstupní data a zkuste to znovu. |
| BlobNameTooLong | Cesta k úložišti objektů BLOB v Azure, která se poskytuje pro výstup diagnostiky, je moc dlouhá: {0}. Zkraťte cestu a zkuste to znovu. |
| BlobNotFound | Nelze získat přístup k poskytnutému objektu blob Azure {0}.  Chybová zpráva Azure: {1}. |
| ContainerIsEmpty | Nebyl poskytnut žádný název kontejneru úložiště Azure. Zadejte platný kontejner název a zkuste to znovu. |
| ContainerSegmentInvalid | Neplatný název kontejneru. Zadejte platný kontejner název a zkuste to znovu. |
| ContainerValidationFailed | Ověření kontejneru objektu BLOB se nezdařilo s touto chybou: {0}. |
| DataTypeNotSupported | Nepodporovaný datový typ, k dispozici. Zadejte platné datové typy a zkuste to znovu. |
| DuplicateInputInBatchCall | Dávkový požadavek je neplatný. Nelze zadat jednu i více vstup ve stejnou dobu. Odeberte jeden z těchto položek z požadavku a zkuste to znovu. |
| ExpiryTimeInThePast | Čas vypršení platnosti je uvedený v minulosti: {0}. Zadejte budoucí platnosti ve standardu UTC a zkuste to znovu. Bez vypršení platnosti, nastavte čas vypršení platnosti na hodnotu NULL. |
| IncompleteSettings | Nastavení diagnostiky nejsou úplné. |
| InputBlobRelativeLocationInvalid | K dispozici žádný název objektu blob úložiště Azure. Zadejte platný objekt blob název a zkuste to znovu. |
| InvalidBlob | Neplatná specifikace objektu BLOB pro objekt BLOB: {0}. Ověřte, že připojovací řetězec nebo relativní cestu nebo specifikace token SAS je správný a zkuste to znovu. |
| InvalidBlobConnectionString | Připojovací řetězec zadaný pro jeden z vstupních/výstupních objektů BLOB není platný: {0}. Opravit to a zkuste to znovu. |
| InvalidBlobExtension | Odkaz na objekt BLOB: {0} má neplatnou nebo chybějící příponu souboru. Podporované přípony souborů pro tento typ výstupu jsou: "{1}". |
| InvalidInputNames | V požadavku byly zadány neplatné názvy vstupu služby: {0}. Namapovat vstupní data do správné služby vstupů a zkuste to znovu. |
| InvalidOutputOverrideName | Neplatný název přepisu výstupu: {0}. Služba nemá uzlu výstup s tímto názvem. Předejte prosím správný název uzlu přepsat (platí rozlišování velikosti písmen). |
| InvalidQueryParameter | Neplatný parametr dotazu{0}. {1} |
| MissingInputBlobInformation | Chybí informace o objektu blob úložiště Azure. Zadejte platný připojovací řetězec a relativní cestu nebo identifikátor URI a zkuste to znovu. |
| MissingJobId | Zadané Id žádná úloha. Úlohy Id je vrácena, pokud úloha byla odeslána poprvé. Ověřte správnost Id úlohy a zkuste to znovu. |
| MissingKeys | K dispozici žádné klíče nebo jeden z primární nebo sekundární klíč není k dispozici. |
| MissingModelPackage | Žádné Id modelu balíčku nebo modelu balíček k dispozici. Zadejte platný model balíček Id nebo model balíček a zkuste to znovu. |
| MissingOutputOverrideSpecification | V požadavku chybí specifikace objektu BLOB pro přepsání výstupu {0}. Zadejte platný objekt blob umístění k požadavku, nebo odeberte výstup, pokud přepsání není umístění. |
| MissingRequestInput | Webová služba očekává vstup, ale nebyl poskytnut žádný vstup. Ujistěte se, že jsou k dispozici platné vstupy závislosti na publikovaných vstupní porty v modelu a zkuste to znovu. |
| MissingRequiredGlobalParameters | Všechny požadované parametry webové služby k dispozici. Ověřte správnost parametrů očekává u modulů a zkuste to znovu. |
| MissingRequiredOutputOverrides | Při volání koncového bodu šifrované služby je povinná a zajistěte tak předání výstup přepsání pro všechny služby výstupy. V tuto chvíli pro tyto výstupy chybí přepsání: {0} |
| MissingWebServiceGroupId | Zadané Id skupiny bez webových služeb. Zadejte Id platnou webovou službu skupiny a zkuste to znovu. |
| MissingWebServiceId | Zadané Id žádná webová služba. Zadejte platnou webovou službu Id a zkuste to znovu. |
| MissingWebServicePackage | Žádné webového balíčku služby k dispozici. Zadejte balíček nástroje platnou webovou službu a zkuste to znovu. |
| MissingWorkspaceId | Zadané Id žádný pracovní prostor. Zadejte platné Id pracovního prostoru a zkuste to znovu. |
| ModelConfigurationInvalid | Neplatný model konfigurace v balíčku modelu. Zkontrolujte konfiguraci modelu obsahuje definici koncových bodů výstupu, std chyba koncového bodu, a std výstupní koncový bod a zkuste to znovu. |
| ModelPackageIdInvalid | Neplatné ID balíčku modelu. Zkontrolujte, jestli je ID balíčku modelu správné, a zkuste to znovu. |
| RequestBodyInvalid | Žádný text žádosti k dispozici nebo Chyba při deserializaci textu požadavku. |
| RequestIsEmpty | Není k dispozici žádný požadavek. Zadejte platnou žádost a zkuste to znovu. |
| UnexpectedParameter | Neočekávané parametry zadané. Ověřte všechny názvy parametrů jsou zadány správně, pouze očekávané parametry jsou předány a zkuste to znovu. |
| Neznámé chyby | Neznámá chyba |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Nelze změnit požadavky souběžných požadavků pro {0} webovou službu. |
| WebServiceIdInvalid | Zadané id neplatný webové služby. Id webové služby musí být platný identifikátor guid. |
| WebServiceTooManyConcurrentRequestRequirement | U souběžných požadavků na požadavek nelze nastavit více než {0}. |
| WebServiceTypeInvalid | Zadaný typ neplatný webové služby. Ověřte že správnost tohoto typu platnou webovou službu a zkuste to znovu. Platné typy webové služby: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (stavový kód HTTP 400)
 
Zadaný argument neplatný uživatel.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| InputMismatchError | Vstupní data neodpovídá schématu vstupního portu. |
| InputParseError | Analýza vstupu vektoru se nezdařilo.  Ověřte, že vstupní Vektor nemá správný počet sloupců a datové typy.  Další podrobnosti: {0}. |
| MissingRequiredGlobalParameters | Webová služba očekává parametry nebyly nalezeny. Ověřte, že všechny požadované parametry očekává webové služby jsou správné a zkuste to znovu. |
| UnexpectedParameter | Ověřte očekávání webová služba pouze požadované parametry jsou předány a zkuste to znovu. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (stavový kód HTTP 400)
 
Požadavek je neplatný v aktuálním kontextu.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| CannotStartJob | Úlohu nelze spustit, protože je ve stavu {0}. |
| IncompatibleModel | Model je kompatibilní s verzí požadavku. Žádost o verze podporuje pouze modely výstup jednoho objektu datatable. |
| MultipleInputsNotAllowed | Model není povoleno více vstupů. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (stavový kód HTTP 400)
 
Spouštění modulu došlo k chybě interní knihovny.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (stavový kód HTTP 400)
 
Spouštění modulu došlo k chybě.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (stavový kód HTTP 400)
 
Neplatný webový balíček služby. Zkontrolujte správnost balíčku webové služby k dispozici a zkuste to znovu.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| FormatError | Balíček webové služby je poškozený. Podrobnosti: {0} |
| RuntimesError | Graf balíček webové služby je neplatný. Podrobnosti: {0} |
| ValidationError | Graf balíček webové služby je neplatný. Podrobnosti: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Neoprávněné (stavový kód HTTP 401)
 
Požadavek není autorizovaný k přístupu k prostředku.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| AdminRequestUnauthorized | Neautorizováno |
| ManagementRequestUnauthorized | Neautorizováno |
| ScoreRequestUnauthorized | Zadané neplatné přihlašovací údaje. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (stavový kód HTTP 404)
 
Prostředek se nenašel.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| ModelPackageNotFound | Model balíček se nenašel. Ověřte správnost Id balíčku modelu a zkuste to znovu. |
| WebServiceIdNotFoundInWorkspace | Webové služby v tomto pracovním prostoru nenašla. Došlo k neshodě mezi webServiceId a ID pracovního prostoru. Ověřte, že webová služba k dispozici je součástí pracovního prostoru a zkuste to znovu. |
| WebServiceNotFound | Webová služba nebyla nalezena. Ověřte správnost Id webové služby a zkuste to znovu. |
| WorkspaceNotFound | Pracovní prostor nebyl nalezen. Ověřte správnost Id pracovního prostoru a zkuste to znovu. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (stavový kód HTTP 408)
 
Operaci nešlo dokončit v povoleném čase.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| RequestCanceled | Požadavek byl zrušen klientem. |
| ScoreRequestTimeout | Žádost o spuštění, vypršení časového limitu. |
 
## <a name="conflict-http-status-code-409"></a>Konflikt (kód stavu protokolu HTTP 409)
 
Prostředek už existuje.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Název parametru neplatný výstup. Zkuste použít editor modulu metadat přejmenování sloupců a zkuste to znovu. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (stavový kód HTTP 413)
 
Model překročil kvótu paměti přiřazené.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| OutOfMemoryLimit | Model spotřebovávat více paměti, než byla přidělena pro něj. Maximální povolená paměť pro model je {0} MB. Zkontrolujte prosím váš model pro problémy. |
 
## <a name="internalerror-http-status-code-500"></a>Vnitřní chyba (kód stavu HTTP 500)
 
Provádění došlo k vnitřní chybě.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | Kontejnerový proces selhal s chybou systému |
| ContainerProcessTerminatedWithUnknownError | Kontejnerový proces, došlo k chybě kvůli neznámé chybě |
| ContainerValidationFailed | Ověření kontejneru objektu BLOB se nezdařilo s touto chybou: {0}. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Nebyly zadány žádné argumenty. Ověřte, že platné argumenty jsou předány a zkuste to znovu. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Port ID ={0} obsahuje nepodporovaný datový typ: {1}. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Nepovedlo se vygenerovat Swagger, podrobnosti: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| Neznámé chyby |  |
| UnknownJobStatusCode | Neznámý kód stavu úlohy {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, podrobnosti: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (stavový kód HTTP 500)
 
Provádění došlo k vnitřní chybě. Nedostatek paměti systému. Zkuste to prosím znovu.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (stavový kód HTTP 500)
 
Balíček modelu je neplatný. Zkontrolujte správnost balíčku modelu k dispozici a zkuste to znovu.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (stavový kód HTTP 500)
 
Neplatný webový balíček služby. Ověřte správnost webový balíček, který je k dispozici a zkuste to znovu.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| ModuleError | Graf balíček webové služby je neplatný. Podrobnosti: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (stavový kód HTTP 503)
 
Požadavek nelze provést jako kontejnery jsou během inicializace.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (stavový kód HTTP 503)
 
Služba je dočasně nedostupná.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| NoMoreResources | Žádné prostředky k dispozici pro požadavek. |
| RequestThrottled | Požadavek byl pro {0} koncový bod omezen. Maximální souběžnost pro koncový bod je {1}. |
| TooManyConcurrentRequests | Poslalo příliš mnoho souběžných požadavků. |
| TooManyHostsBeingInitialized | Příliš mnoho hostitelů během inicializace ve stejnou dobu. Vezměte v úvahu omezení využití sítě / opakování. |
| TooManyHostsBeingInitializedPerModel | Příliš mnoho hostitelů během inicializace ve stejnou dobu. Vezměte v úvahu omezení využití sítě / opakování. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (stavový kód HTTP 504)
 
Operaci nešlo dokončit v povoleném čase.
 
| Kód chyby | Zpráva uživatele |
| ---------- |--------------|
| BackendInitializationTimeout | Inicializace webové služby nelze dokončit v povoleném čase. |
| BackendScoreTimeout | Spuštění webové služby požadavek nešlo dokončit v povoleném čase. |
 
