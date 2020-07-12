---
title: Šifrování na straně serveru Azure Managed Disks – PowerShell
description: Azure Storage chrání vaše data tím, že je před tím, než je zachová v clusterech úložiště, v klidovém prostředí. Pro šifrování svých spravovaných disků můžete spoléhat na klíče spravované Microsoftem, případně můžete pomocí klíčů spravovaných zákazníkem spravovat šifrování pomocí vlastních klíčů.
author: roygara
ms.date: 07/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 0bb0fb268d18ddc152dae45014e2154686762976
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259820"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Šifrování na straně serveru Azure Disk Storage

Šifrování na straně serveru (SSE) chrání vaše data a pomáhá splnit závazky zabezpečení a dodržování předpisů vaší organizace. SSE automaticky šifruje vaše data uložená na spravovaných discích Azure (s operačním systémem a datových discích) ve výchozím nastavení při uchování do cloudu. 

Data ve službě Azure Managed disks jsou transparentně šifrovaná pomocí 256 [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), což je jedna z nejúčinnějších šifrovacích šifr, která jsou kompatibilní se standardem FIPS 140-2. Další informace o kryptografických modulech založených na službě Azure Managed disks najdete v tématu [kryptografie API: Next Generation.](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

Šifrování na straně serveru nemá vliv na výkon spravovaných disků a neplatí žádné další náklady. 

> [!NOTE]
> Dočasné disky nejsou spravované disky a nejsou zašifrované pomocí SSE, pokud na hostiteli nepovolíte šifrování.

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Pro šifrování spravovaného disku můžete spoléhat na klíče spravované platformou, nebo můžete šifrování spravovat pomocí vlastních klíčů. Pokud se rozhodnete spravovat šifrování pomocí vlastních klíčů, můžete zadat *klíč spravovaný zákazníkem* , který se použije k šifrování a dešifrování všech dat ve službě Managed disks. 

V následujících částech jsou podrobněji popsány všechny možnosti správy klíčů.

### <a name="platform-managed-keys"></a>Klíče spravované platformou

Ve výchozím nastavení používají spravované disky šifrovací klíče spravované platformou. Od 10. června 2017 se všechny nové spravované disky, snímky, image a nová data zapsaná na stávající spravované disky automaticky zašifrují bez použití klíčů spravovaných platformou.

### <a name="customer-managed-keys"></a>Klíče spravované zákazníkem

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Omezení

Klíče spravované zákazníkem teď mají následující omezení:

- Pokud je tato funkce pro disk povolená, nemůžete ji zakázat.
    Pokud potřebujete tento problém obejít, musíte [zkopírovat všechna data](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) na zcela jiný spravovaný disk, který nepoužívá klíče spravované zákazníkem.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Šifrování v rámci hostitele – komplexní šifrování pro data virtuálních počítačů

Komplexní šifrování začíná z hostitele virtuálního počítače, na kterém je server Azure, ke kterému je váš virtuální počítač přiřazený. Data na dočasných discích a v mezipamětích disků s operačním systémem a daty se ukládají na tomto hostiteli virtuálních počítačů. Pokud povolíte komplexní šifrování, všechna tato data budou v klidovém stavu zašifrovaná a toky se zašifrují do služby úložiště, kde jsou trvalé. Komplexní šifrování nepoužívá procesor vašeho virtuálního počítače a nemá vliv na výkon vašeho virtuálního počítače. 

Dočasné disky jsou v klidovém stavu zašifrované pomocí klíčů spravovaných platformou, když povolíte komplexní šifrování. Mezipaměti operačního systému a datových disků jsou v klidovém stavu zašifrované buď pomocí klíčů spravovaných zákazníkem nebo platformou, v závislosti na typu šifrování. Pokud je například disk zašifrovaný pomocí klíčů spravovaných zákazníkem, pak je mezipaměť pro disk zašifrovaná pomocí klíčů spravovaných zákazníkem a pokud je disk zašifrovaný pomocí klíčů spravovaných platformou, bude mezipaměť pro disk zašifrovaná pomocí klíčů spravovaných platformou.

### <a name="restrictions"></a>Omezení

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>Podporované oblasti

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>Podporované velikosti virtuálních počítačů

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="double-encryption-at-rest"></a>Dvojité šifrování v klidovém umístění

Zákazníci s vysokým zabezpečením, kteří se týkají rizik spojených s jakýmkoli konkrétním šifrovacím algoritmem, implementací nebo klíčem, se teď můžou rozhodnout pro další vrstvu šifrování pomocí jiného šifrovacího algoritmu nebo režimu v infrastruktuře, který používá šifrovací klíče spravované platformou. Tato nová vrstva se dá použít na disky, snímky a image, které se zašifrují v klidovém stavu s dvojitým šifrováním.

### <a name="supported-regions"></a>Podporované oblasti

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Šifrování na straně serveru oproti službě Azure Disk Encryption

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) využívá funkci [nástroje BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) systému Windows k šifrování spravovaných disků pomocí klíčů spravovaných zákazníkem v rámci virtuálního počítače hosta.  Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem se zlepšuje v ADE tím, že vám umožní používat pro vaše virtuální počítače jakékoli typy operačních systémů a image šifrováním dat ve službě úložiště.

> [!IMPORTANT]
> Klíče spravované zákazníkem spoléhají na spravované identity prostředků Azure, což je funkce Azure Active Directory (Azure AD). Při konfiguraci klíčů spravovaných zákazníkem se spravovaná identita automaticky přiřadí k vašim prostředkům v rámci pokrývání. Pokud později přesunete předplatné, skupinu prostředků nebo spravovaný disk z jednoho adresáře služby Azure AD do jiného, nepřesune se do nového tenanta spravovaná identita přidružená ke spravovaným diskům, takže klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu [přenos předplatného mezi adresáři služby Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).


## <a name="next-steps"></a>Další kroky

- [Povolení kompletního šifrování pomocí šifrování v hostiteli – PowerShell](disks-enable-host-based-encryption-powershell.md)
- [Povolení dvojitého šifrování v klidovém prostředí pro vaše spravované disky – PowerShell](disks-enable-double-encryption-at-rest-powershell.md)
- [Povolení klíčů spravovaných zákazníkem pro vaše spravované disky – PowerShell](disks-enable-customer-managed-keys-powershell.md)
- [Povolení klíčů spravovaných zákazníkem pro vaše spravované disky – portál](disks-enable-customer-managed-keys-portal.md)
- [Prozkoumejte šablony Azure Resource Manager pro vytváření šifrovaných disků pomocí klíčů spravovaných zákazníkem](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Co je Azure Key Vault?](../../key-vault/general/overview.md)
