---
title: Šifrování v Azure Data Lake Storage Gen1 | Dokumentace Microsoftu
description: Šifrování v Azure Data Lake Storage Gen1 pomáhá chránit vaše data, implementovat zásady podnikového zabezpečení a splňovat požadavky na dodržování legislativních předpisů. Tento článek poskytuje přehled návrhu a popisuje některé technické aspekty implementace.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: a009f212bd8baaa353d602dc6090aeeccddd4936
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58098130"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Šifrování dat v Azure Data Lake Storage Gen1

Šifrování v Azure Data Lake Storage Gen1 pomáhá chránit vaše data, implementovat zásady podnikového zabezpečení a splňovat požadavky na dodržování legislativních předpisů. Tento článek poskytuje přehled návrhu a popisuje některé technické aspekty implementace.

Data Lake Storage Gen1 podporuje šifrování dat v klidovém stavu i při přenosu. Pro neaktivní uložená data, Data Lake Storage Gen1 podporuje "na ve výchozím nastavení," transparentní šifrování. Tady je podrobnější vysvětlení významu těchto termínů:

* **Na ve výchozím nastavení**: Když vytvoříte nový účet Data Lake Storage Gen1, výchozí nastavení povolí šifrování. Následně data, která je uložená v Data Lake Storage Gen1 se budou šifrovat vždy před uložením na trvalé médium. Toto chování platí pro veškerá data a po vytvoření účtu nejde změnit.
* **Transparentní**: Data Lake Storage Gen1 automaticky šifruje data před uložením a dešifruje před načtením. Šifrování a je na úrovni účtu Data Lake Storage Gen1 spravuje správce. Rozhraní API pro přístup k datům se nemění. Díky tomu se nevyžaduje žádné změny aplikace a služby, které pracují s Data Lake Storage Gen1 kvůli šifrování.

V Data Lake Storage Gen1 se také vždy šifrují data při přenosu (označované také jako data přenášená data). Kromě šifrování dat před uložením na trvalé médium se také vždy šifrují přenášená data pomocí protokolu HTTPS. HTTPS je jediný protokol, který se podporuje v rozhraních Data Lake Storage Gen1 REST. Následující diagram znázorňuje, jak probíhá šifrování dat v Data Lake Storage Gen1:

![Diagram šifrování dat v Data Lake Storage Gen1](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Nastavení šifrování v Data Lake Storage Gen1

Šifrování pro Data Lake Storage Gen1 nastavuje během vytváření účtu a ve výchozím nastavení je vždy povoleno. Můžete spravovat klíče nebo povolit Data Lake Storage Gen1 a aby je spravovala za vás (to je výchozí hodnota).

Další informace najdete v tématu [Začínáme](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Jak funguje šifrování ve službě Data Lake Storage Gen1

Tyto informace se zaměřuje na správu hlavních šifrovacích klíčů, a vysvětluje tři různé typy klíčů, které můžete použít v šifrování dat pro Data Lake Storage Gen1.

### <a name="master-encryption-keys"></a>Hlavní šifrovací klíče

Data Lake Storage Gen1 nabízí dva režimy správy hlavních šifrovacích klíčů (MEK). Prozatím předpokládejme, že hlavní šifrovací klíč je klíč nejvyšší úrovně. K dešifrování jakýchkoli dat uložených v Data Lake Storage Gen1 se vyžaduje přístup k hlavnímu šifrovacímu klíči.

Pro správu hlavních šifrovacích klíčů existují tyto dva režimy:

*   Klíče spravované službou
*   Klíče spravované zákazníkem

V obou režimech je hlavní šifrovací klíč zabezpečen uložením ve službě Azure Key Vault. Key Vault je plně spravovaná, vysoce zabezpečená služba v Azure, kterou můžete použít k bezpečnému ukládání kryptografických klíčů. Další informace najdete v tématu [Key Vault](https://azure.microsoft.com/services/key-vault).

Tady je stručné porovnání možností, které nabízí dva režimy správy hlavních šifrovacích klíčů.

|  | Klíče spravované službou | Klíče spravované zákazníkem |
| --- | --- | --- |
|Jak se data ukládají?|Před uložením se vždy šifrují.|Před uložením se vždy šifrují.|
|Kde je uložený hlavní šifrovací klíč?|Key Vault|Key Vault|
|Jsou nějaké šifrovací klíče uložené v nezašifrované podobě mimo službu Key Vault? |Ne|Ne|
|Může služba Key Vault načíst hlavní šifrovací klíč?|Ne. Po uložení hlavního šifrovacího klíče ve službě Key Vault ho lze použít pouze k šifrování a dešifrování.|Ne. Po uložení hlavního šifrovacího klíče ve službě Key Vault ho lze použít pouze k šifrování a dešifrování.|
|Kdo je vlastníkem instance služby Key Vault a hlavního šifrovacího klíče?|Služba Data Lake Storage Gen1|Vlastníte instanci služby Key Vault, která patří do vašeho předplatného Azure. Hlavní šifrovací klíč ve službě Key Vault může být spravovaný softwarem nebo hardwarem.|
|Můžete odvolat přístup k hlavnímu šifrovacímu KLÍČI pro službu Data Lake Storage Gen1?|Ne|Ano. Můžete spravovat seznamy řízení přístupu ve službě Key Vault a odebrat záznamy řízení přístupu k identitě služby pro službu Data Lake Storage Gen1.|
|Je možné trvale odstranit hlavní šifrovací klíč?|Ne|Ano. Pokud odstraníte hlavní šifrovací klíč ze služby Key Vault, data v účtu Data Lake Storage Gen1 nelze dešifrovat kdokoli, včetně služby Data Lake Storage Gen1. <br><br> Pokud jste hlavní šifrovací klíč před odstraněním ze služby Key Vault explicitně zazálohovali, je možné ho obnovit a následně obnovit i data. Nicméně pokud ještě nezálohovali hlavní šifrovací klíč před odstraněním ze služby Key Vault, data v účtu Data Lake Storage Gen1 můžete nikdy možné dešifrovat.|


Kromě tohoto rozdílu, tedy kdo spravuje hlavní šifrovací klíče a instanci služby Key Vault, ve které se nachází, je zbytek návrhu pro oba režimy stejný.

Při výběru režimu pro hlavní šifrovací klíče je důležité pamatovat na následující:

*   Můžete zvolit, jestli chcete používat zákazníka spravované klíče nebo klíče spravované službou máte při zřizování účtu Data Lake Storage Gen1.
*   Po zřízení účtu Data Lake Storage Gen1 režim nejde změnit.

### <a name="encryption-and-decryption-of-data"></a>Šifrování a dešifrování dat

V návrhu šifrování dat používají tři typy klíčů. Následující tabulka poskytuje souhrn:

| Klíč                   | Zkratka | Přidružený k | Umístění úložiště                             | Type       | Poznámky                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Hlavní šifrovací klíč | MEK          | Účet Data Lake Storage Gen1 | Key Vault                              | Asymetrický | To může být spravován Gen1 úložiště Data Lake nebo.                                                              |
| Šifrovací klíč dat   | DEK          | Účet Data Lake Storage Gen1 | Trvalé úložiště spravované službou Data Lake Storage Gen1 | Symetrický  | Klíč DEK je šifrovaný klíčem MEK. Na trvalé médium se ukládá šifrovaný klíč DEK. |
| Šifrovací klíč bloku  | BEK          | Blokem dat | Žádný                                         | Symetrický  | Klíč BEK je odvozený od klíče DEK a příslušného datového bloku.                                                      |

Následující diagram znázorňuje tyto koncepty:

![Klíče v šifrování dat](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo algoritmus dešifrování souboru:
1.  Zkontrolujte, jestli je klíč DEK pro účet Data Lake Storage Gen1 uložený v mezipaměti a připravený k použití.
    - Pokud není, přečtení šifrovaného klíče DEK z trvalého úložiště a jeho odeslání do služby Key Vault k dešifrování. Uložení dešifrovaného klíče DEK v mezipaměti. Nyní je připraven k použití.
2.  Pro každý blok dat v souboru:
    - Načtení šifrovaného bloku dat z trvalého úložiště.
    - Vygenerování klíče BEK z klíče DEK a šifrovaného bloku dat.
    - Dešifrování dat pomocí klíče BEK.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo algoritmus šifrování bloku dat:
1.  Zkontrolujte, jestli je klíč DEK pro účet Data Lake Storage Gen1 uložený v mezipaměti a připravený k použití.
    - Pokud není, přečtení šifrovaného klíče DEK z trvalého úložiště a jeho odeslání do služby Key Vault k dešifrování. Uložení dešifrovaného klíče DEK v mezipaměti. Nyní je připraven k použití.
2.  Vygenerování jedinečného klíče BEK pro blok dat z klíče DEK.
3.  Šifrování datového bloku pomocí klíče BEK s použitím šifrování AES-256.
4.  Uložení šifrovaného datového bloku v trvalém úložišti.

> [!NOTE] 
> Klíč DEK je vždy uložený zašifrovaný klíčem MEK, ať už se jedná o trvalé médium nebo mezipaměť.

## <a name="key-rotation"></a>Obměna klíčů

Pokud používáte klíče spravované zákazníkem, můžete obměňovat klíč MEK. Informace o nastavení účtu Data Lake Storage Gen1 s použitím klíčů spravovaných zákazníkem najdete v tématu [Začínáme](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Požadavky

Při nastavování účtu Data Lake Storage Gen1 jste zvolili použití vlastních klíčů. Tuto možnost po vytvoření účtu nejde změnit. Následující postup předpokládá, že používáte klíče spravované zákazníkem (tedy že jste zvolili vlastní klíče ze služby Key Vault).

Všimněte si, že pokud použijete výchozí možnosti šifrování, vaše data budou vždy šifrována pomocí klíčů spravovaných službou Data Lake Storage Gen1. Při použití této možnosti není nutné možnost obměňovat klíče, jako jsou spravované službou Data Lake Storage Gen1.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>Obměna klíče MEK ve službě Data Lake Storage Gen1

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte do instance služby Key Vault, které jsou uloženy klíče přidružené k účtu Data Lake Storage Gen1. Vyberte **Klíče**.

    ![Snímek obrazovky služby Key Vault](./media/data-lake-store-encryption/keyvault.png)

3. Vyberte klíč přidružený k účtu Data Lake Storage Gen1 a vytvořte novou verzi tohoto klíče. Všimněte si, že Data Lake Storage Gen1 aktuálně podporuje pouze obměnu klíče na novou verzi klíče. Obměnu za jiný klíč nepodporuje.

   ![Snímek obrazovky okna Klíče se zvýrazněnou možností Nová verze](./media/data-lake-store-encryption/keynewversion.png)

4. Přejděte do účtu Data Lake Storage Gen1 a vyberte **šifrování**.

   ![Snímek obrazovky nástroje Data Lake Storage Gen1 účet okna se zvýrazněnou možností šifrování](./media/data-lake-store-encryption/select-encryption.png)

5. Zobrazí se zpráva informující o dostupnosti nové verze klíče. Kliknutím na **Obměnit klíč** aktualizujte klíč na novou verzi.

   ![Snímek obrazovky nástroje Data Lake Storage Gen1 okna zpráv a možností obměnit klíč zvýrazněnou](./media/data-lake-store-encryption/rotatekey.png)

Tato operace by neměla trvat déle než dvě minuty a nemělo by dojít k žádnému výpadku v důsledku obměny klíče. Po dokončení operace se začne používat nová verze klíče.

> [!IMPORTANT]
> Po dokončení operace obměny klíče se už k šifrování vašich dat nebude aktivně používat stará verze klíče.  Ve výjimečných případech neočekávaného selhání, které ovlivní dokonce i redundantní kopie vašich dat, se však můžou data obnovit ze zálohy, která stále používá starý klíč. Pokud chcete zajistit dostupnost dat i v těchto výjimečných případech, uchovávejte kopii předchozí verze šifrovacího klíče. Zobrazit [pokyny pro zotavení po havárii pro data ve službě Data Lake Storage Gen1](data-lake-store-disaster-recovery-guidance.md) osvědčené postupy pro plánování zotavení po havárii. 