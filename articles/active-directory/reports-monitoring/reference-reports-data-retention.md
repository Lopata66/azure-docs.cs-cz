---
title: Jak dlouho jsou data sestav služby Azure AD Store? | Dokumentace Microsoftu
description: Přečtěte si, jak dlouho Azure ukládá různé typy dat generování sestav.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54636600c208f8f5df9fa2e25460c63dd9f46e85
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80239549"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Jak dlouho jsou data sestav služby Azure AD Store?


V tomto článku se dozvíte o zásadách uchovávání dat pro různé sestavy aktivit v Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Kdy Azure AD začne shromažďovat data?

| Edice Azure AD | Začátek shromažďování |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Když se přihlásíte k předplatnému |
| Azure AD Free| Při prvním otevření okna [Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) nebo použití [rozhraní API pro vytváření sestav](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Kdy jsou data aktivity k dispozici v Azure Portal?

- **Okamžitě** – Pokud jste již pracovali se sestavami v Azure Portal.
- **Do 2 hodin** – Pokud jste neaktivovali vytváření sestav v Azure Portal.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Jak brzy můžu zobrazit data o aktivitách po získání licence na prémii?

Pokud již máte data o aktivitách s bezplatnou licencí, můžete ji okamžitě zobrazit při upgradu. Pokud nemáte žádná data, bude trvat jeden nebo dva dny, než se data zobrazí v sestavách po upgradu na licenci Premium.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Můžu zobrazit data z posledního měsíce po získání licence Azure AD Premium?

Pokud jste nedávno přešli na verzi Premium (včetně zkušební verze), můžete nejdřív zobrazit data až 7 dní. Po shromáždění dat můžete zobrazit data za posledních 30 dní.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Kdy Azure AD začne shromažďovat data bezpečnostních signálů?  

Pro bezpečnostní signály se proces shromažďování spustí, když se přihlásíte k používání **centra ochrany identit**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Jak dlouho Azure AD ukládá data?

**Sestavy aktivit**    

| Sestava                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Protokoly auditu             | 7 dní        | 30 dní             | 30 dní             |
| Přihlášení               | 7 dní        | 30 dní             | 30 dní             |
| Využití Azure MFA        | 30 dní       | 30 dní             | 30 dní             |

Data aktivity auditu a přihlašování můžete uchovávat déle než výchozí doba uchování, která je uvedená výše, směrováním do účtu služby Azure Storage pomocí Azure Monitor. Další informace najdete v tématu [archivace protokolů služby Azure AD do účtu služby Azure Storage](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Signály zabezpečení**

| Sestava         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Ohrožení uživatelé  | 7 dní        | 30 dní             | 90 dnů             |
| Riziková přihlášení | 7 dní        | 30 dní             | 90 dnů             |

---
