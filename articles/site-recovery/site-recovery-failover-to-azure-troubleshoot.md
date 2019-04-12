---
title: Řešení potíží s převzetí služeb při selhání do Azure selhání | Dokumentace Microsoftu
description: Tento článek popisuje, jak řešit běžné chyby v přebírání služeb při selhání do Azure
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/04/2019
ms.author: mayg
ms.openlocfilehash: 2156ee6cf27ecfa32b19ad5bbef7549e99c3f7ef
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492851"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Řešení chyb při selhání virtuálního počítače VMware nebo fyzických počítačů do Azure

Může zobrazit jedna z následující chyby při provádění převzetí služeb při selhání virtuálního počítače do Azure. Řešení potíží s, popsané kroky použijte pro všechny chybové stavy.

## <a name="failover-failed-with-error-id-28031"></a>Převzetí služeb při selhání s ID chyby 28031

Site Recovery se nepodařilo vytvořit nezdařené přes virtuální počítač v Azure. K tomu mohlo dojít vlivem jednoho z následujících důvodů:

* Není k dispozici dostatečnou kvótu pro vytvoření virtuálního počítače k dispozici: Dostupnou kvótu můžete zkontrolovat tak, že přejdete k předplatnému -> využití a kvóty. Můžete otevřít [novou žádost o podporu](https://aka.ms/getazuresupport) o navýšení kvóty.

* Pokoušíte se převzetí služeb při selhání virtuálních počítačů z různých velikostní řady ve stejné sadě dostupnosti. Ujistěte se, že zvolíte stejnou řadu velikostí pro všechny virtuální počítače ve stejné sadě dostupnosti. Změnit velikost tak, že přejdete do nastavení výpočty a síť virtuálního počítače a pak zkuste převzetí služeb při selhání.

* Na předplatné, které brání vytvoření virtuálního počítače není zásadu. Změna zásad pro povolení vytvoření virtuálního počítače a potom opakujte převzetí služeb při selhání.

## <a name="failover-failed-with-error-id-28092"></a>Převzetí služeb při selhání s ID chyby 28092

Site Recovery se nepodařilo vytvořit síťové rozhraní, pro které bylo provedeno přes virtuální počítač. Ujistěte se, že máte dostatečnou kvótu pro vytvoření síťových rozhraní v rámci předplatného k dispozici. Dostupnou kvótu můžete zkontrolovat tak, že přejdete k předplatnému -> využití a kvóty. Můžete otevřít [novou žádost o podporu](https://aka.ms/getazuresupport) o navýšení kvóty. Pokud máte dostatečnou kvótu, pak může se jednat přerušovaný vydávání, zkuste operaci zopakovat. Pokud se problém nevyřeší ani po opakovaných pokusů, pak napište komentář na konci tohoto dokumentu.  

## <a name="failover-failed-with-error-id-70038"></a>Převzetí služeb při selhání s ID chyby 70038

Site Recovery se nepodařilo vytvořit nezdařené přes klasický virtuální počítač v Azure. K tomu mohlo dojít, protože:

* Jeden z prostředků, jako je například virtuální síť, která se vyžaduje pro virtuální počítač, který se má vytvořit neexistuje. Vytvoření virtuální sítě, jak je uvedeno v části Nastavení výpočty a síť virtuálního počítače nebo upravit nastavení pro virtuální síť, která již existuje a zkuste převzetí služeb při selhání.

## <a name="failover-failed-with-error-id-170010"></a>Převzetí služeb při selhání s 170010 ID chyby

Site Recovery se nepodařilo vytvořit nezdařené přes virtuální počítač v Azure. K tomu mohlo dojít, protože se nepovedlo interní aktivitu dosazení dat pro místní virtuální počítač.

Zobrazíte všechny počítače v Azure, prostředí Azure vyžaduje některé z ovladačů ve spouštěcí start stavu a služeb, jako je DHCP ve stavu automatické spuštění. Proto dosazení dat do aktivity, v okamžiku převzetí služeb při selhání, převede typ spouštění **atapi, intelide, storflt, vmbus a storvsc ovladače** do začátku spuštění. Také převádí typ spuštění několik služeb, jako je DHCP na automatické spuštění. Tato aktivita může selhat z důvodu konkrétní chyby prostředí. 

Ruční změna typu spouštění ovladače pro **hostovaného operačního systému Windows**, postupujte následujících kroků:

1. [Stáhněte si](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) ne dosazování skript a spustit ho jako následující. Tento skript kontroluje, pokud virtuální počítač dosazování.

    `.\Script-no-hydration.ps1`

    Pokud je potřeba dosazení dat poskytuje následující výsledek:

        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

        This system doesn't meet no-hydration requirement.

    V případě, že virtuální počítač splňuje požadavek na dosazení Ne, tento skript vám poskytne výsledek "Tento systém splňuje požadavek na dosazení ne". V takovém případě všechny ovladače a služby jsou ve stavu podle potřeby Azure a dosazení dat na virtuálním počítači se nevyžaduje.

2. Spusťte skript č. dosazení dat do sady následujícím způsobem, pokud virtuální počítač nesplňuje požadavek na dosazení č.

    `.\Script-no-hydration.ps1 -set`
    
    Tím se převede typ spouštění ovladačů a poskytne výsledek podobná níže uvedenému příkladu:
    
        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0 

        Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0 

        This system is now no-hydration compatible. 

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Nelze se připojit nebo RDP/SSH k se přes virtuální počítač z důvodu nejde aktivovat tlačítko Připojit na virtuálním počítači

Pokud **připojit** tlačítko na převzetí virtuálního počítače v Azure je zobrazena šedě a nejste připojení k Azure přes Expressroute nebo VPN typu Site-to-Site připojení, potom

1. Přejděte na **virtuálního počítače** > **sítě**, klikněte na název požadované síťové rozhraní.  ![network-interface](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Přejděte do **konfigurací protokolu Ip**, potom klikněte na název pole požadované konfigurace protokolu IP. ![Konfigurací IP adresy](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Pokud chcete povolit veřejné IP adresy, klikněte na **povolit**. ![Povolit IP adresu](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Klikněte na **konfigurovat požadované nastavení** > **vytvořit nový**. ![Vytvořit nový](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Zadejte název veřejné adresy, vyberte výchozí možnosti pro **SKU** a **přiřazení**, pak klikněte na tlačítko **OK**.
6. Nyní uložit změny, klikněte na tlačítko **Uložit**.
7. Zavřete panely a přejděte do **přehled** části virtuální počítač pro připojení nebo protokol RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Nelze se připojit nebo RDP/SSH - připojení virtuálního počítače k dispozici tlačítko

Pokud **připojit** tlačítko na převzetí virtuálního počítače v Azure je k dispozici (není zobrazena šedě), a pak ověřte **Diagnostika spouštění** na virtuálním počítači a zkontrolujte chyby, jak je uvedeno v [v tomto článku](../virtual-machines/windows/boot-diagnostics.md).

1. Pokud se virtuální počítač nespustil, zkuste převzetí služeb při selhání do staršího bodu obnovení.
2. Pokud aplikace ve virtuálním počítači není zapnutý, zkuste převzetí služeb při selhání do bodu obnovení s konzistentní aplikací.
3. Pokud je virtuální počítač k doméně, pak zajistěte, aby že řadiče domény funguje správně. To lze provést pomocí následujících níže uvedené kroky:

    a. Vytvořte nový virtuální počítač ve stejné síti.

    b.  Ujistěte se, že se může připojit ke stejné doméně, ve kterém se přes virtuální počítač se očekává.

    c. Pokud je řadič domény **není** funguje správně, zkuste protokolování, u kterého proběhlo přes virtuální počítač pomocí místního účtu správce.
4. Pokud používáte vlastní server DNS, ujistěte se, že je dostupný. To lze provést pomocí následujících níže uvedené kroky:

    a. Vytvoření nového virtuálního počítače ve stejné síti a

    b. Zkontrolujte, jestli je virtuální počítač moct dokáže překládat pomocí vlastního serveru DNS.

>[!Note]
>Povolení kteréhokoli jiného nastavení než Diagnostika spouštění by vyžadovalo Azure VM Agent nainstalovaný na virtuálním počítači před převzetí služeb

## <a name="unexpected-shutdown-message-event-id-6008"></a>Neočekávané vypnutí zprávy (6008 ID události)

Při spouštění virtuálním počítači Windows příspěvek převzetí služeb při selhání, pokud se zobrazí zpráva neočekávané vypnutí na obnoveného virtuálního počítače, znamená to, že stav vypnutí virtuálního počítače nebyly zaznamenány v bodu obnovení pro převzetí služeb při selhání. To se stane, když obnovíte do bodu, když virtuální počítač kdyby byl vypnutý plně.

To není obvykle příčinou znepokojení a obvykle se dá ignorovat pro neplánované převzetí služeb při selhání. Pokud je naplánovaná převzetí služeb při selhání, ujistěte se, že virtuální počítač je správně vypnutý před převzetí služeb při selhání a poskytnout dostatek času na probíhající replikaci dat v místním k odeslání do Azure. Potom použijte **nejnovější** možnost [převzetí služeb při selhání obrazovky](site-recovery-failover.md#run-a-failover) tak, aby všechny čekající data v Azure je zpracován do bodu obnovení, který potom slouží pro převzetí služeb při selhání virtuálního počítače.

## <a name="unable-to-select-the-datastore"></a>Nelze vybrat úložiště

Tento problém je označeno, když jste na portálu nezobrazuje úložišti dat v Azure, při pokusu o znovunastavením ochrany tohoto virtuálního počítače, který má došlo k selhání. Důvodem je, že hlavní cíl se nerozpoznal jako virtuální počítač v rámci vCenters přidán do Azure Site Recovery.

Další informace o virtuální počítač znovu se zapíná ochrana, najdete v části [znovunastavení ochrany a navrácení služeb po back počítače k místní lokalitě po převzetí služeb při selhání do Azure](vmware-azure-reprotect.md).

Řešení tohoto problému:

Ručně vytvořit hlavní cíl na vCenter, který spravuje váš zdrojový počítač. Úložiště bude k dispozici po další operace vCenter zjišťování a aktualizace prostředků infrastruktury.

> [!Note]
> 
> Zjišťování a aktualizace prostředků infrastruktury operace může trvat až 30 minut. 

## <a name="linux-master-target-registration-with-cs-fails-with-an-ssl-error-35"></a>Registrace hlavního cíle Linuxu ve službě CS se nezdaří s chybou SSL 35 

Registrace konfiguračního serveru do Azure Site Recovery Master Target nezdaří z důvodu ověření proxy serveru se povoluje na hlavním cíli. 
 
Tato chyba je indikován následující řetězce v protokolu instalace: 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

Řešení tohoto problému:
 
1. Na konfiguračním serveru virtuálního počítače otevřete příkazový řádek a ověřte nastavení proxy serveru pomocí následujících příkazů:

    CAT /etc/environment echo $http_proxy echo $https_proxy 

2. Výstup z předchozích příkazů ukazuje, že jsou definovány http_proxy nebo https_proxy nastavení, použijte jednu z následujících metod odblokujete komunikaci hlavní cíl se konfigurační server:
   
   - Stáhněte si [nástroj PsExec](https://aka.ms/PsExec).
   - Použijte nástroj pro přístup k systému uživatelský kontext a určit, jestli je nakonfigurovaná adresa proxy serveru. 
   - Pokud je nakonfigurován proxy server, otevřete aplikaci Internet Explorer v kontextu uživatele systému pomocí nástroje PsExec.
  
     **psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"**

   - Chcete-li zajistit, že hlavní cílový server může komunikovat s konfiguračním serverem:
  
     - Upravte nastavení proxy serveru v aplikaci Internet Explorer obejít IP adresu hlavního cílového serveru přes proxy server.   
     Nebo
     - Vypněte proxy server na hlavním cílovém serveru. 


## <a name="next-steps"></a>Další postup
- Řešení potíží s [připojení RDP k virtuálnímu počítači Windows](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- Řešení potíží s [připojení SSH k virtuálnímu počítači s Linuxem](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Pokud potřebujete další pomoc, pak zveřejněte svůj dotaz na [fórum Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) nebo komentář na konci tohoto dokumentu. Aktivní komunitě, který by měl být schopni pomoct, máme.
