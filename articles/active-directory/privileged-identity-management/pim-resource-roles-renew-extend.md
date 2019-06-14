---
title: Rozšíření nebo obnovení přiřazení role prostředků Azure v PIM – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak rozšířit nebo prodlužovat platnost přiřazení role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a064fc67bf94ba6aa443e429fe83179d84cada84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602641"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-pim"></a>Rozšíření nebo obnovení přiřazení role prostředků Azure v PIM

Privileged Identity Management (PIM) ve Azure Active Directory (Azure AD) představuje nové ovládací prvky pro správu přístupu a přiřazení životní cyklus pro prostředky Azure. Správci mohou přiřadit členství pomocí vlastnosti počáteční a koncové datum a čas. Když se blíží konec přiřazení PIM odešle e-mailová oznámení k ovlivnění uživatelé nebo skupiny. Rovněž odesílá e-mailová oznámení pro správce prostředků k zajištění, že se udržuje odpovídající přístup. Přiřazení může být obnovena a zůstávají viditelné vypršela platnost po dobu až 30 dnů, i když není rozšířené přístup.

## <a name="who-can-extend-and-renew"></a>Kdo může rozšířit a obnovit?

Pouze správci prostředků mohou rozšířit nebo prodlužovat platnost přiřazení role. Ovlivněné člen požádat o prodloužení role, které se chystáte vypršení platnosti a žádat o obnovení role, které jsou už vypršela.

## <a name="when-are-notifications-sent"></a>Když se odešlou oznámení?

PIM odešle e-mailová oznámení pro správce a ovlivněné členy rolí, které jsou vyprší během 14 dnů a jeden den před vypršením platnosti. Další e-mailu odešle po přiřazení oficiálně vypršení platnosti. 

Správci dostanou oznámení, pokud u nichž vyprší platnost nebo vypršela její platnost rolí požádá o rozšíření nebo obnovení. Po vyřešení určitého správce požadavku všech ostatních správců upozorněni rozhodnutí řešení (schválení nebo zamítnutí). Žádost o člen je pak informováni o rozhodnutí. 

## <a name="extend-role-assignments"></a>Rozšíření přiřazení rolí

Následující kroky popisují proces vyžádání, řešení nebo Správa k rozšíření nebo obnovení přiřazení role. 

### <a name="member-extend"></a>Člen rozšíření

Členy z přiřazení role můžete rozšíření u nichž vyprší platnost přiřazení rolí přímo z **oprávněné** nebo **aktivní** kartě **Moje role** stránky prostředku a z nejvyšší úrovně **Moje role** stránce portálu PIM. Členové můžou požádat o prodloužení oprávněných a aktivních rolí (přiřazené), které vyprší za 14 dní.

![Rozšíření role](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Pokud je datum a čas konce přiřazení do 14 dnů, na tlačítko **rozšířit** stane aktivní odkaz v uživatelském rozhraní. V následujícím příkladu se předpokládá aktuální datum následuje 27. března.

![Rozšíření tlačítka](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Chcete-li požádat o rozšíření přiřazení této role, vyberte **rozšířit** otevřete formulář žádosti.

![Otevřete formulář žádosti](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Chcete-li zobrazit informace o původní přiřazení, rozbalte **podrobnosti o přiřazení**. Zadejte důvod, proč požadavek na rozšíření a pak vyberte **rozšířit**.

>[!Note]
>Doporučujeme vám, včetně podrobnosti, proč je nutné rozšíření, a jak dlouho mají udělit rozšíření (Pokud máte tyto informace).

![Rozšíření přiřazení role](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

Během několika okamžiků správci prostředků dostávat e-mailové oznámení, požadování, že si žádost o rozšíření. Pokud již byla odeslána žádost o rozšíření, zobrazí se v horní části webu Azure portal s vysvětlením, chyba oznámení s informační zprávou.

![Oznámení s vysvětlením chyb](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Přejděte **žádosti čekající na vyřízení** kartu v levém podokně, chcete-li zobrazit stav své žádosti, nebo chcete ho zrušit.

![Žádosti čekající na vyřízení](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approve"></a>Schválení správce

Když člen odešle žádost o rozšíření přiřazení rolí, správci prostředků, dostanete oznámení e-mailu, který obsahuje podrobné informace o původní přiřazení a důvod žádosti. Oznámení obsahuje přímý odkaz na žádost správce schválil nebo zamítl. 

Kromě použití následujícího odkaz z e-mailu, správci mohli schválit nebo odmítnout žádosti o tak, že přejdete na správu PIM portálu a následným výběrem **schvalovat žádosti o** v levém podokně.

![Snímek obrazovky chyby](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Pokud správce vybere **schválit** nebo **Odepřít**, jsou uvedeny podrobnosti žádosti, spolu s polem zadávat odůvodnění k protokolům auditu.

![Schválit žádost o přiřazení role](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Při schválení žádosti o rozšíření přiřazení role, prostředek mohou správci nové datum zahájení, datum ukončení a typ přiřazení. Změna přiřazení typu může být nutné v případě, že správce chce omezený přístup k dokončení úkolu (jeden den, například). V tomto příkladu může správce změnit přiřazení z **oprávněné** k **aktivní**. To znamená, že můžete poskytují přístup žadateli bez nutnosti jejich nutnost aktivovat.

### <a name="admin-extend"></a>Rozšíření Správce

Pokud člen role zapomene nebo nemůže požádat o prodloužení členství role, správce můžete rozšířit přiřazení jménem člena. Administrativní rozšíření členství role nevyžadují schválení, ale oznámení se posílají do všech ostatních správců po role byla prodloužena.

Rozšíření členství v roli, přejděte do zobrazení prostředků roli nebo člen v PIM. Najdete člena, který vyžaduje rozšíření. Potom vyberte **rozšířit** ve sloupci Akce.

![Rozšíření členství v roli](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Prodlužovat platnost přiřazení role

I když je koncepčně podobá procesu žádosti o rozšíření, se proces obnovení přiřazení role vypršela platnost. Pomocí následujících kroků, můžou členové a správci obnovit přístup k platnost rolí, pokud je to nezbytné.

### <a name="member-renew"></a>Člen obnovení

Členové, kteří už přístup k prostředkům můžete přistupovat až 30 dní historie vypršela platnost přiřazení. Uděláte to tak, že při procházení **Moje role** v levém podokně a pak vyberte **platnost rolí** kartu v části role prostředků Azure.

![Karta platnost rolí](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

Seznam rolí, které jsou uvedené výchozí hodnota je **oprávněných rolí**. Pomocí rozevírací nabídky můžete přepnout mezi oprávněných a aktivních přiřazené role.

Chcete-li požádat o prodloužení platnosti pro některý z přiřazení rolí v seznamu, vyberte **obnovit** akce. Zadejte důvod pro daný požadavek. Je vhodné zadat dobu trvání kromě jakékoli další kontext, který pomáhá správce prostředků se rozhodnete schválit nebo zamítnout.

![Obnovení přiřazení role](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Po odeslání žádosti jsou správci prostředků upozorněni na čekající žádosti o obnovení přiřazení role.

### <a name="admin-approves"></a>Schválí správce

Správci prostředků mohou přistupovat k žádost o obnovení z odkazu v e-mailových oznámení nebo přístupu k PIM z webu Azure portal a výběrem **schvalovat žádosti o** v levém podokně.

![Schvalování žádostí](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Pokud správce vybere **schválit** nebo **Odepřít**, jsou uvedeny podrobnosti požadavku spolu s polem zadávat odůvodnění pro protokoly auditu.

![Schválit přiřazení role](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Při schválení žádosti o obnovení přiřazení role, správce prostředků musíte zadat nové počáteční datum, koncové datum a typ přiřazení. 

### <a name="admin-renew"></a>Obnovení správce

Správci prostředků můžete obnovit přiřazení role s vypršenou platností ze **členy** kartu v levé navigační nabídce prostředku. Jsou také prodlužovat platnost přiřazení vypršela platnost rolí v rámci **neplatné** kartu role prostředku role.

Chcete-li zobrazit seznam všech skončila přiřazení rolí **členy** obrazovky, vyberte **platnost rolí**.

![Platnost rolí](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Další postup

- [Schválit nebo zamítnout žádosti pro role prostředků Azure v PIM](pim-resource-roles-approval-workflow.md)
- [Konfigurace nastavení role prostředků Azure v PIM](pim-resource-roles-configure-role-settings.md)
