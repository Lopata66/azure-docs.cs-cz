---
title: Vytvoření účtu Azure Remote Renderingu
description: Popisuje kroky pro vytvoření účtu pro vzdálené vykreslování Azure.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 58757dba9a8956d97c19269c2ac913d801f73746
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/26/2020
ms.locfileid: "83844501"
---
# <a name="create-an-azure-remote-rendering-account"></a>Vytvoření účtu Azure Remote Renderingu

Tato kapitola vás provede postupem vytvoření účtu pro službu **Azure Remote rendering** . Platný účet je povinný pro dokončení některého z rychlých startů nebo kurzů.

## <a name="create-an-account"></a>Vytvoření účtu

Pro vytvoření účtu pro službu Azure Remote rendering je potřeba provést tyto kroky:

1. Přejít na [stránku Preview ve smíšeném realitě](https://aka.ms/MixedRealityPrivatePreview)
1. Klikněte na tlačítko vytvořit prostředek.
1. Do vyhledávacího pole ("Hledat na webu Marketplace") zadejte "vzdálené vykreslování" a stiskněte klávesu ENTER.
1. V seznamu výsledků klikněte na dlaždici vzdálené vykreslování.
1. Na další obrazovce klikněte na tlačítko vytvořit. Otevře se formulář, ve kterém se vytvoří nový účet vzdáleného vykreslování:
    1. Nastavte název prostředku na název účtu.
    1. Pokud je to potřeba, aktualizujte předplatné.
    1. Nastavte skupinu prostředků na skupinu prostředků podle vašeho výběru.
1. Po vytvoření účtu přejděte k němu a:
    1. Na kartě *Přehled* si poznamenejte "ID účtu".
    1. Na kartě *nastavení > přístupové klíče* si poznamenejte "primární klíč" – Jedná se o klíč tajného účtu účtu.

### <a name="retrieve-the-account-information"></a>Načtení informací o účtu

Ukázky a kurzy vyžadují, abyste zadali ID účtu a klíč. Například v souboru **ARRConfig. JSON** , který se používá pro ukázkové skripty prostředí PowerShell:

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

Projděte si [seznam dostupných oblastí](../reference/regions.md) pro vyplňování možnosti *region* .

Hodnoty pro **`arrAccountId`** a **`arrAccountKey`** lze nalézt na portálu, jak je popsáno v následujícím postupu:

* Přejít na [Azure Portal](https://www.portal.azure.com)
* Najít **účet vzdáleného vykreslování** – měl by být v seznamu **Poslední prostředky** . Můžete ji také vyhledat na panelu hledání v horní části. V takovém případě se ujistěte, že odběr, který chcete použít, je vybraný ve výchozím filtru předplatného (ikona filtru vedle panelu hledání):

![Filtr předplatných](./media/azure-subscription-filter.png)

Kliknutím na svůj účet přejdete na tuto obrazovku, ve které se hned zobrazuje **ID účtu** :

![ID účtu Azure](./media/azure-account-id.png)

Pro klíč vyberte **přístupové klíče** na panelu vlevo. Na další stránce se zobrazuje primární a sekundární klíč:

![Přístupové klíče Azure](./media/azure-account-primary-key.png)

Hodnota pro **`arrAccountKey`** může být buď primární, nebo sekundární klíč.

## <a name="link-storage-accounts"></a>Propojit účty úložiště

Tento článek vysvětluje, jak propojit účty úložiště s účtem vzdáleného vykreslování. Pokud je propojen účet úložiště, není nutné vygenerovat identifikátor URI SAS pokaždé, když chcete pracovat s daty ve vašem účtu, například při načítání modelu. Místo toho můžete použít názvy účtů úložiště přímo, jak je popsáno v [části načtení modelu](../concepts/models.md#loading-models).

Kroky v tomto odstavci je třeba provést pro každý účet úložiště, který by měl používat tuto alternativní metodu přístupu. Pokud jste ještě nevytvořili účty úložiště, můžete si projít příslušný krok v části [převedení modelu pro rychlý Start vykreslování](../quickstarts/convert-model.md#storage-account-creation).

Nyní se předpokládá, že máte účet úložiště. Přejděte na portál na účet úložiště a přejděte na kartu **Access Control (IAM)** pro tento účet úložiště:

![Úložiště IAM účtu úložiště](./media/azure-storage-account.png)

 Ujistěte se, že máte oprávnění vlastníka pro tento účet úložiště, abyste měli jistotu, že můžete přidat přiřazení rolí. Pokud nemáte přístup, bude možnost **Přidat přiřazení role** zakázaná.

 Musíte přidat tři samostatné role, jak je popsáno v následujících krocích. Pokud nezadáte všechny tři úrovně přístupu, při pokusu o přístup k účtu úložiště dojde k problémům s oprávněním.

 Kliknutím na tlačítko **Přidat** na dlaždici přidat přiřazení role přidejte první roli:

![Úložiště IAM účtu úložiště](./media/azure-add-role-assignment.png)

* První role, která se má přiřadit, je **vlastník** , jak je znázorněno na snímku obrazovky výše.
* V rozevíracím seznamu **přiřadit přístup k** vyberte **účet vzdáleného vykreslování** .
* V posledních rozevíracích seznamech vyberte své předplatné a účet vzdáleného vykreslování.

> [!WARNING]
> V případě, že váš účet vzdáleného vykreslování není uveden, přečtěte si [téma věnované tomuto problému](../resources/troubleshoot.md#cant-link-storage-account-to-arr-account).

Pro odpovídající výběry v rozevírací nabídce **role** znovu přidejte další nové role:
* **Přispěvatel účtů úložiště**
* **Přispěvatel dat objektu BLOB služby Storage**

Ostatní rozevírací seznamy jsou vybrány jako v prvním kroku.

Pokud jste přidali všechny tři role, má účet vzdáleného vykreslování Azure přístup k vašemu účtu úložiště pomocí identit spravované služby přiřazené systémem.

## <a name="next-steps"></a>Další kroky

* [Přihlašovací](authentication.md)
* [Použití rozhraní API Azure front-endu k ověřování](frontend-apis.md)
* [Ukázkové skripty PowerShellu](../samples/powershell-example-scripts.md)
