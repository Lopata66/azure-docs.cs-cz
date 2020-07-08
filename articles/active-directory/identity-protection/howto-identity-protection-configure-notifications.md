---
title: Oznámení Azure Active Directory Identity Protection
description: Přečtěte si, jak oznámení podporují vaše aktivity šetření.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9090ca5b8057179b0cbef1d0a87ae563303ed2c1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85130428"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Oznámení Azure Active Directory Identity Protection

Azure AD Identity Protection posílá dva typy automatických e-mailů s oznámením, které vám pomůžou se správou rizik uživatelů a detekcí rizik:

- E-mail zjištěných uživatelů v nebezpečí
- Týdenní e-mail pro výtah

Tento článek poskytuje přehled e-mailů s oznámením.

## <a name="users-at-risk-detected-email"></a>E-mail zjištěných uživatelů v nebezpečí

V reakci na zjištěný účet hrozí Azure AD Identity Protection vygenerovat e-mailové upozornění s **uživateli s rizikem zjištěným** jako předmětem. E-mail obsahuje odkaz na sestavu **[Uživatelé označení příznakem rizika](../reports-monitoring/concept-user-at-risk.md)** . Osvědčeným postupem je okamžitě prozkoumat riziko pro uživatele.

Konfigurace pro tuto výstrahu vám umožní určit, na jakou úroveň rizika uživatele má být výstraha vygenerována. E-mail se vygeneruje, když úroveň rizika uživatele dosáhne toho, co jste zadali. Pokud jste například nastavili zásady na výstrahu středně velkému uživateli a skóre rizika uživatele od uživatele Jan se z důvodu rizika přihlášení v reálném čase přesune na střední riziko, obdržíte uživateli zjištěné riziko. Pokud má uživatel následnou detekci rizik, která by způsobila, že výpočet úrovně rizika uživatele bude představovat zadanou úroveň rizika (nebo vyšší), obdržíte při přepočítání skóre uživatele zjištěné e-maily další uživatelé s riziky zjištěnými uživateli. Pokud se například uživatel přesune k střednímu riziku 1. ledna, obdržíte e-mailové oznámení, pokud je u nastavení nastaveno výstrahy na střední riziko. Pokud má stejný uživatel další detekci rizik na 1. lednu, což je také střední riziko, a skóre rizika uživatele se přepočítá a je stále střední, obdržíte další e-mailové oznámení. 

Další e-mailové oznámení se ale pošle jenom v případě, že čas, kdy došlo k detekci rizika (která způsobila změnu na úrovni rizika uživatele), je novější než poslední e-mail byl odeslán. Například přihlášení uživatele od 1. ledna v 9:00 a nehrozí riziko v reálném čase (což znamená, že z důvodu tohoto přihlášení nebylo vygenerováno žádné e-maily). Deseti minut 5:10 později se stejný uživatel přihlásí znovu a má vysoké riziko v reálném čase, což způsobí, že se úroveň rizika uživatele přesune na vysokou a pošle se e-mail. V 5:15 ráno se v případě nedostatku do offline režimu projeví rizikové skóre pro původní přihlášení v 5. Další uživatel označený příznakem pro rizikovou e-maily se neposílá, protože čas prvního přihlášení byl před druhým přihlášením, které už spustilo e-mailové oznámení.

Aby nedocházelo k přetížení e-mailů, obdržíte jenom jednoho uživatele, u kterého se zjistilo riziko v 5 sekundách. To znamená, že pokud více uživatelů přejde na zadanou úroveň rizika během stejného 5 sekundového období, agregujeme a pošleme jeden e-mail, který bude reprezentovat změnu na úrovni rizika pro všechny z nich.

![E-mail zjištěných uživatelů v nebezpečí](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Konfigurace výstrah zjištěných uživateli v případě rizik

Jako správce můžete nastavit:

- **Úroveň rizika uživatele, která aktivuje generování tohoto e-mailu** – ve výchozím nastavení je úroveň rizika nastavena na "vysoké" riziko.
- **Příjemci tohoto e-mailu** – ve výchozím nastavení mají příjemci všechny globální správce. Globální správci můžou také přidat další globální správce, správce zabezpečení, čtenáře zabezpečení jako příjemce.
   - Volitelně můžete **Přidat další e-maily pro příjem oznámení o výstrahách** . Tato funkce je verze Preview a uživatelé musí mít příslušná oprávnění k zobrazení propojených sestav v Azure Portal.

V **Azure Portal** v části **Azure Active Directory**  >  **zabezpečení**  >  **identity ochrany**  >  **uživatelů při zjištěných výstrahách**nakonfigurujte e-mailová rizika uživatelů.

## <a name="weekly-digest-email"></a>Týdenní e-mail pro výtah

Týdenní e-mailová zpráva o Digest obsahuje souhrn nových detekcí rizik.  
Obsahuje:

- Zjištěni noví rizikové uživatelé
- Zjištěna nová riziková přihlášení (v reálném čase)
- Odkazy na související sestavy v Identity Protection

![Týdenní e-mail pro výtah](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

Ve výchozím nastavení mají příjemci všechny globální správce. Globální správci můžou také přidat další globální správce, správce zabezpečení, čtenáře zabezpečení jako příjemce.

### <a name="configure-weekly-digest-email"></a>Konfigurace týdenního e-mailu pro vydigest

Jako správce můžete zapnout nebo vypnout odesílání e-mailů na týden Digest a vybrat uživatele, kteří mají přiřazený příjem e-mailů.

V **Azure Portal** v části **Azure Active Directory**  >  **zabezpečení**  >  **identity**Security –  >  **týdenní výtah**nakonfigurujte e-maily s týdenním shrnutím.

## <a name="see-also"></a>Viz také

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
