---
title: Ukázky pro Azure Storage pomocí Javy | Dokumentace Microsoftu
description: Zobrazit, stáhnout a spustit ukázkový kód a aplikace pro službu Azure Storage. Objevte úvodními ukázkami pro objekty BLOB, fronty, tabulky a soubory, pomocí klientských knihoven pro úložiště Java.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 05/03/2019
ms.author: mhopkins
ms.subservice: common
ms.openlocfilehash: 3d241f1905244d3a8039372262f84ba0fd25220d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209772"
---
# <a name="azure-storage-samples-using-java"></a>Ukázky pro Azure Storage pomocí Javy

## <a name="java-sample-index"></a>Java ukázkového indexu

Následující tabulka obsahuje přehled o našem úložišti ukázek a scénáře popsané v každém vzorku. Kliknutím na odkazy k zobrazení odpovídající vzorový kód na Githubu.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Koncový bod</th><th style="font-size:110%">Scénář</th><th style="font-size:110%">Vzorový kód</th></tr></thead><tbody>
<tr>
<td rowspan="16"><b>Objekt blob</b></td>
<td>Doplňovací objekt Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v Javě</a></td>
</tr>
<tr>
<td>Objekt Blob bloku</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v Javě</a></td>
</tr>
<tr>
<td>Šifrování na straně klienta</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption">Začínáme s šifrování na straně klienta Azure v jazyce Java</a></td>
</tr>
<tr>
<td>Zkopírování objektu Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v Javě</a></td>
</tr>
<tr>
<td>Vytvoření kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v Javě</a></td>
</tr>
<tr>
<td>Odstranit objekt Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v Javě</a></td>
</tr>
<tr>
<td>Odstranění kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v Javě</a></td>
</tr>
<tr>
<td>Objekt BLOB metadat/vlastnosti/Stats</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Začínáme se službou Azure Blob Service v Javě</a></td>
</tr>
<tr>
<td>Seznam ACL/Metadata/vlastnosti kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Začínáme se službou Azure Blob Service v Javě</a></td>
</tr>
<tr>
<td>Získání rozsahů stránek</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java#L399">Začínáme se službou Azure Blob Service v Javě</a></td>
</tr>
<tr>
<td>Zapůjčení objektu Blob nebo kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v Javě</a></td>
</tr>
<tr>
<td>Výpis objektu Blob nebo kontejneru</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v Javě</a></td>
</tr>
<tr>
<td>Page Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v Javě</a></td>
</tr>
<tr>
<td>SAS</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513">Ukázka testů SAS</a></td>
</tr>   
<tr>
<td>Vlastnosti služby</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Začínáme se službou Azure Blob Service v Javě</a></td>
</tr>
<tr>
<td>Vytvoření snímku objektu Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Začínáme se službou Azure Blob Service v Javě</a></td>
</tr>
<tr>
<td rowspan="9"><b>File</b></td>
<td>Vytvoření sdílené složky/adresářů a souborů</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Začínáme se službou Azure File v jazyce Java</a></td>
</tr>
<tr>
<td>Odstranění sdílené složky/adresářů a souborů</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Začínáme se službou Azure File v jazyce Java</a></td>
</tr>
<tr>
<td>Adresář vlastností/metadat</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Začínáme se službou Azure File v jazyce Java</a></td>
</tr>
<tr>
<td>Stažení souborů</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Začínáme se službou Azure File v jazyce Java</a></td>
</tr>
<tr>
<td>Soubor vlastností/metadat/metriky</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Začínáme se službou Azure File v jazyce Java</a></td>
</tr>
<tr>
<td>File Service Properties</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Začínáme se službou Azure File v jazyce Java</a></td>
</tr>
<tr>
<td>Seznam adresářů a souborů</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Začínáme se službou Azure File v jazyce Java</a></td>
</tr>
<tr>
<td>Zobrazit seznam sdílených složek</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Začínáme se službou Azure File v jazyce Java</a></td>
</tr>
<tr>
<td>Sdílení vlastností/metadat/Stats</td>
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Začínáme se službou Azure File v jazyce Java</a></td>
</tr>
<tr>
<td rowspan="8"><b>fronty</b></td>
<td>Přidat zprávu</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java#L63">Začínáme s Azure Queue Service v Javě</a></td>
</tr>
<tr>
<td>Šifrování na straně klienta</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption/blob/master/src/gettingstarted/KeyVaultGettingStarted.java">Začínáme s šifrování na straně klienta Azure v jazyce Java</a></td>
</tr>
<tr>
<td>Vytvoření fronty</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Začínáme s Azure Queue Service v Javě</a></td>
</tr>
<tr>
<td>Odstranit zprávu či fronty</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Začínáme s Azure Queue Service v Javě</a></td>
</tr>
<tr>
<td>Náhled zprávy</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Začínáme s Azure Queue Service v Javě</a></td>
</tr>
<tr>
<td>Fronty ACL/Metadata/Stats</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Začínáme s Azure Queue Service v Javě</a></td>
</tr>
<tr>
<td>Vlastnosti služby Queue</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Začínáme s Azure Queue Service v Javě</a></td>
</tr>
<tr>
<td>Aktualizace zprávy</td>
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Začínáme s Azure Queue Service v Javě</a></td>
</tr>
<tr>
<td rowspan="7"><b>Tabulka</b></td>
<td>Vytvoření tabulky</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Začínáme se službou Azure Table Storage v Javě</a></td>
</tr>
<tr>
<td>Odstranit entitu nebo tabulku</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Začínáme se službou Azure Table Storage v Javě</a></td>
</tr>
<tr>
<td>Vložit/sloučení/nahrazení Entity</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Začínáme se službou Azure Table Storage v Javě</a></td>
</tr>
<tr>
<td>Dotazování entit</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Začínáme se službou Azure Table Storage v Javě</a></td>
</tr>
<tr>
<td>Tabulky dotazů</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Začínáme se službou Azure Table Storage v Javě</a></td>
</tr>
<tr>
<td>Seznam ACL/vlastnosti tabulky</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java">Začínáme se službou Azure Table Storage v Javě</a></td>
</tr>
<tr>
<td>Aktualizace Entity</td>
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Začínáme se službou Azure Table Storage v Javě</a></td>
</tr>
</tbody>
</table>
<br/>

## <a name="azure-code-samples-library"></a>Knihovna ukázky kódu Azure

Chcete-li zobrazit úplnou ukázku knihovny, přejděte na [vzorových kódů Azure](https://azure.microsoft.com/resources/samples/?service=storage) knihovny, která obsahuje ukázky pro Azure Storage, který lze stáhnout a spustit místně. Ukázka knihovny kódu poskytuje ukázkový kód ve formátu ZIP. Alternativně můžete procházet a naklonujte úložiště GitHub pro každý vzorek.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Příručky Začínáme

Pokud hledáte pokyny o tom, jak nainstalovat a začít s klientských knihoven pro úložiště Azure, prohlédněte si tyto návody.

* [Začínáme se službou Azure Blob Service v Javě](../blobs/storage-quickstart-blobs-java.md)
* [Začínáme s Azure Queue Service v Javě](../queues/storage-java-how-to-use-queue-storage.md)
* [Začínáme se službou Azure Table Storage v Javě](../../cosmos-db/table-storage-how-to-use-java.md)
* [Začínáme se službou Azure File v jazyce Java](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Další postup

Informace o ukázky pro ostatní jazyky:

* .NET: [Ukázky pro Azure Storage s použitím .NET](storage-samples-dotnet.md)
* Všechny ostatní jazyky: [Ukázky pro Azure Storage](storage-samples.md)
