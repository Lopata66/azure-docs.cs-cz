---
title: 'Azure AD Connect synchronizace: rozšíření adresáře | Microsoft Docs'
description: Toto téma popisuje funkci rozšíření adresáře v Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3afec7c31488529af9ba15100c914899a0d1d808
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85357456"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect synchronizace: přípony adresářů
K rozšíření schématu v Azure Active Directory (Azure AD) s vlastními atributy z místní služby Active Directory můžete použít rozšíření adresáře. Tato funkce umožňuje sestavovat obchodní aplikace pomocí atributů, které budete nadále spravovat místně. Tyto atributy lze spotřebovat prostřednictvím [rozšíření](https://docs.microsoft.com/graph/extensibility-overview
). Dostupné atributy můžete zobrazit pomocí [Microsoft Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer). Tuto funkci můžete také použít k vytvoření dynamických skupin ve službě Azure AD.

V současné době žádné úlohy Office 365 nevyužívají tyto atributy.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Přizpůsobení atributů, které se mají synchronizovat se službou Azure AD

Další atributy, které chcete synchronizovat, můžete nakonfigurovat v cestě k vlastnímu nastavení v Průvodci instalací nástroje.

>[!NOTE]
>V poli dostupné atributy se rozlišují malá a velká písmena.

![Průvodce rozšířením schématu](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

Instalace zobrazuje následující atributy, které jsou platné kandidáty:

* Typy objektů uživatelů a skupin
* Atributy s jednou hodnotou: řetězec, logická hodnota, celé číslo, binární
* Vícehodnotových atributů: String, binary


>[!NOTE]
> I když Azure AD Connect podporuje synchronizaci vícehodnotových atributů služby Active Directory se službou Azure AD jako víceřádková rozšíření adresáře, neexistuje momentálně žádný způsob, jak načíst a využívat data nahraná v atributech rozšíření adresáře s více hodnotami.

Seznam atributů je načten z mezipaměti schématu, která je vytvořena během instalace Azure AD Connect. Pokud jste rozšíření schématu služby Active Directory rozšířili o další atributy, je nutné [schéma aktualizovat](how-to-connect-installation-wizard.md#refresh-directory-schema) předtím, než budou tyto nové atributy viditelné.

Objekt ve službě Azure AD může mít až 100 atributů pro rozšíření adresáře. Maximální délka je 250 znaků. Pokud je hodnota atributu delší, synchronizační modul ho zkrátí.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Změny konfigurace ve službě Azure AD provedené Průvodcem

Během instalace Azure AD Connect je aplikace zaregistrovaná tam, kde jsou tyto atributy k dispozici. Tuto aplikaci můžete zobrazit v Azure Portal. Jeho název je vždycky **aplikace rozšíření schématu tenanta**.

![Aplikace rozšíření schématu](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Ujistěte se, že jste vybrali možnost **všechny aplikace** , aby se tato aplikace zobrazila.

Atributy mají předponu **rozšíření \_ {ApplicationId} \_ **. ApplicationId má stejnou hodnotu pro všechny atributy v tenantovi Azure AD. Tuto hodnotu budete potřebovat pro všechny ostatní scénáře v tomto tématu.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Zobrazení atributů pomocí rozhraní Microsoft Graph API

Tyto atributy jsou nyní k dispozici prostřednictvím rozhraní Microsoft Graph API pomocí [Microsoft Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> V rozhraní Microsoft Graph API musíte požádat o vrácení atributů. Explicitně vyberte atributy takto: `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division` .
>
> Další informace najdete v tématu [Microsoft Graph: použití parametrů dotazu](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="use-the-attributes-in-dynamic-groups"></a>Použití atributů v dynamických skupinách

Jedním z užitečnějších scénářů je použití těchto atributů v části dynamické zabezpečení nebo skupiny Office 365.

1. Vytvoří novou skupinu ve službě Azure AD. Dejte mu dobrý název a ujistěte se, že je **typ členství** **dynamický uživatel**.

   ![Snímek obrazovky s novou skupinou](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Tuto možnost vyberte, pokud chcete **Přidat dynamický dotaz**. Pokud se podíváte na vlastnosti, tyto rozšířené atributy se nezobrazí. Musíte je nejdřív přidat. Klikněte na **načíst vlastní vlastnosti rozšíření**, zadejte ID aplikace a klikněte na **aktualizovat vlastnosti**.

   ![Snímek obrazovky s přidanými rozšířeními adresářů](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Otevřete rozevírací seznam vlastnosti a Všimněte si, že přidané atributy jsou nyní viditelné.

   ![Snímek obrazovky s novými atributy zobrazenými v uživatelském rozhraní](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Dokončete výraz tak, aby vyhovoval vašim požadavkům. V našem příkladu je pravidlo nastavené na **(User. extension_9d98ed114c4840d298fad781915f27e4_division-EQ "Sales and marketing")**.

4. Po vytvoření skupiny poskytněte službě Azure AD nějakou dobu k naplnění členů a pak zkontrolujte členy.

   ![Snímek obrazovky se členy dynamické skupiny](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o konfiguraci [Azure AD Connect synchronizace](how-to-connect-sync-whatis.md) .

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
