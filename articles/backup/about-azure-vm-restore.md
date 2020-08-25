---
title: O procesu obnovení virtuálního počítače Azure
description: Přečtěte si, jak služba Azure Backup obnovuje virtuální počítače Azure.
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: fb3f6bf729047dc6fe1c13c5ead1191188e30e5c
ms.sourcegitcommit: f1b18ade73082f12fa8f62f913255a7d3a7e42d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2020
ms.locfileid: "88761385"
---
# <a name="about-azure-vm-restore"></a>Informace o obnovení virtuálních počítačů Azure

Tento článek popisuje, jak [služba Azure Backup](./backup-overview.md) obnovuje virtuální počítače Azure. K dispozici je několik možností obnovení. Budeme pojednávat o různých scénářích, které podporují.

## <a name="concepts"></a>Koncepty

- **Bod obnovení** (označovaný také jako **bod obnovení**): bod obnovení je kopie původních zálohovaných dat.

- **Úroveň (snímek vs. trezor)**: zálohování virtuálních počítačů Azure se děje ve dvou fázích:

  - Ve fázi 1 se pořízené snímky ukládají společně s diskem. Označuje se jako **vrstva snímku**. Obnovení vrstev snímků je rychlejší (než obnovení z trezoru), protože eliminují dobu čekání, než se snímky nakopírují do trezoru před aktivací obnovení. Proto se obnovení z vrstvy snímku označuje také jako [okamžité obnovení](./backup-instant-restore-capability.md).
  - Ve fázi 2 se snímek přenáší a uloží do trezoru spravovaného službou Azure Backup. Tento postup se označuje jako **vrstva trezoru**.

- **Obnovení původního umístění (OLR)**: obnovení z bodu obnovení na ZDROJovém virtuálním počítači Azure, ze kterého se prováděly zálohy, a nahradí je stavem uloženým v bodu obnovení. Tím se nahradí disk s operačním systémem a datovými disky zdrojového virtuálního počítače.

- **ALR (alternativní umístění)**: obnovení provedené z bodu obnovení na jiném serveru, než je původní server, na kterém byly zálohy pořízeny.

- **Obnovení na úrovni položek (ilr):** Obnovení jednotlivých souborů nebo složek uvnitř virtuálního počítače z bodu obnovení

- **Dostupnost (typy replikace)**: Azure Backup nabízí dva typy replikace k zajištění vysoké dostupnosti úložiště/dat:
  - [Místně redundantní úložiště (LRS)](../storage/common/storage-redundancy.md) replikuje vaše data třikrát (vytváří tři kopie dat) v jednotce škálování úložiště v datacentru. Všechny kopie dat existují ve stejné oblasti. Místně redundantní úložiště nabízí cenově úsporný způsob ochrany dat před selháním místního hardwaru.
  - [Geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy.md) je výchozí a doporučená možnost replikace. GRS replikuje vaše data do sekundární oblasti (vzdálené stovky kilometrů od primárního umístění zdrojových dat). GRS náklady jsou větší než LRS, ale GRS poskytuje vyšší úroveň trvanlivosti vašich dat, i když dojde k oblastnímu výpadku.

- **Obnovení mezi oblastmi (crr)**: jako jedna z [možností obnovení](./backup-azure-arm-restore-vms.md#restore-options), obnovení mezi oblastmi (crr) umožňuje obnovení virtuálních počítačů Azure v sekundární oblasti, která je [spárována s Azure](../best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="restore-scenarios"></a>Scénáře obnovení

![Scénáře obnovení ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **Scénář**                                                 | **Co se děje**                                             | **Kdy je použít**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Obnovit pro vytvoření nového virtuálního počítače](./backup-azure-arm-restore-vms.md) | Obnoví celý virtuální počítač na OLR (Pokud zdrojový virtuální počítač ještě existuje) nebo ALR | <li> Pokud dojde ke ztrátě nebo poškození zdrojového virtuálního počítače, můžete obnovit celý virtuální počítač.  <li> Můžete vytvořit kopii virtuálního počítače.  <li> Můžete provést obnovení s podrobnostmi o auditu nebo dodržování předpisů.  <li> Tato možnost nebude fungovat pro virtuální počítače Azure vytvořené z imagí z Marketplace (to znamená, že nejsou k dispozici, protože platnost licence vypršela). |
| [Obnovení disků virtuálního počítače](./backup-azure-arm-restore-vms.md#restore-disks) | Obnovit disky připojené k virtuálnímu počítači                             |  Všechny disky: Tato možnost vytvoří šablonu a obnoví disk. Tuto šablonu můžete upravit se speciální konfigurací (například skupinami dostupnosti), aby splňovala vaše požadavky, a pak pomocí šablony a obnovením disku znovu vytvořit virtuální počítač. |
| [Obnovte konkrétní soubory v rámci virtuálního počítače.](./backup-azure-restore-files-from-vm.md) | Zvolte bod obnovení, Procházet, vyberte soubory a obnovte je do stejného (nebo kompatibilního) operačního systému jako zálohovaný virtuální počítač. |  Pokud víte, které konkrétní soubory se mají obnovit, použijte tuto možnost namísto obnovení celého virtuálního počítače. |
| [Obnovení šifrovaného virtuálního počítače](./backup-azure-vms-encryption.md) | Z portálu obnovte disky a pak použijte PowerShell k vytvoření virtuálního počítače. | <li> [Šifrovaný virtuální počítač s Azure Active Directory (AAD)](../virtual-machines/windows/disk-encryption-windows-aad.md)  <li> [Zašifrovaný virtuální počítač bez AAD](../virtual-machines/windows/disk-encryption-windows.md) <li> [Zašifrovaný virtuální počítač *s AAD* migrován do *bez AAD*](../virtual-machines/windows/disk-encryption-faq.md#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app) |
| [Obnovení mezi oblastmi](./backup-azure-arm-restore-vms.md#cross-region-restore) | Vytvoření nového virtuálního počítače nebo obnovení disků do sekundární oblasti (spárovaná oblast Azure) | <li> **Úplný výpadek**: Díky funkci obnovení mezi oblastmi není doba čekání na obnovu dat v sekundární oblasti k dispozici. Obnovení v sekundární oblasti můžete zahájit i před tím, než Azure deklaruje výpadek. <li> **Částečný výpadek**: výpadky se můžou vyskytovat v konkrétních clusterech úložiště, kde Azure Backup ukládá vaše Zálohovaná data nebo dokonce i v síti, propojuje Azure Backup a clustery úložiště přidružené k zálohovaným datům. Díky obnovení mezi oblastmi můžete provést obnovení v sekundární oblasti pomocí repliky zálohovaných dat v sekundární oblasti. <li> **Bez výpadku**: pro účely auditování a dodržování předpisů pomocí dat sekundární oblasti můžete využít podrobné analýzy pro provozní kontinuitu a zotavení po havárii (BCDR). Díky tomu můžete provést obnovení zálohovaných dat v sekundární oblasti i v případě, že v primární oblasti nehrozí úplný nebo částečný výpadek pro provozní kontinuitu a přechody na zotavení po havárii.  |

## <a name="next-steps"></a>Další kroky

- [Nejčastější dotazy týkající se obnovení virtuálního počítače](./backup-azure-vm-backup-faq.md#restore)
- [Podporované metody obnovení](./backup-support-matrix-iaas.md#supported-restore-methods)
- [Řešení potíží s obnovením](./backup-azure-vms-troubleshoot.md#restore)
