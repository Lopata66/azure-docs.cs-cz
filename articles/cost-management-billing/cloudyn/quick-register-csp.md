---
title: Registrace u Cloudyn v Azure s použitím informací o partnerovi CSP
description: Tento rychlý start podrobně popisuje proces registrace nezbytný k vytvoření zkušebního předplatného a přihlášení k portálu Cloudyn.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.reviewer: benshy
ms.openlocfilehash: ea156e8efabcb73b324f0a102945b227558cece9
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117773"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Registrace do partnerského programu CSP a zobrazení informací o nákladech

Jako partner CSP se můžete zaregistrovat u Cloudyn. Registrací získáte přístup k portálu Cloudyn. Tento rychlý start podrobně popisuje proces registrace nezbytný k vytvoření zkušebního předplatného a přihlášení k portálu Cloudyn. Ukazuje také, jak rovnou začít zobrazovat informace o nákladech.


> [!NOTE]
>
> Registraci do Cloudyn můžou dokončit pouze přímí partneři CSP a nepřímí poskytovatelé CSP.
>
> Pro ověřování a přístup k datům je nezbytná konfigurace rozhraní API partnerského centra. Pro zřízení přístupu k rozhraní API je potřeba účet globálního správce partnerského centra.
> Další informace najdete v tématu [Připojení k rozhraní API partnerského centra](https://msdn.microsoft.com/library/partnercenter/mt709136.aspx).
>
> Nepřímým prodejcům CSP je možné Cloudyn zpřístupnit poté, co se jejich nepřímý poskytovatel CSP zaregistruje u Cloudyn. Nepřímí prodejci CSP pak můžou poskytovat přístup do Cloudyn předplatným a zákazníkům Azure.
>
>Cloudyn je kompatibilní s modelem zabezpečených aplikací Microsoftu. Další informace najdete v článku o [povolení architektury modelu zabezpečených aplikací](/partner-center/develop/enable-secure-app-model).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="register-with-cloudyn"></a>Registrace u Cloudyn

1. Na webu Azure Portal klikněte v seznamu služeb na **Cost Management a fakturace**.
2. V části **Přehled** klikněte na **Cloudyn**.  
    ![Stránka Cloudyn na webu Azure Portal](./media/quick-register-csp/cost-mgt-billing-service.png)
3. Na stránce **Cloudyn** klikněte na **Přejít na Cloudyn** a v novém okně se otevře stránka pro registraci u Cloudyn.
4. Na stránce pro registraci zkušební verze portálu Cloudyn zadejte název vaší společnosti, vyberte **Správce partnerského programu Microsoft CSP** a pak klikněte na **Další**.  
5. Zadejte **ID aplikace**, **Obchodní ID**, **Tajný klíč aplikace** a vyberte **Výchozí cenový plán**. Pokud tyto informace nemáte po ruce, přihlaste se pomocí svého primárního účtu správce k portálu partnerského centra na adrese [https://partnercenter.microsoft.com](https://partnercenter.microsoft.com) a proveďte následující kroky:
   1. Přejděte na **Řídicí panel**, klikněte na symbol **Nastavení**, klikněte na **Nastavení partnera** a pak na **Správa aplikací**.
   2. Pokud jste už dříve vytvořili webovou aplikaci, tento krok přeskočte. Jinak v části **Webová aplikace** klikněte na **Přidat novou webovou aplikaci**.
   3. Zkopírujte ze své webové aplikace globálně jedinečný identifikátor **ID aplikace**.
   4. Zkopírujte ze své webové aplikace globálně jedinečný identifikátor **Obchodní ID**.
   5. Jako délku platnosti klíče vyberte podle potřeby jeden nebo dva roky. Vyberte **Přidat klíč** a pak zkopírujte a uložte hodnotu tajného klíče.  
    ![Řídicí panel partnera, kde kopírujete informace o přihlašovacích údajích](./media/quick-register-csp/csp-partner-center.png)
   6. Vraťte se na registrační stránku Cloudyn a vložte příslušné údaje.  
      ![Vložení informací o přihlašovacích údajích na registrační stránce Cloudyn](./media/quick-register-csp/csp-reg.png)
6. Vyjádřete souhlas s podmínkami použití a ověřte své údaje. Kliknutím na **Další** udělte Cloudyn oprávnění ke shromažďování dat o prostředcích Azure. Mezi shromažďovaná data patří informace o využití, výkonu, fakturaci a značkách z vašich předplatných.  
7. V části **Pozvat další účastníky** můžete zadáním příslušných e-mailových adres přidat další uživatele. Jakmile budete hotovi, klikněte na **Další**. Přidání všech vašich fakturačních dat do Cloudyn trvá přibližně dvě hodiny.
8. Kliknutím na **Přejít do Cloudyn** otevřete portál Cloudyn a na stránce **Správa cloudových účtů** by se měly zobrazit informace o vašem zaregistrovaném účtu CSP.

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Konfigurace přístupu nepřímých poskytovatelů CSP v Cloudyn

Rozhraní API partnerského centra je ve výchozím nastavení přístupné pouze pro přímé poskytovatele CSP. Přímý poskytovatel CSP však může nakonfigurovat přístup pro své nepřímé zákazníky nebo partnery CSP pomocí skupin entit v Cloudyn.

Pokud chcete umožnit přístup nepřímým zákazníkům nebo partnerům CSP, postupujte podle kroků v části [Registrace u Cloudyn](#register-with-cloudyn) a nastavte registraci zkušební verze. Dále provedením následujících kroků segmentujte data nepřímých poskytovatelů CSP pomocí skupin entit Cloudyn. Potom skupinám entit přiřaďte odpovídající uživatelská oprávnění.

1. Vytvořte skupinu entit s použitím informací v části [Vytvoření entit](tutorial-user-access.md#create-and-manage-entities).
2. Postupujte podle kroků v článku [Assigning subscriptions to Cost Entities](https://www.youtube.com/watch?v=d9uTWSdoQYo) (Přiřazování předplatných k entitám nákladů). Přidružte účet nepřímého zákazníka CSP a příslušná předplatná Azure k entitě, kterou jste vytvořili dříve.
3. Postupujte podle kroků v části [Vytvoření uživatele s přístupem správce](tutorial-user-access.md#create-a-user-with-admin-access) a vytvořte uživatelský účet s přístupem správce. Potom se ujistěte, že má uživatelský účet přístup ke konkrétním entitám, které jste vytvořili dříve pro nepřímý účet.

Nepřímí partneři CSP se k portálu Cloudyn přihlašují pomocí účtů, které jste pro ně vytvořili.


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili své informace o CSP k registraci u Cloudyn. Také jste se přihlásili k portálu Cloudyn a začali jste zobrazovat informace o nákladech. Další informace o Cloudyn najdete v kurzu pro Cloudyn.

> [!div class="nextstepaction"]
> [Kontrola využití a nákladů](tutorial-review-usage.md)
