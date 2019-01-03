---
title: Vykreslení podpora manager – Azure Batch
description: Používání Azure pro vykreslování pomocí integrace služby Azure Batch vykreslení manager
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 4eeece4946b4f957d9f864da7c46d77d119863b5
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539918"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Azure Batch pomocí vykreslení správci farmy

Pokud používáte existující místní vykreslení farmy, pak je vysoce pravděpodobné, že správce vykreslení Určuje kapacitu vykreslovací farmy a úlohy vykreslování.

Azure poskytuje integrovanou podporu nebo doplňky pro oblíbené vykreslení správci. Pak můžete přidávat a odebírat virtuální počítače Azure, včetně virtuálních počítačů s licencováním platby pro použití aplikací a virtuálních počítačů s nízkou prioritou.

Podporují se následující správci vykreslování:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Královská vykreslení](http://www.royalrender.de/)
* [Konečný termín Thinkbox](https://deadline.thinkboxsoftware.com/)

## <a name="using-azure-with-pipelinefx-qube"></a>Používání Azure PipelineFX Qube

Skripty a pokyny pro povolení služby Azure Batch fondu virtuálních počítačů, který se má použít jako Qube pracovních procesů v [úložiště GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Používání Royal vykreslování v Azure

Královská vykreslení integrovaná Azure a Azure Batch integrované, umožňuje rozšířit na vykreslovací farmě pomocí virtuálních počítačů Azure. Souhrn, naleznete v tématu [soubory nápovědy](http://www.royalrender.de/help8/index.html?Cloudrendering.html).

Příklad Royal vykreslení zákazníka pomocí integrace s Azure, najdete v článku [příběh a obrázky Jellyfish](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Pomocí Azure s termínem Thinkbox

Skripty a pokyny pro povolení služby Azure Batch fondu virtuálních počítačů, který se má použít jako podřízené servery konečný termín v [úložiště GitHub](https://github.com/Azure/azure-deadline).

## <a name="next-steps"></a>Další postup

Vyzkoušejte si integrace služby Azure Batch pro správce vykreslování pomocí odpovídající modulu plug-in a pokyny, Githubu, kde je to možné.