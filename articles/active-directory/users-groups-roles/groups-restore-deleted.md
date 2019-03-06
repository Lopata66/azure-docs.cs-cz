---
title: Obnovení odstraněné skupiny Office 365 – Azure AD | Dokumentace Microsoftu
description: Jak obnovit odstraněné skupiny, zobrazení obnovitelné skupin a trvale odstranit skupinu v Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: fee8604bece0ae7249b0735f6952bfa35a4590fb
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443160"
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Obnovení odstraněné skupiny Office 365 v Azure Active Directory
Když odstraníte skupinu Office 365 v Azure Active Directory (Azure AD), uchovává se tato odstraněná skupina po dobu 30 dnů od data odstranění, ale není viditelná. Je to kvůli tomu, abyste v případě potřeby mohli skupinu, včetně jejího obsahu, obnovit. Tato funkce je omezená výhradně pro skupiny Office 365 v Azure AD. Není dostupná pro skupiny zabezpečení a distribuční skupiny.

> [!NOTE]
> Nepoužívejte rutinu `Remove-MsolGroup`, protože tato rutina danou skupinu trvale vymaže. Vždy používejte `Remove-AzureADMSGroup` odstranit skupinu Office 365.

K obnovení skupiny jsou potřebná některá z následujících oprávnění:

Role | Oprávnění
--------- | ---------
Správce společnosti, podpora partnerů úrovně 2 a správci služby Intune | Můžou obnovit jakoukoli odstraněnou skupinu Office 365.
Správce uživatelských účtů a podpora partnerů úrovně 1 | Můžete obnovit všechny odstraněné skupiny Office 365 s výjimkou těchto skupin přiřazen k roli správce společnosti
Uživatel | Můžou obnovit jakoukoli odstraněnou skupinu Office 365, kterou vlastnili.

## <a name="view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore"></a>Umožňuje zobrazit a spravovat odstraněné skupiny Office 365, které jsou k dispozici pro obnovení

1. Přihlaste se k [centrum pro správu Azure AD](https://aad.portal.azure.com) s účtem správce.

2. Vyberte **skupiny**a pak vyberte **odstraněných skupin** zobrazíte odstraněné skupiny, které jsou k dispozici pro obnovení.

    ![Okno odstraněné skupiny](media/groups-lifecycle/deleted-groups3.png)

3. Na **odstraněných skupin** okně můžete:

  - Obnovení odstraněné skupiny a její obsah tak, že vyberete **obnovení skupiny**.
  - Trvale odebrat z odstraněné skupiny tak, že vyberete **trvale odstranit**. Pokud chcete trvale odebrat skupinu, musíte být správcem.

## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore-using-powershell"></a>Zobrazit odstraněné skupiny Office 365, které jsou k dispozici pro obnovení pomocí prostředí Powershell
K zobrazení odstraněných skupin a ověření, že ty, které vás zajímají, ještě nejsou trvale vymazané, můžete použít následující rutiny. Tyto rutiny jsou součástí [modulu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/). Další informace o tomto modulu najdete v článku [Azure Active Directory PowerShell verze 2](/powershell/azure/install-adv2?view=azureadps-2.0).

1.  Spuštěním následující rutiny zobrazíte všechny odstraněné skupiny Office 365 ve vašem tenantovi, které je ještě možné obnovit.
   
    ```
    Get-AzureADMSDeletedGroup
    ```

2.  Pokud znáte identifikátor objectID konkrétní skupiny (a můžete ho získat z rutiny v kroku 1), můžete ověřit, že daná konkrétní rutina ještě není trvale vymazaná, také spuštěním následující rutiny.

    ```
    Get-AzureADMSDeletedGroup –Id <objectId>
    ```

## <a name="how-to-restore-your-deleted-office-365-group-using-powershell"></a>Jak obnovit vaše odstraněné skupiny Office 365 pomocí Powershellu
Když jste ověřili, že příslušnou skupinu je ještě možné obnovit, obnovte tuto odstraněnou skupinu některým z následujících postupů. Pokud daná skupina obsahuje dokumenty, weby SP nebo jiné trvalé objekty, může úplné obnovení této skupiny a jejího obsahu trvat až 24 hodin.

1. Obnovte danou skupinu a její obsah spuštěním následující rutiny.
 
   ```
    Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
    ``` 

2. Odstraněnou skupinu lze případně trvale odebrat spuštěním následující rutiny.
    
    ```
    Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
    ```

## <a name="how-do-you-know-this-worked"></a>Jak zjistíte, že obnovení bylo úspěšné?

Pokud chcete ověřit, že jste skupinu Office 365 úspěšně obnovili, spusťte rutinu `Get-AzureADGroup –ObjectId <objectId>`, která zobrazí informace o dané skupině. Po dokončení žádosti o obnovení:

- Skupina se zobrazí v levém navigačním panelu na Exchangi
- Plán pro danou skupinu se zobrazí v Planneru.
- Všechny weby služby SharePoint a veškerý jeho obsah bude k dispozici
- Daná skupina bude přístupná ze všech koncových bodů Exchange a jiných úloh Office 365, které podporují skupiny Office 365.

## <a name="next-steps"></a>Další postup
Následující články poskytují další informace o skupinách Azure Active Directory.

* [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Správa členů skupiny](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
