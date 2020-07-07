---
title: Odeslání e-mailu z Azure Automation Runbooku
description: V tomto článku se dozvíte, jak odeslat e-mail z Runbooku.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: conceptual
ms.openlocfilehash: 1fa270907c96cb341f6ce2cbaeb91dfa323c4431
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85855224"
---
# <a name="send-an-email-from-a-runbook"></a>Odeslání e-mailu z runbooku

Pomocí prostředí PowerShell můžete odeslat e-mail z Runbooku pomocí [SendGrid](https://sendgrid.com/solutions) . 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ho ještě nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Účet Automation](automation-offering-get-started.md) pomocí **AZ** Modules.
* [Účet Spustit jako](automation-create-runas-account.md) pro uložení a spuštění Runbooku.

## <a name="create-an-azure-key-vault"></a>Vytvoření služby Azure Key Vault

Azure Key Vault můžete vytvořit pomocí následujícího skriptu PowerShellu. Nahraďte hodnoty proměnných hodnotami, které jsou specifické pro vaše prostředí. Použijte vložený Azure Cloud Shell přes tlačítko **vyzkoušet** , které se nachází v pravém horním rohu bloku kódu. Pokud máte na svém místním počítači nainstalované [moduly AZ](/powershell/azure/install-az-ps) , můžete kód taky zkopírovat a spustit místně.

> [!NOTE]
> Pokud chcete načíst svůj klíč rozhraní API, použijte postup v části [vyhledání klíče rozhraní API SendGrid](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Další způsoby vytvoření Azure Key Vault a uložení tajného klíče najdete v tématu [Key Vault rychlé starty](/azure/key-vault/).

## <a name="import-required-modules-into-your-automation-account"></a>Import požadovaných modulů do účtu Automation

Pokud chcete použít Azure Key Vault v rámci sady Runbook, musíte do svého účtu Automation importovat následující moduly:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Pokyny najdete v tématu [Import AZ modules](shared-resources/modules.md#import-az-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Vytvoření Runbooku k odeslání e-mailu

Po vytvoření Key Vault a uložení `SendGrid` klíče rozhraní API je čas vytvořit Runbook, který načte klíč rozhraní API a pošle e-mail. Pojďme použít sadu Runbook, která používá `AzureRunAsConnection` jako [účet Spustit jako](automation-create-runas-account.md) k ověření pomocí Azure pro načtení tajného kódu z Azure Key Vault. Zavoláme Runbook **Send-GridMailMessage**. Můžete upravit skript prostředí PowerShell, který se používá pro ukázkové účely, a znovu ho použít pro různé scénáře.

1. Přejít na účet Azure Automation.
2. V části **Automatizace procesu**vyberte **Runbooky**.
3. V horní části seznamu sad Runbook vyberte **+ vytvořit Runbook**.
4. Na stránce Přidat sadu Runbook zadejte do pole název sady Runbook **odeslání-GridMailMessage** . Jako typ Runbooku vyberte **PowerShell**. Pak vyberte **vytvořit**.
   ![Vytvořit Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Runbook se vytvoří a otevře se stránka Upravit powershellový runbook.
   ![Upravit Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Zkopírujte následující příklad PowerShellu do stránky pro úpravy. Ujistěte se, že `VaultName` Určuje název, který jste zvolili pro Key Vault.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. Vyberte **publikovat** a Runbook uložte a publikujte.

Chcete-li ověřit, zda se sada Runbook úspěšně spustí, můžete postupovat podle kroků v části [test sady Runbook](manage-runbooks.md#test-a-runbook) nebo [spuštění sady Runbook](start-runbooks.md).

Pokud se vám na zkušebním e-mailu nezobrazuje e-mail, zkontrolujte složky **nevyžádanou** **poštou a spam** .

## <a name="clean-up-resources-after-the-email-operation"></a>Vyčištění prostředků po operaci e-mailu

1. Pokud již sada Runbook není potřebná, vyberte ji v seznamu sady Runbook a klikněte na tlačítko **Odstranit**.

2. Odstraňte Key Vault pomocí rutiny [Remove-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0) .

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Další kroky

* Chcete-li odesílat data úloh sady Runbook do pracovního prostoru Log Analytics, přečtěte si téma [Dopředné Azure Automation data úloh do protokolů Azure monitor](automation-manage-send-joblogs-log-analytics.md).
* Pokud chcete monitorovat metriky a protokoly základní úrovně, přečtěte si téma [použití výstrahy ke spuštění sady runbook Azure Automation](automation-create-alert-triggered-runbook.md).
* Chcete-li opravit problémy vznikající během operací sady Runbook, přečtěte si téma [řešení potíží](./troubleshoot/runbooks.md)se sadou Runbook.