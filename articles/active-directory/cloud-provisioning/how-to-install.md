---
title: Instalace agenta zřizování cloudů Azure AD Connect
description: Tento článek popisuje, jak nainstalovat agenta pro zřizování cloudu Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6f06e9ad03face5082950ecedd3f73b97dc6ae4
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2020
ms.locfileid: "85360924"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Instalace agenta zřizování cloudů Azure AD Connect
Tento dokument vás provede procesem instalace agenta zřizování služby Azure Active Directory (Azure AD) Connect a jeho počáteční konfigurace v Azure Portal.

>[!IMPORTANT]
>Následující pokyny k instalaci předpokládají, že byly splněny všechny [požadavky](how-to-prerequisites.md) .

Instalace a konfigurace Azure AD Connect zřizování se provádí v následujících krocích:
    
- [Instalace agenta](#install-the-agent)
- [Ověřit instalaci agenta](#verify-agent-installation)


## <a name="install-the-agent"></a>Instalace agenta
Chcete-li nainstalovat agenta, postupujte podle těchto kroků.

1. Přihlaste se k serveru, který budete používat s oprávněními podnikového správce.
1. Přejděte na web Azure Portal. Na levé straně vyberte **Azure Active Directory**.
1. Vyberte **Spravovat zřizování (Preview)**  >  **Zkontrolujte všechny agenty**.
1. Stáhněte si Azure AD Connect agenta zřizování z Azure Portal.

   ![Stáhnout místního agenta](media/how-to-install/install9.png)</br>
1. Spusťte instalační program pro zřizování služby Azure AD Connect (AADConnectProvisioningAgent. Installer).
1. Na obrazovce **Microsoft Azure AD připojení zřizovacího agenta** přijměte licenční podmínky a vyberte **nainstalovat**.

   ![Obrazovka balíčku pro zřízení zřizovacího agenta Microsoft Azure AD připojení](media/how-to-install/install1.png)</br>

1. Po dokončení této operace se spustí Průvodce konfigurací nástroje. Přihlaste se pomocí účtu globálního správce služby Azure AD.
1. Na obrazovce **připojit ke službě Active Directory** vyberte **Přidat adresář**. Pak se přihlaste pomocí účtu správce služby Active Directory. Tato operace přidá váš místní adresář. Vyberte **Další**.

   ![Obrazovka připojit ke službě Active Directory](media/how-to-install/install3.png)</br>

1. Na obrazovce **Konfigurace byla dokončena** vyberte **Potvrdit**. Tato operace zaregistruje a restartuje agenta.

   ![Obrazovka dokončení konfigurace](media/how-to-install/install4.png)</br>

1. Po dokončení této operace by se měla zobrazit oznámení, že **vaše konfigurace agenta byla úspěšně ověřena.** Vyberte možnost **ukončit**.

   ![Tlačítko Konec](media/how-to-install/install5.png)</br>
1. Pokud se stále zobrazuje úvodní obrazovka úvodního **balíčku Microsoft Azure AD připojení** , vyberte **Zavřít**.

## <a name="verify-agent-installation"></a>Ověřit instalaci agenta
K ověření agenta dochází v Azure Portal a na místním serveru, na kterém je spuštěný agent.

### <a name="azure-portal-agent-verification"></a>Ověřování agenta Azure Portal
Pokud chcete ověřit, jestli je agent v Azure, postupujte podle těchto kroků.

1. Přihlaste se k portálu Azure.
1. Na levé straně vyberte **Azure Active Directory**  >  **Azure AD Connect**. V centru vyberte **Spravovat zřizování (Preview)**.

   ![portál Azure](media/how-to-install/install6.png)</br>

1.  Na obrazovce **Azure AD zřizování (Preview)** vyberte **zkontrolovat všechny agenty**.

    ![Možnost zkontrolovat všechny agenty](media/how-to-install/install7.png)</br>
 
1. Na obrazovce místní **zřizovací agenti** uvidíte agenty, které jste nainstalovali. Ověřte, zda je příslušný agent a označen jako *aktivní*.

   ![Obrazovka místních zřizovacích agentů](media/how-to-install/verify1.png)</br>



### <a name="on-the-local-server"></a>Na místním serveru
Pokud chcete ověřit, jestli je agent spuštěný, postupujte podle těchto kroků.

1.  Přihlaste se k serveru pomocí účtu správce.
1.  Otevřete **služby** tak, že k němu přejdete, nebo když přejdete na **Spustit**  >  **Spustit**  >  **Services. msc**.
1.  V části **služby**se ujistěte, že je k dispozici aktualizace **agenta Microsoft Azure AD Connect** a **Microsoft Azure AD připojení zřizování** , a jejich stav je *spuštěný*.

    ![Obrazovka služby](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>Agent byl nainstalován, ale musí být nakonfigurován a povolen předtím, než bude moci spustit synchronizaci uživatelů. Pokud chcete nakonfigurovat nového agenta, přečtěte si téma [Vytvoření nové konfigurace pro Azure AD Connect cloudové zřizování](how-to-configure.md).



## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
 
