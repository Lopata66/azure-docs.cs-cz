---
title: Přehled virtuálních počítačů s Linuxem v Azure | Dokumentace Microsoftu
description: Popis služeb Azure Compute, Storage a síťové služby Azure s použitím virtuálních počítačů s Linuxem.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: gwallace
editor: ''
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/29/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 9d33b478cb848724d0b3747761a99a1269d58b6e
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671059"
---
# <a name="azure-and-linux"></a>Azure a Linux
Microsoft Azure je rostoucí kolekce integrovaných veřejných cloudových služeb včetně analýz, virtuálních počítačů, databází, mobilních a síťových služeb, úložiště a webu &mdash; je tak ideální pro hostování vašich řešení.  Microsoft Azure poskytuje škálovatelnou výpočetní platformu, s kterou můžete platit jenom za to, co používáte a když to potřebujete – nemusíte tak investovat do hardwaru místně ve své firmě.  Platforma Azure dokáže flexibilně reagovat na vaše potřeby. Můžete tak libovolně škálovat kapacitu (vertikálně i horizontálně) pro svá řešení podle požadavků svých klientů.

Pokud už znáte různé funkce Amazon AWS, můžete prozkoumat [dokument věnovaný mapování definic](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) Azure oproti AWS.

## <a name="regions"></a>Regions
Prostředky Microsoft Azure jsou distribuované mezi několik geografických oblastí po celém světě.  „Oblast“ představuje několik datacenter v jedné zeměpisné oblasti. Azure má v současnosti (k srpnu 2018) 42 obecně dostupných oblastí po celém světě a dalších 12 oblastí je oznámených – to je více globálních oblastí než nabízí jakýkoli jiný poskytovatel cloudu. Aktualizovaný seznam stávajících a nově oznámených oblastí najdete na následující stránce:

* [Oblasti Azure](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Dostupnost
Platforma Azure oznámila špičkovou smlouvu o úrovni služeb (SLA) s 99,9% dostupností pro samostatné instance virtuálních počítačů za předpokladu, že virtuální počítač nasadíte se službou Premium Storage pro všechny disky.  Aby se na vaše nasazení vztahovala standardní záruka 99,95% dostupnosti virtuálního počítače podle smlouvy SLA, stále je nutné nasadit alespoň dva virtuální počítače, které vaši úlohu spouští v rámci skupiny dostupnosti. Skupina dostupnosti zajistí, že vaše virtuální počítače jsou distribuované mezi několik domén selhání v datacentrech Azure a také nasazené na hostitele s různými časovými obdobími údržby. Úplná smlouva [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) vysvětluje garantovanou dostupnost Azure jako celku.

## <a name="managed-disks"></a>Spravované disky

Spravované disky se na pozadí starají o vytvoření a správu účtu služby Azure Storage za vás a zajišťují, že si nemusíte dělat starosti s omezením škálovatelnosti účtu úložiště. Zadáte velikost disku a úroveň výkonu (Standard nebo Premium) a Azure disk vytvoří a postará se o jeho správu. S využitím úložiště si nemusíte dělat starosti ani v případě, že přidáváte disky nebo vertikálně navyšujete a snižujete kapacitu virtuálního počítače. Pokud vytváříte nové virtuální počítače, [pomocí Azure CLI](quick-create-cli.md) nebo webu Azure Portal vytvořte virtuální počítače se spravovanými disky operačního systému a datovými disky. Pokud máte virtuální počítače s nespravovanými disky, můžete [virtuální počítače převést na využívání Spravovaných disků](convert-unmanaged-to-managed-disks.md).

V jednom účtu úložiště na oblast Azure můžete také spravovat vlastní image a vytvářet pomocí nich stovky virtuálních počítačů v rámci stejného předplatného. Další informace o spravovaných discích najdete v tématu [Přehled služby Managed Disks](../linux/managed-disks-overview.md).

## <a name="azure-virtual-machines--instances"></a>Virtuální počítače Azure a instance
Microsoft Azure podporuje spouštění mnoha oblíbených distribucí Linuxu, které poskytuje a udržuje řada partnerů.  Na webu Azure Marketplace najdete distribuce, jako jsou Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD a další. Microsoft aktivně spolupracuje s různými komunitami okolo Linuxu s cílem ještě více rozšířit seznam [distribucí Linuxu schválených pro Azure](endorsed-distros.md).

Pokud vaše upřednostňovaná distribuce Linuxu aktuálně není v galerii, můžete pro virtuální počítač použít vlastní distribuci Linuxu tak, že [vytvoříte a nahrajete VHD s Linuxem v Azure](create-upload-generic.md).

Virtuální počítače Azure umožňují pružně nasadit řadu různých výpočetních řešení. Můžete nasadit prakticky jakoukoli úlohu v jakémkoli jazyce na téměř jakýkoli operační systém – Windows, Linux nebo vlastní systém vytvořený některým z partnerů, jejichž seznam se stále rozšiřuje. Stále jste nenašli, co hledáte?  Buďte bez obav – můžete používat také své vlastní image z místního prostředí.

## <a name="vm-sizes"></a>Velikosti virtuálních počítačů
[Velikost](sizes.md) virtuálního počítače, který použijete, se určuje podle úlohy, kterou chcete spustit. Velikost, kterou vyberete, pak určuje další faktory, jako například výpočetní výkon, paměť a kapacitu úložiště. Azure nabízí širokou škálu velikostí, které podporují mnoho typů použití.

Azure účtuje [hodinovou cenu](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) na základě velikosti virtuálního počítače a na jeho operačním systému. V případě neúplných hodin Azure účtuje jenom využité minuty. Služba Storage je oceněna a účtována samostatně.

## <a name="automation"></a>Automation
K dosažení správné kultury DevOps musí být veškerá infrastruktura tvořená kódem.  Když je veškerá infrastruktura v kódu, je možné ji snadno vytvořit znovu (servery Phoenix).  Azure spolupracuje se všemi hlavními nástroji pro automatizaci, jako jsou Ansible, Chef, SaltStack a Puppet.  Azure obsahuje také vlastní nástroj pro automatizaci:

* [Šablony Azure](create-ssh-secured-vm-from-template.md)
* [Azure VMAccess](using-vmaccess-extension.md)

Azure postupně zavádí podporu nástroje [cloud-init](https://cloud-init.io/) ve většině distribucí Linuxu, které ho podporují.  Aktuálně se s povoleným nástrojem cloud-init ve výchozím nastavení nasazují virtuální počítače s Ubuntu od firmy Canonical.  RHEL od Red Hatu, CentOS i Fedora podporují cloud-init, ale v imagích Azure, které udržuje Red Hat, není aktuálně nástroj cloud-init nainstalovaný.  Pokud chcete používat cloud-init v operačních systémech rodiny Red Hat, musíte vytvořit vlastní image s nainstalovaným nástrojem cloud-init.

* [Použití cloud-init na virtuálních počítačích Azure s Linuxem](using-cloud-init.md)

## <a name="quotas"></a>Kvóty
Každé předplatné Azure má nastavené výchozí kvóty, které můžou ovlivnit nasazení velkého počtu virtuálních počítačů pro váš projekt. Aktuální limit jednoho předplatného je 20 virtuálních počítačů na oblast.  Limity kvót je možné rychle a snadno navýšit vyplněním lístku podpory s žádostí o navýšení limitu.  Další podrobnosti o limitech kvót:

* [Omezení služeb v předplatném Azure](../../azure-subscription-service-limits.md)

## <a name="partners"></a>Partneři
Microsoft úzce spolupracuje s partnery na zajištění aktualizace a optimalizace dostupných imagí pro modul runtime Azure.  Další informace o partnerech Azure najdete na následujících odkazech:

* Linux v Azure – [Schválené distribuce](endorsed-distros.md)
* SUSE – [Azure Marketplace – SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SLES?tab=Overview)
* Red Hat – [Azure Marketplace – Red Hat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical – [Azure Marketplace – Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian –[Azure Marketplace – Debian 8 „Jessie“](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD – [Azure Marketplace – FreeBSD 10.4](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
* CoreOS – [Azure Marketplace – CoreOS (stabilní verze)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS – [Azure Marketplace – RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami – [Knihovna Bitnami pro Azure](https://azure.bitnami.com/)
* Mesosphere – [Azure Marketplace – Mesosphere DC/OS v Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker – [Azure Marketplace – Azure Container Service s Dockerem Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins – [Azure Marketplace – Platforma CloudBees Jenkins](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudbees.cloudbees-core-contact)

## <a name="getting-started-with-linux-on-azure"></a>Začínáme s Linuxem v Azure
Abyste mohli začít používat Azure, potřebujete účet Azure, nainstalované Azure CLI a pár veřejného a privátního klíče SSH.

### <a name="sign-up-for-an-account"></a>Registrace účtu
Prvním krokem při používání cloudu Azure je registrace účtu Azure.  Začněte tím, že přejdete na stránku [Registrace účtu Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="install-the-cli"></a>Instalace rozhraní příkazového řádku
S novým účtem Azure můžete rovnou začít pracovat pomocí webu Azure Portal, což je webový panel pro správu.  Pokud chcete spravovat cloud Azure přes příkazový řádek, nainstalujte `azure-cli`.  Nainstalujte na svou pracovní stanici se systémem Mac nebo Linux [Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH
Nyní máte účet Azure, webový portál Azure a Azure CLI.  Dalším krokem je vytvoření páru klíčů SSH, který slouží k připojení k Linuxu přes SSH bez použití hesla.  [Vytvořte v systému Linux nebo Mac klíče SSH](mac-create-ssh-keys.md) a umožněte přihlašování bez hesla a lepší zabezpečení.

### <a name="create-a-vm-using-the-cli"></a>Vytvoření virtuálního počítače pomocí rozhraní příkazového řádku
Vytvoření virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku představuje rychlý způsob nasazení virtuálního počítače, aniž byste opustili terminál, ve kterém pracujete.  Vše, co můžete zadat na webovém portálu, je k dispozici i v příkazovém řádku prostřednictvím příznaku nebo přepínače.  

* [Vytvoření virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku](quick-create-cli.md)

### <a name="create-a-vm-in-the-portal"></a>Vytvoření virtuálního počítače na portálu
Vytvoření virtuálního počítače na webovém portálu Azure představuje snadný způsob, jak se můžete proklikat různými možnostmi až k nasazení.  Místo používání příznaků nebo přepínačů v příkazovém řádku můžete zobrazit pěkné webové rozhraní s různými možnostmi a nastaveními.  Vše, co je k dispozici prostřednictvím rozhraní příkazového řádku, je dostupné také na portálu.

* [Vytvoření virtuálního počítače s Linuxem pomocí portálu](quick-create-portal.md)

### <a name="log-in-using-ssh-without-a-password"></a>Přihlášení pomocí SSH bez hesla
Virtuální počítač je teď spuštěný v Azure a vy jste připraveni se k němu přihlásit.  Používání hesel při přihlašování přes SSH je riskantní a časově náročné.  Nejbezpečnější a zároveň nejrychlejší způsob přihlášení představuje použití klíčů SSH.  Při vytváření virtuálního počítače s Linuxem prostřednictvím portálu nebo rozhraní příkazového řádku máte na výběr dvě metody ověřování.  Pokud pro SSH zvolíte heslo, Azure nakonfiguruje virtuální počítač tak, aby umožňoval přihlašování pomocí hesel.  Pokud se rozhodnete použít veřejný klíč SSH, Azure nakonfiguruje virtuální počítač tak, aby umožňoval přihlašování pouze pomocí klíčů SSH a zakázal přihlašování pomocí hesel. Pokud chcete svůj virtuální počítač s Linuxem zabezpečit tím, že povolíte přihlašování pouze pomocí klíče SSH, při vytváření virtuálního počítače na portálu nebo v rozhraní příkazového řádku použijte možnost veřejného klíče SSH.

## <a name="related-azure-components"></a>Související komponenty Azure
## <a name="storage"></a>Storage
* [Úvod do Microsoft Azure Storage](../../storage/common/storage-introduction.md)
* [Přidání disku do virtuálního počítače s Linuxem pomocí Azure CLI](add-disk.md)
* [Postup připojení datového disku k virtuálnímu počítači s Linuxem na webu Azure Portal](attach-disk-portal.md)

## <a name="networking"></a>Sítě
* [Přehled služby Virtual Network](../../virtual-network/virtual-networks-overview.md)
* [IP adresy v Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Otevření portů k virtuálnímu počítači s Linuxem v Azure](nsg-quickstart.md)
* [Vytvoření plně kvalifikovaného názvu domény (FQDN) na webu Azure Portal](portal-create-fqdn.md)

## <a name="containers"></a>Containers
* [Virtuální počítače a kontejnery v Azure](containers.md)
* [Úvod do služby Azure Container Service](../../container-service/container-service-intro.md)
* [Nasazení clusteru Azure Container Service](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>Další postup
Nyní máte přehled o Linuxu v Azure.  Můžete se pustit do dalšího kroku a vytvořit několik virtuálních počítačů.

* [Prozkoumejte rozrůstající se seznam ukázkových skriptů pro běžné úlohy prostřednictvím Azure CLI](cli-samples.md).
