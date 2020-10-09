---
title: Kurz – použití Azure Security Center pro virtuální počítače s Windows v Azure
description: V tomto kurzu získáte informace o funkcích služby Azure Security Center k ochraně a zabezpečení virtuálních počítačů s Windows v Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 057cbd5d42eafaa693abc418de62c3ae03a6b995
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984580"
---
# <a name="tutorial-use-azure-security-center-to-monitor-windows-virtual-machines"></a>Kurz: Použití služby Azure Security Center k monitorování virtuálních počítačů s Windows

Azure Security Center vám může pomoct získat přehled o postupech zabezpečení vašich prostředků Azure. Security Center nabízí integrované monitorování zabezpečení. Dokáže detekovat hrozby, kterých byste si jinak možná nevšimli. V tomto kurzu se seznámíte s Azure Security Center a následujícími postupy:

> [!div class="checklist"]
> * Nastavení shromažďování dat
> * Nastavení zásad zabezpečení
> * Zobrazení a oprava problémů se stavem konfigurace
> * Kontrola zjištěných hrozeb

## <a name="security-center-overview"></a>Přehled služby Security Center

Security Center identifikuje potenciální problémy s konfigurací virtuálních počítačů a cílené bezpečnostní hrozby. Ty můžou zahrnovat virtuální počítače bez skupin zabezpečení sítě, nešifrované disky a útoky hrubou silou na protokol RDP (Remote Desktop Protocol). Tyto informace se zobrazí v přehledných grafech na řídicím panelu Security Center.

Přístup k řídicímu panelu Security Center získáte výběrem možnosti **Security Center** v nabídce na webu Azure Portal. Na řídicím panelu můžete zobrazit stav zabezpečení vašeho prostředí Azure, zjistit aktuální počet doporučení a zobrazit aktuální stav upozornění na hrozby. Každý graf vysoké úrovně můžete rozbalit a zobrazit tak další podrobnosti.

![Řídicí panel Security Center](./media/tutorial-azure-security/asc-dash.png)

Security Center jde nad rámec zjišťování dat a poskytuje doporučení k problémům, které detekuje. Pokud se například nasadil virtuální počítač bez připojené skupiny zabezpečení sítě, Security Center zobrazí doporučení s nápravnými kroky, které můžete použít. Získáte automatizovanou nápravu, aniž byste opustili kontext Security Center.  

![Snímek obrazovky zobrazující stránku doporučení, která obsahuje popis, zdroj, stav a závažnost doporučení.](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Nastavení shromažďování dat

Abyste mohli získat přehled o konfiguracích zabezpečení virtuálních počítačů, je potřeba v Security Center nastavit shromažďování dat. Zahrnuje to zapnutí shromažďování dat, čímž se na všechny virtuální počítače ve vašem předplatném automaticky nainstaluje Microsoft Monitoring Agent.

1. Na řídicím panelu Security Center klikněte na **Zásady zabezpečení** a pak vyberte své předplatné. 
2. Pro **Shromažďování dat** vyberte v oblasti **Automatické zřizování** možnost **Zapnuto**.
3. U možnosti **Výchozí konfigurace pracovního prostoru** ponechejte **Použít pracovní prostory vytvořené službou Security Center (výchozí)**.
4. V části **Události zabezpečení** ponechejte výchozí možnost **Běžné**.
4. Klikněte na **Uložit** v horní části stránky. 

Na všechny virtuální počítače se pak nainstaluje agent Security Center pro shromažďování dat a zahájí se shromažďování dat. 

## <a name="set-up-a-security-policy"></a>Nastavení zásady zabezpečení

Zásady zabezpečení slouží k definici položek, pro které Security Center shromažďuje data a poskytuje doporučení. Na různé sady prostředků Azure můžete použít různé zásady zabezpečení. Přestože se ve výchozím nastavení u prostředků Azure vyhodnocují všechny položky zásad, jednotlivé položky zásad můžete pro všechny prostředky Azure nebo skupinu prostředků vypnout. Podrobné informace o zásadách zabezpečení Security Center najdete v tématu [Nastavení zásad zabezpečení v Azure Security Center](../../security-center/tutorial-security-policy.md). 

Nastavení zásad zabezpečení pro celé předplatné:

1. Na řídicím panelu Security Center vyberte **Zásady zabezpečení** a pak vyberte své předplatné.
2. V okně **Zásady zabezpečení** vyberte **Zásady zabezpečení**. 
3. V okně zásady **zabezpečení – zásady zabezpečení** zapněte nebo vypněte položky zásad, které chcete použít u předplatného.
4. Jakmile budete hotovi s výběrem nastavení, vyberte **Uložit** v horní části stránky. 


![Jedinečná zásada](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Zobrazení stavu konfigurace virtuálních počítačů

Po zapnutí shromažďování dat a nastavení zásady zabezpečení začne Security Center poskytovat upozornění a doporučení. Při nasazování virtuálních počítačů se nainstaluje agent pro shromažďování dat. Security Center se pak naplní daty o těchto nových virtuálních počítačích. Podrobné informace o stavu konfigurace virtuálních počítačů najdete v tématu [Ochrana virtuálních počítačů v Security Center](../../security-center/security-center-virtual-machine-protection.md). 

S tím, jak se shromažďují data, se agreguje stav prostředků jednotlivých virtuálních počítačů a souvisejících prostředků Azure. Tyto informace se zobrazí v přehledném grafu. 

Zobrazení stavu prostředků:

1.  Na řídicím panelu Security Center v části **Prevence** vyberte **Compute**. 
2.  V okně **Compute** vyberte **Virtuální počítače a počítače**. Toto zobrazení obsahuje souhrn stavu konfigurace všech vašich virtuálních počítačů.

![Stav výpočetních prostředků](./media/tutorial-azure-security/compute-health.png)

Pokud chcete zobrazit všechna doporučení pro virtuální počítač, vyberte příslušný virtuální počítač. Doporučením a nápravám se podrobněji věnuje další část tohoto kurzu.

## <a name="remediate-configuration-issues"></a>Náprava problémů s konfigurací

Jakmile se Security Center začne naplňovat daty o konfiguraci, začnou se poskytovat doporučení k zásadám zabezpečení, které jste nastavili. Pokud se například nastavil virtuální počítač bez přidružené skupiny zabezpečení sítě, poskytne se doporučení k jejímu vytvoření. 

Zobrazení seznamu všech doporučení: 

1. Na řídicím panelu Security Center vyberte **Doporučení**.
2. Vyberte konkrétní doporučení. Zobrazí se seznam všech prostředků, na které se toto doporučení vztahuje.
3. Pokud chcete doporučení použít, vyberte konkrétní prostředek. 
4. Postupujte podle pokynů k nápravným krokům. 

V řadě případů poskytuje Security Center praktické kroky, pomocí kterých můžete doporučení vyřešit, aniž byste opustili Security Center. V následujícím příkladu Security Center detekuje skupinu zabezpečení sítě, která obsahuje neomezené příchozí pravidlo. Na stránce doporučení můžete vybrat tlačítko **Upravit příchozí pravidla**. Zobrazí se uživatelské rozhraní potřebné k úpravě pravidla. 

![Doporučení](./media/tutorial-azure-security/remediation.png)

Když se doporučení napraví, označí se jako vyřešená. 

## <a name="view-detected-threats"></a>Zobrazení zjištěných hrozeb

Kromě doporučení ke konfiguraci prostředků zobrazuje Security Center upozornění na zjištěné hrozby. Funkce výstrah zabezpečení agreguje data shromážděná z jednotlivých virtuálních počítačů, síťových protokolů Azure a připojených partnerských řešení a detekuje bezpečnostní hrozby u prostředků Azure. Podrobné informace o možnostech detekce hrozeb Security Center najdete v tématu [jak Security Center detekuje hrozby?](../../security-center/security-center-alerts-overview.md#detect-threats).

Funkce výstrah zabezpečení vyžaduje zvýšení cenové úrovně Security Center z úrovně *Free* na úroveň *Standard*. Po přechodu na tuto vyšší cenovou úroveň bude k dispozici **bezplatná zkušební verze** . 

Změna cenové úrovně:  

1. Na řídicím panelu Security Center klikněte na **Zásady zabezpečení** a pak vyberte své předplatné.
2. Vyberte **cenovou úroveň**.
3. Vyberte **Standard** a potom klikněte na **Uložit** v horní části okna.


Po změně cenové úrovně se s detekovanými výstrahami zabezpečení začne naplňovat graf výstrah zabezpečení.

![Výstrahy zabezpečení](./media/tutorial-azure-security/security-alerts.png)

Výběrem výstrahy zobrazíte informace. Můžete například zobrazit popis hrozby, čas detekce, všechny pokusy o zneužití hrozby a doporučenou nápravu. V následujícím příkladu se detekoval útok hrubou silou na protokol RDP s 294 pokusy o připojení RDP, které selhaly. Zobrazí se doporučené řešení.

![Útok na protokol RDP](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste nastavili Azure Security Center a pak jste v Security Center zkontrolovali virtuální počítače. Naučili jste se:

> [!div class="checklist"]
> * Nastavení shromažďování dat
> * Nastavení zásad zabezpečení
> * Zobrazení a oprava problémů se stavem konfigurace
> * Kontrola zjištěných hrozeb

V dalším kurzu se dozvíte, jak na pár virtuálních počítačů s Windows nainstalovat stack SQL, služby IIS a .NET.

> [!div class="nextstepaction"]
> [Stack SQL, služby IIS a .NET](tutorial-iis-sql.md)
