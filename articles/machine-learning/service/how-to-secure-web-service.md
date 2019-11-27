---
title: Zabezpečení webových služeb pomocí protokolu SSL
titleSuffix: Azure Machine Learning
description: Naučte se, jak povolit protokol HTTPS v pořadí, v jakém jsou webové služby nasazené prostřednictvím Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/12/2019
ms.custom: seodec18
ms.openlocfilehash: f577ac723558ad3772b697922baa624e0047dd21
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276832"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>Použití SSL k zabezpečení webové služby prostřednictvím Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak zabezpečit webovou službu, která je nasazená prostřednictvím Azure Machine Learning.

Pomocí [protokolu HTTPS](https://en.wikipedia.org/wiki/HTTPS) můžete omezit přístup k webovým službám a zabezpečit data, která klienti odesílají. Protokol HTTPS pomáhá zabezpečit komunikaci mezi klientem a webovou službou tím, že šifruje komunikaci mezi nimi. Šifrování používá [protokol TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Protokol TLS se někdy označuje jako *SSL (Secure Sockets Layer)* (SSL), což bylo předchůdce TLS.

> [!TIP]
> Sada Azure Machine Learning SDK používá pojem "SSL" pro vlastnosti, které se vztahují k zabezpečené komunikaci. To neznamená, že webová služba nepoužívá protokol *TLS*. SSL je jenom častěji rozpoznaná doba.

Protokoly TLS a SSL závisí na *digitálních certifikátech*, které vám pomůžou se šifrováním a ověřením identity. Další informace o tom, jak digitální certifikáty fungují, najdete v tématu [Infrastruktura veřejných klíčů](https://en.wikipedia.org/wiki/Public_key_infrastructure)tématu Wikipedii.

> [!WARNING]
> Pokud pro webovou službu nepoužíváte protokol HTTPS, data odesílaná do a ze služby mohou být viditelná i pro ostatní na internetu.
>
> Protokol HTTPS taky umožňuje klientovi ověřit pravost serveru, ke kterému se připojuje. Tato funkce chrání klienty proti [útokům prostředníkem](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) .

Toto je obecný proces zabezpečení webové služby:

1. Získáte název domény.

2. Získejte digitální certifikát.

3. Nasaďte nebo aktualizujte webovou službu s povoleným protokolem SSL.

4. Aktualizujte svoji službu DNS tak, aby odkazoval na webovou službu.

> [!IMPORTANT]
> Pokud nasazujete do služby Azure Kubernetes Service (AKS), můžete si koupit vlastní certifikát nebo použít certifikát, který poskytuje Microsoft. Pokud používáte certifikát od Microsoftu, nemusíte mít název domény ani certifikát SSL. Další informace najdete v části [Povolení protokolu SSL a nasazení](#enable) v tomto článku.

Existují mírné rozdíly při zabezpečení napříč [cíli nasazení](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Získání názvu domény

Pokud název domény ještě nemáte, kupte si ho od *registrátora názvu domény*. Proces a cena se v rámci registrátorů liší. Registrátor poskytuje nástroje pro správu názvu domény. Tyto nástroje slouží k mapování plně kvalifikovaného názvu domény (FQDN) (například webové\.contoso.com) na IP adresu, která je hostitelem vaší webové služby.

## <a name="get-an-ssl-certificate"></a>Získat certifikát SSL

Existuje mnoho způsobů, jak získat certifikát SSL (digitální certifikát). Nejběžnější je koupit si ho od certifikační *autority* (CA). Bez ohledu na to, kde certifikát obdržíte, potřebujete následující soubory:

* **Certifikát**. Certifikát musí obsahovat úplný řetěz certifikátů a musí být "PEM-encodeded".
* **Klíč**. Klíč musí být také zakódovaný v PEM.

Když vyžádáte certifikát, musíte zadat plně kvalifikovaný název domény adresy, kterou chcete používat pro webovou službu (například web\.contoso.com). Adresa, která je vyražena na certifikát a adresu, kterou používají klienti, je porovnána s cílem ověřit identitu webové služby. Pokud se tyto adresy neshodují, klient obdrží chybovou zprávu.

> [!TIP]
> Pokud certifikační autorita nemůže certifikát a klíč zadat jako soubory kódované PEM, můžete změnit formát pomocí nástroje, jako je třeba [OpenSSL](https://www.openssl.org/) .

> [!WARNING]
> Certifikáty *podepsané svým držitelem* používejte jenom pro vývoj. Nepoužívejte je v produkčních prostředích. Certifikáty podepsané svým držitelem může způsobovat problémy v klientovi aplikace. Další informace naleznete v dokumentaci pro síťové knihovny, které používá vaše klientská aplikace.

## <a id="enable"></a>Povolení SSL a nasazení

Chcete-li nasadit (nebo znovu nasadit) službu s povoleným protokolem SSL, nastavte parametr *ssl_enabled* na hodnotu "true", ať je to možné. Nastavte parametr *ssl_certificate* na hodnotu souboru *certifikátu* . Nastavte *ssl_key* na hodnotu souboru *klíče* .

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Nasadit v AKS a poli brány pole brány (FPGA)

  > [!NOTE]
  > Informace v této části platí také při nasazení zabezpečené webové služby pro návrháře. Pokud nejste obeznámeni s používáním sady Python SDK, přečtěte si téma [co je Azure Machine Learning SDK pro Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Při nasazení na AKS můžete vytvořit nový cluster AKS nebo připojit existující. Další informace o vytvoření nebo připojení clusteru najdete v tématu [nasazení modelu do clusteru služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).
  
-  Pokud vytvoříte nový cluster, použijete **[AksCompute. provisionining_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none-)** .
- Pokud připojíte existující cluster, použijete **[AksCompute. attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none--load-balancer-type-none-)** . Vrátí objekt konfigurace, který má metodu **Enable_ssl** .

Metoda **Enable_ssl** může používat certifikát, který poskytuje společnost Microsoft nebo certifikát, který si koupíte.

  * Použijete-li certifikát od společnosti Microsoft, je nutné použít parametr *leaf_domain_label* . Tento parametr vygeneruje název DNS pro službu. Například hodnota "contoso" vytvoří název domény "contoso\<šest-Random-Characters >.\<a >. cloudapp. Azure. com ", kde \<a > je oblast, která obsahuje službu. Volitelně můžete pomocí parametru *overwrite_existing_domain* přepsat existující *leaf_domain_label*.

    Chcete-li nasadit (nebo znovu nasadit) službu s povoleným protokolem SSL, nastavte parametr *ssl_enabled* na hodnotu "true", ať je to možné. Nastavte parametr *ssl_certificate* na hodnotu souboru *certifikátu* . Nastavte *ssl_key* na hodnotu souboru *klíče* .

    > [!IMPORTANT]
    > Pokud používáte certifikát od Microsoftu, nemusíte kupovat svůj vlastní certifikát ani název domény.

    Následující příklad ukazuje, jak vytvořit konfiguraci, která povolí certifikát SSL od společnosti Microsoft:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * Když použijete *zakoupený certifikát*, použijete parametry *ssl_cert_pem_file*, *ssl_key_pem_file*a *ssl_cname* . Následující příklad ukazuje, jak pomocí souborů *. pem* vytvořit konfiguraci, která používá certifikát SSL, který jste zakoupili:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Další informace o *Enable_ssl*naleznete v tématu [AksProvisioningConfiguration. Enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) a [AksAttachConfiguration. Enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Nasadit na Azure Container Instances

Při nasazení na Azure Container Instances zadáte hodnoty parametrů souvisejících s protokolem SSL, jak ukazuje následující fragment kódu:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Další informace naleznete v tématu [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-).

## <a name="update-your-dns"></a>Aktualizujte svoji službu DNS

V dalším kroku je nutné aktualizovat DNS tak, aby odkazoval na webovou službu.

+ **Pro Container Instances:**

  Pomocí nástrojů z registrátora názvu domény aktualizujte záznam DNS pro název domény. Záznam musí ukazovat na IP adresu služby.

  Může dojít ke zpoždění minut nebo hodin, než klienti budou moci přeložit název domény v závislosti na registrátoru a TTL (Time to Live), který je nakonfigurován pro název domény.

+ **Pro AKS:**

  > [!WARNING]
  > Pokud jste použili *leaf_domain_label* k vytvoření služby pomocí certifikátu od Microsoftu, neaktualizujte ručně hodnotu DNS clusteru. Hodnota by měla být nastavena automaticky.

  Aktualizujte DNS veřejné IP adresy clusteru AKS na kartě **Konfigurace** v části **Nastavení** v levém podokně. (Podívejte se na následující obrázek.) Veřejná IP adresa je typ prostředku, který se vytvoří v rámci skupiny prostředků, která obsahuje uzly agenta AKS a další síťové prostředky.

  [![Azure Machine Learning: zabezpečení webových služeb pomocí SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-ssl-certificate"></a>Aktualizace certifikátu SSL

Platnost certifikátů SSL vyprší a je třeba je obnovit. K tomu obvykle dochází každý rok. Informace v následujících částech použijte k aktualizaci a obnovení certifikátu pro modely nasazené do služby Azure Kubernetes Service:

### <a name="update-a-microsoft-generated-certificate"></a>Aktualizace certifikátu vygenerovaného společností Microsoft

Pokud certifikát původně vygeneroval Microsoft (při použití *leaf_domain_label* k vytvoření služby), použijte k aktualizaci certifikátu jeden z následujících příkladů:

**Použití sady SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Použití rozhraní příkazového řádku**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True
```

Další informace najdete v následujících dokumentech k dokumentaci:

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>Aktualizovat vlastní certifikát

Pokud byl certifikát původně vygenerován certifikační autoritou, použijte následující postup:

1. K obnovení certifikátu použijte dokumentaci poskytovanou certifikační autoritou. Tento proces vytvoří nové soubory certifikátů.

1. Pomocí sady SDK nebo rozhraní příkazového řádku aktualizujte službu pomocí nového certifikátu:

    **Použití sady SDK**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **Použití rozhraní příkazového řádku**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Další informace najdete v následujících dokumentech k dokumentaci:

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-ssl"></a>Zakázat protokol SSL

Pokud chcete protokol SSL zakázat pro model nasazený do služby Azure Kubernetes, vytvořte v `status="Disabled"``SslConfiguration` a pak proveďte aktualizaci:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable SSL
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Další kroky
Naučte se:
+ [Využití modelu strojového učení nasazeného jako webové služby](how-to-consume-web-service.md)
+ [Zabezpečené spouštění experimentů a odvození v rámci virtuální sítě Azure](how-to-enable-virtual-network.md)
