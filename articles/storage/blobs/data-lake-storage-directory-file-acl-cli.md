---
title: Použití rozhraní příkazového řádku Azure pro soubory & seznamů ACL v Azure Data Lake Storage Gen2
description: Pomocí Azure CLI můžete spravovat adresáře a seznamy řízení přístupu (ACL) souborů a adresářů v účtech úložiště, které mají hierarchický obor názvů.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 05/18/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 8fdcad18ccec2748761cf35f2cd0b8efe9749958
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2020
ms.locfileid: "84466132"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Použití Azure CLI ke správě adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2

V tomto článku se dozvíte, jak pomocí [rozhraní příkazového řádku Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) vytvářet a spravovat adresáře, soubory a oprávnění v účtech úložiště, které mají hierarchický obor názvů. 

Mapování Gen1 na [Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)  |  [Sdělte nám svůj názor](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](data-lake-storage-quickstart-create-account.md) pokynů.
> * Verze Azure CLI `2.6.0` nebo vyšší

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Ujistěte se, že máte nainstalovanou správnou verzi rozhraní příkazového řádku Azure CLI.

1. Otevřete [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)nebo pokud máte rozhraní příkazového řádku Azure místně [nainstalované](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , otevřete konzolovou aplikaci, například Windows PowerShell.

2. Pomocí následujícího příkazu ověřte, že verze rozhraní příkazového řádku Azure, která je nainstalovaná, je `2.6.0` nebo vyšší.

   ```azurecli
    az --version
   ```
   Pokud je vaše verze rozhraní příkazového řádku Azure nižší než `2.6.0` , nainstalujte novější verzi. Viz [instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="connect-to-the-account"></a>Připojit k účtu

1. Pokud používáte Azure CLI místně, spusťte příkaz Login.

   ```azurecli
   az login
   ```

   Pokud rozhraní příkazového řádku může otevřít výchozí prohlížeč, bude to mít za následek a načíst přihlašovací stránku Azure.

   V opačném případě otevřete stránku prohlížeče na adrese [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadejte autorizační kód zobrazený v terminálu. Pak se přihlaste pomocí přihlašovacích údajů k účtu v prohlížeči.

   Další informace o různých metodách ověřování najdete v tématu [autorizace přístupu k objektům blob nebo Queue data z Azure pomocí Azure CLI](../common/authorize-data-operations-cli.md).

2. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné na předplatné účtu úložiště, který bude hostovat váš statický Web.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Nahraďte `<subscription-id>` hodnotu zástupného symbolu číslem ID vašeho předplatného.

> [!NOTE]
> Příklad uvedený v tomto článku ukazuje autorizaci Azure Active Directory (AD). Další informace o metodách autorizace najdete v tématu [autorizace přístupu k objektům blob nebo Queue data z Azure pomocí Azure CLI](../common/authorize-data-operations-cli.md).

## <a name="create-a-file-system"></a>Vytvoření systému souborů

Systém souborů funguje jako kontejner pro vaše soubory. Můžete ho vytvořit pomocí `az storage fs create` příkazu. 

Tento příklad vytvoří systém souborů s názvem `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-system-properties"></a>Zobrazit vlastnosti systému souborů

Vlastnosti systému souborů můžete vytisknout do konzoly pomocí `az storage fs show` příkazu.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-file-system-contents"></a>Výpis obsahu systému souborů

Seznamte se s obsahem adresáře pomocí `az storage fs file list` příkazu.

V tomto příkladu je uveden seznam obsahu systému souborů s názvem `my-file-system` .

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file-system"></a>Odstranění systému souborů

Pomocí příkazu odstraňte systém souborů `az storage fs delete` .

Tento příklad odstraní systém souborů s názvem `my-file-system` . 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář pomocí `az storage fs directory create` příkazu. 

Tento příklad přidá adresář s názvem `my-directory` do systému souborů s názvem `my-file-system` , který je umístěn v účtu s názvem `mystorageaccount` .

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Zobrazit vlastnosti adresáře

Vlastnosti adresáře lze vytisknout do konzoly pomocí `az storage fs directory show` příkazu.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Přejmenování nebo přesunutí adresáře

Přejmenujte nebo přesuňte adresář pomocí `az storage fs directory move` příkazu.

Tento příklad přejmenuje adresář z názvu `my-directory` na název `my-new-directory` ve stejném systému souborů.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

Tento příklad přesune adresář do systému souborů s názvem `my-second-file-system` .

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstraňte adresář pomocí `az storage fs directory delete` příkazu.

Tento příklad odstraní adresář s názvem `my-directory` . 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Zjistit, jestli adresář existuje

Zjistěte, zda v systému souborů existuje konkrétní adresář pomocí `az storage fs directory exists` příkazu.

Tento příklad ukáže, zda adresář s názvem `my-directory` existuje v `my-file-system` systému souborů. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Stažení z adresáře

Stáhněte si soubor z adresáře pomocí `az storage fs file download` příkazu.

Tento příklad stáhne soubor s názvem `upload.txt` z adresáře s názvem `my-directory` . 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Výpis obsahu adresáře

Seznamte se s obsahem adresáře pomocí `az storage fs file list` příkazu.

Tento příklad vypíše obsah adresáře s názvem `my-directory` , který se nachází v `my-file-system` systému souborů účtu úložiště s názvem `mystorageaccount` . 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nahrajte soubor do adresáře pomocí `az storage fs directory upload` příkazu.

Tento příklad nahraje soubor s názvem `upload.txt` do adresáře s názvem `my-directory` . 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Zobrazit vlastnosti souboru

Vlastnosti souboru můžete vytisknout do konzoly pomocí `az storage fs file show` příkazu.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Přejmenování nebo přesunutí souboru

Přejmenujte nebo přesuňte soubor pomocí `az storage fs file move` příkazu.

Tento příklad přejmenuje soubor z názvu `my-file.txt` na název `my-file-renamed.txt` .

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Odstranění souboru

Odstraňte soubor pomocí `az storage fs file delete` příkazu.

Tento příklad odstraní soubor s názvem`my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="manage-permissions"></a>Spravovat oprávnění

Můžete získat, nastavit a aktualizovat přístupová oprávnění adresářů a souborů.

> [!NOTE]
> Pokud k autorizaci příkazů používáte Azure Active Directory (Azure AD), ujistěte se, že je vašemu objektu zabezpečení přiřazená [role vlastníka dat objektu BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Pokud se chcete dozvědět víc o tom, jak se používají oprávnění seznamu ACL, a důsledky jejich změny, přečtěte si téma [řízení přístupu v Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Získat seznam ACL

Získat seznam řízení přístupu k **adresáři** pomocí `az storage fs access show` příkazu.

Tento příklad získá seznam ACL adresáře a pak vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Pomocí příkazu Získejte přístupová oprávnění k **souboru** `az storage fs access show` . 

Tento příklad získá seznam řízení přístupu k souboru a poté vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Následující obrázek ukazuje výstup po získání seznamu ACL adresáře.

![Získat výstup ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

V tomto příkladu má vlastnící uživatel oprávnění ke čtení, zápisu a spouštění. Vlastnící skupina má pouze oprávnění ke čtení a spouštění. Další informace o seznamech řízení přístupu najdete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Nastavení seznamu ACL

Pomocí `az storage fs access set` příkazu nastavte seznam řízení přístupu (ACL) **adresáře**. 

Tento příklad nastavuje seznam řízení přístupu v adresáři pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele a pak vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Tento příklad nastaví *výchozí* seznam řízení přístupu v adresáři pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele a pak vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

`az storage fs access set`K nastavení seznamu ACL **souboru**použijte příkaz. 

Tento příklad nastavuje seznam řízení přístupu pro soubor pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele a pak vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Následující obrázek ukazuje výstup po nastavení seznamu ACL souboru.

![Získat výstup ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

V tomto příkladu mají vlastnící uživatel a vlastnící skupina jenom oprávnění ke čtení a zápisu. Všichni ostatní uživatelé mají oprávnění k zápisu a spouštění. Další informace o seznamech řízení přístupu najdete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-an-acl"></a>Aktualizace seznamu ACL

Dalším způsobem, jak toto oprávnění nastavit, je použít `az storage fs access set` příkaz. 

Aktualizujte seznam řízení přístupu (ACL) adresáře nebo souboru nastavením `-permissions` parametru na krátký tvar seznamu ACL.

Tento příklad aktualizuje seznam řízení přístupu k **adresáři**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Tento příklad aktualizuje seznam řízení přístupu k **souboru**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Vlastnícího uživatele a skupinu adresáře nebo souboru můžete také aktualizovat nastavením `--owner` `group` parametrů nebo na ID entity nebo hlavní název uživatele (UPN) uživatele. 

Tento příklad změní vlastníka adresáře. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Tento příklad změní vlastníka souboru. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="see-also"></a>Viz také

* [Mapování Gen1 na Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Poskytnout zpětnou vazbu](https://github.com/Azure/azure-cli-extensions/issues)
* [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)


