---
title: Chyba v oblasti nedostatečných domén selhání v Azure HDInsight
description: Vytvoření clusteru se nezdařilo z důvodu nedostatečných domén selhání v oblasti ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8be7e05ac85ce0b1b10edf18d3885a07e016b9ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895027"
---
# <a name="scenario-cluster-creation-failed-due-to-not-sufficient-fault-domains-in-region-in-azure-hdinsight"></a>Scénář: Vytvoření clusteru selhalo kvůli `not sufficient fault domains in region` službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

`not sufficient fault domains in region` Při pokusu o vytvoření clusteru Apache Kafka se zobrazí chybová zpráva podobná.

## <a name="cause"></a>Příčina

Doména selhání je logické seskupení základního hardwaru v datovém centru Azure. Všechny domény selhání sdílí společný zdroje napájení a síťový přepínač. Virtuální počítače a spravované disky, které implementují uzly v clusteru služby HDInsight, jsou distribuované napříč těmito doménami selhání. Tato architektura omezuje potenciální dopad selhání fyzického hardwaru.

Každá oblast Azure má určitý počet domén selhání. Seznam domén a počet domén selhání, které obsahují, najdete v dokumentaci k [sadám dostupnosti](../../virtual-machines/windows/manage-availability.md).

V HDInsight musí být clustery Kafka zřízené v oblasti s nejméně třemi doménami selhání.

## <a name="resolution"></a>Řešení

Pokud v oblasti, ve které chcete vytvořit cluster, nejsou dost domén selhání, vynechejte si produktový tým a umožněte zřizování clusteru i v případě, že nejsou k dispozici tři domény selhání.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
