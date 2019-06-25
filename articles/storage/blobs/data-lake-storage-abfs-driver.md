---
title: Ovladač systému souborů objektů Blob v Azure pro Azure Data Lake Storage Gen2
description: Ovladač systému souborů Hadoop ABFS
services: storage
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 57cda4d07315c6c37c2ce51f530fb081949b628c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939501"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Ovladač systému souborů Azure Blob (ABFS): Vyhrazené ovladač Azure Storage pro Hadoop

Jedním z primárních přístupové metody pro data ve službě Azure Data Lake Storage Gen2 je prostřednictvím [systému souborů Hadoop](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). Data Lake Storage Gen2 umožňuje uživatelům přístupu úložiště objektů Blob v Azure nový ovladač ovladač systému souborů Azure Blob nebo `ABFS`. ABFS je součástí systému Apache Hadoop a je součástí mnoha komerčních distribuce hadoopu. Pomocí tohoto ovladače, mnoho aplikací a architektur můžou k datům ve službě Azure Blob Storage bez jakéhokoli kódu explicitně odkazující na Gen2 úložiště Data Lake.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Předchozí funkce: Ovladače Windows Azure Storage Blob

Ovladače Windows Azure Storage Blob nebo [WASB ovladač](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) původní podporu pro úložiště objektů Blob v Azure k dispozici. Tento ovladač provádět složité úlohy systému souborů mapování sémantiku (podle potřeby pomocí rozhraní Hadoop systému souborů) na tento objekt uložení rozhraní se stylem podobným vystavený službou Azure Blob Storage. Tento ovladač i nadále podporuje tento model poskytuje vysoký výkon přístupu k datům uloženým v objektech BLOB, ale obsahuje značné množství kódu provádí toto mapování, kvůli tomu obtížné udržovat. Kromě toho některé operace, jako [FileSystem.rename()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) a [FileSystem.delete()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) při použití do adresáře vyžadovat ovladač obrovské množství operací (vzhledem k absenci objektu úložiště Podpora pro adresáře) která často vede k snížení výkonu. Ovladač ABFS je navržená k překonání inherentní nedostatky WASB.

## <a name="the-azure-blob-file-system-driver"></a>Ovladač systému souborů Azure Blob

[Rozhraní Azure Data Lake Storage REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) je navržen pro podporu sémantiku systému souborů v Azure Blob Storage. Vzhledem k tomu, že systém souborů Hadoop je navržený tak, aby podporují stejnou sémantiku neexistuje žádný požadavek pro komplexní mapování v ovladači. Ovladač systému souborů Azure Blob (nebo ABFS) tedy překrytí pouhé klienta pro rozhraní REST API.

Existují však některé funkce, které ovladače musí i nadále provádět:

### <a name="uri-scheme-to-reference-data"></a>Schéma identifikátoru URI pro referenční data

Konzistentní s jinými implementacemi systému souborů v systému Hadoop, ovladač ABFS definuje vlastní schéma identifikátoru URI tak, aby (adresářů a souborů) může být výrazně materiály. Schéma identifikátoru URI je popsána v [použít identifikátor URI služby Azure Data Lake Storage Gen2](./data-lake-storage-introduction-abfs-uri.md). Struktura identifikátoru URI je: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Pomocí výše uvedených formát identifikátoru URI, standardní nástroje Hadoop a architektury je možné odkazovat na tyto prostředky:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

Interně ovladač ABFS přeloží zadaný v identifikátoru URI k souborům a adresářům nebo prostředky a provede volání REST API služby Azure Data Lake Storage s odkazy.

### <a name="authentication"></a>Authentication

Ovladač ABFS podporuje dva typy ověřování tak, aby aplikaci Hadoop může zabezpečený přístup k prostředků obsažených v rámci účtu Data Lake Storage Gen2 podporuje. Úplné podrobnosti o dostupných ověřovací schémata jsou k dispozici v [Průvodci zabezpečením Azure Storage](../common/storage-security-guide.md). Jsou to tyto:

- **Sdílený klíč:** To umožňuje uživatelům přístup ke všem prostředkům v účtu. Klíč je zašifrované a uložené v konfiguraci systému Hadoop.

- **Tokenu nosiče OAuth Azure Active Directory:** Azure AD nosné tokeny jsou získaných a aktualizovat ovladač pomocí buď identitě koncového uživatele nebo instančního objektu nakonfigurované. Pomocí tohoto modelu ověřování, autorizaci veškerý přístup na základě za volání pomocí identity přidružené k zadaného tokenu a vyhodnotit proti přiřazené POSIX přístupu ovládacího prvku seznam (ACL).

### <a name="configuration"></a>Konfigurace

Všechny konfigurace pro ovladač ABFS je uložená v <code>core-site.xml</code> konfigurační soubor. V distribucích systému Hadoop s [Ambari](https://ambari.apache.org/), konfigurace může spravovat také pomocí webového portálu nebo rozhraní Ambari REST API.

Podrobnosti o všech položek podporované konfigurace, které jsou určené v [Hadoop oficiální dokumentaci](https://hadoop.apache.org/docs/current/hadoop-azure/index.html).

### <a name="hadoop-documentation"></a>Dokumentace ke službě Hadoop

Ovladač ABFS je plně dokumentovány v článku [Hadoop oficiální dokumentaci](https://hadoop.apache.org/docs/current/hadoop-azure/index.html)

## <a name="next-steps"></a>Další postup

- [Vytvoření clusteru služby Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)
- [Azure Data Lake Storage Gen2 URI](./data-lake-storage-introduction-abfs-uri.md)
