---
title: Kurz – CI/CD z Jenkinse do virtuálních počítačů Azure s Azure DevOps Services
description: V tomto kurzu se naučíte, jak nastavit kontinuální integraci (CI) a průběžné nasazování (CD) aplikace Node.js pomocí Jenkins na virtuální počítače Azure ze správy vydaných verzí v produktech Visual Studio Team Services nebo Microsoft Team Foundation Server.
author: tomarchermsft
manager: jpconnock
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: jenkins
ms.workload: infrastructure
ms.date: 07/31/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 734b0a516d4a9fe882545dd5cde5a57d8af719cb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034568"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-with-using-jenkins-and-azure-devops-services"></a>Kurz: Nasazení aplikace na linuxové virtuální počítače v Azure pomocí Jenkins a Azure DevOps Services

Kontinuální integrace (CI) a průběžné nasazování (CD) tvoří kanál, s jehož pomocí můžete sestavovat, vydávat a nasazovat kód. Azure DevOps Services poskytuje kompletní plnohodnotnou sadu nástrojů pro automatizaci CI/CD umožňující nasazení do Azure. Jenkins je populární serverový nástroj CI/CD od jiného výrobce, který rovněž nabízí automatizaci CI/CD. Kombinací produktů Azure DevOps Services a Jenkins můžete přizpůsobit způsob, jakým dodáváte cloudovou aplikaci nebo službu.

V tomto kurzu použijete Jenkins k sestavení webové aplikace Node.js. Pomocí Azure DevOps ji pak nasadíte

do [skupiny nasazení](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups/index?view=vsts), která obsahuje linuxové virtuální počítače. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Získání ukázkové aplikace
> * Konfigurace modulů plug-in Jenkins
> * Konfigurace projektu Jenkins Freestyle pro Node.js
> * Konfigurace Jenkins pro integraci Azure DevOps Services
> * Vytvoření koncového bodu služby Jenkins
> * Vytvoření skupiny nasazení pro virtuální počítače Azure
> * Vytvořte kanál verze Azure Pipelines.
> * Ruční nasazení nebo nasazení aktivované přes CI

## <a name="before-you-begin"></a>Než začnete

* Potřebujete přístup k serveru Jenkins. Pokud jste ještě nevytvořili server Jenkins, přečtěte si článek o [vytvoření hlavní databáze Jenkins ve virtuálním počítači Azure](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Přihlaste se ke své organizaci služby Azure DevOps Services (**https://{vaše organizace}.visualstudio.com**). 
  Můžete si opatřit [bezplatnou organizaci služby Azure DevOps Services](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Další informace najdete v článku o [připojení ke službě Azure DevOps Services](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts).

*  Potřebujete linuxový virtuální počítač jako cíl nasazení.  Další informace najdete v článku o [vytvoření a správě linuxových virtuálních počítačů pomocí rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Otevřete příchozí port 80 pro virtuální počítač. Další informace najdete v článku o [vytvoření skupin zabezpečení sítě pomocí Azure Portalu](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic).

## <a name="get-the-sample-app"></a>Získání ukázkové aplikace

K nasazení potřebujete nějakou aplikaci uloženou v úložišti Gitu.
V tomto kurzu doporučujeme použít [tuto ukázkovou aplikaci dostupnou na GitHubu](https://github.com/azure-devops/fabrikam-node). Tento kurz obsahuje ukázkový skript, který se používá k instalaci Node.js a aplikace. Pokud chcete pracovat se svým vlastním úložištěm, měli byste si nakonfigurovat podobnou ukázku.

Vytvořte fork této aplikace a poznačte si umístění (adresu URL), které použijete později v tomto kurzu. Další informace najdete v článku o [vytvoření forku úložiště](https://help.github.com/articles/fork-a-repo/).    

> [!NOTE]
> Tato aplikace byla vytvořena přes [Yeoman](https://yeoman.io/learning/index.html). Používá Express, Bower a Grunt. Jako závislosti také obsahuje několik balíčků npm.
> Součástí této ukázky je také skript, který nainstaluje Nginx a nasadí tuto aplikaci. Spouští se na virtuálních počítačích. Tento skript konkrétně:
> 1. Nainstaluje Node, Nginx a PM2
> 2. Nakonfiguruje Nginx a PM2
> 3. Spustí aplikaci Node

## <a name="configure-jenkins-plug-ins"></a>Konfigurace modulů plug-in Jenkins

Nejprve musíte nakonfigurovat dva moduly plug-in Jenkins: **NodeJS** a **VS Team Services Continuous Deployment**.

1. Otevřete svůj účet Jenkins a vyberte **Manage Jenkins** (Spravovat Jenkins).
2. Na stránce **Manage Jenkins** (Spravovat Jenkins) vyberte **Manage Plugins** (Spravovat moduly plug-in).
3. Filtrováním seznamu vyhledejte modul plug-in **NodeJS** a vyberte možnost **Install without restart** (Nainstalovat bez restartování).
    ![Přidání modulu plug-in NodeJS do Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Filtrováním seznamu vyhledejte modul plug-in **VS Team Services Continuous Deployment** a vyberte možnost **Install without restart** (Nainstalovat bez restartování).
5. Přejděte zpět na řídicí panel Jenkins a vyberte **Manage Jenkins** (Spravovat Jenkins).
6. Vyberte **Global Tool Configuration** (Globální konfigurace nástroje). Najděte **NodeJS** a vyberte **NodeJS installations** (Instalace NodeJS).
7. Vyberte možnost **Install automatically** (Nainstalovat automaticky) a pak zadejte hodnotu **Name** (Název).
8. Vyberte **Uložit**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Konfigurace projektu Jenkins Freestyle pro Node.js

1. Vyberte **New Item** (Nová položka). Zadejte název položky.
2. Vyberte **Freestyle project** (Projekt Freestyle). Vyberte **OK**.
3. Na kartě **Source Code Management** (Správa zdrojového kódu) vyberte **Git** a zadejte podrobnosti o úložišti a větvi, které obsahují kód vaší aplikace.    
    ![Přidání úložiště do sestavení](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. Na kartě **Build Triggers** (Aktivační události sestavení) vyberte **Poll SCM** (Cyklicky dotazovat Správce řízení služeb) a zadejte plán `H/03 * * * *`, který se bude úložiště Git dotazovat na změny každé tři minuty. 
5. Na kartě **Build Environment** (Prostředí sestavení) vyberte **Provide Node &amp; npm bin/ folder PATH** (Zadat cestu ke složce Node a npm bin/) a vyberte hodnotu **NodeJS Installation** (Instalace NodeJS). Možnost **npmrc file** (Soubor npmrc) nechejte nastavenou na **use system default** (použít výchozí nastavení systému).
6. Na kartě **Build** (Sestavení) vyberte **Execute shell** (Spustit prostředí) a zadáním příkazu `npm install` zajistěte aktualizaci všech závislostí.


## <a name="configure-jenkins-for-azure-devops-services-integration"></a>Konfigurace Jenkins pro integraci Azure DevOps Services

> [!NOTE]
> Zajistěte, aby token PAT, který použijete v následujících krocích, obsahoval ve službě Azure DevOps Services oprávnění k *vydání verze* (čtení, zápis, spuštění a správa).
 
1.  Pokud token PAT ještě nemáte, vytvořte ho ve své organizaci služby Azure DevOps Services. Jenkins vyžaduje tyto údaje pro přístup k organizaci služby Azure DevOps Services. Nezapomeňte informace tohoto tokenu uložit pro nadcházející kroky této části.
  
    Informace o vygenerován tokenu najdete v článku o [vytvoření tokenu PAT pro Azure DevOps Services](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=vsts).
2. Na kartě **Post-build Actions** (Akce po sestavení) vyberte **Add post-build action** (Přidat akci po sestavení). Vyberte **Archive the artifacts** (Archivovat artefakty).
3. Do pole **Files to archive** (Soubory k archivaci) zadejte `**/*`, čímž zahrnete všechny soubory.
4. Pokud chcete vytvořit další akci, vyberte **Add post-build action** (Přidat akci po sestavení).
5. Vyberte **Trigger release in TFS/Team Services** (Aktivovat vydanou verzi v TFS/Team Services). Zadejte identifikátor URI organizace služby Azure DevOps Services, například **https://{název vaší organizace}.visualstudio.com**.
6. Zadejte název **projektu**.
7. Zvolte název pro kanál verze. (Tento kanál verze vytvoříte ve službě Azure DevOps Services později.)
8. Zvolte přihlašovací údaje pro připojení k prostředí Azure DevOps Services nebo Team Foundation Server:
   - Pokud používáte Azure DevOps Services, nechejte **Uživatelské jméno** prázdné. 
   - Pokud používáte místní verzi Team Foundation Serveru, zadejte uživatelské jméno a heslo.    
   ![Konfigurace akcí po sestavení v Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Uložte projekt Jenkins.


## <a name="create-a-jenkins-service-endpoint"></a>Vytvoření koncového bodu služby Jenkins

Koncový bod služby umožňuje službě Azure DevOps Services připojení k Jenkins.

1. Ve službě Azure DevOps Services otevřete stránku **Služby**, otevřete seznam **Nový koncový bod služby** a vyberte **Jenkins**.
   ![Přidání koncového bodu Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Zadejte název tohoto připojení.
3. Zadejte adresu URL serveru Jenkins a vyberte možnost **Přijmout nedůvěryhodné certifikáty SSL**. Příklad adresy URL: **http://{vaše-adresa-URL-Jenkins}.westcentralus.cloudapp.azure.com**.
4. Zadejte uživatelské jméno a heslo svého účtu Jenkins.
5. Výběrem příkazu **Ověřit připojení** zkontrolujte, že jsou tyto údaje správné.
6. Výběrem tlačítka **OK** vytvořte koncový bod služby.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Vytvoření skupiny nasazení pro virtuální počítače Azure

[Skupinu nasazení](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) potřebujete k registraci agenta služby Azure DevOps Services, aby se kanál verze dal nasadit do virtuálního počítače. Skupiny nasazení usnadňují definování logických skupin cílových počítačů pro nasazení a instalaci požadovaných agentů na jednotlivé počítače.

   > [!NOTE]
   > V následujícím postupu nainstalujte všechno, co je potřeba, a *nespouštějte skript s oprávněními sudo*.

1. Otevřete kartu **Vydání** centra **Sestavení a vydání&amp;, otevřete** Skupiny nasazení**a vyberte**+ Nový **.
2. Zadejte název skupiny nasazení a volitelný popis. Potom vyberte **Vytvořit**.
3. Zvolte operační systém virtuálního počítače cíle nasazení. Vyberte například **Ubuntu 16.04+** .
4. Vyberte **Použít ve skriptu token PAT k ověřování**.
5. Vyberte odkaz **Systémové předpoklady**. Nainstalujte tyto předpoklady do operačního systému.
6. Výběrem příkazu **Zkopírovat skript do schránky** skript zkopírujte.
7. Přihlaste se k virtuálnímu počítači cíle nasazení a spusťte tento skript. Nespouštějte skript s oprávněními sudo.
8. Po instalaci budete vyzváni k zadání značek skupiny nasazení. Přijměte výchozí hodnoty.
9. Ve službě Azure DevOps Services vyhledejte v seznamu **Cíle** v oblasti **Skupiny nasazení** nově registrovaný virtuální počítač.

## <a name="create-an-azure-pipelines-release-pipeline"></a>Vytvoření kanálu pro vydání Azure Pipelines

Kanál verze určuje proces, který Azure Pipelines používá k nasazení aplikace. V tomto příkladu spustíte skript prostředí.

Kanál verze vytvoříte v Azure Pipelines takto:

1. Otevřete kartu **Vydání** centra **Sestavení a vydání&amp; a vyberte** Vytvořit kanál verze **. 
2. Zvolte, že chcete začít **prázdným procesem** a vyberte **prázdnou** šablonu.
3. V části **Artefakty** vyberte **+ Přidat artefakt** a jako **Typ zdroje** zvolte **Jenkins**. Vyberte připojení ke koncovému bodu služby Jenkins. Pak vyberte zdrojovou úlohu Jenkins a vyberte **Přidat**.
4. Vyberte tři tečky vedle **Prostředí 1**. Vyberte **Přidat fázi skupiny nasazení**.
5. Zvolte skupinu nasazení.
5. Výběrem symbolu **+** přidejte do **Fáze skupiny nasazení** úlohu.
6. Vyberte úlohu **Skript prostředí** a pak vyberte **Přidat**. Úloha **Skript prostředí** zajišťuje konfiguraci skriptu, který má na jednotlivých serverech nainstalovat Node.js a spustit aplikaci.
8. Do pole **Cesta ke skriptu** zadejte **$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh**.
9. Vyberte **Upřesnit** a povolte **Zadat pracovní adresář**.
10. Do pole **Pracovní adresář** zadejte **$(System.DefaultWorkingDirectory)/Fabrikam-Node**.
11. Název kanálu verze změňte na název, který jste pro sestavení v Jenkins zadali na kartě **Post-build Actions** (Akce po sestavení). Služba Jenkins tento název vyžaduje, aby mohla při aktualizaci zdrojových artefaktů aktivovat novou vydanou verzi.
12. Výběrem příkazů **Uložit** a **OK** kanál verze uložte.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Ruční nasazení nebo nasazení aktivované přes CI

1. Vyberte **+ Vydaná verze** a vyberte **Vytvořit vydanou verzi**.
2. Ve zvýrazněném rozevíracím seznamu vyberte dokončené sestavení a pak vyberte **Zařadit do fronty**.
3. Ve vyskakovací zprávě zvolte odkaz na vydanou verzi. Příklad: „Vydaná verze **Verze-1** byla vytvořena.“
4. Otevřete kartu **Protokoly** a prohlédněte si výstup konzole vydané verze.
5. V prohlížeči otevřete adresu URL jednoho ze serverů, který jste přidali do skupiny nasazení. Zadejte například **http://{IP-adresa-vašeho-serveru}** .
6. Přejděte do zdrojového úložiště Gitu a změňte text nadpisu **h1** v souboru app/views/index.jade.
7. Potvrďte tuto změnu.
8. Po několika minutách uvidíte novou verzi vytvořenou na stránce **Vydání** v Azure DevOps. Otevřete tuto vydanou verzi a zjistěte, jestli probíhá nasazení. Blahopřejeme!

## <a name="troubleshooting-the-jenkins-plugin"></a>Řešení potíží s modulem plug-in Jenkinse

Pokud v modulech plug-in Jenkinse narazíte na nějaké chyby, založte problém na stránce [Jenkins JIRA](https://issues.jenkins-ci.org/) pro konkrétní komponentu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste automatizovali nasazení aplikace do Azure pomocí služby Jenkins pro build a služby Azure DevOps Services pro verzi. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Sestavení aplikace v Jenkins
> * Konfigurace Jenkins pro integraci Azure DevOps Services
> * Vytvoření skupiny nasazení pro virtuální počítače Azure
> * Vytvoření kanálu verze, který nakonfiguruje virtuální počítače a nasadí aplikaci

Pokud chcete zjistit, jak nasadit sadu LAMP (Linux, Apache, MySQL a PHP), přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Nasazení zásobníku LAMP](tutorial-lamp-stack.md)
