---
title: Požadavky na úložiště objektů Blob v Microsoft Azure Data Box | Dokumentace Microsoftu
description: Další informace o podporovaných verzích rozhraní API, sad SDK a klientské knihovny pro úložiště objektů Blob v Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61436490"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Požadavky na úložiště Azure Data Box Blob

Tento článek obsahuje seznam verzí rozhraní API služby Azure, klientské knihovny Azure a nástrojů podporovaných pomocí služby Data Box Blob storage. Pole objektů Blob úložiště dat poskytuje funkce správy objektů blob se sémantikou konzistentních s Azure. Tento článek shrnuje také známé rozdíly úložiště objektů Blob v Azure Data Box ze služby Azure Storage.

Doporučujeme, abyste si informace o pečlivě před připojení k úložišti objektů Blob pole Data a pak zpátky na ni odkazovat podle potřeby.


## <a name="storage-differences"></a>Rozdíly úložiště

|     Funkce                                             |     Azure Storage                                     |     Data Box Blob storage |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File storage                                   |    Cloudové sdílené složky SMB podporované              |    Nepodporuje se      |
|    Služba šifrování pro neaktivní uložená data                  |    256bitového šifrování AES                             |    256bitového šifrování AES |
|    Typ účtu úložiště                                 |    Účty pro obecné účely a Azure blob storage    |    Pouze pro obecné účely v1|
|    Název objektu blob                                            |    1 024 znaků (2 048 bajtů)                     |    880 znaků (1,760 bajty)|
|    Maximální velikost objektu blob bloku                              |    4,75 TB (100 MB × 50 000 bloků)                   |    4,75 TB (100 MB × 50 000 bloků) pro zařízení Azure Data Box v verze 1.8.|
|    Maximální velikost objektu blob stránky                               |    8 TB                                               |    1 TB                   |
|    Velikost stránky objektu blob stránky                                  |    512 bajtů                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Podporované verze rozhraní API

S úložištěm objektů Blob Data pole jsou podporovány následující verze rozhraní API služby Azure Storage:

Azure Data Box 1.8 a vyšší

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05) |
## <a name="supported-azure-client-libraries"></a>Podporované klientské knihovny Azure

Pro úložiště objektů Blob pole dat jsou konkrétní klientské knihovny a požadavky na příponu určitého koncového bodu. Koncové body pole datový objekt Blob úložiště nemají úplná parita s nejnovější verzí služby REST API služby Azure Blob Storage, najdete v článku [podporované verze pro Azure Data Box 1.8 a vyšší](#supported-api-versions). Klientské knihovny pro úložiště musíte mít na paměti, která je kompatibilní s rozhraním REST API verze.

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1.8 a vyšší

| Klientská knihovna     |Úložiště objektů Blob pole data podporovaná verze     | Odkaz   |     Koncový bod specifikace      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Balíček Nuget:   https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>Verze Githubu:   https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    souboru app.config                 |
|    Java                |    7.0.0                                           |    Maven balíček:   https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>Verze Githubu:   https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Nastavení připojovací řetězce         |
|    Node.js             |    2.8.3                                           |    Odkaz na NPM:   https://www.npmjs.com/package/azure-storage   (Spuštění: `npm install azure-storage@2.7.0` )   <br>Verze Githubu:   https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Deklarace instance služby    |
|    C++                 |    5.2.0                                           |    Balíček Nuget:   https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>Verze Githubu:   https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Nastavení připojovací řetězce         |
|    PHP                 |    1.2.0                                           |    Verze Githubu:<br>Běžné: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Objekt BLOB: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Instalace přes Composer (Chcete-li získat další informace, viz podrobnosti níže.)                                                                                                             |    Nastavení připojovací řetězce         |
|    Python              |    1.1.0                                           |    Verze Githubu:<br>Běžné:   https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Objekt BLOB:   https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Deklarace instance služby    |
|    Ruby                |    1.0.1                                           |    Balíček RubyGems:<br>Běžné:   https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Objekt BLOB: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>Verze Githubu:<br>Běžné: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Objekt BLOB: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Nastavení připojovací řetězce         |



### <a name="install-php-client-via-composer---current"></a>Instalace klienta PHP prostřednictvím autora – aktuální

Chcete-li nainstalovat prostřednictvím autora: (take blob jako příklad).
1. Vytvořte soubor s názvem souboru composer.json v kořenovém adresáři projektu s následujícím kódem:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Stáhněte si `composer.phar` do kořenového adresáře projektu.

3. Spustit: Nainstalujte php composer.phar.

### <a name="endpoint-declaration"></a>Koncový bod deklarace

Koncový bod úložiště objektů Blob v Azure Data Box obsahuje dvě části: název oblasti a domény zařízení Data Box. V poli datový objekt Blob storage SDK, je výchozí koncový bod `\<serial no. of the device>.microsoftdatabox.com`.  Další informace o koncový bod služby blob service, přejděte na [připojit přes pole datový objekt Blob úložiště](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Příklady

### <a name="net"></a>.NET

Pro úložiště objektů Blob pole dat, je podle přípona koncového bodu `app.config` souboru:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Pro úložiště objektů Blob pole dat přípona koncového bodu je zadán v nastavení připojovacího řetězce:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Pro úložiště objektů Blob pole dat přípona koncového bodu určena v instanci deklarace:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Pro úložiště objektů Blob pole dat přípona koncového bodu je zadán v nastavení připojovacího řetězce:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Pro úložiště objektů Blob pole dat přípona koncového bodu je zadán v nastavení připojovacího řetězce:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Pro úložiště objektů Blob pole dat přípona koncového bodu určena v instanci deklarace:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Pro úložiště objektů Blob pole dat přípona koncového bodu je zadán v nastavení připojovacího řetězce:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Další postup

* [Nasazení Azure Data Box](data-box-deploy-ordered.md)
