---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8b1a4a8a6c808348ca17a9eebac17c5821ceefe5
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530079"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Stažení a instalace binárního linkeru linkeru

V prostředí založeném na bash na MacOS použijte `curl` ke stažení linkeru vydaných verzí následujícím způsobem:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

Binární soubor klienta `linkerd` běží na klientském počítači a umožňuje interakci s propojenou sítí služby. Použijte následující příkazy k instalaci linkeru `linkerd`ho binárního souboru klienta do prostředí založeného na bash na MacOS. Tyto příkazy kopírují binární soubor `linkerd` klienta do umístění standardního uživatelského programu v `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Pokud byste chtěli doplňování příkazového řádku pro Linkerový `linkerd` binárního souboru klienta, nastavte ho následujícím způsobem:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```