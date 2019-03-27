---
title: Vytvoření clusteru Azure Service Fabric pomocí běžný název certifikátu | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit cluster Service Fabric pomocí běžný název certifikátu ze šablony.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: aljo
ms.openlocfilehash: 5f2ba517e68dcd37de44bd26f476442e80817086
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498022"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Nasazení clusteru Service Fabric, která používá namísto kryptografický otisk certifikátu běžný název
Žádné dva certifikáty můžou mít se stejným kryptografickým otiskem, což znesnadňuje clusteru certifikáty vyměnit nebo správy. Více certifikátů, ale mají stejný běžný název nebo předmětu.  Clusteru běžnému názvu certifikátu pomocí certifikátu značně zjednodušuje správu. Tento článek popisuje, jak nasadit cluster Service Fabric běžný název certifikátu použít místo kryptografického otisku certifikátu.
 
## <a name="get-a-certificate"></a>Získání certifikátu
Nejprve Získejte certifikát [certifikační autority (CA)](https://wikipedia.org/wiki/Certificate_authority).  Běžný název certifikátu by měl být pro vlastní doménu vlastní a koupené z doménového registrátora. Například "azureservicefabricbestpractices.com"; názvy DNS LB nebo Traffic Manageru nejde použít jako běžnému názvu certifikátu, takže budete muset zřídit těmi, které nejsou zaměstnanci Microsoftu nejde zřídit certifikáty pro domény MS [zóny Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) Pokud vaší vlastní doména dát přeložit v Azure. Také můžete deklarovat ve vaší vlastní doméně, které vlastníte jako váš clusteru "managementEndpoint", pokud chcete portál tak, aby odrážely vlastní doména alias pro váš cluster.

Pro účely testování, může získat certifikát podepsaný certifikační Autoritou z bezplatné nebo otevřete certifikační autority.

> [!NOTE]
> Certifikáty podepsané svým držitelem, včetně těch, které generuje při nasazování clusteru Service Fabric na webu Azure Portal, se nepodporují. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Nahrajte certifikát do služby key vault
V Azure se nasadí cluster Service Fabric na škálovací sadu virtuálních počítačů.  Nahrajte certifikát do služby key vault.  Pokud nasadí clusteru, certifikát nainstaluje na škálovací sadu virtuálních počítačů, spuštěné v clusteru.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>Stažení a aktualizace Ukázková šablona
Tento článek používá [zabezpečeného clusteru s 5 uzly příklad](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) šablonu a parametry šablony. Stáhněte si *azuredeploy.json* a *azuredeploy.parameters.json* soubory do počítače.

### <a name="update-parameters-file"></a>Aktualizovat soubor parametrů
Nejprve otevřete *azuredeploy.parameters.json* v textovém editoru a přidejte následující hodnotu parametru:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Dále nastavte *certificateCommonName*, *sourceVaultValue*, a *certificateUrlValue* hodnoty parametrů těm, které jsou vrácené předchozím skriptu:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Aktualizace souboru šablony
Dále otevřete *azuredeploy.json* souboru v textovém editoru a provést tři aktualizace pro podporu běžný název certifikátu.

1. V **parametry** části, přidejte *certificateCommonName* parametr:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    ```

    Také zvažte odebrání *certificateThumbprint*, možná už nepotřebujete.

2. Nastavte hodnotu *sfrpApiVersion* proměnné "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. V **Microsoft.Compute/virtualMachineScaleSets** prostředků, aktualizujte rozšíření virtuálního počítače. Chcete-li použít běžný název v nastavení certifikátu místo kryptografického otisku.  V **virtualMachineProfile**->**extensionProfile**->**rozšíření**->**vlastnosti** -> **nastavení**->**certifikát**, přidat 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    a odebrat `"thumbprint": "[parameters('certificateThumbprint')]",`.

    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": [
                     "[parameters('certificateCommonName')]"
                  ],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4. V **Microsoft.ServiceFabric/clusters** prostředků, verze aktualizace rozhraní API pro "2018-02-01".  Také přidat **certificateCommonNames** nastavení **commonNames** vlastnost a odebrat **certifikát** nastavení (pomocí vlastnosti kryptografický otisk), viz následující příklad Příklad:
   ```json
   {
       "apiVersion": "2018-02-01",
       "type": "Microsoft.ServiceFabric/clusters",
       "name": "[parameters('clusterName')]",
       "location": "[parameters('clusterLocation')]",
       "dependsOn": [
       "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
       ],
       "properties": {
       "addonFeatures": [
           "DnsService",
           "RepairManager"
       ],        
       "certificateCommonNames": {
           "commonNames": [
           {
               "certificateCommonName": "[parameters('certificateCommonName')]",
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > Pole 'certificateIssuerThumbprint' umožňuje očekávané vystavitelů certifikátů s daným subjektem běžný název. Toto pole přijímá čárkami výčet kryptografických otisků, SHA1. Všimněte si, že jde o posílení ověření certifikátu – v případě, když není zadaný Vystavitel nebo prázdná, certifikát přijme pro ověřování, pokud jeho řetězci může být sestaven a končí v kořenové důvěryhodné validátorem. Pokud je zadaný vystavitel, certifikát přijme, pokud kryptografický otisk vystavitele přímé odpovídá některému z hodnoty zadané v tomto poli – bez ohledu na to, zda je kořen důvěryhodné nebo ne. Mějte prosím na paměti, že infrastruktura veřejných KLÍČŮ může používat odlišnými certifikačními autoritami k vydávání certifikátů pro stejnou předmět, a proto je důležité určit všechny kryptografické otisky očekávaný název vystavitele pro daného subjektu.
   >
   > Určení Vystavitel je považován za osvědčený postup; Při vynechání se budou dál fungovat – pro certifikáty řetězení pro důvěryhodného kořenového - toto chování má omezení a může být postupně v blízké budoucnosti. Všimněte si také, že clusterech nasazených v Azure a zabezpečený pomocí X509 certifikáty vydané soukromé infrastruktury veřejných KLÍČŮ a deklaroval subjektu nebudou moct ověřit ve službě Azure Service Fabric (pro komunikaci clusteru to-service), pokud se zásady certifikátu PKI. není k dispozici a vyhledatelnost multimediálních souborů. 

## <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony
Nasaďte aktualizovanou šablonu znovu po provedení změn.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Další postup
* Další informace o [clusteru zabezpečení](service-fabric-cluster-security.md).
* Zjistěte, jak [výměna certifikátů clusteru](service-fabric-cluster-rollover-cert-cn.md)
* [Aktualizace a správa certifikátů clusteru](service-fabric-cluster-security-update-certs-azure.md)
* Zjednodušení správy certifikátů ve [změna cluster kryptografický otisk certifikátu běžný název](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
