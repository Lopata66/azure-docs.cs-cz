---
title: Nasazení Azure Security Center pro modul IoT Edge | Microsoft Docs
description: Přečtěte si, jak nasadit Azure Security Center pro agenta zabezpečení IoT v IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2019
ms.author: mlottner
ms.openlocfilehash: 7dff2a88da2e12388bfb3a97cfdad236045170cf
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543881"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Nasazení modulu zabezpečení na zařízení IoT Edge


**Azure Security Center pro modul IoT** poskytuje komplexní řešení zabezpečení pro vaše IoT Edgeá zařízení.
Modul zabezpečení shromažďuje, agreguje a analyzuje nezpracovaná data zabezpečení z operačního systému a systému kontejnerů do užitečných doporučení zabezpečení a výstrah.
Další informace najdete v tématu [modul zabezpečení pro IoT Edge](security-edge-architecture.md).

V tomto článku se dozvíte, jak nasadit modul zabezpečení na zařízení IoT Edge.

## <a name="deploy-security-module"></a>Nasadit modul zabezpečení

Pomocí následujících kroků nasaďte Azure Security Center pro modul zabezpečení IoT pro IoT Edge.

### <a name="prerequisites"></a>Požadavky

1. V IoT Hub se ujistěte, že je zařízení [registrované jako IoT Edge zařízení](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

1. Azure Security Center pro modul IoT Edge vyžaduje, aby na zařízení IoT Edge bylo nainstalované [auditované rozhraní](https://linux.die.net/man/8/auditd) .

    - Nainstalujte rozhraní spuštěním následujícího příkazu na zařízení IoT Edge:
   
    `sudo apt-get install auditd audispd-plugins`

    - Ověřte, že je audit aktivní, spuštěním následujícího příkazu: 
   
    `sudo systemctl status auditd`<br>
    - Očekávaná odpověď je: `active (running)` 
        

### <a name="deployment-using-azure-portal"></a>Nasazení pomocí Azure Portal

1. Z Azure Portal otevřete **Marketplace**.

1. Vyberte **Internet věcí**, vyhledejte **Azure Security Center pro IoT** a vyberte ji.

   ![Vyberte Azure Security Center pro IoT](media/howto/edge-onboarding-8.png)

1. Kliknutím na **vytvořit** nakonfigurujete nasazení. 

1. Zvolte **předplatné** Azure vašeho IoT Hub a pak vyberte svoji **IoT Hub**.<br>Vyberte **nasadit do zařízení** pro zaměření na jedno zařízení, nebo vyberte **nasadit ve velkém měřítku** , abyste mohli cílit na více zařízení, a klikněte na **vytvořit**. Další informace o nasazení ve velkém měřítku najdete v tématu [nasazení](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >Pokud jste vybrali možnost **nasadit ve škálování**, před pokračováním na kartu **přidat moduly** v následujících pokynech přidejte název a podrobnosti o zařízení.     

Dokončete každý krok, abyste dokončili nasazení IoT Edge pro Azure Security Center pro IoT. 

#### <a name="step-1-modules"></a>Krok 1: moduly

1. Vyberte modul **AzureSecurityCenterforIoT** .
1. Na kartě **nastavení modulu** změňte **název** na **azureiotsecurity**.
1. Na kartě **proměnné vyhledáte** přidejte proměnnou v případě potřeby (například úroveň ladění).
1. Na kartě **možnosti vytvoření kontejneru** přidejte následující konfiguraci:

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }    
    ```
    
1. Na kartě **nastavení s dvojitým modulem** přidejte následující konfiguraci:
      
    ``` json
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration":{}
    ```

1. Vyberte **Aktualizovat**.

#### <a name="step-2-runtime-settings"></a>Krok 2: nastavení modulu runtime

1. Vyberte **nastavení modulu runtime**.
1. V části **hraniční centrum**změňte **Obrázek** na **MCR.Microsoft.com/azureiotedge-hub:1.0.8.3**.
1. Ověřte, že **Možnosti vytváření** jsou nastavené na následující konfiguraci: 
         
    ``` json
    { 
       "HostConfig":{ 
          "PortBindings":{ 
             "8883/tcp":[ 
                { 
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[ 
                { 
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[ 
                { 
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```
    
1. Vyberte **Save** (Uložit).
   
1. Vyberte **Next** (Další).

#### <a name="step-3-specify-routes"></a>Krok 3: určení tras 

1. Na kartě **zadat trasy** se ujistěte, že máte trasu (explicitní nebo implicitní), která bude předávat zprávy z modulu **azureiotsecurity** , aby se **$upstream** podle následujících příkladů. Pouze v případě, že je trasa zavedena, vyberte možnost **Další**.

   Příklady tras:

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream" 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ~~~

1. Vyberte **Next** (Další).

#### <a name="step-4-review-deployment"></a>Krok 4: Kontrola nasazení

- Na kartě **zkontrolovat nasazení** zkontrolujte informace o vašem nasazení a pak výběrem **vytvořit** dokončete nasazení.

## <a name="diagnostic-steps"></a>Kroky diagnostiky

Pokud narazíte na problém, je nejlepším způsobem, jak se dozvědět o stavu zařízení modulu zabezpečení IoT Edge, jsou protokoly kontejnerů. Ke shromáždění informací použijte příkazy a nástroje uvedené v této části.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Ověřte, že požadované kontejnery jsou nainstalované a fungují podle očekávání.

1. Na zařízení IoT Edge spusťte následující příkaz:
    
    `sudo docker ps`
   
1. Ověřte, zda jsou spuštěny následující kontejnery:
   
   | Name (Název) | IMAGE |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.1 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |
   
   Pokud nejsou k dispozici minimální požadované kontejnery, ověřte, zda je váš manifest nasazení IoT Edge zarovnán s doporučeným nastavením. Další informace najdete v tématu [nasazení modulu IoT Edge](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Kontrola chyb v protokolech modulů
   
1. Na zařízení IoT Edge spusťte následující příkaz:

   `sudo docker logs azureiotsecurity`
   
1. Pro podrobnější protokoly přidejte následující proměnnou prostředí do nasazení modulu **azureiotsecurity** : `logLevel=Debug`.

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o možnostech konfigurace, přejděte k příručce Průvodce konfigurací modulu. 
> [!div class="nextstepaction"]
> [Průvodce konfigurací modulů](./how-to-agent-configuration.md)
