---
title: Seznam všech úloh Azure Import/Export | MicrosoftDocs
description: Zjistěte, jak zobrazit seznam všech úloh služby Azure Import/Export v rámci předplatného.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 0ae9e7fa76c8ecbb724cf0f494e648df989dff30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478687"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Vytváření výčtu úloh ve službě Azure Import/Export
Chcete-li vytvořit výčet všech úloh v rámci předplatného, zavolejte [vypisovat úlohy](/rest/api/storageimportexport/jobs) operace. `List Jobs` Vrátí seznam úloh, jakož i následující atributy:

-   Typ úlohy (Import nebo Export)

-   Aktuální stav úlohy

-   Úloha přidružený k tomuto účtu úložiště

## <a name="next-steps"></a>Další postup

* [Pomocí rozhraní REST API služby Import/Export](storage-import-export-using-the-rest-api.md)
