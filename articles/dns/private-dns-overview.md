---
title: Použití Azure DNS pro privátní domény
description: Přehled privátní DNS, který hostuje službu v Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 3/1/2019
ms.author: victorh
ms.openlocfilehash: 7f5f377f34a43dfb01ea516e023bb98f118d0dd4
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247218"
---
# <a name="use-azure-dns-for-private-domains"></a>Použití Azure DNS pro privátní domény

Domain Name System nebo DNS, je zodpovědný za převod (nebo řešení) název služby na jeho IP adresu. Hostitelská služba určená pro domény DNS, poskytuje Azure DNS překlad názvů s využitím infrastruktury Microsoft Azure. Kromě podpory domén DNS směřujících k Internetu, Azure DNS teď podporuje také privátní DNS domény jako funkci ve verzi preview.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Azure DNS poskytuje spolehlivou a zabezpečenou službu DNS pro správu a řešení názvů domén ve virtuální síti, aniž byste museli přidat vlastního řešení DNS. S využitím privátních zón DNS, můžete použít vlastní názvy vlastních domén, místo ještě dnes dostupných názvů poskytovaných Azure. Pomocí vlastních názvů domén vám umožní přizpůsobit Architektura virtuální sítě podle potřeb vaší organizace. Nabízí řešení názvů pro virtuální počítače (VM) v rámci virtuální sítě a mezi virtuálními sítěmi. Navíc můžete konfigurovat názvy zón s rozděleným zobrazením, což umožňuje, aby privátní a veřejné zóny DNS název sdílené složky.

Když chcete publikovat privátní zónu DNS do virtuální sítě, zadáte seznam virtuálních sítí, které mají povoleno překládat záznamy v rámci této zóny. Ty označujeme jako *virtuální sítě pro překlad*. Můžete také zadat virtuální síť, pro kterou bude Azure DNS uchovávat záznamy názvů hostitelů při každém vytvoření virtuálního počítače, změně jeho IP adresy nebo jeho odstranění. Tu označujeme jako *registrační virtuální síť*.

Pokud chcete zadat registrační virtuální síť, záznamy DNS pro virtuální počítače z této virtuální sítě, které jsou registrovány k privátní zóně nejsou zobrazitelné nebo získat z prostředí Azure Powershell a rozhraní API pro rozhraní příkazového řádku Azure, ale záznamy virtuálního počítače jsou ve skutečnosti zaregistrované a bude úspěšně vyřešte.

![Přehled systému DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> As a best practice, do not use a .local domain for your private DNS zone. Všechny operační systémy podporují.

## <a name="benefits"></a>Výhody

Azure DNS poskytuje následující výhody:

* **Eliminuje nutnost vlastního řešení DNS**. Mnoho zákazníků vytvořili vlastní DNS řešení ke správě zón DNS v jejich virtuální sítě. Můžete teď provádět správa zóny DNS pomocí nativní infrastrukturu Azure, který odstraní starosti související s vytvořením a správou vlastního řešení DNS.

* **Použít všechny běžné typy záznamů DNS**. Azure DNS podporuje záznamy A, AAAA, CNAME, MX, PTR, SOA, SRV a TXT.

* **Správa záznamů automatické hostname**. Spolu s hostitelem vašich vlastních záznamů DNS, Azure automaticky uchovávat záznamy názvů hostitelů pro virtuální počítače v zadané virtuální sítě. V tomto scénáři můžete optimalizovat názvy domén, které používáte, bez nutnosti vytvoření vlastního řešení DNS nebo úprava aplikací.

* **Překlad názvů hostitelů mezi virtuálními sítěmi**. Na rozdíl od názvů hostitelů poskytuje Azure mohou být sdíleny privátních zón DNS virtuální sítě. Tato možnost zjednodušuje scénáře různé sítě a zjišťování služby, třeba partnerský vztah virtuální sítě.

* **Známé nástroje a činnost koncových uživatelů**. Ke snížení učit se, používá tato nová nabídka zavedené nástroje Azure DNS (PowerShell, šablony Azure Resource Manageru a rozhraní REST API).

* **Podpora DNS zobrazením Split-horizon**. S Azure DNS můžete vytvořit zóny se stejným názvem, které odkazují na různé odpovědi z v rámci virtuální sítě a z veřejného Internetu. Typický scénář pro službu DNS zobrazením split-horizon je poskytnout vyhrazený verze služby pro použití ve vaší virtuální síti.

* **K dispozici ve všech oblastech Azure**. Funkce privátních zón Azure DNS je k dispozici ve všech oblastech Azure ve veřejném cloudu Azure.

## <a name="capabilities"></a>Možnosti

Azure DNS poskytuje následující možnosti:

* **Automatická registrace virtuálních počítačů z jedné virtuální sítě, který je propojený s privátní zóny jako registrační virtuální síť**. Virtuální počítače jsou registrované (přidal) do privátní zóny jako záznamy odkazující na jejich privátní IP adresy. Když virtuální počítač v registraci, je odstranit virtuální síť, Azure odebere také automaticky odpovídající DNS záznam z propojené privátní zónu. 

  Ve výchozím nastavení registrační virtuální sítě chovat i jako virtuální sítě pro překlad, v tom smyslu, že funguje překlad názvů DNS pro zónu z některého z virtuálních počítačů v rámci registrační virtuální síť.

  > [!NOTE]
  > Pokud chcete zadat registrační virtuální síť, nejsou záznamy DNS pro virtuální počítače z této virtuální sítě, které jsou registrovány k privátní zóně zobrazitelné nebo získat z prostředí Azure Powershell a rozhraní API pro Azure CLI. Záznamy virtuálního počítače jsou ve skutečnosti zaregistrované a vyřeší úspěšně.

* **Přední rozlišení serveru DNS je podporována mezi virtuálními sítěmi, které jsou propojeny k privátní zóně jako virtuální sítě pro překlad**. Pro různé virtuální síť překlad názvů DNS neexistuje žádná explicitní závislost tak, aby mezi sebou vytvoření partnerského vztahu virtuálních sítí. Zákazníci však může být vhodné vytvořit partnerský vztah virtuálních sítí pro další scénáře (například přenosy HTTP).

* **Zpětné vyhledávání DNS je podporována v rámci virtuální sítě oboru**. Zpětné vyhledávání DNS pro privátní IP adresu v rámci virtuální sítě, které jsou přiřazeny k privátní zóně vrátí plně kvalifikovaný název domény, který obsahuje název hostitele/záznamu a zóny název jako přípona.

## <a name="limitations"></a>Omezení

Azure DNS má následující omezení:

* Jednu privátní zónu je povolen pouze jeden registrační virtuální síť.
* Až 10 řešení virtuálních sítí jsou povoleny jednu privátní zónu. Toto omezení se odeberou, když tato funkce je obecně dostupná.
* Konkrétnímu virtuálnímu síťovému může být propojený jenom jednu privátní zónu jako registrační virtuální síť.
* Konkrétnímu virtuálnímu síťovému nesmí být propojení až 10 privátních zón jako virtuální síť pro překlad. Toto omezení se odeberou, když tato funkce je obecně dostupná.
* Pokud chcete zadat registrační virtuální síť, nejsou záznamy DNS pro virtuální počítače z této virtuální sítě, které jsou registrovány k privátní zóně zobrazitelné nebo získat z prostředí Azure Powershell a rozhraní API pro Azure CLI. Záznamy virtuálního počítače jsou ve skutečnosti zaregistrované a vyřeší úspěšně.
* Reverzní DNS funguje pouze pro privátní adresní prostor IP v registrační virtuální síť.
* Reverzní DNS pro privátní IP adresa, která není registrována v privátní zóny (například privátní IP adresy pro virtuální počítač ve virtuální síti, který se propojí jako virtuální síť pro překlad pro privátní zóny) vrátí *internal.cloudapp.net* jako příponu DNS. Tato přípona však není možné přeložit.
* Virtuální síť musí být úplně prázdná poprvé, můžete ho propojit s privátní zóně jako virtuální síť registraci nebo řešení. Však může být virtuální síť pak neprázdný pro budoucí propojení jako virtuální síť registraci nebo řešení, k jiné privátní zóny.
* Podmíněné předávání v současné době nepodporuje (například pro povolení rozlišení mezi sítěmi Azure a místního prostředí). Informace o tom, jak umožňuje zákazníkům realizovat tento scénář prostřednictvím jiné mechanismy, naleznete v tématu [překlad názvů pro virtuální počítače a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Pro běžné otázky a odpovědi k privátní zóny v Azure DNS, včetně konkrétní chování registrace a překlad DNS můžete očekávat pro některé typy operací, najdete v článku [nejčastější dotazy k](./dns-faq.md#private-dns).  

## <a name="pricing"></a>Ceny

Funkci privátní zóny DNS je ve verzi public preview zdarma. Období všeobecné dostupnosti tato funkce nabízí podle využití cenový model podobná stávající Azure DNS nabídky. 

## <a name="next-steps"></a>Další postup

Zjistěte, jak vytvořit privátní zónu v Azure DNS s využitím [prostředí Azure PowerShell](./private-dns-getstarted-powershell.md) nebo [rozhraní příkazového řádku Azure](./private-dns-getstarted-cli.md).

Přečtěte si informace o některé běžné [scénáře pro privátní zóny](./private-dns-scenarios.md) , která se dají realizovat s privátními zónami v Azure DNS.

Pro běžné otázky a odpovědi k privátní zóny v Azure DNS, včetně konkrétní chování můžete očekávat pro některé typy operací, najdete v článku [nejčastější dotazy k](./dns-faq.md#private-dns). 

Další informace o záznamy a zóny DNS najdete [Přehled záznamů a zón DNS](dns-zones-records.md).

Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
