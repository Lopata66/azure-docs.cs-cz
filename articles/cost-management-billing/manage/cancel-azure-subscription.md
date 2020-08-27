---
title: Zrušení předplatného Azure
description: Tento článek popisuje, jak zrušit předplatné Azure, například bezplatné zkušební předplatné.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 16ba70717d04c44e950ce2ed2c47e54859c4be47
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684877"
---
# <a name="cancel-your-azure-subscription"></a>Zrušení předplatného Azure

Pokud už předplatné Azure nepotřebujete, můžete ho zrušit na webu Azure Portal.

I když se to nevyžaduje, Microsoft *doporučuje*, abyste před zrušením vašeho předplatného provedli následující akce:

* Zálohujte svá data. Pokud například uchováváte data v Azure Storage nebo SQL, stáhněte si kopii. Pokud máte virtuální počítač, uložte jeho image místně.
* Vypněte používané služby. Přejděte na [stránku s prostředky na portálu pro správu](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) a **zastavte** všechny běžící virtuální počítače, aplikace nebo jiné služby.
* Zvažte migraci svých dat. Přečtěte si článek [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Odstraňte všechny prostředky a všechny skupiny prostředků.
* Pokud máte v `AssignableScopes` nějaké vlastní role, které odkazují na toto předplatné, měli byste tyto vlastní role aktualizovat, aby předplatné šlo odebrat. Pokud se pokusíte vlastní roli aktualizovat po zrušení předplatného, může se zobrazit chyba. Další informace najdete v článku věnovaném [řešení potíží s vlastními rolemi](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) a v článku zaměřeném na [vlastní role Azure](../../role-based-access-control/custom-roles.md).

Pokud zrušíte placený plán podpory Azure, bude se vám účtovat zbývající část období předplatného. Další informace najdete v článku [Plány podpory Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Zrušení předplatného na webu Azure Portal

1. Vyberte své předplatné na [stránce Předplatná na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Vyberte předplatné, které chcete zrušit.
1. Vyberte **Přehled** a pak vyberte **Zrušit předplatné**.
    ![Snímek obrazovky, který zobrazuje tlačítko Zrušit](./media/cancel-azure-subscription/cancel_ibiza.png)
1. Postupujte podle pokynů a dokončete zrušení.

## <a name="who-can-cancel-a-subscription"></a>Kdo může předplatné zrušit?

Následující tabulka popisuje oprávnění nutná ke zrušení předplatného.

|Typ předplatného     |Kdo může zrušit  |
|---------|---------|
|Předplatná vytvořená při registraci Azure prostřednictvím webu Azure Když si například zaregistrujete [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [účet s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo jste [předplatitelem sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Správce účtu a vlastníci předplatného  |
|[Smlouva Microsoft Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) a [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Vlastník účtu a vlastníci předplatného       |
|[Plán Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) a [Plán Azure pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Vlastníci předplatného      |

## <a name="what-happens-after-i-cancel-my-subscription"></a>Co se stane po zrušení předplatného?

Po zrušení se okamžitě zastaví účtování. Zrušení se ale na portálu může projevit až za 10 minut. Pokud předplatné zrušíte uprostřed fakturačního období, po skončení tohoto období vám pošleme konečnou fakturu k běžnému datu faktury.

Po zrušení jsou vaše služby zakázané. To znamená, že se zruší přidělení vašich virtuálních počítačů, uvolní se dočasná IP adresa a úložiště přejde do režimu jen pro čtení.

Po zrušení vašeho předplatného Microsoft počká 30 až 90 dní pro případ, že k datům budete potřebovat přístup nebo že změníte své rozhodnutí, teprve potom je trvale odstraní. Za uchování těchto dat vám nic neúčtujeme. Další informace najdete v článku [Centrum zabezpečení Microsoftu – jak spravujeme vaše data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="delete-free-trial-subscription"></a>Odstranění bezplatného zkušebního předplatného

Pokud máte bezplatné zkušební předplatné, nemusíte čekat 30 dnů, než se automaticky odstraní. Předplatné můžete odstranit *tři dny* potom, co ho zrušíte. Možnost **odstranění předplatného** je dostupná až po třech dnech od jeho zrušení.

1. Počkejte tři dny ode dne, kdy jste předplatné zrušili.
1. Vyberte předplatné na stránce [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure Portal.
1. Vyberte předplatné, které chcete odstranit.
1. Vyberte **Přehled** a pak vyberte **Odstranit předplatné**.

## <a name="reactivate-subscription"></a>Opětovná aktivace předplatného

Pokud omylem zrušíte své předplatné s průběžnými platbami, můžete [ho znovu aktivovat v Centru účtů](subscription-disabled.md).

Pokud se nejedná o předplatné s průběžnými platbami, požádejte během 90 dnů od zrušení podporu o opětovnou aktivaci svého předplatného.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- V případě potřeby můžete předplatné s průběžnými platbami znovu aktivovat v [Centru účtů](subscription-disabled.md):