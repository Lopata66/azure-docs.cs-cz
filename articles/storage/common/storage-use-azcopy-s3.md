---
title: Kopírování dat ze služby Amazon S3 do Azure Storage pomocí AzCopy | Microsoft Docs
description: Přenos dat pomocí kontejnerů AzCopy a Amazon S3
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a3180593eaf8c01c772fd761d88b5f5b9f7657ee
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941509"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Kopírování dat z Amazon S3 do Azure Storage pomocí AzCopy

AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování objektů BLOB nebo souborů do nebo z účtu úložiště. Tento článek vám pomůže s kopírováním objektů, adresářů a kontejnerů z Amazon Web Services (AWS) S3 do úložiště objektů BLOB v Azure pomocí AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Vyberte způsob poskytování autorizačních přihlašovacích údajů.

* K autorizaci pomocí Azure Storage použijte token Azure Active Directory (AD) nebo Shared Access Signature (SAS).

* K autorizaci s AWS S3 použijte přístupový klíč AWS a tajný přístupový klíč.

### <a name="authorize-with-azure-storage"></a>Autorizovat pomocí Azure Storage

Pokud si chcete stáhnout AzCopy, přečtěte si článek Začínáme [s AzCopy](storage-use-azcopy-v10.md) a vyberte způsob poskytování autorizačních přihlašovacích údajů do služby úložiště.

> [!NOTE]
> V příkladech v tomto článku se předpokládá, že jste ověřili vaši identitu pomocí příkazu `AzCopy login`. AzCopy pak použije účet Azure AD k autorizaci přístupu k datům v úložišti objektů BLOB.
>
> Pokud místo toho chcete použít token SAS k autorizaci přístupu k datům objektu blob, můžete tento token připojit k adrese URL prostředku v každém příkazu AzCopy.
>
> Například: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autorizace pomocí AWS S3

Shromážděte AWS přístupový klíč a tajný klíč přístupu a pak nastavte tyto proměnné prostředí:

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Kopírování objektů, adresářů a kontejnerů

AzCopy používá [blok Put z](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) rozhraní API URL, takže se data zkopírují přímo mezi AWS S3 a servery úložiště. Tyto operace kopírování nepoužívají šířku pásma sítě vašeho počítače.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Pokud se rozhodnete odebrat data z bloků S3 po operaci kopírování, ujistěte se, že před odebráním dat jste ověřili, že data byla správně zkopírována do vašeho účtu úložiště.

> [!TIP]
> Příklady v této části uzavírají argumenty cesty s jednoduchými uvozovkami (' '). Použijte jednoduché uvozovky ve všech příkazových prostředích s výjimkou příkazového prostředí systému Windows (cmd. exe). Pokud používáte příkazové prostředí systému Windows (cmd. exe), uzavřete argumenty cesty pomocí dvojitých uvozovek ("") místo jednoduchých uvozovek (' ').

 Tyto příklady také fungují s účty, které mají hierarchický obor názvů. [Přístup k více protokolům na data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) umožňuje použít stejnou syntaxi URL (`blob.core.windows.net`) na těchto účtech. 

### <a name="copy-an-object"></a>Zkopírování objektu

Pro účty, které mají hierarchický obor názvů, použijte stejnou syntaxi URL (`blob.core.windows.net`).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Příklad** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Příklad** (hierarchický obor názvů) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> Příklady v tomto článku používají adresy URL ve stylu cesty pro AWS S3 (například: `http://s3.amazonaws.com/<bucket-name>`). 
>
> Můžete také použít virtuální adresy URL hostovaného stylu (například: `http://bucket.s3.amazonaws.com`). 
>
> Další informace o virtuálním hostování kontejnerů naleznete v tématu [virtuální hostování kontejnerů]] (https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html).

### <a name="copy-a-directory"></a>Kopírování adresáře

Pro účty, které mají hierarchický obor názvů, použijte stejnou syntaxi URL (`blob.core.windows.net`).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Příklad** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Příklad** (hierarchický obor názvů)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Kopírování intervalu

Pro účty, které mají hierarchický obor názvů, použijte stejnou syntaxi URL (`blob.core.windows.net`).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Příklad** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Příklad** (hierarchický obor názvů)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Kopírovat všechny intervaly ve všech oblastech

Pro účty, které mají hierarchický obor názvů, použijte stejnou syntaxi URL (`blob.core.windows.net`).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Příklad** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Příklad** (hierarchický obor názvů)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Kopírovat všechny intervaly v konkrétní oblasti S3

Pro účty, které mají hierarchický obor názvů, použijte stejnou syntaxi URL (`blob.core.windows.net`).

|    |     |
|--------|-----------|
| **Syntaxe** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Příklad** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Příklad** (hierarchický obor názvů)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Zpracování rozdílů v pravidlech pojmenovávání objektů

AWS S3 má jinou sadu zásad pro pojmenování názvů sad, které jsou ve srovnání s kontejnery objektů blob Azure. O nich si můžete přečíst [tady](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Pokud se rozhodnete zkopírovat skupinu kontejnerů do účtu služby Azure Storage, operace kopírování může selhat kvůli rozdílům v pojmenování.

AzCopy zpracovává dva z nejběžnějších problémů, které mohou nastat; sady obsahující tečky a intervaly, které obsahují po sobě jdoucí spojovníky. Názvy kontejnerů AWS S3 můžou obsahovat tečky a po sobě jdoucí pomlčky, ale kontejner v Azure nemůže. AzCopy nahrazuje tečky pomlčkami a po sobě jdoucích spojovníků číslo představující počet po sobě jdoucích spojovníků (například: sada s názvem `my----bucket` se `my-4-bucket`. 

I když AzCopy kopíruje soubory, kontroluje kolize názvů a pokusy o jejich vyřešení. Například pokud obsahuje množiny s názvem `bucket-name` a `bucket.name`, AzCopy přeloží kontejner s názvem `bucket.name` nejprve na `bucket-name` a následně na `bucket-name-2`.

## <a name="handle-differences-in-object-metadata"></a>Zpracování rozdílů v metadatech objektů

AWS S3 a Azure povolují v názvech klíčů objektů různé sady znaků. Můžete si přečíst o znacích, které AWS [S3 používá.](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys) Na straně Azure se klíče objektu BLOB řídí pravidly pro [ C# ](https://docs.microsoft.com/dotnet/csharp/language-reference/)pojmenovávání identifikátorů.

Jako součást příkazu AzCopy `copy` můžete zadat hodnotu volitelného příznaku `s2s-invalid-metadata-handle`, který určuje, jak chcete zpracovat soubory, kde metadata souboru obsahují nekompatibilní názvy klíčů. V následující tabulce jsou popsány všechny hodnoty příznaků.

| Hodnota příznaku | Popis  |
|--------|-----------|
| **ExcludeIfInvalid** | (Výchozí možnost) Tato metadata nejsou obsažena v přenesených objektech. AzCopy zaznamená upozornění. |
| **FailIfInvalid** | Objekty nejsou kopírovány. AzCopy zaznamená chybu a zahrne tuto chybu do neúspěšného počtu, který se zobrazí v souhrnu přenosu.  |
| **RenameIfInvalid**  | AzCopy přeloží Neplatný klíč metadat a zkopíruje objekt do Azure s použitím dvojice hodnot klíče přeložené metadat. Informace o tom, jaké kroky AzCopy přejmenovává klíče objektů, najdete v části [jak AzCopy přejmenuje klíče objektů](#rename-logic) níže. Pokud AzCopy nemůže klíč přejmenovat, objekt se nezkopíruje. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Jak AzCopy přejmenuje klíče objektů

AzCopy provádí tyto kroky:

1. Nahradí neplatné znaky znakem _.

2. Přidá řetězec `rename_` na začátek nového platného klíče.

   Tento klíč se použije k uložení původní **hodnoty**metadat.

3. Přidá řetězec `rename_key_` na začátek nového platného klíče.
   Tento klíč se použije k uložení původních metadat neplatného **klíče**.
   Tento klíč můžete použít k tomu, abyste si vyzkoušeli a obnovili metadata na straně Azure, protože klíč metadat se uchová jako hodnota ve službě BLOB Storage.

## <a name="next-steps"></a>Další kroky

Další příklady najdete v některém z těchto článků:

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)

- [Přenos dat pomocí AzCopy a BLOB Storage](storage-use-azcopy-blobs.md)

- [Přenos dat pomocí AzCopy a úložiště souborů](storage-use-azcopy-files.md)

- [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)