---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 7a22256c88f9cee3ce62c68a2de4a5974d76f026
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77593704"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Stažení a instalace binárního linkeru linkeru

V prostředí založeném na bash v systému Linux nebo v subsystému [Windows pro Linux][install-wsl]použijte `curl` ke stažení linkerované verze následujícím způsobem:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

`linkerd`Binární soubor klienta běží na klientském počítači a umožňuje interakci s propojenou sítí služby. Použijte následující příkazy pro instalaci linkeru `linkerd` binárního klienta do prostředí založeného na bash v systému Linux nebo v subsystému [Windows pro Linux][install-wsl]. Tyto příkazy kopírují `linkerd` binární soubor klienta do umístění standardního uživatelského programu v `PATH` .

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Pokud chcete dokončit příkazový řádek pro Linkerový `linkerd` binární soubor klienta, nastavte ho následujícím způsobem:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10