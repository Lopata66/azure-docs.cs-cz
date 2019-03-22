---
title: Azure Multi-Factor Authentication – jak funguje – Azure Active Directory
description: Azure Multi-Factor Authentication pomáhá chránit přístup k datům a aplikacím a současně plní požadavky uživatelů na jednoduchý proces přihlašování.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7328fb958774b5e17511d046e914cc5612e8a96d
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310927"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Jak to funguje: Azure Multi-Factor Authentication

Zabezpečení dvoustupňové ověřování spočívá v jeho vrstveného přístupu. Tím bylo narušeno několika faktory ověřování představuje velkou výzvou pro útočníky. I v případě, že útočník dokázal další heslo uživatele, je zbytečné bez nutnosti vlastnictví dodatečnou metodu ověřování. Funguje tak, že vyžaduje dva nebo více z následujících metod ověřování:

* Něco víte (obvykle heslo)
* Něco, co máte (důvěryhodné zařízení, které není lehké duplikovat, jako je telefon)
* Něco, co že je (vaše biometrika)

<center>

![Koncepční ověřovací metody image](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) pomáhá chránit přístup k datům a aplikacím při zachování jednoduchosti pro uživatele. Poskytuje dodatečné zabezpečení vyžadováním druhou formu ověřování a poskytuje silné ověřování přes celou řadu snadno použitelné [metody ověřování](concept-authentication-methods.md). Uživatelé můžou nebo nemusí být ovlivňuje vícefaktorové ověřování na základě konfigurace rozhodnutí, které provádí správce.

## <a name="how-to-get-multi-factor-authentication"></a>Jak získat ověření službou Multi-Factor Authentication?

Ověřování službou Multi-Factor Authentication je součástí následujících nabídek:

* **Licence Azure Active Directory Premium** -plně vybavené použití služby Azure Multi-Factor Authentication (Cloud) nebo ověřování Azure Multi-Factor Authentication Server (místní).
   * **Služba Azure MFA (Cloud)** - **tato možnost je doporučený pro nová nasazení**. Azure MFA v cloudu vyžaduje žádnou místní infrastrukturu a je možné s uživateli federované nebo jenom pro cloud.
   * **Azure MFA Server** – Pokud vaše organizace potřebuje ke správě prvky přidružené infrastruktury a nasazena služba AD FS ve vašem místním prostředí tímto způsobem může být možnost.
* **Multi-Factor Authentication pro Office 365** -podmnožinu funkcí Azure Multi-Factor Authentication jsou k dispozici jako součást vašeho předplatného. Další informace o vícefaktorové ověřování pro Office 365, najdete v článku [plánování ověřování službou Multi-Factor Authentication pro Office 365 nasazení](https://support.office.com/article/plan-for-multi-factor-authentication-for-office-365-deployments-043807b2-21db-4d5c-b430-c8a6dee0e6ba).
* **Azure Active Directory globální správci** -podmnožinu funkcí Azure Multi-Factor Authentication jsou k dispozici jako prostředek k ochraně účty globálních správců.

> [!NOTE]
> Noví zákazníci už koupit Azure Multi-Factor Authentication jako samostatná nabízí efektivní dne 1. května 2018. Ověřování službou Multi-Factor Authentication bude i nadále k dispozici funkce v licence Azure AD Premium.

## <a name="supportability"></a>Možnosti podpory

Protože většina uživatelů jsou zvyklí používat jenom hesla pro ověření, je důležité, že vaše organizace komunikuje se všichni uživatelé týkající se tohoto procesu. Sledování může snížit pravděpodobnost, že uživatelé volat vaše oddělení technické podpory pro menší problémy související s MFA. Existují však některé scénáře, kde dočasným zákazem MFA je nezbytné. Chcete-li pochopit, jak zvládnout tyto scénáře pomocí následujících pokynů:

* Trénování pracovníci podpory pro zpracování scénářů, kde uživatel nemůže přihlásit, protože nemají přístup ke své metody ověřování, nebo nejsou správně funguje.
   * Pomocí zásad podmíněného přístupu pro službu Azure MFA, pracovníci podpory můžete přidat uživatele do skupiny, která je vyloučena ze zásad, které vyžadují vícefaktorové ověřování.
   * Pracovníci podpory zákazníků můžete povolit dočasné jednorázové přihlášení pro uživatele Azure MFA Server chcete, aby uživatel možné ověřit bez dvoustupňové ověřování. Jednorázové přihlášení je dočasné a vyprší po zadaném počtu sekund.   
* Zvažte použití důvěryhodné IP adresy nebo pojmenovaná umístění jako způsob, jak minimalizovat dvoustupňové ověřování výzvy. Pomocí této funkce můžou správci tenanta spravované nebo federované obejít dvoustupňové ověřování pro uživatele, kteří se přihlašují ze důvěryhodné síťové umístění, například intranetu organizace.
* Nasazení [Azure AD Identity Protection](../active-directory-identityprotection.md) a aktivovat dvoustupňové ověřování založené na rizikové události.

## <a name="next-steps"></a>Další postup

- Získat podrobné MFA [plán nasazení](https://aka.ms/MFADeploymentPlan)

- Přečtěte si podrobnosti o [licencování uživatelů](concept-mfa-licensing.md).

- Získejte podrobné informace o tom, [kterou verzi nasadit](concept-mfa-whichversion.md).

- Získejte odpovědi na [nejčastější dotazy](multi-factor-authentication-faq.md).
