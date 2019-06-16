---
title: Řešení potíží s protokol Secure LDAP (LDAPS) ve službě Azure AD Domain Services | Dokumentace Microsoftu
description: Řešení potíží s Secure LDAP (LDAPS) pro spravované domény služby Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: c9732545e1759ea23d62c0a56379e3868ed40a0b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246626"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Řešení potíží s Secure LDAP (LDAPS) pro spravované domény služby Azure AD Domain Services

## <a name="connection-issues"></a>Problémy s připojením
Pokud máte potíže s připojením ke spravované doméně pomocí protokolu secure LDAP:

* Na straně klienta musí důvěřovat řetězu vystavitele certifikát protokolu secure LDAP. Můžete přidat kořenové certifikační autority do úložiště důvěryhodných kořenových certifikátů na straně klienta k navázání vztahu důvěryhodnosti.
* Ověřte, že klienta protokolu LDAP (například ldp.exe) připojí k zabezpečení koncového bodu protokolu LDAP pomocí názvu DNS není IP adresa.
* Zkontrolujte název DNS, který klient LDAP se připojí k. Musí se přeložit na veřejnou IP adresu pro protokol secure LDAP ve spravované doméně.
* Ověřte, že certifikát protokolu secure LDAP pro spravovanou doménu má v předmětu nebo alternativní názvy subjektů atribut názvu DNS.
* Nastavení skupiny zabezpečení sítě pro virtuální síť musí umožňovat provoz na portu 636 z Internetu. Tento krok platí jenom v případě, že jste povolili přístup protokolu secure LDAP přes internet.


## <a name="need-help"></a>Potřebujete pomoc?
Pokud stále máte potíže s připojením ke spravované doméně pomocí protokolu secure LDAP [kontaktováním produktového týmu](contact-us.md) nápovědu. Uveďte následující informace umožňující provádět lepší diagnostiku problému:
* Snímek obrazovky s ldp.exe vytváření připojení a selhání.
* Vaše ID tenanta Azure AD a název domény DNS vaší spravované domény.
* Přesné uživatelské jméno, které se snažíte vytvořit vazbu jako.


## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](create-instance.md)
* [Spravovat domény služby Azure AD Domain Services](manage-domain.md)
* [Základy dotazů protokolu LDAP](https://technet.microsoft.com/library/aa996205.aspx)
* [Správa zásad skupiny pro službu Azure AD Domain Services](manage-group-policy.md)
* [Skupiny zabezpečení sítě](../virtual-network/security-overview.md)
* [Vytvořte skupinu zabezpečení sítě](../virtual-network/tutorial-filter-network-traffic.md)
