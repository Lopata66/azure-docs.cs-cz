---
title: Push a pull artefaktu OCI
description: Nabízení a stahování artefaktů Open container Initiative (OCI) pomocí privátního registru kontejnerů v Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 03/11/2020
ms.author: stevelas
ms.openlocfilehash: 2c6b66b635a2513ccc19e0352414d18d8389fef1
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371048"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Vložení a vyžádání artefaktu OCI pomocí služby Azure Container Registry

Pomocí služby Azure Container Registry můžete ukládat a spravovat [artefakty rozhraní OCI (Open container Initiative)](container-registry-image-formats.md#oci-artifacts) i image kontejneru kompatibilní s Docker a Docker.

V tomto článku se dozvíte, jak pomocí nástroje [Registry OCI jako úložiště (Oras)](https://github.com/deislabs/oras) odeslat ukázkový artefakt – textový soubor – do služby Azure Container Registry. Pak z registru spusťte artefakt. Pomocí různých nástrojů příkazového řádku, které jsou vhodné pro každý artefakt, můžete spravovat nejrůznější artefakty OCI v registru kontejnerů Azure.

## <a name="prerequisites"></a>Požadavky

* **Registr kontejnerů Azure** – Vytvořte registr kontejnerů ve svém předplatném Azure. Použijte například [Azure Portal](container-registry-get-started-portal.md) nebo rozhraní příkazového [řádku Azure CLI](container-registry-get-started-azure-cli.md).
* **Nástroj Oras** – Stáhněte a nainstalujte si aktuální verzi Oras pro váš operační systém z [úložiště GitHub](https://github.com/deislabs/oras/releases). Nástroj se uvolní jako komprimovaný soubor tarballu (`.tar.gz`). Extrahujte a nainstalujte soubor pomocí standardních postupů pro váš operační systém.
* **Azure Active Directory instanční objekt (volitelné)** – pro ověřování přímo pomocí Oras vytvořte [instanční objekt](container-registry-auth-service-principal.md) pro přístup k vašemu registru. Ujistěte se, že instanční objekt má přiřazenou roli, jako je například AcrPush, aby měl oprávnění k nasdílení a vyžádání artefaktů.
* **Azure CLI (volitelné)** – Chcete-li použít individuální identitu, potřebujete místní instalaci rozhraní příkazového řádku Azure CLI. Doporučuje se verze 2.0.71 nebo novější. Pro nalezení verze spusťte `az --version `. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).
* **Docker (volitelné)** – Chcete-li použít jednotlivou identitu, je také nutné mít k ověření v registru místně nainstalovaný Docker. Docker poskytuje balíčky, které snadno konfigurují Docker na jakémkoli systému [MacOS][docker-mac], [Windows][docker-windows]nebo [Linux][docker-linux] .


## <a name="sign-in-to-a-registry"></a>Přihlášení k registru

V této části najdete dva navrhované pracovní postupy pro přihlášení k registru v závislosti na použité identitě. Vyberte metodu, která je vhodná pro vaše prostředí.

### <a name="sign-in-with-oras"></a>Přihlaste se pomocí ORAS

Pomocí [instančního objektu](container-registry-auth-service-principal.md) s nabízenými právy spusťte příkaz `oras login` a přihlaste se k registru pomocí ID a hesla aplikace instančního objektu. V tomto případě *myregistry.azurecr.IO*zadejte plně kvalifikovaný název registru (všechna malá písmena). ID aplikace instančního objektu se předává do proměnné prostředí `$SP_APP_ID`a heslo v proměnné `$SP_PASSWD`.

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Pokud si chcete přečíst heslo ze standardního vstupu, použijte `--password-stdin`.

### <a name="sign-in-with-azure-cli"></a>Přihlášení pomocí Azure CLI

[Přihlaste](/cli/azure/authenticate-azure-cli) se do Azure CLI s vaší identitou a zasuňte a nahrajte artefakty z registru kontejnerů.

Pak použijte příkaz Azure CLI [AZ ACR Login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) pro přístup k registru. Například pro ověření v registru s názvem *myregistry*:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login` používá klienta Docker k nastavení tokenu Azure Active Directory v souboru `docker.config`. Aby bylo možné dokončit jednotlivé směry ověřování, je nutné nainstalovat a spustit klienta Docker.

## <a name="push-an-artifact"></a>Vložení artefaktu

Vytvořte textový soubor v místním pracovním pracovním adresáři s nějakým ukázkovým textem. Například v prostředí bash:

```bash
echo "Here is an artifact!" > artifact.txt
```

Pomocí příkazu `oras push` můžete tento textový soubor odeslat do registru. Následující příklad vloží vzorový textový soubor do úložiště `samples/artifact`. Registr se identifikuje s plně kvalifikovaným názvem registru *myregistry.azurecr.IO* (malými písmeny). Artefakt je označený `1.0`. Artefakt má Nedefinovaný typ ve výchozím nastavení identifikovaný řetězcem *typu média* za názvem souboru `artifact.txt`. Další typy najdete v tématu [artefakty OCI](https://github.com/opencontainers/artifacts) . 

**Linux**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

Výstup úspěšných nabízených oznámení je podobný následujícímu:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Pokud chcete spravovat artefakty v registru, pokud používáte Azure CLI, spusťte standardní `az acr` příkazy pro správu imagí. Můžete například získat atributy artefaktu pomocí příkazu [AZ ACR úložiště show][az-acr-repository-show] :

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

Výstup je podobný tomuto:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Načíst artefakt

Spusťte příkaz `oras pull` pro stažení artefaktu z registru.

Nejdřív odeberte textový soubor z místního pracovního adresáře:

```bash
rm artifact.txt
```

Spusťte `oras pull` pro vyžádání artefaktu a zadejte typ média, který se použije pro vložení artefaktu:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Ověřte, jestli se operace vyžádání úspěšně provedla:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>Odebrat artefakt (volitelné)

Pokud chcete odebrat artefakt ze služby Azure Container Registry, použijte příkaz [AZ ACR úložiště Delete][az-acr-repository-delete] . Následující příklad odebere artefakt, který jste uložili:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>Další kroky

* Další informace o [knihovně Oras](https://github.com/deislabs/oras/tree/master/docs), včetně postupu konfigurace manifestu pro artefakt
* Odkazy na informace o nových typech artefaktů najdete v úložišti [artefaktů OCI](https://github.com/opencontainers/artifacts) .



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
