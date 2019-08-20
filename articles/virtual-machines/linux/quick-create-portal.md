---
title: Rychlý start – Vytvoření virtuálního počítače s Linuxem na webu Azure Portal | Microsoft Docs
description: V tomto rychlém startu zjistíte, jak pomocí webu Azure Portal vytvořit virtuální počítač s Linuxem
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 8/16/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 189d6d0030264590986d6fe2af47d35705cfb08b
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575804"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Rychlý start: Vytvoření virtuálního počítače se systémem Linux v Azure Portal

Virtuální počítače Azure je možné vytvářet na webu Azure Portal. Azure Portal je uživatelské rozhraní v prohlížeči, pomocí kterého můžete vytvářet virtuální počítače a související prostředky. V tomto rychlém startu se dozvíte, jak pomocí webu Azure Portal nasadit virtuální počítač s Linuxem Ubuntu 16.04 LTS. Také se k virtuálnímu počítači připojíte přes SSH a nainstalujete na něj webový server NGINX, abyste virtuální počítač viděli v akci.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-ssh-key-pair"></a>Vytvoření páru klíčů SSH

K dokončení tohoto rychlého startu potřebujete pár klíčů SSH. Pokud už máte pár klíčů SSH, můžete tento krok přeskočit.

Otevřete prostředí Bash a pomocí nástroje [ssh-keygen](https://www.ssh.com/ssh/keygen/) vytvořte pár klíčů SSH. Příklad příkazu, který je třeba provést, je uveden níže. Pokud na místním počítači nemáte prostředí Bash, můžete použít [Azure Cloud Shell](https://shell.azure.com/bash).

```bash
ssh-keygen -t rsa -b 2048
```

Zobrazí se výzva k zadání souboru, do kterého chcete uložit dvojici klíčů. Můžete buď zadat konkrétní umístění souboru, nebo jen "Enter", aby se uložily ve výchozím umístění uvedeném v závorkách. Pak se zobrazí výzva k zadání hesla. Můžete zadat přístupové heslo pro svůj klíč SSH. Pokud chcete pokračovat bez hesla, můžete stisknout Enter.

```bash
[root@linuxvm ~]$ ssh-keygen -t rsa -b 2048
Enter the file in which to save the key (home/root/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your public key has been saved in /home/root/.ssh/id_rsa.pub.
The key fingerprint is: SHA256:kkQS13gbaxevy4ULH0mW6wLIkcFm0twx/rIlSo1fIqU
The key's randomart image is:
+---[RSA 2018]----+
|   +oo=+         |
|  . B=o.+ .      |
|   + o+. + +     |
|    o* o+ = .    |
|   .EoB.S+ =     |
|   .o+.O. * .    |
|    . o. = =     |
|        . *      |
|         .       |
+----[SHA256]-----+
```
Příkaz vygeneruje veřejné a privátní klíče s výchozím `id_rsa` názvem v `~/.ssh directory`. `ssh-keygen` Příkaz vrátí úplnou cestu k veřejnému klíči. Použijte cestu k veřejnému klíči v příkazu `cat` a zobrazte obsah tohoto klíče.

```bash
cat ~/.ssh/id_rsa.pub
```

>[!NOTE]
> Pokud se rozhodnete uložit klíč SSH v jiném umístění než ve výchozím nastavení, budete ho muset použít při spuštění`cat`

Výstup tohoto příkazu si uložte. Toto je váš veřejný klíč a budete ho potřebovat při konfiguraci účtu správce pro přihlášení k vašemu VIRTUÁLNÍmu počítači.

Další informace o příkazu ssh-keygen najdete na [stránce muž](https://linux.die.net/man/1/ssh-keygen).

Pokud používáte počítač s Windows a chcete další informace o tom, jak vytvářet páry klíčů SSH, včetně použití výstupu, přečtěte si téma [použití klíčů ssh v systému Windows](ssh-from-windows.md).

Pokud pár klíčů SSH vytvoříte pomocí služby Cloud Shell, uloží se do sdílené složky Azure, kterou služba [Cloud Shell automaticky připojí](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Tuto sdílenou složku ani účet úložiště neodstraňujte,dokud nenačtete své klíče, jinak ztratíte přístup k virtuálnímu počítači.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.

1. Do vyhledávacího pole nad seznamem prostředků Azure Marketplace vyhledejte **Ubuntu Server 18,04** a vyberte nabídku Ubuntu 18,04 LTS a pak zvolte **vytvořit**.

1. Přesvědčte se, že je na kartě **Základy** v části **Podrobnosti o projektu** vybrané správné předplatné a potom v části **Skupina prostředků** vyberte **Vytvořit nový**. V automaticky otevíraném okně zadejte jako název skupiny prostředků *myResourceGroup* a potom zvolte **OK**.

    ![Vytvoření nové skupiny prostředků pro virtuální počítač](./media/quick-create-portal/project-details.png)

1. V části **Podrobnosti o instancích** jako **Název virtuálního počítače** zadejte *myVM* a u možnosti **Oblast** zvolte *USA – východ*. Zbytek ponechte ve výchozím nastavení.

    ![Část podrobností o instancích](./media/quick-create-portal/instance-details.png)

1. V části **účet správce**vyberte **veřejný klíč SSH**, zadejte své uživatelské jméno a potom do textového pole vložte svůj veřejný klíč, který jste předtím uložili. Ve veřejném klíči odeberte počáteční a koncové prázdné znaky.

    ![Účet správce](./media/quick-create-portal/administrator-account.png)

1. V části **Pravidla portů pro příchozí spojení** > **Veřejné příchozí porty** zvolte **Povolit vybrané porty** a potom z rozevíracího seznamu vyberte **SSH (22)** a **HTTP (80)** .

    ![Otevřené porty pro protokoly RDP a HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Zbytek ponechte ve výchozím nastavení a pak v dolní části stránky vyberte tlačítko **Zkontrolovat a vytvořit**.

1. Na stránce **Vytvoření virtuálního počítače** se zobrazí podrobnosti o virtuálním počítači, který se chystáte vytvořit. Až budete připraveni, vyberte **Vytvořit**.

Nasazení virtuálního počítače bude několik minut trvat. Po dokončení nasazení se přesuňte k další části.


## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači

Vytvořte připojení SSH k virtuálnímu počítači.

1. Na stránce Přehled pro váš virtuální počítač vyberte tlačítko **Připojit**.

    ![Portál 9](./media/quick-create-portal/portal-quick-start-9.png)

2. Na stránce **Připojení k virtuálnímu počítači** ponechte výchozí výběr možností pro připojení podle IP adresy přes port 22. V části **Přihlásit pomocí místního účtu virtuálního počítače** se zobrazí příkaz pro připojení. Kliknutím na tlačítko příkaz zkopírujte. Následující příklad ukazuje, jak vypadá příkaz pro připojení přes SSH:

    ```bash
    ssh azureuser@10.111.12.123
    ```

3. Pomocí stejného prostředí bash, které jste použili k vytvoření páru klíčů SSH (jako je [Azure Cloud Shell](https://shell.azure.com/bash) nebo místní prostředí bash), vložte do prostředí příkaz pro připojení SSH a vytvořte relaci SSH.

## <a name="install-web-server"></a>Instalace webového serveru

Pokud se chcete podívat na virtuální počítač v akci, nainstalujte webový server NGINX. V relaci SSH aktualizujte zdroje balíčku a pak nainstalujte nejnovější balíček NGINX.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Až budete hotovi, zadáním příkazu `exit` ukončete relaci SSH.


## <a name="view-the-web-server-in-action"></a>Zobrazení webového serveru v akci

V libovolném webovém prohlížeči zobrazte výchozí úvodní stránku serveru NGINX. Jako webovou adresu zadejte veřejnou IP adresu virtuálního počítače. Veřejnou IP adresu najdete na stránce přehledu virtuálního počítače nebo v připojovacím řetězci SSH, který jste použili dříve.

![Výchozí web NGINX](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, virtuální počítač a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků pro příslušný virtuální počítač, vyberete **Odstranit** a pak potvrdíte název skupiny prostředků, kterou chcete odstranit.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jednoduchý virtuální počítač, vytvořili jste skupinu zabezpečení sítě a pravidlo a nainstalovali jste základní webový server. Další informace o virtuálních počítačích Azure najdete v kurzu pro virtuální počítače s Linuxem.

> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Linuxem](./tutorial-manage-vm.md)
