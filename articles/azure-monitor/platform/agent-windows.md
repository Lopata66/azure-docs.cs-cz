---
title: Připojení počítačů s Windows do Azure monitoru | Dokumentace Microsoftu
description: Tento článek popisuje postup připojení počítačů s Windows hostovaných v jiných cloudech nebo místních do Azure monitoru pomocí agenta Log Analytics pro Windows.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: magoedte
ms.openlocfilehash: 7f562959ac6022539ccf7137f352a2e9507758dc
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67146355"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Připojení počítačů s Windows do Azure monitoru

Pokud chcete monitorování a správa virtuálních počítačů nebo fyzických počítačů v místním datovém centru nebo jiných cloudové prostředí pomocí Azure monitoru, je potřeba nasadit agenta Log Analytics (také označované jako Microsoft Monitoring Agent (MMA)) a konfigurací sestavy na jeden nebo více pracovních prostorů Log Analytics. Agent také podporuje role Hybrid Runbook Worker pro Azure Automation.  

Na monitorovaném počítači Windows je agent uveden jako služba Microsoft Monitoring Agent. Služba Microsoft Monitoring Agent shromažďuje události ze souborů protokolu a protokolu událostí Windows, údaje o výkonu a další telemetrie. I v případě, že agent není schopen komunikovat se službou Azure Monitor bude posílat sestavy, agent běží dál a zařadí do fronty shromážděná data na disk monitorovaného počítače. Při obnovení připojení služba Microsoft Monitoring Agent odešle shromážděná data do služby.

Může být agent nainstalovaný pomocí jedné z následujících metod. Většina zařízení pomocí kombinace těchto metod nainstalovat různé sady počítačů, podle potřeby.  Podrobnosti o použití každé metody jsou k dispozici později v tomto článku.

* Ruční instalace. Instalační program je v počítači pomocí Průvodce instalací, z příkazového řádku spustit ručně nebo nasadit pomocí stávajících nástroje pro distribuci softwaru.
* Azure Automation Desired State Configuration (DSC). Používání DSC ve službě Azure Automation pomocí skriptu pro počítače Windows už nasazená ve vašem prostředí.  
* Skript prostředí PowerShell.
* Šablona Resource Manageru pro virtuální počítače s Windows v místním ve službě Azure Stack. 

>[!NOTE]
>Azure Security Center (ASC) závisí na Microsoft Monitoring Agent (také označované jako agenta Log Analytics Windows) a bude nainstalovat a nakonfigurovat, aby sestavy do pracovního prostoru Log Analytics jako součást nasazení. ASC zahrnuje možnost automatické zřizování, který umožňuje automatickou instalaci agenta Log Analytics Windows na všech virtuálních počítačů ve vašem předplatném a nakonfiguruje ho, aby zprávy do konkrétního pracovního prostoru. Další informace o této možnosti najdete v tématu [povolit automatické zřizování agenta Log Analytics](../../security-center/security-center-enable-data-collection.md#enable-automatic-provisioning-of-microsoft-monitoring-agent-).
>

Pokud je potřeba nakonfigurovat agenta na generování sestav k více než jednomu pracovnímu prostoru, toto nelze provést během počáteční instalace pouze později a aktualizuje nastavení v Ovládacích panelech nebo prostředí PowerShell popsaný v [přidání nebo odebrání pracovního prostoru](agent-manage.md#adding-or-removing-a-workspace).  

Abyste lépe porozuměli podporované konfiguraci, přečtěte si o [podporovaných operačních systémech Windows](log-analytics-agent.md#supported-windows-operating-systems) a [konfiguraci síťové brány firewall](log-analytics-agent.md#network-firewall-requirements).

## <a name="obtain-workspace-id-and-key"></a>Získání ID a klíče pracovního prostoru
Před instalací agenta Log Analytics pro Windows, budete potřebovat ID pracovního prostoru a klíč vašeho pracovního prostoru Log Analytics.  Tyto informace jsou nezbytné při instalaci z každé metody instalace ke správné konfiguraci agenta a zajistěte, aby že mohla úspěšně komunikovat s Azure Monitor v komerční Azure a cloud pro státní správu USA. 

1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor, který chcete v konfiguraci agenta pro hlášení.
3. Vyberte **Upřesňující nastavení**.<br><br> ![Upřesňující nastavení Log Analytics](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Vyberte **Připojené zdroje** a pak **Servery Windows**.   
5. Zkopírujte a vložte do oblíbeného editoru, **ID pracovního prostoru** a **primární klíč**.    
   
## <a name="configure-agent-to-use-tls-12"></a>Konfigurace agenta pro použití protokolu TLS 1.2
Konfigurace používání [TLS 1.2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) protokol pro komunikaci mezi agentem Windows a službě Log Analytics, můžete postupovat podle postupem uvedeným níže povolte předtím, než je agent nainstalovaný na virtuálním počítači nebo později.   

1. Vyhledejte následující podklíč registru: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Vytvořit podklíč pod **protokoly** pro TLS 1.2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**
3. Vytvoření **klienta** podklíče pod podklíčem verze protokolu TLS 1.2 jste vytvořili dříve. Například **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**.
4. Vytvořte následující hodnoty DWORD pod **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**:

    * **Povolené** [hodnota = 1]
    * **DisabledByDefault** [hodnota = 0]  

Konfigurace rozhraní .NET Framework 4.6 nebo novější k podpoře zabezpečené šifrování, protože ve výchozím nastavení je zakázána. [Silného šifrování](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) používá bezpečnější síťové protokoly, jako je protokol TLS 1.2 a blokuje protokoly, které nejsou zabezpečeny. 

1. Vyhledejte následující podklíč registru: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319**.  
2. Vytvořte hodnotu DWORD **SchUseStrongCrypto** v tomto podklíči s hodnotou **1**.  
3. Vyhledejte následující podklíč registru: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319**.  
4. Vytvořte hodnotu DWORD **SchUseStrongCrypto** v tomto podklíči s hodnotou **1**. 
5. Restartujte systém pro nastavení projevilo. 

## <a name="install-the-agent-using-setup-wizard"></a>Instalace agenta pomocí Průvodce instalací
Následující kroky instalace a konfigurace agenta Log Analytics v Azure a cloudu Azure Government s použitím Průvodce instalací pro agenta v počítači. Pokud chcete další informace o konfiguraci agenta tak, aby také sestavy ke skupině pro správu System Center Operations Manager najdete v tématu [nasazení agenta nástroje Operations Manager pomocí Průvodce instalací agenta](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. V pracovním prostoru Log Analytics z **servery Windows** přejde dříve, vyberte příslušné stránky **stáhnout agenta Windows** verze ke stažení v závislosti na architektuře procesoru operační systém Windows.   
2. Spusťte instalační program a nainstalujte agenta na svém počítači.
2. Na **úvodní** stránce klikněte na **Další**.
3. Na stránce **Licenční podmínky** si přečtěte licenční podmínky a pak klikněte na **Souhlasím**.
4. Na stránce **Cílová složka** změňte nebo ponechte výchozí instalační složku a pak klikněte na **Další**.
5. Na stránce **Možnosti instalace agenta** zvolte připojení agenta k Azure Log Analytics a pak klikněte na **Další**.   
6. Na stránce **Azure Log Analytics** postupujte následovně:
   1. Vložte **ID pracovního prostoru** a **Klíč pracovního prostoru (primární klíč)** , které jste si zkopírovali dříve.  Pokud se má počítač hlásit do pracovního prostoru služby Log Analytics v cloudu Azure Government, vyberte z rozevíracího seznamu **Cloud Azure** možnost **Azure US Government**.  
   2. Pokud počítač potřebuje komunikovat se službou Log Analytics přes proxy server, klikněte na **Upřesnit** a zadejte adresu URL a číslo portu proxy serveru.  Pokud váš proxy server vyžaduje ověření, zadejte uživatelské jméno a heslo pro ověření proxy serveru a pak klikněte na **Další**.  
7. Jakmile dokončíte zadávání nezbytných nastavení konfigurace, klikněte na **Další**.<br><br> ![vložení ID pracovního prostoru a primárního klíče](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Na stránce **Připraveno k instalaci** zkontrolujte zvolené volby a pak klikněte na **Nainstalovat**.
9. Na stránce **Konfigurace byla úspěšně dokončena** klikněte na **Dokončit**.

Po dokončení se **Microsoft Monitoring Agent** zobrazí v **Ovládacích panelech**. Pokud chcete potvrdit, odesílá informace do Log Analytics, přečtěte si téma [ověřit připojení agenta k Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Instalace agenta pomocí příkazového řádku
Stažený soubor pro agenta je samostatná instalace balíčku.  Instalační program pro agenta a jeho podpůrné soubory jsou obsažené v balíčku a potřeba extrahovat, aby bylo možné správně nainstalovat pomocí příkazového řádku je znázorněno v následujícím příkladu.    

>[!NOTE]
>Pokud chcete provést upgrade agenta, musíte používat službu Log Analytics, rozhraní API pro skriptování. Naleznete v tématu [Správa a údržba agenta Log Analytics pro Windows a Linux](agent-manage.md) pro další informace.

Následující tabulka obsahuje konkrétní parametry nepodporuje instalaci agenta, včetně toho, když se nasadí pomocí Automation DSC.

|Možnosti specifické pro agenta MMA                   |Poznámky         |
|---------------------------------------|--------------|
| NASTAVENÍ NOAPM = 1                               | Volitelný parametr. Nainstaluje agenta bez .NET Application Performance Monitoring pro aplikace.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = konfigurace agenta na generování sestav s pracovním prostorem                |
|OPINSIGHTS_WORKSPACE_ID                | ID pracovního prostoru (guid) pro pracovní prostor pro přidání                    |
|OPINSIGHTS_WORKSPACE_KEY               | Klíč pracovního prostoru pro počáteční ověření s pracovním prostorem |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Zadejte cloudovém prostředí, kde se nachází pracovní prostor <br> 0 = komerčním cloudu azure (výchozí) <br> 1 = azure Government |
|OPINSIGHTS_PROXY_URL               | Identifikátor URI pro použití proxy serveru |
|OPINSIGHTS_PROXY_USERNAME               | Uživatelské jméno pro přístup k ověřený proxy server |
|OPINSIGHTS_PROXY_PASSWORD               | Heslo pro přístup k ověřený proxy server |

1. K extrakci instalačních souborů agenta z příkazového řádku se zvýšenými oprávněními spusťte `MMASetup-<platform>.exe /c` a zobrazí výzvu pro cestu k extrakci souborů.  Případně lze zadat cestu předáním argumentů `MMASetup-<platform>.exe /c /t:<Full Path>`.  
2. Bezobslužná instalace agenta a nakonfigurovat, aby hlásit do pracovního prostoru v komerčním cloudu Azure, ze složky extrahovány instalační soubory, zadejte: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   nebo pokud chcete konfigurovat agenta na generování sestav do cloudu Azure pro státní správu USA, zadejte: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >Řetězcové hodnoty pro parametry *OPINSIGHTS_WORKSPACE_ID* a *OPINSIGHTS_WORKSPACE_KEY* musí být zapouzdřen v uvozovkách dáte pokyn, aby instalační služby systému Windows na interprit jako možnosti pro balíček. 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Instalace agenta pomocí ve službě Azure Automation DSC

Následující ukázkový skript můžete použít k instalaci agenta pomocí Azure Automation DSC.   Pokud nemáte účet Automation, přečtěte si téma [Začínáme s Azure Automation](/azure/automation/) vám pomohou pochopit požadavky a kroky pro vytvoření účtu Automation pomocí Automation DSC je požadována.  Pokud nejste obeznámeni s Automation DSC, přečtěte si [Začínáme se službou Automation DSC](../../automation/automation-dsc-getting-started.md).

Následující příklad nainstaluje agenta 64bitovým kompilátorem identifikované `URI` hodnotu. Můžete také použít 32bitovou verzi tak, že nahradíte hodnotu identifikátoru URI. Identifikátory URI pro obě verze jsou:

- Agent Windows 64-bit – https://go.microsoft.com/fwlink/?LinkId=828603
- Agent Windows 32-bit – https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Příklad tohoto postupu a skripty nepodporuje upgrade agenta už nasadili do počítače Windows.

32bitové a 64bitové verze balíček agenta mají jiný produkt kódy a vydání nové verze také mít jedinečnou hodnotu.  Kód produktu je identifikátor GUID, který je základní identifikátor aplikace nebo produktu a je reprezentována Instalační služby systému Windows **ProductCode** vlastnost.  `ProductId` Hodnotu **MMAgent.ps1** skriptu musí odpovídat kód produktu z instalačního balíčku 32bitová nebo 64bitová verze agenta.

Chcete-li načíst kód přímo z instalačního balíčku agenta, můžete použít Orca.exe z [Windows vývojáři sady SDK součásti pro Windows Installer](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) , který je součástí sady Windows Software Development Kit nebo pomocí Prostředí PowerShell následující [ukázkový skript](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) napsané pomocí Microsoft Valuable Professional (MVP).  Kterýkoliv přístup, musíte nejprve k extrakci **MOMagent.msi** souboru z instalačního balíčku MMASetup.  To je ukázáno dříve v prvním kroku v části [instalace agenta pomocí příkazového řádku](#install-the-agent-using-the-command-line).  

1. Import DSC xPSDesiredStateConfiguration modulu z [ https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration ](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) do Azure Automation.  
2.  Vytvoření proměnných assetů Azure Automation pro *OPSINSIGHTS_WS_ID* a *OPSINSIGHTS_WS_KEY*. Nastavte *OPSINSIGHTS_WS_ID* ID pracovního prostoru Log Analytics a nastavte *OPSINSIGHTS_WS_KEY* na primární klíč pracovního prostoru.
3.  Zkopírujte skript a uložte ho jako MMAgent.ps1.

    ```powershell
    Configuration MMAgent
    {
        $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
        $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
        $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

        Import-DscResource -ModuleName xPSDesiredStateConfiguration
        Import-DscResource -ModuleName PSDesiredStateConfiguration

        Node OMSnode {
            Service OIService
            {
                Name = "HealthService"
                State = "Running"
                DependsOn = "[Package]OI"
            }

            xRemoteFile OIPackage {
                Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
                DestinationPath = $OIPackageLocalPath
            }

            Package OI {
                Ensure = "Present"
                Path  = $OIPackageLocalPath
                Name = "Microsoft Monitoring Agent"
                ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
                Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
                DependsOn = "[xRemoteFile]OIPackage"
            }
        }
    }

    ```

4. Aktualizace `ProductId` hodnoty ve skriptu s kódem produktu extrahují z nejnovější verze agenta nainstalovat balíček pomocí metody doporučuje dříve. 
5. [Importovat konfigurační skript MMAgent.ps1](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) do účtu Automation. 
5. [Přiřaďte počítači Windows nebo uzlu](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) ke konfiguraci. Během 15 minut uzel ověří jeho konfigurace a agent se vloží do uzlu.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Ověřit připojení agenta k Log Analytics

Po dokončení instalace agenta ověřením úspěšně připojen a generování sestav můžete provést dvěma způsoby.  

V **Ovládacích panelech** na počítači vyhledejte položku **Microsoft Monitoring Agent**.  Vyberte ji a na kartě **Azure Log Analytics** by agent měl zobrazit tuto zprávu: **Microsoft Monitoring Agent se úspěšně připojilo ke službě Microsoft Operations Management Suite.**<br><br> ![Stav připojení MMA k Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Jednoduchý protokol dotazu můžete provést také na webu Azure Portal.  

1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Azure Monitor**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Azure Monitor**.  
2. Vyberte **protokoly** v nabídce. 
2. V podokně protokoly v aplikaci zadejte do pole dotazu:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Ve výsledcích hledání vrátí měli byste vidět záznamy prezenčního signálu pro počítač oznamující, že je připojený a vytváření sestav ve službě.   

## <a name="next-steps"></a>Další postup

- Kontrola [Správa a údržba agenta Log Analytics pro Windows a Linux](agent-manage.md) Další informace o tom, jak Správa agenta během životního cyklu jeho nasazení na vašich počítačích.  

- Kontrola [řešení potíží s agentem Windows](agent-windows-troubleshoot.md) Pokud narazíte na problémy při instalaci a správě agenta.
