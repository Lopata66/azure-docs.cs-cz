---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1a023475de1ce2891916807632d9ee15e382326c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81737375"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Stažení a instalace binárního souboru klienta Linkerd Linkerd

V prostředí založeném na prostředí `Invoke-WebRequest` PowerShell v systému Windows použijte ke stažení verze Linkerd následujícím způsobem:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

Binární `linkerd` klient skáče v klientském počítači a umožňuje interakci s mřížkou služby Linkerd. Pomocí následujících příkazů nainstalujte binární `linkerd` soubor klienta Linkerd do prostředí založeného na prostředí PowerShell v systému Windows. Tyto příkazy `linkerd` zkopírují binární soubor klienta do složky Linkerd a poté jej zpřístupní okamžitě `PATH`(v aktuálním prostředí) a trvale (v rámci restartování prostředí) prostřednictvím aplikace . Ke spuštění těchto příkazů nepotřebujete zvýšená oprávnění (Správce) a nemusíte restartovat prostředí.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Linkerd\"
```