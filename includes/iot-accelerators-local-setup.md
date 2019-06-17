---
title: zahrnout soubor
description: zahrnout soubor
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66111711"
---
## <a name="download-the-source-code"></a>Stáhněte si zdrojový kód

Úložišť zdrojového kódu vzdáleného monitorování zahrnují zdrojový kód a soubory konfigurace Dockeru, které potřebujete pro spuštění imagí Dockeru mikroslužeb.

Klonovat a vytvořte místní verzi úložiště, použijte prostředí příkazového řádku přejděte do složky na místním počítači vhodný. Poté spusťte následující sady příkazů buď naklonujte úložiště .NET:

Pokud chcete stáhnout nejnovější verzi implementace mikroslužby .NET, spusťte:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Tyto příkazy stáhnout zdrojový kód pro všechny mikroslužby kromě skripty, které používáte ke spouštění mikroslužby lokálně. I když není nutné zdrojový kód pro spuštění mikroslužby v Dockeru, zdrojový kód je užitečné, pokud budete později chtít upravit akcelerátor řešení a místní test provedených změn.

## <a name="deploy-the-azure-services"></a>Nasazení služby Azure

I když v tomto článku se dozvíte, jak spouštět mikroslužby lokálně, jsou závislé na spouštění v cloudu služby Azure. Pomocí následujícího skriptu pro nasazení služby Azure. Následující příklady skriptu se předpokládá, že používáte úložiště .NET na počítači s Windows. Pokud pracujete s jiným prostředím, cesty, přípony souboru a oddělovače cest odpovídajícím způsobem nastavte.

### <a name="create-new-azure-resources"></a>Vytvářet nové prostředky Azure

Pokud jste dosud vytvořili požadované prostředky Azure, postupujte podle těchto kroků:

1. Ve vašem prostředí příkazového řádku, přejděte **\services\scripts\local\launch** složky v klonovaném kopii úložiště.

1. Spusťte následující příkazy pro instalaci **počítače** rozhraní příkazového řádku nástroje a přihlaste se ke svému účtu Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Spustit **start.cmd** skriptu. Skript vyzve k zadání následujících informací:
   * Název řešení.
   * Předplatné Azure, které se má použít.
   * Umístění datového centra Azure používat.

     Tento skript vytvoří skupinu prostředků v Azure s názvem řešení. Tato skupina prostředků obsahuje prostředky Azure, které používá akcelerátor řešení. Jakmile už nepotřebujete odpovídající prostředky můžete odstranit tuto skupinu prostředků.

     Skript také přidá sadu proměnných prostředí s předponou **počítače** do místního počítače. Tyto proměnné prostředí zadejte podrobnosti pro vzdálené monitorování bude moct číst z prostředku Azure Key Vault. Tento prostředek Key Vault je vzdálené monitorování bude načteno jeho konfigurační hodnoty z.

     > [!TIP]
     > Po dokončení skriptu, také uloží proměnné prostředí do souboru s názvem  **\<domovskou složku\>\\.pcs\\\<název řešení\>.env** . Můžete je použít pro nasazení akcelerátoru řešení budoucí. Všimněte si, že všechny proměnné prostředí nastavené v místním počítači přepsat hodnoty v **služby\\skripty\\místní\\.env** souboru při spuštění **docker-compose**.

1. Výstup z prostředí příkazového řádku.

### <a name="use-existing-azure-resources"></a>Použít existující prostředky Azure

Pokud jste již vytvořili požadované prostředky Azure, vytvořte odpovídající proměnné prostředí v místním počítači.
Nastavení proměnných prostředí pro následující:
* **PCS_KEYVAULT_NAME** – název prostředku Azure Key Vault
* **PCS_AAD_APPID** – ID aplikace AAD
* **PCS_AAD_APPSECRET** -tajný klíč aplikace AAD

Hodnoty konfigurace budou číst z tohoto prostředku Azure Key Vault. Tyto proměnné prostředí mohou být uloženy v  **\<domovskou složku\>\\.pcs\\\<název řešení\>.env** souboru z nasazení. Všimněte si, že proměnné prostředí nastavené v místním počítači přepsat hodnoty v **služby\\skripty\\místní\\.env** souboru při spuštění **docker-compose**.

Některé konfigurace vyžaduje mikroslužby je uložené v instanci **služby Key Vault** , která byla vytvořena na původním nasazení. Odpovídající proměnné v trezoru klíčů by měl být upraven podle potřeby.