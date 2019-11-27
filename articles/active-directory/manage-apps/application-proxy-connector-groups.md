---
title: Publikování aplikací v samostatných sítích prostřednictvím skupin konektorů – Azure AD
description: Popisuje, jak vytvářet a spravovat skupiny konektorů v Azure AD Application Proxy.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22fa1de0a0e3bb91480212381e07b17875bf0bf4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275567"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publikování aplikací na samostatných sítí a umístění s využitím skupiny konektorů

Zákazníci využívat Azure AD Application Proxy pro další a další scénáře a aplikace. Proto jsme Proxy aplikací ještě flexibilnější povolením více topologií. Můžete vytvořit skupiny konektorů Proxy aplikací, takže můžete přiřadit konkrétní konektory k poskytování určité aplikace. Tato funkce nabízí další ovládací prvek a způsoby, jak optimalizovat nasazení vašeho Proxy aplikací.

Každý konektor Proxy aplikací je přiřazen do skupiny konektorů. Všechny konektory, které patří do stejné skupiny konektor bude fungovat jako samostatná jednotka pro vysokou dostupnost a vyrovnávání zatížení. Všechny konektory patří do skupiny konektorů. Pokud nevytvoříte skupin, všechny konektory jsou ve výchozí skupině. Váš správce můžete vytvářet nové skupiny a přiřaďte konektory k nim na webu Azure Portal.

Všechny aplikace jsou přiřazeny skupině konektorů. Pokud nevytvoříte skupin, všechny aplikace přiřazené do výchozí skupiny. Ale pokud své konektory uspořádáte do skupin, můžete nastavit každou aplikaci pro práci s konkrétním konektorům skupiny. V takovém případě pouze konektorů v této skupině poskytovat aplikaci na vyžádání. Tato funkce je užitečná, pokud vaše aplikace hostované v různých umístěních. Skupiny konektorů v závislosti na umístění, můžete vytvořit tak, aby aplikace se vždycky obsluhují konektory, které jsou fyzicky blízko je.

> [!TIP]
> Pokud máte velké nasazení Proxy aplikací, nepřiřazovat všechny aplikace do výchozí skupiny konektoru. Tímto způsobem nové konektory nepřijímají žádné živé přenosy, dokud je nezařadíte do skupiny konektor služby active. Tato konfigurace umožňuje také můžete umístit konektory v režimu nečinnosti jejich přesunutím zpět do výchozí skupiny, takže můžete provést údržbu, aniž to ovlivní vaše uživatele.

## <a name="prerequisites"></a>Požadavky

Chcete-li seskupit konektory, musíte se ujistit, že jste [nainstalovali více konektorů](application-proxy-add-on-premises-application.md). Při instalaci nového konektoru se automaticky připojí **výchozí** skupina konektorů.

## <a name="create-connector-groups"></a>Vytvoření skupiny konektorů

Pomocí těchto kroků můžete vytvořit libovolný počet skupin konektoru.

1. Přihlásit se na [Azure Portal](https://portal.azure.com).
1. Vyberte **Azure Active Directory** > **podnikové aplikace** > **aplikační proxy**.
1. Vyberte možnost **Nová skupina konektorů**. Otevře se okno Nová skupina konektorů.

   ![Zobrazuje obrazovku pro výběr nové skupiny konektorů.](./media/application-proxy-connector-groups/new-group.png)

1. Nová skupina konektorů pojmenujte a pak pomocí rozevírací nabídky vyberte, které konektory patří do této skupiny.
1. Vyberte **Uložit**.

## <a name="assign-applications-to-your-connector-groups"></a>Přiřazení aplikací do skupiny konektorů

Tyto kroky použijte pro každou aplikaci, kterou jste publikovali pomocí Proxy aplikace. Když nejprve publikovat, nebo můžete použít tyto kroky pro změnu přiřazení je vždy, když chcete, můžete přiřadit aplikace do skupiny konektorů.

1. Z řídicího panelu pro správu vašeho adresáře vyberte **podnikové aplikace** > **všechny aplikace** > aplikaci, kterou chcete přiřadit ke skupině konektorů > **proxy aplikací**.
1. Pomocí rozevírací nabídky **Skupina konektoru** vyberte skupinu, kterou chcete použít v aplikaci.
1. Vyberte **Uložit** a použijte změnu.

## <a name="use-cases-for-connector-groups"></a>Případy použití pro skupiny konektorů

Skupiny konektorů jsou užitečné pro různé scénáře, včetně:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Servery s několika propojených datových centrech

Mnoho organizací mají počet propojených datových centrech. V tomto případě chcete zachovat co nejvíce přenosů v rámci datacentra nejdříve, protože odkazy na víc datacenter jsou pomalé a drahé. Nasazením konektorů v každé datové centrum, která bude sloužit pouze aplikace, které se nacházejí v rámci datového centra. Tento přístup minimalizuje odkazy mezi datovým centrem a nabízí zcela transparentní prostředí pro vaše uživatele.

### <a name="applications-installed-on-isolated-networks"></a>Aplikace nainstalované v izolovaných sítích

Aplikace je možné hostovat v sítích, které nejsou součástí hlavní podnikové sítě. Skupiny konektorů můžete použít k instalaci vyhrazené konektory na izolované sítě také izolovat aplikace k síti. Obvykle se to stane, když dalších dodavatelů udržuje konkrétní aplikaci pro vaši organizaci.

Skupiny konektorů umožňují nainstalovat vyhrazený konektory pro tyto sítě, které publikují jenom konkrétní aplikace, takže snadněji a bezpečněji externí správu aplikací na jiných výrobců.

### <a name="applications-installed-on-iaas"></a>Aplikace nainstalované na IaaS

Skupiny konektorů pro aplikace nainstalované na IaaS pro přístup k cloudu, poskytuje běžné služby zabezpečený přístup ke všem aplikacím. Skupiny konektorů není vytvoření další závislosti na vaší podnikové síti nebo fragment prostředí aplikace. Konektory lze nainstalovat na každý datového centra v cloudu a slouží pouze aplikace, které se nacházejí v dané síti. Můžete nainstalovat několik konektorů, abyste dosáhli vysoké dostupnosti.

Provést třeba, že organizace, která má několik virtuální počítače připojené k vlastní IaaS hostované virtuální sítě. Umožňuje uživatelům používat tyto aplikace jsou těchto privátních sítí připojené k podnikové síti prostřednictvím sítě site-to-site VPN. Nabízí kvalitní prostředí pro zaměstnance, kteří se nacházejí na místních. Ale nemusí být ideální pro vzdálení zaměstnanci, protože vyžaduje další místní infrastruktury pro směrování přístup, jak je vidět na následujícím diagramu:

![Diagram, který ilustruje síť Azure AD IaaS](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Pomocí skupiny konektorů Proxy aplikací Azure AD můžete povolit běžné služby k zabezpečení přístupu ke všem aplikacím bez vytvoření další závislosti ve vaší podnikové síti:

![Dodavatelé více cloudů Azure AD IaaS](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Více doménových struktur – skupiny různých konektorů pro každou doménovou strukturu

Většina zákazníků, kteří mají nasazenou Proxy aplikací používají jeho jednotného přihlašování (SSO) funkce pomocí protokolu Kerberos omezené delegování (KCD). Za tím účelem počítače tento konektor potřeba být připojený k doméně, která může delegovat uživatelů k aplikaci. KCD podporuje možnosti mezi doménovými strukturami. Ale pro společnosti, kteří mají odlišná vícedoménových prostředí bez vztahu důvěryhodnosti mezi nimi, jeden konektor nelze použít pro všechny doménové struktury. 

Konkrétní konektory můžete být v tomto případě nasazení pro každou doménovou strukturu a nastavit k poskytování aplikací, které byly publikovány poskytovat uživatelům této konkrétní doménové struktury. Každá skupina konektorů představuje jiné doménové struktuře. Když tenanta a většina prostředí jednotně pro všechny doménové struktury, uživatelům je možné přiřadit ke svým aplikacím doménové struktury pomocí skupin Azure AD.

### <a name="disaster-recovery-sites"></a>Servery pro zotavení po havárii

Existují dva různé přístupy, které můžete provést pomocí webu pro zotavení po havárii, v závislosti na tom, jak jsou implementované vaše weby:

* Pokud váš web pro zotavení po Havárii je postavený na režim aktivní – aktivní, kde je úplně stejně jako hlavní lokalitu a má stejné sítě a nastavení služby AD, můžete vytvořit konektory v lokalitě zotavení po Havárii ve stejné skupině konektoru jako hlavní lokalitu. To umožňuje službě Azure AD ke zjišťování převzetí služeb při selhání.
* Pokud webu zotavení po Havárii je oddělená od hlavní web, můžete vytvořit skupinu jiný konektor v lokalitě zotavení po Havárii a buď (1) mít zálohovací aplikace nebo (2) ručně přesměrovat existující aplikace do skupiny konektor zotavení po Havárii podle potřeby.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Poskytování více společností z jednoho tenanta

Existuje mnoho různých způsobů implementace modelu, ve kterém jednoho poskytovatele nasadí a Azure AD udržuje týkající se služby pro více společností. Skupiny konektorů pomoc správci oddělit konektorů a aplikací do různých skupin. Jedním ze způsobů, který je vhodný pro malé společnosti, je použití jedné službě Azure AD tenanta jiné společnosti mají své vlastní název domény a sítě. To platí také pro scénáře M & A a situace, kde jeden IT oddělení slouží několik společností zákonné nebo obchodní důvody.

## <a name="sample-configurations"></a>Ukázky konfigurace

Mezi příklady, které můžete implementovat, patří následující skupiny konektorů.

### <a name="default-configuration--no-use-for-connector-groups"></a>Výchozí konfigurace – bez použití pro skupiny konektorů

Pokud nepoužíváte skupiny konektorů, bude konfigurace vypadat takto:

![Příklad služby Azure AD – žádné skupiny konektorů](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Tato konfigurace je dostatečná pro malá nasazení a testů. Také bude fungovat, pokud má vaše organizace bez stromové struktury síťové topologie.

### <a name="default-configuration-and-an-isolated-network"></a>Výchozí konfigurace a izolované síti

Tato konfigurace je vývojem představ o výchozí hodnotu, ve kterém je konkrétní aplikaci, která běží v izolované síti jako jsou například IaaS, virtuální sítě:

![Příklad služby Azure AD žádné skupiny konektorů a izolované sítě](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Doporučená konfigurace – několik konkrétních skupin a výchozí skupiny pro nečinnosti

Doporučenou konfiguraci pro velkých a složitých organizací je výchozí skupina konektorů jako skupina bariéru všechny aplikace, která se používá pro nečinných nebo nově nainstalované konektory. Všechny aplikace jsou obsluhovány pomocí skupin vlastní konektor. To umožňuje všem složitosti scénáře popsané výše.

V následujícím příkladu společnost má dvě datová centra, A a B, s dva konektory, které každé lokality slouží. Každá lokalita má jiné aplikace, které běží na ní.

![Příklad společnosti se dvěma datacentry a 2 konektory](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>Další kroky

* [Vysvětlení konektorů Azure Proxy aplikací služby AD](application-proxy-connectors.md)
* [Povolení jednoduchého přihlášení](what-is-single-sign-on.md)
