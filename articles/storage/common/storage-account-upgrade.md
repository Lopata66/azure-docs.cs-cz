---
title: Upgrade na účet úložiště pro obecné účely v2 – Azure Storage | Dokumentace Microsoftu
description: Upgradovat na účty úložiště pro obecné účely v2.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/28/2019
ms.author: tamram
ms.openlocfilehash: df9bc1680f20fe6264da0109cd52db1072fd9fc5
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311131"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Upgradovat na účet úložiště pro obecné účely verze 2

Účty úložiště pro obecné účely v2 podporuje nejnovější funkce služby Azure Storage a zapracovali všechny funkce pro obecné účely v1 a účty Blob storage. Účty pro obecné účely v2 se doporučuje pro většinu scénářů úložiště. Účty pro obecné účely v2 doručování nejnižší podle sazby za gigabajt kapacity ceny pro Azure Storage, jakož i průmysl konkurenceschopných cen za transakce.

Upgrade na účet úložiště pro obecné účely verze 2 z pro obecné účely v1 a účty úložiště Blob je jednoduché. Můžete upgradovat pomocí webu Azure portal, Powershellu nebo rozhraní příkazového řádku Azure.

> [!IMPORTANT]
> Upgraduje se účet úložiště v1 pro obecné účely verze 2 je trvalá a není možné vrátit zpět.

## <a name="upgrade-using-the-azure-portal"></a>Upgrade s využitím webu Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte na svůj účet úložiště.
3. V **nastavení** klikněte na tlačítko **konfigurace**.
4. V části **Druh účtu** klikněte na **Upgradovat**.
5. V části **Potvrdit upgrade** zadejte název svého účtu.
6. Klikněte na tlačítko **upgradovat** v dolní části okna.

## <a name="upgrade-with-powershell"></a>Upgrade pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud chcete upgradovat účet pro obecné účely v1 na účet pro obecné účely verze 2 pomocí Powershellu, nejprve aktualizujte PowerShell, které chcete používat nejnovější verzi **Az.Storage** modulu. Informace o instalaci PowerShellu najdete v tématu [Instalace a konfigurace Azure PowerShellu](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Pak zavolejte následující příkaz pro upgrade účet, kde nahradíte název vaší skupiny prostředků a účet úložiště:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>Upgrade pomocí Azure CLI

Pokud chcete upgradovat účet pro obecné účely v1 na účet pro obecné účely verze 2 pomocí Azure CLI, nejprve nainstalujte nejnovější verzi Azure CLI. Informace o instalaci rozhraní příkazového řádku najdete v tématu [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Pak zavolejte následující příkaz pro upgrade účet, kde nahradíte název vaší skupiny prostředků a účet úložiště:

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
```

## <a name="specify-an-access-tier-for-blob-data"></a>Zadat vrstvu přístupu k datům objektu blob

Účty pro obecné účely v2 podporují všechny služby Azure storage a datové objekty, ale jsou k dispozici pouze pro objekty BLOB bloku v úložišti objektů Blob úrovně přístupu. Při upgradu na účet úložiště pro obecné účely v2, můžete určit úroveň přístupu pro data objektů blob.

Vrstva přístupu umožňují zvolit cenově nejvýhodnější úložiště založené na vaše postupy očekávané využití. Objekty BLOB bloku mohou být uloženy ve vrstvě Hot, Cool nebo Archive. Další informace o úrovních přístupu najdete v části [úložiště objektů Blob v Azure: Horké, studené a archivní úroveň úložiště](../blobs/storage-blob-storage-tiers.md).

Ve výchozím nastavení v horká vrstva přístupu je vytvořen nový účet úložiště a účet úložiště pro obecné účely v1 je upgradovat na horká vrstva přístupu. Pokud zkoumáte jaké úroveň přístupu pro vaše data po upgradu, zvažte možnost váš scénář. Existují dva běžné uživatelské scénáře pro migraci na účet pro obecné účely verze 2:

* Máte stávající účet úložiště pro obecné účely v1 a chcete vyhodnotit upgrade na účet úložiště pro obecné účely v2, se na úrovni přístupu úložiště pro data objektů blob.
* Rozhodli jste se použít účet úložiště pro obecné účely verze 2 nebo již máte a chcete vyhodnotit, jestli byste měli použít pro data objektů blob v horké nebo studené úrovni přístupu úložiště.

V obou případech je hlavní prioritou odhad nákladů na ukládání, přístup k a provozování na datech uložených v účtu úložiště pro obecné účely v2 a jejich porovnání s aktuálními náklady.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Upgrade účtu storage v1 na účet pro obecné účely verze 2 je zdarma. Ale změna úrovně přístupu úložiště můžou způsobit změny na faktuře. 

Všechny účty úložiště vycházejí z cenového modelu úložiště objektů blob založeného na úrovních jednotlivých objektů blob. Při použití účtu úložiště je potřeba vzít v úvahu tyto fakturační podmínky:

* **Náklady na úložiště**: Kromě objemu uložených dat náklady na uložení dat liší v závislosti na úrovni přístupu úložiště. Pokud je úroveň chladnější, cena za gigabajt se snižuje.

* **Cena za přístup**: Přístup k datům za úroveň chladnější. Pro data ve studené a archivní úrovni přístupu úložiště bude se vám účtovat poplatek za GB dat přístup pro čtení.

* **Cena za transakce**: Se účtuje poplatek za transakce pro všechny úrovně, které zvýší úroveň chladnější.

* **Cena za přenosy dat geografické replikace**: Tento poplatek se vztahuje jen na účty s nastavenou geografickou replikací, jako třeba GRS a RA-GRS. Přenos dat geografické replikace je zpoplatněný podle sazby za GB.

* **Cena za přenosy odchozích dat**: Přenosy odchozích dat (dat přenesených směrem z oblasti Azure) jsou zpoplatněné využití šířky pásma na základě sazby za gigabajt konzistentní s účty úložiště pro obecné účely.

* **Změna úrovně přístupu úložiště**: Změna úrovně přístupu účtu úložiště ze studené na horkou je zpoplatněna částkou, která odpovídá přečtení všech dat v aktuálním účtu úložiště. Ale změna úrovně přístupu účtu z horké na studenou je zpoplatněna částkou, která odpovídá zápisu všech dat do studené vrstvy (pouze účty GPv2).

> [!NOTE]
> Další informace o cenovém modelu pro účty úložišť najdete na stránce [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Další informace o poplatcích za odchozí přenosy dat najdete na stránce [Podrobné informace o cenách přenosů dat](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Odhadnout náklady za současného využití

Pokud chcete zjistit přibližnou cenu za ukládání a přístup k datům objektu blob v účtu úložiště pro obecné účely v2 v konkrétní úroveň, vyhodnotit svůj aktuální vzor používání nebo přibližný stanovili. Celkově vzato potřebujete vědět:

* Objekt Blob spotřebu úložiště, v gigabajtech, včetně:
    - Kolik dat se v účtu úložiště ukládá?
    - Jak se mění objem dat měsíčně? Nahrazují nová data neustále stará data?
* Vzor primární přístupový objekt Blob úložiště dat, včetně:
    - Kolik dat se čte a zapisuje do účtu úložiště?
    - Počet operací čtení a zápisu operace, ke kterým došlo u dat v účtu storage?

Při rozhodování o nejlepší úroveň přístupu pro vaše potřeby, může být užitečné určit kapacitu pro data objektů blob a jak tato data používá. To nejlepší lze zobrazením monitorovací metriky pro svůj účet.

### <a name="monitoring-existing-storage-accounts"></a>Monitorování existujících účtů úložiště

K monitorování existujících účtů úložiště a sesbírání dat můžete využít službu Azure Storage Analytics, která provádí protokolování a poskytuje data metriky pro účet úložiště. Analýza úložiště může pro účty úložiště typu GPv1, GPv2 a Blob ukládat metriky, včetně souhrnné statistiky transakcí a dat o kapacitě požadavků na službu úložiště. Tato data se ukládají do známých tabulek na tom samém účtu úložiště.

Další informace najdete na stránkách věnovaných [metrikám Analýzy úložiště](https://msdn.microsoft.com/library/azure/hh343258.aspx) a [tabulkovému schématu metrik Analýzy úložiště](https://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Účty úložiště Blob zpřístupňují koncový bod služby Table service pouze pro účely ukládání a zpřístupnění dat metrik pro tento účet.

Pokud chcete monitorovat využití úložiště pro účet úložiště Blob, je potřeba povolit metriky kapacity.
Když tuto funkci zapnete, data o kapacitě služby Blob service pro daný účet úložiště se budou denně zaznamenávat jako zápisy do tabulky *$MetricsCapacityBlob* v rámci stejného účtu úložiště.

Aby bylo možné pro účet úložiště Blob monitorovat vzory přístupu k datům, je potřeba povolit hodinovou metriku transakcí z rozhraní API. Když povolíte hodinovou metriku transakcí, data o transakcích rozhraní API se budou každou hodinu shromažďovat a zaznamenávat jako zápisy do tabulky *$MetricsHourPrimaryTransactionsBlob* v rámci stejného účtu úložiště. Při použití účtů úložiště RA-GRS zaznamenává tabulka *$MetricsHourSecondaryTransactionsBlob* transakce do sekundárního koncového bodu.

> [!NOTE]
> Pokud máte účet úložiště pro obecné účely, ve kterém jsou uložené objekty blob stránky a disky virtuálních počítačů, případně fronty, soubory, nebo tabulky, vedle dat objektů blob bloku a doplňovacích objektů blob, odhad tímto postupem provést nepůjde. Data o kapacitě nerozlišují objekty blob bloku od ostatních typů a neposkytují data o kapacitě pro ostatní typy dat. Pokud používáte tyto typy, můžete se podívat na množství na nejnovějším vyúčtování.

Pokud chcete dobře odhadnout spotřebu dat a přístup k nim, doporučujeme pro měření dat vybrat takovou dobu uchování, která vystihuje pravidelné používání, a potom údaje extrapolovat. Můžete například měřená data uchovávat po sedm dní, sesbírat jednou za týden a analyzovat je na konci měsíce. Nebo změřte a nasbírejte data za posledních 30 dní a na konci 30denního období je analyzujte.

Podrobnosti o povolení, shromažďování a zobrazování dat metrik najdete v tématu [metrikách Storage analytics](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Uložení, zobrazování a stahování analyzovaných data se účtuje stejně jako běžná uživatelská data.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Odhadnutí nákladů s pomocí naměřených údajů o využití

#### <a name="capacity-costs"></a>Náklady na kapacitu

Poslední položka v tabulce kapacitní metriky *$MetricsCapacityBlob* s klíčem řádku *data* zobrazuje kapacitu úložiště spotřebovanou uživatelskými daty. Poslední položka v tabulce kapacitní metriky *$MetricsCapacityBlob* s klíčem řádku *analytics* zobrazuje kapacitu úložiště spotřebovanou protokoly analýzy.

Celková kapacita spotřebovaná uživatelskými daty a protokoly analýzy (pokud jsou povoleny) se potom dají použít k odhadu nákladů za uložení dat v účtu úložiště. Stejnou metodu je možné použít také pro odhad nákladů na úložiště v účtech úložiště GPv1.

#### <a name="transaction-costs"></a>Cena za transakce

Součet *TotalBillableRequests* všech položek rozhraní API v tabulce metrik transakcí udává celkový počet transakcí daného rozhraní API. *Například* celkový počet transakcí *GetBlob* v daném časovém období se dá vypočítat jako celkový součet fakturovatelných požadavků všech položek s klíčem řádku *user;GetBlob*.

Pokud chcete pro účet Blob Storage odhadnout náklady za transakce, je potřeba rozdělit transakce do tří skupin, protože se cenově liší.

* Transakce zápisu jako *PutBlob*, *PutBlock*, *PutBlockList*, *AppendBlock*, *ListBlobs*, *ListContainers*, *CreateContainer*, *SnapshotBlob* a *CopyBlob*.
* Transakce odstranění jako *DeleteBlob* a *DeleteContainer*.
* Veškeré ostatní transakce.

Pokud chcete odhadnout náklady na transakce pro účet úložiště GPv1, je potřeba započítat všechny transakce bez ohledu na operaci nebo rozhraní API.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Přístup k datům a cena za přenos dat – geografická replikace

Analýza úložiště sice k účtu úložiště nevypíše množství přečtených a zapsaných dat, toto množství lze ale zhruba odhadnout z tabulky metriky transakcí. Součet *TotalBillableRequests* všech položek rozhraní API v tabulce metrik transakcí udává celkové množství příchozích dat k tomuto rozhraní API v bajtech. Podobně součet *TotalEgress* udává celkové množství odchozích dat v bajtech.

Pokud chcete pro účet Blob Storage odhadnout náklady za přístup k datům, je potřeba transakce rozdělit do dvou skupin.

* Množství dat načtených z účtu úložiště lze odhadnout ze součtu *TotalEgress* především u operací *GetBlob* a *CopyBlob*.

* Množství dat zapsaných do účtu úložiště lze odhadnout ze součtu *TotalIngress* především u operací *PutBlob*, *PutBlock*, *CopyBlob* a *AppendBlock*.

Také cena za přenos geograficky replikovaných dat účtů Blob Storage se v případě účtu úložiště typu GRS nebo RA-GRS dá vypočítat pomocí toho, že odhadnete množství zapsaných dat.

> [!NOTE]
> Podrobnější příklad výpočtu ceny za využívání vybrat horkou nebo studenou úroveň přístupu, podívejte se na Kladenou *"co jsou úrovně přístupu Hot a Cool a jak určit, který se má použít?"* na stránce [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Další postup

- [Vytvoření účtu úložiště](storage-quickstart-create-account.md)
- [Správa účtů služby Azure storage](storage-account-manage.md)
