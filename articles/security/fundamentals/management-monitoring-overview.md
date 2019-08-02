---
title: Funkce zabezpečení správy a monitorování – Microsoft Azure | Microsoft Docs
description: Tento článek obsahuje přehled funkcí a služeb zabezpečení, které Azure poskytuje, aby vám pomohly při správě a monitorování cloudových služeb a virtuálních počítačů Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 9ed2d00d6f229d958e0df024c70381051840548c
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726998"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Přehled správy a monitorování zabezpečení Azure
Tento článek obsahuje přehled funkcí a služeb zabezpečení, které Azure poskytuje, aby vám pomohly při správě a monitorování cloudových služeb a virtuálních počítačů Azure.

## <a name="shared-responsibility"></a>Sdílená odpovědnost

Zabezpečení cloudových služeb Microsoftu je partnerství a sdílená odpovědnost mezi vámi a společností Microsoft. Společnost Microsoft zodpovídá za platformu Azure a fyzického zabezpečení svých Datacenter (pomocí ochrany zabezpečení, jako jsou dveře, zámky a ochranné údaje o zamčených položkách. Azure poskytuje na softwarové vrstvě silné úrovně zabezpečení cloudu, které splňují požadavky zákazníků na zabezpečení, ochranu osobních údajů a dodržování předpisů.

Vaše data a identity, odpovědnost za jejich ochranu, zabezpečení vašich místních prostředků a zabezpečení cloudových součástí, na kterých máte kontrolu, můžete vlastnit. Microsoft poskytuje bezpečnostní kontrolu a možnosti, které vám pomůžou chránit vaše data a aplikace. Váš stupeň zodpovědnosti za zabezpečení je založený na typu cloudové služby.

Následující tabulka shrnuje bilanci zodpovědnosti mezi společností Microsoft a zákazníkem.

![Sdílená odpovědnost](./media/management-monitoring-overview/shared-responsibility.png)

Další informace o správě zabezpečení najdete v tématu [Správa zabezpečení v Azure](management.md).

## <a name="role-based-access-control"></a>Řízení přístupu na základě rolí

Access Control na základě rolí (RBAC) poskytuje podrobnou správu přístupu pro prostředky Azure. Pomocí RBAC můžete uživatelům udělit jenom množství přístupu, který potřebují k provádění svých úloh. RBAC vám taky může pomáhat zajistit, že když lidé odejdou z organizace, ztratí přístup k prostředkům v cloudu.

Další informace:

* [Blog týmu služby Active Directory na RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Access Control na základě rolí Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Antimalware

S Azure můžete použít antimalwarový software od hlavních dodavatelů zabezpečení, jako jsou Microsoft, Symantec, Trend Micro, McAfee a Kaspersky. Tento software pomáhá chránit vaše virtuální počítače před škodlivými soubory, adwarem a dalšími hrozbami.

Microsoft Antimalware pro Azure Cloud Services a Virtual Machines nabízí možnost instalace antimalwarového agenta pro role PaaS i pro virtuální počítače. Na základě Endpoint Protection System Center tato funkce přináší ověřenou místní technologii zabezpečení do cloudu.

Nabízíme také rozsáhlou integraci pro [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) produkty trendu [a na](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) platformu Azure. Důkladné zabezpečení je antivirové řešení a SecureCloud je řešení šifrování. Hloubkové zabezpečení je nasazeno v rámci virtuálních počítačů prostřednictvím modelu rozšíření. Pomocí uživatelského rozhraní Azure Portal a PowerShellu se můžete rozhodnout použít hloubkové zabezpečení uvnitř nových virtuálních počítačů, které se prosazují, nebo existující virtuální počítače, které jsou už nasazené.

Společnost Symantec Endpoint Protection (SEP) je také podporována v Azure. Prostřednictvím integrace portálu můžete určit, že chcete na virtuálním počítači použít SEP. SEP se dá nainstalovat na nový virtuální počítač prostřednictvím Azure Portal, nebo se dá nainstalovat na existující virtuální počítač přes PowerShell.

Další informace:

* [Nasazování antimalwarových řešení na virtuálních počítačích Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft Antimalware pro Azure Cloud Services a Virtual Machines](antimalware.md)
* [Postup instalace a konfigurace Trend Micro hlubokého zabezpečení jako služby na virtuálním počítači s Windows](/azure/virtual-machines/windows/classic/install-trend)
* [Jak nainstalovat a nakonfigurovat Symantec Endpoint Protection na virtuálním počítači s Windows](/azure/virtual-machines/windows/classic/install-symantec)
* [Nové antimalwarové možnosti pro ochranu Azure Virtual Machines](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Ověřování Azure Multi-Factor Authentication je metoda ověřování, která vyžaduje použití více než jedné metody ověřování. Přičítá se kritická druhá vrstva zabezpečení pro přihlášení a transakce uživatelů.

Služba Multi-Factor Authentication pomáhá chránit přístup k datům a aplikacím a současně splňuje požadavky uživatelů na jednoduchý proces přihlašování. Zajišťuje silné ověřování prostřednictvím řady možností ověřování (telefonní hovor, textová zpráva nebo oznámení mobilní aplikace nebo ověřovací kód) a tokeny OATH třetích stran.

Další informace:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co je Azure Multi-Factor Authentication?](/azure/active-directory/authentication/multi-factor-authentication)
* [Jak funguje Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Azure ExpressRoute můžete použít k rozšiřování místních sítí do Microsoft Cloud prostřednictvím vyhrazeného privátního připojení, které usnadňuje poskytovatel připojení. Pomocí ExpressRoute můžete navázat připojení ke cloudovým službám Microsoftu, jako je Azure, Office 365 a CRM Online. Připojení může být:

* Síť typu any-to-Any (IP VPN).
* Síť Ethernet typu Point-to-Point.
* Virtuální křížové připojení prostřednictvím poskytovatele připojení v zařízení společného umístění.

Připojení ExpressRoute nejdou přes veřejný Internet. Můžou nabídnout spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes Internet.

Další informace:

* [Technický přehled ExpressRoute](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Brány virtuální sítě

Brány VPN, označované taky jako brány virtuální sítě Azure, se používají k posílání síťového provozu mezi virtuálními sítěmi a místními umístěními. Používají se také k posílání provozu mezi několika virtuálními sítěmi v rámci Azure (síť do sítě). Brány VPN poskytují zabezpečené připojení mezi různými místy mezi Azure a vaší infrastrukturou.

Další informace:

* [Informace o branách VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Přehled zabezpečení sítě Azure](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Někdy uživatelé potřebují provádět privilegované operace v prostředcích Azure nebo jiných aplikacích SaaS. To často znamená, že organizace jim poskytnou trvalý privilegovaný přístup v Azure Active Directory (Azure AD).

Toto je rostoucí bezpečnostní riziko pro prostředky hostované v cloudu, protože organizace nemůžou dostatečně monitorovat, co dělají uživatelé s privilegovaným přístupem. Kromě toho platí, že pokud dojde k ohrožení bezpečnosti uživatelského účtu s privilegovaným přístupem, může toto porušení ovlivnit celkové cloudové zabezpečení organizace. Azure AD Privileged Identity Management pomáhá vyřešit toto riziko tím, že snižuje dobu expozice oprávnění a zvyšuje přehlednost využití.  

Privileged Identity Management zavádí koncept dočasného správce pro roli nebo "just in time" přístup správce. Tento druh správce je uživatel, který potřebuje k dokončení procesu aktivace pro přiřazenou roli. Proces aktivace změní přiřazení uživatele k roli v Azure AD z neaktivní na aktivní pro zadané časové období.

Další informace:

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Začínáme s Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

Azure AD Identity Protection poskytuje ucelený přehled o podezřelých přihlašovacích aktivitách a potenciálních ohroženích zabezpečení, které vám pomůžou ochránit vaši firmu. Identity Protection detekuje podezřelé aktivity uživatelů a privilegovaných identit (správců) na základě signálů, jako jsou:

* Útoky hrubou silou.
* Nevrácená pověření.
* Přihlášení z neznámých umístění a nakažených zařízení.

Díky poskytování oznámení a doporučené nápravy pomáhá ochrana identity zmírnit rizika v reálném čase. Počítá závažnost rizika uživatele. Můžete nakonfigurovat zásady založené na rizicích, které budou automaticky pomáhat chránit přístup k aplikacím před budoucími hrozbami.

Další informace:

* [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection)
* [Kanál 9: Azure AD a zobrazení identity: Identity Protection ve verzi Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center

Azure Security Center pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně. Security Center poskytuje lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované monitorování zabezpečení a správu zásad napříč předplatnými Azure. Pomáhá detekovat hrozby, které by jinak neinformovaly, a spolupracuje s širokou ekosystémem řešení zabezpečení.

Security Center vám pomůže optimalizovat a monitorovat zabezpečení prostředků Azure pomocí těchto akcí:

* Vám umožní definovat zásady pro vaše prostředky předplatného Azure podle těchto pravidel:
  * Požadavky na zabezpečení vaší společnosti.
  * Typ aplikací nebo citlivosti dat v každém předplatném.
* Monitorování stavu virtuálních počítačů, sítí a aplikací Azure.
* Poskytuje seznam výstrah zabezpečení s určením priorit, včetně výstrah z integrovaných partnerských řešení. Poskytuje také informace, které potřebujete k rychlému prověření útoků a doporučení k tomu, jak je opravit.

Další informace:

* [Úvod do Azure Security Center](../../security-center/security-center-intro.md)
* [Vylepšit vaše zabezpečené skóre v Azure Security Center](../../security-center/security-center-secure-score.md)

## <a name="intelligent-security-graph"></a>Intelligent Security Graph

Intelligent Security Graph poskytuje ochranu před hrozbami v reálném čase v produktech a službách Microsoftu. Používá pokročilou analýzu, která propojí obrovské množství informací o hrozbách a zabezpečení dat a poskytuje přehledy, které mohou posílit zabezpečení organizace. Microsoft používá pokročilou analýzu – zpracovává více než 450 000 000 000 ověřování za měsíc, prohledává 400 000 000 000 e-mailů pro malware a útoky phishing a aktualizuje 1 000 000 000 zařízení, aby poskytoval širší přehledy. které mohou vaší organizaci pomoci zjišťovat útoky a rychle na ně reagovat.

* [Intelligent Security Graph](https://www.microsoft.com/security/intelligence)

