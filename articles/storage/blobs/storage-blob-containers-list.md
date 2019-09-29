---
title: Výpis kontejnerů objektů BLOB pomocí Azure Storage .NET
description: Přečtěte si, jak zobrazit seznam kontejnerů objektů BLOB v účtu Azure Storage pomocí klientské knihovny .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 06454798deb4a5bc5064e28535a837f73c083e1c
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671304"
---
# <a name="list-blob-containers-with-net"></a>Výpis kontejnerů objektů BLOB pomocí .NET

Když vytvoříte seznam kontejnerů v účtu Azure Storage z kódu, můžete zadat několik možností pro správu, jak se výsledky vrátí z Azure Storage. Tento článek ukazuje, jak zobrazit seznam kontejnerů pomocí [klientské knihovny Azure Storage pro .NET](/dotnet/api/overview/azure/storage/client).  

## <a name="understand-container-listing-options"></a>Principy možností výpisu kontejneru

Pokud chcete zobrazit seznam kontejnerů ve vašem účtu úložiště, zavolejte jednu z následujících metod:

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

Přetížení pro tyto metody poskytují další možnosti pro správu způsobu, jakým operace výpisu vrací kontejnery. Tyto možnosti jsou popsány v následujících částech.

### <a name="manage-how-many-results-are-returned"></a>Spravujte, kolik výsledků se vrátí.

Ve výchozím nastavení vrací operace výpisu po dobu až 5000 výsledků. Chcete-li vrátit menší sadu výsledků, zadejte při volání jedné z metod `maxresults` **ListContainerSegmented** nenulovou hodnotu parametru.

Pokud váš účet úložiště obsahuje více než 5000 kontejnerů, nebo pokud jste zadali hodnotu `maxresults` , kterou operace výpisu vrátí podmnožinu kontejnerů v účtu úložiště, pak Azure Storage vrátí token pro *pokračování* s Seznam kontejnerů. Token pokračování je neprůhledná hodnota, kterou můžete použít k načtení další sady výsledků z Azure Storage.

V kódu zkontrolujte hodnotu tokenu pokračování a určete, zda má hodnotu null. Pokud má token pokračování hodnotu null, sada výsledků je dokončena. Pokud token pro pokračování není null, zavolejte znovu **ListContainersSegmented** nebo **ListContainersSegmentedAsync** , předejte do tokenu pro pokračování, aby se načetla další sada výsledků, dokud token pro pokračování nemá hodnotu null.

### <a name="filter-results-with-a-prefix"></a>Filtrovat výsledky s předponou

Chcete-li filtrovat seznam kontejnerů, zadejte řetězec pro `prefix` parametr. Řetězec předpony může obsahovat jeden nebo více znaků. Azure Storage pak vrátí pouze kontejnery, jejichž názvy začínají předponou.

### <a name="return-container-metadata"></a>Vrátit metadata kontejneru

Chcete-li vrátit metadata kontejneru s výsledky, zadejte hodnotu **metadat** pro výčet [ContainerListDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) . Azure Storage zahrnuje metadata s každým vráceným kontejnerem, takže nemusíte také volat jednu z metod **FetchAttributes** k načtení metadat kontejneru.

## <a name="example-list-containers"></a>Příklad: Výpis kontejnerů

Následující příklad asynchronně vypíše kontejnery v účtu úložiště, který začíná zadanou předponou. Příklad vypíše kontejnery v přírůstcích po 5 výsledků a pomocí tokenu pro pokračování získá další segment výsledků. Příklad také vrátí metadata kontejneru s výsledky.

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("List all containers beginning with prefix {0}, plus container metadata:", prefix);

    try
    {
        ContainerResultSegment resultSegment = null;
        BlobContinuationToken continuationToken = null;

        do
        {
            // List containers beginning with the specified prefix, returning segments of 5 results each.
            // Passing null for the maxResults parameter returns the max number of results (up to 5000).
            // Requesting the container's metadata with the listing operation populates the metadata,
            // so it's not necessary to also call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, 5, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token. If not null, get the next segment.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Viz také:

[Vypsat kontejnery](/rest/api/storageservices/list-containers2)
[vytváření výčtu prostředků objektů BLOB](/rest/api/storageservices/enumerating-blob-resources)
