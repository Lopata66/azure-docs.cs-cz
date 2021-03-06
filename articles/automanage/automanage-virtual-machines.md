---
title: Azure automanage pro virtuální počítače
description: Přečtěte si o Azure automanage pro virtuální počítače.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 7772d57937393da1c48fa2658818d8a1a2b28a1f
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550780"
---
# <a name="azure-automanage-for-virtual-machines"></a>Azure automanage pro virtuální počítače

Tento článek obsahuje informace o službě Azure automanage pro virtuální počítače, které mají následující výhody:

- Inteligentní zprovoznění virtuálních počítačů pro výběr osvědčených postupů služeb Azure
- Automaticky konfiguruje každou službu na osvědčené postupy Azure
- Monitoruje při zjištění posun a opravy.
- Poskytuje jednoduché možnosti (Ukázat, kliknout, nastavit, zapomenout).


## <a name="overview"></a>Přehled

Azure automanage pro virtuální počítače je služba, která eliminuje nutnost zjišťovat, poznat, jak připojit a jak nakonfigurovat určité služby v Azure, které by mohly být pro váš virtuální počítač výhodné. Tyto služby vám pomůžou zlepšit spolehlivost, zabezpečení a správu pro virtuální počítače a považují se za služby Azure Best Practices, jako je například [azure Update Management](../automation/update-management/overview.md) a [Azure Backup](../backup/backup-overview.md) -pouze pár názvů.

Po připojení virtuálních počítačů k automatické správě Azure automaticky nakonfiguruje každou službu osvědčených postupů na Doporučené nastavení. Osvědčené postupy se pro jednotlivé služby liší. Příkladem může být Azure Backup, kde osvědčeným postupem může být vytvoření zálohy virtuálního počítače jednou denně a doba uchování po dobu šesti měsíců.

Azure automanage také automaticky monitoruje při zjištění posunu a oprav. To znamená, že pokud je váš virtuální počítač připojen do Azure automanage, nebudeme ho konfigurovat jenom na osvědčené postupy Azure, ale monitoruje váš počítač, aby se zajistilo, že bude nadále dodržovat tyto osvědčené postupy napříč celým životním cyklem. Pokud váš virtuální počítač posune nebo odliší od těchto postupů, my ho Opravme a nahrajeme váš počítač zpátky do požadovaného stavu.

A konečně prostředí je neuvěřitelně jednoduché.


## <a name="prerequisites"></a>Požadavky

Před tím, než se pokusíte povolit službu Azure na virtuálních počítačích, je třeba zvážit několik požadavků.

- Jenom virtuální počítače s Windows serverem
- Virtuální počítače musí být v podporované oblasti (viz odstavec níže).
- Uživatel musí mít správná oprávnění (viz odstavec níže).
- Automanage v tuto chvíli nepodporuje odběry izolovaného prostoru (sandbox).

Je také důležité si uvědomit, že automanage podporuje jenom virtuální počítače s Windows, které jsou v těchto oblastech: Západní Evropa, Východní USA, Západní USA 2, Kanada – střed, Středozápadní USA, Japonsko – východ.

Abyste mohli povolit autosprávu na virtuálních počítačích pomocí stávajícího účtu pro správu, musíte mít ve skupině prostředků, která obsahuje vaše virtuální počítače, roli **Přispěvatel** . Pokud povolujete autosprávu pomocí nového účtu pro autosprávu, budete potřebovat následující oprávnění k vašemu předplatnému: role **vlastníka** nebo **přispěvatele** spolu s rolemi **Správce přístupu uživatele** .

> [!NOTE]
> Pokud chcete použít automanage na virtuálním počítači, který je připojený k pracovnímu prostoru v jiném předplatném, musíte mít oprávnění popsaná výše v každém předplatném.

## <a name="participating-services"></a>Zúčastněné služby

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="Inteligentně zaregistrované služby.":::

V tématu Virtual Machines osvědčené postupy pro úplný seznam zúčastněných služeb Azure a také na jejich podporované konfigurační profily viz [Azure automanage](virtual-machines-best-practices.md) .

 Do těchto zúčastněných služeb vás automaticky připojíme. Jsou zásadní pro náš dokument white paper k osvědčeným postupům, který najdete v našem [cloudovém rozhraní pro přijetí](/azure/cloud-adoption-framework/manage/azure-server-management).

Pro všechny tyto služby budeme automaticky připínat, automaticky konfigurovat, monitorovat a opravovat, pokud se zjistí posun.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Povolení automanage pro virtuální počítače v Azure Portal

V Azure Portal můžete povolit automanage na existujícím virtuálním počítači nebo při vytváření nového virtuálního počítače. Stručné kroky tohoto procesu najdete v [rychlém startu pro automatizované správy virtuálních počítačů](quick-create-virtual-machines-portal.md).

Pokud pro virtuální počítač používáte službu automanage poprvé, můžete hledat v Azure Portal pro možnost **automanage – osvědčené postupy pro virtuální počítače Azure**. Klikněte na **Povolit na existujícím virtuálním počítači**, vyberte virtuální počítače, které chcete připojit, klikněte na **Vybrat**, klikněte na **Povolit** a Vy jste hotovi.

Jediná doba, kterou možná budete potřebovat k interakci s tímto virtuálním počítačem za účelem správy těchto služeb, je v události, kterou jsme se pokusili opravit váš virtuální počítač, ale to se nepovedlo. Pokud jsme váš virtuální počítač úspěšně napravili, převedeme ho zpátky do dodržování předpisů, aniž by vás upozornili na vás.


## <a name="configuration-profiles"></a>Konfigurační profily

Pokud povolujete pro virtuální počítač možnost automanage, je vyžadován konfigurační profil. Základem této služby jsou konfigurační profily. Definují přesně to, které služby tyto počítače zařadí do a do určité míry, jakou mají konfigurace těchto služeb.

### <a name="default-configuration-profiles"></a>Výchozí konfigurační profily

K dispozici jsou dva konfigurační profily, které jsou aktuálně k dispozici.

- **Osvědčené postupy pro virtuální počítače Azure –** konfigurační profil pro vývoj a testování je určený pro vývojové a testovací počítače.
- **Osvědčené postupy pro virtuální počítače Azure –** konfigurační profil pro produkční prostředí je určen pro produkční prostředí.

Důvodem pro tento rozdíl je, že některé služby jsou doporučené na základě spuštěné úlohy. Například v produkčním počítači se automaticky připojíme k Azure Backup. Pro vývoj/testování počítačů se však služba zálohování nejedná o zbytečné náklady, protože počítače pro vývoj/testování obvykle mají nižší dopad na firmu.

### <a name="customizing-a-configuration-profile-using-preferences"></a>Přizpůsobení konfiguračního profilu pomocí předvoleb

Kromě standardních služeb, na které vás připravujeme, vám umožníme nakonfigurovat určitou podmnožinu předvoleb. Tyto preference jsou povolené v rámci různých možností konfigurace, které neporušily osvědčené postupy. V případě Azure Backup vám například umožní definovat četnost zálohování a den v týdnu, ve kterém se vyskytuje. Nebudeme ale  moct úplně vypnout Azure Backup.

> [!NOTE]
> V konfiguračním profilu pro vývoj/testování se virtuální počítač vůbec nezálohuje.

Nastavení výchozího konfiguračního profilu můžete upravit pomocí předvoleb. [Tady](virtual-machines-custom-preferences.md)se dozvíte, jak vytvořit preference.

> [!NOTE]
> Pokud je povolená možnost automanage, nemůžete změnit konfigurační profil na VIRTUÁLNÍm počítači. Pro tento virtuální počítač budete muset zakázat možnost automatického spravování a pak znovu povolit automanage s požadovaným konfiguračním profilem a preferencemi.


## <a name="automanage-account"></a>Účet pro autosprávu

Účet pro automatické spravování je kontext zabezpečení nebo identita, pod kterou dojde k automatickým operacím. Obvykle možnost automatického spravování účtu není pro výběr k dispozici, ale pokud existoval scénář delegování, kdy jste chtěli rozdělit automatizovanou správu prostředků (třeba mezi dvěma správci systému), tato možnost umožňuje definovat identitu Azure pro každého z těchto správců.

V prostředí Azure Portal máte při povolování funkce automanage na vašich virtuálních počítačích pokročilý rozevírací seznam v okně **Povolit osvědčené postupy pro virtuální počítače Azure** , které vám umožní přiřadit nebo ručně vytvořit účet pro správu.

Účtu automanage budou udělena **role přispěvatelů** a **zásad prostředků přispěvatelům** k předplatným, která obsahují počítače, které chcete spravovat. Stejný účet pro správu můžete použít na počítačích v rámci více předplatných, což udělí oprávnění k autosprávě účtu **přispěvatele** a **zásad prostředků** u všech předplatných.

Pokud je váš virtuální počítač připojený k pracovnímu prostoru Log Analytics v jiném předplatném, bude mít účet pro  správu v tomto jiném předplatném taky **Přispěvatel a přispěvatel zásad prostředků** .

Pokud povolujete autosprávu pomocí nového účtu pro autosprávu, budete potřebovat následující oprávnění k vašemu předplatnému: role **vlastníka** nebo **přispěvatele** spolu s rolemi **Správce přístupu uživatele** .

Pokud povolujete možnost automanage s existujícím účtem pro správu, musíte mít roli **přispěvatele** ve skupině prostředků, která obsahuje vaše virtuální počítače.

> [!NOTE]
> Když zakážete možnosti pro automanage, budete mít i oprávnění účtu automanage u všech přidružených předplatných. Ručně odeberte oprávnění tak, že na stránce IAM předplatného odeberete nebo odstraníte účet pro správu. Účet automanage nelze odstranit, pokud stále spravuje všechny počítače.


## <a name="status-of-vms"></a>Stav virtuálních počítačů

V Azure Portal přejdete na stránku s **osvědčenými postupy pro automatické spravování – Azure Virtual Machine** , kde najdete seznam všech vašich automatických spravovaných virtuálních počítačů. Tady se zobrazí celkový stav každého virtuálního počítače.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Seznam nakonfigurovaných virtuálních počítačů.":::

Pro každý uvedený virtuální počítač se zobrazí následující podrobnosti: název, konfigurační profil, předvolby konfigurace, stav, účet, předplatné a skupina prostředků.

Sloupec **Status (stav** ) může zobrazit následující stavy:
- *Probíhá – virtuální* počítač byl právě povolen a probíhá jeho konfigurace.
- *Nakonfigurováno* – virtuální počítač je nakonfigurovaný a nezjistil se žádný posun.
- *Selhalo* – virtuální počítač se ztratil a nedokázali jsme ho opravit.
- *Čeká na vyřízení* – virtuální počítač v tuto chvíli není spuštěný a při příštím spuštění se služba automanage pokusí virtuální počítač zprovoznit nebo ho opravit.

Pokud se **stav** zobrazuje jako *neúspěšný*, můžete řešit problémy s nasazením prostřednictvím skupiny prostředků, ve které se váš virtuální počítač nachází. Přejděte na **skupiny prostředků**, vyberte svoji skupinu prostředků, klikněte na **nasazení** a v části stav *selhání se* zobrazí podrobnosti o chybě.


## <a name="disabling-automanage-for-vms"></a>Zakázání automanage pro virtuální počítače

Můžete se rozhodnout jeden den, abyste mohli na určitých virtuálních počítačích zakázat tuto metodu. Například váš počítač používá určitou zabezpečenou úlohu s velmi citlivými daty a je potřeba ho uzamknout ještě více než Azure, protože by to bylo přirozeně, takže je potřeba nakonfigurovat počítač mimo osvědčené postupy Azure.

Pokud to chcete udělat v Azure Portal, přečtěte si stránku s **osvědčenými postupy pro automatické spravování – Azure Virtual Machine** , která obsahuje seznam všech vašich automatických spravovaných virtuálních počítačů. Zaškrtněte políčko u virtuálního počítače, který chcete zakázat z možnosti spravovat, a pak klikněte na tlačítko **Zakázat autosprávu** .

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Zakázání automanage na virtuálním počítači.":::

Než se odsouhlaste s **zakázáním**, důkladně si přečtěte zprávy ve výsledném okně.

> [!NOTE]
> Zakázáním funkce automanagement ve virtuálním počítači dojde k následujícímu chování:
>
> - Konfigurace virtuálního počítače a služeb, které jsou zaregistrované, aby se nezměnily.
> - Veškeré poplatky, které tyto služby vznikly, zůstávají fakturovatelné a i nadále budou platit.
> - Jakékoli chování při autosprávě se okamžitě zastaví.


První a první, nebudeme virtuální počítač od žádné ze služeb, které jsme připojili a nakonfigurovali, mimo kartu. Všechny poplatky, které tyto služby vznikly, budou i nadále Fakturovatelné. V případě potřeby budete potřebovat mimo panel. Jakékoli chování funkce automanage se okamžitě zastaví. Například už nebudeme monitorovat virtuální počítač pro posun.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, že automanage pro virtuální počítače poskytuje způsob, jakým můžete eliminovat nutnost znát, připojit a nakonfigurovat osvědčené postupy pro služby Azure. Kromě toho, pokud se počítač, který se připojí k automatické správě virtuálních počítačů, posune od nastavených konfiguračních profilů, automaticky ho vrátíme zpátky do dodržování předpisů.

Zkuste povolit automanage pro virtuální počítače v Azure Portal.

> [!div class="nextstepaction"]
> [Povolit automanage pro virtuální počítače v Azure Portal](quick-create-virtual-machines-portal.md)