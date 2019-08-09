---
title: Shromažďovat data o protokolech a metrikách pomocí Azure Storage Analytics | Microsoft Docs
description: Analýza úložiště vám umožňuje sledovat data metrik pro všechny služby úložiště a shromažďovat protokoly pro objekty blob, front a Table Storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 04206a45a3322a1b0d28e3dfeed65225773e9038
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68844856"
---
# <a name="storage-analytics"></a>Storage Analytics

Analýza Azure Storage provádí protokolování a poskytuje data metriky pro účet úložiště. Tato data můžete použít k trasování požadavků, analýze trendů použití a diagnostice problémů s účtem úložiště.

Chcete-li použít Analýza úložiště, je nutné ji povolit individuálně pro každou službu, kterou chcete monitorovat. Můžete ji povolit z [Azure Portal](https://portal.azure.com). Podrobnosti najdete v tématu [monitorování účtu úložiště v Azure Portal](storage-monitor-storage-account.md). Analýza úložiště můžete také povolit programově prostřednictvím REST API nebo klientské knihovny. Použijte [vlastnost nastavit službu BLOB Service](/rest/api/storageservices/set-blob-service-properties), [nastavte vlastnosti služby fronty](/rest/api/storageservices/set-queue-service-properties), [nastavte vlastnosti služby Table](/rest/api/storageservices/set-table-service-properties)a [nastavte operace vlastností souborové služby](/rest/api/storageservices/Get-File-Service-Properties) , aby bylo možné analýza úložiště pro každou službu.

Agregovaná data se ukládají do známého objektu BLOB (pro protokolování) a v známých tabulkách (pro metriky), ke kterým může být přistupovaná pomocí Blob service a Table service rozhraní API.

U množství uložených dat, která jsou nezávislá na celkovém limitu pro váš účet úložiště, Analýza úložiště má limit 20 TB. Další informace o limitech účtu úložiště najdete v tématu [Azure Storage škálovatelnost a výkonnostní cíle](storage-scalability-targets.md).

Podrobný návod k používání Analýza úložiště a dalších nástrojů k identifikaci, diagnostice a odstraňování potíží souvisejících s Azure Storage najdete v tématu [monitorování, diagnostika a řešení potíží s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="billing-for-storage-analytics"></a>Fakturace za Analýza úložiště

Všechna data metriky se zapisují službami účtu úložiště. Výsledkem je, že každá operace zápisu prováděná Analýza úložiště je fakturovatelná. Kromě toho je fakturovatelná i objem úložiště využívaného daty metrik.

Následující akce prováděné Analýza úložiště jsou Fakturovatelné:

* Požadavky na vytvoření objektů BLOB pro protokolování
* Požadavky na vytvoření entit tabulky pro metriky.

Pokud jste nakonfigurovali zásady uchovávání dat, neúčtují se vám žádné transakce odstranění, když Analýza úložiště odstraní stará data protokolování a metrik. Transakce odstranit z klienta se ale Fakturovatelné. Další informace o zásadách uchovávání informací najdete v tématu [nastavení analýza úložiště zásady uchovávání dat](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Princip fakturovatelných požadavků

Každý požadavek na službu úložiště účtu je buď fakturovatelný, nebo nefakturovatelná. Analýza úložiště protokolovat jednotlivé požadavky na službu, včetně stavové zprávy, která indikuje, jak byl požadavek zpracován. Podobně Analýza úložiště ukládá metriky pro službu i operace rozhraní API této služby, včetně procentuálních hodnot a počtu určitých stavových zpráv. Tyto funkce společně umožňují analyzovat Fakturovatelné žádosti, dělat vylepšení aplikace a diagnostikovat problémy s požadavky na vaše služby. Další informace o fakturaci najdete v tématu [principy Azure Storage fakturace – šířka pásma, transakce a kapacita](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Při prohlížení dat Analýza úložiště můžete pomocí tabulek v tématu [Analýza úložiště protokolované operace a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) určit, které požadavky budou Fakturovatelné. Pak můžete porovnat protokoly a data metrik ve stavových zprávách a zjistit, jestli jste se vám vyrovnali za konkrétní požadavek. K prozkoumání dostupnosti pro službu úložiště nebo jednotlivé operace rozhraní API můžete použít také tabulky v předchozím tématu.

## <a name="next-steps"></a>Další postup
* [Monitorování účtu úložiště v Azure Portal](storage-monitor-storage-account.md)
* [Analýza úložiště metriky](storage-analytics-metrics.md)
* [Protokolování Analýza úložiště](storage-analytics-logging.md)
