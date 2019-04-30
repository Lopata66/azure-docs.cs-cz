---
title: Povolit automatické tématu vytváření v Apache Kafka – Azure HDInsight
description: Zjistěte, jak nakonfigurovat Apache Kafka v HDInsight pro automatické vytvoření témata. Kafka můžete nakonfigurovat tak, že nastavíte auto.create.topics.enable na hodnotu true prostřednictvím Ambari nebo při vytváření clusteru pomocí šablon Resource Manageru nebo prostředí PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/18/2018
ms.openlocfilehash: af26bcee08ded8eb66d640f954113be3e7672e1b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097857"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Postup konfigurace Apache Kafka v HDInsight pro automatické vytvoření témata

Ve výchozím nastavení [Apache Kafka](https://kafka.apache.org/) na HDInsight neumožňuje vytvoření automatického tématu. Můžete povolit automatického vytváření témat pro existující clustery pomocí [Apache Ambari](https://ambari.apache.org/). Povolení automatického vytváření témat můžou také při vytváření nového clusteru Kafka pomocí šablony Azure Resource Manageru.

## <a name="apache-ambari-web-ui"></a>Apache Ambari webového uživatelského rozhraní

Pokud chcete povolit automatické tématu Vytvoření v existujícím clusteru prostřednictvím webového uživatelského rozhraní Ambari, postupujte následovně:

1. Z [webu Azure portal](https://portal.azure.com), vyberte clusteru Kafka.

2. Z __clusteru přehled__vyberte __řídicí panel clusteru__. 

    ![Obrázek portálu s vybraný řídicí panel clusteru](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Potom vyberte __řídicí panel clusteru HDInsight__. Po zobrazení výzvy ověřování pomocí přihlašovacích údajů (správce) pro cluster.

    ![Obrázek položky řídicí panel clusteru HDInsight](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Kafka službu vyberte ze seznamu na levé straně stránky.

    ![Seznam služeb](./media/apache-kafka-auto-create-topics/service-list.png)

4. Vyberte konfiguraci uprostřed stránky.

    ![Karta Konfigurace služby](./media/apache-kafka-auto-create-topics/service-config.png)

5. Do pole filtru zadejte hodnotu `auto.create`. 

    ![Obrázek pole filtru](./media/apache-kafka-auto-create-topics/filter.png)

    Tím vyfiltrujete seznam vlastností a zobrazí `auto.create.topics.enable` nastavení.

6. Změňte hodnotu vlastnosti `auto.create.topics.enable` k `true`a pak vyberte Uložit. Přidat poznámku a potom vyberte uložit znovu.

    ![Obrázek položky auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Vyberte službu Kafka, vyberte __restartovat__a pak vyberte __restartování všech ovlivněných__. Po zobrazení výzvy vyberte __potvrdit restartujte všechny__.

    ![Obrázek výběru restartování](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Můžete také nastavit hodnoty Ambari pomocí rozhraní Ambari REST API. To je obvykle obtížnější, protože je nutné provést několik volání REST, chcete-li načíst aktuální konfiguraci, upravit ho, atd. Další informace najdete v tématu [HDInsight Správa clusterů pomocí rozhraní REST API Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md) dokumentu.

## <a name="resource-manager-templates"></a>Šablony Resource Manageru

Při vytváření clusteru Kafka pomocí šablony Azure Resource Manageru, můžete přímo nastavit `auto.create.topics.enable` tak, že ho přidáte `kafka-broker`. Následující fragment kódu JSON ukazuje, jak tuto hodnotu nastavit na `true`:

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak můžete povolit automatické tématu vytváření pro Apache Kafka v HDInsight. Další informace o práci s platformou Kafka najdete v následujících tématech:

* [Analýza protokolů platformy Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replikace dat mezi clustery Apache Kafka](apache-kafka-mirroring.md)
