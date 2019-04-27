---
title: Použití prázdných hraničních uzlů v clusterech Apache Hadoop v HDInsight – Azure
description: Postup přidání prázdných hraničních uzlů do clusteru služby HDInsight, který může sloužit jako klient a pak test/hostitele aplikace HDInsight.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: df35ee9791dc1090385e2d2aed5966a1292ddc64
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097357"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Použití prázdných hraničních uzlů v clusterech Apache Hadoop v HDInsight

Zjistěte, jak přidat prázdných hraničních uzlů do clusteru HDInsight. Je virtuální počítač s Linuxem pomocí stejných nástrojů klienta nainstalované a nakonfigurované stejně jako v hlavních uzlech, ale bez prázdných hraničních uzlů [Apache Hadoop](https://hadoop.apache.org/) služby spuštěné. Hraniční uzel můžete použít pro přístup ke clusteru, testování vaší klientské aplikace a který je hostitelem klientských aplikací. 

Prázdných hraničních uzlů můžete přidat do existujícího clusteru HDInsight, do nového clusteru při vytváření clusteru. Přidání prázdných hraničních uzlů se provádí pomocí šablony Azure Resource Manageru.  Následující příklad ukazuje, jak se to dělá pomocí šablony:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "Standard_D3"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Jak je znázorněno v ukázce, Volitelně můžete volat [skript akce](hdinsight-hadoop-customize-cluster-linux.md) provést další konfiguraci, například při instalaci [Apache Hue](hdinsight-hadoop-hue-linux.md) na hraničním uzlu. Skript skript akce musí být veřejně dostupný na webu.  Pokud tento skript je uložený ve službě Azure storage, použijte například veřejné kontejnery nebo veřejné objekty BLOB.

Velikost virtuálního počítače uzlu edge musí splňovat požadavky HDInsight clusteru pracovního uzlu virtuálního počítače velikost. Velikosti virtuálních počítačů uzel doporučené pracovního procesu najdete v tématu [vytvořit Apache Hadoop clusterů v HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Po vytvoření hraniční uzel může připojit k hraničnímu uzlu pomocí SSH a spuštění klientské nástroje pro přístup ke clusteru Hadoop v HDInsight.

> [!WARNING]   
> Vlastní komponenty, které jsou nainstalovány na hraničním uzlu přijímat obchodně přiměřenou podporu od Microsoftu. To může způsobit řešení problémů, na které narazíte. Nebo můžete být uvedené komunitní zdroje o další pomoc. Tady jsou některé z nejvíc weby získat pomoc od komunity:
>
> * [Fórum na webu MSDN pro HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Pokud používáte technologie Apache, je možné k vyhledání pomoci prostřednictvím Apache projektovým webům na [ https://apache.org ](https://apache.org), například [Apache Hadoop](https://hadoop.apache.org/) lokality.

> [!IMPORTANT]
> K dispozici pro vytvoření nového clusteru HDInsight 3 měsících publikování Image Ubuntu. Od ledna 2019 spuštěné clustery (včetně hraničních uzlů) jsou **není** automaticky opravit. Zákazníkům musíte použít akce skriptů nebo jiných mechanismů o opravu spuštěný cluster.  Další informace najdete v tématu [opravy operačního systému pro HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Přidat hraniční uzel do existujícího clusteru
V této části pomocí šablony Resource Manageru pro přidání hraniční uzel do existujícího clusteru HDInsight.  Šablony Resource Manageru najdete v [Githubu](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). Šablony Resource Manageru volá akci skriptu umístěného v https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh. Skript nebude provádět žádné akce.  Je k předvedení akce volání skriptu z šablony Resource Manageru.

**Chcete-li přidat do existujícího clusteru prázdných hraničních uzlů**

1. Kliknutím na následující obrázek pro přihlášení k Azure a otevřete šablonu Azure Resource Manageru na webu Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Konfigurovat následující vlastnosti:
   
   * **Předplatné**: Vyberte předplatné Azure použité k vytvoření clusteru.
   * **Skupina prostředků**: Vyberte skupinu prostředků použitou pro existující cluster HDInsight.
   * **Umístění**: Vyberte umístění existujícího clusteru HDInsight.
   * **Název clusteru**: Zadejte název existujícího clusteru HDInsight.
   * **Hraniční uzel velikost**: Vyberte jednu z velikostí virtuálních počítačů. Velikost virtuálního počítače musí splňovat požadavky na velikost virtuálního počítače uzlu pracovního procesu. Velikosti virtuálních počítačů uzel doporučené pracovního procesu najdete v tématu [vytvořit Apache Hadoop clusterů v HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Hraniční uzel předponu**: Výchozí hodnota je **nové**.  Použita výchozí hodnota je název uzlu edge **nového koncového uzlu**.  Můžete upravit předpony z portálu. Můžete také přizpůsobit celý název ze šablony.

4. Zkontrolujte **vyjadřuji souhlas s podmínkami a ujednáními uvedenými nahoře**a potom klikněte na tlačítko **nákupní** vytvořit na hraničním uzlu.

>[!IMPORTANT]  
> Ujistěte se, že vyberete skupinu prostředků Azure existujícího clusteru HDInsight.  Jinak, zobrazí se chybová zpráva "nelze provést požadovanou operaci pro vnořený prostředek. Nadřazený prostředek "&lt;Název_clusteru >' nebyl nalezen."

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Přidat hraničnímu uzlu při vytváření clusteru
V této části pomocí šablony Resource Manageru k vytvoření clusteru HDInsight s hraniční uzel.  Šablony Resource Manageru najdete v [galerii šablon rychlý start Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). Šablony Resource Manageru volá akci skriptu umístěného v https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. Skript nebude provádět žádné akce.  Je k předvedení akce volání skriptu z šablony Resource Manageru.

**Vytvoření clusteru HDInsight s hraniční uzel**

1. Pokud ještě nemáte, vytvořte HDInsight cluster.  Zobrazit [Začínáme používat Hadoop v HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Kliknutím na následující obrázek pro přihlášení k Azure a otevřete šablonu Azure Resource Manageru na webu Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Konfigurovat následující vlastnosti:
   
   * **Předplatné**: Vyberte předplatné Azure použité k vytvoření clusteru.
   * **Skupina prostředků**: Vytvořte novou skupinu prostředků clusterů.
   * **Umístění**: Vyberte umístění skupiny prostředků.
   * **Název clusteru**: Zadejte název pro nový cluster vytvořit.
   * **Uživatelské jméno přihlášení clusteru**: Zadejte uživatelské jméno Hadoop HTTP.  Výchozí uživatelské jméno je **admin**.
   * **Heslo přihlášení clusteru**: Zadejte heslo uživatele Hadoop HTTP.
   * **Uživatelské jméno SSH**: Zadejte uživatelské jméno SSH. Výchozí název je **sshuser**.
   * **SSH heslo**: Zadejte heslo uživatele SSH.
   * **Instalace akce skriptu**: Ponechte výchozí hodnotu pro procházení tohoto kurzu.
     
     Některé vlastnosti byly pevně zakódované v šabloně: Typ clusteru, počet pracovních uzlů clusteru, velikost uzlu Edge a název uzlu Edge.
4. Zkontrolujte **vyjadřuji souhlas s podmínkami a ujednáními uvedenými nahoře**a potom klikněte na tlačítko **nákupní** k vytvoření clusteru pomocí hraničního uzlu.

## <a name="add-multiple-edge-nodes"></a>Přidat víc hraničních uzlů

Můžete přidat více hraničních uzlů do clusteru HDInsight.  Konfiguraci více uzlů edge jde jenom pomocí šablon Azure Resource Manageru.  Najdete v ukázce šablony na začátku tohoto článku.  Je potřeba aktualizovat **targetInstanceCount** tak, aby odrážely počet hraničních uzlů, které chcete vytvořit.

## <a name="access-an-edge-node"></a>Přístup k hraničnímu uzlu
Na hraničním uzlu ssh je koncový bod &lt;EdgeNodeName >.&lt; Název clusteru >-ssh.azurehdinsight.net:22.  Například nový edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Na hraničním uzlu se zobrazí jako aplikace na webu Azure portal.  Portál poskytuje informace pro přístup k hraničnímu uzlu pomocí SSH.

**Chcete-li ověřit koncový bod SSH hraniční uzel**

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Otevřete HDInsight cluster s hraniční uzel.
3. Klikněte na tlačítko **aplikací**. Měly by se zobrazit na hraničním uzlu.  Výchozí název je **nového koncového uzlu**.
4. Klikněte na hraničním uzlu. Měly by se zobrazit koncový bod SSH.

**Použití Hive na hraničním uzlu**

1. Připojení k hraničnímu uzlu pomocí SSH Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po připojení k hraničnímu uzlu pomocí SSH, použijte následující příkaz pro otevření konzoly Hive:
   
        hive
3. Spuštěním následujícího příkazu zobrazíte tabulek Hive v clusteru:
   
        show tables;

## <a name="delete-an-edge-node"></a>Odstranit hraniční uzel
Hraniční uzel můžete odstranit z portálu Azure portal.

**Pro přístup k hraničnímu uzlu**

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Otevřete HDInsight cluster s hraniční uzel.
3. Klikněte na tlačítko **aplikací**. Zobrazí se seznam hraničních uzlů.  
4. Klikněte pravým tlačítkem na hraničním uzlu, kterou chcete odstranit a pak klikněte na **odstranit**.
5. Pro potvrzení klikněte na tlačítko **Ano**.

## <a name="next-steps"></a>Další postup
V tomto článku jste se naučili přidání hraniční uzel a jak získat přístup k hraničnímu uzlu. Další informace naleznete v následujících článcích:

* [Instalace aplikací HDInsight](hdinsight-apps-install-applications.md): Zjistěte, jak chcete instalovat aplikace HDInsight do clusterů.
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md): Naučte se nasazovat nepublikované aplikace HDInsight do HDInsight.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Zjistěte, jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight v systému Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): další informace o použití akce skriptu k instalaci dalších aplikací.
* [Vytvářet clustery založené na Linuxu Apache Hadoop v HDInsight pomocí šablon Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Zjistěte, jak voláním šablon Resource Manageru vytvoříte clustery HDInsight.

