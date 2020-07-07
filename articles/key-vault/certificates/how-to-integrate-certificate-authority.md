---
title: Integrace služby Key Vault s certifikační autoritou DigiCert
description: Jak integrovat Key Vault s certifikační autoritou DigiCert
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: 7627625a917a8f652da62d4197368f023ad8c110
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964494"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>Integrace služby Key Vault s certifikační autoritou DigiCert

Azure Key Vault umožňuje snadno zřídit, spravovat a nasazovat digitální certifikáty pro vaši síť a povolit zabezpečenou komunikaci pro aplikace. Digitální certifikát je elektronické přihlašovací údaje, které slouží k vytvoření důkazu identity v elektronické transakci. 

Uživatelé služby Azure Key Recovery můžou certifikáty DigiCert vygenerovat přímo z jejich Key Vault. Key Vault by zajistilo ucelenou správu životního cyklu certifikátů pro certifikáty vydávané DigiCert prostřednictvím důvěryhodného partnerství Key Vault s certifikační autoritou DigiCert.

Obecnější informace o certifikátech najdete v tématu [Azure Key Vault certifikátů](/azure/key-vault/certificates/about-certificates).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení této příručky musíte mít následující prostředky.
* Trezor klíčů. Můžete použít existující Trezor klíčů nebo vytvořit nový pomocí následujících kroků v jednom z těchto rychlých startů:
   - [Vytvoření trezoru klíčů pomocí Azure CLI](../secrets/quick-create-cli.md)
   - [Vytvoření trezoru klíčů pomocí Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Vytvořte Trezor klíčů pomocí Azure Portal](../secrets/quick-create-portal.md).
*   Musíte aktivovat účet DigiCert CertCentral. [Zaregistrujte](https://www.digicert.com/account/signup/) si účet CertCentral.
*   Oprávnění na úrovni správce v účtech.


### <a name="before-you-begin"></a>Než začnete

Ujistěte se, že máte následující informace užitečné z účtu DigiCert CertCentral:
-   ID účtu CertCentral
-   ID organizace
-   Klíč rozhraní API

## <a name="adding-certificate-authority-in-key-vault"></a>Přidání certifikační autority v Key Vault 
Po shromáždění výše uvedených informací z účtu DigiCert CertCentral teď můžete přidat DigiCert do seznamu certifikačních autorit v trezoru klíčů.

### <a name="azure-portal"></a>portál Azure

1.  Pokud chcete přidat certifikační autoritu DigiCert, přejděte do trezoru klíčů, do kterého chcete přidat DigiCert. 
2.  Na stránkách Key Vault vlastnosti vyberte **certifikáty**.
3.  Vyberte kartu **certifikační autority** . ![ Vlastnosti certifikátu](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  Vyberte **Přidat** možnost.
 ![Vlastnosti certifikátu](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  Na obrazovce **Vytvoření certifikační autority** vyberte následující hodnoty:
    -   **Název**: Přidejte název vystavitele, který chcete identifikovat. Příklad DigicertCA
    -   **Zprostředkovatel**: z nabídky vyberte DigiCert.
    -   **ID účtu**: Zadejte ID účtu DigiCert CertCentral.
    -   **Heslo účtu**: Zadejte klíč rozhraní API, který jste vygenerovali v účtu DigiCert CertCentral.
    -   **ID organizace**: zadejte OrgID shromážděné z účtu DigiCert CertCentral 
    -   Klikněte na **Vytvořit**.
   
6.  Uvidíte, že v seznamu certifikačních autorit se teď přidalo DigicertCA.


### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell slouží k vytváření a správě prostředků Azure pomocí příkazů nebo skriptů. Hostitelé Azure Azure Cloud Shell interaktivní prostředí prostředí, které můžete použít v Azure Portal v samotném prohlížeči.

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento kurz Azure PowerShell verze modulu 1.0.0 nebo novější. `$PSVersionTable.PSVersion`Pro nalezení verze zadejte. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzAccount` pro vytvoření připojení k Azure.

```azurepowershell-interactive
Login-AzAccount
```

1.  Vytvoření **skupiny prostředků**

Vytvořte skupinu prostředků Azure pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. Vytvoření **Key Vault**

Pro váš Trezor klíčů je nutné použít jedinečný název. Tady je název "contoso-trezor", Key Vault v rámci této příručky.

- **Název trezoru** Contoso-trezor.
- **Název skupiny prostředků** ContosoResourceGroup.
- **Umístění** EastUS.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. Definujte proměnné pro informace shromážděné z účtu DigiCert CertCentral.

- Definovat proměnnou **ID účtu**
- Definovat proměnnou **ID organizace**
- Definovat **klíčovou proměnnou rozhraní API**
- Definovat proměnnou **názvu vystavitele**

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzureKeyVaultCertificateOrganizationDetails -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
$issuerName = "DigiCertCA"
```

4. Nastavit **vystavitele** Tím se do trezoru klíčů přidá DigiCert jako certifikační autorita.
```azurepowershell-interactive
Set-AzureKeyVaultCertificateIssuer -VaultName $vaultName -IssuerName $issuerName -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org
```

5. **Nastavení zásad pro certifikát a vystavení certifikátu** z DigiCert přímo v Key Vault.

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName DigiCertCA -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Certifikát byl teď úspěšně vydaný certifikační autoritou DigiCert v zadaném Key Vault prostřednictvím této integrace.

## <a name="troubleshoot"></a>Řešení potíží

Pokud je certifikát vystavený ve Azure Portal stav zakázáno, přejděte k části zobrazení **certifikátu** a zkontrolujte, jestli je v něm zobrazená chybová zpráva DigiCert.

 ![Vlastnosti certifikátu](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

Další informace najdete v referenčních informacích o [operacích certifikátu v REST API Key Vault](/rest/api/keyvault). Informace o tom, jak vytvářet oprávnění, najdete v tématu [trezory – vytvoření nebo aktualizace](/rest/api/keyvault/vaults/createorupdate) a [trezory – zásady přístupu pro aktualizaci](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Další kroky

- [Ověřování, žádosti a odpovědi](../general/authentication-requests-and-responses.md)
- [Průvodce vývojáře pro službu Key Vault](../general/developers-guide.md)
