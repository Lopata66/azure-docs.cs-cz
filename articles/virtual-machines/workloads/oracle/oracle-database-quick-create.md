---
title: Vytvoření databáze Oracle ve virtuálním počítači Azure | Microsoft Docs
description: Rychle získáte Oracle Database databázi 12c v prostředí Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: BorisB2015
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: borisb
ms.openlocfilehash: 77a374a83c178639052e8db6fc85c31e366ac0e6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683641"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Vytvoření Oracle Database na virtuálním počítači Azure

Tato příručka podrobně popisuje použití rozhraní příkazového řádku Azure k nasazení virtuálního počítače Azure z [Image Galerie Oracle Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) , aby bylo možné vytvořit databázi Oracle 12c. Po nasazení serveru se připojíte přes SSH, aby se nakonfigurovala databáze Oracle. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Pokud chcete vytvořit virtuální počítač, použijte příkaz [AZ VM Create](/cli/azure/vm) . 

Následující příklad vytvoří virtuální počítač `myVM`. Také vytvoří klíče SSH, pokud ještě neexistují ve výchozím umístění klíče. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Po vytvoření virtuálního počítače se v Azure CLI zobrazí podobné informace jako v následujícím příkladu. Všimněte si hodnoty pro `publicIpAddress`. Tuto adresu použijete pro přístup k virtuálnímu počítači.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

Pokud chcete vytvořit relaci SSH s virtuálním počítačem, použijte následující příkaz. Nahraďte IP adresu `publicIpAddress` hodnotou svého virtuálního počítače.

```bash
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>Vytvoření databáze

V imagi na webu Marketplace už je nainstalovaný software Oracle. Vytvořte ukázkovou databázi následujícím způsobem. 

1.  Přepněte na uživatele *Oracle* a pak inicializujte naslouchací proces pro protokolování:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    Výstup je podobný tomuto:

    ```output
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  Vytvořte databázi:

    ```bash
    dbca -silent \
           -createDatabase \
           -templateName General_Purpose.dbc \
           -gdbname cdb1 \
           -sid cdb1 \
           -responseFile NO_VALUE \
           -characterSet AL32UTF8 \
           -sysPassword OraPasswd1 \
           -systemPassword OraPasswd1 \
           -createAsContainerDatabase true \
           -numberOfPDBs 1 \
           -pdbName pdb1 \
           -pdbAdminPassword OraPasswd1 \
           -databaseType MULTIPURPOSE \
           -automaticMemoryManagement false \
           -storageType FS \
           -ignorePreReqs
    ```

    Vytvoření databáze trvá několik minut.

3. Nastavit proměnné Oracle

Než se připojíte, musíte nastavit dvě proměnné prostředí: *ORACLE_HOME* a *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```

Do souboru. bashrc můžete také přidat proměnné ORACLE_HOME a ORACLE_SID. Tím by se uložily proměnné prostředí pro budoucí přihlášení. Potvrďte, že se do `~/.bashrc` souboru přidaly následující příkazy pomocí editoru podle vašeho výběru.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Připojení Oracle EM Express

Pro nástroj pro správu grafického uživatelského rozhraní, který můžete použít k prozkoumání databáze, nastavte Oracle EM Express. Pokud se chcete připojit k Oracle EM Express, musíte nejdřív nastavit port v Oracle. 

1. Připojte se k databázi pomocí sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. Po připojení nastavte port 5502 pro EM Express.

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Otevřete kontejner PDB1, pokud ještě není otevřený, ale nejdřív se podívejte na stav:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    Výstup je podobný tomuto:

    ```output
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Pokud OPEN_MODE pro `PDB1` není pro zápis, spusťte následující příkazy a otevřete PDB1:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Musíte zadat `quit` , aby se ukončila relace sqlplus a typ `exit` odhlášení uživatele Oracle.

## <a name="automate-database-startup-and-shutdown"></a>Automatizace spuštění a vypnutí databáze

Databáze Oracle se ve výchozím nastavení automaticky nespustí po restartování virtuálního počítače. Chcete-li nastavit automatické spuštění databáze Oracle, nejprve se přihlaste jako kořenový adresář. Pak vytvořte a aktualizujte některé systémové soubory.

1. Přihlásit se jako kořen

    ```bash
    sudo su -
    ```

2.  Pomocí oblíbeného editoru upravte soubor `/etc/oratab` a změňte výchozí nastavení `N` na: `Y`

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Vytvořte soubor s názvem `/etc/init.d/dbora` a vložte následující obsah:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Změňte oprávnění k souborům pomocí *chmod* následujícím způsobem:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Vytvořte symbolické odkazy pro spuštění a vypnutí následujícím způsobem:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Chcete-li otestovat změny, restartujte virtuální počítač:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Otevření portů pro připojení

Poslední úlohou je konfigurace některých externích koncových bodů. Pokud chcete nastavit skupinu zabezpečení sítě Azure, která chrání virtuální počítač, nejdřív na VIRTUÁLNÍm počítači ukončete relaci SSH (při restartování v předchozím kroku by se měla vykázat z protokolu SSH). 

1.  Chcete-li otevřít koncový bod, který používáte pro vzdálený přístup k databázi Oracle, vytvořte pravidlo skupiny zabezpečení sítě pomocí příkazu [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule) následujícím způsobem: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Pokud chcete otevřít koncový bod, který používáte pro přístup k Oracle EM Express vzdáleně, vytvořte pravidlo skupiny zabezpečení sítě pomocí příkazu [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule) následujícím způsobem:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. V případě potřeby Získejte veřejnou IP adresu virtuálního počítače znovu pomocí příkazu [AZ Network Public-IP show](/cli/azure/network/public-ip) takto:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Připojte EM Express z prohlížeče. Ujistěte se, že je váš prohlížeč kompatibilní s EM Express (vyžaduje se instalace Flash): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

Můžete se přihlásit pomocí účtu **sys** a zaškrtnout políčko **jako SYSDBA** . Použijte **OraPasswd1** hesla, které jste nastavili během instalace. 

![Snímek obrazovky se stránkou pro přihlášení Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile dokončíte zkoumání první databáze Oracle v Azure a virtuální počítač už není potřeba, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [AZ Group Delete](/cli/azure/group) .

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

Seznamte se [s dalšími řešeními Oracle v Azure](oracle-considerations.md). 

Vyzkoušejte kurz [instalace a konfigurace Oracle Automated Storage Management](configure-oracle-asm.md) .
