---
title: Služby, kvóty a omezení – Azure Batch | Dokumentace Microsoftu
description: Další informace o výchozí služby Azure Batch, kvóty, limity a omezení, a jak si vyžádat kvóta zvýší
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: f8941f84e85f330713cb4b263f2154acb80b520c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57532391"
---
# <a name="batch-service-quotas-and-limits"></a>Kvóty a omezení služby Batch

Jako s ostatními službami Azure, se vztahují omezení určitých prostředků služby Batch. Mnoho z těchto omezení jsou výchozí kvóty uplatňované službou Azure na úrovni účtu nebo předplatného. Tento článek popisuje tyto výchozí hodnoty a jak můžete požádat o kvóta zvýší.

Mějte tyto kvóty při navrhování a škálování nahoru úloh služby Batch. Například pokud jej váš fond nemá přístup do cílového počtu výpočetních uzlů, které jste zadali, může dosáhli jste limitu kvóty jader pro účet Batch.

Můžete spustit několik dávkových úloh služby Batch v jednom účtu Batch najednou, nebo můžete úlohy rozložit mezi více účtů Batch, které jsou v jednom předplatném, ale v různých oblastech Azure.

Pokud máte v plánu pro spouštění úloh v produkčním prostředí ve službě Batch, budete muset zvýšit nejméně jeden z kvót nad výchozí. Pokud chcete kvótu zvýšit, můžete otevřít online [žádost o zákaznickou podporu](#increase-a-quota) bez poplatků.

> [!NOTE]
> Kvóta je kreditního limitu záruku kapacity. Pokud máte velkou kapacitu, kontaktujte prosím podporu Azure.
> 
> 

## <a name="resource-quotas"></a>Kvóty prostředků
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]


### <a name="cores-quotas-in-user-subscription-mode"></a>Kvóty jader v režimu předplatného uživatele

Pokud jste nevytvořili účet Batch s režimem přidělování fondu nastavit na **předplatné uživatele**, jinak se uplatňují kvóty. V tomto režimu se virtuální počítače dávky a dalším prostředkům vytvářejí přímo ve vašem předplatném při vytvoření fondu. Kvóty jader Azure Batch se nevztahují na účet vytvořený v tomto režimu. Místo toho kvóty ve vašem předplatném pro místní výpočetních jader a další prostředky se použijí. Další informace o těchto kvót v [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Omezení velikosti fondu

| **Prostředek** | **Maximální omezení** |
| --- | --- |
| **Výpočetní uzly v [fondu komunikace mezi uzly povolena](batch-mpi.md)**  ||
| Režim přidělování fondů služba batch | 100 |
| Režim přidělování fondů předplatné služby batch | 80 |
| **Výpočetní uzly v [fond vytvořen s použitím vlastní image virtuálního počítače](batch-custom-images.md)**<sup>1</sup> ||
| Vyhrazené uzly | 2000 |
| Uzly s nízkou prioritou | 1000 |

<sup>1</sup> pro fondy, které nejsou povolená komunikace v rámci uzlu.

## <a name="other-limits"></a>Další omezení

| **Prostředek** | **Maximální omezení** |
| --- | --- |
| [Souběžné úlohy](batch-parallel-node-tasks.md) na výpočetním uzlu | 4 x počet jader na uzel |
| [Aplikace](batch-application-packages.md) jeden účet Batch | 20 |
| Balíčky aplikací na jednu aplikaci | 40 |
| Úloha maximální doba života | 180 dnů<sup>1</sup> |

<sup>1</sup> maximální doba života úkolu, od jeho přidání do úlohy po dokončení je 180 dnů. Dokončené úkoly se zachovávají po dobu 7 dní; data pro úkoly, které se nedokončí v rámci maximální doby života není přístupný.

## <a name="view-batch-quotas"></a>Zobrazit kvóty služby Batch

Zobrazit vaše kvóty účtu Batch na [webu Azure portal][portal].

1. Vyberte **účty Batch** na portálu vyberte účet Batch, který vás zajímá.
1. Vyberte **kvóty** v nabídce účtu Batch.
1. Zobrazit kvóty aktuálně používané k účtu Batch
   
    ![Kvóty účtu batch][account_quotas]



## <a name="increase-a-quota"></a>Navýšení kvóty

Postupujte podle těchto kroků žádost o kvótu zvýšit pro váš účet Batch nebo předplatnému pomocí [webu Azure portal][portal]. Typ zvýšení kvóty, závisí na režimu přidělování fondů pro váš účet Batch.

### <a name="increase-a-batch-cores-quota"></a>Zvýšit kvótu pro jádra služby Batch 

1. Vyberte **Nápověda a podpora** dlaždici na řídicím panelu portálu nebo otazník (**?**) v pravém horním rohu portálu.
1. Vyberte **nová žádost o podporu** > **Základy**.
1. V **Základy**:
   
    a. **Typ problému** > **kvóty**
   
    b. Vyberte své předplatné.
   
    c. **Typ kvóty** > **služby Batch**
   
    d. **Plán podpory** > **kvóty podpora – zahrnuto**
   
    Klikněte na **Další**.
1. V **problém**:
   
    a. Vyberte **závažnost** podle vaší [dopad na chod firmy][support_sev].
   
    b. V **podrobnosti**, zadejte každá kvóta, kterou chcete změnit název účtu Batch a nový limit.
   
    Klikněte na **Další**.
1. V **kontaktní informace**:
   
    a. Vyberte **upřednostňovaný způsob kontaktování**.
   
    b. Zkontrolujte a zadejte požadované podrobnosti kontaktu.
   
    Kliknutím na **Vytvořit** odešlete žádost o podporu.

Jakmile jste odeslali žádost o podporu, bude vás kontaktovat podporu Azure. Všimněte si, že dokončení žádosti může trvat až 2 pracovních dnů.

## <a name="related-quotas-for-vm-pools"></a>Související kvóty pro fondy virtuálních počítačů

Fondy služby batch v konfiguraci virtuálního počítače nasazené ve službě Azure virtual network automaticky přidělit další prostředky Azure sítě. V následujících zdrojích informací jsou potřeba pro každý uzly 50 fondu ve virtuální síti:

* 1 [skupina zabezpečení sítě](../virtual-network/security-overview.md#network-security-groups)
* 1 [veřejnou IP adresu](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* 1 [nástroj pro vyrovnávání zatížení](../load-balancer/load-balancer-overview.md)

Tyto prostředky se přidělují v předplatném, která obsahuje virtuální síť zadali při vytváření fondu služby Batch. Pro tyto prostředky platí omezení [kvót prostředků](../azure-subscription-service-limits.md) předplatného. Pokud máte v plánu nasazení velkého fondu ve virtuální síti, zkontrolujte předplatného kvóty pro tyto prostředky. V případě potřeby požádat o zvýšení na webu Azure Portal tak, že vyberete **Nápověda a podpora**.


## <a name="related-topics"></a>Související témata
* [Vytvoření účtu Azure Batch pomocí webu Azure portal](batch-account-create-portal.md)
* [Přehled funkcí Azure Batch](batch-api-basics.md)
* [Limity, kvóty a omezení předplatného a služeb Azure](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
