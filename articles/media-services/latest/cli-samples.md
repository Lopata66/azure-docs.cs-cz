---
title: Příklady Azure CLI – Azure Media Services | Dokumentace Microsoftu
description: Příklady Azure CLI pro službu Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 03/11/2019
ms.author: juliako
ms.openlocfilehash: dee7f831562dc1f4b2478d13b204aab1d8455e1e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840626"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Příklady Azure CLI pro službu Azure Media Services

Následující tabulka obsahuje odkazy na příklady Azure CLI pro službu Azure Media Services.

## <a name="examples"></a>Příklady

|  |  |
|---|---|
|**Škálování**||
| [Rezervované jednotky médií škálování](media-reserved-units-cli-how-to.md)|Pro analýzu zvuku a videa analytických úloh, které jsou aktivovány Media Services v3 nebo Video Indexer důrazně doporučujeme pro účet zřídit s 10 použité položky S3. <br/>Tento skript ukazuje, jak pomocí rozhraní příkazového řádku můžete škálovat rezervované jednotky médií (použité položky).|
|**Účet**||
| [Vytvoření účtu Media Services](create-account-cli-how-to.md) | Tento skript vytvoří účet Azure Media Services. |
| [Resetovat přihlašovací údaje k účtu](./scripts/cli-reset-account-credentials.md)|Resetování přihlašovacích údajů k účtu a získá zpět nastavení souboru app.config.|
|**Prostředky**||
| [Vytvoření prostředků](./scripts/cli-create-asset.md)|Vytvoří k Media Services Assetu nahrát obsah do.|
| [Nahrát soubor](./scripts/cli-upload-file-asset.md)|Nahraje místní soubor do kontejneru úložiště.|
| **Transformuje** a **úlohy**||
| [Vytvoření transformace](./scripts/cli-create-transform.md)|Ukazuje postup vytvoření transformace. Transformace popisují jednoduchý pracovní postup úloh pro zpracování videosouborů nebo zvukových souborů a často se označují jako předpisy.<br/> Vždy byste měli zkontrolovat Pokud transformace s požadovaným názvem a "předpisu" již existuje. Pokud ano, znovu použít. |
| [Kódování pomocí vlastní transformace](custom-preset-cli-howto.md) | Ukazuje, jak vytvářet vlastní přednastavení cílit na konkrétní požadavky scénáře nebo zařízení.|
| [Vytváření úloh](./scripts/cli-create-jobs.md)|Odešle úlohu, která jednoduché kódování transformace pomocí adresy URL HTTPs.|
| [Vytvoření EventGrid](./scripts/cli-create-event-grid.md)|Vytvoří odběr Event gridu úroveň pro účet pro změny stavu úlohy.|
| **Doručování**||
| [Publikování assetu](./scripts/cli-publish-asset.md)| Vytvoří Lokátor streamování a získá zpět adresy URL streamování. |
| [Filtr](filters-dynamic-manifest-cli-howto.md)| Konfiguruje filtr pro prostředek videa na vyžádání a ukazuje, jak použít rozhraní příkazového řádku k vytvoření [filtrů účtů](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) a [Asset filtry](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="see-also"></a>Další informace najdete v tématech

- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
- [Rychlé zprovoznění: Stream video soubory – rozhraní příkazového řádku](stream-files-cli-quickstart.md)
