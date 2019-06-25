---
title: Přehled řízení přístupu v Azure Data Lake Storage Gen2 | Dokumentace Microsoftu
description: Vysvětlení, jak funguje řízení přístupu v Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 72a72e385217178cb6afee237cc3a3e5c5d1248b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66751632"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Řízení přístupu v Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementuje model řízení přístupu, který podporuje řízení přístupu Azure na základě rolí (RBAC) a seznamy řízení přístupu POSIX jako (ACL). Tento článek shrnuje základy modelu řízení přístupu pro Data Lake Storage Gen2.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

RBAC využívá pro efektivní použití sady oprávnění k přiřazení rolí *objekty zabezpečení*. A *objektu zabezpečení* je objekt, který představuje uživatele, skupiny, instanční objekt nebo spravovanou identitu, která je definována v Azure Active Directory (AD), která žádá o přístup k prostředkům Azure.

Obvykle se tyto prostředky Azure, jsou přednastaveny omezené prostředky nejvyšší úrovně (například: Účty úložiště Azure). V případě služby Azure Storage a proto Azure Data Lake Storage Gen2 tento mechanismus bylo rozšířeno na kontejner prostředku (systém souborů).

Další postup přiřazování rolí pro objekty zabezpečení v rámci vašeho účtu úložiště najdete v tématu [udělit přístup k Azure data objektů blob a fronty pomocí RBAC na webu Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>Dopad přiřazení rolí na seznamy řízení přístupu na úrovni souborů a adresářů

Při použití přiřazení rolí pro RBAC je výkonný mechanismus pro řízení oprávnění k přístupu, je velmi hrubě jemněji mechanismus vzhledem k seznamy ACL. To se dá vyhodnotit za vyšší prioritu než seznamy ACL nejmenší členitosti pro RBAC je na úrovni systému souborů. Proto pokud přiřadíte roli objektu zabezpečení v rámci systému souborů, tento objekt zabezpečení má úroveň autorizace, které jsou spojené s touto rolí pro všechny adresáře a soubory v tomto systému souborů, bez ohledu na přiřazení seznamu ACL.

Když objektu zabezpečení je oprávnění RBAC dat prostřednictvím [předdefinovaná role](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues), nebo prostřednictvím vlastní roli, tato oprávnění jsou vyhodnoceny jako první při ověření požadavku. Pokud požadovaná operace je oprávnění RBAC přiřazení objektu zabezpečení autorizace se okamžitě vyřešit a další jsou provedeny kontroly seznamu ACL. Případně, pokud objekt zabezpečení nemá RBAC přiřazení nebo operace požadavku se neshoduje s přiřazená oprávnění, pak seznamu ACL se provádí kontroly určit, zda objekt zabezpečení autorizaci k provedení požadované operace.

> [!NOTE]
> Pokud objekt zabezpečení má přiřazenou přiřazení předdefinované role vlastník dat úložiště objektů Blob, pak se považuje za objekt zabezpečení *superuživatele* a udělí se mu úplný přístup ke všem operacím mutující, včetně nastavení Vlastník adresář nebo soubor, stejně jako seznamů ACL pro adresářů a souborů, u kterých nejsou vlastníka. Superuživatel přístup je jenom autorizované způsobem změnit vlastníka prostředku.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Sdílený klíč a sdíleného přístupového podpisu (SAS)

Azure Data Lake Storage Gen2 podporuje sdíleného klíče a SAS metody pro ověřování. Typické pro tyto metody ověřování je, že žádná identita je spojen s volajícím, a proto nejde provést ověřování na základě oprávnění instančního objektu zabezpečení.

V případě sdíleného klíče volající efektivně získá přístup 'superuživatel' to znamená úplný přístup ke všem operacím na všechny prostředky, včetně nastavení vlastníka a změna seznamů ACL.

Tokeny SAS zahrnout jako součást tokenu mechanismu povolených oprávnění. Oprávnění zahrnuté v tokenu SAS efektivně platí pro všechny autorizačních rozhodnutích, ale jsou prováděny žádné další kontroly seznamu ACL.

## <a name="access-control-lists-on-files-and-directories"></a>Seznamy řízení přístupu na soubory a adresáře

Objekt zabezpečení můžete přidružit úroveň přístupu pro soubory a adresáře. Tato přidružení jsou zachyceny v *seznam řízení přístupu (ACL)* . Jednotlivých souborů a adresářů v účtu úložiště má seznam řízení přístupu.

Pokud jste přiřadili roli k objektu zabezpečení na úrovni účtu úložiště, můžete použít seznamy řízení přístupu udělení že tohoto objektu zabezpečení přístupu k určité soubory a adresáře se zvýšenými oprávněními.

Nelze použít seznamy řízení přístupu k poskytování úrovně přístupu, která je nižší než úroveň poskytované přiřazení role. Například, pokud přiřadíte [Přispěvatel dat objektu Blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) role zabezpečení, nemůžete konzolu použít řízení přístupu v seznamech zabránit zápisu do adresáře takového objektu zabezpečení.

### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Sada souborů a adresářů oprávnění na úrovni pomocí seznamů řízení přístupu

Pokud chcete nastavit oprávnění na úrovni souborů a adresářů, najdete v článku některého z následujících článků:

|Pokud chcete tento nástroj použijte:    |Najdete v tomto článku:    |
|--------|-----------|
|Azure Storage Explorer    |[Nastavení souborů a adresářů úrovně oprávnění pomocí Průzkumníka služby Azure Storage s Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)|
|REST API    |[Cesta - Update](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Pokud je objekt zabezpečení *služby* instančního objektu, je potřeba použít ID objektu instanční objekt a není ID objektu aplikace související se registrace. K získání ID objektu instančního objektu otevřete rozhraní příkazového řádku Azure a pak použijte tento příkaz: `az ad sp show --id <Your App ID> --query objectId`. Nezapomeňte nahradit `<Your App ID>` zástupný prvek s ID aplikace, u registrace vaší aplikace.

### <a name="types-of-access-control-lists"></a>Typy seznamů řízení přístupu

Existují dva druhy seznamy řízení přístupu: *přístupové seznamy ACL* a *výchozí seznamy ACL*.

Přístupové seznamy ACL řízení přístupu k objektu. Soubory a adresáře měli seznamy ACL.

Výchozí seznamy ACL jsou šablony seznamy ACL asociovaná s adresářem, které určují přístup seznamy ACL pro všechny podřízené položky, které jsou vytvořené v rámci tohoto adresáře. Výchozí seznamy ACL pro soubory není definován.

Přístupové seznamy ACL i výchozí seznamy ACL mají stejnou strukturu.

> [!NOTE]
> Změna výchozího seznamu ACL u nadřazené položky nemá vliv na přístup k seznamu ACL nebo výchozí seznam ACL podřízené položky, které již existují.

### <a name="levels-of-permission"></a>Úrovně oprávnění

Oprávnění pro objekt systému souborů jsou **čtení**, **zápisu**, a **Execute**, a jejich pak lze použít na soubory a adresáře, jak je znázorněno v následující tabulce:

|            |    File     |   Adresář |
|------------|-------------|----------|
| **Číst (R)** | Může číst obsah souboru | Vyžaduje **čtení** a **Execute** k výpisu obsahu adresáře |
| **Zapisovat (W)** | Může zapisovat do souboru nebo k němu připojovat data | Vyžaduje **zápisu** a **Execute** pro vytváření podřízených položek v adresáři |
| **Provést (X)** | Nemá žádný význam v kontextu Data Lake Storage Gen2 | Je vyžadováno k procházení podřízenými položkami adresáře |

> [!NOTE]
> Pokud udělujete oprávnění jenom pomocí seznamů ACL (žádné RBAC), pak udělit službu objektu zabezpečení pro čtení nebo zápis do souboru, bude potřeba poskytnout instanční objekt služby **Execute** oprávnění systému souborů a všechny složky v hierarchie složek, které vedou k souboru.

#### <a name="short-forms-for-permissions"></a>Zkrácené verze oprávnění

Zápis **RWX** se používá k označení **Číst + Zapisovat + Provést**. Používá se i zhuštěná číselná verze, která využívá nahrazení **Číst = 4**, **Zapisovat = 2** a **Provést = 1**, přičemž oprávnění je vyjádřeno součtem. Dále je uvedeno několik příkladů.

| Číselný tvar | Krátký tvar |      Význam     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Číst + Zapisovat + Provést |
| 5            | `R-X`        | Číst + Provést         |
| 4            | `R--`        | Čtení                   |
| 0            | `---`        | Žádná oprávnění         |

#### <a name="permissions-inheritance"></a>Dědičnost oprávnění

V modelu stylu POSIX, který používá Data Lake Storage Gen2 jsou oprávnění pro položku uložena přímo s příslušnou položkou. Jinými slovy oprávnění pro položku nelze zdědit z nadřazené položky, pokud jsou oprávnění po již vytvoření podřízené položky. Oprávnění se dědí, pouze pokud výchozí oprávnění jsou nastavené na nadřazené položky, před podřízenými položkami byly vytvořeny.

### <a name="common-scenarios-related-to-permissions"></a>Běžné scénáře týkající se oprávnění

V následující tabulce jsou uvedeny některé obvyklé scénáře, které vám pomohou pochopit, jaká oprávnění jsou nutná k provádění určitých operací s účtem úložiště.

|    Operace             |    /    | Liberecký kraj / | Portland / | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Přečtěte si Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Připojit k Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Odstranit Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Vytvoření Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| List /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Seznam /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Seznam /Oregon/Portland /  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Zapsat oprávnění k souboru není nutné ji odstranit, dokud předchozí dvě podmínky jsou splněny.

### <a name="users-and-identities"></a>Uživatelé a identity

Každý soubor a adresář má samostatná oprávnění pro tyto identity:

- Vlastnící uživatel
- Vlastnící skupina
- Pojmenovaní uživatelé
- Pojmenované skupiny
- Pojmenované instančních objektů
- S názvem spravované identity
- Všichni ostatní uživatelé

Identity uživatelů a skupin jsou identity Azure Active Directory (Azure AD). Ano, pokud není uvedeno jinak, *uživatele*, v souvislosti s Data Lake Storage Gen2 se může odkazovat na uživatele služby Azure AD, služba instančního objektu a spravovanou identitu nebo skupinu zabezpečení.

#### <a name="the-owning-user"></a>Vlastnící uživatel

Uživatel, který položku vytvořil, je automaticky jejím vlastníkem. Vlastnící uživatel může:

* Měnit oprávnění souboru, jehož vlastníkem je.
* Měnit vlastnící skupinu pro vlastněný soubor, pokud je vlastnící uživatel členem cílové skupiny.

> [!NOTE]
> Vlastnící uživatel *nelze* měnit vlastnícího uživatele souboru nebo adresáře. Pouze Superuživatelé může měnit vlastnícího uživatele souboru nebo adresáře.

#### <a name="the-owning-group"></a>Vlastnící skupina

V seznamech ACL POSIX je přidružený každý uživatel *primární skupina*. Uživatel "Alice" může například patřit do skupiny "finance". Alice může patřit do několika skupin, ale jedna skupina je vždy určena jako svou primární skupinu. Když Alice vytvoří soubor v rámci specifikace POSIX, bude jako vlastnící skupina tohoto souboru nastavena její primární skupina, což je v tomto případě skupina „finance“. Jinak se vlastnící skupina chová podobně jako přiřazená oprávnění pro jiné uživatele nebo skupiny.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Přiřazuje se vlastnící skupina pro nový soubor nebo adresář

* **Případ 1**: Kořenový adresář "/". Tento adresář se vytvoří při vytvoření systémem souborů Data Lake Storage Gen2. V takovém případě je vlastnící skupina nastavena pro uživatele, který vytvořil systému souborů, pokud bylo provedeno pomocí metody OAuth. Pokud systém souborů je vytvořen pomocí sdíleného klíče, SAS účtu nebo SAS služby, pak vlastníka a vlastnící skupina nastavena **$superuser**.
* **Případ 2** (všechny ostatní případy): Při vytvoření nové položky se vlastnící skupina je zkopírován z adresáře nadřazené.

##### <a name="changing-the-owning-group"></a>Mění se vlastnící skupina

Vlastnící skupinu smí změnit:
* Všichni superuživatelé.
* Vlastnící uživatel, pokud je také členem cílové skupiny.

> [!NOTE]
> Vlastnící skupina nelze měnit přístupové seznamy souboru nebo adresáře.  Zatímco je vlastnící skupina nastavená na uživatele, který vytvořil účet v případě kořenového adresáře **případ 1** výše, jedním uživatelským účtem není možné poskytnout oprávnění prostřednictvím vlastnící skupiny. Toto oprávnění můžete přiřadit platné skupině uživatelů, pokud nějaká existuje.

### <a name="access-check-algorithm"></a>Algoritmus kontroly přístupu

Následujícím pseudokódu představuje algoritmu kontroly přístupu pro účty úložiště.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask isn't used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

# Handle named groups and owning group
member_count = 0
perms = 0
entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    member_count += 1
    perms | =  entry.permissions
if (member_count>0) :
return ((desired_perms & perms & mask ) == desired_perms)

# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

#### <a name="the-mask"></a>Maska

Jak je znázorněno v algoritmu kontroly přístupu, maska omezuje přístup pro pojmenovaného uživatele, vlastnící skupinu a pojmenované skupiny.  

> [!NOTE]
> Pro nový systém souborů Data Lake Storage Gen2 je použita výchozí maska pro přístup k seznamu ACL kořenové adresáře ("/") a 750 pro adresáře 640 pro soubory. Soubory nebudou přijímat X bit je závislé na soubory v systému jenom pro úložiště.
>
> Maska může být zadáno na jednotlivá volání. To umožňuje různé využívání systémů, například clustery mít různé efektivní masky pro jejich operací se soubory. Pokud maska není zadána na danou žádost, zcela přepisuje výchozí masku.

#### <a name="the-sticky-bit"></a>Bit sticky

Sticky bit představuje pokročilejší funkci systému souborů POSIX. V kontextu Data Lake Storage Gen2 nepravděpodobné, že bude potřeba sticky bit. Stručně řečeno pokud je sticky bit povolené v adresáři, podřízené položky můžete pouze smazány nebo přejmenovány vlastnící uživatel podřízenou položku.

Sticky bit se nezobrazí na portálu Azure portal.

### <a name="default-permissions-on-new-files-and-directories"></a>Výchozí oprávnění pro nové soubory a adresáře

Když se v rámci stávajícího adresáře nový soubor nebo adresář, výchozí seznam ACL v nadřazeném adresáři určuje:

- Výchozí seznam ACL a přístup k seznamu ACL podřízené adresáře.
- Přístup seznam ACL podřízeného souboru (soubory není definován výchozí seznam ACL).

#### <a name="umask"></a>Vlastnost umask

Při vytváření souboru nebo adresáři, vlastnost umask se používá k úpravě nastavení výchozí seznamy ACL na podřízené položky. Vlastnost umask je 9bitové hodnoty na nadřazené adresáře, které obsahuje hodnota RWX pro **vlastnícího uživatele**, **vlastnící skupinu**, a **jiných**.

Vlastnost umask pro Azure Data Lake Storage Gen2 konstantní hodnoty, který je nastaven na 007. Tato hodnota se přeloží na:

| Vlastnost umask komponenty     | Číselný tvar | Krátký tvar | Význam |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Pro vlastnícího uživatele, zkopírujte výchozí seznam ACL nadřazené položky pro přístup k prvku seznamu ACL | 
| umask.owning_group  |    0         |   `---`      | Pro vlastnící skupinu, zkopírujte výchozí seznam ACL nadřazené položky pro přístup k prvku seznamu ACL | 
| umask.Other         |    7         |   `RWX`      | Pro ostatní odeberte všechna oprávnění k přístupu podřízeného seznamu ACL |

Hodnota umask efektivně používat Azure Data Lake Storage Gen2 znamená, že hodnota **jiných** se nikdy nepřenáší ve výchozím nastavení na nové podřízené položky bez ohledu na to, jaké výchozí seznam ACL označuje. 

Následujícím pseudokódu ukazuje, jak vlastnost umask se použije při vytváření seznamů ACL pro podřízené položky.

```
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Běžné dotazy týkající se seznamů ACL v Data Lake Storage Gen2

### <a name="do-i-have-to-enable-support-for-acls"></a>Je třeba povolit podporu pro seznamy ACL?

Ne. Řízení přístupu prostřednictvím seznamů ACL je povolené pro účet úložiště tak dlouho, dokud hierarchická Namespace (HNS) funkce je zapnuté.

Pokud HNS je vypnutý, spustí se autorizační pravidla Azure RBAC stále platit.

### <a name="what-is-the-best-way-to-apply-acls"></a>Co je nejlepší způsob, jak použít seznamy ACL?

Vždy používejte skupiny zabezpečení Azure AD jako přiřazené instanční objekt v seznamech ACL. Odolat moci přiřadit přímo jednotlivé uživatele nebo instanční objekty. Pomocí této struktury vám umožní přidávat a odebírat uživatele nebo instanční objekty bez nutnosti znovu použít seznamy ACL pro celý adresářovou strukturu. ) Místo toho stačí jednoduše přidat nebo odebrat z příslušné skupiny zabezpečení Azure AD. Mějte na paměti, že seznamy ACL nejsou děděna a proto obnovení seznamů řízení přístupu vyžaduje aktualizaci jejich seznamu ACL u všech souborů a podadresáře. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Jaká oprávnění jsou vyžadována pro rekurzivní odstranění adresářů a jejího obsahu?

- Volající, má oprávnění k 'superuživatel.

Nebo

- Nadřazený adresář musí mít nastavení Write + oprávnění ke spouštění.
- Adresář, který chcete odstranit a každý adresář obsahuje, vyžaduje číst + zapisovat + provést oprávnění.

> [!NOTE]
> Budete potřebovat Nezapisovat oprávnění k odstranění souborů v adresářích. Také, kořenový adresář "/" nikdy dají odstranit.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Kdo je vlastníkem souboru nebo adresáře?

Tvůrce soubor nebo adresář stává vlastníkem. V případě kořenového adresáře jde o identitu uživatele, který vytvořil systému souborů.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Jaká skupina je nastavena jako vlastnící skupina souboru nebo adresáře při vytvoření?

Vlastnící skupina zkopíruje z vlastnící skupiny nadřazené adresáře, ve kterém se vytvoří nový soubor nebo adresář.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Jsem vlastnícím uživatelem souboru, ale nemám potřebné oprávnění RWX. Co mám udělat?

Vlastnící uživatel může změnit oprávnění k souboru a sám si udělit veškerá potřebná oprávnění RWX.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Proč se někdy zobrazují identifikátory GUID v seznamech ACL?

Identifikátor GUID se zobrazí, pokud položka představuje uživatele a tohoto uživatele v Azure AD už neexistuje. K tomu obvykle dochází, když uživatel opustí společnost nebo když je jeho účet odstraněn ve službě Azure AD. Kromě toho instanční objekty a skupiny zabezpečení nemají hlavní název uživatele (UPN) jejich identifikaci a proto jsou reprezentované prostřednictvím jejich atribut OID (guid).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Jak nastavit seznamy ACL správně pro službu objektu zabezpečení?

Při definování seznamů ACL pro instanční objekty, je potřeba použít ID objektu (OID) z *instanční objekt služby* pro registraci aplikace, kterou jste vytvořili. Je důležité si uvědomit, že registrovaných aplikací mají samostatné instančního objektu v konkrétní tenanta Azure AD. Registrovaná aplikace mají OID, který se zobrazuje na webu Azure portal, ale *instanční objekt služby* má jiný identifikátor objektu (různé).

Chcete-li získat identifikátor OID pro instanční objekt, který odpovídá registrace aplikace, můžete použít `az ad sp show` příkazu. Zadejte ID aplikace jako parametr. Tady je příklad týkající se získání identifikátor OID pro instanční objekt, který odpovídá registrace aplikace s ID aplikace = 18218b12 1895-43e9-ad80-6e8fc1ea88ce. V Azure CLI, spusťte následující příkaz:

`az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
<<OID will be displayed>>`

Až budete mít správný identifikátor objektu k objektu služby, přejděte do Průzkumníka úložišť **spravovat přístup** stránku a přidejte Identifikátor objektu a přiřaďte odpovídající oprávnění pro Identifikátor objektu. Ujistěte se, že vyberete **Uložit**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Podporuje Data Lake Storage Gen2 dědění seznamů ACL?

Azure RBAC přiřazení dědí. Přiřazení tok z předplatného, skupinu prostředků a prostředků účtu úložiště do souboru Systémový prostředek.

Seznamy ACL nedědí. Výchozí seznamy ACL je však možné nastavit seznamy ACL pro podřízené podadresáře a soubory vytvořené v rámci nadřazeného adresáře. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Kde najdu další informace o modelu řízení přístupu POSIX?

* [POSIX Access Control Lists on Linux (Seznamy řízení přístupu v rámci specifikace POSIX v Linuxu)](https://www.linux.com/news/posix-acls-linux)
* [HDFS Permissions Guide (Průvodce oprávněními v HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [Nejčastější dotazy týkající se specifikace POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL na Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL: Using Access Control Lists on Linux (Seznamy ACL: Používání seznamů řízení přístupu v Linuxu)](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Další informace najdete v tématech

* [Přehled služby Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)
