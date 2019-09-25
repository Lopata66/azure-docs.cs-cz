---
title: Postup generování a přenosu klíčů chráněných HSM pro Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Tento článek vám může pomáhat při plánování, generování a přenosu vlastních klíčů chráněných HSM pro použití s Azure Key Vault. Označuje se také jako BYOK nebo Přineste si vlastní klíč.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 71b7e4bd9406e7fb300ebccd86908820b7628c29
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71000765"
---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Postup generování a přenosu klíčů chráněných HSM pro Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pro zvýšení zabezpečení při použití Azure Key Vault můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM), které nikdy nezanechají hranici HSM. Tento scénář se často označuje jako *Přineste si vlastní klíč*nebo BYOK. Moduly hardwarového zabezpečení jsou ověřené podle standardu FIPS 140-2 Level 2. Azure Key Vault k ochraně klíčů používá podpůrný software nCipher hardwarového nShield Family HSM.

Informace v tomto tématu vám pomůžou při plánování, generování a přenosu vlastních klíčů chráněných HSM, které se používají s Azure Key Vault.

Tato funkce není pro Azure Čína k dispozici.

> [!NOTE]
> Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](key-vault-overview.md)  
> Úvodní kurz, který zahrnuje vytvoření trezoru klíčů pro klíče chráněné HSM, najdete v tématu [co je Azure Key Vault?](key-vault-overview.md).

Další informace o generování a přenosu klíče chráněného HSM přes Internet:

* Vygenerujete klíč z offline pracovní stanice, která zmenšuje prostor pro útoky.
* Klíč je zašifrovaný pomocí klíče pro výměnu klíčů (KEK), který zůstane zašifrovaný, dokud se nepřenáší do Azure Key Vault HSM. Původní pracovní stanici opouští jenom zašifrovaná verze vašeho klíče.
* Sada nástrojů sady nástrojů nastaví vlastnosti vašeho klíče tenanta, který sváže váš klíč s Azure Key Vault Security World. Takže když Azure Key Vault HSM přijme a dešifruje váš klíč, můžou ho použít jenom ty HSM. Váš klíč se nedá exportovat. Tato vazba je vynutila podpůrný software nCipher HSM.
* Klíč výměny klíčů (KEK), který se používá k zašifrování klíče, se vygeneruje v rámci Azure Key Vault HSM a nedá se exportovat. HSM vynutila, že nemůžete bez jasné verze KEK mimo HSM. Kromě toho sada nástrojů zahrnuje ověření identity z podpůrný software nCipher, že KEK nelze exportovat a byla vygenerována v originálním modulu HSM, který byl vyroben pomocí podpůrný software nCipher.
* Sada nástrojů zahrnuje ověření identity od podpůrný software nCipher, že byl také vygenerován Azure Key Vault Security World na originálním modulu HSM, který vyrábí podpůrný software nCipher. Tato ověření potvrzuje, že Microsoft používá originální hardware.
* Microsoft používá samostatné KEK a samostatné světů zabezpečení v jednotlivých geografických oblastech. Toto oddělení zajišťuje, že se klíč dá použít jenom v datových centrech v oblasti, ve které jste ho zašifroval. Například klíč od Evropského zákazníka se nedá použít v datových centrech v Severní Americe nebo Asii.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>Další informace o podpůrný software nCipher HSM a službách Microsoftu

Podpůrný software nCipher Security je přední globální poskytovatel šifrování dat a řešení pro místní zabezpečení v oblasti finančních služeb, vysoké technologie, výroby, státní správy a technologických odvětví. V rámci 40 záznamů o ochraně firemních a vládních informací se podpůrný software nCipher řešení zabezpečení zabezpečená pomocí čtyř pěti největších společností. Jejich řešení používají i 22 zemí a oblastí NATO a zabezpečení více než 80 na celosvětové transakce v rámci plateb.

Microsoft spolupracuje se zabezpečením podpůrný software nCipher a vylepšuje stav grafiky pro HSM. Tato vylepšení vám umožní získat běžné výhody hostovaných služeb, aniž byste museli opustit kontrolu vašich klíčů. Konkrétně tato vylepšení umožňují společnosti Microsoft spravovat HSM, takže je nemusíte používat. Jako cloudová služba Azure Key Vault škáluje v krátkém časovém oznámení, aby splňovala špičky využití vaší organizace. Ve stejnou chvíli je váš klíč chráněný v rámci HSM Microsoftu: Podržíte kontrolu nad životním cyklem klíčů, protože vygenerujete klíč a přenášíte ho do HSM Microsoftu.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementace Přineste si vlastní klíč (BYOK) pro Azure Key Vault

Následující informace a postupy použijte, pokud vygenerujete vlastní klíč chráněný HSM a pak ho přeneste do Azure Key Vault – scénář Přineste si vlastní klíč (BYOK).

## <a name="prerequisites-for-byok"></a>Předpoklady pro BYOK

Seznam požadavků pro Přineste si vlastní klíč (BYOK) pro Azure Key Vault najdete v následující tabulce.

| Požadavek | Další informace |
| --- | --- |
| Předplatné Azure |Pokud chcete vytvořit Azure Key Vault, potřebujete předplatné Azure: [Zaregistrovat se k bezplatné zkušební verzi](https://azure.microsoft.com/pricing/free-trial/) |
| Úroveň služby Azure Key Vault Premium na podporu klíčů chráněných HSM |Další informace o úrovních služby a možnostech pro Azure Key Vault najdete na webu [Azure Key Vault s cenami](https://azure.microsoft.com/pricing/details/key-vault/) . |
| Podpůrný software nCipher hardwarového nShield HSM, SmartCard a software podpory |Musíte mít přístup k modulu hardwarového zabezpečení podpůrný software nCipher a základnímu provoznímu znalostí podpůrný software nCipher hardwarového nShield HSM. Seznam kompatibilních modelů najdete v tématu [podpůrný software nCipher hardwarového nShield hardware Security Module](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/how-to-buy) , případně si můžete koupit modul hardwarového zabezpečení (HSM), pokud ho ještě nemáte. |
| Následující hardware a software:<ol><li>Offline pracovní stanice x64 s minimálním operačním systémem Windows pro Windows 7 a software podpůrný software nCipher hardwarového nShield, který je minimálně verze 11,50.<br/><br/>Pokud tato pracovní stanice používá systém Windows 7, musíte [nainstalovat Microsoft .NET Framework 4,5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Pracovní stanice, která je připojená k Internetu a má minimální operační systém Windows Windows 7 a nainstalovanou **minimální verzi** [Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) 1.1.0.</li><li>Jednotka USB nebo jiné přenosné úložné zařízení, které má alespoň 16 MB volného místa.</li></ol> |Z bezpečnostních důvodů doporučujeme, aby první pracovní stanice nebyla připojená k síti. Toto doporučení se ale nevynutilo programově.<br/><br/>V následujících pokynech se tato pracovní stanice označuje jako odpojená pracovní stanice.</p></blockquote><br/>Pokud je váš klíč tenanta pro produkční síť, doporučujeme, abyste k stažení sady nástrojů používali druhou, samostatnou pracovní stanici a nahráli jste klíč tenanta. Pro účely testování ale můžete použít stejnou pracovní stanici jako první.<br/><br/>V následujících pokynech se tato druhá pracovní stanice označuje jako pracovní stanice připojená k Internetu.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generování a přenos klíče pro Azure Key Vault HSM

Pomocí následujících pěti kroků vygenerujte a přeneste klíč do Azure Key Vault HSM:

* [Krok 1: Příprava pracovní stanice připojené k Internetu](#step-1-prepare-your-internet-connected-workstation)
* [Krok 2: Příprava odpojené pracovní stanice](#step-2-prepare-your-disconnected-workstation)
* [Krok 3: Vygenerujte klíč](#step-3-generate-your-key)
* [Krok 4: Příprava klíče k přenosu](#step-4-prepare-your-key-for-transfer)
* [Krok 5: Přeneste svůj klíč do Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Krok 1: Příprava pracovní stanice připojené k Internetu

Pro tento první krok proveďte následující postupy na pracovní stanici, která je připojená k Internetu.

### <a name="step-11-install-azure-powershell"></a>Krok 1,1: Instalovat Azure PowerShell

Z pracovní stanice připojené k Internetu Stáhněte a nainstalujte modul Azure PowerShell, který obsahuje rutiny pro správu Azure Key Vault. Pokyny k instalaci najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>Krok 1,2: Získat ID předplatného Azure

Spusťte relaci Azure PowerShell a přihlaste se k účtu Azure pomocí následujícího příkazu:

```Powershell
   Connect-AzAccount
```
V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. Pak použijte příkaz [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) :

```powershell
   Get-AzSubscription
```
Z výstupu vyhledejte ID předplatného, které budete používat pro Azure Key Vault. Toto ID předplatného budete potřebovat později.

Nezavírejte okno Azure PowerShell.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Krok 1,3: Stáhnout sadu nástrojů BYOK pro Azure Key Vault

Navštivte službu Stažení softwaru a Stáhněte si [sadu nástrojů Azure Key Vault BYOK](https://www.microsoft.com/download/details.aspx?id=45345) pro vaši geografickou oblast nebo instanci Azure. Pomocí následujících informací Identifikujte název balíčku ke stažení a odpovídající hodnotu hash balíčku SHA-256:

---
**Spojené státy americké:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Evropským**

KeyVault-BYOK-Tools-Europe. zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**Východoasijsk**

KeyVault-BYOK-Tools-AsiaPacific. zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**Latinská Amerika:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

---
**Japonsko:**

KeyVault-BYOK-Tools-Japan. zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**Korea:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

---
**Jihoafrická republika:**

KeyVault-BYOK-Tools-SouthAfrica.zip

C41060C5C0170AAAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

---
**SPOJENÉ ARABSKÉ EMIRÁTY**

KeyVault-BYOK-Tools-UAE.zip

FADE80210B06962AA0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**Austrálie:**

KeyVault-BYOK-Tools-Australia. zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

---
[**Azure Government:** ](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**DOD – státní správa USA:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**Kanada:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

---
**Německo:**

KeyVault-BYOK-Tools-Germany. zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

---
**Německo – veřejné:**

KeyVault-BYOK-Tools-Germany-Public. zip

54534936D0A0C99C8117DB724C34A5E50FD204CFCBD75C78972B785865364A29

---
**Indie:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

---
**Francie:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Spojené království:**

KeyVault-BYOK-Tools-UnitedKingdom. zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**Švýcarsko:**

KeyVault-BYOK-Tools-Switzerland. zip

88CF8D39899E26D456D4E0BC57E5C94913ABF1D73A89013FCE3BBD9599AD2FE9

---


K ověření integrity stažené sady nástrojů BYOK z relace Azure PowerShell použijte rutinu [Get-hash](https://technet.microsoft.com/library/dn520872.aspx) .

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Sada nástrojů obsahuje:

* Balíček klíče pro výměnu klíčů (KEK), který má název začínající na **BYOK-KEK-pkg-.**
* Balíček Security World, který má název začínající na **BYOK-SecurityWorld-pkg-.**
* Skript Pythonu s názvem **verifykeypackage.py.**
* Spustitelný soubor příkazového řádku s názvem **KeyTransferRemote. exe** a přidružené knihovny DLL.
* Balíček pro C++ vizuální distribuci s názvem **VCRedist_x64. exe.**

Zkopírujte balíček na jednotku USB nebo jiné přenosné úložiště.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Krok 2: Příprava odpojené pracovní stanice

Pro tento druhý krok proveďte následující postupy na pracovní stanici, která není připojená k síti (buď k Internetu, nebo k vaší interní síti).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Krok 2,1: Příprava odpojené pracovní stanice pomocí modulu podpůrný software nCipher hardwarového nShield HSM

Nainstalujte software podpory podpůrný software nCipher do počítače se systémem Windows a potom k tomuto počítači Připojte modul HARDWAROVÉho zabezpečení podpůrný software nCipher hardwarového nShield.

Ujistěte se, že jsou nástroje podpůrný software nCipher ve vaší cestě ( **%nfast_home%\Bin**). Zadejte například následující:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Další informace najdete v uživatelské příručce, která je součástí modulu HARDWAROVÉho zabezpečení hardwarového nShield.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Krok 2,2: Instalace sady nástrojů BYOK na odpojenou pracovní stanici

Zkopírujte balíček sady nástrojů BYOK z jednotky USB nebo jiného přenosného úložiště a proveďte následující kroky:

1. Extrahujte soubory ze staženého balíčku do libovolné složky.
2. Z této složky spusťte VCRedist_x64. exe.
3. Postupujte podle pokynů pro instalaci komponent Visual C++ runtime pro Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Krok 3: Vygenerujte klíč

Pro tento třetí krok proveďte následující postupy na odpojené pracovní stanici. Aby bylo možné tento krok dokončit, musí být modul HSM v režimu inicializace. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Krok 3,1: Změnit režim HSM na I

Pokud používáte podpůrný software nCipher hardwarového nShield Edge, změňte režim: 1. Pro zvýraznění požadovaného režimu použijte tlačítko režim. 2. Během několika sekund stiskněte a držte tlačítko Vymazat po dobu několika sekund. Pokud se změní režim, indikátor LED nového režimu přestane blikat a zůstane osvětlený. Stav LED může v průběhu několika sekund blikat nepravidelně a pak je pravidelně blikat, až bude zařízení připravené. V opačném případě zařízení zůstane v aktuálním režimu, ve kterém je indikátor LED pro příslušný režim osvětlený.

### <a name="step-32-create-a-security-world"></a>Krok 3,2: Vytvoření světové bezpečnosti

Spusťte příkazový řádek a spusťte program podpůrný software nCipher New-World.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Tento program vytvoří soubor **zabezpečení World** na%NFAST_KMDATA%\local\world, který odpovídá složce Data\local správy C:\ProgramData\nCipher\Key. Pro kvorum můžete použít jiné hodnoty, ale v našem příkladu budete vyzváni k zadání tří prázdných karet a kódů PIN pro každé z nich. Všechny dvě karty pak poskytují úplný přístup k zabezpečení World. Tyto karty se stanou **sadou pro správu** nového světa zabezpečení.

> [!NOTE]
> Pokud váš modul HARDWAROVÉho zabezpečení nepodporuje novější sadu DLf3072s256mRijndael sady šifrováním Suite, můžete nahradit--cipher-suite = DLf3072s256mRijndael with--cipher-suite = DLf1024s160mRijndael.

Potom udělejte následující:

* Zálohujte soubor World. Zabezpečte a zabezpečte soubor ve světě, karty správců a jejich kódy PIN a ujistěte se, že k více než jedné kartě nemá přístup žádná jedna osoba.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Krok 3,3: Změňte režim HSM na O.

Pokud používáte podpůrný software nCipher hardwarového nShield Edge, změňte režim: 1. Pro zvýraznění požadovaného režimu použijte tlačítko režim. 2. Během několika sekund stiskněte a držte tlačítko Vymazat po dobu několika sekund. Pokud se změní režim, indikátor LED nového režimu přestane blikat a zůstane osvětlený. Stav LED může v průběhu několika sekund blikat nepravidelně a pak je pravidelně blikat, až bude zařízení připravené. V opačném případě zařízení zůstane v aktuálním režimu, ve kterém je indikátor LED pro příslušný režim osvětlený.

### <a name="step-34-validate-the-downloaded-package"></a>Krok 3,4: Ověřit stažený balíček

Tento krok je nepovinný, ale doporučuje se, abyste mohli ověřit následující:

* Klíč výměny klíčů, který je součástí sady nástrojů, byl vygenerován z originálního modulu HSM podpůrný software nCipher hardwarového nShield.
* Hodnota hash zabezpečení World, která je součástí sady nástrojů, se vygenerovala v originálním podpůrný software nCipher hardwarového nShield HSM.
* Klíč pro výměnu klíčů nelze exportovat.

> [!NOTE]
> K ověření staženého balíčku musí být modul HARDWAROVÉho zabezpečení připojený, zapnutý a musí mít bezpečnostní svět (například ten, který jste právě vytvořili).

Ověření staženého balíčku:

1. Spusťte skript verifykeypackage.py zadáním jednoho z následujících údajů v závislosti na zeměpisné oblasti nebo instanci Azure:

   * Pro Severní Amerika:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Evropa:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Pro Asie:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * Pro Latinská Amerika:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * Pro Japonsko:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * V případě Koreje:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Pro Jihoafrickou Afrika:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
   * Pro Spojené arabské emiráty:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
   * Pro Austrálii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Pro [Azure Government](https://azure.microsoft.com/features/gov/), které používají instanci USA pro státní správu Azure:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Pro státní správu USA:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Pro Kanadu:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Pro Německo:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Pro Německo Public:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Pro Indie:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * Pro Francii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * Pro Spojené království:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
   * Pro Švýcarsko:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1

     > [!TIP]
     > Software podpůrný software nCipher hardwarového nShield zahrnuje Python na adrese%NFAST_HOME%\python\bin
     >
     >
2. Potvrďte, že se zobrazí následující informace, které indikují úspěšné ověření: **Vyústit NÁSTUP**

Tento skript ověří řetěz podepisování až do kořenového klíče hardwarového nShield. Hodnota hash tohoto kořenového klíče je vložená do skriptu a její hodnota by měla být **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Tuto hodnotu můžete také potvrdit samostatně návštěvou [webu podpůrný software nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/validation).

Nyní jste připraveni vytvořit nový klíč.

### <a name="step-35-create-a-new-key"></a>Krok 3,5: Vytvořit nový klíč

Vygenerujte klíč pomocí programu podpůrný software nCipher hardwarového nShield **GenerateKey** .

Spusťte následující příkaz, který klíč vygeneruje:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Při spuštění tohoto příkazu použijte tyto pokyny:

* Parametr *Protected* musí být nastaven na hodnotu **Module**, jak je znázorněno níže. Tím se vytvoří klíč chráněný modulem. Sada nástrojů BYOK nepodporuje klíče chráněné pro OCS.
* Nahraďte hodnotu *contosokey* pro **Ident** a hodnotou řetězce. Pro minimalizaci administrativních režijních a snížení rizik chyb doporučujeme použít stejnou hodnotu pro obojí. Hodnota **Ident** musí obsahovat jenom čísla, pomlčky a malá písmena.
* V tomto příkladu je Pubexp ponecháno prázdné (výchozí), ale můžete zadat konkrétní hodnoty. Další informace najdete v [dokumentaci k podpůrný software nCipher.](https://www.ncipher.com/resources/solution-briefs/protect-sensitive-data-rest-and-use-across-premises-and-azure-based)

Tento příkaz vytvoří soubor s tokeny ve složce%NFAST_KMDATA%\local s názvem začínajícím na **key_simple_** a následovaný **Ident** , který byl zadán v příkazu. Například: **key_simple_contosokey**. Tento soubor obsahuje zašifrovaný klíč.

Zálohujte tento soubor klíče s tokeny na bezpečném místě.

> [!IMPORTANT]
> Když později svůj klíč převedete na Azure Key Vault, Microsoft nebude moct tento klíč exportovat zpátky, takže bude velmi důležité, abyste bezpečně zálohovali svůj klíč a zabezpečení World. Pokyny a osvědčené postupy pro zálohování klíče vám poskytne [podpůrný software nCipher](https://www.ncipher.com/about-us/contact-us) .
>


Teď jste připraveni přenést svůj klíč do Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>Krok 4: Příprava klíče k přenosu

V tomto čtvrtém kroku proveďte následující postupy na odpojené pracovní stanici.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Krok 4,1: Vytvoření kopie klíče s omezenými oprávněními

Otevřete nový příkazový řádek a změňte aktuální adresář na umístění, kde rozBYOKte soubor zip. Pokud chcete snížit oprávnění ke klíči, spusťte z příkazového řádku jednu z následujících možností v závislosti na zeměpisné oblasti nebo instanci Azure:

* Pro Severní Amerika:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Evropa:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Pro Asie:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* Pro Latinská Amerika:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* Pro Japonsko:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* V případě Koreje:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Pro Jihoafrickou Afrika:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
* Pro Spojené arabské emiráty:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
* Pro Austrálii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Pro [Azure Government](https://azure.microsoft.com/features/gov/), které používají instanci USA pro státní správu Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Pro státní správu USA:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Pro Kanadu:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Pro Německo:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Pro Německo Public:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Pro Indie:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Pro Francii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* Pro Spojené království:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
* Pro Švýcarsko:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1

Když spustíte tento příkaz, nahraďte *contosokey* stejnou hodnotou, kterou jste zadali v **kroku 3,5: Vytvořte nový klíč** pomocí kroku [Generovat klíč](#step-3-generate-your-key) .

Budete požádáni o připojení karet pro správu zabezpečení World.

Po dokončení příkazu se zobrazí **výsledek: Úspěch** a kopie vašeho klíče s omezenými oprávněními jsou v souboru s názvem key_xferacId_\<contosokey >.

Seznamy řízení přístupu (ACL) můžete zkontrolovat pomocí následujících příkazů pomocí nástrojů podpůrný software nCipher hardwarového nShield:

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Když spustíte tyto příkazy, nahraďte contosokey stejnou hodnotou, kterou jste zadali v **kroku 3,5: Vytvořte nový klíč** pomocí kroku [Generovat klíč](#step-3-generate-your-key) .

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Krok 4,2: Šifrování klíče pomocí klíče Microsoft Key Exchange

V závislosti na zeměpisné oblasti nebo instanci Azure spusťte jeden z následujících příkazů:

* Pro Severní Amerika:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Evropa:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Asie:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Latinská Amerika:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Japonsko:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* V případě Koreje:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Jihoafrickou Afrika:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Spojené arabské emiráty:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Austrálii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro [Azure Government](https://azure.microsoft.com/features/gov/), které používají instanci USA pro státní správu Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro státní správu USA:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Kanadu:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Německo:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Německo Public:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Indie:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Francii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Spojené království:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Švýcarsko:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


Při spuštění tohoto příkazu použijte tyto pokyny:

* Nahraďte *contosokey* identifikátorem, který jste použili k vygenerování klíče **v kroku 3,5: Vytvořte nový klíč** pomocí kroku [Generovat klíč](#step-3-generate-your-key) .
* Položku *SubscriptionId* NAHRAĎte ID předplatného Azure, které obsahuje váš Trezor klíčů. Tuto hodnotu jste dříve získali v **kroku 1,2: Získejte ID** vašeho předplatného Azure z kroku [Příprava pracovní stanice připojené k Internetu](#step-1-prepare-your-internet-connected-workstation) .
* Nahraďte *ContosoFirstHSMKey* názvem, který se používá pro název výstupního souboru.

Po úspěšném dokončení se zobrazí **výsledek: Úspěch** a v aktuální složce je nový soubor s následujícím názvem: KeyTransferPackage-*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Krok 4,3: Zkopírování balíčku pro přenos klíčů na pracovní stanici připojené k Internetu

K zkopírování výstupního souboru z předchozího kroku (KeyTransferPackage-ContosoFirstHSMkey. BYOK) do pracovní stanice připojené k Internetu použijte jednotku USB nebo jiné přenosné úložiště.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Krok 5: Přeneste svůj klíč do Azure Key Vault

V tomto posledním kroku nahrajte na pracovní stanici připojené k Internetu pomocí rutiny [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) , kterou jste zkopírovali z odpojené pracovní stanice do modulu hardwarového zabezpečení Azure Key Vault:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Pokud je nahrávání úspěšné, zobrazí se zobrazené vlastnosti klíče, který jste právě přidali.

## <a name="next-steps"></a>Další kroky

Tento klíč chráněný HSM teď můžete použít ve vašem trezoru klíčů. Další informace najdete v této ceně a [porovnání](https://azure.microsoft.com/pricing/details/key-vault/)funkcí.
