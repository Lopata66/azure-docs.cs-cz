---
title: Co je hybridní identity s Azure Active Directory?
description: Hybridní identita má společnou uživatelskou identitu pro ověřování a autorizaci v místním prostředí i v cloudu.
keywords: Úvod k Azure AD Connect, přehled Azure AD Connect, co je Azure AD Connect, instalace active directory
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c43238d44b2309d105ef14e696a5a16848d0b58
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65896832"
---
# <a name="what-is-hybrid-identity-with-azure-active-directory"></a>Co je hybridní identity s Azure Active Directory?

Dnes, podniky a společnosti se častěji stávají směs místních a cloudových aplikací.  Uživatelé, kteří potřebují přístup k těmto aplikacím v místním prostředí i v cloudu. Správa uživatelů místně a v cloudu představuje náročné scénáře. 

Řešení identit společnosti Microsoft zahrnují místní a cloudové funkce.  Tato řešení vytvořit společnou uživatelskou identitu pro ověřování a autorizaci ke všem prostředkům, bez ohledu na umístění. To označujeme **hybridní identita**.

Pomocí hybridních identit do služby Azure AD a hybridní identity management tyto scénáře stát je to možné.

K dosažení hybridní identity s Azure AD, jedním ze tří způsobů ověřování je možné, v závislosti na scénáře.   Jsou tři metody: 

- **[Synchronizace hodnot hash hesel (PHS)](whatis-phs.md)**  
- **[Předávací ověřování (PTA)](how-to-connect-pta.md)**  
- **[Federování (AD FS)](whatis-fed.md)** 

Tyto metody ověřování také poskytují [jednotného přihlašování](how-to-connect-sso.md) možnosti.  Jednotného přihlašování automaticky přihlásí uživatele v případě, že nejsou v jejich firemní zařízení připojených k podnikové síti.

Další informace najdete v tématu [zvolte správné ověřování pro vaše řešení hybridní identity Azure Active Directory](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). 

## <a name="common-scenarios-and-recommendations"></a>Běžné scénáře a doporučení 

Tady najdete několik běžných scénářů souvisejících s hybridní identitou a správou přístupu a doporučení, jaká možnost (nebo možnosti) hybridní identity by mohla být pro každý z nich nejvhodnější. 

|Požadavky:|PHS a SSO<sup>1</sup>| PTA a SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|Automatická synchronizace nových uživatelských, kontaktních a skupinových účtů vytvořených v místní službě Active Directory do cloudu|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučené](./media/whatis-hybrid-identity/ic195031.png) |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Nastavení mému tenantovi Office 365 hybridní scénáře.|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučené](./media/whatis-hybrid-identity/ic195031.png) |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Povolení uživatelé přihlásit a přístup ke cloudovým službám pomocí jejich místní heslo.|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučené](./media/whatis-hybrid-identity/ic195031.png) |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Implementaci jednotného přihlašování pomocí firemních přihlašovacích údajů.|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučené](./media/whatis-hybrid-identity/ic195031.png) |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|  
|Ujistěte se, že žádné hodnoty hash hesel se ukládají v cloudu.| |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Povolení řešení cloudové služby Multi-Factor authentication.|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Povolit místní řešení vícefaktorového ověřování.| | |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Podpora ověřování pomocí čipové karty pro moje uživatele. <sup>4</sup>| | |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Zobrazit oznámení o vypršení platnosti hesel portálu Office a na Windows 10 desktop.| | |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Synchronizace hodnot hash hesel a jednotné přihlašování. 
> 
> <sup>2</sup> Předávací ověřování a jednotné přihlašování.  
> 
> <sup>3</sup> Federované jednotné přihlašování pomocí AD FS.  
>  
> <sup>4</sup> AD FS je možné integrovat s podnikovou infrastrukturou veřejných klíčů a umožnit tak přihlašování pomocí certifikátů. Těmito certifikáty můžou být softwarové certifikáty nasazené přes důvěryhodné kanály zřizování, jako MDM nebo GPO, nebo certifikáty čipových karet (včetně karet PIV/CAC) nebo Hello pro firmy (důvěryhodnost certifikátu). Další informace o podpoře ověřování pomocí čipové karty najdete v [tomto blogovém příspěvku](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/). 
> 

## <a name="license-requirements-for-using-azure-ad-connect"></a>Licenční požadavky pro používání služby Azure AD Connect

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Další kroky 

- [Co je Azure AD Connect a Connect Health?](whatis-azure-ad-connect.md) 
- [Co je synchronizace hodnot hash hesel (PHS)?](whatis-phs.md) 
- [Co je předávací ověřování (PTA)?](how-to-connect-pta.md) 
- [Co je federace?](whatis-fed.md) 
- [Co je jednotné přihlašování na?](how-to-connect-sso.md) 

