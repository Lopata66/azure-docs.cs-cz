---
title: Úprava kvót a omezení ve službě Azure Data Lake Analytics
description: Naučte se upravovat a zvyšovat kvóty a omezení v účtech Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonwhowell
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c1d7c8cc4b50682a74ac88b2113f0d44ebc55199
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75644711"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Úprava kvót a omezení ve službě Azure Data Lake Analytics

Přečtěte si, jak upravit a zvýšit kvótu a omezení v účtech Azure Data Lake Analytics (ADLA). Znalost těchto omezení vám pomůže pochopit chování úlohy U-SQL. Všechna omezení kvót jsou měkká, takže můžete zvýšit maximální limity tím, že se obrátíte na podporu Azure.

## <a name="azure-subscriptions-limits"></a>Omezení předplatných Azure

**Maximální počet účtů ADLA na předplatné na oblast:** 5

Pokud se pokusíte vytvořit šestý účet ADLA, zobrazí se chyba "dosáhli jste maximálního počtu povolených Data Lake Analytics účtů (5) v oblasti" název předplatného ".

Pokud chcete tento limit přesáhnout, můžete vyzkoušet tyto možnosti:
* Pokud je to vhodné, vyberte jinou oblast.
* kontaktujte podporu Azure [otevřením lístku podpory](#increase-maximum-quota-limits) pro vyžádání zvýšení kvóty.

## <a name="default-adla-account-limits"></a>Výchozí omezení účtu ADLA

**Maximální počet jednotek analýzy (Austrálie) na účet:** 250, výchozí hodnota: 32

Toto je maximální počet jednotek Austrálie, které mohou běžet souběžně ve vašem účtu. Pokud celkový počet provozního prostředí Austrálie napříč všemi úlohami překročí tento limit, novější úlohy se automaticky zařadí do fronty. Příklad:

* Pokud máte pouze jednu úlohu spuštěnou s 32 jednotkou Austrálie, při odeslání druhé úlohy bude čekat ve frontě úloh až do dokončení první úlohy.
* Pokud už máte spuštěné čtyři úlohy a každý z nich používá 8 jednotek Austrálie, odešlete pátý úkol, který potřebuje 8 jednotek analýzy, dokud nebude k dispozici 8 jednotek Austrálie.

    ![Stránka omezení Azure Data Lake Analytics a kvóta](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Maximální počet jednotek analýzy (Austrálie) na úlohu:** 250, výchozí hodnota 32

Toto je maximální počet jednotek Austrálie, ke kterým je možné přiřadit každou jednotlivou úlohu ve vašem účtu. Úlohy, které jsou přiřazené víc, než je toto omezení, se odmítnou, pokud není na odesílateli ovlivněné výpočetními zásadami (limit pro odesílání úloh), který poskytuje větší náklady na úlohu. Horní mez této hodnoty je limit AU pro účet.

**Maximální počet souběžných úloh U-SQL na účet:** 20

Toto je maximální počet úloh, které mohou běžet souběžně ve vašem účtu. Nad touto hodnotou se nové úlohy automaticky zařadí do fronty.

## <a name="adjust-adla-account-limits"></a>Upravit omezení účtu ADLA

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Vyberte existující účet ADLA.
3. Klikněte na **Vlastnosti**.
4. Upravte hodnoty pro **maximální Austrálie**, **maximální počet spuštěných úloh**a omezení pro **odesílání úloh** tak, aby vyhovovaly vašim potřebám.

## <a name="increase-maximum-quota-limits"></a>Zvýšení maximálních limitů kvót

Další informace o omezeních Azure najdete v [dokumentaci k omezením specifickým pro službu Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Otevřete žádost o podporu v Azure Portal.

    ![Stránka Azure Data Lake Analyticsového portálu](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Stránka Azure Data Lake Analyticsového portálu](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Vyberte **kvótu**typu problému.
3. Vyberte své **předplatné** (Ujistěte se, že se nejedná o zkušební předplatné).
4. Vyberte typ kvóty **Data Lake Analytics**.

    ![Stránka Azure Data Lake Analyticsového portálu](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Na stránce problém Vysvětlete požadovaný limit zvýšení s **podrobnostmi** o tom, proč tuto dodatečnou kapacitu potřebujete.

    ![Stránka Azure Data Lake Analyticsového portálu](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Ověřte kontaktní informace a vytvořte žádost o podporu.

Microsoft si vaši žádost posuzuje a snaží se co nejdříve přizpůsobit vašim obchodním potřebám.

## <a name="next-steps"></a>Další kroky

* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Správa Azure Data Lake Analytics pomocí Azure PowerShellu](data-lake-analytics-manage-use-powershell.md)
* [Monitorování úloh Azure Data Lake Analytics a odstraňování potíží pomocí webu Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
