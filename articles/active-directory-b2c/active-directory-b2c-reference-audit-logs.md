---
title: Protokoly auditu definice a ukázky v Azure Active Directory B2C | Dokumentace Microsoftu
description: Příručka a ukázky na přístup k protokolům auditu Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 216f5413ce3dae1f2d040643a30a4d7db4a879b8
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835413"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Přístup k protokolům auditu Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) vysílá protokoly auditu obsahující aktivitu informací o prostředcích B2C, vydané tokeny a přístup správce. Tento článek poskytuje stručný přehled informací dostupných prostřednictvím protokolů auditu a pokyny o tom, jak přístup k těmto datům pro vašeho tenanta Azure AD B2C.

> [!IMPORTANT]
> Protokoly auditu se zachovají jenom po dobu sedmi dní. Plán stažení a ukládat vaše protokoly pomocí jedné z metod je uvedeno níže, pokud budete potřebovat delší doba uchovávání informací.

> [!NOTE]
> Nelze zobrazit uživatelská přihlášení pro jednotlivé aplikace Azure AD B2C v části **uživatelé** část **Azure Active Directory** nebo **Azure AD B2C** oken. Přihlášení existuje zobrazí aktivity uživatelů, ale nemůže být korelována zpět do aplikace B2C, který uživatel přihlášený k. Protokoly auditu je nutné použít k tomu, jak je vysvětleno dále v tomto článku.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Přehled aktivit, které jsou k dispozici v kategorii B2C protokolů auditu
**B2C** kategorie z protokolů auditu obsahuje následující typy aktivit:

|Typ aktivity |Popis  |
|---------|---------|
|Authorization |Aktivity týkající se povolení uživatele B2C přístup k prostředkům (například správce přístup k seznamu zásad B2C)         |
|Adresář |Aktivity související s atributy adresáře, načíst, když se správce přihlásí pomocí webu Azure portal |
|Aplikace | Operace CRUD s aplikace B2C |
|Klíč |Operací CRUD u klíčů uložených v kontejneru klíčů B2C |
|Resource |Operace CRUD s B2C prostředky (například, zásady a zprostředkovatele identity)
|Ověřování |Ověření přihlašovacích údajů uživatele a vydávání tokenů|

> [!NOTE]
> Aktivity CRUD objekt uživatele, najdete **základní adresář** kategorie.

## <a name="example-activity"></a>Ukázkové aktivity
Následující příklad ukazuje data zaznamenaná, když se uživatel přihlásí pomocí externího zprostředkovatele identity: ![Příklad stránky s podrobnostmi aktivit protokolů auditu na webu Azure portal](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

Panel podrobností aktivity obsahuje následující důležité informace:

|Section|Pole|Popis|
|-------|-----|-----------|
| Aktivita | Name | Jaké aktivity došlo. Například "vydání tokentu id_token pro aplikaci" (který končí přihlášení skutečné uživatele). |
| Iniciátor (Actor) | ObjectId | **ID objektu** aplikace B2C, které se uživatel přihlašuje (Tento identifikátor se nezobrazuje na portálu Azure portal, ale je třeba přístupná přes rozhraní Graph API). |
| Iniciátor (Actor) | Spn | **ID aplikace** aplikace B2C, které se uživatel přihlašuje. |
| Cíle | ObjectId | **ID objektu** uživatele, který se přihlašuje. |
| Další podrobnosti | TenantId | **ID Tenanta** tenanta Azure AD B2C. |
| Další podrobnosti | `PolicyId` | **ID zásad** toku uživatele. (zásady) se používá k přihlášení uživatele. |
| Další podrobnosti | ApplicationId | **ID aplikace** aplikace B2C, které se uživatel přihlašuje. |

## <a name="accessing-audit-logs-through-the-azure-portal"></a>Přístup k protokolům auditu na webu Azure portal
1. Přejděte na [Azure Portal](https://portal.azure.com). Ujistěte se, že máte ve svém adresáři B2C.
2. Klikněte na **Azure Active Directory** na panel Oblíbené položky na levé straně

    ![Azure Active Directory tlačítko zvýrazněno nabídky na levé straně portálu](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. V části **aktivity**, klikněte na **protokolů auditu**

    ![Tlačítkem protokoly auditu v části aktivity služby nabídky](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. V **kategorie** dropboxu, vyberte **B2C**
3. Klikněte na **použít**

    ![Kategorie a tlačítko použít zvýrazněné filtr protokolu auditu](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Zobrazí se seznam aktivit přihlášení za posledních sedm dní.
- Použití **typ prostředku aktivity** rozevírací seznam můžete filtrovat podle typů aktivit uvedených výše
- Použití **rozsah** rozevírací seznam pro filtrování rozsah aktivit zobrazí
- Pokud kliknete na konkrétní řádek v seznamu, kontextové pole na pravé straně se zobrazí další atributy, které jsou přidružená k aktivitě
- Klikněte na **Stáhnout** stáhnete aktivity jako soubor csv

> [!NOTE]
> Můžete také naleznete v protokolech auditu tak, že přejdete na **Azure AD B2C** spíše než **Azure Active Directory** na panel Oblíbené položky na levé straně. V části **aktivity**, klikněte na **protokoly auditu**, kde najdete stejné protokoly s podobnými funkcemi filtrování.

## <a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Přístup k protokolům auditu prostřednictvím rozhraní API pro generování sestav Azure AD
Protokoly auditu se publikují do stejné kanálu další aktivity pro Azure Active Directory, takže k nim může přistupovat prostřednictvím [Azure Active Directory API pro vytváření sestav](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference).

### <a name="prerequisites"></a>Požadavky
K ověření na rozhraní API pro generování sestav Azure AD musíte nejprve registrovat aplikaci. Ujistěte se, že podle kroků v [požadavky pro přístup k rozhraní API pro generování sestav Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).

### <a name="accessing-the-api"></a>Přístup k rozhraní API
Ke stažení protokolů auditu Azure AD B2C prostřednictvím rozhraní API, budete chtít filtrovat protokoly a **B2C** kategorie. Filtrovat podle kategorie, použijte parametr řetězce dotazu při volání koncového bodu rozhraní API pro generování sestav Azure AD, jak je znázorněno níže:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

### <a name="powershell-script"></a>Skript PowerShellu
Následující skript představuje příklad použití Powershellu k dotazování rozhraní API pro generování sestav Azure AD a výsledek uložit jako soubor JSON:

```powershell
# This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # AAD B2C Tenant; for example, contoso.onmicrosoft.com
$resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&$filter=category eq ''B2C''and activityDate gt ' + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```
