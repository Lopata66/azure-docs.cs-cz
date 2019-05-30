---
title: Zlepšení dostupnosti vaší aplikace s využitím Azure Advisoru | Dokumentace Microsoftu
description: Použití Azure Advisoru ke zlepšení vysoké dostupnosti všech vašich nasazení Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: bdba3f135f852312af1692f77643095d865f1d06
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254673"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Zlepšení dostupnosti vaší aplikace s využitím Azure Advisoru

Azure Advisor pomáhá zajistit a zlepšování kontinuity důležité podnikové aplikace. Doporučení k vysoké dostupnosti v Advisoru z tak můžete získat **vysoké dostupnosti** karty řídicí panel služby Advisor.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Zajištění odolnosti proti chybám virtuálních počítačů

Pokud chcete zajistit redundanci pro vaši aplikaci, doporučujeme seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti. Advisor identifikuje virtuální počítače, které nejsou součástí skupiny dostupnosti a doporučuje jejich přesunutí do skupiny dostupnosti. Tato konfigurace zajistí, že během buď plánované i neplánované údržby, alespoň jeden virtuální počítač je k dispozici a splňuje SLA pro Azure virtual Machines. Můžete vytvořit skupinu dostupnosti pro virtuální počítač nebo virtuální počítač přidat do stávající sady dostupnosti.

> [!NOTE]
> Pokud budete chtít vytvořit skupinu dostupnosti, je nutné přidat alespoň jeden další virtuální počítač do něj. Doporučujeme, abyste během výpadku je k dispozici, které sami seskupíte dvě nebo více virtuálních počítačů ve skupině dostupnosti nastavena zajistit, že aspoň jeden počítač.

## <a name="ensure-availability-set-fault-tolerance"></a>Ujistěte se, že skupina dostupnosti, odolnosti proti chybám

Pokud chcete zajistit redundanci pro vaši aplikaci, doporučujeme seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti. Advisor určí skupiny dostupnosti, které obsahují jeden virtuální počítač a doporučuje přidání jednoho nebo více virtuálních počítačů k němu. Tato konfigurace zajistí, že během buď plánované i neplánované údržby, alespoň jeden virtuální počítač je k dispozici a splňuje SLA pro Azure virtual Machines. Můžete k vytvoření virtuálního počítače nebo přidat existující virtuální počítač do skupiny dostupnosti.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Chcete-li zvýšit spolehlivost dat použít Managed Disks

Virtuální počítače, které jsou ve skupině dostupnosti s disky, které sdílejí účty úložiště nebo jednotky škálování úložiště nejsou během výpadků odolné vůči selhání jednotky škálování jednoho úložiště. Advisor vyhledá tyto skupiny dostupnosti a doporučí migrace na Azure Managed Disks. Tím se zajistí, že disky různých virtuálních počítačů ve skupině dostupnosti jsou dostatečně izolované, aby se zabránilo jediný bod selhání. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Zajištění odolnosti proti chybám application gateway

Toto doporučení zajistí provozní kontinuitu klíčových aplikací, které využívají služby application Gateway. Advisor identifikuje instance brány aplikací, které nejsou konfigurovány pro odolnost proti chybám a navrhne nápravné akce, které můžete provést. Advisor určuje střední a velké aplikace s jedinou instancí brány a doporučí přidávání nejméně jedna další instance. Také určuje instance jednoho nebo více malých aplikačních bran a doporučuje migrace na střední nebo velké skladové položky. Advisor doporučuje tyto akce, které vaše aplikace instance brány jsou nakonfigurujte tak, aby splňovat požadavky na aktuální smlouvy SLA pro tyto prostředky.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Ochrana před náhodným odstraněním dat virtuálního počítače

Nastavení zálohování virtuálních počítačů zajistí dostupnost důležitých podnikových dat a poskytuje ochranu proti náhodnému odstranění nebo poškození. Advisor identifikuje virtuální počítače, kde není povolené zálohování, a doporučí povolení zálohování. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Zajistěte, že abyste měli přístup k odborníkům cloudu Azure, kdykoli ji potřebujete

Při spuštění nejzásadnější úlohy, je důležité mít přístup k technické podpoře v případě potřeby. Advisor identifikuje potenciální nejzásadnější odběry, které nemají v jejich plán podpory součástí je technická podpora a doporučuje upgrade na možnost, která obsahuje technickou podporu.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Vytvoření služby Azure Service Health výstrahy, která vás upozorní, kdy vás ovlivňují problémy Azure

Doporučujeme nastavit upozornění služby Azure Service Health, která vás upozorní, kdy vás ovlivňují problémy se službami Azure. [Azure Service Health](https://azure.microsoft.com/features/service-health/) je bezplatná služba, která poskytuje individuální pokyny a podporu, když jsou ovlivněny problém se službou Azure. Advisor určí odběry, které nemají v nakonfigurovaná upozornění a doporučuje vytvoření nového.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Konfigurace koncových bodů Traffic Manageru pro odolnost proti chybám

Profily Traffic Manageru s více než jeden koncový bod prostředí vyšší dostupnost, pokud se nezdaří libovolný daný koncový bod. Umístění koncových bodů v různých oblastech dále zvyšuje spolehlivost služby. Advisor identifikuje profily Traffic Manageru níž se nachází pouze jeden koncový bod a doporučuje přidávání aspoň jeden další koncový bod v jiné oblasti.

Pokud všechny koncové body v profilu služby Traffic Manager, který je nakonfigurovaný pro směrování blízkých výrazů jsou ve stejné oblasti, uživatelé z jiných oblastí zaznamenat zpoždění připojení. Přidání nebo přesunutí koncový bod do jiné oblasti bude celkový výkon a zajištění lepší dostupnosti, pokud selžou i všechny koncové body v jedné oblasti. Advisor identifikuje profily Traffic Manageru, které jsou nakonfigurované pro bezkontaktní směrování, kde jsou všechny koncové body ve stejné oblasti. Doporučuje, přidání nebo přesunutí koncový bod do jiné oblasti Azure.

Pokud se profil služby Traffic Manager je nakonfigurovaný pro geografické směrování, provoz se směruje do koncových bodů podle definovaných oblastí. Pokud se nezdaří oblasti, neexistuje žádné předdefinované převzetí služeb při selhání. Máte koncový bod, ve které oblastní seskupení je konfigurována pro "Všechny (celý svět)" vyhnout provoz probíhá vyřazování a vylepšit dostupnost služeb. Identifikuje profily Traffic Manageru, které jsou nakonfigurované pro geografické směrování služby Advisor tam, kde neexistuje žádný koncový bod nakonfigurovaný tak, aby mít oblastní seskupení jako "Všechny (celý svět)". Doporučuje změny v konfiguraci koncového bodu "všechny (celý svět).

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Použití obnovitelného odstranění na vašem účtu úložiště Azure k uložení a obnovení dat po náhodnému přepsání nebo odstranění

Povolit [obnovitelné odstranění](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) na vašem účtu úložiště tak, aby odstranit objekty BLOB přechod do stavu obnovitelného odstranění namísto trvale odstraní. Pokud data se přepíší, obnovitelně odstraněného snímku se vygeneruje pro uložení stavu přepsaná data. Použití obnovitelného odstranění umožňuje obnovení v případě, že jsou před náhodným odstraněním nebo přepíše. Advisor určí účtů služby Azure Storage, které nemají povolené obnovitelné odstranění a naznačuje, že ho povolíte.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Nakonfigurovat bránu VPN typu aktivní aktivní pro odolnost připojení

V konfiguraci aktivní aktivní navážou obě instance brány sítě VPN tunely S2S VPN na vaše místní zařízení VPN. Pokud k události plánované údržbě nebo neplánované události dojde k jedné, provoz se měl přepnout na druhý aktivní tunel IPsec automaticky. Azure Advisor vyhledá bran VPN, které nejsou nakonfigurované jako aktivní aktivní a navrhnout, můžete nakonfigurovat pro zajištění vysoké dostupnosti.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Brány VPN produkční použít ke spuštění vaší úlohy v produkčním prostředí

Azure Advisor zkontroluje pro všechny brány VPN, které jsou základní SKU a doporučujeme používat místo produkčního SKU. Základní SKU je určená pro účely vývoje a testování. Produkční skladové položky nabízejí větší počet tunelových propojení, podporu protokolu BGP, možnosti konfigurace aktivní – aktivní, vlastní zásady Ipsec/IKE a vyšší stabilitu a dostupnost.

## <a name="repair-invalid-log-alert-rules"></a>Oprava pravidla upozornění protokolu je neplatná

Azure Advisor zjistí pravidel upozornění, které mají neplatné dotazy podle jejich stavu bodu. Pravidla upozornění protokolů se vytvoří ve službě Azure Monitor a slouží ke spouštění analytických dotazů v pravidelných intervalech. Výsledky dotazu určit, jestli je potřeba aktivovat výstrahu. Analytické dotazy může být neplatný přesčas z důvodu změn v odkazované prostředky, tabulek nebo příkazy. Advisor vám doporučí, opravte dotaz v pravidle výstrahy zabránit v získání automaticky zakázáno a zajištění monitorování rozsahu vašich prostředků v Azure. [Další informace o řešení potíží s pravidla upozornění](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Konfigurace režimu konzistentní indexování do kolekce Cosmos DB

Kontejnery služby Azure Cosmos DB nakonfigurována s režimem Opožděné indexování může mít vliv na aktuálnosti výsledky dotazu. Poradce zjistí, že kontejnery nakonfigurována tímto způsobem a doporučujeme přepnutí do režimu konzistentní vzhledem k aplikacím. [Další informace o indexování zásady ve službě Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Nakonfigurujte své kontejnery služby Azure Cosmos DB s klíčem oddílu

Azure Advisor bude identifikovat bez oddílů kolekce Azure Cosmos DB, které se blíží svou kvótu zřízeného úložiště. Vám doporučí, migrace těchto kolekcí do nové kolekce s definicí klíče oddílu, tak, aby se může automaticky škálovat podle služby. [Další informace o výběru klíče oddílu](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Upgrade sady Azure Cosmos DB .NET SDK na nejnovější verzi z Nuget

Azure Advisor bude identifikovat účtům Azure Cosmos DB, které používají starší verze sady .NET SDK a doporučujeme upgradovat na nejnovější verzi z Nugetu pro nejnovější opravy, vylepšení výkonu a novými funkcemi. [Další informace o Cosmos DB .NET SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Upgrade Azure Cosmos DB Java SDK na nejnovější verzi z Mavenu

Azure Advisor bude identifikovat účtům Azure Cosmos DB, které používají starší verze sady Java SDK a doporučujeme upgradovat na nejnovější verzi z Mavenu pro nejnovější opravy, vylepšení výkonu a novými funkcemi. [Další informace o Cosmos DB Java SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Konektor Spark Azure Cosmos DB upgradovat na nejnovější verzi z Mavenu

Azure Advisor bude identifikovat účtům Azure Cosmos DB, které používají staré verze konektoru Cosmos DB Spark a doporučujeme upgradovat na nejnovější verzi z Mavenu pro nejnovější opravy, vylepšení výkonu a novými funkcemi. [Další informace o konektoru Cosmos DB Spark](https://aka.ms/cosmosdb/spark-connector)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Jak získat přístup k doporučení k vysoké dostupnosti v Advisoru

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na řídicím panelu služby Advisor, klikněte na tlačítko **vysoké dostupnosti** kartu.

## <a name="next-steps"></a>Další postup

Další informace o doporučení Advisoru naleznete v tématu:
* [Úvod do Azure Advisoru](advisor-overview.md)
* [Začínáme se službou Advisor](advisor-get-started.md)
* [Náklady na doporučení advisoru](advisor-cost-recommendations.md)
* [Poradce doporučení k výkonu](advisor-performance-recommendations.md)
* [Bezpečnostní doporučení advisoru](advisor-security-recommendations.md)

