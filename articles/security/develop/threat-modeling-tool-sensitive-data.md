---
title: Citlivá data – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: zmírnění rizik pro ohrožené hrozby v Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: d482ae375fca3824213b54c2045d114fa2f0bfbe
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727866"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Rámec zabezpečení: Citlivá data | Hrozeb 
| Produkt nebo službu | Článek |
| --------------- | ------- |
| **Hranice důvěryhodnosti počítače** | <ul><li>[Ujistěte se, že binární soubory jsou zakódovány, pokud obsahují citlivé informace.](#binaries-info)</li><li>[Při ochraně důvěrných dat specifických pro uživatele zvažte použití systému souborů EFS (Encrypted File System).](#efs-user)</li><li>[Zajistěte, aby citlivá data uložená aplikací v systému souborů byla zašifrovaná.](#filesystem)</li></ul> | 
| **Webová aplikace** | <ul><li>[Zajistěte, aby se citlivý obsah v prohlížeči neukládal do mezipaměti.](#cache-browser)</li><li>[Šifrovat oddíly konfiguračních souborů webové aplikace, které obsahují citlivá data](#encrypt-data)</li><li>[Explicitně zakázat atribut AutoComplete HTML v citlivých formulářích a vstupech](#autocomplete-input)</li><li>[Zajistěte, aby se citlivá data zobrazená na obrazovce uživatele maskována](#data-mask)</li></ul> | 
| **Database** | <ul><li>[Implementace dynamického maskování dat pro omezení ohrožení citlivých dat uživatelů bez oprávnění](#dynamic-users)</li><li>[Zajistěte, aby hesla byla uložená ve formátu soled hash.](#salted-hash)</li><li>[Ujistěte se, že citlivá data ve sloupcích databáze jsou šifrovaná.](#db-encrypted)</li><li>[Ujistěte se, že je povolené šifrování na úrovni databáze (TDE).](#tde-enabled)</li><li>[Zajistěte, aby zálohy databáze byly šifrované.](#backup)</li></ul> | 
| **Webové rozhraní API** | <ul><li>[Ujistěte se, že citlivá data související s webovým rozhraním API nejsou uložená v úložišti prohlížeče.](#api-browser)</li></ul> | 
| Azure Document DB | <ul><li>[Šifrování citlivých dat uložených v Azure Cosmos DB](#encrypt-docdb)</li></ul> | 
| **Hranice vztahu důvěryhodnosti virtuálních počítačů Azure IaaS** | <ul><li>[Použití Azure Disk Encryption k šifrování disků používaných nástrojem Virtual Machines](#disk-vm)</li></ul> | 
| **Service Fabric hranice důvěryhodnosti** | <ul><li>[Šifrování tajných kódů v aplikacích Service Fabric](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Proveďte modelování zabezpečení a v případě potřeby použijte obchodní jednotky/týmy.](#modeling-teams)</li><li>[Minimalizace přístupu ke sdílení funkcí u kritických entit](#entities)</li><li>[Školení uživatelů o rizicích spojených s funkcí sdílení Dynamics CRM a dobrými postupy zabezpečení](#good-practices)</li><li>[Zahrnutí pravidla pro vývojové standardy proscribing zobrazení podrobností o konfiguraci ve správě výjimek](#exception-mgmt)</li></ul> | 
| **Azure Storage** | <ul><li>[Použít šifrování služby Azure Storage (SSE) pro neaktivní neaktivní data (Preview)](#sse-preview)</li><li>[Použití šifrování na straně klienta k ukládání citlivých dat v Azure Storage](#client-storage)</li></ul> | 
| **Mobilní klient** | <ul><li>[Šifrování citlivých nebo PII dat zapsaných na telefonech v místním úložišti](#pii-phones)</li><li>[Zazmatení vygenerovaných binárních souborů před distribucí koncovým uživatelům](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Nastavení clientCredentialType na certifikát nebo Windows](#cert)</li><li>[WCF – režim zabezpečení není povolený.](#security)</li></ul> | 

## <a id="binaries-info"></a>Ujistěte se, že binární soubory jsou zakódovány, pokud obsahují citlivé informace.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Zajistěte, aby byly binární soubory zakódovány, pokud obsahují citlivé informace, jako jsou obchodní tajemství, citlivá obchodní logika, která by neměla být reverzní. Účelem je zastavit zpětnou inženýraci sestavení. Pro tento `CryptoObfuscator` účel se můžou použít nástroje, jako je. |

## <a id="efs-user"></a>Při ochraně důvěrných dat specifických pro uživatele zvažte použití systému souborů EFS (Encrypted File System).

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Zvažte použití systému souborů EFS (Encrypted File System) k ochraně důvěrných dat specifických pro uživatele z nežádoucí osoby s fyzickým přístupem k počítači. |

## <a id="filesystem"></a>Zajistěte, aby citlivá data uložená aplikací v systému souborů byla zašifrovaná.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Ujistěte se, že citlivá data uložená v aplikaci v systému souborů jsou šifrovaná (například pomocí DPAPI), pokud se systém souborů EFS nedá vyhovět. |

## <a id="cache-browser"></a>Zajistěte, aby se citlivý obsah v prohlížeči neukládal do mezipaměti.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, webové formuláře, MVC5, MVC6 |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Prohlížeče můžou ukládat informace pro účely ukládání do mezipaměti a historie. Tyto soubory uložené v mezipaměti se ukládají do složky, jako je například složka dočasných internetových souborů v případě aplikace Internet Explorer. Když se tyto stránky znovu odkazují, prohlížeč je zobrazí ze své mezipaměti. Pokud se uživatelům zobrazí citlivé informace (například adresa, podrobnosti o kreditních kartách, číslo sociálního pojištění nebo uživatelské jméno), pak tyto informace mohou být uloženy v mezipaměti prohlížeče, a proto je možné je získat prostřednictvím přezkoumání mezipaměti prohlížeče nebo pomocí Stačí stisknout tlačítko zpět v prohlížeči. Nastaví hodnotu hlavičky Cache-Control na hodnotu "No-Store" pro všechny stránky. |

### <a name="example"></a>Příklad
```XML
<configuration>
  <system.webServer>
   <httpProtocol>
    <customHeaders>
        <add name="Cache-Control" value="no-cache" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="-1" />
    </customHeaders>
  </httpProtocol>
 </system.webServer>
</configuration>
```

### <a name="example"></a>Příklad
To může být implementováno prostřednictvím filtru. Můžete použít následující příklad: 
```csharp
public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            if (filterContext == null || (filterContext.HttpContext != null && filterContext.HttpContext.Response != null && filterContext.HttpContext.Response.IsRequestBeingRedirected))
            {
                //// Since this is MVC pipeline, this should never be null.
                return;
            }

            var attributes = filterContext.ActionDescriptor.GetCustomAttributes(typeof(System.Web.Mvc.OutputCacheAttribute), false);
            if (attributes == null || **Attributes**.Count() == 0)
            {
                filterContext.HttpContext.Response.Cache.SetNoStore();
                filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
                filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddHours(-1));
                if (!filterContext.IsChildAction)
                {
                    filterContext.HttpContext.Response.AppendHeader("Pragma", "no-cache");
                }
            }

            base.OnActionExecuting(filterContext);
        }
``` 

## <a id="encrypt-data"></a>Šifrovat oddíly konfiguračních souborů webové aplikace, které obsahují citlivá data

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [Postup: Zašifrujte konfigurační oddíly v ASP.NET 2,0](https://msdn.microsoft.com/library/ff647398.aspx)pomocí rozhraní DPAPI, [zadáním chráněného poskytovatele konfigurace](https://msdn.microsoft.com/library/68ze1hb2.aspx) [pomocí Azure Key Vault k ochraně tajných klíčů aplikací](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) . |
| **Kroky** | Konfigurační soubory, jako je soubor Web. config, appSettings. JSON, se často používají k ukládání citlivých informací, včetně uživatelských jmen, hesel, připojovacích řetězců databáze a šifrovacích klíčů. Pokud tyto informace nechráníte, je vaše aplikace zranitelná vůči útočníkům nebo zlomyslným uživatelům, kteří získají citlivé informace, jako jsou uživatelská jména a hesla pro účty, názvy databází a názvy serverů. V závislosti na typu nasazení (Azure/on-Prem) Šifrujte citlivé oddíly konfiguračních souborů pomocí DPAPI nebo služeb, jako je Azure Key Vault. |

## <a id="autocomplete-input"></a>Explicitně zakázat atribut AutoComplete HTML v citlivých formulářích a vstupech

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [MSDN: atributy automatického](https://msdn.microsoft.com/library/ms533486(VS.85).aspx)dokončování [pomocí automatického dokončování ve formátu HTML](https://msdn.microsoft.com/library/ms533032.aspx), [chyby zabezpečení při upravení HTML](https://technet.microsoft.com/security/bulletin/MS10-071), [Automatické dokončování.](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Kroky** | Atribut AutoComplete určuje, zda má formulář mít zapnuto nebo vypnuto automatické dokončování. Když je automatické dokončování zapnuté, prohlížeč automaticky dokončí hodnoty na základě hodnot, které uživatel zadal před. Pokud je například ve formuláři zadáno nové jméno a heslo a formulář je odeslán, prohlížeč zobrazí dotaz, zda má být heslo uloženo. Po zobrazení formuláře se pak jméno a heslo vyplní automaticky nebo se dokončí, protože se zadá název. Útočník s místním přístupem by mohl získat heslo nešifrovaného textu z mezipaměti prohlížeče. Ve výchozím nastavení je automatické dokončování povolené a musí být explicitně zakázané. |

### <a name="example"></a>Příklad
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a id="data-mask"></a>Zajistěte, aby se citlivá data zobrazená na obrazovce uživatele maskována

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Citlivá data, jako jsou hesla, čísla kreditních karet, SSN atd., by se měla maskovat při zobrazení na obrazovce. K tomu je potřeba zabránit neoprávněným osobám v přístupu k datům (např. hesla procházení rameny, zaměstnanci podpory, kteří prohlížejí SSN čísly uživatelů). Ujistěte se, že tyto datové prvky nejsou viditelné ve formátu prostého textu a jsou vhodně maskovány. Tato akce musí být pořízena při přijetí jako vstup (např. input type = "Password") a zobrazení zpět na obrazovce (například zobrazit pouze poslední 4 číslice čísla platební karty). |

## <a id="dynamic-users"></a>Implementace dynamického maskování dat pro omezení ohrožení citlivých dat uživatelů bez oprávnění

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Sql Azure, OnPrem |
| **Atributy**              | SQL verze – V12, verze SQL – MsSQL2016 |
| **Odkazy**              | [Dynamické maskování dat](https://msdn.microsoft.com/library/mt130841) |
| **Kroky** | Účelem dynamického maskování dat je omezit vystavení citlivých dat a zabránit tak uživatelům, kteří by neměli mít přístup k datům ze zobrazení. Dynamické maskování dat nemá za cíl zabránit uživatelům databáze v přímém připojení k databázi a spouštění vyčerpávajících dotazů, které zveřejňují citlivé údaje. Dynamické maskování dat je doplňkem dalších funkcí zabezpečení SQL Server (auditování, šifrování, zabezpečení na úrovni řádků) a důrazně se doporučuje tuto funkci používat společně s nimi, aby lépe chránila citlivá data v databáze. Upozorňujeme, že tato funkce je podporovaná jenom SQL Server počínaje 2016 a Azure SQL Database. |

## <a id="salted-hash"></a>Zajistěte, aby hesla byla uložená ve formátu soled hash.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [Hash hesla pomocí rozhraní API pro šifrování .NET](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Kroky** | Hesla by se neměla ukládat do vlastních databází uživatelských úložišť. Hodnoty hash hesla by měly být uloženy s hodnotami Salt. Ujistěte se, že hodnota salt pro uživatele je vždycky jedinečná a před uložením hesla použijte b-crypt, s-crypt nebo PBKDF2, s minimálním počtem iterací pracovních faktorů 150 000 smyček, aby se vyloučila možnost hrubého vynucení.| 

## <a id="db-encrypted"></a>Ujistěte se, že citlivá data ve sloupcích databáze jsou šifrovaná.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | SQL verze – vše |
| **Odkazy**              | Jak [šifrovat citlivá data v SQL serveru](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [postupy: Šifrování sloupce dat v SQL Server](https://msdn.microsoft.com/library/ms179331), [šifrování podle certifikátu](https://msdn.microsoft.com/library/ms188061) |
| **Kroky** | Citlivá data, jako jsou třeba čísla kreditních karet, se musí v databázi šifrovat. Data lze zašifrovat pomocí šifrování na úrovni sloupce nebo pomocí funkce šifrování. |

## <a id="tde-enabled"></a>Ujistěte se, že je povolené šifrování na úrovni databáze (TDE).

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [Principy SQL Server transparentní šifrování dat (TDE)](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Kroky** | Funkce transparentní šifrování dat (TDE) v systému SQL Server pomáhá šifrovat citlivá data v databázi a chránit klíče používané k šifrování dat pomocí certifikátu. To zabrání komukoli bez klíčů v používání dat. TDE chrání data v klidovém znění, což znamená data a soubory protokolů. Nabízí možnost dodržovat řadu zákonů, předpisů a pokynů v různých oborech. |

## <a id="backup"></a>Zajistěte, aby zálohy databáze byly šifrované.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | SQL Azure, OnPrem |
| **Atributy**              | SQL verze – V12, verze SQL – MsSQL2014 |
| **Odkazy**              | [Šifrování zálohování databáze SQL](https://msdn.microsoft.com/library/dn449489) |
| **Kroky** | SQL Server má možnost šifrovat data při vytváření zálohy. Když při vytváření zálohy zadáte šifrovací algoritmus a modul pro šifrování (certifikát nebo asymetrický klíč), může jeden vytvořit zašifrovaný záložní soubor. |

## <a id="api-browser"></a>Ujistěte se, že citlivá data související s webovým rozhraním API nejsou uložená v úložišti prohlížeče.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webové rozhraní API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC 5, MVC 6 |
| **Atributy**              | Zprostředkovatel identity – ADFS, zprostředkovatel identity – Azure AD |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | <p>V některých implementacích jsou citlivé artefakty relevantní pro ověřování webového rozhraní API uložené v místním úložišti prohlížeče. Například artefakty ověřování Azure AD, jako ADAL. idtoken, ADAL. nonce. idtoken, ADAL. Access. token. Key, ADAL. token. Keys, ADAL. State. Login, ADAL. Session. State, ADAL. vypršení platnosti. Key atd.</p><p>Všechny tyto artefakty jsou k dispozici i po odhlášení nebo zavření prohlížeče. Pokud nežádoucí osoba získá přístup k těmto artefaktům, může je znovu použít pro přístup k chráněným prostředkům (API). Ujistěte se, že všechny citlivé artefakty související s webovým rozhraním API nejsou uložené v úložišti prohlížeče. V případech, kdy je úložiště na straně klienta nevyhnutelné (například jednostránkové aplikace (SPA), které využívají implicitní toky OpenIdConnect/OAuth, musí ukládat přístupové tokeny lokálně), používat volby úložiště s nestálou trvanlivostí. například preferovat SessionStorage na LocalStorage.</p>| 

### <a name="example"></a>Příklad
Níže uvedený fragment kódu JavaScriptu je z vlastní knihovny ověřování, která ukládá artefakty ověřování v místním úložišti. Taková implementace by se měla vyhnout. 
```javascript
ns.AuthHelper.Authenticate = function () {
window.config = {
instance: 'https://login.microsoftonline.com/',
tenant: ns.Configurations.Tenant,
clientId: ns.Configurations.AADApplicationClientID,
postLogoutRedirectUri: window.location.origin,
cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
};
```

## <a id="encrypt-docdb"></a>Šifrování citlivých dat uložených v Cosmos DB

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Document DB | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Šifrování citlivých dat na úrovni aplikace před uložením v databázi dokumentů nebo uložení citlivých dat v jiných řešeních úložiště, jako je Azure Storage nebo Azure SQL| 

## <a id="disk-vm"></a>Použití Azure Disk Encryption k šifrování disků používaných nástrojem Virtual Machines

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahu důvěryhodnosti virtuálních počítačů Azure IaaS | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [Použití Azure Disk Encryption k šifrování disků používaných vašimi virtuálními počítači](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Kroky** | <p>Azure Disk Encryption je nová funkce, která je aktuálně ve verzi Preview. Tato funkce umožňuje šifrovat disky s operačním systémem a datové disky, které používá virtuální počítač s IaaS. V systému Windows se jednotky šifrují pomocí standardní technologie šifrování BitLockeru v oboru. Pro Linux jsou disky šifrované pomocí technologie DM-crypt. Tato možnost je integrovaná s Azure Key Vault, aby vám umožnila řídit a spravovat klíče pro šifrování disků. Řešení Azure Disk Encryption podporuje následující tři scénáře šifrování zákazníka:</p><ul><li>Povolte šifrování u nových virtuálních počítačů s IaaS vytvořených ze souborů VHD zašifrovaných zákazníkem a šifrovacích klíčů poskytnutých zákazníkem, které jsou uložené v Azure Key Vault.</li><li>Povolte šifrování u nových virtuálních počítačů s IaaS vytvořených z Azure Marketplace.</li><li>Povolte šifrování u stávajících virtuálních počítačů s IaaS, které už běží v Azure.</li></ul>| 

## <a id="fabric-apps"></a>Šifrování tajných kódů v aplikacích Service Fabric

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Service Fabric hranice důvěryhodnosti | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Prostředí – Azure |
| **Odkazy**              | [Správa tajných kódů v aplikacích Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Kroky** | Tajné kódy můžou obsahovat citlivé informace, jako jsou například připojovací řetězce úložiště, hesla nebo jiné hodnoty, které by neměly být zpracovány v prostém textu. Ke správě klíčů a tajných kódů v aplikacích Service Fabric použijte Azure Key Vault. |

## <a id="modeling-teams"></a>Proveďte modelování zabezpečení a v případě potřeby použijte obchodní jednotky/týmy.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Proveďte modelování zabezpečení a v případě potřeby použijte obchodní jednotky/týmy. |

## <a id="entities"></a>Minimalizace přístupu ke sdílení funkcí u kritických entit

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Minimalizace přístupu ke sdílení funkcí u kritických entit |

## <a id="good-practices"></a>Školení uživatelů o rizicích spojených s funkcí sdílení Dynamics CRM a dobrými postupy zabezpečení

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Školení uživatelů o rizicích spojených s funkcí sdílení Dynamics CRM a dobrými postupy zabezpečení |

## <a id="exception-mgmt"></a>Zahrnutí pravidla pro vývojové standardy proscribing zobrazení podrobností o konfiguraci ve správě výjimek

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | Není k dispozici  |
| **Kroky** | Zahrňte pravidlo pro vývojové standardy proscribing se zobrazením konfiguračních údajů ve správě výjimek mimo vývoj. Testujte je jako součást revizí kódu nebo pravidelné kontroly.|

## <a id="sse-preview"></a>Použít šifrování služby Azure Storage (SSE) pro neaktivní neaktivní data (Preview)

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | StorageType – objekt BLOB |
| **Odkazy**              | [Šifrování služby Azure Storage pro neaktivní neaktivní data (Preview)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Kroky** | <p>Šifrování služby Azure Storage (SSE) pro neaktivní data pomáhá chránit a chránit vaše data, aby splňovala závazky zabezpečení a dodržování předpisů vaší organizace. Pomocí této funkce služba Azure Storage automaticky šifruje vaše data před zachováním v úložišti a dešifruje před jejich načtením. Šifrování, dešifrování a Správa klíčů je pro uživatele zcela transparentní. SSE platí jenom pro objekty blob bloku, objekty blob stránky a doplňovací objekty blob. Ostatní typy dat, včetně tabulek, front a souborů, nebudou zašifrovány.</p><p>Pracovní postup šifrování a dešifrování:</p><ul><li>Zákazník povoluje šifrování v účtu úložiště.</li><li>Když zákazník zapisuje nová data (vložte objekt blob, blok PUT, vložte stránku atd.) do úložiště objektů BLOB; Každý zápis je zašifrovaný pomocí 256 šifrování AES, což je jedno z nejúčinnějších šifrovacích šifr, které jsou k dispozici.</li><li>Když zákazník potřebuje přístup k datům (získat objekt BLOB atd.), data se před návratem k uživateli automaticky dešifrují.</li><li>Pokud je šifrování zakázané, nové zápisy už nebudou šifrované a stávající šifrovaná data zůstanou zašifrovaná, dokud je uživatel nepřepíše. Když je šifrování povolené, zašifrují se zápisy do úložiště objektů BLOB. Stav dat se nemění s uživatelem přepínáním mezi povolením nebo zakázáním šifrování pro účet úložiště.</li><li>Všechny šifrovací klíče se ukládají, šifrují a spravují pomocí Microsoftu.</li></ul><p>Upozorňujeme, že v tuto chvíli jsou klíče používané pro šifrování spravované Microsoftem. Společnost Microsoft tyto klíče vygenerovala jako původní a spravuje zabezpečené úložiště klíčů a také pravidelné otočení definované interními zásadami Microsoftu. V budoucnu zákazníci získají možnost spravovat vlastní šifrovací klíče > a poskytnou klíč pro migraci z klíčů spravovaných společností Microsoft do klíčů spravovaných zákazníkem.</p>| 

## <a id="client-storage"></a>Použití šifrování na straně klienta k ukládání citlivých dat v Azure Storage

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [Azure Key Vault a šifrování na straně klienta pro Microsoft Azure Storage](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/) [kurz: Šifrování a dešifrování objektů BLOB v Microsoft Azure Storage pomocí](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/)Azure Key Vault, [bezpečné ukládání dat v Azure Blob Storage s rozšířeními šifrování Azure](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Kroky** | <p>Balíček klientské knihovny NuGet Azure Storage pro .NET podporuje šifrování dat v rámci klientských aplikací před odesláním do Azure Storage a dešifrování dat při stahování do klienta. Knihovna také podporuje integraci se službou Azure Key Vault pro správu klíčů účtu úložiště. Tady je stručný popis toho, jak funguje šifrování na straně klienta:</p><ul><li>Sada SDK Azure Storage klienta generuje šifrovací klíč obsahu (CEK), což je symetrický klíč založený na jednorázovém použití.</li><li>Zákaznická data se šifrují pomocí tohoto CEK</li><li>CEK se pak zabalí (zašifruje) pomocí klíčového šifrovacího klíče (KEK). KEK je identifikován identifikátorem klíče a může se jednat o asymetrický klíč nebo symetrický klíč a lze ho spravovat místně nebo uložit v Azure Key Vault. Klient úložiště nemá nikdy přístup k KEK. Pouze vyvolá algoritmus zalamování klíčů, který je k dispozici v Key Vault. Zákazníci si můžou vybrat, jestli mají používat vlastní poskytovatele pro zalamování a rozbalení klíče, pokud chtějí.</li><li>Šifrovaná data se pak nahrají do služby Azure Storage. Odkazy v části odkazy najdete v podrobnostech o implementaci nízké úrovně.</li></ul>|

## <a id="pii-phones"></a>Šifrování citlivých nebo PII dat zapsaných na telefonech v místním úložišti

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilní klient | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, Xamarin  |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [Správa nastavení a funkcí v zařízeních pomocí zásad Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies) [osobního řetězce klíčů](https://components.xamarin.com/view/square.valet) |
| **Kroky** | <p>Pokud aplikace zapisuje citlivé informace, jako je třeba PII (e-mail, telefonní číslo, křestní jméno, příjmení, předvolby atd.) – v mobilním systému souborů by měl být před zápisem do místního systému souborů zašifrován. Pokud je aplikace podniková aplikace, prozkoumejte možnost publikování aplikace pomocí služby Windows Intune.</p>|

### <a name="example"></a>Příklad
Intune se dá nakonfigurovat pomocí následujících zásad zabezpečení pro ochranu citlivých dat: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Příklad
Pokud aplikace není podniková aplikace, použijte úložiště klíčů, které poskytuje platforma, k ukládání šifrovacích klíčů, pomocí kterých kryptografické operace lze provést v systému souborů. Následující fragment kódu ukazuje, jak získat přístup ke klíči ze řetězce klíčů pomocí Xamarin: 
```csharp
        protected static string EncryptionKey
        {
            get
            {
                if (String.IsNullOrEmpty(_Key))
                {
                    var query = new SecRecord(SecKind.GenericPassword);
                    query.Service = NSBundle.MainBundle.BundleIdentifier;
                    query.Account = "UniqueID";

                    NSData uniqueId = SecKeyChain.QueryAsData(query);
                    if (uniqueId == null)
                    {
                        query.ValueData = NSData.FromString(System.Guid.NewGuid().ToString());
                        var err = SecKeyChain.Add(query);
                        _Key = query.ValueData.ToString();
                    }
                    else
                    {
                        _Key = uniqueId.ToString();
                    }
                }

                return _Key;
            }
        }
```

## <a id="binaries-end"></a>Zazmatení vygenerovaných binárních souborů před distribucí koncovým uživatelům

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilní klient | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [Zmatené šifrování pro .NET](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Kroky** | Generované binární soubory (sestavení v rámci APK) by měly být zakódovány pro zastavení zpětné analýzy sestavení. Pro tento `CryptoObfuscator` účel se můžou použít nástroje, jako je. |

## <a id="cert"></a>Nastavení clientCredentialType na certifikát nebo Windows

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | .NET Framework 3 |
| **Atributy**              | Není k dispozici  |
| **Odkazy**              | [Fortify](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Kroky** | Použití UsernameToken s heslem ve formátu prostého textu prostřednictvím nešifrovaného kanálu zpřístupňuje heslo útočníkům, kteří můžou sledovat zprávy protokolu SOAP. Poskytovatelé služeb, kteří používají UsernameToken, můžou přijímat hesla odesílaná ve formátu prostého textu. Posílání hesel v prostém textu přes nešifrovaný kanál může vystavit přihlašovací údaje útočníkům, kteří můžou zprávu protokolu SOAP sledovat. | 

### <a name="example"></a>Příklad
Následující konfigurace poskytovatele služby WCF používá UsernameToken: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Nastavte clientCredentialType na Certificate nebo Windows. 

## <a id="security"></a>WCF – režim zabezpečení není povolený.

| Název                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, .NET Framework 3 |
| **Atributy**              | Režim zabezpečení – přenos, režim zabezpečení – zpráva |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Španělská](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference)republika, [základy služby WCF CoDe Security Magazine](https://www.codemag.com/article/0611051) |
| **Kroky** | Nebyla definována žádná zabezpečení přenosu nebo zprávy. Aplikace, které přenášejí zprávy bez přenosu nebo zabezpečení zprávy, nemůžou zaručit integritu a důvěrnost zpráv. Pokud je vazba zabezpečení WCF nastavená na hodnotu žádné, je přenos i zabezpečení zpráv zakázané. |

### <a name="example"></a>Příklad
Následující konfigurace nastaví režim zabezpečení na žádný. 
```
<system.serviceModel> 
  <bindings> 
    <wsHttpBinding> 
      <binding name=""MyBinding""> 
        <security mode=""None""/> 
      </binding> 
  </bindings> 
</system.serviceModel> 
```

### <a name="example"></a>Příklad
Režim zabezpečení napříč všemi vazbami služby je pět možných způsobů zabezpečení: 
* Žádné. Vypne zabezpečení. 
* Přepravu. Používá zabezpečení přenosu pro vzájemné ověřování a ochranu zpráv. 
* zpráva. Používá zabezpečení zpráv pro vzájemné ověřování a ochranu zpráv. 
* Protokoly. Umožňuje poskytovat nastavení pro přenos a zabezpečení na úrovni zpráv (podporuje to pouze služba MSMQ). 
* TransportWithMessageCredential. Přihlašovací údaje se předávají se zprávou a ochranou zpráv a ověření serveru zajišťuje přenosová vrstva. 
* TransportCredentialOnly. Pověření klienta se předávají s přenosovou vrstvou a není použita žádná ochrana zpráv. K ochraně integrity a důvěrnosti zpráv použijte přenos a zabezpečení zpráv. Níže uvedená konfigurace oznamuje službě, aby používala zabezpečení přenosu s přihlašovacími údaji zprávy.
  ```
  <system.serviceModel>
  <bindings>
    <wsHttpBinding>
    <binding name=""MyBinding""> 
    <security mode=""TransportWithMessageCredential""/> 
    <message clientCredentialType=""Windows""/> 
    </binding> 
  </bindings> 
  </system.serviceModel> 
  ```
