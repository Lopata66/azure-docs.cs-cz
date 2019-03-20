---
title: Nastavit dobu platnosti pro skupiny Office 365 – Azure Active Directory | Dokumentace Microsoftu
description: Jak nastavit vypršení platnosti pro skupiny Office 365 ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e4585e8e1a809824e63f917fed1cc8a9cfa646a
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202924"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Konfigurace zásad vypršení platnosti pro skupiny Office 365

Teď můžete spravovat životní cyklus skupiny Office 365 tak, že pro ně nastavíte zásady vypršení platnosti. Můžete nastavit zásady vypršení platnosti pro pouze skupiny Office 365 ve službě Azure Active Directory (Azure AD). 

Jakmile jednou nastavíte skupinu do vypršení platnosti:

- Vlastníci skupiny se zobrazí oznámení, jak vypršení platnosti přiblíží obnovení skupiny
- Libovolná skupina, která se neobnoví se odstraní.
- Všechny skupiny Office 365, který je odstraněn, je možné obnovit do 30 dní vlastníci skupiny nebo správce

V současné době se pro skupiny Office 365 v rámci jednoho tenanta dá nakonfigurovat jenom jedna zásada vypršení platnosti.

> [!NOTE]
> Konfigurace a použití zásady vypršení platnosti pro skupiny Office 365 vyžaduje, abyste na straně máte licence Azure AD Premium pro všechny členy skupiny, pro které se platí zásady vypršení platnosti.

Informace o tom, jak stáhnout a nainstalovat nejnovější verzi rutin Azure AD Powershellu najdete v tématu [Azure Active Directory PowerShell pro Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="roles-and-permissions"></a>Role a oprávnění

Toto jsou role, které můžete nakonfigurovat a používat vypršení platnosti pro skupiny Office 365 ve službě Azure AD.

Role | Oprávnění
-------- | --------
Globální správce nebo Správce uživatelů | Můžete vytvořit, číst, aktualizovat nebo odstranit nastavení zásad vypršení platnosti skupin Office 365<br>Můžete obnovit jakékoli skupiny Office 365
Uživatel | Můžete obnovit skupinu Office 365, které vlastní<br>Můžete obnovit skupinu Office 365, které vlastní<br>Může číst nastavení zásad vypršení platnosti

Další informace o oprávnění k obnovení odstraněné skupiny, najdete v části [obnovení odstraněné skupiny Office 365 ve službě Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Nastavení vypršení platnosti skupiny

1. Otevřít [centrum pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je globálním správcem ve vašem tenantovi Azure AD.

2. Vyberte **skupiny**a pak vyberte **vypršení platnosti** otevřete nastavení vypršení platnosti.
  
   ![Nastavení vypršení platnosti pro skupiny](./media/groups-lifecycle/expiration-settings.png)

3. Na **vypršení platnosti** okně můžete:

  * Nastavení doby platnosti skupiny ve dnech. Můžete třeba vybrat jednu z přednastavených hodnot nebo vlastní hodnota (třeba 31 dnů nebo déle). 
  * Zadejte e-mailovou adresu, kde mají být odesílána upozornění prodloužení platnosti a ukončení platnosti při skupina nemá žádného vlastníka. 
  * Vyberte skupiny Office 365, které vyprší. Můžete povolit vypršení platnosti pro **všechny** skupiny Office 365, můžete také povolit pouze **vybrané** skupiny Office 365, nebo vyberte **žádný** zakázat vypršení platnosti pro všechny skupiny .
  * Až to uděláte tak, že vyberete uložit nastavení **Uložit**.

> [!NOTE]
> * Při prvním nastavování vypršení platnosti všech skupin, které jsou starší než interval vypršení platnosti jsou nastavené na 30 dní do vypršení platnosti. První e-mail s oznámením obnovení bude odeslaná starší než jeden den. Například skupiny A vytvořil 400 dnů a vypršení platnosti interval je nastavená na 180 dnů. Při použití nastavení vypršení platnosti skupiny A má 30 dní, než je odstraníme, pokud jej obnoví vlastníka.
> * Když dojde k odstranění a následnému obnovení dynamické skupiny, považuje se za novou skupinu a naplní se na základě pravidla. Tento proces může trvat až 24 hodin.

## <a name="email-notifications"></a>E-mailová oznámení

E-mailová oznámení, jako je například tento se odesílají vlastníkům skupin Office 365 30 dnů, 15 dnů a 1 den před vypršením platnosti skupiny. Jazyk e-mailu je určen upřednostňovaného jazyka nebo jazyce tenanta vlastníka skupiny. Pokud vlastník skupiny definoval upřednostňovaného jazyka nebo více vlastníkům mají stejné preferovaný jazyk, použije se tento jazyk. U všech ostatních případech se používá jazyk tenanta.

![Vypršení platnosti e-mailových oznámení](./media/groups-lifecycle/expiration-notification.png)

Z **obnovit skupinu** oznámení e-mailu, přímo můžou vlastníci skupiny přístup ke stránce Podrobnosti skupiny na přístupovém panelu. Uživatelé, můžete získat další informace o skupině jako jeho popis, když to byl poslední prodloužení platnosti, když vyprší platnost a také možnost obnovit skupinu. Na stránce podrobností skupiny teď obsahuje také odkazy na prostředky skupiny Office 365, tak, aby vlastník skupiny můžete pohodlně zobrazit obsah a aktivity ve své skupině.

Když vyprší platnost skupiny, skupina se odstranila jeden den po datu vypršení platnosti. Oznámení o e-mailu, jako je například tento posílá informace o vypršení platnosti a následné odstranění jejich skupiny Office 365 vlastníkům skupin Office 365.

![E-mailová oznámení odstranění skupiny](./media/groups-lifecycle/deletion-notification.png)

Skupiny je možné obnovit do 30 dní od jeho odstranění tak, že vyberete **obnovení skupiny** nebo pomocí rutin prostředí PowerShell, jak je popsáno v [obnovení odstraněné skupiny Office 365 ve službě Azure Active Directory](groups-restore-deleted.md).
    
Pokud na skupinu, kterou jste obnovení obsahuje dokumenty, Sharepointových webech nebo další trvalé objekty, může trvat až 24 hodin plně obnovit skupiny a její obsah.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Jak načíst data vypršení platnosti skupin Office 365
Kromě přístupovém panelu, kde uživatelé můžou zobrazit podrobnosti o skupině včetně datum vypršení platnosti a datum posledního obnovené a datum vypršení platnosti skupiny Office 365 mohou být načteny z verze Beta Microsoft Graph REST API. v Microsoft Graphu Beta se povolila expirationDateTime jako vlastnost skupiny. Se dá načíst požadavek GET. Další podrobnosti najdete [v tomto příkladu](https://docs.microsoft.com/en-us/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> Pokud chcete spravovat členství ve skupině na přístupovém panelu, musí být nastavena na hodnotu "Ne" v Azure Active Directory skupiny Obecné nastavení "Omezení přístupu ke skupinám na přístupovém panelu".


## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Jak funguje vypršení platnosti skupin Office 365 s poštovní schránku na blokování z právních důvodů
Po vypršení platnosti skupiny a odstranění, potom 30 dnů od odstranění skupiny data z aplikací, jako jsou plánovače, weby, nebo týmy se trvale odstraní, ale poštovní skupiny, která je z důvodu soudního sporu se uchovávají, se trvale odstraní. Správce můžete použít k obnovení poštovní schránky k načtení dat rutin systému Exchange. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Jak funguje vypršení platnosti skupin Office 365 se zásady uchovávání informací
Zabezpečení a dodržování předpisů Center jsou nakonfigurované zásady uchovávání informací. Pokud jste nastavili zásadu uchovávání pro skupiny Office 365 po vypršení platnosti skupiny a odstranění, skupinové konverzace v poštovní schránce skupiny a soubory v lokalitě skupiny jsou zachovány v kontejneru uchovávání dat pro určitý počet dní, které jsou definovány v uchovávání zásady. Uživatelé neuvidí po vypršení platnosti skupiny nebo jeho obsah, ale můžete obnovit data lokality a poštovní schránky prostřednictvím elektronického zjišťování.

## <a name="powershell-examples"></a>Příklady prostředí PowerShell
Tady jsou příklady použití rutin prostředí PowerShell a zadejte nastavení vypršení platnosti pro skupiny Office 365 ve vašem tenantovi:

1. Nainstalujte modul prostředí PowerShell verze 2.0 ve verzi Preview (2.0.0.137) a přihlaste se na příkazovém řádku prostředí PowerShell:
   ```powershell
   Install-Module -Name AzureADPreview
   connect-azuread 
   ```
2. Konfigurace nastavení vypršení platnosti New-AzureADMSGroupLifecyclePolicy:  Tato rutina nastaví dobu života pro všechny skupiny Office 365 v tenantovi až 365 dnů. Oznámení o prodloužení platnosti pro Office 365 skupin bez vlastníků se odešlou do "emailaddress@contoso.com.
  
   ```powershell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```
3. Načtěte existující zásady Get-AzureADMSGroupLifecyclePolicy: Tato rutina načte aktuální nastavení Office 365 skupiny vypršení platnosti, které byly nakonfigurovány. V tomto příkladu je vidět:
   * ID zásad 
   * Doba života pro všechny skupiny Office 365 v tenantovi je nastavena na 365 dnů
   * Oznámení o prodloužení platnosti pro Office 365 skupin bez vlastníků se odešlou do "emailaddress@contoso.com."
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ``` 
   
4. Aktualizace Set-AzureADMSGroupLifecyclePolicy existující zásady: Tato rutina se používá k aktualizaci existující zásady. V následujícím příkladu životnost skupiny v existující zásady změnit za 365 dnů na 180 dnů. 
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
5. Přidání zadaných skupin do zásad Add-AzureADMSLifecyclePolicyGroup: Tato rutina přidá skupiny k zásadám životního cyklu. Jako příklad:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
6. Odeberte existující zásady Remove-AzureADMSGroupLifecyclePolicy: Tato rutina odstraní nastavení vypršení platnosti skupin Office 365, ale vyžaduje ID zásad. Tato akce zakáže vypršení platnosti pro skupiny Office 365. 
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Následující rutiny slouží ke konfiguraci zásad podrobněji. Další informace najdete v tématu [dokumentaci k Powershellu](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

* Get-AzureADMSGroupLifecyclePolicy
* New-AzureADMSGroupLifecyclePolicy
* Get-AzureADMSGroupLifecyclePolicy
* Set-AzureADMSGroupLifecyclePolicy
* Remove-AzureADMSGroupLifecyclePolicy
* Add-AzureADMSLifecyclePolicyGroup
* Remove-AzureADMSLifecyclePolicyGroup
* Reset-AzureADMSLifeCycleGroup   
* Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Další postup
Tyto články poskytují další informace o skupinách Azure AD.

* [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Správa členů skupiny](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
