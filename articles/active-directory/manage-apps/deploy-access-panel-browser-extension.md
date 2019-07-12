---
title: Nasazení rozšíření přístupového panelu Azure pro Internet Exploreru pomocí objektu zásad skupiny | Dokumentace Microsoftu
description: Jak používat zásady skupiny nasadit doplněk aplikace Internet Explorer pro Moje aplikace z portálu.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71c342ede77349b3f6c22093e5877ad5f5ce6549
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807685"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Postup: Nasazení rozšíření přístupový Panel pro aplikaci Internet Explorer pomocí zásad skupiny

Tento kurz ukazuje, jak používat zásady skupiny k provedení vzdálené instalace rozšíření přístupový Panel pro aplikaci Internet Explorer v počítačích uživatelů. Toto rozšíření, je třeba Internet Explorer, kteří se muset přihlásit do aplikace, které jsou nakonfigurované pomocí [založené na heslech jednotného přihlašování](what-is-single-sign-on.md#password-based-sso).

Doporučuje se, že správci automatizovat nasazení tohoto rozšíření. V opačném případě uživatelé muset stáhnout a nainstalovat rozšíření, které jsou náchylné k chybám uživatelů a vyžaduje oprávnění správce. Tento kurz se zaměřuje na jednu z metod automatizace nasazení softwaru pomocí zásad skupiny. [Další informace o zásadách skupiny.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Rozšíření přístupový Panel je také k dispozici pro [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) a [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), ani jedno z nich vyžadují oprávnění správce k instalaci.

## <a name="prerequisites"></a>Požadavky

* Nastavíte [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), a vaši uživatelé počítačů se připojili k vaší doméně.
* Musíte mít oprávnění "Upravit nastavení" k úpravě objektu zásad skupiny (GPO). Ve výchozím nastavení mají toto oprávnění členy z těchto skupin zabezpečení: Domain Administrators, Enterprise Administrators a Group Policy Creator Owners. [Další informace](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Krok 1: Vytvoření distribučního bodu

Nejprve je nutné umístit je balíček Instalační služby v umístění v síti, který je přístupný počítačům, které chcete vzdáleně nainstalovat rozšíření. Postupujte přitom takto:

1. Přihlaste se k serveru jako správce.
1. V **správce serveru** okno, přejděte na **soubory a služby úložiště**.

    ![Otevřené soubory a služby úložiště](./media/deploy-access-panel-browser-extension/files-services.png)

1. Přejděte **sdílené složky** kartu. Pak klikněte na tlačítko **úlohy** > **novou sdílenou složku...**

    ![Snímek obrazovky ukazuje, kde se mají hledat novou sdílenou složku z obrazovky úlohy](./media/deploy-access-panel-browser-extension/shares.png)

1. Dokončení **Průvodce vytvořením nové sdílené složky** a nastavit oprávnění a ujistěte se, že byla přístupná z vašich uživatelů počítačů. [Další informace o sdílených složek.](https://technet.microsoft.com/library/cc753175.aspx)
1. Stáhněte následující balíček Instalační služby systému Windows (soubor .msi): [Access Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. Zkopírujte instalační balíček do požadovaného umístění ve sdílené složce.

    ![Zkopírujte tento soubor .msi do sdílené složky](./media/deploy-access-panel-browser-extension/copy-package.png)

1. Ověřte, že se klientské počítače přístup k balíčku instalačního programu ze sdílené složky.

## <a name="step-2-create-the-group-policy-object"></a>Krok 2: Vytvoření objektu zásad skupiny

1. Přihlaste se k serveru, který je hostitelem vaší instalace služby Active Directory Domain Services (AD DS).
1. Ve Správci serveru přejděte na **nástroje** > **Správa zásad skupiny**.

    ![Přejděte na Nástroje > Správa zásad skupiny](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. V levém podokně **Správa zásad skupiny** okně zobrazit vaši hierarchii organizační jednotky (OU) a určit v oboru, který chcete použít zásady skupiny. Například můžete rozhodnout pro výběr malých organizační jednotky nasadíte několik uživatelů pro účely testování nebo vyberete organizační jednotka nejvyšší úrovně pro nasazení pro celou organizaci.

   > [!NOTE]
   > Pokud chcete vytvořit nebo upravit vaše organizační jednotky (OU), přepněte zpět do správce serveru a přejděte na **nástroje** > **Active Directory Users and Computers**.

1. Jakmile vyberete organizační jednotku, pravým tlačítkem myši a vyberte **vytvořit objekt zásad skupiny v této doméně a propojit jej sem...**

    ![Snímek obrazovky ukazuje vytvoření nové možnosti objektu zásad skupiny](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. V **nový objekt zásad skupiny** řádku, zadejte název pro nový objekt zásad skupiny.
1. Klikněte pravým tlačítkem na objekt zásad skupiny, který jste vytvořili a vyberte **upravit**.

## <a name="step-3-assign-the-installation-package"></a>Krok 3: Přiřadit instalační balíček

1. Určete, jestli byste chtěli nasadit rozšíření na základě **konfigurace počítače** nebo **konfigurace uživatele**. Při použití [konfigurace počítače](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), rozšíření je nainstalované v počítači bez ohledu na to, které se přihlašují uživatelé k ho. S [konfigurace uživatele](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), uživatelé mají nainstalované pro ně bez ohledu na počítače, které se přihlašují k rozšíření.
1. V levém podokně **Editor správy zásad skupiny** okno, přejděte na jednu z následujících cest složku, v závislosti na tom, jaký typ konfigurace jste zvolili:

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`

1. Klikněte pravým tlačítkem na **instalace softwaru**a pak vyberte **nový** > **balíčku...**
1. Přejděte do sdílené složky, která obsahuje balíček instalačního programu z [krok 1: Vytvoření distribučního bodu](#step-1-create-the-distribution-point), vyberte soubor MSI a klikněte na tlačítko **otevřít**.

   > [!IMPORTANT]
   > Pokud je na tomto serveru stejné sdílené složce, ověřte, že při přístupu k souboru MSI přes síťovou cestu souboru, nikoli cestu k místnímu souboru.

    ![Vyberte instalační balíček ze sdílené složky](./media/deploy-access-panel-browser-extension/select-package.png)

1. V **nasazením softwaru** příkazový řádek, vyberte **přiřazeno** pro metodu nasazení. Pak klikněte na **OK**.

Rozšíření je momentálně nasazené do organizační jednotky, kterou jste vybrali. [Další informace o instalací softwaru zásad skupiny.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Krok 4: Auto povolit rozšíření pro aplikaci Internet Explorer

Kromě spuštění instalačního programu, každý pro aplikaci Internet Explorer musí být explicitně povoleno rozšíření předtím, než je možné. Postupem uvedeným níže povolte rozšíření přístupového panelu pomocí zásad skupiny:

1. V **Editor správy zásad skupiny** okno, přejděte na jednu z následujících cest, v závislosti na tom, jaký typ konfigurace, který jste zvolili v [krok 3: Přiřadit instalačního balíčku](#step-3-assign-the-installation-package):

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

1. Klikněte pravým tlačítkem na **seznam doplňků**a vyberte **upravit**.

    ![Klikněte pravým tlačítkem myši klikněte na tlačítko "Doplněk seznam" a vyberte "Edit"](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. V **seznam doplňků** okně **povoleno**. Potom v části **možnosti** klikněte na tlačítko **zobrazení...** .

    ![Klikněte na tlačítko Povolit a pak klikněte na zobrazit...](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. V **zobrazit obsah** okno, proveďte následující kroky:

   1. První sloupec ( **název hodnoty** pole), zkopírujte a vložte následující ID třídy: `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   1. Pro druhý sloupec ( **hodnotu** pole), zadejte následující hodnotu: `1`
   1. Klikněte na tlačítko **OK** zavřete **zobrazit obsah** okno.

      ![Vyplňte následující hodnoty, jak je uvedeno v předchozím kroku](./media/deploy-access-panel-browser-extension/show-contents.png)

1. Klikněte na tlačítko **OK** použít změny a zavřete **seznam doplňků** okna.

Rozšíření by měl nyní možné pro počítače ve vybrané organizační jednotce. [Další informace o použití zásad skupiny k povolení nebo zakázání doplňky aplikace Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Krok 5 (volitelné): Zakáže "Zapamatovat heslo"

Při přihlášení k webům pomocí rozšíření přístupového panelu, Internet Explorer může zobrazit následující výzva s dotazem, "Chcete uložit heslo?"

![Ukazuje, "Přejete si uložit hesla..." řádek](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Pokud budete chtít zabráníte uživatelům zobrazovat tuto výzvu, postupujte podle pokynů níže, aby se zabránilo automatické dokončování z pamatovat si hesla:

1. V **Editor správy zásad skupiny** okno, přejděte k cestě uvedené níže. Toto nastavení konfigurace je k dispozici v rámci pouze **konfigurace uživatele**.

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. Najít nastavení s názvem **zapnout funkci automatického dokončování pro uživatelská jména a hesla ve formulářích**.

   > [!NOTE]
   > Předchozí verze služby Active Directory výpisu toto nastavení s názvem **zakázat automatické dokončování pro ukládání hesel**. Konfigurace tohoto nastavení se liší od nastavení popsané v tomto kurzu.

    ![Mějte na paměti Toto hledání v části uživatelské nastavení](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. Klikněte pravým tlačítkem myši klikněte na následující nastavení a vyberte **upravit**.
1. V okně s názvem **zapnout funkci automatického dokončování pro uživatelská jména a hesla ve formulářích**vyberte **zakázané**.

    ![Vyberte možnost "Zakázáno" pro zapnutí funkce automatického dokončování](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. Klikněte na tlačítko **OK** chcete tyto změny použít a zavřít okno.

Uživatelé už nebudou moci uložit své přihlašovací údaje nebo použití automatického dokončování pro přístup k dříve uložené přihlašovací údaje. Ale tyto zásady umožňují uživatelům i nadále používat automatické dokončování pro ostatní typy polí formuláře, jako je například vyhledávacích polí.

> [!WARNING]
> Pokud po uživatelům se rozhodli ukládat některé přihlašovací údaje, bude tato zásada se povolí tyto zásady *není* vymazat přihlašovací údaje, které již byly uloženy.

## <a name="step-6-testing-the-deployment"></a>Krok 6: Testování nasazení

Postupujte podle kroků níže. Tím ověříte, jestli rozšíření nasazení byla úspěšná:

1. Pokud jste nasadili pomocí **konfigurace počítače**, přihlaste se k klientský počítač, který patří do organizační jednotky, kterou jste vybrali v [krok 2: Vytvoření objektu zásad skupiny](#step-2-create-the-group-policy-object). Pokud jste nasadili pomocí **konfigurace uživatele**, ujistěte se, že se přihlásit jako uživatel, který patří do této organizační jednotce.
1. Může trvat několik přihlášení pro změny zásad skupiny pro úplnou aktualizaci se tento počítač. Chcete-li vynutit aktualizaci, otevřete **příkazového řádku** okna a spusťte následující příkaz: `gpupdate /force`
1. Po restartování počítače, aby instalace stihla proběhnout. Spouštění může trvat výrazně déle než obvykle při rozšíření nainstaluje.
1. Po restartování počítače, otevřete **aplikace Internet Explorer**. V pravém horním rohu okna, klikněte na **nástroje** (ikona ozubeného kolečka) a pak vyberte **spravovat doplňky**.
1. V **spravovat doplňky** okna, ověřte, že **rozšíření přístupového panelu** byla nainstalována a že jeho **stav** byla nastavena na **povoleno**.

   ![Ověřte, že je nainstalovaný a povolený rozšíření přístupového panelu](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>Víc se uč

* [Přístup k aplikaci a jednotné přihlašování s Azure Active Directory](what-is-single-sign-on.md)
* [Řešení potíží s rozšíření přístupového panelu pro aplikaci Internet Explorer](manage-access-panel-browser-extension.md)
