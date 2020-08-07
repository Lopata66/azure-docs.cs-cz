---
title: Apache Kafka REST proxy – Azure HDInsight
description: Naučte se, jak provádět operace s Apache Kafka pomocí Kafka REST proxy v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: 660e200b673da53af1ee00e4de1e2ce3298e861d
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876440"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interakce s Apache Kafka clustery ve službě Azure HDInsight pomocí proxy REST

Kafka REST proxy umožňuje komunikovat s clusterem Kafka prostřednictvím REST API přes protokol HTTP. Tato akce znamená, že klienti Kafka můžou být mimo vaši virtuální síť. Klienti můžou provádět jednoduché volání HTTP do clusteru Kafka a nemusí se spoléhat na knihovny Kafka. Tento článek vám ukáže, jak vytvořit cluster Kafka s povoleným proxy serverem REST. Obsahuje také vzorový kód, který ukazuje, jak volat proxy REST.

## <a name="rest-api-reference"></a>REST API – referenční informace

Informace o operacích podporovaných Kafka REST API najdete v tématu [Reference k rozhraní API HDInsight Kafka REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Pozadí

![Návrh proxy serveru REST Kafka](./media/rest-proxy/rest-proxy-architecture.png)

Úplnou specifikaci operací podporovaných rozhraním API najdete v tématu [rozhraní API služby REST proxy serveru Apache Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Koncový bod proxy REST

Vytvoření clusteru HDInsight Kafka pomocí proxy REST vytvoří nový veřejný koncový bod pro váš cluster, který můžete najít ve **vlastnostech** clusteru HDInsight na Azure Portal.

### <a name="security"></a>Zabezpečení

Přístup k proxy Kafka REST se spravuje pomocí skupin zabezpečení Azure Active Directory. Při vytváření clusteru Kafka zadejte skupinu zabezpečení Azure AD s přístupem ke koncovému bodu REST. Kafka klienti, kteří potřebují přístup k proxy REST, by měli být k této skupině zaregistrovaní vlastníkem skupiny. Vlastník skupiny se může zaregistrovat prostřednictvím portálu nebo pomocí PowerShellu.

V případě požadavků koncových bodů proxy REST by klientské aplikace měly získat token OAuth. Token slouží k ověření členství ve skupině zabezpečení. Níže najdete [ukázku klientské aplikace](#client-application-sample) , která ukazuje, jak získat token OAuth. Klientská aplikace předá token OAuth v požadavku HTTP na proxy REST.

> [!NOTE]
> Další informace o skupinách zabezpečení AAD najdete v tématu [Správa přístupu k aplikacím a prostředkům pomocí skupin Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md). Další informace o tom, jak tokeny OAuth fungují, najdete v tématu [autorizace přístupu k Azure Active Directory webových aplikací pomocí toku udělení kódu oauth 2,0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="kafka-rest-proxy-with-network-security-groups"></a>Kafka REST proxy se skupinami zabezpečení sítě
Pokud přenesete vlastní virtuální síť a řízení síťového provozu pomocí skupin zabezpečení sítě, povolte kromě portu 443 **příchozí** provoz na portu **9400** . Tím se zajistí, že proxy server Kafka REST je dosažitelný.

## <a name="prerequisites"></a>Požadavky

1. Zaregistrovat aplikaci s Azure AD. Klientské aplikace, které napíšete pro interakci s proxy Kafka REST, budou k ověření v Azure používat ID a tajný kód této aplikace.

1. Vytvořte skupinu zabezpečení Azure AD. Přidejte aplikaci, kterou jste zaregistrovali ve službě Azure AD, do skupiny zabezpečení jako **člena** skupiny. Tato skupina zabezpečení se použije k určení, které aplikace můžou pracovat s proxy REST. Další informace o vytváření skupin Azure AD najdete v tématu [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Ověřte, že je skupina typu **zabezpečení**.
    ![Skupina zabezpečení](./media/rest-proxy/rest-proxy-group.png)

    Ověřte, že je aplikace členem skupiny.
    ![Ověřit členství](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Vytvoření clusteru Kafka s povolenou službou REST proxy

Následující postup používá Azure Portal. Příklad použití rozhraní příkazového řádku Azure najdete v tématu [vytvoření Apache Kafkaho clusteru REST proxy pomocí Azure CLI](tutorial-cli-rest-proxy.md).

1. V pracovním postupu vytváření clusteru Kafka na kartě **zabezpečení + sítě** zaškrtněte možnost **Povolit proxy REST Kafka** .

     ![Povolit proxy REST Kafka a vybrat skupinu zabezpečení](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Klikněte na **Vybrat skupinu zabezpečení**. V seznamu skupin zabezpečení vyberte skupinu zabezpečení, kterou chcete mít přístup k proxy REST. Pomocí vyhledávacího pole můžete najít příslušnou skupinu zabezpečení. Klikněte na tlačítko **Vybrat** v dolní části.

     ![Povolit proxy REST Kafka a vybrat skupinu zabezpečení](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Dokončete zbývající kroky k vytvoření clusteru, jak je popsáno v tématu [Vytvoření clusteru Apache Kafka ve službě Azure HDInsight pomocí Azure Portal](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Po vytvoření clusteru přejděte do vlastností clusteru a zaznamenejte adresu URL proxy serveru Kafka REST.

     ![Zobrazit adresu URL proxy serveru REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Ukázka klientské aplikace

K interakci s proxy REST v clusteru Kafka můžete použít následující kód Pythonu. Chcete-li použít ukázku kódu, postupujte podle následujících kroků:

1. Uložte vzorový kód do počítače s nainstalovaným Pythonem.
1. Pomocí příkazu nainstalujte požadované závislosti Pythonu `pip3 install msal` .
1. Upravte část kód **konfigurace těchto vlastností** a aktualizujte následující vlastnosti svého prostředí:

    |Vlastnost |Popis |
    |---|---|
    |ID tenanta|Tenant Azure, ve kterém je vaše předplatné.|
    |ID klienta|ID aplikace, kterou jste zaregistrovali ve skupině zabezpečení.|
    |Tajný klíč klienta|Tajný klíč pro aplikaci, kterou jste zaregistrovali ve skupině zabezpečení.|
    |Kafkarest_endpoint|Tuto hodnotu získáte na kartě **vlastnosti** v přehledu clusteru, jak je popsáno v [části nasazení](#create-a-kafka-cluster-with-rest-proxy-enabled). Měl by být v následujícím formátu –`https://<clustername>-kafkarest.azurehdinsight.net`|

1. Z příkazového řádku spusťte soubor Python spuštěním příkazu.`sudo python3 <filename.py>`

Tento kód provede následující akci:

1. Načte token OAuth z Azure AD.
1. Ukazuje, jak vytvořit požadavek na Kafka proxy REST.

Další informace o získání tokenů OAuth v Pythonu najdete v tématu [Třída Python AuthenticationContext](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). V takovém případě se může zobrazit prodleva `topics` , která není vytvořena ani smazána prostřednictvím proxy Kafka REST. Tato prodleva je způsobená obnovením mezipaměti.

```python
#Required python packages
#pip3 install msal

import msal

#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
    )

# The pattern to acquire a token looks like this.
result = None

result = app.acquire_token_for_client(scopes=[scope])

print(result)
accessToken = result['access_token']

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Níže najdete další ukázku, jak získat token z Azure pro proxy REST pomocí příkazu složeného. **Všimněte si, že pro `scope=https://hib.azurehdinsight.net/.default` získání tokenu potřebujeme zadaný.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Další kroky

* [Referenční dokumenty k rozhraní API proxy Kafka REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
