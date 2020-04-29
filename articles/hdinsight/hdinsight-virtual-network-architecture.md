---
title: Architektura virtuální sítě Azure HDInsight
description: Seznamte se s prostředky, které jsou k dispozici při vytváření clusteru HDInsight v Virtual Network Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: ad0e0250b32f2bdef4944e6e148be3215f3822f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81390207"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Architektura virtuální sítě Azure HDInsight

Tento článek popisuje prostředky, které jsou k dispozici při nasazení clusteru HDInsight do vlastního Virtual Network Azure. Tyto informace vám pomůžou připojit místní prostředky ke clusteru HDInsight v Azure. Další informace o virtuálních sítích Azure najdete v tématu [co je Azure Virtual Network?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Typy prostředků v clusterech Azure HDInsight

Clustery Azure HDInsight mají různé typy virtuálních počítačů nebo uzlů. Každý typ uzlu hraje roli v provozu systému. Následující tabulka shrnuje tyto typy uzlů a jejich role v clusteru.

| Typ | Popis |
| --- | --- |
| Hlavní uzel |  Pro všechny typy clusterů kromě Apache Storm hlavní uzly hostují procesy, které spravují provádění distribuované aplikace. Hlavní uzel je také uzel, do kterého můžete přihlédnout a spouštět aplikace, které jsou potom koordinovány pro spouštění v rámci prostředků clusteru. Počet hlavních uzlů je pevně stanoven dvakrát pro všechny typy clusterů. |
| Uzel ZooKeeper | Zookeeper koordinuje úlohy mezi uzly, které provádí zpracování dat. Má také volbu vedoucího hlavního uzlu a sleduje, který hlavní uzel spouští určitou hlavní službu. Počet uzlů ZooKeeper je pevně stanoven na tři. |
| Pracovní uzel | Představuje uzly, které podporují funkci zpracování dat. Pracovní uzly můžete přidat nebo odebrat z clusteru, abyste mohli škálovat výpočetní funkce a spravovat náklady. |
| R Server hraniční uzel | R Server hraniční uzel představuje uzel, ke kterému můžete přihlédnout a spouštět aplikace, které jsou pak koordinovány pro spouštění napříč prostředky clusteru. Hraniční uzel se nepodílí na analýze dat v rámci clusteru. Tento uzel je také hostitelem R Studio serveru, který umožňuje spuštění aplikace R pomocí prohlížeče. |
| Uzel oblasti | Pro typ clusteru HBA uzel region (také označovaný jako datový uzel) spouští server oblasti. Servery oblastí slouží a spravují část dat spravovaných pomocí adaptérů HBA. Uzly oblasti je možné přidat nebo odebrat z clusteru, abyste mohli škálovat výpočetní funkce a spravovat náklady.|
| Uzel Nimbus | Pro typ clusteru neNimbus poskytuje uzel funkce podobné hlavnímu uzlu. Uzel Nimbus přiřazuje úlohy jiným uzlům v clusteru prostřednictvím Zookeeper, který koordinuje spouštění topologií s více průchody. |
| Uzel vedoucího | Pro typ clusteru s více podprocesy provede uzel Správce instrukce, které poskytuje uzel Nimbus, ke zpracování. |

## <a name="resource-naming-conventions"></a>Zásady vytváření názvů prostředků

Při adresování uzlů v clusteru použijte plně kvalifikované názvy domény (FQDN). Plně kvalifikované názvy domény pro různé typy uzlů v clusteru můžete získat pomocí [rozhraní Ambari API](hdinsight-hadoop-manage-ambari-rest-api.md).

Tyto plně kvalifikované názvy domén budou ve formátu `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`.

Bude HN pro *hn* hlavních, *dolů* pro pracovní uzly a Zn pro uzly Zookeeper. *zn* `<node-type-prefix>`

Pokud potřebujete jenom název hostitele, použijte jenom první část plně kvalifikovaného názvu domény:`<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Základní prostředky virtuální sítě

Následující diagram znázorňuje umístění uzlů HDInsight a síťových prostředků v Azure.

![Diagram entit HDInsight vytvořených ve vlastní virtuální síti Azure](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

Mezi výchozí prostředky v Azure Virtual Network patří typy uzlů clusteru uvedené v předchozí tabulce. A síťová zařízení, která podporují komunikaci mezi virtuální sítí a externími sítěmi.

Následující tabulka shrnuje devět uzlů clusteru vytvořených při nasazení HDInsight do vlastního Virtual Network Azure.

| Typ prostředku | Číslo k dispozici | Podrobnosti |
| --- | --- | --- |
|Hlavní uzel | dva |    |
|Uzel Zookeeper | 3 | |
|Pracovní uzel | dva | Toto číslo se může lišit v závislosti na konfiguraci a škálování clusteru. Pro Apache Kafka je potřeba minimálně tři pracovní uzly.  |
|Uzel brány | dva | Uzly brány jsou virtuální počítače Azure, které jsou vytvořené v Azure, ale nejsou ve vašem předplatném viditelné. Pokud potřebujete tyto uzly restartovat, obraťte se na podporu. |

V rámci virtuální sítě používané se službou HDInsight se automaticky vytvoří tyto síťové prostředky:

| Prostředek sítě | Číslo k dispozici | Podrobnosti |
| --- | --- | --- |
|Nástroj pro vyrovnávání zatížení | 3 | |
|Síťová rozhraní | devět | Tato hodnota je založena na běžném clusteru, kde každý uzel má své vlastní síťové rozhraní. Devět rozhraní je pro: dva hlavní uzly, tři uzly Zookeeper, dva pracovní uzly a dva uzly brány, které jsou uvedené v předchozí tabulce. |
|Veřejné IP adresy | dva |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Koncové body pro připojení ke službě HDInsight

Ke clusteru HDInsight máte přístup třemi způsoby:

- Koncový bod HTTPS mimo virtuální síť v `CLUSTERNAME.azurehdinsight.net`.
- Koncový bod SSH pro přímé připojení k hlavnímu uzlu na `CLUSTERNAME-ssh.azurehdinsight.net`.
- Koncový bod HTTPS v rámci virtuální sítě `CLUSTERNAME-int.azurehdinsight.net`. Všimněte si,`-int`že v této adrese URL je "". Tento koncový bod se přeloží na soukromou IP adresu v této virtuální síti a není přístupný z veřejného Internetu.

K těmto třem koncovým bodům se přiřadí nástroj pro vyrovnávání zatížení.

Veřejné IP adresy se také poskytují ke dvěma koncovým bodům, které umožňují připojení mimo virtuální síť.

1. K nástroji pro vyrovnávání zatížení pro plně kvalifikovaný název domény (FQDN), který se má použít při připojování ke clusteru z Internetu `CLUSTERNAME.azurehdinsight.net`, se přiřadí jedna veřejná IP adresa.
1. Druhá veřejná IP adresa se používá pouze pro název `CLUSTERNAME-ssh.azurehdinsight.net`domény SSH.

## <a name="next-steps"></a>Další kroky

- [Zabezpečení příchozího provozu do clusterů HDInsight ve virtuální síti s privátním koncovým bodem](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
