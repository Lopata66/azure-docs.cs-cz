---
title: Správa místa na disku ve službě Azure HDInsight
description: Kroky řešení potíží a možná řešení pro problémy při komunikaci s clustery Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77473009"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Správa místa na disku ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="hive-log-configurations"></a>Konfigurace protokolu podregistru

1. Z webového prohlížeče přejděte do `https://CLUSTERNAME.azurehdinsight.net`umístění, kde `CLUSTERNAME` je název vašeho clusteru.

1. Přejděte na **podregistry** > **Konfigurace** > **Pokročilé** > **Pokročilé podregistru-log4j**. Zkontrolujte následující nastavení:

    * `hive.root.logger=DEBUG,RFA`. Jedná se o výchozí hodnotu, upravte [úroveň protokolu](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) na, `INFO` aby se vytiskly položky s nižšími protokoly.

    * `log4jhive.log.maxfilesize=1024MB`. Toto je výchozí hodnota, podle potřeby upravte.

    * `log4jhive.log.maxbackupindex=10`. Toto je výchozí hodnota, podle potřeby upravte. Pokud byl parametr vynechán, vygenerované soubory protokolu budou nekonečné.

## <a name="yarn-log-configurations"></a>Konfigurace protokolu příze

Zkontrolujte následující konfigurace:

* Apache Ambari

    1. Z webového prohlížeče přejděte do `https://CLUSTERNAME.azurehdinsight.net`umístění, kde `CLUSTERNAME` je název vašeho clusteru.

    1. Přejděte do**Konfigurace** >  **podregistru** > **Rozšířené** > **Správce prostředků**. Ujistěte se, že je zaškrtnuté políčko **Povolit agregaci protokolů** . Pokud je tato akce zakázaná, názvy uzlů budou uchovávat místní protokoly a nemusí je agregovat do vzdáleného úložiště při dokončování nebo ukončování aplikace.

* Ujistěte se, že velikost clusteru odpovídá zatížení. Je možné, že se úlohy změnily v poslední době, jinak se změnila velikost clusteru. [Horizontální](../hdinsight-scaling-best-practices.md) navýšení kapacity clusteru tak, aby odpovídaly vyššímu zatížení.

* `/mnt/resource`můžou být vyplněné osamocenými soubory (jako v případě restartování Resource Manageru). V případě potřeby je ručně `/mnt/resource/hadoop/yarn/log` vyčistit `/mnt/resource/hadoop/yarn/local`a.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
