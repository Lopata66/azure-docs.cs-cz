---
title: Ochrana proti útoku hrubou silou, která pomocí služby Azure AD inteligentní uzamčení – Azure Active Directory
description: Azure Active Directory inteligentním uzamčením pomáhá chránit vaši organizaci před útoky hrubou silou uhádnutí hesla
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 150ecbdfcc21ee7ec0bf54fd5b824bc93e0c76ce
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483305"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory inteligentní uzamčení

Inteligentní uzamčení pomáhá při uzamčení nesprávnými účastníky, kteří se snaží uhodnout hesla uživatelů nebo využít hrubou silou. Může rozpoznat přihlášení pocházející z platných uživatelů a jinak než těch, které útočníci a dalších neznámé zdroje jsou s nimi zacházet. Inteligentní uzamčení k uzamčení útočníci při se vaši uživatelé i nadále přístup ke svým účtům a produktivitu.

Ve výchozím nastavení zamkne inteligentní uzamčení účtu z pokusů o přihlášení pro jednu minutu po 10 neúspěšných pokusů o přihlášení. Uzamčení účtu po každé následné selhání pokus o přihlášení, na minutu na první a delší dobu v následné pokusy.

Inteligentní uzamčení sleduje posledních tří hodnot hash hesel chybný-li zamezit zvýšení čítače uzamčení pro stejné heslo. Pokud někdo zadá chybné heslo více než jednou, toto chování nezpůsobí účet, který chcete uzamčení.

 > [!NOTE]
 > Sledování funkce hash není k dispozici pro zákazníky s předávací ověřování povolit, protože není v cloudu v místním využíváno ověření.

Inteligentní uzamčení je vždy pro všechny zákazníky Azure AD se tato výchozí nastavení, které nabízejí správné kombinace zabezpečení a použitelnost. Přizpůsobení nastavení inteligentním uzamčením, s hodnotami, které jsou specifické pro vaši organizaci, vyžaduje Azure AD Basic nebo vyšší licencí pro vaše uživatele.

Pomocí inteligentního uzamčení nezaručuje, že bude pravý uživatel nikdy uzamčena na. Inteligentní uzamčení uzamčení uživatelského účtu, snažíme co nejlépe uzamčení originální uživatele. Služba uzamčení se pokusí zajistit, že nesprávnými účastníky nemůže získat přístup k originální uživatelský účet.  

* Každé datové centrum Azure Active Directory nezávisle na sobě sleduje uzamčení. Uživatel bude mít (threshold_limit * datacenter_count) počet pokusů, pokud uživatel stiskne každé datové centrum.
* Inteligentní uzamčení používá známé umístění vs neznámého umístění rozlišovat mezi chybný actor a pravý uživatele. Neznámé a dobře známého umístění bude mít samostatné uzamčení čítače.

Inteligentní uzamčení lze integrovat s hybridní nasazení pomocí synchronizace hodnot hash hesel nebo předávací ověřování ochrana místních účtů služby Active Directory zamknutí útočníci. Inteligentní uzamčení zásady nastavíte odpovídajícím způsobem ve službě Azure AD, dají se útoky odfiltrovat dřív, než dorazí v místní službě Active Directory.

Při použití [předávací ověřování](../hybrid/how-to-connect-pta.md), je třeba Ujistěte se, že:

* Prahová hodnota pro uzamčení Azure AD je **méně** než prahovou hodnotu uzamknutí účtu služby Active Directory. Nastavte hodnoty tak, aby prahovou hodnotu uzamknutí účtu služby Active Directory je minimálně dvakrát nebo třikrát déle než prahová hodnota pro uzamčení Azure AD. 
* Doba trvání uzamčení Azure AD musí být nastavena déle, než Active Directory Vynulovat čítač uzamčení účtu po dobu trvání. Mějte na paměti, že Azure AD trvání je nastavena v sekundách, během AD doba trvání se nastavuje v minutách. 

Například pokud chcete vaše služby Azure AD čítač vyšší než AD, pak Azure AD by 120 sekund (2 minuty) při na úrovni Premium, AD je nastavený na 1 minutu (60 sekund).

> [!IMPORTANT]
> Správce aktuálně nedá odemknout účtům cloudových uživatelů, pokud se mají byl uzamčen inteligentním uzamčením schopnosti. Správce musí počkat po dobu trvání uzamknutí vyprší.

## <a name="verify-on-premises-account-lockout-policy"></a>Zkontrolujte místní zásady uzamčení účtů

Chcete-li ověřit vaše místní zásady uzamčení účtů služby Active Directory použijte následující pokyny:

1. Spusťte nástroj pro správu zásad skupiny.
2. Upravit zásady skupiny, které obsahuje zásady uzamčení účtu vaší organizace, například **výchozí zásady domény**.
3. Přejděte do **konfigurace počítače** > **zásady** > **nastavení Windows** > **nastavení zabezpečení**   >  **Zásady účtů** > **zásady uzamčení účtů**.
4. Ověřte vaše **prahovou hodnotu uzamknutí účtu** a **resetování čítače uzamčení účtu po** hodnoty.

![Upravit zásady uzamčení účtů služby Active Directory v místním](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Správa služby Azure AD inteligentním uzamčením hodnot

Podle požadavků vaší organizace, inteligentním uzamčením hodnot může být potřeba upravit. Přizpůsobení nastavení inteligentním uzamčením, s hodnotami, které jsou specifické pro vaši organizaci, vyžaduje Azure AD Basic nebo vyšší licencí pro vaše uživatele.

Chcete-li zkontrolovat nebo změnit inteligentním uzamčením hodnoty pro vaši organizaci, použijte následující kroky:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a klikněte na **Azure Active Directory**, pak **metody ověřování**.
1. Nastavte **prahová hodnota pro uzamčení**závislosti na počtu neúspěšných přihlášení jsou povoleny v rámci účtu před jeho první uzamčení. Výchozí hodnota je 10.
1. Nastavte **doba trvání uzamčení v řádu sekund**, délku každého uzamčení v řádu sekund. Výchozí hodnota je 60 sekund (jednu minutu).

> [!NOTE]
> Pokud první přihlášení po uzamčení je také neúspěšná, účet zamezí znovu. Pokud účet uzamkne opakovaně, doba trvání uzamčení se zvýší.

![Přizpůsobte si zásadu inteligentním uzamčením Azure AD na webu Azure Portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Jak určit, zda funkci inteligentního uzamčení funguje nebo ne

Když prahová hodnota pro inteligentní uzamčení se aktivuje, zobrazí se následující zpráva při uzamčení účtu:

**Váš účet je dočasně uzamčený, abyste zabránili neoprávněnému používání. Zkuste to znovu později a Pokud potíže přetrvávají, obraťte se na svého správce.**

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak zakázat chybná hesla ve vaší organizaci pomocí služby Azure AD.](howto-password-ban-bad.md)
* [Nakonfigurujte samoobslužné resetování hesla a umožňují uživatelům odemknout své účty.](quickstart-sspr.md)
