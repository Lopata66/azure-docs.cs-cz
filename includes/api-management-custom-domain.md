---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: dff01f8bc4a4cf58d1ed503b69a29dadc367fecb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248842"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Jak zareaguje APIM Proxy Server s využitím certifikátů SSL v TLS handshake

### <a name="clients-calling-with-sni-header"></a>Volání s hlavičkou SNI klientů
Pokud zákazník má jeden nebo více vlastních domén, které jsou nakonfigurované pro proxy server, APIM moci odpovídat na požadavky HTTPS vlastní domény (např. contoso.com) a také výchozí doménu (například služby apim-service-name.azure-api.net). Na základě informací v hlavičce indikace názvu serveru (SNI), certifikát příslušný server odpoví APIM.

### <a name="clients-calling-without-sni-header"></a>Klienti volání bez SNI záhlaví
Pokud zákazník používá klient, který neodesílá [SNI](https://tools.ietf.org/html/rfc6066#section-3) záhlaví, vytvoří APIM odpovědi na základě následujícího postupu:

* Pokud má služba pouze jednu pro proxy server v konfiguraci vlastní domény, výchozí certifikát není certifikát, který byl vydán pro vlastní domény proxy serveru.
* Pokud má služba nakonfigurována více vlastních domén pro proxy server (podporováno pouze v **Premium** vrstva), zákazníka můžete určit, který certifikát by měl být výchozí certifikát. Chcete-li nastavit výchozí certifikát [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/createorupdate#hostnameconfiguration) vlastnost musí být nastavena na hodnotu true ("defaultSslBinding": "PRAVDA"). Pokud zákazník sady nenajdete vlastnost, je výchozí certifikát certifikát vydaný pro výchozí proxy server domény hostovaných *.azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Podpora pro požadavek PUT/POST s velké datové části

Server Proxy služby APIM podporuje požadavek se velké datové části při použití certifikátů na straně klienta v protokolu HTTPS (například datové části > 40 KB). Abyste zabránili zmrazení požadavku na server, Zákazníci můžete nastavit vlastnost ["negotiateClientCertificate": "PRAVDA"](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) na název hostitele proxy serveru. Pokud je nastavena na hodnotu true, klient certifikát je požadován v době připojení SSL/TLS, před libovolná výměna požadavku HTTP. Protože toto nastavení se vztahuje na **název hostitele Proxy** úrovně, všechny požadavky na připojení požádat o certifikát klienta. Zákazníci můžou nakonfigurovat až 20 vlastní domény pro server Proxy (podporováno pouze v **Premium** vrstva), tak i pracovní vyhnout uvedeným potížím.

