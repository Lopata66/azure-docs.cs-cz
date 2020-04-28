---
title: Azure Data Lake Storage Gen1 porovnání s Azure Storage Blob | Microsoft Docs
description: Azure Data Lake Storage Gen1 porovnání s Azure Storage Blob
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 7c958c3ed4d6ddaabd87f053005fcfc1eba8c842
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75438722"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Porovnávání Azure Data Lake Storage Gen1 a Azure Blob Storage

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

Tabulka v tomto článku shrnuje rozdíly mezi Azure Data Lake Storage Gen1 a Azure Blob Storage a dalšími klíčovými aspekty zpracování velkých objemů dat. Azure Blob Storage je univerzální účelově škálovatelné úložiště objektů, které je navržené pro širokou škálu scénářů úložiště. Azure Data Lake Storage Gen1 je úložiště typu Hyper-Scale optimalizované pro úlohy analýzy velkých objemů dat.

|  | Azure Data Lake Storage Gen1 | Azure Blob Storage |
| --- | --- | --- |
| Účel |Optimalizované úložiště pro úlohy analýzy velkých objemů dat |Úložiště objektů pro obecné účely pro širokou škálu scénářů úložiště, včetně analýz velkých objemů dat |
| Případy použití |Batch, interaktivní, Stream Analytics a data strojového učení, jako jsou soubory protokolů, data IoT, klikněte na streamy, velké datové sady. |Jakýkoli typ textu nebo binárních dat, jako je back-end aplikace, zálohovaná data, mediální úložiště pro streamování a data pro obecné účely. Kromě toho plná podpora pro úlohy analýzy; Batch, interaktivní, Stream Analytics a data strojového učení, jako jsou soubory protokolů, data IoT, klikněte na streamy, velké datové sady. |
| Klíčové koncepty |Data Lake Storage Gen1 účet obsahuje složky, které zase obsahují data uložená jako soubory. |Účet úložiště obsahuje kontejnery, které zase obsahují data ve formě objektů BLOB. |
| Struktura |Hierarchický systém souborů |Úložiště objektů s plochým oborem názvů |
| Rozhraní API |REST API přes HTTPS |REST API přes HTTP/HTTPS |
| Rozhraní API na straně serveru |[REST API kompatibilní WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[REST API Blob Storage Azure](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Klient systému souborů Hadoop |Ano |Ano |
| Operace s daty – ověřování |Na základě [Azure Active Directory identit](../active-directory/develop/authentication-scenarios.md) |Na základě sdílených tajných klíčů – klíčů pro [přístup k účtu](../storage/common/storage-account-keys-manage.md) a [klíčů sdíleného přístupového podpisu](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Datové operace – protokol ověřování |OAuth 2,0. Volání musí obsahovat platný token JWT (JSON Web Token) vydaný Azure Active Directory |Algoritmus HMAC (hash-based Message Authentication Code). V rámci požadavku HTTP musí volání obsahovat hash SHA-256 s kódováním base64. |
| Operace s daty – autorizace |Seznam ACL Access Control.  Seznamy řízení přístupu (ACL) na základě Azure Active Directory identit lze nastavit na úrovni souborů a složek. |Pro autorizaci na úrovni účtu – použijte [přístupové klíče účtu](../storage/common/storage-account-keys-manage.md) .<br>Pro účet, kontejner nebo autorizaci objektu BLOB použijte [klíče pro sdílené přístupové podpisy](../storage/common/storage-dotnet-shared-access-signature-part-1.md) . |
| Operace s daty – auditování |K dispozici. Informace najdete [tady](data-lake-store-diagnostic-logs.md) . |K dispozici. |
| Data šifrování v klidovém umístění |<ul><li>Transparentní, strana serveru</li> <ul><li>Pomocí klíčů spravovaných službou</li><li>Pomocí klíčů spravovaných zákazníkem ve službě Azure webrecovery</li></ul></ul> |<ul><li>Transparentní, strana serveru</li> <ul><li>Pomocí klíčů spravovaných službou</li><li>Pomocí klíčů spravovaných zákazníkem ve službě Azure webrecovery (Preview)</li></ul><li>Šifrování na straně klienta</li></ul> |
| Operace správy (například vytvoření účtu) |[Řízení přístupu na základě role](../role-based-access-control/overview.md) (RBAC) poskytované Azure pro správu účtů |[Řízení přístupu na základě role](../role-based-access-control/overview.md) (RBAC) poskytované Azure pro správu účtů |
| Sady SDK pro vývojáře |.NET, Java, Python, Node. js |.NET, Java, Python, Node. js, C++, Ruby, PHP, přejít, Android, iOS |
| Výkon úloh analýzy |Optimalizovaný výkon pro úlohy paralelní analýzy. Vysoká propustnost a IOPS |Optimalizovaný výkon pro úlohy paralelní analýzy. |
| Omezení velikosti |Bez omezení velikosti účtů, velikostí souborů nebo počtu souborů |Konkrétní omezení najdete v tématu [cíle škálovatelnosti pro účty úložiště úrovně Standard](../storage/common/scalability-targets-standard-account.md) a [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](../storage/blobs/scalability-targets.md). Větší omezení účtu k dispozici kontaktováním [podpory Azure](https://azure.microsoft.com/support/faq/) |
| Geografická redundance |Místně redundantní (více kopií dat v jedné oblasti Azure) |Místně redundantní (LRS), zóna redundantní (ZRS), globálně redundantní (GRS), globálně redundantní přístup pro čtení (RA-GRS). Další informace najdete [tady](../storage/common/storage-redundancy.md) . |
| Stav služby |Obecná dostupnost |Obecná dostupnost |
| Regionální dostupnost |Podívejte se [sem](https://azure.microsoft.com/regions/#services) |K dispozici ve všech oblastech Azure |
| Price |Zobrazit [ceny](https://azure.microsoft.com/pricing/details/data-lake-store/) |Zobrazit [ceny](https://azure.microsoft.com/pricing/details/storage/) |


