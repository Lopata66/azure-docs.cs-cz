---
title: Vytvoření účtu na webu Azure Portal – Azure Batch | Dokumenty společnosti Microsoft
description: Naučte se vytvořit účet Azure Batch na portálu Azure, abyste mohli spouštět velké paralelní úlohy v cloudu.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3723631609a04f6d12abcaac1f9d7733bf3caa01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247641"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Vytvoření účtu Batch pomocí webu Azure Portal

Naučte se vytvořit účet Azure Batch na webu [Azure Portal][azure_portal] a zvolit vlastnosti účtu odpovídající výpočetnímu scénáři. Zjistěte, kde najít důležité vlastnosti účtu, jako jsou přístupové klíče a adresy URL účtu.

Informace o scénářích a účtech Batch najdete v [přehledu funkcí](batch-api-basics.md).

## <a name="create-a-batch-account"></a>Vytvoření účtu Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Přihlaste se k [portálu Azure][azure_portal].

1. Vyberte **možnost Vytvořit službu** > **výpočetní** > **dávky prostředků**.

    ![Batch na webu Marketplace][marketplace_portal]

1. Zadejte nastavení **Nový účet Batch**. Viz následující podrobnosti.

    ![Vytvoření účtu Batch][account_portal]

    a. **Předplatné**: Předplatné, ve kterém chcete účet Batch vytvořit. Pokud máte jenom jedno předplatné, bude ve výchozím nastavení vybrané.

    b. **Skupina prostředků**: Vyberte existující skupinu prostředků vašeho nového účtu Batch, popřípadě si vytvořte novou.

    c. **Název účtu:** Název, který zvolíte, musí být jedinečný v oblasti Azure, ve které se účet vytvoří (viz **Umístění** níže). Název účtu smí obsahovat jenom malá písmena a číslice a musí mít 3 až 24 znaků.

    d. **Umístění**: Oblast Azure, ve které chcete účet Batch vytvořit. Jako možnosti se zobrazí jenom oblasti, které podporuje vaše předplatné a skupina prostředků.

    e. **Účet úložiště**: Volitelný účet Úložiště Azure, který přidružíte ke svému účtu Batch. Pro nejlepší výkon se doporučuje účet úložiště v2 pro obecné účely. Všechny možnosti účtu úložiště v aplikaci Batch najdete v tématu [Přehled funkcí Dávka](batch-api-basics.md#azure-storage-account). Na portálu vyberte existující účet úložiště nebo vytvořte nový.

      ![vytvořit účet úložiště][storage_account]

    f. **Režim přidělení fondu**: Na kartě **Upřesnit** nastavení můžete určit režim přidělení fondu jako **dávkovou službu** nebo **odběr uživatele**. Ve většině scénářů přijměte výchozí **službu Batch**.

      ![Režim přidělení dávkového fondu][pool_allocation]

1. Výběrem možnosti **Vytvořit** vytvořte účet.

## <a name="view-batch-account-properties"></a>Zobrazení vlastností účtu Batch

Po vytvoření účtu ho vyberte pro přístup k jeho nastavením a vlastnostem. Všechna nastavení a vlastnosti účtu jsou přístupná pomocí nabídky vlevo.

![Stránka účtu Batch na webu Azure Portal][account_blade]

* **Název účtu Batch, URL a klíče**: K vývoji aplikace pomocí [rozhraní API služby Batch](batch-apis-tools.md#azure-accounts-for-batch-development) potřebujete URL a klíč účtu pro přístup k prostředkům služby Batch. (Batch podporuje také ověřování pomocí Azure Active Directory.)

    Pokud chcete zobrazit informace pro přístup k účtu Batch, vyberte **Klíče**.

    ![Klíče účtu Batch na webu Azure Portal][account_keys]

* Pokud chcete zobrazit název a klíče účtu úložiště přidruženého k účtu Batch, vyberte **Účet úložiště**.

* Pokud chcete zobrazit kvóty prostředků, které platí pro účet Batch, vyberte **Kvóty**. Podrobnosti najdete v článku o [kvótách a omezeních služby Batch](batch-quota-limit.md).

## <a name="additional-configuration-for-user-subscription-mode"></a>Další konfigurace pro režim předplatného uživatele

Pokud zvolíte možnost vytvořit účet Batch v režimu předplatného uživatele, proveďte před vytvořením tohoto účtu následující kroky.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Povolení přístupu k předplatnému pro Azure Batch (jednorázová operace)

Při vytváření prvního účtu Batch v režimu předplatného uživatele musíte zaregistrovat předplatné ve službě Batch. (Pokud jste tento postup již provedli, přejděte k další části.)

1. Přihlaste se k [portálu Azure][azure_portal].

1. Vyberte**Předplatná** **všech služeb** > a vyberte předplatné, které chcete použít pro účet Batch.

1. Na stránce **Předplatné** vyberte **Poskytovatelé prostředků** a vyhledejte **Microsoft.Batch**. Zkontrolujte, že je v předplatném zaregistrovaný poskytovatel prostředků **Microsoft.Batch**. Pokud není zaregistrovaný, vyberte odkaz **Zaregistrovat**.

    ![Registrace poskytovatele Microsoft.Batch][register_provider]

1. Na stránce **Odběr** vyberte**Přiřazení** > rolí **řízení přístupu (IAM)** > **Přidat přiřazení role**.

    ![Řízení přístupu pro předplatné][subscription_access]

1. Na stránce **Přidat přiřazení role** vyberte roli **přispěvatele** a vyhledejte dávkové rozhraní API. Hledejte každý z těchto řetězců, dokud nenajdete rozhraní API:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. Novější tenanti služby Azure AD mohou používat tento název.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** je ID rozhraní API služby Batch.

1. Jakmile najdete rozhraní API služby Batch, vyberte ho a pak vyberte **Uložit**.

    ![Přidání oprávnění pro službu Batch][add_permission]

### <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

V režimu předplatného uživatele je vyžadován trezor klíčů Azure, který patří do stejné skupiny prostředků jako účet Batch, který se má vytvořit. Ověřte, že se skupina prostředků nachází v oblasti, kde je služba Batch [k dispozici](https://azure.microsoft.com/regions/services/) a která podporuje vaše předplatné.

1. Na webu [Azure Portal][azure_portal] vyberte **Nový** > **Zabezpečení** > **Key Vault**.

1. Na stránce **Vytvořit trezor klíčů** zadejte název pro trezor klíčů a vytvořte skupinu prostředků v oblasti, kterou chcete použít pro účet Batch. Pro zbývající nastavení ponechte výchozí hodnoty a pak vyberte **Vytvořit**.

Při vytváření dávkového účtu v režimu uživatelského předplatného použijte skupinu prostředků pro trezor klíčů. Zadejte **uživatelské předplatné** jako režim přidělení fondu, vyberte trezor klíčů a zaškrtněte políčko udělit Azure Batch přístup k trezoru klíčů. 

Pokud dáváte přednost ručnímu udělení přístupu k trezoru klíčů, přejděte do části **Zásady přístupu** v trezoru klíčů a vyberte **Přidat zásady přístupu** a vyhledejte **Microsoft Azure Batch**. Po výběru budete muset nakonfigurovat **tajná oprávnění** pomocí rozevírací nabídky. Azure Batch musí mít minimální oprávnění **Get**, **List**, **Set**a **Delete.**

![Tajná oprávnění pro Azure Batch](./media/batch-account-create-portal/secret-permissions.png)


> [!NOTE]
> Ujistěte se, že **virtuální počítače Azure pro nasazení** a Azure Resource Manager pro nasazení **šablony** zaškrtávací políčka jsou zaškrtnutá v části **Zásady přístupu** pro propojený prostředek **trezoru klíčů.**
> 
> ![Povinné zásady](./media/batch-account-create-portal/key-vault-access-policy.png) přístupu trezoru klíčů To není povinné při vytváření účtu Batch na webu Azure Portal. Tato možnost je vybrána ve výchozím nastavení.



### <a name="configure-subscription-quotas"></a>Konfigurace kvót předplatného

Základní kvóty nejsou nastaveny ve výchozím nastavení na účty batch předplatného uživatele. Kvóty jádra musí být nastaveny ručně, protože standardní kvóty základní dávky se nevztahují na účty v režimu předplatného uživatele.

1. Na [webu Azure Portal][azure_portal]vyberte režim předplatného uživatele Batch účet a zobrazte jeho nastavení a vlastnosti.

1. V levé nabídce vyberte **Kvóty,** chcete-li zobrazit a nakonfigurovat základní kvóty přidružené k účtu Batch.

Další informace o kvótách základního režimu předplatného uživatele naleznete v [kvótách služby Batch a omezeních.](batch-quota-limit.md)

## <a name="other-batch-account-management-options"></a>Další možnosti správy účtu Batch

K vytváření a správě účtů Batch můžete kromě webu Azure Portal používat také následující nástroje:

* [Rutiny PowerShellu pro účty Batch](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Knihovna Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Další kroky

* Další informace o koncepcích a funkcích služby Batch najdete v článku [Přehled funkcí Batch](batch-api-basics.md). Článek popisuje primární prostředky služby Batch, například fondy, výpočetní uzly a úlohy, a nabízí přehled funkcí služby pro velké výpočetní úlohy.
* Seznamte se se základy vývoje aplikací s podporou služby Batch pomocí [klientské knihovny Batch .NET](quick-run-dotnet.md) nebo [Pythonu](quick-run-python.md). Tyto rychlé starty vás provedou ukázkovou aplikací, která používá službu Batch ke spouštění úlohy na několika výpočetních uzlech, a představí vám použití služby Azure Storage k přípravě a načítání souborů úloh.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
