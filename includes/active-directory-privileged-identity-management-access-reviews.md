---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 05/26/2020
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: ee02200f1fb32d430a858d218e27534561ebc24a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83868013"
---
## <a name="create-one-or-more-access-reviews"></a>Vytvoření jedné nebo více kontrol přístupu

1. Kliknutím na **Nový** vytvořte novou kontrolu přístupu.

1. Pojmenujte kontrolu přístupu. Volitelně můžete zadat popis revize. Název a popis se zobrazí kontrolorům.

    ![Vytvoření kontroly přístupu – kontrola názvu a popisu](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Nastavte **počáteční datum**. Ve výchozím nastavení proběhne kontrola přístupu jednou, začíná ve stejnou dobu, kdy je vytvořena, a končí v jednom měsíci. Počáteční a koncové datum můžete změnit tak, aby byla kontrola přístupu zahájena v budoucnosti a poslední, ale kolik dní potřebujete.

    ![Počáteční datum, četnost, doba trvání, konec, počet a datum a čas ukončení](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Chcete-li provést opakované kontroly přístupu, změňte nastavení **frekvence** od **jednoho** na **týdně**, **měsíčně**, **čtvrtletně**, **jednou ročně**nebo **Semi-annually**po měsících. Pomocí posuvníku **Trvání** nebo textového pole můžete definovat, kolik dní bude každá revize opakujících se řad otevřená pro vstup od revidujících. Například maximální doba, kterou můžete nastavit pro měsíční revizi, je 27 dní, aby se předešlo překrývání recenzí.

1. Pomocí nastavení **konec** určete, jak se má ukončit řada kontroly opakovaného přístupu. Série může končit třemi způsoby: průběžně spouští recenze na neomezenou dobu, až do konkrétního data nebo po dokončení definovaného počtu výskytů. Vy, jiný správce nebo jiný globální správce může série po vytvoření zastavit změnou data v **Nastavení**, takže skončí k tomuto datu.

1. V části **Uživatelé** vyberte jednu nebo více rolí, u kterých chcete zkontrolovat členství.

    ![Obor uživatelů pro kontrolu členství v rolích](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > - Role, které tady vyberete, zahrnují [trvalé i oprávněné role](../articles/active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md).
    > - Když vyberete víc než jednu roli, vytvoří se víc kontrol přístupu. Když například vyberete pět rolí, vytvoří se pět samostatných revizí přístupu.

    Pokud vytváříte kontrolu přístupu pro **role Azure AD**, následující příklad uvádí seznam kontrol členství.

    ![V podokně členství zobrazit seznam rolí Azure AD můžete vybrat](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Pokud vytváříte kontrolu přístupu pro **role prostředků Azure**, zobrazí se na následujícím obrázku příklad seznamu členů kontroly.

    ![V podokně členství zobrazit seznam rolí prostředků Azure, které můžete vybrat](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. V části **revidujícíi** vyberte jednu nebo více lidí pro kontrolu všech uživatelů. Nebo můžete vybrat, aby členové zkontrolovali svůj vlastní přístup.

    ![Seznam revidujících vybraných uživatelů nebo členů (osoba)](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Vybraní uživatelé** – tuto možnost použijte, Pokud nevíte, kdo potřebuje přístup. Pomocí této možnosti můžete k dokončení této revize přiřadit vlastníka prostředku nebo správce skupin.
    - **Členové (vlastní)** – tuto možnost použijte, pokud chcete, aby uživatelé zkontrolovali vlastní přiřazení rolí.

### <a name="upon-completion-settings"></a>Nastavení po dokončení

1. Chcete-li určit, co se stane po dokončení kontroly, rozbalte část **nastavení po dokončení** .

    ![Automatické použití nastavení po dokončení a kontrola, že nereagují](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Pokud chcete automaticky odebrat přístup pro uživatele, kterým bylo odepřeno, nastavte **automatické použití výsledků na prostředek** , aby **bylo možné povolit**. Pokud chcete výsledky použít ručně po dokončení kontroly, nastavte přepínač na **Zakázat**.

1. Seznam **by měl kontrolor bez odpovědi** použít k určení toho, co se stane pro uživatele, kteří kontrolor v rámci období revize nekontroloval. Toto nastavení nemá vliv na uživatele, kteří byli zkontrolováni ručně. Pokud je posledním rozhodnutím kontrolora zamítnutí, bude přístup uživatele odebrán.

    - **Žádná změna** – opuštění přístupu uživatele nezměněné
    - **Odebrání přístupu** – odebrání přístupu uživatele
    - **Schválit přístup** – schválení přístupu uživatele
    - **Využijte doporučení** – Vezměte v úvahu doporučení systému při odepření nebo schvalování trvalého přístupu uživatele.

### <a name="advanced-settings"></a>Upřesnit nastavení

1. Chcete-li zadat další nastavení, rozbalte oddíl **Upřesnit nastavení** .

    ![Rozšířená nastavení pro možnost zobrazit doporučení, vyžadovat důvod při schválení, e-mailová oznámení a připomenutí](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Nastavením **Zobrazit doporučení** **umožníte, aby se** recenzenti zobrazovala doporučení k systému na základě informací o přístupu uživatele.

1. Nastavte **vyžadovat důvod schválení** , aby mohl uživatel vyžadovat **, aby kontrolor** zadal důvod schválení.

1. Nastavte e- **mailová oznámení** , která **umožní** , aby služba Azure AD odesílala e-mailová oznámení kontrolorům při zahájení kontroly přístupu a správcům, když se kontrola dokončí.

1. Nastavením **připomenutí** **umožníte** , aby služba Azure AD odesílala připomenutí kontrol přístupu, která nedokončila jejich kontrolu.
