---
title: Tutorial`:` Use a managed identity to access Azure Key Vault - Windows - Azure AD
description: Tento kurz vás postupně provede používáním spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure Key Vaultu.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 106252b7c77f9ee3d6b9bdebafce3441d9c4b090
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224228"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Kurz: Použití spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure Key Vaultu 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak pomocí spravované identity přiřazené systémem pro virtuální počítač s Windows získat přístup ke službě Azure Key Vault. Key Vault slouží ke spuštění. Klientské aplikaci umožňuje použít tajný kód pro přístup k prostředkům, které nejsou zabezpečené službou Azure Active Directory (AD). Identity spravovaných služeb, které se spravují automaticky v Azure, slouží k ověření přístupu ke službám podporujícím ověřování Azure AD bez nutnosti vložení přihlašovacích údajů do kódu. 

Získáte informace o těchto tématech:


> [!div class="checklist"]
> * Udělení přístupu virtuálnímu počítači k tajnému kódu uloženému ve službě Key Vault 
> * Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k načtení tajného kódu ze služby Key Vault 

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Udělení přístupu virtuálnímu počítači k tajnému kódu uloženému ve službě Key Vault 
 
Když použijete spravované identity pro prostředky Azure, může kód získat přístupové tokeny sloužící k ověření přístupu k prostředkům, které podporují ověřování Azure AD.  However, not all Azure services support Azure AD authentication. To use managed identities for Azure resources with those services, store the service credentials in Azure Key Vault, and use the VM's managed identity to access Key Vault to retrieve the credentials. 

Napřed potřebujete vytvořit Key Vault a pak k němu udělíte přístup spravované identitě přiřazené systémem virtuálního počítače.   

1. Na navigačním panelu vlevo nahoře vyberte **Vytvořit prostředek** > **Zabezpečení a identita** > **Key Vault**.  
2. Zadejte **název** nového trezoru klíčů. 
3. Umístěte trezor klíčů do stejného předplatného a stejné skupiny prostředků jako virtuální počítač, který jste vytvořili dříve. 
4. Vyberte **Zásady přístupu** a klikněte na **Přidat novou**. 
5. V nabídce Konfigurace ze šablony vyberte **Správa tajných kódů**. 
6. Zvolte **Výběr objektu zabezpečení** a do vyhledávacího pole zadejte název dříve vytvořeného virtuálního počítače.  Select the VM in the result list and click **Select**. 
7. Kliknutím na **OK** dokončete přidání nové zásady přístupu. Kliknutím na **OK** dokončete výběr zásady přístupu. 
8. Kliknutím na **Vytvořit** dokončete vytvoření trezoru klíčů. 

    ![Alternativní text k obrázku](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Potom přidejte do trezoru klíčů tajný kód, abyste ho mohli později načíst kódem spuštěným na vašem virtuálním počítači: 

1. Vyberte **Všechny prostředky** a najděte a vyberte trezor klíčů, který jste vytvořili. 
2. Vyberte **Tajné kódy** a klikněte na **Přidat**. 
3. V nabídce **Možnosti nahrání** vyberte **Ručně**. 
4. Zadejte název a hodnotu tajného kódu.  The value can be anything you want. 
5. Nechte datum aktivace i datum konce platnosti nevyplněné a **Povoleno** nechte nastavené na **Ano**. 
6. Kliknutím na **Vytvořit** vytvořte tajný kód. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k načtení tajného kódu ze služby Key Vault  

Pokud nemáte nainstalovaný PowerShell 4.3.1 nebo novější, budete si muset [stáhnout a nainstalovat nejnovější verzi](https://docs.microsoft.com/powershell/azure/overview).

Nejdřív použijeme spravovanou identitu přiřazenou systémem virtuálního počítače k získání přístupového tokenu pro ověření v Key Vaultu:
 
1. Na portálu přejděte na **Virtuální počítače**, přejděte na svůj virtuální počítač s Windows a v části **Přehled** klikněte na **Připojit**.
2. Zadejte své **Uživatelské jméno** a **Heslo**, které jste přidali při vytváření **virtuálního počítače s Windows**.  
3. Teď, když jste vytvořili **připojení ke vzdálené ploše** virtuálního počítače, otevřete ve vzdálené relaci PowerShell.  
4. V PowerShellu vyvolejte na tenantovi webový požadavek na získání tokenu pro místního hostitele na konkrétním portu pro virtuální počítač.  

    Požadavek PowerShellu:
    
    ```powershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
    ```
    
    Dále extrahujte úplnou odpověď, která je uložená jako formátovaný řetězec JSON (JavaScript Object Notation) v objektu $response.  
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json 
    ```
    
    Potom z odpovědi extrahujte přístupový token.  
    
    ```powershell
    $KeyVaultToken = $content.access_token 
    ```
    
    Nakonec pomocí příkazu Invoke-WebRequest v PowerShellu načtěte tajný kód, který jste ve službě Key Vault vytvořili dříve, předáním přístupového tokenu v autorizační hlavičce.  You’ll need the URL of your Key Vault, which is in the **Essentials** section of the **Overview** page of the Key Vault.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    Odpověď bude vypadat takto: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Jakmile ze služby Key Vault načtete tajný kód, můžete ho použít při přihlášení ke službě, která vyžaduje jméno a heslo. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak využít spravovanou identitu přiřazenou systémem na virtuálním počítači s Windows pro přístup k Azure Key Vaultu.  Další informace o službě Azure Key Vault najdete tady:

> [!div class="nextstepaction"]
>[Azure Key Vault](/azure/key-vault/key-vault-overview)
