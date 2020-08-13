---
title: Připojit hybridní počítač k serverům s podporou ARC Azure
description: Naučte se připojit a zaregistrovat svůj hybridní počítač pomocí ARC Azure pro servery.
ms.topic: quickstart
ms.date: 08/12/2020
ms.openlocfilehash: 2feb9da3600ef3664b0a75bf2d985d698572ff6f
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2020
ms.locfileid: "88173122"
---
# <a name="quickstart-connect-hybrid-machine-with-azure-arc-enabled-servers"></a>Rychlý Start: připojení hybridního počítače se servery s podporou ARC Azure

[Azure ARC pro servery](../overview.md) (Preview) umožňuje spravovat a řídit počítače s Windows a Linux hostovanými v místních, hraničních a prostředích s více cloudy. V tomto rychlém startu nasadíte a nakonfigurujete agenta připojeného počítače na počítači s Windows nebo Linux hostovaným mimo Azure za účelem správy pomocí ARC pro servery (Preview).

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Nasazení agenta pro hybridního počítače s připojením (ARC pro servery) vyžaduje, abyste na počítači měli oprávnění správce pro instalaci a konfiguraci agenta. V systému Linux, pomocí kořenového účtu a ve Windows, s účtem, který je členem místní skupiny Administrators.

* Než začnete, nezapomeňte si projít [požadavky](../agent-overview.md#prerequisites) agenta a ověřit následující:

    * Na cílovém počítači běží podporovaný [operační systém](../agent-overview.md#supported-operating-systems).

    * Vašemu účtu je přidělené přiřazení k [požadovaným rolím Azure](../agent-overview.md#required-permissions).

    * Pokud se počítač připojuje prostřednictvím brány firewall nebo proxy server komunikovat přes Internet, ujistěte se, že [uvedené](../agent-overview.md#networking-configuration) adresy URL nejsou blokované.

    * Azure ARC pro servery (Preview) podporuje jenom oblasti, které jsou [tady](../overview.md#supported-regions)uvedené.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Registrovat poskytovatele prostředků Azure

Azure ARC pro servery (verze Preview) závisí na následujících poskytovatelích prostředků Azure ve vašem předplatném, aby bylo možné tuto službu používat:

* Microsoft. HybridCompute
* Microsoft. GuestConfiguration

Zaregistrujte je pomocí následujících příkazů:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>Generovat instalační skript

Skript pro automatizaci stahování, instalace a navázání připojení ke službě Azure ARC je k dispozici z Azure Portal. Chcete-li dokončit proces, postupujte následovně:

1. Spusťte službu Azure ARC v Azure Portal kliknutím na **všechny služby**a pak vyhledáte a vyberete **počítače – Azure ARC**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Vyhledat oblouk pro servery ve všech službách" border="false":::

1. Na stránce **počítače – ARC Azure** vyberte buď **Přidat**, v levém horním rohu nebo možnost **vytvořit počítač – Azure ARC** v dolní části prostředního podokna.

1. Na stránce **Vyberte metodu** vyberte dlaždici **přidat počítače pomocí interaktivního skriptu** a pak vyberte **vygenerovat skript**.

1. Na stránce **vygenerovat skript** vyberte předplatné a skupinu prostředků, ve které chcete, aby se počítač spravoval v rámci Azure. Vyberte umístění Azure, kam se budou ukládat metadata počítače.

1. Na stránce **vygenerovat skript** v rozevíracím seznamu **operační systém** vyberte operační systém, ve kterém bude skript spuštěn.

1. Pokud počítač komunikuje prostřednictvím proxy server pro připojení k Internetu, vyberte **Další: proxy server**.

1. Na kartě **proxy server** zadejte proxy server IP adresu nebo název a číslo portu, které bude počítač používat ke komunikaci s proxy server. Zadejte hodnotu ve formátu `http://<proxyURL>:<proxyport>` .

1. Vyberte **zkontrolovat a generovat**.

1. Na kartě **Revize + generovat** zkontrolujte souhrnné informace a pak vyberte **Stáhnout**. Pokud stále potřebujete dělat změny, vyberte **Předchozí**.

## <a name="install-the-agent-using-the-script"></a>Instalace agenta pomocí skriptu

### <a name="windows-agent"></a>Agent Windows

1. Přihlaste se k serveru.

1. Otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními 64.

1. Přejděte do složky nebo sdílené složky, do které jste zkopírovali skript, a spusťte ho na serveru spuštěním `./OnboardingScript.ps1` skriptu.

### <a name="linux-agent"></a>Agent pro Linux

1. Pokud chcete nainstalovat agenta pro Linux do cílového počítače, který může přímo komunikovat s Azure, spusťte následující příkaz:

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * Pokud cílový počítač komunikuje prostřednictvím proxy server, spusťte následující příkaz:

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>Ověření připojení k Azure Arcu

Po nainstalování agenta a jeho konfiguraci pro připojení k Azure ARC pro servery (Preview), navštivte Azure Portal a ověřte, že se server úspěšně připojil. Zobrazte počítač v [Azure Portal](https://aka.ms/hybridmachineportal).

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Úspěšné připojení k počítači" border="false":::

## <a name="next-steps"></a>Další kroky

Teď, když jste povolili hybridní počítač se systémem Linux nebo Windows a úspěšně jste se připojili ke službě, jste připraveni povolit Azure Policy pro pochopení dodržování předpisů v Azure.

Další informace o tom, jak identifikovat počítač s povolenou službou Azure ARC pro servery (ve verzi Preview), na kterém není nainstalovaný agent Log Analytics, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy](tutorial-assign-policy-portal.md)
