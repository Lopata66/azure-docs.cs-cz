---
title: Rychlý start Azure – Vytvoření objektu blob v úložišti objektů pomocí Pythonu | Microsoft Docs
description: V tomto rychlém startu vytvoříte v úložišti objektů (blob) účet úložiště a kontejner. Pak použijete klientskou knihovnu pro úložiště pro Python k nahrání objektu blob do služby Azure Storage, stažení objektu blob a výpisu objektů blob v kontejneru.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/14/2018
ms.author: tamram
ms.openlocfilehash: a1a931573967f12eb7abc791bd951dc6e1e9e60b
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607394"
---
# <a name="quickstart-upload-download-and-list-blobs-with-python"></a>Rychlý start: Nahrávání, stahování a výpis objektů BLOB pomocí Pythonu

V tomto rychlém startu naleznete v tématu Jak používat Python k nahrání, stažení a výpis objektů BLOB bloku v kontejneru v úložišti objektů Blob v Azure. Objekty BLOB jsou jednoduché objekty, které může obsahovat jakýkoli objem textových nebo binárních dat (například obrázky, dokumenty, streamování médií, archivovat data atd.) a se liší ve službě Azure Storage ze sdílené složky, bez schématu tabulky a fronty zpráv. (Další informace najdete v tématu [Úvod do služby Azure Storage](/azure/storage/common/storage-introduction.md).)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Ujistěte se, že máte nainstalované následující další požadavky:

* [Python](https://www.python.org/downloads/)
* [Azure Storage SDK pro Python](https://github.com/Azure/azure-sdk-for-python)

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace
[Ukázková aplikace](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) v tomto rychlém startu je základní aplikace v Pythonu.  

Pomocí [gitu](https://git-scm.com/) stáhněte kopii aplikace do svého vývojového prostředí. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Tento příkaz naklonuje úložiště *Azure-Samples/storage-blobs-python-quickstart* do vaší místní složky gitu. Pokud chcete spustit program v Pythonu, otevřete soubor *example.py* v kořenovém adresáři úložiště.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště
V aplikaci zadejte název účtu úložiště a klíč účtu pro vytvoření objektu `BlockBlobService`. V Průzkumníku řešení vašeho integrovaného vývojového prostředí (IDE) otevřete soubor *example.py*. Nahraďte hodnoty `accountname` a `accountkey` názvem a klíčem vašeho účtu. 

```python 
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
```

## <a name="run-the-sample"></a>Spuštění ukázky
Tato ukázka vytvoří testovací soubor ve složce *Dokumenty*. Ukázkový program nahraje testovací soubor do úložiště objektů blob, vypíše objekty blob v kontejneru a stáhne soubor s novým názvem. 

Nejprve nainstalujte závislosti spuštěním příkazu `pip install`:

    pip install azure-storage-blob

Potom spusťte ukázku. Zobrazí se zprávy podobné následujícímu výstupu:
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Než budete pokračovat, vyhledejte ve složce *Dokumenty* příslušné dva soubory. Můžete je otevřít a podívat se, že jsou stejné.

K zobrazení souborů v úložišti objektů blob můžete použít také nástroj, jako je [Průzkumník služby Azure Storage](https://storageexplorer.com). Průzkumník služby Azure Storage je bezplatný nástroj pro více platforem, který umožňuje přístup k informacím o účtu úložiště. 

Po ověření souborů stiskněte libovolnou klávesu a dokončete ukázku a odstraňte testovací soubory. Když teď víte, co ukázka dělá, otevřete soubor *example.py* a prohlédněte si kód. 

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

Teď si projdeme vzorový kód a vysvětlíme si, jak funguje.

### <a name="get-references-to-the-storage-objects"></a>Získání odkazů na objekty úložiště
Nejprve vytvoříte odkazy na objekty sloužící k přístupu k úložišti objektů blob a jeho správě. Tyto objekty se vzájemně využívají a každý z nich je využívaný dalším objektem v seznamu.

* Vytvořte instanci objektu **BlockBlobService**, která odkazuje na službu Blob service ve vašem účtu úložiště. 

* Vytvořte instanci objektu **CloudBlobContainer**, která představuje kontejner, ke kterému přistupujete. Kontejnery slouží k uspořádání objektů blob podobně jako složky na počítači k uspořádání souborů.

Jakmile budete mít CloudBlobContainer, vytvořte instanci objektu **CloudBlockBlob**, která odkazuje na konkrétní objekt blob, který vás zajímá. Tento objekt blob pak můžete podle potřeby nahrát, stáhnout nebo zkopírovat.

> [!IMPORTANT]
> Názvy kontejnerů musí obsahovat jen malá písmena. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

V této části vytvoříte instance objektů, vytvoříte nový kontejner a pak nastavíte oprávnění ke kontejneru tak, aby objekty blob byly veřejné. Kontejner má název **quickstartblobs**. 

```python 
# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
 
# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
block_blob_service.create_container(container_name) 

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(container_name, public_access=PublicAccess.Container)
```
### <a name="upload-blobs-to-the-container"></a>Nahrání objektů blob do kontejneru

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Nejčastěji používané jsou objekty blob bloku, které se používají také v tomto rychlém startu.  

Pokud chcete nahrát soubor do objektu blob, získejte úplnou cestu k souboru spojením názvu adresáře a názvu souboru na místním disku. Pak můžete soubor nahrát do zadané cesty pomocí metody `create\_blob\_from\_path`. 

Vzorový kód vytvoří místní soubor, který se použije k nahrání a stažení. Soubor k nahrání uloží do proměnné `file\_path\_to\_file` a název objektu blob do proměnné `local\_file\_name`. Následující příklad nahraje soubor do kontejneru **quickstartblobs**.

```python
# Create a file in Documents to test the upload and download.
local_path=os.path.expanduser("~\Documents")
local_file_name ="QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file =os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name
block_blob_service.create_blob_from_path(container_name, local_file_name, full_path_to_file)
```

V případě úložiště objektů blob můžete k nahrání použít několik metod. Například pokud máte paměťový proud, můžete místo metody `create\_blob\_from\_path` použít metodu `create\_blob\_from\_stream`. 

Objekty blob bloku můžou mít velikost až 4,7 TB a můžou být čímkoli od tabulky aplikace Excel po velké videosoubory. Objekty blob stránky se používají hlavně pro soubory VHD využívané virtuálními počítači IaaS. Doplňovací objekty blob se používají k protokolování, například když chcete zapisovat do souboru a pak přidávat další informace. Většina objektů blob ukládaných do úložiště jsou typu blok.

### <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Seznam souborů v kontejneru můžete získat pomocí metody `list_blobs`. Tato metoda vrací generátor. Následující kód načte seznam objektů blob &mdash; pak je ve smyčce projde &mdash; a zobrazí názvy nalezených objektů blob v kontejneru.  

```python
# List the blobs in the container
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Stažení objektů blob

Objekty blob můžete stáhnout na místní disk pomocí metody `the get\_blob\_to\_path`. Následující kód stáhne objekt blob nahraný v předchozí části. K názvu objektu blob se přidá přípona *_DOWNLOADED*, takže na místním disku uvidíte oba soubory. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už nepotřebujete objekty blob nahrané v rámci tohoto rychlého startu, můžete celý kontejner odstranit pomocí metody `delete\_container`. Pokud místo toho chcete odstranit jednotlivé soubory, použijte metodu `delete\_blob`.

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```
## <a name="resources-for-developing-python-applications-with-blobs"></a>Zdroje informací pro vývoj aplikací v Pythonu s využitím objektů blob

Další informace o vývoji v Pythonu s využitím úložiště objektů blob najdete v těchto dalších zdrojích informací:

### <a name="binaries-and-source-code"></a>Binární soubory a zdrojový kód

- Prohlédněte, stáhněte a nainstalujte si [zdrojový kód klientské knihovny pro Python](https://github.com/Azure/azure-storage-python) pro službu Azure Storage na GitHubu.

### <a name="client-library-reference-and-samples"></a>Klientská knihovna – referenční informace a ukázky

- Další informace o klientské knihovně pro Python najdete v [referenčních informacích k rozhraní Python API](https://docs.microsoft.com/python/api/overview/azure/storage).
- Prozkoumejte [ukázky pro úložiště objektů blob](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) napsané s využitím klientské knihovny pro Python.

## <a name="next-steps"></a>Další postup
 
V tomto rychlém startu jste zjistili, jak přenášet soubory mezi místním diskem a úložištěm objektů blob v Azure pomocí Pythonu. Další informace o práci s úložištěm objektů blob najdete v postupech pro úložiště objektů blob.

> [!div class="nextstepaction"]
> [Operace s úložištěm objektů blob – postupy](./storage-python-how-to-use-blob-storage.md)
 
Další informace o Průzkumníku služby Storage a objektech blob najdete v tématu [Správa prostředků úložiště objektů blob v Azure pomocí Průzkumníka služby Storage](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
