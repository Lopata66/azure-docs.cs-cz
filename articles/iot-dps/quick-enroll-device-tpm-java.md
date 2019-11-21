---
title: Enroll TPM device to Azure Device Provisioning Service using Java
description: Quickstart - Enroll TPM device to Azure IoT Hub Device Provisioning Service using Java service SDK. V tomto rychlém startu se používají jednotlivé registrace.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: e1ef20b8e4ef865c93c31265d4b036c34b4b1aee
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229553"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-java-service-sdk"></a>Quickstart: Enroll TPM device to IoT Hub Device Provisioning Service using Java service SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]


Tyto kroky ukazují, jak prostřednictvím kódu programu vytvořit jednotlivou registraci pro simulované zařízení TPM ve službě Azure IoT Hub Device Provisioning pomocí sady [SDK služby pro Javu](https://azure.github.io/azure-iot-sdk-java/service/) a ukázkové aplikace v Javě. Přestože sada SDK služby pro Javu funguje na počítačích s Windows i Linuxem, tento článek provádí procesem registrace s použitím vývojového počítače s Windows.

Než budete pokračovat, nezapomeňte [nastavit službu IoT Hub Device Provisioning pomocí webu Azure Portal](./quick-setup-auto-provision.md) a [nasimulovat zařízení TPM](quick-create-simulated-device.md#simulatetpm).

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Příprava vývojového prostředí 

1. Ujistěte se, že na svém počítači máte nainstalované prostředí [Java SE Development Kit 8](https://aka.ms/azure-jdks). 

2. Nastavte proměnné prostředí pro vaši instalaci Javy. Proměnná `PATH` by měla obsahovat úplnou cestu k adresáři *jdk1.8.x\bin*. Pokud se jedná o první instalaci Javy na vašem počítači, vytvořte novou proměnnou prostředí `JAVA_HOME` a přidejte do ní odkaz na úplnou cestu k adresáři *jdk1.8.x*. Na počítači s Windows se tento adresář nachází ve složce *C:\\Program Files\\Java\\* a proměnné prostředí můžete vytvořit nebo upravit tak, že v **Ovládacích panelech** na počítači s Windows vyhledáte možnost **Upravit proměnné prostředí systému**. 

   Úspěšné nastavení Javy na vašem počítači můžete zkontrolovat spuštěním následujícího příkazu v příkazovém okně:

    ```cmd\sh
    java -version
    ```

3. Stáhněte a rozbalte na svém počítači [Maven 3](https://maven.apache.org/download.cgi). 

4. Upravte proměnnou prostředí `PATH` tak, aby odkazovala na složku *apache-maven-3.x.x\\bin* ve složce, kam se extrahoval Maven. Úspěšnou instalaci Mavenu můžete ověřit spuštěním tohoto příkazu v příkazovém okně:

    ```cmd\sh
    mvn --version
    ```

5. Ujistěte se, že je na vašem počítači nainstalovaný [git](https://git-scm.com/download/) a že je přidaný do proměnné prostředí `PATH`. 


<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>Stažení a úprava vzorového kódu v Javě

Tato část ukazuje, jak do vzorového kódu přidat podrobnosti o zřizování vašeho zařízení TPM. 

1. Otevřete příkazový řádek. Naklonujte úložiště GitHub se vzorovým kódem pro registraci zařízení pomocí sady SDK služby pro Javu:
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. Ve staženém zdrojovém kódu přejděte do složky s ukázkou **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_** . Otevřete soubor **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_** v libovolném editoru a přidejte následující podrobnosti:

   1. Následujícím způsobem přidejte `[Provisioning Connection String]` pro vaši službu zřizování z portálu:
       1. Přejděte k vaší službě zřizování na webu [Azure Portal](https://portal.azure.com). 
       2. Otevřete **Zásady sdíleného přístupu** a vyberte zásadu s oprávněním *EnrollmentWrite*.
       3. Zkopírujte **Primární připojovací řetězec klíče**. 

           ![Získání připojovacího řetězce pro zřizování z portálu](./media/quick-enroll-device-tpm-java/provisioning-string.png)  

       4. V souboru se vzorovým kódem **_ServiceEnrollmentSample.java_** nahraďte `[Provisioning Connection String]` za **Primární připojovací řetězec klíče**.
    
           ```Java
           private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
           ```

   2. Přidejte podrobnosti o zařízení TPM:
       1. Podle postupu vedoucího k části [Simulace zařízení TPM](quick-create-simulated-device.md#simulatetpm) získejte *ID registrace* a *Ověřovací klíč TPM* pro simulaci zařízení TPM.
       2. Použijte **_ID registrace_** a **_Ověřovací klíč_** z výstupu předchozího kroku k nahrazení `[RegistrationId]` a `[TPM Endorsement Key]` v souboru se vzorovým kódem **_ServiceEnrollmentSample.java_** :
        
           ```Java
           private static final String REGISTRATION_ID = "[RegistrationId]";
           private static final String TPM_ENDORSEMENT_KEY = "[TPM Endorsement Key]";
           ```

   3. Volitelně můžete svou službu zřizování nakonfigurovat pomocí vzorového kódu:
      - Pokud chcete do ukázky přidat tuto konfiguraci, postupujte následovně:
        1. Na webu [Azure Portal](https://portal.azure.com) přejděte k centru IoT propojenému s vaší službou zřizování. Otevřete kartu **Přehled** tohoto centra a zkopírujte **Název hostitele**. Přiřaďte tento **Název hostitele** k parametru *IOTHUB_HOST_NAME* (NÁZEV_HOSTITELE_CENTRA_IOT).
            ```Java
            private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
            ```
        2. Parametru *DEVICE_ID* (ID_ZAŘÍZENÍ) přiřaďte popisný název a u parametru *PROVISIONING_STATUS* (STAV_ZŘIZOVÁNÍ) ponechte výchozí hodnotu *ENABLED* (POVOLENO). 
    
      - NEBO, pokud se rozhodnete svoji službu zřizování nekonfigurovat, nezapomeňte okomentovat nebo odstranit následující příkazy v souboru _ServiceEnrollmentSample.java_:
          ```Java
          // The following parameters are optional. Remove it if you don't need.
          individualEnrollment.setDeviceId(DEVICE_ID);
          individualEnrollment.setIotHubHostName(IOTHUB_HOST_NAME);
          individualEnrollment.setProvisioningStatus(PROVISIONING_STATUS);
          ```

   4. Prostudujte si vzorový kód. Tento kód vytvoří, aktualizuje, dotazuje a odstraní jednotlivou registraci zařízení TPM. Pokud chcete ověřit úspěšnou registraci na portálu, dočasně okomentujte následující řádky kódu na konci souboru _ServiceEnrollmentSample.java_:
    
       ```Java
       // *********************************** Delete info of individualEnrollment ************************************
       System.out.println("\nDelete the individualEnrollment...");
       provisioningServiceClient.deleteIndividualEnrollment(REGISTRATION_ID);
       ```

   5. Uložte soubor _ServiceEnrollmentSample.java_.

<a id="runjavasample"></a>

## <a name="build-and-run-the-java-sample-code"></a>Sestavení a spuštění vzorového kódu v Javě

1. Otevřete příkazové okno a přejděte do složky **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_** .

2. Sestavte vzorový kód pomocí tohoto příkazu:

    ```cmd\sh
    mvn install -DskipTests
    ```

   Tento příkaz na váš počítač stáhne balíček Maven [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client). Tento balíček obsahuje binární soubory sady SDK služby pro Javu, které vzorový kód vyžaduje k sestavení. 

3. Spusťte ukázku pomocí těchto příkazů v příkazovém okně:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-sample-{version}-with-deps.jar
    ```

4. Sledujte v okně výstupu úspěšnou registraci. 

5. Přejděte k vaší službě zřizování na webu Azure Portal. Click **Manage enrollments**, and select the **Individual Enrollments** tab. Notice that the *Registration ID* of your simulated TPM device is now listed. 

    ![Ověření úspěšné registrace TPM na portálu](./media/quick-enroll-device-tpm-java/verify-tpm-enrollment.png)  

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete prozkoumat ukázku služby v Javě, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Zavřete na svém počítači okno výstupu ukázky v Javě.
1. Zavřete okno simulátoru TPM, pokud jste ho otevřeli pro simulaci zařízení TPM.
1. Navigate to your Device Provisioning service in the Azure portal, click **Manage enrollments**, and then select the **Individual Enrollments** tab. Select the *Registration ID* of the device you enrolled using this Quickstart, and click the **Delete** button at the top of the blade. 

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste do své služby Device Provisioning zaregistrovali simulované zařízení TPM. Pokud se chcete se zřizováním zařízení seznámit podrobněji, pokračujte ke kurzu nastavení služby Device Provisioning na webu Azure Portal. 

> [!div class="nextstepaction"]
> [Kurzy pro službu Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)
