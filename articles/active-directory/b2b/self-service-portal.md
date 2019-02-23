---
title: Registrační portál samoobslužné služby pro spolupráci B2B – Azure Active Directory | Dokumentace Microsoftu
description: Spolupráce B2B ve službě Azure Active Directory podporuje vaše vztahy s ostatními společnostmi tím, že vašim obchodním partnerům umožní selektivní přístup ke podnikovým aplikacím
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c3ad424e8cab444b2405715eaa468411166ebd9
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674272"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Portál se samoobslužnou registrací pro spolupráci B2B služby Azure AD

Zákazníci mají spoustu možností, jak využít integrované funkce, ke kterým mají přístup prostřednictvím webu [Azure Portal](https://portal.azure.com) a [přístupového panelu aplikací](https://myapps.microsoft.com) pro koncové uživatele. Pracovní postup onboardingu můžete ale potřebovat přizpůsobit pro uživatele B2B, aby vyhovoval potřebám vaší organizace. Můžete to udělat pomocí [rozhraní API pro pozvánky](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Jako zvoucí organizace nemusíte vždy předem vědět, kdo jednotliví externí spolupracovníci, kteří potřebují přístup k vašim prostředkům, jsou. Potřebujete způsob, kterým se můžou uživatelé z partnerských společností zaregistrovat sami a získat sadu zásad, které jako zvoucí organizace řídíte. Tento scénář je možné provést prostřednictvím rozhraní API. [Na GitHubu existuje ukázkový projekt](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web), který přesně tohle dokáže.

Tento projekt na GitHubu ukazuje, jak můžou organizace použít rozhraní API, aby poskytnuly důvěryhodným partnerům samoobslužné registrace založené na zásadách s pravidly, které budou určovat přístupné aplikace. Partnerští uživatelé můžou k prostředkům v případě potřeby přistupovat. Můžou to dělat bezpečně, aniž by je zvoucí organizace musela onboardovat ručně. Tento projekt můžete jednoduše nasadit do libovolného předplatného Azure.

## <a name="as-is-code"></a>Kód „tak, jak je“

Tento kód je k dispozici jako ukázka použití rozhraní API pro pozvánky Azure Active Directory B2B. Váš tým vývojářů nebo partner by ho měl přizpůsobit a před nasazením do produkčního prostředí byste ho měli zkontrolovat.

## <a name="next-steps"></a>Další postup

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Licencování spolupráce B2B ve službě Azure AD](licensing-guidance.md)
* [Nejčastější dotazy týkající se spolupráce Azure Active Directory s B2B](faq.md)
