---
title: Azure Disk Encryption pro Windows
description: Nasadí Azure Disk Encryption na virtuální počítač s Windows pomocí rozšíření virtuálního počítače.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: e975e1757b77b4aab52a59d1f0709ef9cadae94e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80066867"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption pro Windows (Microsoft. Azure. Security. AzureDiskEncryption)

## <a name="overview"></a>Přehled

Azure Disk Encryption využívá BitLocker k poskytování úplného šifrování disků na virtuálních počítačích Azure s Windows.  Toto řešení je integrované s Azure Key Vault pro správu klíčů a tajných kódů disků v předplatném trezoru klíčů. 

## <a name="prerequisites"></a>Požadavky

Úplný seznam požadovaných součástí najdete v tématu [Azure Disk Encryption pro virtuální počítače s Windows](../windows/disk-encryption-overview.md), konkrétně v následujících oddílech:

- [Podporované virtuální počítače a operační systémy](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Požadavky na síť](../windows/disk-encryption-overview.md#networking-requirements)
- [Zásady skupiny požadavky](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Schéma rozšíření

Existují dvě verze schématu rozšíření pro Azure Disk Encryption (ADE):
- v 2.2 – novější Doporučené schéma, které nepoužívá vlastnosti Azure Active Directory (AAD).
- v 1.1 – starší schéma, které vyžaduje vlastnosti Azure Active Directory (AAD). 

Chcete-li vybrat cílové schéma, `typeHandlerVersion` musí být vlastnost nastavena na hodnotu stejné jako verze schématu, kterou chcete použít.

### <a name="schema-v22-no-aad-recommended"></a>Schéma v 2.2: žádné AAD (doporučeno)

Schéma v 2.2 se doporučuje pro všechny nové virtuální počítače a nevyžaduje Azure Active Directory vlastnosti.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KekVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Schéma v 1.1: s AAD 

Schéma 1,1 vyžaduje `aadClientID` a `aadClientSecret` `AADClientCertificate` není doporučeno pro nové virtuální počítače.

Používá se `aadClientSecret` :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

Používá se `AADClientCertificate` :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Hodnoty vlastností

| Name | Hodnota/příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| vydavatel | Microsoft. Azure. Security | řetězec |
| typ | AzureDiskEncryption | řetězec |
| typeHandlerVersion | 2,2, 1,1 | řetězec |
| (schéma 1,1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (schéma 1,1) AADClientSecret | heslo | řetězec |
| (schéma 1,1) AADClientCertificate | kryptografický | řetězec |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | řetězec | 
| (volitelné – výchozí RSA – výplně OAEP) KeyEncryptionAlgorithm | ' RSA-VÝPLNĚ OAEP ', ' RSA-VÝPLNĚ OAEP-256 ', ' RSA1_5 ' | řetězec |
| KeyVaultURL | url | řetězec |
| KeyVaultResourceId | url | řetězec |
| volitelné KeyEncryptionKeyURL | url | řetězec |
| volitelné KekVaultResourceId | url | řetězec |
| volitelné SequenceVersion | uniqueidentifier | řetězec |
| VolumeType | Operační systém, data, vše | řetězec |

## <a name="template-deployment"></a>Nasazení šablon

Příklad nasazení šablony založeného na schématu v 2.2 najdete v článku Azure pro rychlý Start [– šablona 201-Encrypting-running-Windows-VM-bez AAD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).

Příklad nasazení šablony založeného na schématu v 1.1 najdete v tématu Azure Starter template [201-Encrypt-running-Windows-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

>[!NOTE]
> I když `VolumeType` je parametr nastaven na hodnotu All, datové disky budou zašifrovány pouze v případě, že jsou správně naformátovány. 

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Informace o řešení potíží najdete v [Průvodci odstraňováním potíží s Azure Disk Encryption](../windows/disk-encryption-troubleshooting.md).

### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/community/). 

Případně můžete zasouborovat incident podpory Azure. Přejít na [podporu Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky

* Další informace o rozšířeních najdete v tématu [rozšíření virtuálních počítačů a funkce pro Windows](features-windows.md).
* Další informace o Azure Disk Encryption pro Windows najdete v tématu [virtuální počítače s Windows](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines).
