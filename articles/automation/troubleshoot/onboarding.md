---
title: Řešení potíží s chyby registrace Update Management, Change Tracking a Inventory
description: Zjistěte, jak řešit potíže s Update Management, Change Tracking a Inventory řešení chybami připojení
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 3687a2fdcba9c2078bbbd9344089b5a22467682c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477486"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>Řešení chyb při registraci řešení

Pravděpodobně narazíte na chyby při připojování řešení, jako jsou Update Management nebo Change Tracking a Inventory. Tento článek popisuje různé chyby, které mohou nastat a způsob jejich řešení.

## <a name="general-errors"></a>Obecné chyby

### <a name="missing-write-permissions"></a>Scénář: Registrace selže se zprávou – řešení není možné.

#### <a name="issue"></a>Problém

Při pokusu o připojení virtuálního počítače do řešení zobrazí jedna z následujících zpráv:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Příčina

Tato chyba je způsobena nesprávnou nebo chybějící oprávnění na virtuálním počítači, pracovní prostor, nebo pro uživatele.

#### <a name="resolution"></a>Řešení

Ujistěte se, že máte správná oprávnění k připojení virtuálního počítače. Zkontrolujte [práva potřebná k připojení počítačů](../automation-role-based-access-control.md#onboarding) a připojit řešení znovu. Pokud se zobrazí chyba `The solution cannot be enabled on this VM because the permission to read the workspace is missing`, ujistěte se, že máte `Microsoft.OperationalInsights/workspaces/read` oprávnění, abyste mohli najít, pokud je virtuální počítač připojit k pracovnímu prostoru.

### <a name="diagnostic-logging"></a>Scénář: Registrace selže a zobrazí se zpráva – nepovedlo se nakonfigurovat účet automatizace pro protokolování diagnostiky

#### <a name="issue"></a>Problém

Při pokusu o připojení virtuálního počítače do řešení zobrazí následující zpráva:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Příčina

Tato chyba může nastat, pokud cenová úroveň neodpovídá modelu fakturace předplatného. Další informace najdete v tématu [monitorování využití a odhadované náklady ve službě Azure Monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Řešení

Ruční vytvoření pracovního prostoru Log Analytics a zopakovat tento proces registrace a vyberte pracovní prostor vytvořený.

### <a name="computer-group-query-format-error"></a>Scénář: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problém

Tento kód chyby znamená, že uložený vyhledávací dotaz skupiny počítače používají k zaměření řešení nemá správný formát. 

#### <a name="cause"></a>Příčina

Můžete změnit dotaz, nebo může byla změněna systému.

#### <a name="resolution"></a>Řešení

Dotaz pro toto řešení a reonboard řešení, která znovu vytvoří dotaz, můžete odstranit. Dotaz můžete najít v rámci pracovního prostoru v části **uložená hledání**. Název dotazu je **MicrosoftDefaultComputerGroup**, a název řešení spojených s Tento dotaz je kategorie dotazu. Pokud je povoleno více řešení, **MicrosoftDefaultComputerGroup** zobrazuje více než jednou v rámci **uložená hledání**.

### <a name="policy-violation"></a>Scénář: PolicyViolation

#### <a name="issue"></a>Problém

Tento kód chyby znamená, že nasazení se nezdařilo z důvodu porušení zásady jednoho nebo více.

#### <a name="cause"></a>Příčina 

Zásady je na místě, které blokuje dokončení operace.

#### <a name="resolution"></a>Řešení

Pokud chcete úspěšně nasadit řešení, je potřeba zvážit změnu označený zásad. Jsou různé druhy zásad, které lze definovat konkrétní změny požadované závisí na zásadu, která je porušena. Například pokud zásada byla definována na skupinu prostředků, která odepřena oprávnění ke změně obsahu určité typy prostředků v rámci této skupiny prostředků, můžete je například provedete kterýkoli z následujících:

* Úplně odeberte zásady.
* Pokuste se připojit k jiné skupině prostředků.
* Upravit zásady, tím, k například:
  * Změnu cíle zásad do konkrétního prostředku (například za účelem konkrétní účet Automation).
  * Úprava sady prostředků této zásadě byl konfigurován k odepření.

Zkontrolovat oznámení v pravém horním rohu webu Azure portal nebo přejděte do skupiny prostředků obsahující účet automation a vyberte **nasazení** pod **nastavení** zobrazíte neúspěšný nasazení. Další informace o službě Azure Policy najdete v tématu: [Přehled služby Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="unlink"></a>Scénář: Chyby při pokusu o zrušení propojení pracovního prostoru

#### <a name="issue"></a>Problém

Při pokusu o zrušení propojení pracovního prostoru se zobrazí následující chyba:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Příčina

Tato chyba nastane, pokud máte řešení aktivní ve vašem pracovním prostoru Log Analytics, která závisí na pracovního prostoru účtu Automation a analýzy protokolu na které se odkazuje.

### <a name="resolution"></a>Řešení

Pokud chcete tento problém vyřešit, musíte odebrat následující řešení z pracovního prostoru, pokud je používáte:

* Update Management
* Sledování změn
* Spuštění/zastavení virtuálních počítačů mimo špičku

Po odebrání řešení můžete zrušit propojení pracovního prostoru. Je třeba odstranit všechny existující artefakty z těchto řešení z pracovního prostoru a účtu Automation i.  

* Update Management
  * Odebrat nasazení aktualizací (plány) ve svém účtu Automation
* Spuštění/zastavení virtuálních počítačů mimo špičku
  * Odebrání všech zámků na součásti řešení ve vašem účtu Automation v části **nastavení** > **zámky**.
  * Pro další kroky k odebrání špičku spouštění/zastavování virtuálních počítačů najdete v článku, [odebrání spuštění/zastavení virtuálního počítače špičku](../automation-solution-vm-management.md##remove-the-solution).

## <a name="mma-extension-failures"></a>Chyby rozšíření agenta MMA

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Při nasazení řešení, jsou nasazené širokou škálu související prostředky. Jeden z těchto prostředků je rozšíření Microsoft Monitoring Agent nebo Log Analytics agenta pro Linux. Jedná se o rozšíření virtuálního počítače nainstalovat agenta hosta virtuálního počítače, který zodpovídá za komunikaci s nakonfigurovaný pracovní prostor Log Analytics pro účely novější koordinaci stahování binárních souborů a další soubory, které řešení budete registrace závisí na po zahájení provádění.
Je obvykle nejprve zaregistrují agenta MMA nebo Log Analytics pro Linux chyby při instalaci z oznámení v centru oznámení. Kliknutím na toto oznámení obsahuje další informace o toto specifické selhání. Navigace na prostředek skupiny prostředků a potom na element nasazení v ní také poskytuje podrobné informace o selhání nasazení, ke kterým došlo.
Instalace agenta MMA nebo Log Analytics pro Linux může selhat z různých důvodů a kroky pro řešení těchto chyb lišit v závislosti na problému. Postupujte podle konkrétní postup řešení potíží.

Následující část popisuje různé problémy, které se můžete setkat při připojování, způsobit selhání nasazení rozšíření agenta MMA.

### <a name="webclient-exception"></a>Scénář: Během požadavku WebClient došlo k výjimce

Rozšíření agenta MMA na virtuálním počítači se nemůže komunikovat s externím prostředkům a nasazení se nezdaří.

#### <a name="issue"></a>Problém

Následují příklady chybové zprávy, které jsou vráceny:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Příčina

Některé možné příčiny této chyby jsou:

* Je proxy server nakonfigurovaný na virtuálním počítači, povolující jen určité porty.

* Nastavení brány firewall má zablokovaný přístup k požadované porty a adresy.

#### <a name="resolution"></a>Řešení

Ujistěte se, že máte správné porty a adresy otevřít pro komunikaci. Seznam portů a adres najdete v tématu [plánování sítě](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="transient-environment-issue"></a>Scénář: Instalace se nezdařila z důvodu problémů s přechodnou prostředí

Instalace rozšíření Microsoft Monitoring Agent selhala při nasazení z důvodu jiná instalace nebo blokuje instalaci akce

#### <a name="issue"></a>Problém

Následují příklady chybové zprávy mohou být vráceny:

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Příčina

Některé možné příčiny této chyby jsou:

* Probíhá jiná instalace
* Systém se aktivuje restartovat během nasazování šablony

#### <a name="resolution"></a>Řešení

Tato chyba je přechodná chyba ze své podstaty. Pokusem o nasazení k instalaci rozšíření.

### <a name="installation-timeout"></a>Scénář: Časový limit instalace

Instalace agenta MMA rozšíření nebyla dokončena z důvodu vypršení časového limitu.

#### <a name="issue"></a>Problém

V následujícím příkladu je chybová zpráva, která mohou být vráceny:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Příčina

K této chybě dochází, protože virtuální počítač se v případě velkého zatížení během instalace.

### <a name="resolution"></a>Řešení

Pokus o instalaci rozšíření agenta MMA, při nižší zatížení virtuálního počítače.

## <a name="next-steps"></a>Další postup

Pokud nezobrazila váš problém nebo nelze vyřešit vaše potíže, navštíví některý z následujících kanálů pro další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
