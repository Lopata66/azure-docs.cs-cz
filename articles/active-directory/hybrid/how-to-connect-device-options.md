---
title: 'Azure AD Connect: Možnosti zařízení | Dokumentace Microsoftu'
description: Tento dokument podrobně popisuje možnosti zařízení, které jsou k dispozici ve službě Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96ddcdb67ef079cfa23902a1dcb03b0ec61077fe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109529"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect: Možnosti zařízení

V následující dokumentaci najdete informace o různých možnostech zařízení, která je k dispozici ve službě Azure AD Connect. Azure AD Connect můžete nakonfigurovat následující dvě operace: 
* **Připojení k hybridní službě Azure AD**: Pokud má místní prostředí AD nároky na místo a chcete výhody služby Azure AD, můžete implementovat hybridních zařízení připojených k Azure AD. Tato zařízení jsou připojené k vaší místní Active Directory a Azure Active Directory.
* **Zpětný zápis zařízení**: Zpětný zápis zařízení se používá k povolení podmíněného přístupu založené na zařízení se službou AD FS (2012 R2 nebo vyšší) chráněný zařízení

## <a name="configure-device-options-in-azure-ad-connect"></a>Konfigurovat možnosti zařízení ve službě Azure AD Connect

1.  Spuštění služby Azure AD Connect. V **další úkoly** stránce **konfigurovat možnosti zařízení**.  Klikněte na **Další**.
    ![Konfigurovat možnosti zařízení](./media/how-to-connect-device-options/deviceoptions.png) 

    **Přehled** stránky zobrazují podrobnosti.
    ![Přehled](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > K dispozici pouze ve verzi 1.1.819.0 a novějších verzí se nakonfigurovat možnosti zařízení.

2.  Po zadání přihlašovacích údajů pro službu Azure AD, můžete zvolit operaci provést na stránce Možnosti zařízení.
    ![Operace zařízení](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Další postup

* [Konfigurace připojení k hybridní službě Azure AD](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Konfigurace / zakázat zpětný zápis zařízení](how-to-connect-device-writeback.md)

