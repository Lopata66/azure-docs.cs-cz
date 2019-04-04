---
title: Instalace místní brány dat | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat a nakonfigurovat službu On-premises data gateway.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 629a97048ceba4ac02e3aa1dd59310980e5a0c95
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894162"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalace a konfigurace místní brány dat

Při jeden nebo více serverů služby Azure Analysis Services ve stejné oblasti připojení k místním zdrojům dat, místní brány dat se vyžaduje. Další informace o bráně, naleznete v tématu [On-premises data gateway](analysis-services-gateway.md).

## <a name="prerequisites"></a>Požadavky

**Minimální požadavky:**

* .NET 4.5 Framework
* 64bitová verze Windows 7 / Windows Server 2008 R2 (nebo novější)

**Doporučené:**

* Procesor s 8 jádry
* 8 GB paměti
* 64bitová verze Windows 2012 R2 (nebo novější)

**Důležité informace:**

* Během instalace, při registraci vaší brány Azure je vybrán výchozí oblast pro vaše předplatné. Můžete použít v jiné oblasti. Pokud máte servery ve více než jedné oblasti, musíte nainstalovat bránu pro každou oblast. 
* Brány nelze nainstalovat na řadič domény.
* V jednom počítači lze nainstalovat pouze jednu bránu.
* Bránu nainstalujte na počítač, který zůstane na a ne přechází do režimu spánku.
* Bránu neinstalujte na počítač bezdrátově připojené k vaší síti. Výkon můžete snížila.
* Při instalaci brány, uživatelský účet, který jste přihlášení k počítači pomocí musí mít protokolu jako oprávnění služby. Po dokončení instalace službu On-premises data gateway používá účet NT SERVICE\PBIEgwService přihlásit jako službu. Jiný účet lze zadat během instalace nebo služby, po dokončení instalace. Nastavení zásad skupiny zkontrolujte, zda povolit, že účet, který jste přihlášení se při instalaci a účet služby, kterou zvolíte mají protokolu na jako oprávnění služby.
* Přihlášení k Azure pomocí účtu ve službě Azure AD pro stejné [tenanta](/previous-versions/azure/azure-services/jj573650(v=azure.100)#BKMK_WhatIsAnAzureADTenant) jako předplatné při registraci brány v. Azure B2B účty (Host) nejsou podporována při instalaci a registraci brány.
* Pokud jsou zdroje dat ve službě Azure Virtual Network (VNet), je nutné nakonfigurovat [AlwaysUseGateway](analysis-services-vnet-gateway.md) vlastnost serveru.
* (Sjednocené) brány je zde popsáno, není podporována v oblastech Azure Germany. Místo toho použijte **vyhrazená místní brána pro Azure Analysis Services**, nainstalované ze svého serveru **rychlý Start** na portálu. 


## <a name="download"></a>Stáhnout

 [Stáhněte si bránu](https://aka.ms/azureasgateway)

## <a name="install"></a>Instalace

1. Spusťte instalační program.

2. Vyberte umístění, přijměte podmínky a pak klikněte na tlačítko **nainstalovat**.

   ![Nainstalujte umístění a licenční podmínky](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Přihlaste se k Azure. Účet musí být ve vašem tenantovi Azure Active Directory. Tento účet slouží pro správce brány. Azure B2B účty (Host) nejsou podporována při instalaci a registraci brány.

   ![Přihlásit se k Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Pokud jste se přihlásit pomocí účtu domény, se mapuje na váš účet organizace ve službě Azure AD. Váš účet organizace se používá jako správce brány.

## <a name="register"></a>Registrace

Chcete-li vytvořit bránu prostředků v Azure, je nutné zaregistrovat místní instance, kterou jste nainstalovali, se Cloudová služba brány. 

1.  Vyberte **registrovat novou bránu na tomto počítači**.

    ![Registrace](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Zadejte název a obnovení klíče pro bránu. Ve výchozím nastavení brána používá výchozí oblasti vašeho předplatného. Pokud je potřeba vybrat jinou oblast, vyberte **změnit oblast**.

    > [!IMPORTANT]
    > Obnovovací klíč uložte na bezpečném místě. Obnovovací klíč se vyžaduje mohli převzetí, migrovat nebo obnovit bránu. 

   ![Registrace](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Vytvoření prostředku Azure gateway

Po nainstalovaný a zaregistrovaný bránu, je potřeba vytvořit prostředek brány ve vašem předplatném Azure. Přihlaste se k Azure pomocí stejného účtu, který jste použili při registraci brány.

1. Na webu Azure portal, klikněte na tlačítko **vytvořit prostředek** > **integrace** > **On-premises data gateway**.

   ![Vytváření prostředku brány](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. V **vytvořit připojení brány**, zadejte tato nastavení:

   * **Název**: Zadejte název pro prostředek brány. 

   * **Předplatné**: Vyberte předplatné Azure, které chcete přidružit k prostředku brány. 
   
     Výchozí odběr je založený na účtu Azure, který jste použili k přihlášení.

   * **Skupina prostředků**: Vytvořte skupinu prostředků nebo vyberte existující.

   * **Umístění**: Vyberte oblast, které jste zaregistrovali bránu.

   * **Název instalace**: Pokud se instalace brány není vybrána, vyberte bránu, zaregistrovaný. 

     Jakmile budete hotovi, klikněte na tlačítko **vytvořit**.

## <a name="connect-servers"></a>Připojení serverů k prostředku brány

1. V přehledu vaší služby Azure Analysis Services serveru, klikněte na tlačítko **On-Premises Data Gateway**.

   ![Připojení serveru k bráně](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. V **vybrat službu On-Premises Data Gateway pro připojení**, vyberte prostředek brány a potom klikněte na tlačítko **připojit vybranou bránu**.

   ![Připojení serveru k prostředku brány](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Pokud vaše brána se nezobrazí v seznamu, je váš server pravděpodobně že není ve stejné oblasti jako oblast jste zadali při registraci brány. 

A to je vše. Pokud je potřeba otevřít porty nebo provést další řešení potíží, nezapomeňte se podívat [On-premises data gateway](analysis-services-gateway.md).

## <a name="next-steps"></a>Další postup

* [Správa služby Analysis Services](analysis-services-manage.md)   
* [Získání dat ze služby Azure Analysis Services](analysis-services-connect.md)   
* [Použití brány pro zdroje dat ve službě Azure Virtual Network](analysis-services-vnet-gateway.md)
