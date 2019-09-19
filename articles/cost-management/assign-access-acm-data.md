---
title: Přiřazení přístupu k datům služby Azure Cost Management | Dokumentace Microsoftu
description: Tento článek vás provede, když přiřadíte oprávnění k datům Azure Cost Management pro různé obory přístupu.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: 332ec3930a7654fd5aecf1fc71ccb55c16df127f
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105125"
---
# <a name="assign-access-to-cost-management-data"></a>Přiřazení přístupu k datům služby Cost Management

Pro uživatele se smlouvou Azure Enterprise je kombinací oprávnění udělených v Azure Portal a portálu Enterprise (EA) definována úroveň přístupu uživatelů k datům Azure Cost Management. Pro uživatele s jinými typy účtů Azure je úroveň přístupu uživatele Cost Management jednodušší. Tento článek vás provede přiřazuje se přístup k datům služby Cost Management. Po přiřazení kombinace oprávnění na základě zobrazení dat uživatele ve službě Cost Management oboru, aby měli přístup k a v oboru, vyberte na portálu Azure portal.

Obor, který uživatel vybere se používá v celém Cost Management k poskytování konsolidace dat a k řízení přístupu na informace o nákladech. Při použití rozsahů, uživatelé není vícenásobný výběr je. Místo toho vyberte větší rozsah, který až vrátit podřízených oborech a pak se filtr seznamu na to, co chtějí zobrazit. Konsolidace dat je důležité pochopit, protože někteří uživatelé neměli mít přístup, který podřízené obory vrátit až nadřazený obor.

Podívejte se na [Postup přiřazení přístupu k Azure cost management](https://www.youtube.com/watch?v=J997ckmwTa8) videu, kde se dozvíte, jak přiřazovat přístup k nákladům na zobrazení a poplatkům pomocí řízení přístupu na základě role Azure.

>[!VIDEO https://www.youtube.com/embed/J997ckmwTa8]

## <a name="cost-management-scopes"></a>Cost Management obory

Správa nákladů podporuje nejrůznější typy účtů Azure. Úplný seznam podporovaných typů účtů najdete v tématu [pochopení cost management dat](understand-cost-mgt-data.md). Typ účtu určuje dostupné obory.

### <a name="azure-ea-subscription-scopes"></a>Obory předplatného Azure EA

Chcete-li zobrazit nákladová data pro předplatná Azure EA, musí mít uživatel alespoň přístup pro čtení k jednomu nebo více následujícím rozsahům.

| **Rozsah** | **Definovaný na** | **Požadovaný přístup k zobrazení dat** | **Požadované nastavení EA** | **Konsoliduje data** |
| --- | --- | --- | --- | --- |
| Fakturační účet<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Podnikový správce | Žádný | Všechna předplatná ze smlouvy Enterprise |
| Oddělení | [https://ea.azure.com](https://ea.azure.com/) | Správce oddělení | **Zobrazit náklady DA** povoleno | Všechna předplatná patřící do registračního účtu propojeného s oddělením |
| Registrační účet<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Vlastník účtu | **Zobrazit náklady AO** povoleno | Všechna předplatná z registračního účtu |
| Skupina pro správu | [https://portal.azure.com](https://portal.azure.com/) | Čtenář Cost Management (nebo Čtenář) | **Zobrazit náklady AO** povoleno | Všechna předplatná spadající do skupiny pro správu |
| Předplatné | [https://portal.azure.com](https://portal.azure.com/) | Čtenář Cost Management (nebo Čtenář) | **Zobrazit náklady AO** povoleno | Všechny prostředky nebo skupiny prostředků v předplatném |
| Skupina prostředků | [https://portal.azure.com](https://portal.azure.com/) | Čtenář Cost Management (nebo Čtenář) | **Zobrazit náklady AO** povoleno | Všechny prostředky ve skupině prostředků |

<sup>1</sup> fakturační účet se také nazývá uzavřenou smlouvu Enterprise nebo registrace.

<sup>2</sup> registračnímu účtu se také označuje jako vlastník účtu.

Následující diagram znázorňuje vztah mezi Cost Management obory s rolemi a nastavením portálu EA.

![Diagram znázorňující vztah mezi Cost Management obory s rolemi a nastavením portálu EA](./media/assign-access-acm-data/scope-access-relationship-diagram.png)

Pokud jsou **poplatky za zobrazení da** na portálu EA zakázané, zobrazí se při pokusu o zobrazení nákladů na oddělení a účty zpráva informující o nákladech, které jsou *pro vaši organizaci zakázané* .

Podobně platí, že když se na portálu EA zakáže **poplatky za zobrazení Ao** , zobrazí se vám při pokusu o zobrazení nákladů na účty pro zápis, skupiny pro správu, předplatná a skupiny prostředků zpráva s informacemi o tom, že jsou *pro vaši organizaci zakázané náklady* .

## <a name="other-azure-account-scopes"></a>Další obory účtů Azure

Chcete-li zobrazit nákladová data pro ostatní předplatná Azure, musí mít uživatel alespoň přístup pro čtení k jednomu nebo více následujícím rozsahům:

- Účet Azure
- Skupina pro správu
- Resource group

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Povolení přístupu k náklady na portálu EA

Obor oddělení vyžaduje **DA zobrazit náklady** možnost **povoleno** přes portál EA. Všechny ostatní obory vyžadují **AO zobrazit náklady** možnost **povoleno** přes portál EA.

Pokud chcete povolit možnost:

1. Přihlaste se k portálu EA na [ https://ea.azure.com ](https://ea.azure.com) s účet správce podnikové sítě.
2. Vyberte **spravovat** v levém podokně.
3. Pro správu nákladů obory, které chcete poskytnout přístup k povolit možnost poplatek **DA zobrazit náklady** a/nebo **AO zobrazit náklady**.  
    ![Registrace kartu zobrazením DA a AO poplatky za možnosti](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Po povolení možnosti zobrazení poplatek se většina oborů také vyžadovat konfigurace oprávnění řízení přístupu na základě role na webu Azure Portal.

## <a name="enterprise-administrator-role"></a>Role správce rozlehlé sítě

Ve výchozím nastavení správce podnikové sítě má přístup k fakturačnímu účtu (smlouva Enterprise pro zápis) a všechny ostatní obory, které jsou podřízené obory. Podnikový správce přiřadí přístup obory pro ostatní uživatele. Jako osvědčený postup zajišťuje nepřetržitý chod podniků byste měli mít vždy dva uživatele s přístupem správce organizace. Následující části jsou příklady názorném postupu přiřazení přístupu správce organizace do oborů pro ostatní uživatele.

## <a name="assign-billing-account-scope-access"></a>Přiřazení přístupu oboru fakturační účet

Přístup k oboru fakturační účet vyžaduje oprávnění správce podniku na portálu EA. Správce rozlehlé sítě má přístup k zobrazení náklady napříč celou registraci smlouvy Enterprise nebo více registrací. Na webu Azure Portal pro obor fakturační účet nevyžaduje žádná akce.

1. Přihlaste se k portálu EA na [ https://ea.azure.com ](https://ea.azure.com) s účet správce podnikové sítě.
2. Vyberte **spravovat** v levém podokně.
3. Na **registrace** kartu, vyberte možnost registrace, který chcete spravovat.  
    ![Vyberte vaše registrace na portálu EA](./media/assign-access-acm-data/ea-portal.png)
4. Klikněte na tlačítko **+ přidat správce**.
5. V dialogovém okně Přidat správce vyberte typ ověřování a zadejte e-mailovou adresu uživatele.
6. Pokud uživatel má mít přístup jen pro čtení pro data o využití a nákladů, v části **jen pro čtení**vyberte **Ano**.  V opačném případě vyberte **ne**.
7. Klikněte na tlačítko **přidat** k vytvoření účtu.  
    ![Příklad informace zobrazené v okně Přidat správce](./media/assign-access-acm-data/add-admin.png)

Může trvat až 30 minut, než se nový uživatel může přístup k datům ve službě Cost Management.

### <a name="assign-department-scope-access"></a>Přiřazení oddělení obor přístupu

Přístup k oboru oddělení vyžaduje oddělení správce (DA zobrazit náklady) přístupu přes portál EA. Správce oddělení má přístup k zobrazení související s oddělení nebo několika oddělení data o využití a nákladů. Data pro oddělení zahrnují všechna předplatná, které patří k účtu pro zápis, které jsou spojeny s oddělení. Na webu Azure Portal nevyžaduje žádná akce.

1. Přihlaste se k portálu EA na [ https://ea.azure.com ](https://ea.azure.com) s účet správce podnikové sítě.
2. Vyberte **spravovat** v levém podokně.
3. Na **registrace** kartu, vyberte možnost registrace, který chcete spravovat.
4. Klikněte na tlačítko **oddělení** kartu a potom klikněte na tlačítko **přidat správce**.
5. V dialogovém okně Přidat Správce oddělení vyberte typ ověřování a pak zadejte e-mailovou adresu uživatele.
6. Pokud uživatel má mít přístup jen pro čtení pro data o využití a nákladů, v části **jen pro čtení**vyberte **Ano**.  V opačném případě vyberte **ne**.
7. Vyberte jako vodítko použijte oddělení, které chcete udělit oprávnění pro správu oddělení.
8. Klikněte na tlačítko **přidat** k vytvoření účtu.  
    ![Zadejte požadované informace do textového pole přidat oddělení správce](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Přiřadit přístup k oboru registraci účtu

Přístup k rozsahu registrace účtu vyžaduje (AO zobrazit náklady) přístup vlastníka účtu na portálu EA. Vlastník účtu můžete zobrazit přidružené k předplatným vytvořené z daného účtu registrace data o využití a nákladů. Na webu Azure Portal nevyžaduje žádná akce.

1. Přihlaste se k portálu EA na [ https://ea.azure.com ](https://ea.azure.com) s účet správce podnikové sítě.
2. Vyberte **spravovat** v levém podokně.
3. Na **registrace** kartu, vyberte možnost registrace, který chcete spravovat.
4. Klikněte na tlačítko **účet** kartu a potom klikněte na tlačítko **přidat účet**.
5. V dialogovém okně Přidat účet vyberte **oddělení** přidružit účet, který chcete, a necháváme ji jak nepřiřazené.
6. Vyberte typ ověřování a zadejte název účtu.
7. Zadejte e-mailovou adresu uživatele a volitelně zadejte nákladové středisko.
8. Klikněte na **přidat** k vytvoření účtu.  
    ![Zadejte požadované informace do textového pole přidat účet pro účet pro zápis](./media/assign-access-acm-data/add-account.png)

Po dokončení výše uvedených kroků, uživatelský účet stane registraci účtu na webu Enterprise Portal a můžete vytvářet odběry. Má uživatel přístup data o využití a nákladů pro předplatné, které vytvářejí.

## <a name="assign-management-group-scope-access"></a>Přiřazení přístupu obor skupiny správy

Přístup k zobrazení rozsahu skupiny pro správu vyžaduje alespoň oprávnění čtenář (nebo čtenář) Cost Management. Na webu Azure Portal můžete nakonfigurovat oprávnění pro skupinu pro správu. Musíte mít alespoň oprávnění správce přístupu uživatelů (nebo vlastník) pro skupinu pro správu umožňující přístup pro ostatní uživatele. A v případě účtů EA Azure je také nutné povolit nastavení **nákladů na zobrazení Ao** na portálu EA.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Vyberte **všechny služby** na bočním panelu vyhledejte _skupin pro správu_a pak vyberte **skupin pro správu**.
3. Vyberte skupiny pro správu v hierarchii.
4. Vedle názvu skupiny pro správu, klikněte na tlačítko **podrobnosti**.
5. Vyberte **řízení přístupu (IAM)** v levém podokně.
6. Klikněte na tlačítko **Add** (Přidat).
7. V části **Role**vyberte **náklady na správu čtečky**.
8. V části **přiřadit přístup k**vyberte **uživatele Azure AD, skupinu nebo aplikaci**.
9. K přiřazení přístupu, vyhledejte a vyberte uživatele.
10. Klikněte na **Uložit**.  
    ![informace z příkladu v poli Přidat oprávnění pro skupinu pro správu](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Přiřazení předplatného obor přístupu

Přístup k předplatnému vyžaduje alespoň oprávnění Čtenář náklady na správu (nebo Čtenář). Na webu Azure Portal můžete nakonfigurovat oprávnění k předplatnému. Musíte mít alespoň oprávnění správce přístupu uživatelů (nebo vlastník) pro předplatné, které chcete povolit přístup pro ostatní uživatele. A v případě účtů EA Azure je také nutné povolit nastavení **nákladů na zobrazení Ao** na portálu EA.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Vyberte **všechny služby** na bočním panelu vyhledejte _předplatná_a pak vyberte **předplatná**.
3. Vyberte své předplatné.
4. Vyberte **řízení přístupu (IAM)** v levém podokně.
5. Klikněte na tlačítko **Add** (Přidat).
6. V části **Role**vyberte **náklady na správu čtečky**.
7. V části **přiřadit přístup k**vyberte **uživatele Azure AD, skupinu nebo aplikaci**.
8. K přiřazení přístupu, vyhledejte a vyberte uživatele.
9. Klikněte na **Uložit**.

## <a name="assign-resource-group-scope-access"></a>Přiřazení přístupu oboru skupiny prostředků

Přístup do skupiny prostředků vyžaduje alespoň oprávnění Čtenář náklady na správu (nebo Čtenář). Na webu Azure Portal můžete nakonfigurovat oprávnění pro skupinu prostředků. Musíte mít alespoň oprávnění správce přístupu uživatelů (nebo vlastník) pro skupinu prostředků pro povolení přístupu pro ostatní uživatele. A v případě účtů EA Azure je také nutné povolit nastavení **nákladů na zobrazení Ao** na portálu EA.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Vyberte **všechny služby** na bočním panelu vyhledejte _skupiny prostředků_a pak vyberte **skupiny prostředků**.
3. Vyberte skupinu prostředků.
4. Vyberte **řízení přístupu (IAM)** v levém podokně.
5. Klikněte na tlačítko **Add** (Přidat).
6. V části **Role**vyberte **náklady na správu čtečky**.
7. V části **přiřadit přístup k**vyberte **uživatele Azure AD, skupinu nebo aplikaci**.
8. K přiřazení přístupu, vyhledejte a vyberte uživatele.
9. Klikněte na **Uložit**.

## <a name="cross-tenant-authentication-issues"></a>Problémy s ověřováním mezi klienty

V současné době má Azure Cost Management omezená podpora pro ověřování mezi klienty. V některých případech se při pokusu o ověření napříč klienty může při analýze nákladů zobrazit chyba **odepření přístupu** . K tomuto problému může dojít, pokud nakonfigurujete řízení přístupu na základě role (RBAC) na předplatné jiného tenanta a pak se pokusíte zobrazit data o nákladech.

*Postup při*obejít problému: Až nakonfigurujete RBAC mezi klienty, počkejte hodinu. Pak se pokuste zobrazit náklady v analýze nákladů nebo udělit uživatelům přístup k Cost Managementům v obou klientech.  


## <a name="next-steps"></a>Další postup

- Pokud jste ještě nedokončili první tohoto rychlého startu Cost Management, přečtěte si ho na [začít analýza nákladů](quick-acm-cost-analysis.md).
