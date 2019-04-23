---
title: Identita synchronizace a odolnost duplicitních atributů | Dokumentace Microsoftu
description: Nové chování, jak zpracovat objekty s hlavní název uživatele nebo ProxyAddress konflikty během synchronizace adresářů přes Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a65af5a5ea0629b617c4e736d8c110cbb9aa540c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60348764"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Synchronizace identit a odolnost duplicitních atributů
Odolnost duplicitních atributů je funkce v Azure Active Directory, ke které dojde k odstranění řešit zádrhele spojené s způsobené **UserPrincipalName** a **ProxyAddress** je v konfliktu při spuštění jedné od Microsoftu Nástroje pro synchronizaci.

Tyto dva atributy se obvykle musí být jedinečný mezi všemi **uživatele**, **skupiny**, nebo **kontakt** objekty v daném tenantu Azure Active Directory.

> [!NOTE]
> Jenom uživatelé mají UPN.
> 
> 

Nové chování, která umožňuje tato funkce je v cloudové části kanálu synchronizace, a proto je klient nezávislá a jsou relevantní pro libovolný produkt synchronizace společnosti Microsoft, včetně služby Azure AD Connect, nástroje DirSync a MIM + konektoru. Obecný pojem "synchronizačního klienta" je v tomto dokumentu používá k reprezentování některou z těchto produktů.

## <a name="current-behavior"></a>Aktuální chování
Při pokusu o zřízení nového objektu s hodnotou hlavní název uživatele nebo ProxyAddress, který porušuje tato omezení jedinečnosti blokuje služby Azure Active Directory tohoto objektu bránit vytváření. Podobně pokud objekt je aktualizován nejedinečné hlavní název uživatele nebo ProxyAddress, aktualizace se nezdaří. Pokus o zajišťování nebo aktualizace proběhne při každém cyklu export klientem synchronizace a i nadále fungovat, dokud bude konflikt vyřešen. Chyba sestavy e-mailu je generována při každém pokusu o a je zaznamenána chyba klientem synchronizace.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Chování s odolnost duplicitních atributů
Místo úplně neúspěšné ke zřízení nebo aktualizaci objektu s duplicitní atribut, Azure Active Directory "umístí do karantény" duplicitní atribut, který by mohla narušit omezení jedinečnosti. Pokud tento atribut je vyžadován pro zřizování, jako jsou UserPrincipalName, služba přiřadí hodnotu zástupného symbolu. Formát tyto dočasné hodnoty  
"***\<OriginalPrefix > +\<4DigitNumber >\@\<InitialTenantDomain >. onmicrosoft.com***".  
Pokud atribut není vyžadována, třeba **ProxyAddress**, Azure Active Directory jednoduše umístí do karantény atribut konflikt a pokračuje v objektu vytvoření nebo aktualizace.

Při umístění do karantény atribut, odesílají informace o konflikt stejné chybě sestav e-mailu používat staré chování. Ale tyto informace se zobrazí jenom v chybové zprávě jednou, když se stane o karanténě, ho nepokračuje má být zaznamenána v budoucích e-mailů. Navíc protože úspěšném exportu pro tento objekt synchronizačního klienta neprotokoluje chybu a neopakuje vytvořit nebo aktualizovat operace při následné synchronizaci cykly.

Podporující toto chování byl přidán nový atribut k objektu třídy uživatelů, skupin a kontaktů:  
**DirSyncProvisioningErrors**

To je více Vážíme si toho atribut, který se používá k ukládání konfliktní atributy, které by mohla narušit omezení jedinečnosti se přidaly normálně. Úlohy na pozadí časovač je povolen v Azure Active Directory, která se spouští každou hodinu hledat duplicitní atribut je v konfliktu, které byly vyřešeny a automaticky odstraní dotyčný atributy z karantény.

### <a name="enabling-duplicate-attribute-resiliency"></a>Povolení odolnost duplicitních atributů
Odolnost duplicitních atributů bude toto nové chování u všech tenantů Azure Active Directory. Bude na ve výchozím nastavení pro všechny tenanty, které povolena synchronizace poprvé 22. srpna 2016 nebo novější. Klienty, kteří povolili synchronizaci před tímto datem bude mít zapnutou v dávkách. Zavedení této opravy bude zahájeno v září 2016 a e-mailové oznámení se odešlou na každého tenanta technická oznámení kontaktu s konkrétní datum, kdy bude povolena funkce.

> [!NOTE]
> Jakmile bylo zapnuto odolnost proti chybám duplicitní atribut nejde zakázat.

Pokud chcete zkontrolovat, pokud je funkce pro vašeho tenanta, můžete tak učinit stažení nejnovější verze modulu Azure Active Directory PowerShell a spuštěním:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Rutina Set-MsolDirSyncFeature už můžete aktivně povolit funkci duplicitní atribut odolnost předtím, než je zapnutý pro vašeho tenanta. Aby bylo možné otestovat funkci, musíte vytvořit nového tenanta Azure Active Directory.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identifikaci objektů s DirSyncProvisioningErrors
Nyní existují dvě metody k identifikaci objektů, které mají tyto chyby z důvodu duplicitní vlastnost je v konfliktu, Azure Active Directory PowerShell a [centra pro správu služeb Microsoft 365](https://admin.microsoft.com). Existují plány můžete rozšířit do další portálu závislosti vytváření sestav v budoucnu.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
Pro rutiny Powershellu v tomto tématu platí následující:

* Všechny tyto rutiny jsou malá a velká písmena.
* **– ErrorCategory PropertyConflict** musí být zahrnut. Aktuálně nejsou žádné jiné typy **ErrorCategory**, ale to může v budoucnu rozšířit.

Nejprve Začínáme spuštěním **Connect-MsolService** a zadání přihlašovacích údajů správce tenanta.

Potom použijte následující rutiny a operátory k zobrazení chyb různými způsoby:

1. [Zobrazit vše](#see-all)
2. [Podle typu vlastnosti](#by-property-type)
3. [Konfliktní hodnota](#by-conflicting-value)
4. [Hledání řetězce](#using-a-string-search)
5. Řazení
6. [V omezené množství nebo vše](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Zobrazit všechno
Jakmile budete připojeni, zobrazíte seznam obecné zřizování atribut chyby v tenantovi spusťte:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

To vytváří výsledek podobný tomuto:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Podle typu vlastnosti
Chcete-li zobrazit chyby podle typu vlastnosti, přidejte **- PropertyName** s příznakem **UserPrincipalName** nebo **ProxyAddresses** argument:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Nebo

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Konfliktní hodnota
Pokud chcete zobrazit chyby týkající se určité vlastnosti přidat **- PropertyValue** příznak (**- PropertyName** musí také možné používat při přidávání tohoto příznaku):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Hledání řetězce
Postup použití vyhledávání široký řetězec **- SearchString:** příznak. To je možné nezávisle na sobě ze všech výše uvedených příznaky, s výjimkou produktů **- ErrorCategory PropertyConflict**, což je vždy vyžaduje:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>V omezené množství nebo vše
1. **MaxResults \<Int >** slouží k omezení dotazu na konkrétní počet hodnot.
2. **Všechny** slouží k zajištění všechny výsledky jsou načteny v případě, že existuje velký počet chyb.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="microsoft-365-admin-center"></a>Centrum pro správu služeb Microsoft 365
Chyby synchronizace adresáře můžete zobrazit v Centru pro správu Microsoftu 365. Sestavy v Microsoft 365 admin center zobrazí se jenom **uživatele** objekty, které mají tyto chyby. Nezobrazuje se informace o konfliktech mezi **skupiny** a **kontakty**.

![Aktivní uživatelé](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "aktivních uživatelů")

Pokyny o tom, jak zobrazit chyby synchronizace adresáře v Centru pro správu služeb Microsoft 365 najdete v tématu [identifikaci chyb synchronizace adresáře v Office 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Sestava chyb synchronizace identit
Když se zpracovává objekt s konfliktem duplicitní atribut kontaktovat toto nové chování oznámení je součástí standardní e-mailovou zprávu o chybách synchronizace identit, odeslaný technická oznámení tenanta. Je však důležité změny v rámci tohoto chování. V minulosti informace o duplicitní atribut konfliktu by být součástí každé následné chybách dokud konflikt se vyřešil. S toto nové chování oznamování chyb pro danou konflikt být použito pouze jednou – v době, kdy je v karanténě konfliktním atributem.

Tady je příklad vypadá e-mailové oznámení pro ProxyAddress konflikt:  
    ![Aktivní uživatelé](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "aktivních uživatelů")  

## <a name="resolving-conflicts"></a>Řešení konfliktů
Řešení potíží s taktika strategie a řešení pro tyto chyby by neměl lišit od způsob zpracovávaly chyby duplicitních atributů v minulosti. Jediným rozdílem je, že úlohy časovače přesune prostřednictvím klienta na straně služby automaticky přidat atribut dotyčný správný objekt po vyřešení konfliktu.

Následující článek popisuje různé strategie jejich řešení: [Duplicitní nebo neplatný atributů, které brání synchronizace adresářů v Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Známé problémy
Žádná z těchto známých problémů způsobí, že snížení dat ke ztrátě nebo služby. Některé z nich jsou aesthetic, ostatní způsobit standard "*předběžné odolnost proti chybám*" chyby duplicitních atributů, která je vyvolána namísto umístění do karantény atribut konflikt a druhý způsobí, že některé chyby tak, aby vyžadovala velmi ruční opravit.

**Základní chování:**

1. Objekty s konkrétním atributem konfigurace dál dostávat chyby export na rozdíl od duplicitní atributy, které se umístí do karantény.  
   Příklad:
   
    a. Vytvoření nového uživatele ve službě AD s název UPN **Joe\@contoso.com** a ProxyAddress **smtp:Joe\@contoso.com**
   
    b. Vlastnosti tohoto objektu jsou v konfliktu s existující skupinu, ve kterém je ProxyAddress **SMTP:Joe\@contoso.com**.
   
    c. Při exportu **ProxyAddress konflikt** nemuseli konfliktní atributy umístí do karantény, je vržena chyba. Operace je opakována při každém cyklu následná synchronizace, jako by byl předtím, než byla povolena funkce odolnosti proti chybám.
2. Pokud v místním se stejnou adresou SMTP se vytvoří dvě skupiny, jeden selže zřizování při prvním pokusu se standardní duplicitní **ProxyAddress** chyby. Ale duplicitní hodnota je správně umístit do karantény na další cyklus synchronizace.

**Sestavy portálu Office**:

1. Podrobná chybová zpráva pro dva objekty v sadě konflikt (UPN) je stejný. To znamená, že oba měly jejich hlavní název uživatele změnit / umístit do karantény, když ve skutečnosti pouze jeden z nich měl jakékoli změny dat.
2. Podrobná chybová zpráva pro hlavní název uživatele konflikt zobrazuje nesprávný displayName pro uživatele, který má určitá jejich hlavní název uživatele změnit/umístit do karantény. Příklad:
   
    a. **Uživatel A** synchronizuje se první z nich s **UPN = uživatel\@contoso.com**.
   
    b. **Uživatel B** se pokusil synchronizovat do další s **UPN = uživatel\@contoso.com**.
   
    c. **Uživatel B** UPN se změní na **User1234\@contoso.onmicrosoft.com** a **uživatele\@contoso.com** se přidá do **DirSyncProvisioningErrors** .
   
    d. Chybová zpráva pro **uživateli B** , která by měla zobrazovat **uživatel A** už má **uživatele\@contoso.com** podle UPN, ale zobrazuje **uživatele B** vlastní displayName.

**Sestava chyb synchronizace identit**:

Odkaz pro *pokyny k vyřešení tohoto problému* není správná:  
    ![Aktivní uživatelé](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "aktivních uživatelů")  

Mělo by odkazovat [ https://aka.ms/duplicateattributeresiliency ](https://aka.ms/duplicateattributeresiliency).

## <a name="see-also"></a>Další informace najdete v tématech
* [Synchronizace služby Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
* [Identifikace chyb synchronizace adresáře v Office 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

