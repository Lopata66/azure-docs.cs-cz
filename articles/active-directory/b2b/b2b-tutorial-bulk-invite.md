---
title: Kurz hromadného pozvání uživatelů spolupráce B2B – Azure Active Directory | Microsoft Docs
description: V tomto kurzu se dozvíte, jak použít PowerShell a soubor CSV k hromadnému rozeslání pozvánek externím uživatelům spolupráce B2B služby Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 897fa353061d5b3e59f5613ea46e47afba0e3efa
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337795"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Kurz: Hromadné pozvat uživatele spolupráce B2B ve službě Azure AD

Pokud k práci se svými externími partnery používáte spolupráci B2B služby Azure Active Directory (Azure AD), můžete do organizace pozvat více uživatelů typu host najednou. V tomto kurzu se dozvíte, jak použít PowerShell k hromadnému rozeslání pozvánek externím uživatelům. Konkrétně provedete následující:

> [!div class="checklist"]
> * Připravíte soubor CSV (textový soubor s oddělovači) s informacemi o uživatelích.
> * Spustíte skript PowerShellu pro odeslání pozvánek.
> * Ověříte, že jsou uživatelé v adresáři přidaní.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky

### <a name="install-the-latest-azureadpreview-module"></a>Instalace nejnovějšího modulu AzureADPreview
Přesvědčte se, že instalujete nejnovější verzi modulu Azure AD PowerShell pro Graph (AzureADPreview). 

Nejdřív zkontrolujte, které moduly jste nainstalovali. Otevřete Windows PowerShell se zvýšenými uživatelskými oprávněními (Spustit jako správce) a spusťte následující příkaz:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Na základě výstupu proveďte jednu z následujících akcí:

- Pokud se nezobrazí žádné výsledky, spusťte k instalaci modulu AzureADPreview následující příkaz:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Pokud se ve výsledcích zobrazí pouze modul AzureAD, spusťte k instalaci modulu AzureADPreview tyto příkazy: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Pokud se ve výsledcích zobrazí pouze modul AzureADPreview, ale zobrazí se zpráva, že existuje novější verze, aktualizujte ho následujícími příkazy: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Může se zobrazit výzva, že modul instalujete z nedůvěryhodného úložiště. K tomu dochází, pokud jste úložiště PSGallery dříve nenastavili jako důvěryhodné. Stisknutím klávesy **Y** modul nainstalujte.

### <a name="get-test-email-accounts"></a>Získání testovacích e-mailových účtů

Potřebujete alespoň dva testovací e-mailové účty, na které můžete pozvánky odeslat. Účty se nesmí nacházet ve vaší organizaci. Můžete použít libovolný typ účtu, včetně sociálních účtů jako jsou adresy gmail.com nebo outlook.com.

## <a name="prepare-the-csv-file"></a>Příprava souboru CSV

V Microsoft Excelu vytvořte soubor CSV se seznamem jmen pozvaných uživatelů a jejich e-mailových adres. Nezapomeňte nastavit záhlaví sloupce **Name** (Jméno) a **InvitedUserEmailAddress** (E-mailová adresa pozvaného uživatele). 

List vytvořte například v následujícím formátu:


![Výstup PowerShellu zobrazující zatím neschválené pozvání uživatele](media/tutorial-bulk-invite/AddUsersExcel.png)

Soubor uložte jako **C:\BulkInvite\Invitations.csv**. 

Pokud nemáte Excel, můžete soubor CSV vytvořit v libovolném textovém editoru, jako je například Poznámkový blok. Každou hodnotu oddělte čárkou a každý řádek novým řádkem. 

## <a name="sign-in-to-your-tenant"></a>Přihlášení k tenantovi

Spuštěním následujícího příkazu se připojte k doméně klienta:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Například, `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Po zobrazení výzvy zadejte své přihlašovací údaje.

## <a name="send-bulk-invitations"></a>Hromadné odeslání pozvánek

Pokud chcete pozvánky odeslat, spusťte následující skript PowerShellu (cesta **c:\bulkinvite\invitations.csv** je cestou k souboru CSV): 

```powershell
$invitations = import-csv c:\bulkinvite\invitations.csv
   
$messageInfo = New-Object Microsoft.Open.MSGraph.Model.InvitedUserMessageInfo
   
$messageInfo.customizedMessageBody = "Hello. You are invited to the Contoso organization."
   
foreach ($email in $invitations) 
   {New-AzureADMSInvitation `
      -InvitedUserEmailAddress $email.InvitedUserEmailAddress `
      -InvitedUserDisplayName $email.Name `
      -InviteRedirectUrl https://myapps.azure.com `
      -InvitedUserMessageInfo $messageInfo `
      -SendInvitationMessage $true
   }
```
Tento skript odešle pozvánku e-mailovým adresám v souboru Invitations.csv. Zobrazený výstup by měl u každého uživatele vypadat přibližně takto:

![Výstup PowerShellu zobrazující zatím neschválené pozvání uživatele](media/tutorial-bulk-invite/B2BBulkImport.png)

## <a name="verify-users-exist-in-the-directory"></a>Ověření existence uživatelů v adresáři

Pokud chcete ověřit, zda se uživatelé do Azure AD přidali, spusťte následující příkaz:
```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```
Měli byste vidět, které můžete pozvat uživatele uvedená se hlavní název uživatele (UPN) ve formátu *emailaddress*EXT #\@*domény*. Například *lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, kde je organizace, ze kterého jste odeslali pozvánek contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už testovací uživatelské účty v adresáři nepotřebujete, můžete je odstranit. Uživatelský účet odstraníte spuštěním následujícího příkazu:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Příklad: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>Další postup
V tomto kurzu jste hromadně odeslali pozvánky uživatelům typu host, kteří nepatří do vaší organizace. V dalším článku se dozvíte, jak funguje uplatnění pozvánky.

> [!div class="nextstepaction"]
> [Další informace o uplatnění pozvánky ve spolupráci B2B služby Azure AD](redemption-experience.md)

