---
title: Odebrání uživatele nebo skupiny přiřazení podnikové aplikace v Azure Active Directory | Dokumentace Microsoftu
description: Odebrání přiřazení přístupu uživatele nebo skupiny z podnikové aplikace v Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4ad995db26b67f365cd8ed9b2aeba5e47fe9c01
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826075"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Odebrání uživatele nebo skupiny přiřazení podnikové aplikace v Azure Active Directory
Je snadné odeberte uživatele nebo skupiny z přiřazeného přístupu k jednomu s vašimi firemními aplikacemi v Azure Active Directory (Azure AD). Budete potřebovat příslušná oprávnění ke správě podnikové aplikace. A vy musíte být globální správce adresáře.

> [!NOTE]
> For Applications Microsoftu (jako jsou například aplikace Office 365) odeberte uživatelům podnikové aplikace pomocí Powershellu.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Jak odebrat uživatele nebo skupiny přiřazení pro podnikové aplikace na webu Azure Portal?
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce.
1. Vyberte **všechny služby**, zadejte **Azure Active Directory** v textovém poli a pak vyberte **Enter**.
1. Na **Azure Active Directory – *NazevAdresare***  stránka (to znamená Azure AD Directory spravujete), vyberte **podnikové aplikace**.
1. Na **podnikové aplikace – všechny aplikace** stránku, uvidíte seznam aplikací můžete spravovat. Vyberte aplikaci.
1. Na ***appname*** stránka s přehledem (to znamená, stránka s názvem vybranou aplikaci v názvu), vyberte **uživatelů a skupin**.
1. Na ***appname*** **– uživatele a skupiny přiřazení** stránky, vyberte jednu z více uživatelů nebo skupin a potom vyberte **odebrat** příkazu. Potvrďte svoje rozhodnutí do příkazového řádku.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Jak odebrat uživatele nebo skupiny přiřazení pro podnikové aplikace pomocí Powershellu?
1. Otevřete příkazový řádek se zvýšenými oprávněními prostředí Windows PowerShell.

    >[!NOTE] 
    > Je potřeba nainstalovat modul Azure AD (použijte příkaz `Install-Module -Name AzureAD`). Pokud se zobrazí výzva k instalaci modulu NuGet nebo nový modul Powershellu pro Azure Active Directory V2, zadejte Y a stiskněte klávesu ENTER.

1. Spustit `Connect-AzureAD` a přihlaste se pomocí uživatelského účtu globálního správce.
1. Pomocí následujícího skriptu z aplikace odebrat uživatele a role:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ``` 
   ## <a name="next-steps"></a>Další postup

- [Zobrazit všechny moje skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Přiřadit uživatele nebo skupiny k podnikové aplikace](assign-user-or-group-access-portal.md)
- [Zakázat přihlášení uživatele pro podnikové aplikace](disable-user-sign-in-portal.md)
- [Změnit název nebo loga podnikové aplikace](change-name-or-logo-portal.md)
