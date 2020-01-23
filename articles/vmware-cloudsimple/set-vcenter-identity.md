---
title: Řešení Azure VMware podle CloudSimple – nastavení zdrojů vCenter identity v privátním cloudu
description: Popisuje, jak nastavit privátní cloud vCenter pro ověřování pomocí služby Active Directory pro správce VMware pro přístup k serveru vCenter.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eeced5205b836a15a43fbccfb8c6cb60b4bec29f
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76542861"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Nastavení zdrojů identity vCenter pro používání služby Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>O zdrojích identity VMware vCenter

VMware vCenter podporuje různé zdroje identity pro ověřování uživatelů, kteří přistupují k vCenter.  Váš CloudSimple privátní cloud vCenter se dá nastavit tak, aby se pro správce VMware nastavila ověřování se službou Active Directory pro přístup k serveru vCenter. Po dokončení instalace může uživatel **cloudowner** přidat uživatele ze zdroje identity do vCenter.  

Doménu a řadiče domény služby Active Directory můžete nastavit některým z těchto způsobů:

* Doména a řadiče domény služby Active Directory místně spuštěné
* Doména a řadiče domény služby Active Directory běžící na Azure jako virtuální počítače ve vašem předplatném Azure
* Nová doména a řadiče domény služby Active Directory běžící v privátním cloudu
* Služba Azure Active Directory

V této příručce se dozvíte, jak nastavit doménu a řadiče domény služby Active Directory, které jsou spuštěné místně nebo jako virtuální počítače ve vašich předplatných.  Pokud chcete jako zdroj identity použít Azure AD, přečtěte si téma [použití Azure AD jako zprostředkovatele identity pro vCenter v CloudSimple privátním cloudu](azure-ad.md) , kde najdete podrobné pokyny k nastavení zdroje identity.

Před [přidáním zdroje identity](#add-an-identity-source-on-vcenter)dočasně předávejte [oprávnění vCenter](escalate-private-cloud-privileges.md).

> [!CAUTION]
> Noví uživatelé musí být přidáni pouze *ke skupině*Cloud-Owner-Group *-Global-cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin* -Group nebo, *Cloud-Global-VM-admin-Group*.  Uživatelé přidaní do skupiny *správců* se automaticky odeberou.  Pouze účty služeb musí být přidány do skupiny *Administrators* a účty služeb nesmí být použity pro přihlášení k WEBOVÉmu uživatelskému rozhraní vSphere.   


## <a name="identity-source-options"></a>Možnosti zdroje identity

* [Přidání místní služby Active Directory jako zdroje identity jednotného přihlašování](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Nastavení nové služby Active Directory v privátním cloudu](#set-up-new-active-directory-on-a-private-cloud)
* [Nastavení služby Active Directory v Azure](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Přidání místní služby Active Directory jako zdroje identity jednotného přihlašování

Pokud chcete nastavit místní službu Active Directory jako zdroj identity jednotného přihlašování, budete potřebovat:

* [Připojení VPN typu Site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) z místního datacentra do privátního cloudu.
* IP adresa místního serveru DNS přidaná do vCenter a řadiče služeb platformy (PSC).

Při nastavování domény služby Active Directory použijte informace v následující tabulce.

| **Možnost** | **Popis** |
|------------|-----------------|
| **Název** | Název zdroje identity |
| **Základní rozlišující název pro uživatele** | Základní rozlišující název pro uživatele |
| **Název domény** | Nezadávejte domény, například example.com. V tomto textovém poli nezadávejte IP adresu. |
| **Alias domény** | Název domény pro rozhraní NetBIOS. Pokud používáte ověřování pomocí rozhraní SSPI, přidejte název domény služby Active Directory jako alias zdroje identity. |
| **Základní rozlišující název pro skupiny** | Základní rozlišující název pro skupiny |
| **Adresa URL primárního serveru** | Server LDAP primárního řadiče domény pro doménu.<br><br>Použijte formát `ldap://hostname:port` nebo `ldaps://hostname:port`. Port je obvykle 389 pro připojení LDAP a 636 pro připojení LDAPs. Pro nasazení služby Active Directory Multi-Domain Controller je port obvykle 3268 pro LDAP a 3269 pro LDAPs.<br><br>Certifikát, který vytváří vztah důvěryhodnosti pro koncový bod LDAPs serveru Active Directory, se vyžaduje při použití `ldaps://` v primární nebo sekundární adrese URL protokolu LDAP. |
| **Adresa URL sekundárního serveru** | Adresa serveru LDAP sekundárního řadiče domény, který se používá pro převzetí služeb při selhání. |
| **Zvolit certifikát** | Pokud chcete použít LDAPs se serverem služby Active Directory LDAP nebo zdrojem identity serveru OpenLDAP, po zadání `ldaps://` v textovém poli URL se zobrazí tlačítko zvolit certifikát. Sekundární adresa URL není povinná. |
| **Uživatelské jméno** | ID uživatele v doméně, který má minimální přístup jen pro čtení k základnímu rozlišujícímu názvu pro uživatele a skupiny. |
| **Heslo** | Heslo uživatele, který je určen uživatelským jménem. |

Pokud máte informace v předchozí tabulce, můžete do vCenter přidat místní službu Active Directory jako zdroj identity jednotného přihlašování.

> [!TIP]
> Další informace o zdrojích identity jednotného přihlašování najdete na [stránce dokumentace k VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html).

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>Nastavení nové služby Active Directory v privátním cloudu

V privátním cloudu můžete nastavit novou doménu služby Active Directory a použít ji jako zdroj identity pro jednotné přihlašování.  Doména služby Active Directory může být součástí existující doménové struktury služby Active Directory nebo může být nastavena jako nezávislá doménová struktura.

### <a name="new-active-directory-forest-and-domain"></a>Nová doménová struktura a doména služby Active Directory

K nastavení nové doménové struktury a domény služby Active Directory potřebujete:

* Jeden nebo více virtuálních počítačů se systémem Microsoft Windows Server, které se mají použít jako řadiče domény pro novou doménovou strukturu a doménu služby Active Directory.
* Jeden nebo více virtuálních počítačů se službou DNS pro překlad názvů.

Podrobné pokyny najdete v tématu [instalace nové doménové struktury služby Active Directory systému Windows Server 2012](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) .

> [!TIP]
> Pro zajištění vysoké dostupnosti služeb doporučujeme nastavit několik řadičů domény a serverů DNS.

Po nastavení doménové struktury a domény služby Active Directory můžete do [vCenter přidat zdroj identity](#add-an-identity-source-on-vcenter) pro novou službu Active Directory.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Nová doména služby Active Directory v existující doménové struktuře služby Active Directory

Chcete-li nastavit novou doménu služby Active Directory v existující doménové struktuře služby Active Directory, budete potřebovat:

* Připojení VPN typu Site-to-site k umístění doménové struktury služby Active Directory.
* Server DNS pro překlad názvu vaší existující doménové struktury služby Active Directory.

Podrobné pokyny najdete v tématu [instalace nové podřízené domény nebo domény stromové struktury služby Active Directory systému Windows Server 2012](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) .

Po nastavení domény služby Active Directory můžete do [vCenter přidat zdroj identity](#add-an-identity-source-on-vcenter) pro novou službu Active Directory.

## <a name="set-up-active-directory-on-azure"></a>Nastavení služby Active Directory v Azure

Služba Active Directory běžící v Azure je podobná službě Active Directory běžící v místním prostředí.  K nastavení služby Active Directory běžící v Azure jako zdroje identity jednotného přihlašování na vCenter musí mít vCenter Server a PSC připojení k síti Azure Virtual Network, kde jsou spuštěné služby Active Directory.  Toto připojení můžete vytvořit pomocí [azure Virtual Networkho připojení pomocí služby ExpressRoute](azure-expressroute-connection.md) z Azure Virtual Network, kde jsou spuštěné služby Active Directory pro CloudSimple privátní cloud.

Po navázání síťového připojení použijte postup v části [Přidání místní služby Active Directory jako zdroje identity jednotného přihlašování](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) a přidejte ho jako zdroj identity.  

## <a name="add-an-identity-source-on-vcenter"></a>Přidání zdroje identity na vCenter

1. [Eskalace oprávnění](escalate-private-cloud-privileges.md) ve vašem privátním cloudu.

2. Přihlaste se k vCenter pro váš privátní cloud.

3. Vyberte možnost **domovská > Správa**.

    ![Správa](media/OnPremAD01.png)

4. Vyberte **konfiguraci jednotného přihlašování >** .

    ![Jednotné přihlašování](media/OnPremAD02.png)

5. Otevřete kartu **zdroje identit** a kliknutím na **+** přidejte nový zdroj identity.

    ![Zdroje identity](media/OnPremAD03.png)

6. Vyberte **Active Directory jako server LDAP** a klikněte na **Další**.

    ![Adresář služby Active Directory](media/OnPremAD04.png)

7. Zadejte zdrojové parametry identity pro vaše prostředí a klikněte na **Další**.

    ![Adresář služby Active Directory](media/OnPremAD05.png)

8. Zkontrolujte nastavení a klikněte na **Dokončit**.
