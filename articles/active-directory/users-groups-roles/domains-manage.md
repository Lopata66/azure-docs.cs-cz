---
title: Přidat a ověřit názvy vlastních domén – Azure Active Directory | Dokumentace Microsoftu
description: Správa koncepty a postupy pro správu názvu domény ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 908ae768ae471ab6f49452c99323c31d34772d45
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60472302"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Správa vlastních názvů domén v Azure Active Directory

Název domény je důležitou součástí identifikátoru pro mnoho prostředků adresáře: je součástí uživatelského jména nebo e-mailové adresy pro uživatele, část adresy pro skupinu a v některých případech je součástí aplikace identifikátor URI ID aplikace. Název domény, který je vlastněn adresáře, který obsahuje prostředek může obsahovat prostředků v Azure Active Directory (Azure AD). Jenom globální správce může spravovat domény ve službě Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Nastavit název domény primárního pro váš adresář Azure AD

Když se do adresáře, počáteční název domény, například "contoso.onmicrosoft.com", je také název primární doménu. Primární doména je výchozí název domény pro nového uživatele, když vytvoříte nového uživatele. Nastavení názvu domény primární zjednodušuje proces pro správce k vytváření nových uživatelů na portálu. Chcete-li změnit primární doména název:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu, který má oprávnění globálního správce adresáře.
2. Vyberte **Azure Active Directory**.
3. Vyberte **Názvy vlastních domén**.
  
   ![Otevřete stránku správy uživatelů](./media/domains-manage/add-custom-domain.png)
4. Vyberte název domény, které mají být primární doménu.
5. Vyberte **nastavit jako primární** příkazu. Potvrďte svou volbu po zobrazení výzvy.
  
   ![Ujistěte se, primární název domény](./media/domains-manage/make-primary-domain.png)

Můžete změnit název primární domény pro váš adresář bude ověřené vlastní doméně, která není Federovaná. Primární doména pro svůj adresář změna neovlivní uživatelské jméno pro všechny stávající uživatele.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Přidání vlastních názvů domén do svého tenanta Azure AD

Můžete přidat až 900 spravovaných názvech domén. Pokud konfigurujete všech domén pro federaci s místní služby Active Directory, můžete přidat až 450 názvy domén v každém adresáři.

## <a name="add-subdomains-of-a-custom-domain"></a>Přidání subdomén vlastní domény

Pokud chcete přidat název domény třetí úrovně jako je například 'europe.contoso.com' do adresáře, byste nejprve přidání a ověření domény druhé úrovně, jako je například contoso.com. Poddomény automaticky ověření pomocí Azure AD. Pokud chcete zobrazit, že je ověřený subdomény, kterou jste přidali, aktualizujte seznam domén v prohlížeči.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Co dělat, když změníte registrátora DNS pro název vaší vlastní domény

Pokud změníte registrátorů DNS, nejsou žádné další konfigurační úlohy ve službě Azure AD. Můžete pokračovat v používání název domény v Azure AD bez přerušení. Pokud používáte vlastní název domény s Office 365, Intune nebo jiných služeb, které spoléhají na vlastních názvů domén ve službě Azure AD, najdete v dokumentaci pro tyto služby.

## <a name="delete-a-custom-domain-name"></a>Odstranit vlastní název domény

Pokud vaše organizace už používá tento název domény nebo pokud budete muset použít tento název domény pomocí jiné služby Azure AD, můžete odstranit vlastní název domény z Azure AD.

Před odstranit vlastní název domény, musíte napřed zajistit, že žádné prostředky ve vašem adresáři Spolehněte se na název domény. Název domény nelze odstranit z adresáře, pokud:

* Každý uživatel má uživatelské jméno, e-mailová adresa nebo adresa proxy serveru, který zahrnuje název domény.
* Žádné skupiny má e-mailová adresa nebo adresa proxy serveru, který zahrnuje název domény.
* Všechny aplikace ve službě Azure AD má aplikace identifikátor URI pro ID, která zahrnuje název domény.

Musíte změnit nebo odstranit takových prostředků ve vašem adresáři Azure AD, než budete moct odstranit vlastní název domény.

### <a name="forcedelete-option"></a>Možnost ForceDelete

Je možné **ForceDelete** názvu domény v [centra pro správu Azure AD](https://aad.portal.azure.com) nebo pomocí [Microsoft Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta). Tyto možnosti použít asynchronní operaci a aktualizujte všechny odkazy z vlastního názvu domény jako "user@contoso.com"na počáteční výchozí název domény, jako"user@contoso.onmicrosoft.com." 

Chcete-li volat **ForceDelete** na webu Azure Portal, musíte zajistit, že existují méně než 1000 odkazy na název domény, a všechny odkazy, kde je zřizovací služba Exchange musí aktualizovat nebo odebrat v [ Centra pro správu Exchange](https://outlook.office365.com/ecp/). To zahrnuje Exchange Mail-Enabled zabezpečení skupin a distribuovaných seznamů; Další informace najdete v tématu [odebírají skupiny zabezpečení s povolenou poštou](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups). Také **ForceDelete** operace nebude úspěšná, pokud je splněna jedna z následujících akcí:

* Koupit domény prostřednictvím služby domény předplatné Office 365
* Jste partner Správa jménem jiného tenanta zákazníka

Byly provedeny následující akce v rámci **ForceDelete** operace:

* Přejmenuje na počáteční výchozí název domény hlavního názvu uživatele, EmailAddress a ProxyAddress uživatelů s odkazy na vlastní název domény.
* Přejmenuje EmailAddress skupiny s odkazy na vlastní název domény na počáteční výchozí název domény.
* Přejmenuje identifierUris aplikací s odkazy na vlastní název domény na počáteční výchozí název domény.

Chyba je vrácena, pokud:

* Počet objektů, které chcete přejmenovat, je větší než 1000
* Jednou z aplikace, které chcete přejmenovat, je aplikace s více tenanty

### <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Proč selhává v odstraňování domény zobrazí se chyba s oznámením, že mám na tento název domény Exchange standardní skupiny?** <br>
**Odpověď:** V současné době určitých skupin, jako jsou skupiny zabezpečení s povolenou poštou a distribuované seznamy byly povolené nástrojem Exchange a muset ručně vyčistit v [správce Exchange System Center (EAC)](https://outlook.office365.com/ecp/). Existuje může být přetrvávání odstraněných ProxyAddresses, které využívají vlastní název domény a bude nutné ručně aktualizovat tak, aby jiný název domény. 

**Otázka: Jsem se přihlášeni jako správce\@contoso.com, ale vybral jsem nelze odstranit název domény "contoso.com"?**<br>
**Odpověď:** Nemůže odkazovat vlastní název domény, který se pokoušíte odstranit v názvu uživatelského účtu. Ujistěte se, že účet globálního správce používá počáteční výchozí název domény (. onmicrosoft.com), jako admin@contoso.onmicrosoft.com. Přihlaste se pomocí jiného globálního správce účtu, který jako admin@contoso.onmicrosoft.com nebo jiný název vlastní domény, jako je "fabrikam.com", které je účet admin@fabrikam.com.

**Otázka: Po klepnutí na tlačítko pro odstranění domény a viz `In Progress` stavu pro operace Delete. Jak dlouho trvá? Co se stane, když dojde k chybě?**<br>
**Odpověď:** Operace odstranění domény je pozadí asynchronní úloha, která přejmenuje všechny odkazy na název domény. By se měla dokončit během několika minut. Pokud odstranění domény selhání, ujistěte se, že není nutné:

* Aplikace nakonfigurované v názvu domény pomocí appIdentifierURI
* Žádné skupina s povolenou poštou odkazovat na název vlastní domény
* Více než 1 000 odkazy na název domény.

Pokud zjistíte, že některá z podmínek nebyly splněny, ručně vyčistit odkazy a zkuste to znovu odstranit doménu.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Pomocí Powershellu nebo rozhraní Graph API ke správě názvů domén

Většina úkolů správy pro názvy domén v Azure Active Directory je možné provést i pomocí Powershellu Microsoft nebo programově pomocí rozhraní Azure AD Graph API.

* [Použití Powershellu ke správě názvů domén ve službě Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Pomocí rozhraní Graph API ke správě názvů domén ve službě Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Další postup

* [Přidání vlastních názvů domén](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Odebrat skupiny zabezpečení s povoleným e-mailu Exchange v Centru pro správu Exchange na vlastní název domény ve službě Azure AD](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [ForceDelete vlastního názvu domény pomocí rozhraní Microsoft Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)
