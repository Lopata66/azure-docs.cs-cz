---
title: Rychlý start vytvoření zařízení Azure IoT Edge v Linuxu | Dokumentace Microsoftu
description: V tomto rychlém startu zjistěte, jak vytvořit zařízení IoT Edge a pak nasazení předem připravených kódu vzdáleně z webu Azure portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/02/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 58dc2f5d8862c0b51b36e028d52275b2c8e732be
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537553"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-device"></a>Rychlý start: Nasazení prvního modulu IoT Edge na zařízení s Linuxem

Služba Azure IoT Edge posouvá výkon cloudu k zařízením Internetu věcí. V tomto rychlém startu se naučíte používat cloudové rozhraní ke vzdálenému nasazení předem připraveného kódu do zařízení IoT Edge.

V tomto rychlém startu se naučíte:

1. Vytvořit IoT Hub.
2. Zaregistrovat zařízení IoT Edge do centra IoT Hub.
3. Nainstalovat na zařízení modul runtime Azure IoT Edge a spustit ho.
4. Vzdáleně nasadit modul na zařízení IoT Edge.

![Diagram – rychlý start architektury pro zařízení a cloud](./media/quickstart-linux/install-edge-full.png)

Tento rychlý start vás provede vytvořením virtuální počítač Azure nakonfigurovaný tak, aby se zařízení IoT Edge. Potom můžete modul nasadit z webu Azure Portal do svého zařízení. Modul, který v tomto rychlém kurzu nasadíte, je simulovaný snímač, který generuje údaje o teplotě, vlhkosti a atmosferickém tlaku. Další kurzy o Azure IoT Edge vycházejí z tohoto kurzu. V něm nasadíte moduly, které analyzují simulovaná data kvůli získání obchodních informací.

Pokud nemáte aktivní předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

K dokončení řady kroků v tomto rychlém startu použijete Azure CLI. Azure IoT má rozšíření, které nabízí další funkce.

Přidejte rozšíření Azure IoT do instance služby Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prerequisites"></a>Požadavky

Cloudové prostředky:

* Skupina prostředků pro správu všech prostředků, které v tomto rychlém startu použijete.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Zařízení IoT Edge:

* Virtuální počítač nebo zařízení s Linuxem, který bude fungovat jako zařízení IoT Edge. Měli byste použít poskytované společností Microsoft [Azure IoT Edge na Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) virtuální počítač, který provede vše potřebné pro spuštění na zařízení IoT Edge předinstalaci. Vytvoření tohoto virtuálního počítače pomocí následujících příkazů:

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   Může trvat několik minut pro vytvoření a spuštění nového virtuálního počítače.

   Při vytváření nového virtuálního počítače, poznamenejte si, **publicIpAddress**, který je součástí výstupu příkazu create. Tato veřejná IP adresa bude používat pro připojení k virtuálnímu počítači dále v tomto rychlém startu.

* Pokud chcete spustit modul runtime Azure IoT Edge ve svém vlastním zařízení, postupujte podle pokynů na adrese [nainstalovat modul runtime Azure IoT Edge v Linuxu (x64)](how-to-install-iot-edge-linux.md) nebo [modul runtime nainstalovat Azure IoT Edge v Linuxu (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Spuštění tohoto rychlého startu tak, že vytvoříte službu IoT hub pomocí Azure CLI.

![Diagram – vytvoření centra IoT v cloudu](./media/quickstart-linux/create-iot-hub.png)

Pro tento rychlý start můžete použít bezplatnou úroveň IoT Hubu. Pokud jste službu IoT Hub někdy používali a máte vytvořené bezplatné centrum IoT, můžete ho použít. V každém předplatném může být jenom jeden bezplatný IoT Hub.

Následující kód vytvoří bezplatné centrum **F1** ve skupině prostředků **IoTEdgeResources**. Nahraďte *{hub_name}* jedinečným názvem vašeho centra IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1
   ```

   Pokud dojde k chybě kvůli tomu, že vaše předplatné již jedno bezplatné centrum obsahuje, změňte skladovou položku na **S1**. Pokud dojde k chybě, že název služby IoT Hub není k dispozici, znamená to, že má někdo jiný již centra s tímto názvem. Zkuste použít nový název.

## <a name="register-an-iot-edge-device"></a>Registrace zařízení IoT Edge

Zaregistrujte zařízení IoT Edge do nově vytvořeného centra IoT.

![Diagram – registrace zařízení s identitou služby IoT Hub](./media/quickstart-linux/register-device.png)

Vytvoření identity zařízení pro zařízení IoT Edge, tak, aby mohl komunikovat s centrem IoT. Identita zařízení se uchovává v cloudu a k přidružení fyzického zařízení k identitě zařízení se používá jedinečný připojovací řetězec zařízení.

Protože zařízení IoT Edge se chovají a lze je spravovat jinak než typické zařízení IoT, deklarujte tuto identitu pro zařízení IoT Edge se `--edge-enabled` příznak.

1. Ve službě Azure Cloud Shell zadejte následující příkaz, kterým v centru vytvoříte zařízení **myEdgeDevice**.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   Pokud se zobrazí chyba týkající se iothubowner klíče zásad, ujistěte se, že vaší služby cloud shell běží nejnovější verze rozšíření azure-cli-iot-ext, přípona.

2. Načtěte připojovací řetězec svého zařízení, který propojí vaše fyzické zařízení s jeho identitou ve službě IoT Hub.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Zkopírujte hodnotu `connectionString` klíče z výstupu JSON a uložte ho. Tato hodnota je připojovací řetězec zařízení. Tento připojovací řetězec budete používat ke konfiguraci modulu runtime IoT Edge v další části.

   ![Načtení připojovacího řetězce z výstupu rozhraní příkazového řádku](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Konfigurace zařízení IoT Edge

Spusťte modul runtime Azure IoT Edge na zařízení IoT Edge.

![Diagram - Start modul runtime na zařízení](./media/quickstart-linux/start-runtime.png)

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Skládá se ze tří částí. **Démon zabezpečení IoT Edge** spustí pokaždé, když zařízení IoT Edge se spustí a bootstraps zařízení spuštěním agenta IoT Edge. **Agent IoT Edge** umožňuje nasadit a monitorovat moduly na zařízení IoT Edge, včetně centra služby IoT Edge. Druhým je **IoT Edge Hub**, který na zařízení IoT Edge řídí komunikaci mezi moduly a také mezi zařízením a IoT Hubem.

Během konfigurace modulu runtime zadáte připojovací řetězec zařízení. Použijte řetězec, který jste získali z Azure CLI. Tento řetězec přidruží vaše fyzické zařízení k identitě zařízení IoT Edge v Azure.

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>Nastavit připojovací řetězec na zařízení IoT Edge

Pokud používáte Azure IoT Edge na virtuální počítač s Ubuntu, jak je popsáno v rámci požadavků, vaše zařízení už má modul runtime IoT Edge, které jsou nainstalované. Potřebujete nakonfigurovat své zařízení s připojovacím řetězcem zařízení, který jste získali v předchozí části. Můžete to provést vzdáleně bez nutnosti připojení k virtuálnímu počítači. Spusťte následující příkaz a nahraďte **{device_connection_string}** s vlastním řetězcem.

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Pokud používáte na místním počítači nebo ARM32 zařízení IoT Edge, musíte nainstalovat modul runtime IoT Edge a nezbytný software ve vašem zařízení. Postupujte podle pokynů v [nainstalovat modul runtime Azure IoT Edge v Linuxu (x64)](how-to-install-iot-edge-linux.md) nebo [modul runtime nainstalovat Azure IoT Edge v Linuxu (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md), pak se vrátit na tomto rychlém startu.

### <a name="view-the-iot-edge-runtime-status"></a>Zobrazení stavu modulu runtime IoT Edge

Zbývající příkazy v tomto rychlém startu proběhla na zařízení IoT Edge, tak, abyste viděli, co se děje v zařízení. Pokud používáte virtuální počítač, připojte se k tomuto počítači pomocí veřejné adresy IP, který byl výstupem příkazu pro vytvoření. Můžete také najít veřejnou IP adresu na stránce Přehled virtuálního počítače na webu Azure Portal. Použijte následující příkaz pro připojení k virtuálnímu počítači. Nahraďte **{azureuser}** Pokud použijete jiné uživatelské jméno, než je navrženo v rámci požadavků. Nahraďte **{publicIpAddress}** adresou vašeho počítače.

   ```azurecli-interactive
   ssh azureuser@{publicIpAddress}
   ```

Ověřte, že modul runtime byl úspěšně nainstalován a nakonfigurován na vašem zařízení IoT Edge.

>[!TIP]
>Ke spouštění příkazů `iotedge` potřebujete zvýšená oprávnění. Vaše oprávnění se automaticky aktualizují, jakmile se po instalaci modulu runtime IoT Edge odhlásíte z počítače a poprvé se k němu opět přihlásíte. Do té doby před příkazy používejte **sudo**.

1. Zkontrolujte, že démon zabezpečení IoT Edge běží jako systémová služba.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Proces démon IoT Edge spuštění jako služby systému naleznete v tématu](./media/quickstart-linux/iotedged-running.png)

2. Pokud potřebujete řešit potíže se službou, načtěte protokoly služby.

   ```bash
   journalctl -u iotedge
   ```

3. Zobrazte moduly spuštěné na vašem zařízení.

   ```bash
   sudo iotedge list
   ```

   ![Zobrazení jednoho modulu na zařízení](./media/quickstart-linux/iotedge-list-1.png)

Vaše zařízení IoT Edge je teď nakonfigurované. Je připravené na spouštění modulů nasazených v cloudu.

## <a name="deploy-a-module"></a>Nasazení modulu

Pokud budete zařízení Azure IoT Edge spravovat v cloudu, můžete nasadit modul, který bude odesílat telemetrická data do služby IoT Hub.
![Diagram – nasazení modulu z cloudu do zařízení](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

V tomto rychlém startu jste vytvořili nové zařízení IoT Edge a nainstalovali jste na něj modul runtime IoT Edge. Pak jste použili na webu Azure portal k nasazení modulu IoT Edge pro spuštění na zařízení bez nutnosti provádět změny samotné zařízení.

V takovém případě vytvoří modul, který jste odeslali ukázková data, která můžete použít pro testování. Modul simulované teplotní snímač generuje dat prostředí, který můžete použít pro testování později. Monitoruje simulovaných senzorů na počítači a prostředí kolem počítače. Senzor se například může být v místnosti serverů, na výrobním závodě nebo větrné turbíny. Zpráva obsahuje okolní teploty a vlhkosti, počítač teploty a tlaku a časové razítko. V kurzech IoT Edge pomocí dat vytvořil tento modul, protože testovací data pro analýzu.

Znovu otevřete příkazový řádek na vašem zařízení IoT Edge, ani používat připojení SSH z příkazového řádku Azure. Zkontrolujte, že modul, který jste nasadili z cloudu, běží na zařízení IoT Edge:

   ```bash
   sudo iotedge list
   ```

   ![Zobrazení tří modulů na zařízení](./media/quickstart-linux/iotedge-list-2.png)

Zobrazení zpráv odesílány z modulu senzoru teploty:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >Při odkazu na modul názvy jsou malá a velká písmena příkazy IoT Edge.

   ![Zobrazení dat z modulu](./media/quickstart-linux/iotedge-logs.png)

Můžete rovněž sledovat zprávy dorazí ve službě IoT hub pomocí [rozšíření Azure IoT Hub Toolkit pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (dříve rozšíření Azure IoT Toolkit).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat dalšími kurzy o IoT Edge, použijte zařízení, které jste zaregistrovali a nastavili v tomto rychlém startu. V opačném případě můžete odstranit prostředky Azure, které jste vytvořili nemuseli platit případné poplatky.

Pokud jste virtuální počítač a centrum IoT vytvořili v nové skupině prostředků, můžete odstranit tuto skupinu a všechny související prostředky. Dvojitá kontrola obsah skupiny prostředků pro Ujistěte se, že existuje vaší nic, které chcete zachovat. Pokud nechcete odstranit celou skupinu, můžete místo toho odstranit jednotlivé prostředky.

Odeberte skupinu **IoTEdgeResources**.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>Další postup


V tomto rychlém startu jste vytvořili zařízení IoT Edge a použít rozhraní Azure IoT Edge cloud k nasazení kódu do zařízení. Teď máte testovací zařízení, které generuje nezpracovaná data o prostředí.

Dalším krokem je nastavení svoje místní vývojové prostředí, takže můžete začít vytvářet moduly, které spouští vaši obchodní logiku IoT Edge. 

> [!div class="nextstepaction"]
> [Začít s vývojem modulů IoT Edge pro zařízení s Linuxem](tutorial-develop-for-linux.md)
