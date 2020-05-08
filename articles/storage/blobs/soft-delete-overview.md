---
title: Obnovitelné odstranění pro úložiště objektů BLOB
titleSuffix: Azure Storage
description: Povolit obnovitelné odstranění objektů blob, abyste mohli snadněji obnovit data v případě, že je omylem změnil nebo odstranila aplikace nebo jiný uživatel účtu úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: dd5d9c721c3e0204a66367b76654f9a917e26ba6
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2020
ms.locfileid: "82884628"
---
# <a name="soft-delete-for-blob-storage"></a>Obnovitelné odstranění pro úložiště objektů BLOB

Obnovitelné odstranění: Ochrana dat objektů BLOB je náhodná nebo nesprávně upravená nebo Odstraněná. Když je pro účet úložiště povolené obnovitelné odstranění, objekty blob, verze objektů BLOB (Preview) a snímky v tomto účtu úložiště se po jejich odstranění můžou obnovit v době uchování, kterou zadáte.

Pokud existuje možnost, že by vaše data mohla být omylem upravována nebo odstraněna aplikací nebo jiným uživatelem účtu úložiště, společnost Microsoft doporučuje zapnout obnovitelné odstranění.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-soft-delete"></a>O obnovitelném odstranění

Když je v účtu úložiště povolené obnovitelné odstranění, můžete objekty po odstranění obnovit v rámci zadané doby uchovávání dat. Tato ochrana se rozšiřuje na všechna data objektů BLOB (objekty blob bloku, doplňovací objekty BLOB a objekty blob stránky), která se vymažou jako výsledek přepsání.

Pokud jsou data v existujícím objektu BLOB nebo snímku smazána, ale je povolené obnovitelné odstranění, ale Správa verzí objektů BLOB (Preview) není povolená, pak se vygeneruje měkký odstraněný snímek, který uloží stav přepsaných dat. Po uplynutí zadané doby uchování dojde k trvalému odstranění objektu.

Pokud je v účtu úložiště zapnutá Správa verzí a obnovitelné odstranění objektů blob, pak odstranění objektu BLOB vytvoří novou verzi místo snímku, který je odstraněný jako měkký. Nová verze není Odstraněná a při vypršení doby uchování dočasného odstranění se neodebere. Obnovitelné odstraněné verze objektu BLOB se dají obnovit v rámci doby uchovávání voláním operace [obnovení objektu BLOB](/rest/api/storageservices/undelete-blob) . Objekt BLOB lze následně obnovit z jedné z jeho verzí voláním operace [kopírování objektu BLOB](/rest/api/storageservices/copy-blob) . Další informace o použití správy verzí objektů BLOB a obnovitelného odstranění najdete v tématu [Správa verzí objektů BLOB a obnovitelné odstranění](versioning-overview.md#blob-versioning-and-soft-delete).

Měkké odstraněné objekty jsou neviditelné, pokud nejsou výslovně uvedeny.

Obnovitelné odstranění je zpětně kompatibilní, takže nemusíte dělat žádné změny v aplikacích, abyste mohli využít výhod ochrany, kterou tato funkce nabízí. [Obnovení dat](#recovery) ale zavádí nové **neodstranitelné rozhraní API objektů BLOB** .

Obnovitelné odstranění je k dispozici pro nové i existující účty úložiště pro obecné účely v2, obecné účely V1 a BLOB Storage. Podporují se účty typu Standard a Premium. Obnovitelné odstranění je k dispozici pro všechny úrovně úložiště, včetně horké, studené a archivní. Obnovitelné odstranění je k dispozici pro nespravované disky, které jsou objekty blob stránky v rámci pokrývání, ale nejsou k dispozici pro spravované disky.

### <a name="configuration-settings"></a>Nastavení konfigurace

Když vytváříte nový účet, bude ve výchozím nastavení zakázané obnovitelné odstranění. U stávajících účtů úložiště je ve výchozím nastavení také zakázáno obnovitelné odstranění. Můžete kdykoli povolit nebo zakázat obnovitelné odstranění účtu úložiště.

Pokud povolíte obnovitelné odstranění, musíte nakonfigurovat dobu uchování. Doba uchování označuje dobu, po kterou jsou data Odstraněná po uložení a dostupná pro obnovení. Pro objekty, které jsou explicitně odstraněny, se čas doby uchování spustí při odstranění dat. Pro tiché odstraněné verze nebo snímky vygenerované funkcí tichého odstranění při přepisování dat se čas spustí při vygenerování verze nebo snímku. Doba uchovávání dat může být mezi 1 a 365 dny.

Dobu uchování obnovitelného odstranění můžete kdykoli změnit. Aktualizovaná doba uchování se vztahuje pouze na nově Odstraněná data. Vyprší platnost dříve odstraněných dat na základě doby uchování, která byla konfigurována při odstranění těchto dat. Pokus o odstranění nepodmíněného odstraněného objektu nemá vliv na čas jeho vypršení platnosti.

Pokud obnovitelné odstranění zakážete, můžete v účtu úložiště, který byl uložen v době, kdy byla funkce povolená, dál přistupovat a obnovovat tichá Odstraněná data.

### <a name="saving-deleted-data"></a>Ukládání odstraněných dat

Obnovitelné odstranění zachová data v mnoha případech, kde jsou objekty smazány nebo přepsány.

Když je objekt BLOB přepsaný pomocí **objektu Put BLOB**, **seznamu blokovaných**objektů nebo **kopie objektu**blob, verze nebo snímek stavu objektu BLOB před operací zápisu se automaticky vygeneruje. Tento objekt je neviditelný, pokud nejsou explicitně odstraněné objekty výslovně uvedeny. V části věnované [obnovení](#recovery) se dozvíte, jak zobrazit seznam neodstraněných objektů.

![](media/soft-delete-overview/storage-blob-soft-delete-overwrite.png)

*Měkké Odstraněná data jsou šedá, zatímco aktivní data jsou modrá. Další data napsaná v poslední době se zobrazí pod staršími daty. Když se B0 přepíše pomocí B1, vygeneruje se měkký odstraněný snímek B0. Když je B1 přepsána v B2, je vygenerována měkký odstraněný snímek B1.*

> [!NOTE]  
> Obnovitelné odstranění nabízí pouze přepis ochrany při operacích kopírování, pokud je zapnutý pro účet cílového objektu BLOB.

> [!NOTE]  
> Obnovitelné odstranění neumožňuje přepsat ochranu objektů BLOB v archivní úrovni. Pokud je objekt BLOB v archivu přepsaný novým objektem blob na jakékoli úrovni, přepsanému objektu BLOB se trvale vyprší platnost.

Při volání metody **Delete BLOB** pro snímek je tento snímek označený jako měkký odstraněný. Nový snímek se nevygeneruje.

![](media/soft-delete-overview/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Měkké Odstraněná data jsou šedá, zatímco aktivní data jsou modrá. Další data napsaná v poslední době se zobrazí pod staršími daty. Když se zavolá **objekt BLOB snímku** , B0 se vytvoří jako snímek a B1 je aktivním stavem objektu BLOB. Po odstranění snímku B0 je označen jako měkký odstraněný.*

Když se **objekt BLOB pro odstranění** volá u základního objektu BLOB (jakýkoli objekt blob, který není sám snímkem), tento objekt BLOB je označený jako měkký odstraněný. V souladu s předchozím chováním volání funkce **Odstranit objekt BLOB** u objektu blob, který má aktivní snímky, vrátí chybu. Volání metody **Delete BLOB** u objektu BLOB s nejemnými odstraněnou snímků nevrátí chybu. Můžete přesto odstranit objekt BLOB a všechny jeho snímky v rámci jedné operace, když je zapnuté obnovitelné odstranění. Tím se vyznačuje základní objekt BLOB a snímky jako obnovitelné.

![](media/soft-delete-overview/storage-blob-soft-delete-explicit-include.png)

*Měkké Odstraněná data jsou šedá, zatímco aktivní data jsou modrá. Další data napsaná v poslední době se zobrazí pod staršími daty. Zde je provedeno volání **objektu BLOB Delete** pro odstranění B2 a všech přidružených snímků. Aktivní objekt blob, B2 a všechny přidružené snímky jsou označené jako měkké odstraněné.*

> [!NOTE]  
> Po přepsání nepodmíněného odstraněného objektu BLOB se automaticky vygeneruje měkký odstraněný snímek stavu objektu BLOB před operací zápisu. Nový objekt BLOB zdědí úroveň přepsaného objektu BLOB.

Obnovitelné odstranění neukládá vaše data v případě, že dojde k odstranění kontejneru nebo účtu, ani když jsou metadata objektů BLOB a vlastnosti objektů BLOB přepsána. K ochraně účtu úložiště před chybným odstraněním můžete nakonfigurovat zámek pomocí Azure Resource Manager. Další informace o tom, [Jak zabránit neočekávaným změnám](../../azure-resource-manager/management/lock-resources.md) , najdete v článku o Azure Resource Manager uzamčení prostředků.

Následující tabulka podrobně popisuje očekávané chování při zapnutí obnovitelného odstranění:

| Operace REST API | Typ prostředku | Popis | Změna chování |
|--------------------|---------------|-------------|--------------------|
| [Odstranit](/rest/api/storagerp/StorageAccounts/Delete) | Účet | Odstraní účet úložiště, včetně všech kontejnerů a objektů blob, které obsahuje.                           | Žádná změna. Kontejnery a objekty BLOB v odstraněném účtu nejde obnovit. |
| [Odstranění kontejneru](/rest/api/storageservices/delete-container) | Kontejner | Odstraní kontejner včetně všech objektů blob, které obsahuje. | Žádná změna. Objekty BLOB v odstraněném kontejneru nejsou obnovitelné. |
| [Vložení objektu blob](/rest/api/storageservices/put-blob) | Blokování, připojení a objekty blob stránky | Vytvoří nový objekt BLOB nebo nahradí existující objekt BLOB v rámci kontejneru. | Pokud se použije k nahrazení existujícího objektu blob, automaticky se vygeneruje snímek stavu objektu BLOB před voláním. To platí také pro dříve odstraněné objekty blob, pokud jsou nahrazeny objektem BLOB stejného typu (blok, připojit nebo stránka). Pokud je nahrazen objektem BLOB jiného typu, všechna existující dočasná Odstraněná data budou trvale vypršet. |
| [Odstranění objektu blob](/rest/api/storageservices/delete-blob) | Blokování, připojení a objekty blob stránky | Označí objekt BLOB nebo snímek objektu BLOB pro odstranění. Objekt BLOB nebo snímek se později odstraní během uvolňování paměti. | Pokud se použije k odstranění snímku objektu blob, bude tento snímek označený jako měkký odstraněný. Při použití k odstranění objektu BLOB je tento objekt BLOB označený jako měkký odstraněný. |
| [Zkopírování objektu blob](/rest/api/storageservices/copy-blob) | Blokování, připojení a objekty blob stránky | Zkopíruje zdrojový objekt blob do cílového objektu BLOB ve stejném účtu úložiště nebo v jiném účtu úložiště. | Pokud se použije k nahrazení existujícího objektu blob, automaticky se vygeneruje snímek stavu objektu BLOB před voláním. To platí také pro dříve odstraněné objekty blob, pokud jsou nahrazeny objektem BLOB stejného typu (blok, připojit nebo stránka). Pokud je nahrazen objektem BLOB jiného typu, všechna existující dočasná Odstraněná data budou trvale vypršet. |
| [Blok vložení](/rest/api/storageservices/put-block) | Objekty blob bloku | Vytvoří nový blok, který bude potvrzen jako součást objektu blob bloku. | Pokud se použije k potvrzení bloku do objektu blob, který je aktivní, nedošlo k žádné změně. Pokud se použije k potvrzení bloku do objektu blob, který se dočasná odstraní, vytvoří se nový objekt BLOB a automaticky se vygeneruje snímek, který zachytí stav podmíněného odstraněného objektu BLOB. |
| [Seznam blokovaných umístění](/rest/api/storageservices/put-block-list) | Objekty blob bloku | Potvrdí objekt BLOB zadáním sady identifikátorů ID bloků, které tvoří objekt blob bloku. | Pokud se použije k nahrazení existujícího objektu blob, automaticky se vygeneruje snímek stavu objektu BLOB před voláním. To platí i pro dřív odstraněný objekt blob, pokud je a jenom v případě, že se jedná o objekt blob bloku. Pokud je nahrazen objektem BLOB jiného typu, všechna existující dočasná Odstraněná data budou trvale vypršet. |
| [Vložit stránku](/rest/api/storageservices/put-page) | Objekty blob stránky | Zapisuje rozsah stránek do objektu blob stránky. | Žádná změna. Data objektu blob stránky, která jsou přepsána nebo vymazána pomocí této operace, nejsou uložena a nelze je obnovit. |
| [Připojit blok](/rest/api/storageservices/append-block) | Doplňovací objekty blob | Zapisuje blok dat na konec doplňovacího objektu BLOB. | Žádná změna. |
| [Nastavení vlastností objektu BLOB](/rest/api/storageservices/set-blob-properties) | Blokování, připojení a objekty blob stránky | Nastaví hodnoty vlastností systému definované pro objekt BLOB. | Žádná změna. Přepsané vlastnosti objektu BLOB nelze obnovit. |
| [Nastavení metadat objektu BLOB](/rest/api/storageservices/set-blob-metadata) | Blokování, připojení a objekty blob stránky | Nastaví uživatelsky definovaná metadata pro zadaný objekt BLOB jako jednu nebo více párů název-hodnota. | Žádná změna. Přepsaná metadata objektu BLOB nelze obnovit. |

Je důležité si všimnout, že volající **Stránka Put** pro přepsání nebo vymazání rozsahů objektu blob stránky nebude automaticky generovat snímky. Disky virtuálních počítačů se zálohují objekty blob stránky a používají **stránku Put** k zápisu dat.

### <a name="recovery"></a>Obnovení

Volání operace obnovení [objektu BLOB](/rest/api/storageservices/undelete-blob) na neodstraněném základním objektu BLOB obnoví a všechny přidružené měkké odstraněné snímky jako aktivní. Volání operace **obnovení objektu BLOB** na aktivním základním objektu BLOB obnoví všechny přidružené měkké odstraněné snímky jako aktivní. Když se snímky obnovují jako aktivní, vypadají jako uživatelem generované snímky; nepřepisují základní objekt BLOB.

Chcete-li obnovit objekt blob do konkrétního neodstraněného snímku, můžete volat možnost **zrušit odstranění objektu BLOB** u základního objektu BLOB. Pak můžete snímek zkopírovat přes objekt BLOB nyní – aktivní. Snímek můžete také zkopírovat do nového objektu BLOB.

![](media/soft-delete-overview/storage-blob-soft-delete-recover.png)

*Měkké Odstraněná data jsou šedá, zatímco aktivní data jsou modrá. Další data napsaná v poslední době se zobrazí pod staršími daty. V tomto případě se v objektu BLOB B volá příkaz **Undelete BLOB** , který obnovuje základní objekt blob, B1 a všechny přidružené snímky, a to jenom B0 jako aktivní. V druhém kroku se B0 zkopíruje přes základní objekt BLOB. Tato operace kopírování vygeneruje měkký odstraněný snímek B1.*

Pokud chcete zobrazit nepodmíněné odstraněné objekty BLOB a snímky objektů blob, můžete se rozhodnout zahrnout Odstraněná data do **seznamu objektů BLOB**. Můžete si vybrat, že se mají zobrazovat jenom obnovitelné odstraněné základní objekty blob, nebo taky zahrnout obnovitelné odstraněné snímky objektů BLOB. Pro všechna tichá Odstraněná data můžete zobrazit čas odstranění dat a počet dní, než budou data trvale vypršet.

### <a name="example"></a>Příklad

Následuje výstup konzoly skriptu .NET, který nahrává, Přepisuje, snímuje, odstraňuje a obnovuje objekt BLOB s názvem *Hello* , pokud je obnovitelné odstranění zapnuté:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

V části [Další kroky](#next-steps) najdete ukazatel na aplikaci, která vytvořila tento výstup.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Všechna tichá Odstraněná data se účtují stejnou sazbou jako aktivní data. Neúčtují se vám poplatky za data, která se po nakonfigurované době uchování trvale odstraní. Pro hlubší podrobně na snímky a jejich navýšení poplatků vám [pomůžou pochopit, jak se na snímcích účtují poplatky](storage-blob-snapshots.md).

Neúčtují se vám transakce týkající se automatického generování snímků. V případě operací zápisu se vám budou účtovat transakce **neodstraněného objektu BLOB** .

Další podrobnosti o cenách pro Azure Blob Storage obecně najdete na stránce s cenami za [azure BLOB Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Když zpočátku zapnete obnovitelné odstranění, společnost Microsoft doporučuje používat krátkou dobu uchovávání, aby lépe porozuměla tomu, jak bude tato funkce mít na vyúčtování vliv.

Povolení obnovitelného odstranění často přepsaných dat může mít za následek zvýšené poplatky za kapacitu úložiště a vyšší latenci při výpisu objektů BLOB. Tyto dodatečné náklady a latence můžete zmírnit uložením často přepsaných dat do samostatného účtu úložiště, kde je deaktivované obnovitelné odstranění.

## <a name="faq"></a>Nejčastější dotazy

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Můžu použít nastavení rozhraní API vrstev objektů BLOB pro objekty blob vrstvy se měkkými odstraněnou snímků?

Ano. Měkké odstraněné snímky zůstanou v původní úrovni, ale základní objekt BLOB se přesune na novou úroveň.

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Účty úložiště úrovně Premium mají omezení počtu snímků objektů blob na 100. Počítá se z tohoto limitu měkké odstraněné snímky?

Ne, měkké odstraněné snímky se nepočítají směrem k tomuto limitu.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Když odstraním celý účet nebo kontejner se zapnutým obnovitelném odstraněním, budou se všechny přidružené objekty blob ukládat?

Ne. Pokud odstraníte celý účet nebo kontejner, všechny přidružené objekty BLOB se trvale odstraní. Další informace o ochraně účtu úložiště před náhodným odstraněním najdete v tématu [uzamčení prostředků, aby se zabránilo neočekávaným změnám](../../azure-resource-manager/management/lock-resources.md).

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Můžu zobrazit metriky kapacity pro Odstraněná data?

Dočasná Odstraněná data jsou součástí celkové kapacity účtu úložiště. Další informace o sledování a sledování kapacity úložiště najdete v tématu [Analýza úložiště](../common/storage-analytics.md).

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Můžu číst a kopírovat obnovitelné odstraněné snímky objektu BLOB?  

Ano, ale v objektu BLOB musíte nejdřív zavolat příkaz Undelete.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>Je obnovitelné odstranění k dispozici pro disky virtuálních počítačů?  

Obnovitelné odstranění je k dispozici pro nespravované disky úrovně Premium i Standard, což jsou objekty blob stránky v rámci pokrývání. Obnovitelné odstranění vám pomůže jenom obnovit data odstraněná **odstraněním objektů BLOB**, **vložením objektů BLOB**, **vložením seznamu blokovaných** **objektů a kopírováním operací blobů** . Data přepsaná voláním **stránky Put** nelze obnovit.

Virtuální počítač Azure se zapisuje na nespravovaný disk pomocí volání na **stránku Put**, takže pomocí obnovitelného odstranění vrátíte zpátky zápisy na nespravovaný disk z virtuálního počítače Azure, není podporovaný scénář.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Potřebuji změnit moje existující aplikace, aby používaly obnovitelné odstranění?

Je možné využít obnovitelné odstranění bez ohledu na verzi rozhraní API, kterou používáte. Pokud ale chcete vypsat a obnovit obnovitelné odstraněné objekty BLOB a snímky objektů blob, budete muset použít verzi 2017-07-29 [Azure Storage REST API](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) nebo vyšší. Microsoft doporučuje vždy používat nejnovější verzi rozhraní Azure Storage API.

## <a name="next-steps"></a>Další kroky

- [Povolit obnovitelné odstranění pro objekty blob](soft-delete-enable.md)
- [Správa verzí objektů BLOB (Preview)](versioning-overview.md)
