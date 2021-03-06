---
title: Vyhledání virtuálních počítačů VMware pomocí posouzení serveru Azure Migrate
description: Naučte se zjišťovat místní virtuální počítače VMware pomocí nástroje Azure Migrate Server Assessment.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 9/14/2020
ms.custom: mvc
ms.openlocfilehash: 0e06d82c30743a4084cfc5ff856b4a9c8d548146
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98566937"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>Kurz: Vyhledání virtuálních počítačů VMware pomocí posouzení serveru

Jako součást cesty migrace do Azure zjistíte místní inventář a zatížení.

V tomto kurzu se dozvíte, jak zjistit místní virtuální počítače VMware pomocí nástroje Azure Migrate: Server Assessment Tool pomocí jednoduchého zařízení Azure Migrate. Zařízení nasadíte jako virtuální počítač VMware, abyste průběžně zjistili virtuální počítače a jejich metadata o výkonu, aplikace běžící na virtuálních počítačích a závislosti virtuálních počítačů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte účet Azure.
> * Připravte prostředí VMware pro zjišťování.
> * Jak vytvořit projekt Azure Migrate.
> * Nastavte zařízení Azure Migrate.
> * Spusťte nepřetržité zjišťování.

> [!NOTE]
> Kurzy ukazují nejrychlejší cestu k vyzkoušení scénáře a používají výchozí možnosti, pokud je to možné.  

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, Projděte si tyto požadavky.


**Požadavek** | **Podrobnosti**
--- | ---
**Hostitel vCenter Server/ESXi** | Potřebujete vCenter Server, na kterém běží verze 5,5, 6,0, 6,5 nebo 6,7.<br/><br/> Virtuální počítače musí být hostované na hostiteli ESXi, na kterém běží verze 5,5 nebo novější.<br/><br/> Na vCenter Server povolte příchozí připojení na portu TCP 443, aby zařízení mohlo shromažďovat metadata o konfiguraci a výkonu.<br/><br/> Ve výchozím nastavení se zařízení připojuje ke vCenter na portu 443. Pokud vCenter Server naslouchá na jiném portu, můžete port změnit při zadání podrobností vCenter Server v nástroji Configuration Manager pro zařízení.<br/><br/> Na serveru ESXi, který je hostitelem virtuálních počítačů, zajistěte, aby byl na portu TCP 443 povolen příchozí přístup pro zjišťování aplikací nainstalovaných na virtuálních počítačích a závislostech virtuálních počítačů.
**Náplně** | vCenter Server potřebuje prostředky pro přidělení virtuálního počítače pro Azure Migrate zařízení:<br/><br/> -32 GB paměti RAM, 8 vCPU a asi 80 GB diskového úložiště.<br/><br/> – Externí virtuální přepínač a přístup k Internetu na virtuálním počítači zařízení, přímo nebo prostřednictvím proxy serveru.
**Virtuální počítače** | Všechny verze operačních systémů Windows a Linux jsou podporovány pro zjišťování konfigurace a metadat výkonu a také pro zjišťování aplikací nainstalovaných na virtuálních počítačích. <br/><br/> [Zde](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) najdete verzi operačního systému podporovanou pro analýzu závislostí bez agentů.<br/><br/> Aby bylo možné zjistit nainstalované aplikace a závislosti virtuálních počítačů, je nutné nainstalovat a spustit nástroje VMware (novější než 10.2.0) na virtuálních počítačích a na virtuálních počítačích s Windows musí být nainstalovaný PowerShell verze 2,0 nebo novější.


## <a name="prepare-an-azure-user-account"></a>Příprava uživatelského účtu Azure

Chcete-li vytvořit projekt Azure Migrate a zaregistrovat Azure Migrate zařízení, budete potřebovat účet s tímto:
- Oprávnění přispěvatele nebo vlastníka v předplatném Azure
- Oprávnění k registraci aplikací Azure Active Directory (AAD)
- Vlastník nebo přispěvatel plus oprávnění správce přístupu k předplatnému Azure k vytvoření Key Vault používaného během migrace VMware bez agenta

Pokud jste si právě vytvořili bezplatný účet Azure, jste vlastníkem vašeho předplatného. Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte oprávnění následujícím způsobem:

1. V Azure Portal vyhledejte "předplatná" a v části **služby** vyberte **předplatná**.

    ![Vyhledávací pole pro hledání předplatného Azure](./media/tutorial-discover-vmware/search-subscription.png)

2. Na stránce **předplatná** vyberte předplatné, ve kterém chcete vytvořit projekt Azure Migrate. 
3. V předplatném vyberte **řízení přístupu (IAM)**  >  **Kontrola přístupu**.
4. V části **kontrolovat přístup** vyhledejte příslušný uživatelský účet.
5. V nabídce **Přidat přiřazení role** klikněte na **Přidat**.

    ![Vyhledejte uživatelský účet pro kontrolu přístupu a přiřazení role.](./media/tutorial-discover-vmware/azure-account-access.png)

6. V části **Přidat přiřazení role** vyberte roli přispěvatel nebo vlastník a v našem příkladu vyberte účet (azmigrateuser). Potom klikněte na **Uložit**.

    ![Otevře stránku přidat přiřazení role, která účtu přiřadí roli.](./media/tutorial-discover-vmware/assign-role.png)

1. Aby bylo možné zařízení zaregistrovat, váš účet Azure potřebuje **oprávnění k registraci aplikací AAD.**
1. V Azure Portal přejděte na   >    >  **uživatelské nastavení** Azure Active Directory uživatelé.
1. V **nastavení uživatele** ověřte, že uživatelé Azure AD můžou registrovat aplikace (ve výchozím nastavení nastavené na **Ano** ).

    ![Ověřte v uživatelských nastaveních, která můžou uživatelé registrovat v aplikacích Active Directory.](./media/tutorial-discover-vmware/register-apps.png)

9. Pokud je nastavení ' Registrace aplikací ' nastaveno na hodnotu ' ne ', požádejte tenanta/globálního správce, aby přiřadil požadované oprávnění. Alternativně může tenant nebo globální správce přiřadit roli **vývojář aplikace** k účtu, aby umožnil registraci aplikace AAD. [Přečtěte si další informace](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>Příprava VMware

V vCenter Server ověřte, že váš účet má oprávnění k vytvoření virtuálního počítače pomocí souboru sady vajíček. To je potřeba, když nasadíte Azure Migrate zařízení jako virtuální počítač VMware pomocí souboru sady vajíček.

Pro účely zjišťování a hodnocení virtuálních počítačů VMware potřebuje vyhodnocování serveru vCenter Server účet jen pro čtení. Pokud chcete zjistit také nainstalované aplikace a závislosti virtuálních počítačů, účet potřebuje oprávnění povolená pro **Virtual Machines > operace hostů**.

### <a name="create-an-account-to-access-vcenter"></a>Vytvoření účtu pro přístup k serveru vCenter

V vSphere webovém klientovi nastavte účet následujícím způsobem:

1. Pomocí účtu s oprávněními správce ve webovém klientovi vSphere > vyberte možnost **Správa**.
2. **Přístup**, vyberte možnost **Uživatelé a skupiny jednotného přihlašování**.
3. V **Uživatelé** přidejte nového uživatele.
4. Do pole **Nový uživatel** zadejte podrobnosti o účtu. Pak klikněte na **OK**.
5. V části **globální oprávnění** vyberte uživatelský účet a přiřaďte k účtu roli jen **pro čtení** . Pak klikněte na **OK**.
6. Pokud chcete zjistit také nainstalované aplikace a závislosti virtuálních počítačů, klikněte na **role** > vyberte roli jen **pro čtení** a v části **oprávnění** vyberte **operace hostů**. Oprávnění můžete rozšířit na všechny objekty pod vCenter Server tím, že vyberete zaškrtávací políčko šířit do podřízených.
 
    ![Zaškrtnutím políčka povolíte operace hostů u role jen pro čtení.](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>Vytvoření účtu pro přístup k virtuálním počítačům

Abyste zjistili nainstalované aplikace a závislosti virtuálních počítačů, potřebujete uživatelský účet s potřebnými oprávněními na virtuálních počítačích. Uživatelský účet můžete zadat v nástroji Configuration Manager pro zařízení. Zařízení neinstaluje žádné agenty na virtuální počítače.

1. Pro virtuální počítače s Windows vytvořte účet (místní nebo doména) s oprávněními správce na virtuálních počítačích.
2. Pro virtuální počítače se systémem Linux vytvořte účet s oprávněními root. Alternativně můžete vytvořit účet s těmito oprávněními pro soubory/bin/netstat a/bin/ls: CAP_DAC_READ_SEARCH a CAP_SYS_PTRACE.

> [!NOTE]
> V současné době Azure Migrate podporuje jeden uživatelský účet pro virtuální počítače s Windows a jeden uživatelský účet pro virtuální počítače se systémem Linux, které je možné poskytnout na zařízení pro zjišťování nainstalovaných aplikací a závislostí virtuálních počítačů.


## <a name="set-up-a-project"></a>Nastavení projektu

Nastavte nový projekt Azure Migrate.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.
3. V **přehledu** vyberte **vytvořit projekt**.
5. V nástroji **vytvořit projekt** vyberte své předplatné Azure a skupinu prostředků. Vytvořte skupinu prostředků, pokud ji nemáte.
6. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt. Projděte si podporované geografické oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pole pro název a oblast projektu](./media/tutorial-discover-vmware/new-project.png)

7. Vyberte **Vytvořit**.
8. Počkejte několik minut, než se projekt Azure Migrate nasadí. **Azure Migrate: Nástroj pro vyhodnocení serveru** se ve výchozím nastavení přidá do nového projektu.

![Stránka zobrazující Nástroj pro vyhodnocení serveru přidaný ve výchozím nastavení](./media/tutorial-discover-vmware/added-tool.png)

> [!NOTE]
> Pokud jste již vytvořili projekt, můžete použít stejný projekt k registraci dalších zařízení ke zjišťování a vyhodnocení více virtuálních počítačů. další[informace](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Nastavení zařízení

Azure Migrate: posouzení serveru používá odlehčené Azure Migrate zařízení. Zařízení provádí zjišťování virtuálních počítačů a odesílá metadata o konfiguraci a výkonu virtuálních počítačů do Azure Migrate. Zařízení je možné nastavit nasazením šablony vajíček, kterou je možné stáhnout z Azure Migrate projektu.

> [!NOTE]
> Pokud z nějakého důvodu nemůžete zařízení nastavit pomocí šablony, můžete ho nastavit pomocí skriptu PowerShellu na existujícím serveru Windows Server 2016. [Přečtěte si další informace](deploy-appliance-script.md#set-up-the-appliance-for-vmware).


### <a name="deploy-with-ova"></a>Nasazení pomocí vajíček

K nastavení zařízení pomocí šablony vajíček:
1. Zadejte název zařízení a vygenerujte Azure Migrate klíč projektu na portálu.
1. Stáhněte soubor šablony vajíček a naimportujte ho do vCenter Server. Ověřte, jestli je VAJÍČKa zabezpečená.
1. Vytvořte zařízení a ověřte, že se může připojit k Azure Migrate posouzení serveru.
1. Nakonfigurujte zařízení poprvé a zaregistrujte ho pomocí Azure Migrate projektu pomocí klíče Azure Migrate projektu.

### <a name="1-generate-the-azure-migrate-project-key"></a>1. vygenerujte klíč projektu Azure Migrate.

1. V části **Cíle migrace** > **Servery** > **Azure Migrate: Hodnocení serverů** vyberte **Zjistit**.
2. V možnosti **zjišťovat počítače**  >  **jsou vaše počítače virtualizované?** vyberte **Ano, pomocí VMware vSphere hypervisor**.
3. V **1: vygenerujte Azure Migrate klíč projektu**, zadejte název Azure Migrate zařízení, které nastavíte pro zjišťování virtuálních počítačů VMware. název by měl být alfanumerický a musí obsahovat maximálně 14 znaků.
1. Kliknutím na **vygenerovat klíč** spustíte vytváření požadovaných prostředků Azure. Během vytváření prostředků prosím Nezavírejte stránku zjišťovacích počítačů.
1. Po úspěšném vytvoření prostředků Azure se vygeneruje **klíč projektu Azure Migrate** .
1. Zkopírujte klíč, protože ho budete potřebovat k dokončení registrace zařízení během jeho konfigurace.

### <a name="2-download-the-ova-template"></a>2. Stáhněte si šablonu pro VAJÍČKu

V **2: Stáhněte zařízení Azure Migrate** vyberte. Soubor vajíček a klikněte na **Stáhnout**.

### <a name="verify-security"></a>Ověřit zabezpečení

Před nasazením ověřte, zda je soubor sady vajíček zabezpečený:

1. Na počítači, do kterého jste soubor stáhli, otevřete jako správce příkazový řádek.
2. Spusťte následující příkaz, který vygeneruje hodnotu hash pro soubor sady vajíček:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Příklady použití: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Ověřte nejnovější verze zařízení a hodnoty hash:

    - Pro veřejný cloud Azure:
    
        **Algoritmus** | **Stáhnout** | **SHA256**
        --- | --- | ---
        VMware (11,9 GB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Pro Azure Government:
    
        **Algoritmus** | **Stáhnout** | **SHA256**
        --- | --- | ---
        VMware (85,8 MB) | [Nejnovější verze](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca

### <a name="3-create-the-appliance-vm"></a>3. Vytvoření virtuálního počítače zařízení

Naimportujte stažený soubor a vytvořte virtuální počítač.

1. V klientské konzole vSphere klikněte na **soubor**  >  **nasadit šablonu OVF**.
2. V Průvodci nasazením šablony OVF > **zdroj** zadejte umístění souboru vajíček.
3. Do pole **název** a **umístění** zadejte popisný název virtuálního počítače. Vyberte objekt inventáře, do kterého bude virtuální počítač hostovat.
5. V části **hostitel nebo cluster** zadejte hostitele nebo cluster, na kterém se virtuální počítač spustí.
6. V části **Storage (úložiště**) zadejte cíl úložiště pro virtuální počítač.
7. V části **Disk Format** (Formát disku) zadejte typ a velikost disku.
8. V části **mapování sítě** určete síť, ke které se bude virtuální počítač připojovat. Síť potřebuje připojení k Internetu, aby odesílala metadata Azure Migrate posouzení serveru.
9. Zkontrolujte a ověřte všechna nastavení a pak klikněte na **Finish** (Dokončit).


### <a name="verify-appliance-access-to-azure"></a>Ověření přístupu zařízení k Azure

Ujistěte se, že se virtuální počítač zařízení může připojit k adresám URL Azure pro cloudy [veřejné](migrate-appliance.md#public-cloud-urls) a [státní správy](migrate-appliance.md#government-cloud-urls) .


### <a name="4-configure-the-appliance"></a>4. konfigurace zařízení

Nastavte zařízení poprvé.

> [!NOTE]
> Pokud zařízení nastavíte pomocí [skriptu PowerShellu](deploy-appliance-script.md) namísto stažené sady vajíček, první dva kroky v tomto postupu nejsou relevantní.

1. V klientské konzole vSphere klikněte pravým tlačítkem myši na virtuální počítač a vyberte možnost **otevřít konzolu**.
2. Zadejte jazyk, časové pásmo a heslo pro zařízení.
3. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k VIRTUÁLNÍmu počítači, a otevřete adresu URL webové aplikace zařízení: ***název zařízení https://nebo IP adresa*: 44368**.

   Alternativně můžete aplikaci otevřít z plochy zařízení tak, že vyberete zástupce aplikace.
1. Přijměte **licenční podmínky** a přečtěte si informace třetích stran.
1. Ve webové aplikaci > **nastavení požadavků** postupujte takto:
   - **Připojení**: aplikace kontroluje, jestli má virtuální počítač přístup k Internetu. Pokud virtuální počítač používá proxy server:
     - Klikněte na **nastavit proxy server** a zadejte adresu proxy serveru (ve formuláři http://ProxyIPAddress nebo http://ProxyFQDN) portu pro naslouchání.
     - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
     - Podporuje se jen proxy protokolu HTTP.
     - Pokud jste přidali podrobnosti proxy serveru nebo zakážete proxy server nebo ověřování, kliknutím na **Uložit** spusťte kontrolu připojení znovu.
   - **Čas synchronizace**: čas v zařízení by měl být synchronizovaný s internetovým časem, aby zjišťování fungovalo správně.
   - **Nainstalovat aktualizace**: zařízení zajišťuje, že jsou nainstalované nejnovější aktualizace. Po dokončení kontroly můžete kliknout na **Zobrazit služby zařízení** a zobrazit stav a verze komponent spuštěných na zařízení.
   - **Instalace VDDK**: zařízení kontroluje, jestli je nainstalovaná VMware vSphere Virtual disk Development Kit (VDDK). Pokud není nainstalovaný, Stáhněte si z VMware VDDK 6,7 a Extrahujte stažený obsah souboru zip do zadaného umístění na zařízení, jak je uvedeno v **pokynech k instalaci**.

     Migrace Azure Migrate serveru používá VDDK k replikaci počítačů během migrace do Azure. 
1. Pokud chcete, můžete kdykoli **znovu spustit požadované součásti** , abyste zkontrolovali, jestli zařízení splňuje všechny požadavky.

### <a name="register-the-appliance-with-azure-migrate"></a>Zaregistrovat zařízení ve Azure Migrate

1. Vložte **klíč projektu Azure Migrate** zkopírovaný z portálu. Pokud tento klíč nemáte, Projděte si část **vyhodnocení serveru> zjistit> spravovat existující zařízení**, vyberte název zařízení, který jste zadali v době generování klíče, a zkopírujte odpovídající klíč.
1. K ověření pomocí Azure budete potřebovat kód zařízení. Kliknutím na **přihlášení** se otevře modální okno s kódem zařízení, jak je znázorněno níže.

    ![Modální zobrazení kódu zařízení](./media/tutorial-discover-vmware/device-code.png)

1. Kliknutím na **zkopírovat kód & přihlášením** zkopírujte kód zařízení a otevřete výzvu k přihlášení Azure na nové kartě prohlížeče. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.
1. Na kartě nový vložte kód zařízení a přihlaste se pomocí uživatelského jména a hesla Azure.
   
   Přihlášení pomocí PIN kódu se nepodporuje.
3. Pokud kartu přihlášení omylem zavřete bez přihlašování, budete muset aktualizovat kartu prohlížeče Configuration Manageru a povolit tak tlačítko pro přihlášení znovu.
1. Po úspěšném přihlášení se vraťte na předchozí kartu pomocí Správce konfigurace zařízení.
1. Pokud má uživatelský účet Azure použitý k protokolování správná oprávnění k prostředkům Azure vytvořeným během generování klíče, zahájí se registrace zařízení.
1. Po úspěšné registraci zařízení si můžete zobrazit podrobnosti o registraci kliknutím na **Zobrazit podrobnosti**.



## <a name="start-continuous-discovery"></a>Spustit průběžné zjišťování

Aby bylo možné zjistit konfiguraci a údaje o výkonu virtuálních počítačů, musí se zařízení připojit k vCenter Server.

1. V **kroku 1: zadejte vCenter Server přihlašovací údaje**, klikněte na **Přidat přihlašovací údaje** , abyste zadali popisný název pro přihlašovací údaje, přidejte **uživatelské jméno** a **heslo** pro účet vCenter Server, který zařízení použije ke zjištění virtuálních počítačů v instanci služby vCenter Server.
    - V předchozím kurzu byste měli mít nastavený účet s požadovanými oprávněními.
    - Chcete-li omezit obor zjišťování na konkrétní objekty VMware (vCenter Server datových center, clustery, složku clusterů, hostitele, složku hostitelů nebo jednotlivé virtuální počítače), přečtěte si pokyny v [tomto článku](set-discovery-scope.md) a omezte účet používaný v Azure Migrate.
1. V **kroku 2: zadejte vCenter Server podrobnosti**, klikněte na **Přidat zdroj zjišťování** a vyberte popisný název z rozevíracího seznamu, zadejte **IP adresu nebo plně kvalifikovaný název domény** instance vCenter Server. **Port** můžete ponechat výchozí (443) nebo zadejte vlastní port, na kterém vCenter Server naslouchá, a klikněte na **Uložit**.
1. Po kliknutí na Uložit se zařízení pokusí ověřit připojení k vCenter Server s poskytnutými přihlašovacími údaji a zobrazit **stav ověření** v tabulce proti vCenter Server IP adrese nebo plně kvalifikovanému názvu domény.
1. Můžete znovu **ověřit** připojení k vCenter Server kdykoli před zahájením zjišťování.
1. V **kroku 3: zadání přihlašovacích údajů k virtuálnímu počítači pro zjišťování nainstalovaných aplikací a provádění mapování závislostí bez agenta**, klikněte na **Přidat přihlašovací údaje** a zadejte operační systém, pro který se přihlašovací údaje poskytují, popisný název přihlašovacích údajů a **uživatelské jméno** a **heslo**. Pak klikněte na **Uložit**.

    - Přihlašovací údaje můžete volitelně přidat tady, pokud jste vytvořili účet, který se má použít pro [zjišťování aplikací](how-to-discover-applications.md), nebo pro [analýzu závislostí bez agenta](how-to-create-group-machine-dependencies-agentless.md).
    - Pokud tyto funkce nechcete používat, můžete tento krok přeskočit kliknutím na posuvník. Záměr můžete kdykoli vrátit později.
    - Zkontrolujte oprávnění potřebná pro účet pro [zjišťování aplikací](migrate-support-matrix-vmware.md#application-discovery-requirements)nebo pro [analýzu závislostí bez agentů](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. Kliknutím na **Spustit zjišťování Vyhajte** zjišťování virtuálních počítačů. Po úspěšném zahájení zjišťování můžete v tabulce zjistit stav zjišťování oproti vCenter Server IP adrese nebo plně kvalifikovanému názvu domény.

Zjišťování funguje následujícím způsobem:
- Zobrazení zjištěných metadat virtuálního počítače na portálu trvá přibližně 15 minut.
- Zjišťování nainstalovaných aplikací, rolí a funkcí nějakou dobu trvá. Doba trvání závisí na počtu zjištěných virtuálních počítačů. Pro virtuální počítače 500 trvá na portálu Azure Migrate přibližně jednu hodinu, než se inventář aplikace zobrazí.
- Po dokončení zjišťování virtuálních počítačů můžete na portálu povolit analýzu závislostí bez agentů na požadovaných virtuálních počítačích.


## <a name="next-steps"></a>Další kroky

- [Vyhodnoťte virtuální počítače VMware](./tutorial-assess-vmware-azure-vm.md) pro migraci na virtuální počítače Azure.
- [Zkontrolujte data](migrate-appliance.md#collected-data---vmware) , která zařízení shromažďuje během zjišťování.