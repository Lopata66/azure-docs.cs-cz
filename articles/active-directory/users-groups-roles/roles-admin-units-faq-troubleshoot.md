---
title: Řešení potíží s jednotkami pro správu a nejčastější dotazy – Azure Active Directory | Microsoft Docs
description: Prozkoumejte jednotky pro správu a udělte oprávnění s omezeným rozsahem v Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 022658306d6e4d69174cc616d230cfe4892f1204
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684857"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Jednotky pro správu Azure AD: řešení potíží a nejčastější dotazy

Pro přesnější administrativní řízení v Azure Active Directory (Azure AD) můžete přiřadit uživatele k roli Azure AD s oborem, který je omezený na jednu nebo více jednotek pro správu (Austrálie). Ukázkové skripty PowerShellu pro běžné úlohy najdete v tématu [práce s jednotkami pro správu](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Proč mi nelze vytvořit jednotku pro správu?**

**A:** Ve službě Azure AD může vytvořit pouze *globální správce* nebo *správce privilegovaných rolí* . Zkontrolujte, jestli se uživatel, který se pokouší vytvořit jednotku pro správu, přiřadí roli správce *globálního správce* nebo *privilegované role* .

**Otázka: Přidal (a) jsem skupinu do jednotky pro správu. Proč se členové skupiny ještě nezobrazuje?**

**A:** Když přidáte skupinu do jednotky pro správu, která nevede k tomu, že se do ní přidávají členové skupiny. Uživatelé musí být přiřazeni přímo do jednotky pro správu.

**Otázka: právě jste přidali (nebo odebrali) člena jednotky pro správu. Proč se člen v uživatelském rozhraní nezobrazuje (nebo se stále zobrazuje)?**

**A:** V některých případech může zpracování přidávání nebo odebírání jednoho nebo více členů jednotky pro správu trvat několik minut, než se na stránce **jednotky pro správu** projeví. Alternativně můžete přejít přímo k vlastnostem přidruženého prostředku a zjistit, zda byla akce dokončena. Další informace o uživatelích a skupinách ve službě Austrálie najdete v tématu [Seznam jednotek pro správu pro uživatele](roles-admin-units-add-manage-users.md) a [Seznam jednotek pro správu pro skupinu](roles-admin-units-add-manage-groups.md).

**Otázka: jsem delegovaný správce hesel na jednotce pro správu. Proč se mi nepovedlo resetovat heslo konkrétního uživatele?**

**A:** Jako správce jednotky pro správu můžete hesla resetovat pouze pro uživatele, kteří jsou přiřazeni k jednotce pro správu. Ujistěte se, že uživatel, jehož resetování hesla selhává, patří do jednotky pro správu, ke které jste byli přiřazeni. Pokud uživatel patří do stejné jednotky pro správu, ale stále nemůžete resetovat heslo, ověřte role přiřazené uživateli. 

Aby se zabránilo zvýšení oprávnění, správce s rozsahem jednotky pro správu nemůže resetovat heslo uživatele, který je přiřazen k roli s rozsahem celé organizace.

**Otázka: Proč jsou jednotky pro správu nezbytné? Nemohli jsme použít skupiny zabezpečení jako způsob definování oboru?**

**A:** Skupiny zabezpečení mají existující účel a autorizační model. *Správce uživatele*může například spravovat členství všech skupin zabezpečení v organizaci Azure AD. Role může používat skupiny ke správě přístupu k aplikacím, jako je Salesforce. *Správce uživatele* by neměl být schopný spravovat samotný model delegování, což by vedlo k tomu, že se skupiny zabezpečení rozšířily na podporu scénářů seskupení prostředků. Jednotky pro správu, například organizační jednotky ve službě Windows Server Active Directory, mají sloužit jako způsob určení rozsahu správy široké škály objektů adresáře. Samotné skupiny zabezpečení můžou být členy oborů prostředků. Použití skupin zabezpečení k definování sady skupin zabezpečení, které může správce spravovat, může být matoucí.

**Otázka: co znamená přidat skupinu do jednotky pro správu?**

**A:** Přidání skupiny do jednotky pro správu přinese skupinu do rozsahu správy libovolného *správce uživatele* , který je také vymezen pro tuto jednotku správy. Správci uživatelů jednotky pro správu mohou spravovat název a členství samotné skupiny. Neuděluje *správcům* oprávnění k administrativní jednotce oprávnění ke správě uživatelů skupiny (například k resetování hesla). Chcete-li *uživateli* udělit možnost spravovat uživatele, musí být uživatelé přímo členy jednotky pro správu.

**Otázka: může být prostředek (uživatel nebo skupina) členem více než jedné jednotky pro správu?**

**A:** Ano, prostředek může být členem více než jedné jednotky pro správu. Prostředek může být spravovaný všemi správci v rámci celé organizace a administrativními jednotkami, kteří mají oprávnění k prostředku.

**Otázka: jsou v B2C organizacích dostupné jednotky pro správu?**

**A:** Ne, administrativní jednotky nejsou pro organizace B2C k dispozici.

**Otázka: jsou podporované vložené jednotky pro správu?**

**A:** Ne, vnořené jednotky pro správu se nepodporují.

**Otázka: jsou jednotky pro správu podporované v PowerShellu a v Graph API?**

**Odpověď:** Ano. Najdete podporu pro jednotky pro správu v [dokumentaci k rutinám prostředí PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) a v [ukázkových skriptech](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview). 

V Microsoft Graph najdete podporu pro [typ prostředku administrativeUnit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit) .

## <a name="next-steps"></a>Další kroky

- [Omezení rozsahu pro role pomocí jednotek pro správu](directory-administrative-units.md)
- [Spravovat jednotky pro správu](roles-admin-units-manage.md)
