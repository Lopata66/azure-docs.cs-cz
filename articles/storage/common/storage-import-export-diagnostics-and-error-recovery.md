---
title: Diagnostika a zotavení z chyb pro úlohy Azure Import/Export | Dokumentace Microsoftu
description: Zjistěte, jak povolit podrobné protokolování pro úlohy služby Microsoft Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 306b94fbe23e0ae92dcd59f7a87b7bb58ef7c3b6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478806"
---
# <a name="diagnostics-and-error-recovery-for-azure-importexport-jobs"></a>Diagnostika a zotavení z chyb pro úlohy Azure Import/Export
Pro každou jednotku zpracována služba Azure Import/Export vytvoří protokol chyb do přidruženého účtu úložiště. Můžete také povolit podrobné protokolování tak, že nastavíte `LogLevel` vlastnost `Verbose` při volání [úlohy umístit](/rest/api/storageimportexport/jobs) nebo [aktualizovat vlastnosti úlohy](/rest/api/storageimportexport/jobs) operace.

 Ve výchozím nastavení, protokoly se zapisují do kontejneru s názvem `waimportexport`. Můžete zadat jiný název tak, že nastavíte `DiagnosticsPath` vlastnost při volání `Put Job` nebo `Update Job Properties` operace. Protokoly se ukládají jako objekty BLOB bloku s následujícími zásadami vytváření názvů: `waies/jobname_driveid_timestamp_logtype.xml`.

 Identifikátor URI v protokolech úlohy můžete načíst pomocí volání [Get Job](/rest/api/storageimportexport/jobs) operace. Identifikátor URI pro podrobného protokolování se vrátí v `VerboseLogUri` pro každou jednotku, zatímco identifikátor URI pro protokol chyb je vrácen ve vlastnosti `ErrorLogUri` vlastnost.

Protokolování dat můžete použít k identifikaci následující problémy.

## <a name="drive-errors"></a>Jednotka chyby

Následující položky jsou klasifikovány jako chyby jednotky:

-   Chyby v přístupu k nebo čtení souboru manifestu

-   Nesprávný klíče Bitlockeru

-   Jednotky pro čtení a zápisu chyby

## <a name="blob-errors"></a>Chyby objektů BLOB

Následující položky jsou klasifikovány jako chyby objektů blob:

-   Názvy nebo nesprávnou či konfliktní objekt blob

-   Chybějící soubory

-   Nebyl nalezen objekt BLOB

-   Zkrácený soubory (soubory na disku jsou menší, než je určeno v manifestu)

-   Poškozený soubor obsahu (pro úlohy importu zjistila se neshoda kontrolního součtu MD5)

-   Poškozený objekt blob metadat a vlastností souborů (zjistila se neshoda kontrolního součtu MD5)

-   Nesprávné schéma pro vlastnosti objektu blob a/nebo soubory metadat

Můžou nastat případy, kde některé části úlohu importu nebo exportu úspěšně vyřídit, zatímco stále dokončení celkové úlohy. V takovém případě můžete nahrát nebo stáhnout chybějící části dat přes síť, nebo můžete vytvořit novou úlohu k přenosu dat. Najdete v článku [referenčních informacích k Azure Import/Export nástroj](storage-import-export-tool-how-to-v1.md) se naučíte opravit data přes síť.

## <a name="next-steps"></a>Další postup

* [Pomocí rozhraní REST API služby Import/Export](storage-import-export-using-the-rest-api.md)
