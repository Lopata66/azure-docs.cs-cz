---
title: Šifrování služby Azure Storage pro neaktivní uložená data | Dokumentace Microsoftu
description: Pomocí funkce Azure Storage Service Encryption pro šifrování Azure Managed Disks, úložiště objektů Blob v Azure, soubory Azure, Azure Queue storage a Azure Table storage na straně služby při ukládání dat a při získávání data dešifrovat.
services: storage
author: lakasa
ms.service: storage
ms.topic: article
ms.date: 08/01/2018
ms.author: lakasa
ms.component: common
ms.openlocfilehash: a19cdac54fff641f72d0628c7b0d47060baba9b4
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384118"
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Šifrování služby Azure Storage pro neaktivní uložená data
Šifrování služby Azure Storage pro neaktivní uložená data vám pomůže chránit vaše data pro splnění vaší organizace na zabezpečení a závazky dodržování předpisů. Pomocí této funkce platformy Azure storage automaticky šifruje vaše data před uložením do Azure Managed Disks, tabulky, fronty nebo Azure Blob storage nebo Azure Files a dešifruje před načítání. Správa šifrování, šifrování neaktivních uložených dat, dešifrování a správu klíčů v šifrování služby Storage je pro uživatele transparentní. Všechna data zapsaná do platformy úložiště Azure je zašifrovaná pomocí 256bitových [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednu z nejsilnějších bloku šifer k dispozici.

Šifrování služby Storage je povolená pro všechny účty nová a existující úložiště a nejde zakázat. Vzhledem k tomu, že ve výchozím nastavení jsou vaše data zabezpečená, není nutné upravovat kód vaší aplikace a chcete využít výhod šifrování služby Storage.

Tato funkce automaticky šifruje data na:

- Služby Azure storage:
    - Azure Managed Disks
    - Azure Blob Storage
    - Soubory Azure
    - Azure Queue storage
    - Azure Table storage.  
- Obě úrovně výkonu (Standard a Premium).
- Oba modely nasazení (Azure Resource Manageru a Klasický model).

Šifrování služby Storage nemá vliv na výkon služby Azure storage.

Můžete použít šifrovací klíče s Šifrováním služby Storage spravované Microsoftem nebo vlastní šifrovací klíče. Další informace o použití vlastních klíčů najdete v tématu [šifrování služby Storage pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Nastavení šifrování zobrazení na webu Azure Portal
Chcete-li zobrazit nastavení pro šifrování služby Storage, přihlaste se k [webu Azure portal](https://portal.azure.com) a vyberte účet úložiště. V **nastavení** podokně, vyberte **šifrování** nastavení.

![Portálu snímek obrazovky zobrazující nastavení šifrování](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Nejčastější dotazy k šifrování služby Storage
**Jak šifrují data v účtu úložiště Resource Manageru?**  
Šifrování služby Storage je povolená pro všechny účty úložiště – classic a Resource Manageru, veškeré stávající soubory v účtu úložiště, vytvořili před povolením šifrování se zpětně získat zašifrovaná pomocí šifrování proces na pozadí.

**Šifrování služby Storage zapnutá ve výchozím nastavení při vytváření účtu úložiště?**  
Ano, je povolené šifrování služby úložiště pro všechny účty úložiště a pro všechny služby Azure storage.

**Mám účtu úložiště Resource Manageru. Můžete povolit šifrování služby Storage na něm?**  
Ve výchozím nastavení na všechny existující účty úložiště Resource Manageru je povoleno šifrování služby Storage. To platí pro Azure Blob storage, Azure Files a Azure Queue storage, Table storage. 

**Můžete zakázat šifrování na svůj účet úložiště?**  
Ve výchozím nastavení je povolené šifrování, a není možné zakázat šifrování pro účet úložiště. 

**Jak Azure Storage stát Pokud je povolené šifrování služby Storage?**  
Se neúčtují žádné další poplatky.

**Můžete použít vlastní šifrovací klíče?**  
Pro úložiště objektů Blob v Azure a službou soubory Azure Ano, můžete použít vlastní šifrovací klíče. Klíče spravované zákazníkem nejsou aktuálně podporovány službou Azure Managed Disks. Další informace najdete v tématu [šifrování služby Storage pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

**Můžete odvolat přístup k šifrovacím klíčům?**  
Ano, pokud jste [pomocí vlastních šifrovacích klíčů](storage-service-encryption-customer-managed-keys.md) ve službě Azure Key Vault.

**Čím se liší od Azure Disk Encryption šifrování služby Storage?**  
Azure Disk Encryption poskytuje integraci řešení na základě operačního systému, jako je BitLocker a DM-Crypt a Azure Key Vaultu. Šifrování služby Storage poskytuje šifrování nativně ve vrstvě platformy Azure storage, níže virtuálního počítače.

**Mám klasický účet úložiště. Můžete povolit šifrování služby Storage na něm?**  
Šifrování služby Storage je povolená pro všechny účty úložiště (classic a Resource Manager).

**Jak můžete šifrovat data ve svém účtu klasického úložiště?**  
S šifrováním ve výchozím nastavení povolené všechna data uložená ve službě Azure storage services automaticky šifrována. 

**Můžete vytvořit účty úložiště pomocí šifrování služby Storage povolit pomocí Azure Powershellu a rozhraní příkazového řádku Azure?**  
V době vytváření všechny účty úložiště ve výchozím nastavení povoleno šifrování služby Storage (classic nebo Resource Manager). Vlastnosti účtu můžete ověřit pomocí Azure Powershellu a rozhraní příkazového řádku Azure.

**Svůj účet úložiště je nastavený na geo redundantně replikaci. Pomocí šifrování služby Storage budou Moje redundantní kopie také zašifrovaná?**  
Ano, jsou šifrovány veškeré kopie tohoto účtu úložiště. Všechny redundance možnosti jsou podporovány – místně redundantní úložiště, zónově redundantní úložiště, geograficky redundantní úložiště a geograficky redundantní úložiště jen pro čtení.

**Je pouze v konkrétní oblasti povolené šifrování služby Storage?**  
Šifrování služby Storage je k dispozici ve všech oblastech.

**Storage Service Encryption FIPS 140-2 je kompatibilní?**  
Ano, je šifrování služby Storage podle standardu FIPS 140-2 kompatibilní. Další informace o kryptografických modulů základní šifrování služby Storage najdete v tématu [rozhraní API kryptografických služeb: Další generace](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

**Jak se mám obrátit někdo Pokud můžu mít jakékoli problémy nebo chcete poskytnout zpětnou vazbu?**  
Kontakt [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) pro nějaké problémy nebo zpětnou vazbu související s šifrování služby Storage.

## <a name="next-steps"></a>Další postup
Azure Storage nabízí že komplexní sadu funkcí zabezpečení tohoto vám společně pomohou vývojářům vytvářet zabezpečené aplikace. Další informace najdete v tématu [Příručka zabezpečení úložiště](../storage-security-guide.md).
