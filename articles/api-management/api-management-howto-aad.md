---
title: Autorizace vývojářských účtů pomocí Azure Active Directory – Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak autorizovat uživatele pomocí služby Azure Active Directory ve službě API Management.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: d267ff3a43438d9fe6e4e21f0ac023cfa6675f19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956299"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorizace vývojářských účtů pomocí Azure Active Directory ve službě Azure API Management

V tomto článku se dozvíte, jak povolit přístup k portálu pro vývojáře pro uživatele ze služby Azure Active Directory (Azure AD). Tento průvodce také ukazuje, jak spravovat skupiny uživatelů Azure AD tak, že přidáte externí skupiny, které obsahují uživatele.

## <a name="prerequisites"></a>Požadavky

- Projděte si následující rychlý start: [Vytvoření instance Azure API Management](get-started-create-service-instance.md).
- Import a publikování instance Azure API Management. Další informace najdete v tématu [Import a publikování](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorizace vývojářských účtů pomocí Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
2. Vyberte ![šipka](./media/api-management-howto-aad/arrow.png).
3. Typ **api** do vyhledávacího pole.
4. Vyberte **služby API Management**.
5. Vyberte instanci služby API Management.
6. V části **zabezpečení**vyberte **identit**.
7. Vyberte **+ přidat** shora.

    **Přidat zprostředkovatele identity** otevře se podokno na pravé straně.
8. V části **typ zprostředkovatele**vyberte **Azure Active Directory**.

    V podokně se zobrazí ovládací prvky, které vám umožní zadat další potřebné informace. Ovládací prvky zahrnují **ID klienta** a **tajný kód klienta**. (Dále v tomto článku získáte informace o těchto ovládacích prvků.)
9. Poznamenejte si obsah **adresy URL pro přesměrování**.
    
   ![Kroky pro přidání poskytovatele identit na portálu Azure portal](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. V prohlížeči se otevře na jinou kartu. 
11. Přejděte [portál Azure – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) postup pro registraci aplikace ve službě Active Directory.
12. V části **spravovat**vyberte **registrace aplikací**.
13. Vyberte **registrace nové**. Na **zaregistrovat aplikaci** nastavte hodnoty takto:
    
* Nastavte **název** na smysluplný název. například *portál pro vývojáře*
* Nastavte **podporovaných typů účtu** k **účty v tomto adresáři organizace jenom**. 
* Nastavte **identifikátor URI pro přesměrování** hodnotě jste získali v kroku 9. 
* Zvolte **zaregistrovat**. 

14.  Po registraci aplikace zkopírovat **ID aplikace (klient)** z **přehled** stránky. 
15. Vraťte se do instance služby API Management. V **přidat zprostředkovatele identity** okně Vložit **ID aplikace (klient)** hodnoty do **ID klienta** pole.
16. Přepněte zpět do konfigurace služby Azure AD, vyberte **certifikáty a tajné kódy** pod **spravovat**. Vyberte **nový tajný kód klienta** tlačítko. Zadejte hodnotu do **popis**, vyberte možnosti pro **Expires** a zvolte **přidat**. Zkopírujte hodnotu tajného kódu klienta před opuštěním stránky. Budete je potřebovat v dalším kroku. 
17. V části **spravovat**vyberte **ověřování** a pak vyberte **tokeny typu ID** pod **implicitní udělení**
18. Vraťte se do instance služby API Management, vložte tajný klíč do **tajný kód klienta** pole.

    > [!IMPORTANT]
    > Zkontrolujte prosím, že aktualizace **tajný kód klienta** vypršení platnosti klíče. 
    >  
    >

19. **Přidat zprostředkovatele identity** okno obsahuje také **povolené Tenantů** textového pole. Existuje zadejte domény instancí Azure AD, ke kterým chcete udělit přístup k rozhraním API instance služby API Management. Více domén můžete oddělit vložení znaků newline, mezerami nebo čárkami.

> [!NOTE]
> Můžete zadat více doménách **povolené Tenantů** části. Než každý uživatel může přihlásit z jiné domény než původní doménu, ve kterém byl zaregistrován aplikace, globálním správcem jiné doméně, musí udělit oprávnění pro aplikaci pro přístup k datům adresáře. Udělení oprávnění globálního správce by měl:. Přejděte na `https://<URL of your developer portal>/aadadminconsent` (například https://contoso.portal.azure-api.net/aadadminconsent).
> b. Zadejte název domény, které chtějí poskytnout přístup k tenantovi Azure AD.
> c. Vyberte **odeslat**. 

20.  Po zadání požadované konfigurace, vyberte **přidat**.

Po uložení změn, uživatelů ve službě Azure AD zadané instance můžete přihlásit k portálu pro vývojáře pomocí následujících kroků v [přihlásit k portálu pro vývojáře pomocí účtu služby Azure AD](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Přidat vnější skupinu Azure AD

Když povolíte přístup pro uživatele v instanci služby Azure AD, můžete přidat skupiny Azure AD ve službě API Management. Potom můžete snadněji spravovat přidružení požadované produkty vývojářů ve skupině.

 > [!IMPORTANT]
 > Přidat vnější skupinu Azure AD, je nutné nejprve nakonfigurovat instanci služby Azure AD na **identit** kartu podle postupu v předchozí části. Kromě toho aplikace musí mít udělen přístup k Azure AD Graph API s využitím `Directory.Read.All` oprávnění. 

Přidat externí Azure AD skupiny z **skupiny** kartu vaší instance služby API Management.

1. Vyberte kartu **Skupiny**.
2. Vyberte **skupiny AAD přidat** tlačítko.
   ![Tlačítko "Přidat skupinu AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Vyberte skupinu, kterou chcete přidat.
4. Stisknutím klávesy **vyberte** tlačítko.

Po přidání externí Azure AD seskupit, můžete zkontrolovat a nakonfigurujte její vlastnosti. Vyberte název skupiny z **skupiny** kartu. Tady můžete upravit **název** a **popis** informace o skupině.
 
Uživatelé z nakonfigurované instance služby Azure AD můžete přihlásit k portálu pro vývojáře. Se může zobrazovat a odebírat žádné skupiny, ke kterým mají viditelnost.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Přihlaste se k portálu pro vývojáře pomocí účtu služby Azure AD

Pro přihlášení k portálu pro vývojáře pomocí účtu služby Azure AD, kterou jste nakonfigurovali v předchozích částech:

1. Otevřete nové okno prohlížeče pomocí adresu URL přihlášení z konfigurace aplikace služby Active Directory a vyberte **Azure Active Directory**.

   ![Přihlašovací stránka][api-management-dev-portal-signin]

1. Zadejte přihlašovací údaje z jednoho z uživatelů ve službě Azure AD a vyberte **přihlášení**.

   ![Přihlášení pomocí uživatelského jména a hesla][api-management-aad-signin]

1. Pokud je potřeba žádné další informace o může výzva s registrační formulář. Vyplňte registrační formulář a vyberte **zaregistrovat**.

   !["Zaregistrovat" tlačítko na registrační formulář][api-management-complete-registration]

Uživatel je nyní přihlášení k portálu pro vývojáře pro instanci služby API Management.

![Portál pro vývojáře po dokončení registrace][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
