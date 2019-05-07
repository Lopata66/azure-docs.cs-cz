---
title: Přidejte jednotlivě licencovaných uživatelů na licencování na základě skupin – Azure Active Directory | Dokumentace Microsoftu
description: Jak migrovat z jednotlivých uživatelských licencí pro licencování na základě skupiny pomocí služby Azure Active Directory
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333f0ae0153073b57740446ecf47e36a1f9ce590
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192460"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Migrace uživatelů s licencemi na jednotlivé do skupin pro licencování

Máte stávající licence k nasazení uživatelům v organizacích prostřednictvím "přímého přiřazení"; To znamená pomocí skriptů Powershellu nebo jiných nástrojů k přiřazení licencí jednotlivým uživatelům. Než začnete používat licencování na základě skupiny pro správu licencí ve vaší organizaci, můžete tento plán migrace bez problému nahradit stávající řešení s licencováním na základě skupin.

Nejdůležitějším principem brát v úvahu je, že byste se měli vyhnout situace, kdy migrace na licencování na základě skupiny způsobí uživatele dočasně ztráty aktuálně přiřazené licence. Chcete-li odebrat riziko ztráty přístupu ke službám a jejich data uživatelů, mělo by se vyhnout jakýkoli proces, který může mít za následek odebrání licencí.

## <a name="recommended-migration-process"></a>Proces migrace doporučené

1. Máte existující automatizace (například prostředí PowerShell) Správa licencí přiřazení a odebrání pro uživatele. Necháte spuštěné, jak je.

2. Vytvoření nové skupiny licencí (nebo rozhodnout, které existující skupiny, pro použití) a ujistěte se, že všechny požadované uživatelé přidávají jako členové.

3. Přiřadit požadované licence, které na tyto skupiny. vaším cílem mělo být tak, aby odrážely stejné licencování stavu, ve kterém se těmto uživatelům použití existující automatizace (například prostředí PowerShell).

4. Ověřte, že licence byla použita pro všechny uživatele v těchto skupinách. Tuto aplikaci můžete udělat tak, že zkontrolujete stav zpracování pro každou skupinu a kontrolou protokolů auditu.

   - Můžete místo zaškrtnutí jednotlivých uživatelů podle jejich podrobnosti licence. Uvidíte, že mají stejné licence přiřazené "přímo" a "dědí" ze skupin.

   - Můžete spustit skript prostředí PowerShell, který [ověřte přiřazení licencí uživatelům](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Pokud licenci na stejný produkt je přiřazená uživateli i přímo nebo prostřednictvím skupiny, pouze jedna licence se spotřebovává uživatelem. Proto žádné další licence jsou potřebná k provedení migrace.

5. Ověřte, že se žádná přiřazení licencí se nezdařilo kontrolou každé skupiny uživatelů v chybovém stavu. Další informace najdete v tématu [určení a řešení problémů pro skupinu](licensing-groups-resolve-problems.md).

6. Zvažte odebrání původní přímého přiřazení můžete chtít provést postupně, "vlny", nejprve sledovat výsledek na určitou podskupinu uživatelů.

   Původní přímých přiřazení by mohl nechat na uživatele, ale při jejich licencované skupiny nechte uživatele, zůstanou původní licence, který může být nechcete.

## <a name="an-example"></a>Příklad

Organizace má 1000 uživateli současně. Všichni uživatelé potřebovat licencí Enterprise Mobility + Security (EMS). 200 uživatelů jsou z finančního oddělení a vyžadují licence Office 365 Enterprise E3. Organizace má momentálně Powershellový skript spuštěn v místním prostředí, přidávání a odebírání licence od uživatelů, dokud budou pocházet a přejít. Organizace potřebuje licencování založené na skupině, tak licence je možné spravovat pomocí služby Azure AD automaticky nahradit skriptu.

Tady je proces migrace by mohla vypadat:

1. Pomocí webu Azure portal, přiřadit licence EMS pro **všichni uživatelé** skupiny ve službě Azure AD. Přiřadit licenci E3, abyste **finančního oddělení** skupinu, která obsahuje všechny požadované uživatele.

2. Pro každou skupinu potvrďte, že byla dokončena přiřazení licencí pro všechny uživatele. Přejděte do okna pro každou skupinu, vyberte **licence**a zjistit stav zpracování v horní části **licence** okno.

   - Vyhledejte "Licence nejnovější změny se použily pro všechny uživatele" potvrďte zpracování bylo dokončeno.

   - Hledejte v horní části o všichni uživatelé, pro které licence možná nebyl přiřazen úspěšně oznámení. Jsme dostatek licencí pro některé uživatele? Mají někteří uživatelé konfliktní licence skladových položek, které jim zabránit dědění licencí skupiny?

3. Přímé zkontrolujte někteří uživatelé ověřit, že mají obě s přímým přístupem a skupinových licence použít. Přejděte do okna pro uživatele, vyberte **licence**a zkontrolovat stav licencí.

   - Tento stav byl očekáván typ user je během migrace:

      ![byl očekáván typ user stavu během migrace](./media/licensing-groups-migrate-users/expected-user-state.png)

   Tím potvrdíte, že uživatel má licence s přímým přístupem a zděděná. Vidíme, že oba **EMS** a **E3** jsou přiřazeny.

   - Vyberte každou licenci k zobrazení podrobností o povolené služby. To lze použít ke kontrole, pokud licence s přímým přístupem a skupinových povolit přesně stejné plánů služeb pro uživatele.

      ![Zkontrolujte plány služeb pro uživatele](./media/licensing-groups-migrate-users/check-service-plans.png)

4. Po potvrzení, zda jsou ekvivalentní s přímým přístupem a skupiny licencí, můžete začít odebrání přímé licencí od uživatelů. Můžete otestovat tak, že odeberete pro jednotlivé uživatele na portálu a potom spustit skripty pro automatizaci nechat na nich odebrat hromadně. Tady je příklad stejného uživatele s licencí s přímým přístupem odebrat na portálu. Všimněte si, že stav licence zůstane beze změny, ale už vidíme přímých přiřazení.

   ![Potvrďte, že se odeberou přímé licence](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Další postup

Další informace o další scénáře pro správu licencí pomocí skupin najdete v článku

* [Co je skupina založená na licencování v Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Přiřazení licencí ke skupině v Azure Active Directory](licensing-groups-assign.md)
* [Identifikace a řešení potíží s licencemi pro skupinu v Azure Active Directory](licensing-groups-resolve-problems.md)
* [Migrace uživatelů mezi licencemi produktů pomocí licencování pro skupiny ve službě Azure Active Directory](licensing-groups-change-licenses.md)
* [Další scénáře licencování na základě skupin v Azure Active Directory](licensing-group-advanced.md)
* [Příklady prostředí PowerShell pro licencování na základě skupiny v Azure Active Directory](licensing-ps-examples.md)
