---
title: Nainstalovat Azure IoT Edge pro Windows | Microsoft Docs
description: Instalace Azure IoT Edge pro kontejnery Windows na zařízeních s Windows
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: kgremban
ms.openlocfilehash: 7857f93e8c767f270041bb6bf041447786ce19ff
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633857"
---
# <a name="install-and-manage-azure-iot-edge-for-windows"></a>Instalace a Správa Azure IoT Edge pro Windows

Azure IoT Edge pro Windows běží přímo na hostitelském zařízení s Windows a používá kontejnery Windows ke spuštění obchodní logiky na hraničních zařízeních.

Azure IoT Edge modul runtime je tím, že zařízení přepíná do IoT Edge zařízení. Modul runtime se dá na zařízeních nasadit tak, jak malý, jako např. v/v jako průmyslový Server. Jakmile v zařízení nakonfigurujete modul runtime IoT Edge, můžete do něj z cloudu začít nasazovat obchodní logiku. Další informace najdete v tématu [pochopení Azure IoT Edge runtime a jeho architektury](iot-edge-runtime.md).

>[!NOTE]
>Azure IoT Edge pro systém Windows nebude podporován počínaje verzí 1.2.0 Azure IoT Edge.
>
>Zvažte použití nové metody pro spuštění IoT Edge na zařízeních s Windows Azure IoT Edge pro Linux ve Windows.

<!-- TODO: link to EFLOW-->

Existují dva kroky pro nastavení IoT Edge zařízení. Prvním krokem je instalace modulu runtime a jeho závislostí. Druhým krokem je připojit zařízení k jeho identitě v cloudu a nastavit ověřování pomocí IoT Hub.

V tomto článku jsou uvedené kroky pro instalaci modulu Azure IoT Edge runtime na zařízeních s Windows. Když instalujete modul runtime, budete mít možnost používat kontejnery systému Linux nebo kontejnery Windows. V současné době se v produkčních scénářích podporují jenom kontejnery Windows ve Windows. Kontejnery pro Linux ve Windows jsou užitečné pro scénáře vývoje a testování, zejména pokud vyvíjíte na počítači s Windows, abyste mohli nasadit na zařízení se systémem Linux.

## <a name="prerequisites"></a>Požadavky

* Zařízení s Windows

  IoT Edge s kontejnery Windows vyžaduje systém Windows verze 1809/Build 17762, což je nejnovější [sestavení s podporou dlouhodobé podpory Windows](/windows/release-information/). Pro vývojové a testovací scénáře budou fungovat všechny SKU (pro, Enterprise, server atd.), které podporují funkci Containers. Před vstupem do produkčního prostředí však nezapomeňte [seznam podporovaných systémů](support.md#operating-systems) zkontrolovat.

  IoT Edge s kontejnery pro Linux lze spustit v libovolné verzi systému Windows, která splňuje [požadavky pro Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

* Podpora kontejneru na zařízení

  Azure IoT Edge spoléhá na modul kontejneru, který je kompatibilní s rozhraním [OCI](https://www.opencontainers.org/) . Ujistěte se, že vaše zařízení dokáže podporovat kontejnery.

  Pokud instalujete IoT Edge na virtuálním počítači, povolte vnořenou virtualizaci a přidělte alespoň 2 GB paměti. Virtuální počítače generace 2 pro Hyper-V mají ve výchozím nastavení vnořenou virtualizaci povolenou. Pro VMware je k dispozici přepínač pro povolení funkce na vašem virtuálním počítači.

  Pokud instalujete IoT Edge na zařízení IoT Core, pomocí následujícího příkazu ve [vzdálené relaci PowerShellu](/windows/iot-core/connect-your-device/powershell) ověřte, jestli jsou kontejnery Windows na vašem zařízení podporované:

  ```powershell
  Get-Service vmcompute
  ```

* [ID registrovaného zařízení](how-to-register-device.md)

  Pokud jste zařízení zaregistrovali pomocí ověřování symetrického klíče, připravte si připojovací řetězec zařízení.

  Pokud jste zařízení zaregistrovali pomocí ověřování certifikátu X. 509, musí mít alespoň jeden z certifikátů identity, které jste použili k registraci zařízení, a odpovídající privátní privátní klíč, který je k dispozici na vašem zařízení.

## <a name="install-a-container-engine"></a>Instalace modulu kontejneru

Azure IoT Edge spoléhá na modul runtime kontejneru kompatibilní s rozhraním OCI. V produkčních scénářích doporučujeme použít modul založený na Moby. Modul Moby je jediným kontejnerovým modulem, který je oficiálně podporován s Azure IoT Edge. Image kontejnerů Docker CE/EE jsou kompatibilní s modulem runtime Moby.

V produkčních scénářích použijte modul založený na Moby, který je součástí instalačního skriptu. Není k dispozici žádný další postup pro instalaci modulu.

Pro IoT Edge s kontejnery pro Linux je potřeba poskytnout vlastní modul runtime kontejneru. Než budete pokračovat, nainstalujte na zařízení [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) a nakonfigurujte ho tak, aby [používal kontejnery Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) .

## <a name="install-the-iot-edge-security-daemon"></a>Instalace démona zabezpečení IoT Edge

>[!TIP]
>Pro zařízení IoT Core doporučujeme spouštět instalační příkazy pomocí vzdálené relace PowerShellu. Další informace najdete v tématu [použití PowerShellu pro Windows IoT](/windows/iot-core/connect-your-device/powershell).

1. Spusťte PowerShell jako správce.

   Použijte relaci AMD64 PowerShellu, ne PowerShell (x86). Pokud si nejste jistí, který typ relace používáte, spusťte následující příkaz:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Spusťte příkaz [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) , který provede následující úlohy:

   * Kontroluje, zda má počítač s Windows podporovanou verzi.
   * Zapne funkci Containers.
   * Stáhne modul Moby a modul runtime IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

   `Deploy-IoTEdge`Příkaz ve výchozím nastavení používá kontejnery Windows. Pokud chcete použít kontejnery pro Linux, přidejte `ContainerOs` parametr:

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

3. V tuto chvíli se zařízení IoT Core můžou restartovat automaticky. Zařízení s Windows 10 nebo Windows Server vás můžou vyzvat k restartování. Pokud ano, restartujte zařízení nyní.

Při instalaci IoT Edge na zařízení můžete pomocí dalších parametrů upravit proces, včetně:

* Směrování provozu pomocí proxy server
* Najeďte instalačnímu programu na místní adresář pro instalaci offline.

Další informace o těchto dalších parametrech najdete v tématu [skripty PowerShellu pro IoT Edge ve Windows](reference-windows-scripts.md).

## <a name="provision-the-device-with-its-cloud-identity"></a>Zřízení zařízení pomocí cloudové identity

Teď, když je v zařízení nainstalovaný modul pro vytváření kontejnerů a IoT Edge runtime, jste připraveni k dalšímu kroku, ve kterém můžete nastavit zařízení s jeho identitou v cloudu a ověřovacími informacemi.

V závislosti na typu ověřování, který chcete použít, vyberte další oddíl:

* [Možnost 1: ověření pomocí symetrických klíčů](#option-1-authenticate-with-symmetric-keys)
* [Možnost 2: ověření pomocí certifikátů X. 509](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Možnost 1: ověření pomocí symetrických klíčů

V tomto okamžiku je IoT Edge modul runtime nainstalovaný na zařízení s Windows a Vy musíte zřídit zařízení s jeho identitou v cloudu a ověřovacími informacemi.

Tato část vás provede postupem zřízení zařízení s ověřováním pomocí symetrického klíče. Zařízení byste měli zaregistrovali v IoT Hub a z informací o zařízení se načetl připojovací řetězec. V takovém případě postupujte podle pokynů v části [registrace zařízení IoT Edge v IoT Hub](how-to-register-device.md).

1. Na zařízení IoT Edge spusťte PowerShell jako správce.

2. Pomocí příkazu [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) nakonfigurujte na svém počítači modul runtime IoT Edge. Příkaz je standardně nastaven na ruční zřizování pomocí kontejnerů Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Pokud používáte kontejnery platformy Linux, přidejte `-ContainerOs` do příznaku parametr. Být konzistentní s možností kontejneru, kterou jste zvolili pomocí `Deploy-IoTEdge` příkazu, který jste předtím spustili.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * Pokud jste do svého zařízení stáhli IoTEdgeSecurityDaemon.ps1 skript pro instalaci offline nebo konkrétní verze, nezapomeňte na místní kopii skriptu odkazovat.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Po zobrazení výzvy zadejte připojovací řetězec zařízení, který jste získali v předchozí části. Připojovací řetězec zařízení přidružuje fyzické zařízení k ID zařízení v IoT Hub a poskytne ověřovací informace.

   Připojovací řetězec zařízení má následující formát a nesmí obsahovat uvozovky: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

Při ručním zřizování zařízení můžete pomocí dalších parametrů upravit proces, včetně:

* Směrování provozu pomocí proxy server
* Deklarovat konkrétní image kontejneru edgeAgent a zadat přihlašovací údaje, pokud se nachází v privátním registru

Další informace o těchto dalších parametrech najdete v tématu [skripty PowerShellu pro IoT Edge ve Windows](reference-windows-scripts.md).

### <a name="option-2-authenticate-with-x509-certificates"></a>Možnost 2: ověření pomocí certifikátů X. 509

V tomto okamžiku je IoT Edge modul runtime nainstalovaný na zařízení s Windows a Vy musíte zřídit zařízení s jeho identitou v cloudu a ověřovacími informacemi.

V této části se seznámíte s postupem zřízení zařízení s ověřováním pomocí certifikátu X. 509. Měli byste mít zaregistrované zařízení v IoT Hub a poskytnou se kryptografické otisky, které odpovídají certifikátu a soukromému klíči, který se nachází v IoT Edgem zařízení. V takovém případě postupujte podle pokynů v části [registrace zařízení IoT Edge v IoT Hub](how-to-register-device.md).

1. Na zařízení IoT Edge spusťte PowerShell jako správce.

2. Pomocí příkazu [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) nakonfigurujte na svém počítači modul runtime IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Pokud používáte kontejnery platformy Linux, přidejte `-ContainerOs` do příznaku parametr. Být konzistentní s možností kontejneru, kterou jste zvolili pomocí `Deploy-IoTEdge` příkazu, který jste předtím spustili.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * Pokud jste do svého zařízení stáhli IoTEdgeSecurityDaemon.ps1 skript pro instalaci offline nebo konkrétní verze, nezapomeňte na místní kopii skriptu odkazovat.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Po zobrazení výzvy zadejte tyto informace:

   * **IotHubHostName**: název hostitele IoT Hub, ke kterému se bude zařízení připojovat. Například `{IoT hub name}.azure-devices.net`.
   * **DeviceID**: ID, které jste zadali při registraci zařízení.
   * **X509IdentityCertificate**: absolutní cesta k certifikátu identity na zařízení. Například `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey**: absolutní cesta k souboru privátního klíče pro poskytnutý certifikát identity. Například `C:\path\identity_key.pem`.

Při ručním zřizování zařízení můžete pomocí dalších parametrů upravit proces, včetně:

* Směrování provozu pomocí proxy server
* Deklarovat konkrétní image kontejneru edgeAgent a zadat přihlašovací údaje, pokud se nachází v privátním registru

Další informace o těchto dalších parametrech najdete v tématu [skripty PowerShellu pro IoT Edge ve Windows](reference-windows-scripts.md).

## <a name="offline-or-specific-version-installation-optional"></a>Instalace offline nebo konkrétní verze (volitelné)

Kroky v této části se týkají scénářů, které nejsou zahrnuté do standardních kroků instalace. To může zahrnovat:

* Nainstalovat IoT Edge v režimu offline
* Nainstalovat Release Candidate verzi
* Nainstalovat jinou verzi než nejnovější

Během instalace se stáhnou tři soubory:

* Skript prostředí PowerShell, který obsahuje pokyny k instalaci
* Microsoft Azure IoT Edge soubor CAB obsahující rozhraní Moby (IoT Edge Security Daemon), modul kontejnerů Moby a CLI
* Instalační program pro Visual C++ Distribuovatelný balíček (VC Runtime)

Pokud bude vaše zařízení během instalace offline nebo pokud chcete nainstalovat určitou verzi IoT Edge, můžete tyto soubory stáhnout na zařízení předem. Až bude čas na instalaci, najeďte instalační skript v adresáři, který obsahuje stažené soubory. Instalační program nejprve zkontroluje adresář a pak stáhne pouze součásti, které nebyly nalezeny. Pokud jsou všechny soubory k dispozici v režimu offline, můžete nainstalovat bez připojení k Internetu.

1. Nejnovější instalační soubory IoT Edge spolu s předchozími verzemi najdete v tématu [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

2. Vyhledejte verzi, kterou chcete nainstalovat, a Stáhněte si následující soubory z části **assets (prostředky** ) poznámky k verzi do zařízení IoT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab z verzí 1.0.9 nebo novější, nebo Microsoft-Azure-IoTEdge.cab z verzí 1.0.8 a starších.

   Microsoft-Azure-IotEdge-arm32.cab je k dispozici také pro účely testování, od 1.0.9. IoT Edge se v současné době nepodporují na zařízeních s Windows ARM32.

   Je důležité použít skript prostředí PowerShell ze stejné verze, jako je soubor. cab, který používáte, protože funkce se mění tak, aby podporovaly funkce v jednotlivých vydaných verzích.

3. Pokud má soubor. cab, který jste stáhli, příponu architektury, přejmenujte soubor na pouze **Microsoft-Azure-IoTEdge.cab**.

4. Volitelně můžete stáhnout instalační program pro Visual C++ Redistributable. Například skript PowerShellu používá tuto verzi: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Uložte instalační program do stejné složky v zařízení IoT jako soubory IoT Edge.

5. Chcete [-li nainstalovat](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) nástroje s offline komponentami, poznamenejte si místní kopii skriptu PowerShellu.

6. Spusťte příkaz [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) s `-OfflineInstallationPath` parametrem. Zadejte absolutní cestu k adresáři souborů. Třeba

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   Příkaz pro nasazení bude používat všechny součásti, které jsou k dispozici v adresáři místního souboru. Pokud chybí soubor. cab nebo instalační program Visual C++, pokusí se ho stáhnout.

## <a name="update-iot-edge"></a>Aktualizovat IoT Edge

Pomocí `Update-IoTEdge` příkazu aktualizujte démona zabezpečení. Skript automaticky vyžádá nejnovější verzi démona zabezpečení.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Spuštění příkazu Update-IoTEdge odstraní a aktualizuje démona zabezpečení ze zařízení spolu se dvěma bitovými kopiemi kontejnerů modulu runtime. Soubor config. yaml se uchovává v zařízení a také data z modulu kontejneru Moby (Pokud používáte kontejnery Windows). Udržování informací o konfiguraci znamená, že v průběhu procesu aktualizace nemusíte znovu zadávat informace o připojovacím řetězci nebo službě Device Provisioning pro vaše zařízení.

Pokud chcete aktualizovat na konkrétní verzi démona zabezpečení, vyhledejte verzi, kterou chcete cílit z [IoT Edge verzí](https://github.com/Azure/azure-iotedge/releases). V této verzi Stáhněte soubor **Microsoft-Azure-IoTEdge.cab** . Pak použijte parametr, `-OfflineInstallationPath` který odkazuje na umístění místního souboru. Například:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>`-OfflineInstallationPath`Parametr vyhledá soubor s názvem **Microsoft-Azure-IoTEdge.cab** v zadaném adresáři. Počínaje IoT Edge verzí 1.0.9-RC4 jsou k dispozici dva soubory. cab, jeden pro zařízení AMD64 a jeden pro ARM32. Stáhněte si správný soubor pro vaše zařízení a pak přejmenujte soubor, abyste odebrali příponu architektury.

Pokud chcete aktualizovat zařízení offline, vyhledejte verzi, kterou chcete cílit z [Azure IoT Edge verzí](https://github.com/Azure/azure-iotedge/releases). V této verzi stáhněte *IoTEdgeSecurityDaemon.ps1* a soubory *Microsoft-Azure-IoTEdge.cab* . Je důležité použít skript prostředí PowerShell ze stejné verze, jako je soubor. cab, který používáte, protože funkce se mění tak, aby podporovaly funkce v jednotlivých vydaných verzích.

Pokud má soubor. cab, který jste stáhli, příponu architektury, přejmenujte soubor na pouze **Microsoft-Azure-IoTEdge.cab**.

Chcete-li aktualizovat s offline [komponentami, poznamenejte si místní](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) kopii skriptu PowerShellu. Pak použijte `-OfflineInstallationPath` parametr jako součást `Update-IoTEdge` příkazu a zadejte absolutní cestu k adresáři souborů. Třeba

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

Další informace o možnostech aktualizace získáte pomocí příkazu, `Get-Help Update-IoTEdge -full` nebo se podívejte na [skript PowerShellu pro IoT Edge ve Windows](reference-windows-scripts.md).

## <a name="uninstall-iot-edge"></a>Odinstalace IoT Edge

Pokud chcete odebrat instalaci IoT Edge ze zařízení, použijte následující příkazy.

Pokud chcete odebrat instalaci IoT Edge ze zařízení s Windows, použijte příkaz [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) z okna pro správu prostředí PowerShell. Tento příkaz odebere modul runtime IoT Edge společně se stávající konfigurací a daty modulu Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

`Uninstall-IoTEdge`Příkaz nefunguje na Windows IoT Core. Pokud chcete odebrat IoT Edge, musíte znovu nasadit image Windows IoT Core.

Další informace o možnostech odinstalace získáte pomocí příkazu `Get-Help Uninstall-IoTEdge -full` .

## <a name="next-steps"></a>Další kroky

Pokračujte v [nasazení IoT Edge moduly](how-to-deploy-modules-portal.md) a Naučte se, jak nasadit moduly do svého zařízení.
