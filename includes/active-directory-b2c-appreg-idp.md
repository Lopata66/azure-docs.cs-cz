---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 890b19cd8d6f4bc836f6f541a830c1a5feb8458f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028497"
---
K registraci aplikace ve vašem tenantovi Azure AD B2C můžete využít nové jednotné prostředí pro **Registrace aplikací** nebo naše starší verze  **aplikací (zastaralé)** . [Další informace o novém prostředí](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registrace aplikací](#tab/app-reg-ga/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *testapp1*.
1. Vyberte **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity**.
1. V části **identifikátor URI pro přesměrování** vyberte **Web** a potom zadejte `https://jwt.ms` do textového pole Adresa URL.
1. V části **oprávnění** zaškrtněte políčko *udělit souhlas správcům oprávnění OpenID a offline_access* .
1. Vyberte **Zaregistrovat**.

Po dokončení registrace aplikace povolte tok implicitního udělení:

1. V části **Spravovat** vyberte **ověřování**.
1. Vyberte **vyzkoušet nové prostředí** (Pokud je zobrazeno).
1. V části **implicitní udělení** vyberte zaškrtávací políčka **přístupové tokeny** i **tokeny ID** .
1. Vyberte **Uložit**.

#### <a name="applications-legacy"></a>[Aplikace (starší verze)](#tab/applications-legacy/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikace** a pak vyberte **Přidat**.
1. Zadejte název aplikace. Například *testapp1*.
1. Pro **webovou aplikaci nebo webové rozhraní API** vyberte **Ano**.
1. Jako **adresu URL odpovědi** zadejte `https://jwt.ms`
1. Vyberte **Vytvořit**.