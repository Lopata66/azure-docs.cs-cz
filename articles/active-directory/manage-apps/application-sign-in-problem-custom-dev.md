---
title: Potíže při přihlašování k aplikaci vyvinuté | Dokumentace Microsoftu
description: Běžné chyby, které by mohlo být příčinou je nebudou moci přihlásit k aplikaci vytvořili pomocí Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ad2499aea8bf4e41ca00d6c78d76e112f0493e
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825238"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Potíže při přihlašování k vlastní vyvinuté aplikaci

Existuje několik chyb, které by mohlo být příčinou je nebudou moci přihlásit do aplikace. Největší důvod uživatelé setkávají, které tento problém je špatně nakonfigurovaný. aplikace.

## <a name="errors-related-to--misconfigured-apps"></a>Chyby související s nesprávné konfigurace aplikace

* Ověřte, že konfigurace na portálu odpovídat, co máte ve vaší aplikaci. Konkrétně porovnejte ID klienta nebo aplikace, adresy URL odpovědí, klienta tajných kódů a klíčů a identifikátor URI ID aplikace.

* Prostředek se vyžádání přístupu ke službě v kódu s nakonfigurovanou oprávnění v porovnání **požadované prostředky** kartu a ujistěte se, že jste pouze požadavky na prostředky, které jste nakonfigurovali.

* Zobrazit [Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) všechny podobné chyby nebo problémy.

## <a name="next-steps"></a>Další postup

[Příručka pro vývojáře Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Vyjádření souhlasu a integraci aplikací do služby Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Videí k Azure AD v2.0 a vyjádření souhlasu konvergované aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
