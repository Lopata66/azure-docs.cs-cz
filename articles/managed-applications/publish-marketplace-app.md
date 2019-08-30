---
title: Spravované aplikace Azure v Marketplace | Microsoft Docs
description: Popisuje spravované aplikace Azure, dostupné přes Marketplace.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 07/17/2019
ms.author: tomfitz
ms.openlocfilehash: 372c65f1ac9a08d066f26e637f3af86807b35f11
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182809"
---
# <a name="azure-managed-applications-in-the-marketplace"></a>Spravované aplikace Azure v Marketplace

Dodavatelé můžou použít spravované aplikace Azure k nabízení svých řešení pro všechny zákazníky využívající Azure Marketplace. Těmito dodavateli mohou být poskytovatelé spravovaných služeb (MSP), nezávislí výrobci softwaru (ISV) a integrátoři systémů (SI). Spravované aplikace snižují zákazníkům náklady na údržbu a servis. Dodavatelé prodávají infrastrukturu a software přes marketplace. Ke spravovaným aplikacím můžou přidat také služby a podporu provozu. Další informace najdete v [Přehledu spravovaných aplikací](overview.md).

Tento článek vysvětluje, jak můžete publikovat aplikaci na Marketplace a tím ji zpřístupnit velkému počtu zákazníků.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Předpoklady pro publikování spravované aplikace

K dokončení tohoto článku musíte mít připravený soubor .zip s definicí vaší spravované aplikaci. Další informace najdete v článku [Vytvoření aplikace katalogu služeb](publish-service-catalog-app.md).

Existuje několik obchodních požadavků. Jsou to tyto:

* Vaše společnost nebo její dceřiná společnost se musí nacházet v zemi nebo oblasti, kde prodej podporuje Marketplace.
* Váš produkt musí mít licenci kompatibilní s modely fakturace, které marketplace podporuje.
* Zákazníkům je třeba zpřístupnit z obchodního hlediska přiměřenou technickou podporu. Podpora může být bezplatná, placená nebo komunitní.
* Licence musí pokrývat váš software a všechny závislé komponenty třetích stran.
* Poskytujte obsah, který splňuje kritéria pro to, aby vaše nabídka mohla být zařazena do Marketplace a na webu Azure Portal.
* Vyjádřete souhlas s podmínkami Zásad účasti v programu Azure Marketplace a Ujednání s vydavatelem.
* Vyjádřete souhlas s dodržováním Podmínek použití, Prohlášení o zásadách ochrany osobních údajů a Smlouvy k programu Microsoft Azure Certified.

Musíte mít také účet Marketplace. Pokud chcete vytvořit účet, přečtěte si téma [Vytvoření účtu komerčního tržiště v partnerském centru](..//marketplace/partner-center-portal/create-account.md).

## <a name="create-a-new-azure-application-offer"></a>Vytvoření nové nabídky aplikace Azure

Po vytvoření účtu partnera na portálu můžete pokročit k vytvoření nabídky spravované aplikace.

### <a name="set-up-an-offer"></a>Vytvoření nabídky

Nabídka pro spravované aplikace odpovídá třídě nabídky produktů od vydavatele. Pokud máte nový typ aplikace, kterou chcete v marketplace zpřístupnit, můžete ho vytvořit jako novou nabídku. Nabídka je vlastně kolekce SKU. Každá nabídky se zobrazuje v marketplace jako samostatná entita.

1. Přihlaste se na [portál partnerů cloudu](https://cloudpartner.azure.com/).

1. V navigačním podokně na levé straně vyberte **+ Nová nabídka** > **Aplikace Azure**.

1. V zobrazení **Editor** uvidíte požadované formuláře. Každý z formulářů je popsán dále v tomto článku.

## <a name="offer-settings-form"></a>Formulář pro nastavení nabídky

Formulář **Nastavení nabídky** obsahuje tato pole:

* **ID nabídky**: Tento jedinečný identifikátor identifikuje nabídku v rámci profilu vydavatele. Tento identifikátor se zobrazuje na adresách URL produktu, v šablonách Resource Manageru a ve fakturačních sestavách. Může se skládat jenom z malých alfanumerických znaků nebo pomlček (-). Nemůže končit pomlčkou. Jeho délka je omezená na maximálně 50 znaků. Po publikování nabídky je toto pole zamčené.
* **ID vydavatele**: Pomocí tohoto rozevíracího seznamu vyberte profil vydavatele, pod kterým chcete tuto nabídku publikovat. Po publikování nabídky je toto pole zamčené.
* **Název**: Tento zobrazovaný název vaší nabídky se zobrazí na webu Marketplace a na portálu. Může mít maximálně 50 znaků. Do názvu zahrňte rozpoznatelný název značky pro váš produkt. Naopak název vaší společnosti zahrňte jenom v případě, že se tímto způsobem propaguje. Pokud tuto nabídku propagujete na vlastních webových stránkách, zkontrolujte, že název je přesně stejný jako na vašem webu.

Až budete hotoví, rozdělanou práci uložte volbou **Uložit**.

## <a name="skus-form"></a>Formulář Skladové položky

Dalším krokem je přidání skladových položek (SKU) pro vaši nabídku.

SKU je nejmenší zakoupitelná jednotka nabídky. Skladové položky v rámci stejné produktové třídy (nabídky) můžete použít pro rozlišení:

* Různých funkcí, které jsou podporované
* Toho, jestli nabídka je spravovaná nebo nespravovaná
* Modelů fakturace, které jsou podporované

Skladová položka se na marketplace zobrazí pod příslušnou nadřazenou nabídkou. Na webu Azure Portal se zobrazí jako samostatná entita s možností zakoupení.

1. Vyberte **Skladová položky** > **Nová skladová položka**.

1. Z**ID skladové položky**. ID skladové položky je jedinečný identifikátor SKU v rámci nabídky. Tento identifikátor se zobrazuje na adresách URL produktu, v šablonách Resource Manageru a ve fakturačních sestavách. Může se skládat jenom z malých alfanumerických znaků nebo pomlček (-). ID nemůže končit pomlčkou a jeho délka je omezená na maximálně 50 znaků. Po publikování nabídky je toto pole zamčené. V rámci nabídky může existovat několik skladových položek. Budete potřebovat skladovou položku pro každou image, kterou plánujete publikovat.

1. V následujícím formuláři vyplňte část s **podrobnými informacemi o SKU**:

   Vyplňte následující pole:

   * **Název**: Zadejte název této SKU. Pod tímto názvem bude tato položka zobrazená v galerii.
   * **Souhrn**: Zadejte krátký souhrn této SKU. Tento text se zobrazí pod názvem.
   * **Popis**: Zadejte podrobný popis SKU.
   * **Typ SKU**: Povolené hodnoty jsou *spravované aplikace* a *šablony řešení*. V tomto případě vyberte *Spravovaná aplikace*.
   * **Dostupnost země/oblasti**: Vyberte země nebo oblasti, ve kterých je spravovaná aplikace k dispozici.
   * **Ceny**: Zadejte cenu za správu aplikace. Před nastavením ceny vyberte dostupné země/oblasti.

1. Přidejte nový balíček. V následujícím formuláři vyplňte část s **podrobnými informacemi o balíčku**:

   Vyplňte následující pole:

   * **Verze**: Zadejte verzi balíčku, který nahráváte. Měla by mít formát `{number}.{number}.{number}{number}`.
   * **Soubor balíčku (. zip)** : Tento balíček obsahuje dva požadované soubory zkomprimované do balíčku. zip. Jedním z těchto souborů je šablona, která definuje prostředky k nasazení pro spravovanou aplikaci. Druhý soubor definuje [uživatelské rozhraní](create-uidefinition-overview.md) pro uživatele nasazující tuto spravovanou aplikaci prostřednictvím portálu. V uživatelském rozhraní zadáváte elementy, které uživatelům umožňují zadávat hodnoty parametrů.
   * **ID tenanta**: ID tenanta pro účet, který má získat přístup.
   * **Povolit přístup JIT**: Vyberte **Ano** , pokud chcete pro účet povolit [řízení přístupu za běhu](request-just-in-time-access.md) . Pokud je tato možnost povolená, vyžádáte si k účtu příjemce přístup za zadané časové období. Pokud chcete vyžadovat, aby příjemci spravované aplikace udělili trvalému přístupu k vašemu účtu, vyberte **ne**.
   * **Přizpůsobit povolené akce zákazníků?** : Vyberte **Ano** a určete akce, které můžou příjemci provádět na spravovaných prostředcích.
   * **Povolené akce zákazníka**: Pokud pro předchozí nastavení vyberete **Ano** , můžete určit, které akce mají uživatelé povoleni pomocí [přiřazení Odepřít pro prostředky Azure](../role-based-access-control/deny-assignments.md).

     Dostupné akce najdete v tématu [Azure Resource Manager operací poskytovatele prostředků](../role-based-access-control/resource-provider-operations.md). Například pokud chcete, aby uživatelé mohli restartovat virtuální počítače, přidejte `Microsoft.Compute/virtualMachines/restart/action` do povolených akcí. `*/read` Akce je povolena automaticky, takže nemusíte toto nastavení zahrnovat.
   * **PrincipalId**: Tato vlastnost je identifikátorem Azure Active Directory (Azure AD) uživatele, skupiny uživatelů nebo aplikace, kterým byl udělen přístup k prostředkům v rámci předplatného zákazníka. Definice role popisuje tato oprávnění.
   * **Definice role**: Tato vlastnost je seznam všech předdefinovaných rolí Access Control na základě rolí (RBAC) poskytovaných službou Azure AD. Můžete vybrat roli, která je nejvhodnější pro správu prostředků v zastoupení uživatele.
   * **Nastavení zásad**: Použijte [Azure Policy](../governance/policy/overview.md) pro spravovanou aplikaci, abyste určili požadavky na dodržování předpisů pro nasazená řešení. Z dostupných možností vyberte požadované zásady. V části **Parametry zásad** zadejte řetězec JSON s hodnotami parametrů. Definice zásad a formátování hodnot parametrů najdete v tématu [Ukázky Azure Policy](../governance/policy/samples/index.md).

Autorizací můžete přidat několik. Doporučujeme, abyste vytvořili skupinu uživatelů AD a její ID zadali do **PrincipalId**. Budete tak mít možnost přidávat do této skupiny uživatelů další uživatele, aniž by bylo nutné aktualizovat SKU.

Další informace o RBAC najdete v tématu věnovaném [zahájení práce s RBAC na webu Azure Portal](../role-based-access-control/overview.md).

## <a name="marketplace-form"></a>Formulář Marketplace

Formulář Marketplace vyzývá k zadání polí, která se zobrazují na [Azure Marketplace](https://azuremarketplace.microsoft.com) a na webu [Azure Portal](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Zobrazení náhledu ID předplatných

Zadejte seznam ID předplatných Azure, která mohou přistupovat k nabídce po jejím publikování. Tato povolená předplatná můžete využít k otestování nabídky před jejím zprovozněním. Na portálu pro partnery můžete zkompilovat seznam povolených až 100 předplatných.

### <a name="suggested-categories"></a>Navrhované kategorie

Ze seznamu vyberte až pět kategorií, ke kterým se vaše nabídka dá nejlépe přidružit. Tyto kategorie se používají pro mapování vaší nabídky na kategorie produktů, které jsou k dispozici na [Azure Marketplace](https://azuremarketplace.microsoft.com) a na webu [Azure Portal](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Marketplace

V souhrnu vaší spravované aplikace se zobrazují tato pole:

![Souhrn na Marketplace](./media/publish-marketplace-app/publishvm10.png)

Na kartě **Přehled** vaší spravované aplikace se zobrazují tato pole:

![Přehled webu Marketplace](./media/publish-marketplace-app/publishvm11.png)

Na kartě **Plány a ceny** vaší spravované aplikace se zobrazují tato pole:

![Plány na Marketplace](./media/publish-marketplace-app/publishvm15.png)

#### <a name="azure-portal"></a>portál Azure

V souhrnu vaší spravované aplikace se zobrazují tato pole:

![Souhrn na portálu](./media/publish-marketplace-app/publishvm12.png)

V přehledu vaší spravované aplikace se zobrazují tato pole:

![Přehled na portálu](./media/publish-marketplace-app/publishvm13.png)

#### <a name="logo-guidelines"></a>Pokyny pro použití loga

Postupujte podle těchto pokynů pro všechna loga, která nahrajete na portál partnerů cloudu:

*   Design Azure má jednoduchou paletu barev. Omezte počet primárních a sekundárních barev ve vašem logu.
*   Barvami motivů na portálu jsou bílá a černá. Nepoužívejte tyto barvy jako barvu pozadí pro vaše logo. Použijte barvu, která vaše logo na portálu zvýrazní. Doporučujeme použít jednoduché primární barvy. *Pokud použijete průhledné pozadí, ujistěte se, že text ani logo nejsou bílé, černé nebo modré.*
*   Nepoužívejte v logu pozadí s barevným přechodem.
*   Na logo neumisťujte žádný text, a to ani název vaší společnosti nebo název značky. Logo by mělo působit plochým dojmem a nemělo by obsahovat barevné přechody.
*   Zajistěte, aby logo nebylo roztažené.

#### <a name="hero-logo"></a>Hero logo

Hero logo je volitelné. Vydavatel se může rozhodnout, že ho nenahraje. Jakmile se hero logo nahraje, už nejde odstranit. Partner přitom musí postupovat podle pokynů Marketplace pro hero loga.

Při vytváření ikony hero loga postupujte podle těchto pokynů:

*   Zobrazovaný název vydavatele, název plánu a dlouhý souhrn nabídky se zobrazují bíle. Proto jako pozadí pro hero ikonu nepoužívejte světlé barvy. U hero ikon není povolené černé, bílé ani průhledné pozadí.
*   Po uvedení nabídky se jednotlivé elementy do hero loga vkládají programově. Tyto vložené elementy zahrnují zobrazovaný název vydavatele, název plánu, dlouhý souhrn nabídky a tlačítko **Create** (Vytvořit). V důsledku při navrhování hero loga nezadávejte žádný text. Na pravé straně ponechte volné místo, protože právě tam se programově vkládá text. Prázdné místo pro text by mělo být 415 x 100 pixelů napravo. Od levého okraje je posunuté o 370 pixelů.

    ![Příklad hero loga](./media/publish-marketplace-app/publishvm14.png)

## <a name="support-form"></a>Formulář Podpora

Ve formuláři **Podpora** vyplňte kontakty na podporu ve vaší společnosti. Mohou to být kontakty na techniky a na zákaznickou podporu.

## <a name="publish-an-offer"></a>Publikování nabídky

Po vyplnění všech částí vyberte **Publish** (Publikovat). Zahájíte tak proces zpřístupnění vaší nabídky zákazníkům.

## <a name="next-steps"></a>Další postup

* Informace o tom, co se stane po kliknutí na **publikovat**, najdete v tématu [publikování nabídky aplikací Azure](../marketplace/cloud-partner-portal/azure-applications/cpp-publish-offer.md) .
* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací](overview.md).
* Informace o publikování spravované aplikace katalogu služeb najdete v tématu věnovaném [vytvoření a publikování spravované aplikace katalogu služeb](publish-service-catalog-app.md).
