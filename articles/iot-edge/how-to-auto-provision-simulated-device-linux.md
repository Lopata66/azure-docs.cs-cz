---
title: Automatické zřizování zařízení Linux s DPS - Azure IoT Edge | Dokumentace Microsoftu
description: Použít čip TPM simulované na virtuální počítač s Linuxem k otestování služby Azure Device Provisioning Service pro Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9a549221a9e1864e1b7565f35139cb4c2a6ca65e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58093122"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Vytvoření a zřízení zařízení IoT Edge s virtuální čip TPM na virtuální počítač s Linuxem

Zařízení Azure IoT Edge je možné automaticky zřizovat pomocí [služby Device Provisioning](../iot-dps/index.yml). Pokud neznáte proces autoprovisioning, přečtěte si [autoprovisioning koncepty](../iot-dps/concepts-auto-provisioning.md) než budete pokračovat. 

Tento článek ukazuje, jak otestovat autoprovisioning na simulovaném zařízení IoT Edge pomocí následujících kroků: 

* Vytvoření virtuálního počítače (VM) s Linuxem v technologii Hyper-V s Simulovaná Trusted Platform Module (TPM) pro zabezpečení hardwaru.
* Vytvoření instance z IoT Hubu zařízení zřizování služby (DPS).
* Vytvořit jednotlivou registraci pro zařízení
* Nainstalujte modul runtime IoT Edge a připojení zařízení k centru IoT

Kroky v tomto článku jsou určená pro účely testování.

## <a name="prerequisites"></a>Požadavky

* Vývojový počítač s Windows s [Hyper-V povolené](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Tento článek používá Windows 10, spuštění virtuálního počítače s Ubuntu Server. 
* Aktivním centrem IoT. 

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Vytvoření virtuálního počítače s Linuxem pomocí virtuální čip TPM

V této části vytvoříte nový virtuální počítač Linux v technologii Hyper-V. Nakonfigurovat tento virtuální počítač s Simulovaná čipem TPM, takže ho můžete použít pro testování funkce Automatické zřizování funguje s IoT Edge. 

### <a name="create-a-virtual-switch"></a>Vytvoření virtuálního přepínače

Virtuální přepínač umožňuje ve virtuálním počítači pro připojení k fyzické síti.

1. Otevřete Správce technologie Hyper-V na svém počítači s Windows. 

2. V **akce** nabídce vyberte možnost **Správce virtuálních přepínačů**. 

3. Zvolte **externí** virtuální přepínače a potom vyberte **vytvořit virtuální přepínač**. 

4. Pojmenujte nový virtuální přepínač, například **EdgeSwitch**. Ujistěte se, že typ připojení je nastaven na **externí síť**a pak vyberte **Ok**.

5. Automaticky otevírané okno vás upozorní, že může přerušit připojení k síti. Vyberte **Ano** pokračujte. 

Pokud se zobrazí chyby při vytváření nového virtuálního přepínače, ujistěte se, žádné přepínače používáte adaptér sítě ethernet a že žádné další přepínače použijte stejný název. 

### <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. Stažení souboru bitové kopie disku pro virtuální počítač a uloží do místního prostředí. Například [Ubuntu server](https://www.ubuntu.com/download/server). 

2. Ve Správci technologie Hyper-V, znovu vyberte **nový** > **virtuálního počítače** v **akce** nabídky.

3. Dokončení **Průvodce novým virtuálním počítačem** s následující konkrétní konfigurace:

   1. **Zadejte generování**: Vyberte **2. generace**. Virtuální počítače generace 2 mají vnořená virtualizace povolena, což je potřeba ke spouštění IoT Edge na virtuálním počítači.
   2. **Konfigurace sítí**: Nastavte hodnotu **připojení** k virtuálnímu přepínači, který jste vytvořili v předchozí části. 
   3. **Možnosti instalace**: Vyberte **nainstalovat operační systém ze souboru bitové spouštěcí kopie** a přejděte do souboru image disku, který jste uložili místně.

4. Vyberte **Dokončit** v průvodci k vytvoření virtuálního počítače.

Může trvat několik minut pro vytvoření nového virtuálního počítače. 

### <a name="enable-virtual-tpm"></a>Povolit virtuální čip TPM

Po vytvoření virtuálního počítače, otevřete její nastavení umožní čipu virtuální trusted platform module (TPM), která vám umožní autoprovision zařízení. 

1. Vyberte virtuální počítač a pak otevřete jeho **nastavení**.

2. Přejděte do **zabezpečení**. 

3. Zrušte zaškrtnutí políčka **povolení zabezpečeného spouštění**.

4. Zkontrolujte **povolit Trusted Platform Module**. 

5. Klikněte na **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Spustit virtuální počítač a shromažďovat TPM data

Ve virtuálním počítači sestavení SDK pro jazyk C nástroj, který můžete použít k načtení zařízení **ID registrace** a **ověřovací klíč**. 

1. Spustit virtuální počítač a připojte se k němu.

2. Postupujte podle pokynů v rámci virtuálního počítače pro dokončení procesu instalace a restartování počítače. 

3. Přihlaste se ke svému virtuálnímu počítači, postupujte podle pokynů v [nastavte vývojové prostředí Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) k instalaci a sestavení sady SDK pro zařízení Azure IoT pro C. 

   >[!TIP]
   >V tomto článku se budete zkopírujte a vložte z virtuálního počítače, který není prostřednictvím aplikace připojení Správce technologie Hyper-V. Můžete chtít připojit k virtuálnímu počítači pomocí Správce technologie Hyper-V jednou k načtení jeho IP adresu: `ifconfig`. Potom můžete použít IP adresu pro připojení přes SSH: `ssh <username>@<ipaddress>`.

4. Spusťte následující příkazy k sestavení SDK pro jazyk C nástroj, který načítá informace o zřizování vašeho zařízení. 

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

5. Zkopírujte hodnoty **ID registrace** a **ověřovací klíč**. Tyto hodnoty použijete k vytvoření jednotlivé registrace pro zařízení do služby Device Provisioning. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Nastavte si IoT Hub Device Provisioning Service

Vytvořit novou instanci IoT Hub Device Provisioning Service v Azure a propojit jej do služby IoT hub. Můžete podle pokynů v [nastavení IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Jakmile budete mít spuštěné služby Device Provisioning Service, zkopírujte hodnotu **rozsah ID** na stránce Přehled. Tuto hodnotu použijete při konfiguraci modulu runtime IoT Edge. 

## <a name="create-a-dps-enrollment"></a>Vytvoření registrace distribučních bodů

Načíst informace o zajištění z vašeho virtuálního počítače a použít k vytvoření jednotlivé registrace do služby Device Provisioning. 

Až vytvořit registraci ve službě Device Provisioning, budete mít příležitost k deklaraci **počáteční stav Dvojčete zařízení**. Ve dvojčeti zařízení můžete nastavit značky k seskupení zařízení podle libovolné metriky, které potřebujete ve vašem řešení, jako je oblast, prostředí, umístění nebo zařízení typu. Tyto značky se používají k vytváření [automatické nasazení](how-to-deploy-monitor.md). 


1. V [webu Azure portal](https://portal.azure.com)a přejděte k vaší instanci IoT Hub Device Provisioning Service. 

2. V části **nastavení**vyberte **Správa registrací**. 

3. Vyberte **přidat jednotlivou registraci** pak dokončete následující postup pro konfiguraci registrace:  

   1. Pro **mechanismus**vyberte **TPM**. 
   
   2. Zadejte **ověřovací klíč** a **ID registrace** , který jste zkopírovali z vašeho virtuálního počítače.
   
   3. Vyberte **True** deklarovat, že tento virtuální počítač je zařízení IoT Edge. 
   
   4. Zvolte propojený **služby IoT Hub** , že chcete připojení k zařízení. Můžete vybrat více rozbočovače a zařízení se přiřadí jeden z nich souladu se zásadami pro vybrané přidělení. 
   
   5. Pokud chcete, zadejte ID pro vaše zařízení. ID zařízení můžete cílit na jednotlivá zařízení pro nasazení modulu. Pokud nezadáte ID zařízení, se používá ID registrace.
   
   6. Přidat hodnotu značky k **počáteční stav Dvojčete zařízení** Pokud byste o ni. Značky na cílové skupiny zařízení můžete použít pro nasazení modulu. Příklad: 

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. Vyberte **Uložit**. 

Teď, když registraci pro toto zařízení existuje, modul runtime IoT Edge můžete automaticky zřizovat zařízení během instalace. 

## <a name="install-the-iot-edge-runtime"></a>Nainstalovat modul runtime IoT Edge

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Jeho součástí spouštění v kontejnerech a můžete nasadit další kontejnery do zařízení tak, aby kód můžete spustit na hraničních zařízeních. Nainstalujte modul runtime IoT Edge ve vašem virtuálním počítači. 

Vědět, službě Device Provisioning **rozsah ID** a zařízení **ID registrace** před zahájením článek, který odpovídá typu vašeho zařízení. Pokud jste nainstalovali server se systémem Ubuntu v příkladu, použijte **x64** pokyny. Ujistěte se, že konfigurace modulu runtime IoT Edge není ruční, automatické zřizování. 

* [Instalace modulu runtime Azure IoT Edge v Linuxu (x64)](how-to-install-iot-edge-linux.md)
* [Instalace modulu runtime Azure IoT Edge v Linuxu (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Poskytnout přístup IoT Edge do čipu TPM

Aby modul runtime IoT Edge k automatickému zřízení zařízení potřebuje přístup do čipu TPM. 

Můžete poskytnout přístup TPM na modul runtime IoT Edge tak, že přepíšete systemd nastavení tak, aby **iotedge** služby má oprávnění root. Pokud nechcete, aby ke zvýšení oprávnění služby, můžete použít následující kroky také ručně poskytnout přístup čipu TPM. 

1. Najít cestu k modulu hardwarového TPM na vašem zařízení a uložte ho jako lokální proměnné. 

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Vytvořte nové pravidlo, které vám poskytne IoT Edge, modul runtime přístup k tpm0. 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Otevřete soubor pravidel. 

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Zkopírujte následující informace o přístup do souboru pravidel. 

   ```input 
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Uložte a zavřete soubor. 

6. Aktivace procesu udev systému k vyhodnocení nové pravidlo. 

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Ověřte, že bylo úspěšně aplikováno pravidlo.

   ```bash
   ls -l /dev/tpm0
   ```

   Úspěšný výstup by měl vypadat nějak takto:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Pokud se nezobrazí, zda byla použita správná oprávnění, zkuste restartovat systém aktualizovat udev. 

8. Otevřete soubor přepíše modul runtime IoT Edge. 

   ```bash
   sudo systemctl edit iotedge.service
   ```

9. Přidejte následující kód k vytvoření proměnné prostředí čipu TPM.

   ```input
   [Service]
   Environment=IOTEDGE_USE_TPM_DEVICE=ON
   ```

10. Uložte a zavřete soubor.

11. Ověřte, že toto přepsání bylo úspěšné.

    ```bash
    sudo systemctl cat iotedge.service
    ```

    Úspěšný výstup zobrazuje **iotedge** výchozí služby proměnné a poté zobrazí proměnné prostředí, že jste nastavili **override.conf**. 

12. Znovu načte nastavení.

    ```bash
    sudo systemctl daemon-reload
    ```

## <a name="restart-the-iot-edge-runtime"></a>Restartujte modul runtime IoT Edge

Restartujte modul runtime IoT Edge, aby použila všechny změny konfigurace, které jste provedli v zařízení. 

   ```bash
   sudo systemctl restart iotedge
   ```

Zkontrolujte, zda je spuštěn modul runtime IoT Edge. 

   ```bash
   sudo systemctl status iotedge
   ```

Pokud vidíte chyby zřizování, může být, že změny konfigurace ještě nevstoupilo v platnost. Restartujte démona IoT Edge znovu. 

   ```bash
   sudo systemctl daemon-reload
   ```
   
Nebo, zkuste restartovat virtuální počítač zobrazíte, pokud se změny projeví na začít znovu. 

## <a name="verify-successful-installation"></a>Ověření úspěšné instalace

Pokud modul runtime byl úspěšně spuštěn, můžete přejít do služby IoT Hub a najdete v článku, že byl automaticky zřídit nové zařízení. Vaše zařízení je nyní připraven ke spuštění moduly IoT Edge. 

Kontrola stavu démona IoT Edge.

```cmd/sh
systemctl status iotedge
```

Zkontrolujte démon protokoly.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Seznam s moduly.

```cmd/sh
iotedge list
```

Můžete ověřit, že byl použit jednotlivé registrace, který jste vytvořili ve službě Device Provisioning. Přejděte k vaší instanci služby Device Provisioning na webu Azure Portal. Otevřete podrobnosti registrace pro jednotlivé registrace, který jste vytvořili. Všimněte si, že stav přihlášení je **přiřazené** a zařízení ID je uvedeno. 

## <a name="next-steps"></a>Další postup

Proces registrace služby Device Provisioning umožňuje nastavit ID zařízení a značky dvojčat zařízení ve stejnou dobu, jak zřídit nové zařízení. Tyto hodnoty můžete cílit na jednotlivá zařízení nebo skupin pomocí automatické správy zařízení. Zjistěte, jak [nasazení a monitorování modulů při škálování na portálu Azure IoT Edge](how-to-deploy-monitor.md) nebo [pomocí Azure CLI](how-to-deploy-monitor-cli.md).
