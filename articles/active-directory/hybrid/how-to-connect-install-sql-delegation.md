---
title: Instalace služby Azure AD Connect pomocí oprávnění delegovaného správce SQL | Dokumentace Microsoftu
description: Toto téma popisuje aktualizaci služby Azure AD Connect, která umožňuje instalaci pomocí účtu, který má pouze oprávnění SQL dbo.
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6269d00c9a6a8f827a4e31044d9d20efb0f8471b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60243515"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Instalace služby Azure AD Connect pomocí oprávnění delegovaného správce SQL
Před na nejnovější verzi Azure AD Connect správu delegování, při nasazování konfigurace, které vyžaduje SQL, nepodporovaly.  Uživatelé, kteří chtěli nainstalovat Azure AD Connect je potřeba mít oprávnění správce serveru na SQL serveru.

S nejnovější verzí služby Azure AD Connect zřizování může databáze nyní možné provádět vzdáleně správce SQL a následně je nainstalován Správce služby Azure AD Connect s oprávněními vlastníka databáze.

## <a name="before-you-begin"></a>Než začnete
Chcete-li tuto funkci použít, budete muset dobré si uvědomit, že existuje několik pohyblivých částí a každý z nich může zahrnovat jiného správce ve vaší organizaci.  Následující tabulka shrnuje jednotlivé role a jejich povinností v nasazení služby Azure AD Connect s touto funkcí.

|Role|Popis|
|-----|-----|
|Doména nebo doménová struktura AD správce|Vytvoří účet služeb na úrovni domény, který slouží ke spouštění služby synchronizace Azure AD Connect.  Další informace o účtech služby najdete v tématu [účtech a oprávněních](reference-connect-accounts-permissions.md).
|Správce SQL|Vytvoří databázi ADSync a udělí přihlášení + dbo přístup správce Azure AD Connect a účet služby, který vytvořil správce domény nebo doménové struktuře.|
Správce služby Azure AD Connect|Nainstaluje služby Azure AD Connect a určuje účet služby při vlastní instalaci.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Kroky pro instalaci Azure AD Connect s použitím SQL delegovaná oprávnění
Zřízení databáze mimo IP síť a nainstalujte Azure AD Connect s oprávněními vlastníka databáze, použijte následující postup.

>[!NOTE]
>Ačkoli není vyžadována, je **důrazně doporučujeme** , že kolace Latin1_General_CI_AS určen při vytváření této databáze.


1. Požádejte správce SQL vytvořit databázi ADSync pomocí pořadí řazení malá a velká písmena **(Latin1_General_CI_AS)** .  Databáze musí mít název **ADSync**.  Model obnovení, úroveň kompatibility a typ členství ve skupině se aktualizují na správné hodnoty při instalaci Azure AD Connect.  Ale pořadí kolace musí být správně nastaveny správcem SQL jinak Azure AD Connect bude blokovat instalaci.  K obnovení přidružení zabezpečení musíte odstranit a znovu vytvořit databázi.
 
   ![Kolace](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Udělte oprávnění správce služby Azure AD Connect a doménu účtu služby:
   - Přihlášení k SQL serveru 
   - **databáze owner(dbo)** práva.
 
   ![Oprávnění](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >Azure AD Connect nepodporuje přihlášení pomocí vnořených členství.  To znamená, že účet správce Azure AD Connect a účet doménové služby je potřeba propojit přihlášení, které jsou udělena oprávnění dbo.  Nelze jednoduše členem skupiny, který je přiřazen k přihlášení s oprávněními vlastníka databáze.

3. Pošlete e-mail na správce Azure AD Connect označující název SQL serveru a instance, který se má použít při instalaci Azure AD Connect.

## <a name="additional-information"></a>Další informace
Po zřízení databáze správce Azure AD Connect můžete nainstalovat a nakonfigurovat synchronizaci s místními svých možností.

V případě, že správce SQL obnovila databáze ADSync z předchozí zálohy služby Azure AD Connect, je potřeba nainstalovat nový server Azure AD Connect pomocí stávající databáze. Další informace o instalaci Azure AD Connect s existující databázi, naleznete v tématu [instalace služby Azure AD Connect s použitím existující databáze ADSync](how-to-connect-install-existing-database.md).

## <a name="next-steps"></a>Další postup
- [Začínáme se službou Azure AD Connect s použitím expresního nastavení](how-to-connect-install-express.md)
- [Vlastní instalace služby Azure AD Connect](how-to-connect-install-custom.md)
- [Instalace nástroje Azure AD Connect s využitím existující databáze ADSync](how-to-connect-install-existing-database.md)  
