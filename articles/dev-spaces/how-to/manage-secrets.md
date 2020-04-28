---
title: Správa tajných kódů při práci s vývojovým prostorem Azure
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Naučte se používat tajné klíče Kubernetes při vývoji aplikací s využitím Azure Dev Spaces nebo při jejich sestavování.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75438469"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Správa tajných kódů při práci s vývojovým prostorem Azure

Vaše služby můžou vyžadovat určitá hesla, připojovací řetězce a další tajné kódy, například pro databáze nebo jiné zabezpečené služby Azure. Nastavením hodnot těchto tajných kódů v konfiguračních souborech je můžete zpřístupnit v kódu jako proměnné prostředí.  Aby se zabránilo ohrožení zabezpečení tajných kódů, musí být tyto konfigurační soubory zpracovávány opatrně.

## <a name="storing-and-using-runtime-secrets"></a>Ukládání a používání tajných kódů za běhu

Azure Dev Spaces poskytuje dvě Doporučené, zjednodušené možnosti pro ukládání tajných kódů v Helm grafech generovaných nástroji Azure Dev Spaces klienta: v `values.dev.yaml` souboru a přímo v `azds.yaml`nástroji. Nedoporučujeme ukládat tajné kódy do `values.yaml`.

> [!NOTE]
> Následující přístupy ukazují, jak ukládat a používat tajné klíče pro Helm grafy generované nástroji klienta. Pokud vytvoříte vlastní graf Helm, můžete použít graf Helm přímo ke správě a ukládání tajných klíčů.

### <a name="using-valuesdevyaml"></a>Použití hodnot. dev. yaml

V projektu, který už je připravený pomocí Azure Dev Spaces vytvořte `values.dev.yaml` soubor ve stejné složce jako `azds.yaml` k definování tajných klíčů a hodnot. Příklad:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

Ověřte odkazy `azds.yaml` `values.dev.yaml` na soubory jako volitelné pomocí `?`. Příklad:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Pokud máte další tajné soubory, můžete je také přidat.

Aktualizujte nebo ověřte, že vaše služba odkazuje na vaše tajné klíče jako proměnné prostředí. Příklad:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Spusťte aktualizované služby pomocí `azds up`.

```console
azds up
```
 
Použijte `kubectl` k ověření, že se tajné kódy vytvořily.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Nedoporučujeme ukládat tajné klíče ve správě zdrojového kódu. Pokud používáte Git, přidejte `values.dev.yaml` do `.gitignore` souboru, abyste se vyhnuli potvrzení tajných kódů ve správě zdrojového kódu.

### <a name="using-azdsyaml"></a>Použití azds. yaml

V projektu, který už je připravený pomocí Azure Dev Spaces, přidejte tajné klíče a hodnoty pomocí syntaxe *$PlaceHolder* v části *konfigurace. vývoj. Install. set* v `azds.yaml`. Příklad:

```yaml
configurations:
  develop:
    ...
    install:
      set:
        secrets:
          redis:
            port: "$REDIS_PORT"
            host: "$REDIS_HOST"
            key: "$REDIS_KEY"
```

> [!NOTE]
> Hodnoty tajného kódu můžete zadat přímo bez *$PLACEHOLDER* použití syntaxe $PlaceHolder `azds.yaml`v. Tento přístup se ale nedoporučuje, protože `azds.yaml` je uložený ve správě zdrojového kódu.
     
Vytvořte `.env` soubor ve stejné složce jako `azds.yaml` k definování *$PlaceHolder* hodnot. Příklad:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Nedoporučujeme ukládat tajné klíče ve správě zdrojového kódu. Pokud používáte Git, přidejte `.env` do `.gitignore` souboru, abyste se vyhnuli potvrzení tajných kódů ve správě zdrojového kódu.

Aktualizujte nebo ověřte, že vaše služba odkazuje na vaše tajné klíče jako proměnné prostředí. Příklad:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Spusťte aktualizované služby pomocí `azds up`.

```console
azds up
```
 
Použijte `kubectl` k ověření, že se tajné kódy vytvořily.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Použití tajných kódů jako argumentů sestavení

Předchozí část ukázala, jak ukládat a používat tajné klíče pro použití v době běhu kontejneru. Můžete také použít jakýkoliv tajný klíč v době sestavení kontejneru, jako je například heslo pro privátní NuGet, pomocí `azds.yaml`.

V `azds.yaml`systému nastavte tajné klíče pro čas sestavení v části `<variable name>: ${secret.<secret name>.<secret key>}` *konfigurace. vývoj. Build. args* pomocí syntaxe. Příklad:

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

Ve výše uvedeném příkladu je *mynugetsecret* stávající tajný klíč a *pattoken* je existující klíč.

>[!NOTE]
> V `.` názvech a klíčích tajných kódů může být znak. Při `\` předávání tajných kódů jako argumentů sestavení použijte k úniku `.` . Například pro předání tajného kódu s názvem *foo. bar* pomocí klíče *tokenu*: `MYTOKEN: ${secret.foo\.bar.token}`. Kromě toho je možné tajné klíče vyhodnotit pomocí předpony a textu přípony. Například, `MYURL: eus-${secret.foo\.bar.token}-version1`. Tajné kódy, které jsou k dispozici v nadřazených a prarodičích, můžou být také předány jako argumenty sestavení.

Ve vašem souboru Dockerfile Použijte direktivu *arg* pro použití tajného klíče a pak použijte stejnou proměnnou později v souboru Dockerfile. Příklad:

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Aktualizujte služby spuštěné v clusteru pomocí těchto změn. Na příkazovém řádku spusťte příkaz:

```
azds up
```

## <a name="next-steps"></a>Další kroky

Pomocí těchto metod se teď můžete bezpečně připojit k databázi, službě Azure cache pro Redis nebo získat přístup k zabezpečeným službám Azure.
 
