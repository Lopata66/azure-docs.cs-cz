---
title: Nasazení služby synchronizace úložiště
description: Nasaďte Azure File Sync cloudový prostředek, službu synchronizace úložiště. Společný textový blok sdílený v rámci migračních dokumentů.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: bd4af178d6e70d910d4643fc30c29fc8eb85303c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043123"
---
V tomto kroku budete potřebovat přihlašovací údaje předplatného Azure.

Základní prostředek, který se má nakonfigurovat pro Azure File Sync, se nazývá *Služba synchronizace úložiště* . Doporučujeme nasadit jenom jednu pro všechny servery, které synchronizují stejnou sadu souborů v současnosti i v budoucnu. Více služeb synchronizace úložiště můžete vytvořit pouze v případě, že máte různé sady serverů, které nikdy nesmí vyměňovat data. Můžete mít třeba servery, které nikdy nesynchronizují stejnou sdílenou složku Azure. V opačném případě se jedná o jediný postup služby synchronizace úložiště.

Vyberte oblast Azure, ve které se služba synchronizace úložiště blíží vašemu umístění. Všechny ostatní cloudové prostředky musí být nasazené ve stejné oblasti. Pokud chcete zjednodušit správu, vytvořte v předplatném novou skupinu prostředků, která bude mít prostředky pro synchronizaci a úložiště.

Další informace najdete v [části o nasazení služby synchronizace úložiště](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) v článku o nasazení Azure File Sync. Postupujte pouze v této části článku. V pozdějších krocích budou odkazy na další části článku.