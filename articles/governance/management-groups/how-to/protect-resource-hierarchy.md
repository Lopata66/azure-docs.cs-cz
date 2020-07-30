---
title: Jak chránit hierarchii prostředků – zásady správného řízení Azure
description: Naučte se chránit svoji hierarchii prostředků pomocí nastavení hierarchie, která zahrnují nastavení výchozí skupiny pro správu.
ms.date: 05/21/2020
ms.topic: conceptual
ms.openlocfilehash: cdaad59d136e89c595a6a42a9760c73523a977c0
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422823"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>Jak chránit hierarchii prostředků

Vaše prostředky, skupiny prostředků, předplatná, skupiny pro správu a tenant společně tvoří hierarchii prostředků. Nastavení v kořenové skupině pro správu, jako jsou například vlastní role Azure nebo přiřazení zásad Azure Policy, můžou mít vliv na každý prostředek v hierarchii prostředků. Je důležité chránit hierarchii prostředků před změnami, které by mohly mít negativní vliv na všechny prostředky.

Skupiny pro správu teď mají nastavení hierarchie, které umožňuje správcům tenanta řídit toto chování. Tento článek obsahuje všechna dostupná nastavení hierarchie a jejich nastavení.

## <a name="rbac-permissions-for-hierarchy-settings"></a>Oprávnění RBAC pro nastavení hierarchie

Konfigurace nastavení hierarchie vyžaduje následující dvě operace RBAC v kořenové skupině pro správu:

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

Tyto operace umožňují uživateli číst a aktualizovat nastavení hierarchie. Operace neposkytují žádný jiný přístup k hierarchii skupiny pro správu ani k prostředkům v hierarchii. Obě tyto operace jsou k dispozici ve **Správci nastavení hierarchie**integrované role Azure.

## <a name="setting---default-management-group"></a>Nastavení – výchozí skupina pro správu

Ve výchozím nastavení je nové předplatné přidané v rámci tenanta přidáno jako člen skupiny pro správu root. Pokud jsou přiřazování zásad, řízení přístupu na základě role (RBAC) a další konstruktory zásad správného řízení přiřazeny ke skupině root management, tyto nové odběry se okamžitě projeví. Z tohoto důvodu mnoho organizací tyto konstrukce nepoužije v kořenové skupině pro správu, a to i v případě, že je to požadované místo pro jejich přiřazení. V ostatních případech je pro nové odběry žádoucí přísnější sada ovládacích prvků, ale neměla by být přiřazena ke všem předplatným. Toto nastavení podporuje jak případy použití.

Díky tomu, že je možné definovat výchozí skupinu pro správu pro nové odběry, můžete použít konstrukty zásad správného řízení v rámci organizace v kořenové skupině pro správu a použít samostatnou skupinu pro správu s přiřazením zásad nebo přiřazení rolí Azure, které jsou víc vhodné k novému předplatnému.

Pro konfiguraci tohoto nastavení se zavolá [Nastavení hierarchie](/rest/api/resources/hierarchysettings) REST API koncový bod. K tomu použijte následující REST API identifikátor URI a formát textu. Nahraďte `{rootMgID}` ID vaší kořenové skupiny pro správu a `{defaultGroupID}` ID skupiny pro správu, která se má stát výchozí skupinou pro správu:

- Identifikátor URI v REST API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Text požadavku

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

Chcete-li nastavit výchozí skupinu pro správu zpět na kořenovou skupinu pro správu, použijte stejný koncový bod a nastavte **defaultManagementGroup** na hodnotu `/providers/Microsoft.Management/managementGroups/{rootMgID}` .

## <a name="setting---require-authorization"></a>Nastavení-vyžadovat autorizaci

Každý uživatel ve výchozím nastavení může vytvořit nové skupiny pro správu v rámci tenanta. Správci tenanta si můžou chtít poskytnout tato oprávnění jenom konkrétním uživatelům, aby zachovali konzistenci a shodu v hierarchii skupin pro správu. Pokud je tato možnost povolena, uživatel vyžaduje `Microsoft.Management/managementGroups/write` operaci pro kořenovou skupinu pro správu pro vytvoření nových podřízených skupin pro správu.

Pro konfiguraci tohoto nastavení se zavolá [Nastavení hierarchie](/rest/api/resources/hierarchysettings) REST API koncový bod. K tomu použijte následující REST API identifikátor URI a formát textu. Tato hodnota je _logická_hodnota, a proto pro ni zadejte hodnotu **true** nebo **false** . Hodnota **true** povolí tuto metodu ochrany vaší hierarchie skupiny pro správu:

- Identifikátor URI v REST API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Text požadavku

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

Chcete-li nastavení vrátit zpět, použijte stejný koncový bod a nastavte **requireAuthorizationForGroupCreation** na hodnotu **false**.

## <a name="next-steps"></a>Další kroky

Další informace o řešeních pro správu najdete v následujících tématech:

- [Vytváření skupin pro správu pro organizaci prostředků Azure](../create.md)
- [Jak měnit, odstraňovat nebo spravovat skupiny pro správu](../manage.md)
