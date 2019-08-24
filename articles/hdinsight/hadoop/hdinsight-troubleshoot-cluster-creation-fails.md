---
title: Řešení chyb při vytváření clusteru s Azure HDInsight
description: Naučte se řešit problémy s vytvářením clusteru pro Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.openlocfilehash: 476b8cff23d09d81fe356a6445e27794b267d9a2
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998110"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Řešení chyb při vytváření clusteru s Azure HDInsight

Následující problémy jsou nejběžnější hlavní příčiny selhání při vytváření clusteru:

- Problémy s oprávněním
- Omezení zásad prostředků
- Brány firewall
- Zámky prostředků
- Nepodporované verze součástí
- Omezení názvu účtu úložiště
- Výpadky služeb

## <a name="permissions-issues"></a>Problémy s oprávněními

Pokud používáte Azure Data Lake Storage Gen 2 a obdržíte chybu "Tato žádost nemá autorizaci k provedení této operace pomocí tohoto oprávnění", otevřete Azure Portal, vyberte svůj účet úložiště a v části Access Control (IAM) zajistěte, aby **objekt BLOB úložiště. Role Přispěvatel dat** nebo role **vlastníka dat objektu BLOB úložiště** přiřadila přístup k **spravované identitě přiřazené uživateli** pro dané předplatné. Podrobné pokyny najdete v tématu [Nastavení oprávnění pro spravovanou identitu na data Lake Storage Gen2ovém účtu](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account) .

Pokud používáte Data Lake Storage Gen 1, přečtěte si [tady](../hdinsight-hadoop-use-data-lake-store.md)pokyny k instalaci a konfiguraci. Data Lake Storage Gen 1 se u clusterů HBA nepodporuje a v HDInsight verze 4,0 se nepodporuje.

Pokud používáte Azure Storage, ujistěte se, že je název účtu úložiště během vytváření clusteru platný.

## <a name="resource-policy-restrictions"></a>Omezení zásad prostředků

Zásady Azure založené na předplatném můžou odepřít vytváření veřejných IP adres. K vytvoření clusteru HDInsight jsou potřeba dvě veřejné IP adresy.  

Obecně platí, že následující zásady mohou ovlivnit vytváření clusteru:

* Zásady zabraňující vytváření IP adres & nástrojů pro vyrovnávání zatížení v rámci předplatného.
* Zásada brání vytvoření účtu úložiště.
* Zásady zabraňující odstraňování síťových prostředků (IP adres/Load vyrovnávání zatížení).

## <a name="firewalls"></a>Brány firewall

Brány firewall ve vaší virtuální síti nebo účtu úložiště můžou odepřít komunikaci s IP adresami správy HDInsight.

Povolte provoz z IP adres v následující tabulce.

| Zdrojová IP adresa | Cíl | Direction |
|---|---|---|
| 168.61.49.99 | *: 443 | Příchozí |
| 23.99.5.239 | *: 443 | Příchozí |
| 168.61.48.131 | *: 443 | Příchozí |
| 138.91.141.162 | *: 443 | Příchozí |

Přidejte taky IP adresy specifické pro oblast, ve které se cluster vytvoří. Seznam adres pro každou oblast Azure najdete v tématu věnovaném [IP adresám správy HDInsight](../hdinsight-management-ip-addresses.md) .

Pokud používáte Express Route nebo vlastní server DNS, přečtěte si téma [plánování virtuální sítě pro Azure HDInsight – připojení více sítí](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Zámky prostředků  

Zajistěte, aby [ve vaší virtuální síti a skupině prostředků](../../azure-resource-manager/resource-group-lock-resources.md)nejsou žádné zámky.  

## <a name="unsupported-component-versions"></a>Nepodporované verze součástí

Ujistěte se, že používáte [podporovanou verzi Azure HDInsight](../hdinsight-component-versioning.md) a všechny [součásti Apache Hadoop](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions) ve vašem řešení.  

## <a name="storage-account-name-restrictions"></a>Omezení názvu účtu úložiště

Názvy účtů úložiště nesmí být delší než 24 znaků a nesmí obsahovat speciální znak. Tato omezení platí také pro výchozí název kontejneru v účtu úložiště.

Pro vytvoření clusteru se taky použijí další omezení pojmenování. Další informace najdete v tématu [omezení názvu clusteru](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name).

## <a name="service-outages"></a>Výpadky služeb

Ověřte [stav Azure](https://status.azure.com/status) pro případné potenciální výpadky nebo problémy se službami.

## <a name="next-steps"></a>Další postup

* [Rozšíření Azure HDInsight pomocí virtuální síť Azure](../hdinsight-plan-virtual-network-deployment.md)
* [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Použití úložiště Azure s clustery Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
* [Nastavení clusterů ve službě HDInsight se softwarem Apache Hadoop, Apache Spark, Apache Kafka a dalšími](../hdinsight-hadoop-provision-linux-clusters.md)
