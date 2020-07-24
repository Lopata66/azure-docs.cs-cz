---
title: Přidání úložiště do mezipaměti prostředí Azure HPC
description: Jak definovat cíle úložiště, aby mezipaměť prostředí Azure HPC mohla používat místní systém souborů NFS nebo kontejnery objektů blob Azure pro dlouhodobé ukládání souborů
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 6923bb31e53493dd01f41cb0b0449f2093bc7e91
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092469"
---
# <a name="add-storage-targets"></a>Přidání cílů úložiště

*Cíle úložiště* jsou back-endové úložiště pro soubory, které jsou dostupné prostřednictvím mezipaměti HPC Azure. Můžete přidat úložiště NFS (jako je místní hardwarový systém) nebo ukládat data do objektu blob Azure.

Pro jednu mezipaměť můžete definovat až deset různých cílů úložiště. Mezipaměť prezentuje všechny cíle úložiště v jednom agregovaném oboru názvů.

Mějte na paměti, že exporty úložiště musí být dostupné z virtuální sítě vaší mezipaměti. U místních hardwarových úložišť možná budete muset nastavit server DNS, který dokáže přeložit názvy hostitelů pro přístup k úložišti NFS. Další informace najdete v tématu věnovaném [přístupu DNS](hpc-cache-prerequisites.md#dns-access).

Přidejte cíle úložiště po vytvoření mezipaměti. Postup se mírně liší v závislosti na tom, zda přidáváte službu Azure Blob Storage nebo export systému souborů NFS. Podrobnosti jsou uvedené níže.

Kliknutím na obrázek níže si můžete přehrát [ukázku](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) pro vytvoření mezipaměti a Přidání cíle úložiště z Azure Portal.

[![Miniatura videa: mezipaměť prostředí Azure HPC: Instalační program (kliknutím můžete navštívit stránku video)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="view-storage-targets"></a>Zobrazit cíle úložiště

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Z Azure Portal otevřete instanci mezipaměti a na levém bočním panelu klikněte na možnost **cíle úložiště** . Stránka cíle úložiště obsahuje seznam všech stávajících cílů a obsahuje odkaz na přidání nového.

![snímek obrazovky s odkazem na cíle úložiště na bočním panelu pod nadpisem konfigurovat, což je mezi záhlavími a nastaveními a monitorováním kategorie](media/hpc-cache-storage-targets-sidebar.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Pro zobrazení stávajících cílů úložiště pro mezipaměť použijte možnost [AZ HPC-cache Storage-Target list](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) . Zadejte název mezipaměti a skupinu prostředků (pokud ji nenastavíte globálně).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Pokud chcete zobrazit podrobnosti o konkrétním cíli úložiště, použijte [příkaz AZ HPC-cache Storage-Target show](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) . (Zadejte cíl úložiště podle názvu.)

Příklad:

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="add-a-new-azure-blob-storage-target"></a>Přidat nový cíl úložiště objektů BLOB v Azure

Nový cíl úložiště objektů BLOB potřebuje prázdný kontejner objektů BLOB nebo kontejner, který je naplněný daty ve formátu cloudového systému souborů mezipaměti HPC Azure HPC. Přečtěte si další informace o předběžném načítání kontejneru objektů BLOB v tématu [přesun dat do služby Azure Blob Storage](hpc-cache-ingest.md).

Stránka Azure Portal **Přidat cíl úložiště** obsahuje možnost vytvořit nový kontejner objektů BLOB těsně před tím, než ho přidáte.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![snímek obrazovky s cílovou stránkou pro přidání cílení úložiště s informacemi o novém cíli úložiště Azure Blob Storage](media/hpc-cache-add-blob.png)

Pro definování kontejneru objektů BLOB v Azure zadejte tyto informace.

* **Název cíle úložiště** – nastavte název, který identifikuje tento cíl úložiště v mezipaměti prostředí Azure HPC.
* **Cílový typ** – vyberte **objekt BLOB**.
* **Účet úložiště** – vyberte účet, který chcete použít.

  Pro přístup k účtu úložiště bude nutné autorizovat instanci mezipaměti, jak je popsáno v tématu [Přidání rolí přístupu](#add-the-access-control-roles-to-your-account).

  Informace o druhu účtu úložiště, který můžete použít, najdete v článku [požadavky na úložiště objektů BLOB](hpc-cache-prerequisites.md#blob-storage-requirements).

* **Kontejner úložiště** – vyberte kontejner objektů BLOB pro tento cíl nebo klikněte na **vytvořit nový**.

  ![snímek obrazovky dialogového okna pro zadání názvu a úrovně přístupu (privátní) pro nový kontejner](media/add-blob-new-container.png)

* **Cesta virtuálního oboru názvů** – nastavte cestu k souboru pro klienta pro tento cíl úložiště. Další informace o funkci virtuálního oboru názvů najdete v tématu [Konfigurace agregovaného oboru názvů](hpc-cache-namespace.md) .

Po dokončení klikněte na tlačítko **OK** a přidejte tak cíl úložiště.

> [!NOTE]
> Pokud je brána firewall účtu úložiště nastavená tak, aby omezila přístup jenom na vybrané sítě, použijte dočasné řešení popsané v tématu [práce s nastavením brány firewall účtu úložiště BLOB](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>Přidání rolí řízení přístupu ke svému účtu

Mezipaměť HPC Azure používá [řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) k autorizaci služby cache Service pro přístup k vašemu účtu úložiště pro cíle Azure Blob Storage.

Vlastník účtu úložiště musí explicitně přidat role přispěvatele [účtu úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) a přispěvatel [dat objektů BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) pro uživatele "poskytovatel prostředků mezipaměti HPC".

Můžete to provést předem nebo kliknutím na odkaz na stránce, kam přidáte cíl úložiště objektů BLOB. Mějte na paměti, že může trvat až pět minut, než se nastavení role rozšíří přes prostředí Azure, takže byste před vytvořením cíle úložiště měli počkat několik minut od přidání rolí.

Postup přidání rolí RBAC:

1. Otevřete stránku **řízení přístupu (IAM)** pro účet úložiště. (Odkaz na stránce **Přidat cíl úložiště** automaticky otevře tuto stránku pro vybraný účet.)

1. Klikněte na v **+** horní části stránky a vyberte **Přidat přiřazení role**.

1. V seznamu vyberte roli Přispěvatel účtu úložiště.

1. V poli **přiřadit přístup k** ponechte vybranou výchozí hodnotu ("uživatel služby Azure AD, skupina nebo instanční objekt").  

1. V poli **Vybrat** vyhledejte "HPC".  Tento řetězec by měl odpovídat jednomu instančnímu objektu s názvem "poskytovatel prostředků mezipaměti HPC". Kliknutím vyberte objekt zabezpečení.

   > [!NOTE]
   > Pokud hledání "HPC" nefunguje, zkuste místo toho použít řetězec "storagecache". Uživatelé, kteří se účastnili verzí Preview (před GA), můžou potřebovat použít starší název instančního objektu.

1. V dolní části klikněte na tlačítko **Uložit** .

1. Tento postup opakujte, pokud chcete přiřadit roli "Přispěvatel dat objektů BLOB úložiště".  

![snímek obrazovky s grafickým uživatelským rozhraním pro přiřazení role](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

### <a name="prerequisite-storage-account-access"></a>Předpoklad: přístup k účtu úložiště

Před přidáním cíle služby Blob Storage ověřte, zda má mezipaměť správné role pro přístup k účtu úložiště, a zda nastavení brány firewall umožní vytvoření cíle úložiště.

Mezipaměť HPC Azure používá [řízení přístupu na základě role (RBAC)](../role-based-access-control/index.yml) k autorizaci služby cache Service pro přístup k vašemu účtu úložiště pro cíle Azure Blob Storage.

Vlastník účtu úložiště musí explicitně přidat role přispěvatele [účtu úložiště](../role-based-access-control/built-in-roles.md#storage-account-contributor) a přispěvatel [dat objektů BLOB úložiště](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) pro uživatele "poskytovatel prostředků mezipaměti HPC".

Vytvoření cíle úložiště se nezdaří, pokud mezipaměť tyto role nemá.

Může trvat až pět minut, než se nastavení role rozšíří přes prostředí Azure, takže byste před vytvořením cíle úložiště měli počkat několik minut od přidání rolí.

Podrobné pokyny najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure CLI](../role-based-access-control/role-assignments-cli.md) .

Taky ověřte nastavení brány firewall účtu úložiště. Pokud je brána firewall nastavená tak, aby omezila přístup jenom na vybrané sítě, může vytvoření cíle úložiště selhat. Použijte alternativní řešení popsané v části [práce s nastavením brány firewall účtu úložiště BLOB](hpc-cache-blob-firewall-fix.md).

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Přidání cíle úložiště objektů BLOB pomocí Azure CLI

K definování cíle Azure Blob Storage použijte rozhraní [AZ HPC-cache BLOB-Storage-Target Add](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-add) .

Kromě standardních parametrů skupiny prostředků a názvu mezipaměti musíte poskytnout tyto možnosti pro cíl úložiště:

* ``--name``-Nastavte název, který identifikuje tento cíl úložiště v mezipaměti prostředí Azure HPC.

* ``--storage-account``– Identifikátor účtu, v tomto formátu:/subscriptions/*<subscription_id>*/resourceGroups/*<storage_resource_group>*/Providers/Microsoft.Storage/storageAccounts/*<account_name*>

  Informace o druhu účtu úložiště, který můžete použít, najdete v článku [požadavky na úložiště objektů BLOB](hpc-cache-prerequisites.md#blob-storage-requirements).

* ``--container-name``-Zadejte název kontejneru, který chcete použít pro tento cíl úložiště.

* ``--virtual-namespace-path``– Nastavte cestu k souboru pro klienta pro tento cíl úložiště. Uzavřete cesty do uvozovek. Další informace o funkci virtuálního oboru názvů najdete [v tématu Naplánování agregovaného oboru názvů](hpc-cache-namespace.md) .

Příklad příkazu:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Přidání nového cíle úložiště systému souborů NFS

Cíl úložiště NFS obsahuje více polí, než je cílem úložiště objektů BLOB. Tato pole určují, jak se dostat k exportu úložiště a jak efektivně ukládat data do mezipaměti. Cíl úložiště NFS také umožňuje vytvořit více cest oboru názvů, pokud má hostitel systému souborů NFS více než jeden export.

![Snímek obrazovky s definovaným cílovou stránkou pro přidání úložiště s cílovým systémem souborů NFS](media/add-nfs-target.png)

> [!NOTE]
> Před vytvořením cíle úložiště NFS se ujistěte, že je váš systém úložiště přístupný z mezipaměti HPC Azure a splňuje požadavky na oprávnění. Vytvoření cíle úložiště se nezdaří, pokud mezipaměť nemá přístup k systému úložiště. Podrobnosti najdete v tématu [požadavky na úložiště NFS](hpc-cache-prerequisites.md#nfs-storage-requirements) a [řešení potíží s cíli konfigurace serveru NAS a úložiště NFS](troubleshoot-nas.md) .

### <a name="choose-a-usage-model"></a>Výběr modelu použití
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Při vytváření cíle úložiště, který odkazuje na systém úložiště NFS, je nutné zvolit model využití pro tento cíl. Tento model určuje, jak jsou data ukládána do mezipaměti.

Existují tři možnosti:

* **Čtení těžkých, zřídka používaných zápisů** – tuto možnost použijte, pokud chcete zrychlit přístup pro čtení souborů, které jsou statické nebo zřídka změněné.

  Tato možnost ukládá do mezipaměti soubory, které klienti čtou, ale předává zápis do úložiště back-endu okamžitě. Soubory uložené v mezipaměti se nikdy nerovnají souborům na svazku úložiště NFS.

  Tuto možnost nepoužívejte, pokud existuje riziko, že soubor může být upraven přímo v systému úložiště, aniž byste ho nejdřív napsali do mezipaměti. Pokud k tomu dojde, verze souboru v mezipaměti nebude nikdy aktualizována změnami z back-endu a datová sada může být nekonzistentní.

* Více **než 15% zápisů** – Tato možnost zrychluje výkon čtení i zápisu. Při použití této možnosti musí mít všichni klienti přístup k souborům přes mezipaměť prostředí Azure HPC místo přímého připojení k úložišti back-endu. Soubory v mezipaměti budou mít poslední změny, které nejsou uložené na back-endu.

  V tomto modelu použití nejsou soubory v mezipaměti u souborů v úložišti back-endu kontrolovány. Předpokládá se, že verze souboru v mezipaměti je aktuálnější. Upravený soubor v mezipaměti se zapisuje do back-endového systému úložiště, po kterém byl v mezipaměti, a to za hodinu bez dalších změn.

* **Klienti zapisují do cíle NFS, vynechá mezipaměť** – tuto možnost vyberte, pokud klienti v pracovním postupu zapisují data přímo do systému úložiště, aniž by museli nejdřív zapisovat do mezipaměti. Soubory, které klienti požadují, jsou ukládány do mezipaměti, ale všechny změny těchto souborů z klienta jsou okamžitě předány zpět do back-endového systému úložiště.

  V tomto modelu použití jsou soubory v mezipaměti často kontrolovány proti verzím back-endu pro aktualizace. Toto ověření umožňuje změnu souborů mimo mezipaměť při zachování konzistence dat.

Tato tabulka shrnuje rozdíly v modelu použití:

| Model využití                   | Režim ukládání do mezipaměti | Ověření back-endu | Maximální zpoždění před zpětným zápisem |
|-------------------------------|--------------|-----------------------|--------------------------|
| Čtení těžkých, nečastých zápisů | Číst         | Nikdy                 | Žádné                     |
| Více než 15% zápisů       | Čtení/zápis   | Nikdy                 | 1 hodina                   |
| Klienti obcházejí mezipaměť      | Číst         | 30 sekund            | Žádné                     |

### <a name="create-an-nfs-storage-target"></a>Vytvoření cíle úložiště NFS

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Snímek obrazovky s definovaným cílovou stránkou pro přidání úložiště s cílovým systémem souborů NFS](media/add-nfs-target.png)

Zadejte tyto informace pro cíl úložiště zálohovaného systémem souborů NFS:

* **Název cíle úložiště** – nastavte název, který identifikuje tento cíl úložiště v mezipaměti prostředí Azure HPC.

* **Cílový typ** – vyberte **systém souborů NFS**.

* **Název hostitele** – zadejte IP adresu nebo plně kvalifikovaný název domény pro systém úložiště NFS. (Použijte název domény jenom v případě, že vaše mezipaměť má přístup k serveru DNS, který dokáže tento název přeložit.)

* **Model využití** – vyberte jeden z profilů ukládání dat do mezipaměti na základě pracovního postupu, který je popsaný v části [Výběr modelu použití](#choose-a-usage-model) výše.

### <a name="nfs-namespace-paths"></a>Cesty oboru názvů NFS

Cíl úložiště NFS může mít několik virtuálních cest, pokud každá cesta představuje jiný export nebo podadresář ve stejném systému úložiště.

Vytvoření všech cest z jednoho cíle úložiště.

[Cesty oboru názvů můžete kdykoli přidat a upravit](hpc-cache-edit-storage.md) v cíli úložiště.

Zadejte tyto hodnoty pro každou cestu oboru názvů:

* **Cesta virtuálního oboru názvů** – nastavte cestu k souboru pro klienta pro tento cíl úložiště. Další informace o funkci virtuálního oboru názvů najdete v tématu [Konfigurace agregovaného oboru názvů](hpc-cache-namespace.md) .

* **Cesta exportu NFS** – zadejte cestu k exportu NFS.

* **Cesta k podadresáři** : Pokud chcete připojit určitý podadresář exportu, zadejte ho sem. Pokud ne, ponechte toto pole prázdné.

Po dokončení klikněte na tlačítko **OK** a přidejte tak cíl úložiště.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Použijte příkaz Azure CLI [AZ HPC-cache NFS-Storage-Target Add](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-add) k vytvoření cíle úložiště. Zadejte kromě názvu mezipaměti a skupiny prostředků mezipaměti tyto hodnoty:

* ``--name``-Nastavte název, který identifikuje tento cíl úložiště v mezipaměti prostředí Azure HPC.
* ``--nfs3-target``– IP adresa systému úložiště NFS. (Plně kvalifikovaný název domény můžete použít tady, pokud má vaše mezipaměť přístup k serveru DNS, který dokáže tento název přeložit.)
* ``--nfs3-usage-model``– Jeden z profilů ukládání dat do mezipaměti popsaný v části [Výběr modelu použití](#choose-a-usage-model)výše.

  Ověřte názvy modelů použití pomocí příkazu [AZ HPC-cache Usage-model list](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

* ``--junction``– Parametr spojení propojuje cestu k virtuálnímu souboru pro klienta s cestou exportu v systému úložiště.

  Cíl úložiště NFS může mít několik virtuálních cest, pokud každá cesta představuje jiný export nebo podadresář ve stejném systému úložiště. Vytvořte všechny cesty pro jeden systém úložiště na jednom cíli úložiště.

  [Cesty oboru názvů můžete kdykoli přidat a upravit](hpc-cache-edit-storage.md) v cíli úložiště.

  ``--junction``Parametr používá tyto hodnoty:

  * ``namespace-path``– Cesta k virtuálnímu souboru s přístupem klienta
  * ``nfs-export``– Export systému úložiště, který se má přidružit k cestě s přístupem klienta
  * ``target-path``(volitelné) – podadresář exportu (v případě potřeby)

  Příklad: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  Další informace o funkci virtuálního oboru názvů najdete v tématu [Konfigurace agregovaného oboru názvů](hpc-cache-namespace.md) .

Příklad příkazu:

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

Výstup:
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="next-steps"></a>Další kroky

Po vytvoření cílů úložiště Vezměte v úvahu jednu z těchto úloh:

* [Připojení služby Azure HPC Cache](hpc-cache-mount.md)
* [Přesun dat do služby Azure Blob Storage](hpc-cache-ingest.md)

Pokud potřebujete aktualizovat nastavení, můžete [cíl úložiště upravit](hpc-cache-edit-storage.md).
