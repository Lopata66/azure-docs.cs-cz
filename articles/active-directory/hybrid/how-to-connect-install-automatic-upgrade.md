---
title: 'Azure AD Connect: automatický upgrade | Microsoft Docs'
description: Toto téma popisuje integrovanou funkci automatického upgradu v Azure AD Connect synchronizaci.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a05de8bf6a6e4ab79e63d6634ddb1b79fae6045f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680217"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: Automatický upgrade
Tato funkce byla představena s [1.1.105.0EM buildu (vydáno 2016. února)](reference-connect-version-history.md#111050).  Tato funkce se aktualizovala v [Build 1.1.561](reference-connect-version-history.md#115610) a teď podporuje další scénáře, které se dřív nepodporovaly.

## <a name="overview"></a>Přehled
Ujistěte se, že je instalace Azure AD Connect vždycky aktuální, a to díky funkci **automatického upgradu** nikdy jednodušší. Tato funkce je ve výchozím nastavení povolená pro Expresní instalace a upgrady DirSync. Po vydání nové verze se vaše instalace automaticky upgraduje.
Automatický upgrade je ve výchozím nastavení povolený pro následující:

* Instalace expresních nastavení a DirSync upgrady.
* Pomocí SQL Express LocalDB, což je to, co expresní nastavení vždycky používá. DirSync s SQL Express také používá LocalDB.
* Účet služby AD je výchozí účet MSOL_ vytvořený pomocí expresního nastavení a DirSync.
* V úložišti Metaverse musí být méně než 100 000 objektů.

Aktuální stav automatického upgradu můžete zobrazit pomocí rutiny PowerShellu `Get-ADSyncAutoUpgrade` . Má následující stavy:

| State | Komentář |
| --- | --- |
| Povoleno |Automatický upgrade je povolen. |
| Dočasně blokován. |Nastaveno pouze systémem. Systém nemá v **současné době** nárok na příjem automatických upgradů. |
| Zakázáno |Automatický upgrade je zakázán. |

Můžete změnit mezi **povolenými** a **zakázanými** pomocí `Set-ADSyncAutoUpgrade` . Pouze systém by měl nastavit stav **pozastaveno**.  Před 1.1.750.0 by rutina Set-ADSyncAutoUpgrade blokovala automatický upgrade, pokud byl stav automatického upgradu nastavený na pozastaveno. Tato funkce se teď změnila, takže neblokuje autoupgrade.

Automatický upgrade používá Azure AD Connect Health pro infrastrukturu upgradu. Aby mohl automatický upgrade fungovat, ujistěte se, že jste otevřeli adresy URL v proxy server pro **Azure AD Connect Health** , jak je popsáno v [adresách URL Office 365 a rozsahech IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Pokud **Synchronization Service Manager** uživatelské rozhraní běží na serveru, upgrade se pozastaví, dokud se uživatelské rozhraní nezavře.

## <a name="troubleshooting"></a>Řešení potíží
Pokud se vaše instalace připojení neupgraduje podle očekávání, postupujte podle těchto kroků a zjistěte, co by mohlo být chybné.

Za prvé byste neměli očekávat, že se automatický upgrade bude pokoušet o první vydání nové verze. Před pokusem o upgrade došlo k úmyslnému náhodnosti, takže pokud se instalace neupgraduje hned, neprovádějte upozornění.

Pokud si myslíte, že něco není napravo, spusťte nejprve, `Get-ADSyncAutoUpgrade` abyste zajistili, že je povolen automatický upgrade.

Pak se ujistěte, že jste na proxy serveru nebo v bráně firewall otevřeli požadované adresy URL. Automatická aktualizace používá Azure AD Connect Health, jak je popsáno v [přehledu](#overview). Pokud používáte proxy server, ujistěte se, že stav byl nakonfigurován tak, aby používal [proxy server](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Také otestujte [připojení ke stavu](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) Azure AD.

Po ověření připojení k Azure AD je čas na to, abyste se mohli podívat na protokol událostí. Spusťte prohlížeč událostí a podívejte se do protokolu událostí **aplikace** . Přidejte filtr EventLog pro zdroj **Azure AD Connect upgradujte** a rozsah id události **300-399**.  
![Filtr protokolu událostí pro automatický upgrade](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

Teď můžete zobrazit události související se stavem pro automatický upgrade.  
![Filtr protokolu událostí pro automatický upgrade](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

Kód výsledku obsahuje předponu s přehledem stavu.

| Předpona kódu výsledku | Popis |
| --- | --- |
| Úspěch |Instalace byla úspěšně upgradována. |
| UpgradeAborted |Upgrade zastavil dočasnou podmínku. Bude znovu opakován a očekává se, že bude později úspěšné. |
| UpgradeNotSupported |Systém má konfiguraci blokující automatickou aktualizaci systému. Zkusí se znovu zjistit, jestli se stav mění, ale očekává se, že je potřeba upgradovat systém ručně. |

Tady je seznam nejběžnějších zpráv, které najdete. Neobsahuje žádné výpisy, ale zpráva výsledku by měla být nejasná s obsahem problému.

| Zpráva výsledku | Popis |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Do registru nejde zapisovat. |
| UpgradeAbortedInsufficientDatabasePermissions |Předdefinovaná skupina Administrators nemá oprávnění k databázi. Chcete-li tento problém vyřešit, proveďte ruční upgrade na nejnovější verzi Azure AD Connect. |
| UpgradeAbortedInsufficientDiskSpace |Není dostatek místa na disku pro podporu upgradu. |
| UpgradeAbortedSecurityGroupsNotPresent |Nepovedlo se najít a vyřešit všechny skupiny zabezpečení používané synchronizačním modulem. |
| UpgradeAbortedServiceCanNotBeStarted |Nepovedlo se spustit synchronizaci služby NT **Microsoft Azure AD** . |
| UpgradeAbortedServiceCanNotBeStopped |Nepodařilo se zastavit službu NT **Microsoft Azure AD Sync** . |
| UpgradeAbortedServiceIsNotRunning |Služba NT **Microsoft Azure AD Sync** není spuštěná. |
| UpgradeAbortedSyncCycleDisabled |Možnost SyncCycle je v [plánovači](how-to-connect-sync-feature-scheduler.md) zakázaná. |
| UpgradeAbortedSyncExeInUse |[Synchronizace uživatelského rozhraní Service Manageru](how-to-connect-sync-service-manager-ui.md) je na serveru otevřená. |
| UpgradeAbortedSyncOrConfigurationInProgress |Spustí se Průvodce instalací nebo se naplánovala synchronizace mimo Plánovač. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | Jako metodu přihlašování jste vybrali službu AD FS. |
| UpgradeNotSupportedCustomizedSyncRules |Do konfigurace jste přidali vlastní pravidla. |
| UpgradeNotSupportedDeviceWritebackEnabled |Povolili jste funkci [zpětného zápisu zařízení](how-to-connect-device-writeback.md) . |
| UpgradeNotSupportedGroupWritebackEnabled |Povolili jste funkci zpětného zápisu skupiny. |
| UpgradeNotSupportedInvalidPersistedState |Instalace není expresním nastavením nebo DirSyncm upgradem. |
| UpgradeNotSupportedMetaverseSizeExceeeded |V úložišti Metaverse máte více než 100 000 objektů. |
| UpgradeNotSupportedMultiForestSetup |Připojujete se k více než jedné doménové struktuře. Expresní instalace se připojuje jenom k jedné doménové struktuře. |
| UpgradeNotSupportedNonLocalDbInstall |Nepoužíváte databázi SQL Server Express LocalDB. |
| UpgradeNotSupportedNonMsolAccount |[Účet služba AD DS Connector](reference-connect-accounts-permissions.md#ad-ds-connector-account) už není výchozím MSOL_m účtem. |
| UpgradeNotSupportedNotConfiguredSignInMethod | Při nastavování AAD Connect jste zvolili možnost *Nekonfigurovat* při výběru metody přihlašování. |
| UpgradeNotSupportedStagingModeEnabled |Server je nastavený jako [pracovní režim](how-to-connect-sync-staging-server.md). |
| UpgradeNotSupportedUserWritebackEnabled |Povolili jste funkci [zpětného zápisu uživatelů](how-to-connect-preview.md#user-writeback) . |

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
