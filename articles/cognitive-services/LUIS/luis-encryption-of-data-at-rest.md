---
title: Šifrování neaktivních dat Language Understanding služby
titleSuffix: Azure Cognitive Services
description: Šifrování neaktivních dat Language Understanding služby.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 4fc816c3894120a5d1b356d91ebebbc56f21b530
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052689"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>Šifrování neaktivních dat Language Understanding služby

Služba Language Understanding automaticky šifruje vaše data při trvalém ukládání do cloudu. Šifrování služby Language Understanding chrání vaše data a pomáhá splnit závazky zabezpečení a dodržování předpisů v organizaci.

## <a name="about-cognitive-services-encryption"></a>O šifrování Cognitive Services

Data se šifrují a dešifrují s využitím [256 šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) kompatibilního se [standardem FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) . Šifrování a dešifrování je transparentní, což znamená, že se pro vás spravuje šifrování a přístup. Vaše data jsou ve výchozím nastavení zabezpečená a nemusíte upravovat kód ani aplikace, abyste mohli využívat šifrování.

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Ve výchozím nastavení používá vaše předplatné šifrovací klíče spravované Microsoftem. K dispozici je také možnost Spravovat předplatné s vlastními klíči. Klíče spravované zákazníkem (CMK) nabízejí větší flexibilitu při vytváření, střídání, zakázání a odvolávání řízení přístupu. Můžete také auditovat šifrovací klíče používané k ochraně vašich dat.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Klíče spravované zákazníkem s využitím Azure Key Vaultu

K dispozici je také možnost Spravovat předplatné s vlastními klíči. Klíče spravované zákazníkem (CMK), označované také jako Přineste si vlastní klíč (BYOK), nabízejí větší flexibilitu při vytváření, střídání, zakázání a odvolávání řízení přístupu. Můžete také auditovat šifrovací klíče používané k ochraně vašich dat.

K ukládání klíčů spravovaných zákazníkem je nutné použít Azure Key Vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Prostředek Cognitive Services a trezor klíčů musí být ve stejné oblasti a v rámci stejného tenanta Azure Active Directory (Azure AD), ale můžou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

### <a name="customer-managed-keys-for-language-understanding"></a>Klíče spravované zákazníkem pro Language Understanding

Chcete-li požádat o možnost použití klíčů spravovaných zákazníkem, vyplňte a odešlete [formulář žádosti o klíč spravovaný zákazníkem služby Luis](https://aka.ms/cogsvc-cmk). Bude to trvat přibližně 3-5 pracovních dnů, než se vrátí na stav vaší žádosti. V závislosti na poptávce můžete být do fronty zařazené a schválené, protože místo bude k dispozici. Po schválení pro použití CMK s LUIS budete muset vytvořit nový prostředek Language Understanding z Azure Portal a vybrat E0 jako cenovou úroveň. Nová SKU bude fungovat stejně jako SKU F0, která je již k dispozici, s výjimkou CMK. Uživatelé nebudou moct upgradovat z F0 na novou SKLADOVOU položku E0.

![Obrázek předplatného LUIS](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>Regionální dostupnost

Klíče spravované zákazníkem jsou k dispozici ve všech [oblastech vytváření obsahu](luis-reference-regions.md). 

### <a name="limitations"></a>Omezení

Při použití vrstvy E0 s existujícími nebo dříve vytvořenými aplikacemi platí určitá omezení:

* Migrace do prostředku E0 se zablokuje. Uživatelé budou moct migrovat své aplikace jenom na F0 prostředky. Po dokončení migrace existujícího prostředku na F0 můžete vytvořit nový prostředek ve vrstvě E0. Další informace o [migraci najdete tady](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring).  
* Přesun aplikací do nebo z prostředku E0 se zablokuje. Řešením tohoto omezení je exportovat existující aplikaci a importovat ji jako prostředek E0.
* Funkce kontroly pravopisu Bingu není podporovaná.
* Protokolování provozu koncových uživatelů je zakázané, pokud je vaše aplikace E0.
* Funkce pro dopřední řeči z Azure bot Service není podporovaná pro aplikace ve vrstvě E0. Tato funkce je k dispozici prostřednictvím Azure Bot Service, která nepodporuje CMK.
* Funkce pro dopřední řeči z portálu vyžaduje Azure Blob Storage. Další informace najdete v tématu [Přineste si vlastní úložiště](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging).

### <a name="enable-customer-managed-keys"></a>Povolit klíče spravované zákazníkem

Nový prostředek Cognitive Services je vždycky zašifrovaný pomocí klíčů spravovaných Microsoftem. Klíče spravované zákazníkem není možné povolit v době, kdy se prostředek vytvořil. Klíče spravované zákazníkem jsou uloženy v Azure Key Vault a trezor klíčů musí být zřízen pomocí zásad přístupu, které udělují klíčová oprávnění ke spravované identitě, která je přidružena k prostředku Cognitive Services. Spravovaná identita je k dispozici až po vytvoření prostředku pomocí cenové úrovně pro CMK.

Informace o použití klíčů spravovaných zákazníkem s Azure Key Vault pro šifrování Cognitive Services najdete v těchto tématech:

- [Konfigurace klíčů spravovaných zákazníkem pomocí Key Vault pro Cognitive Services šifrování z Azure Portal](../Encryption/cognitive-services-encryption-keys-portal.md)

Povolením zákaznických klíčů taky povolíte spravovanou identitu přiřazenou systémem, což je funkce Azure AD. Po povolení spravované identity přiřazené systémem se tento prostředek zaregistruje ve službě Azure Active Directory. Po registraci bude spravované identitě udělen přístup k Key Vault vybranému během nastavení spravovaného klíče zákazníka. Další informace o [spravovaných identitách](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)najdete v.

> [!IMPORTANT]
> Pokud zakážete spravované identity přiřazené systémem, odeberou se přístup k trezoru klíčů a veškerá data zašifrovaná pomocí klíčů zákazníka už nebudou dostupná. Jakékoli funkce závislé na těchto datech přestanou fungovat.

> [!IMPORTANT]
> Spravované identity v současné době nepodporují scénáře pro více adresářů. Při konfiguraci klíčů spravovaných zákazníkem v Azure Portal se spravovaná identita automaticky přiřadí v rámci pokrývání. Pokud později přesunete předplatné, skupinu prostředků nebo prostředek z jednoho adresáře služby Azure AD do jiného, spravovaná identita přidružená k prostředku se nepřenáší do nového tenanta, takže klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu **přenos předplatného mezi adresáři služby Azure AD** v [nejčastějších dotazech a známých potížích se spravovanými identitami pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ukládání klíčů spravovaných zákazníkem v Azure Key Vault

Pokud chcete povolit klíče spravované zákazníkem, musíte použít Azure Key Vault k uložení klíčů. V trezoru klíčů musíte povolit jak **obnovitelné odstranění** , tak i **Nemazat** vlastnosti.

Cognitive Services šifrování podporují pouze klíče RSA o velikosti 2048. Další informace o klíčích najdete v tématu **Key Vault Keys** v tématu [informace o Azure Key Vaultch klíčích, tajných klíčích a certifikátech](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Otočit klíče spravované zákazníkem

Klíč spravovaný zákazníkem můžete v Azure Key Vault otočit podle vašich zásad dodržování předpisů. Když je klíč otočen, je nutné aktualizovat prostředek Cognitive Services, aby používal nový identifikátor URI klíče. Informace o tom, jak aktualizovat prostředek tak, aby používal novou verzi klíče v Azure Portal, najdete v části s názvem **aktualizace verze klíče** v tématu [konfigurace klíčů spravovaných zákazníkem pro Cognitive Services pomocí Azure Portal](../Encryption/cognitive-services-encryption-keys-portal.md).

Otočení klíče neaktivuje opětovné šifrování dat v prostředku. Od uživatele není vyžadována žádná další akce.

### <a name="revoke-access-to-customer-managed-keys"></a>Odvolat přístup k klíčům spravovaným zákazníkem

K odvolání přístupu ke klíčům spravovaným zákazníkem použijte PowerShell nebo Azure CLI. Další informace najdete v tématu [Azure Key Vault PowerShellu](https://docs.microsoft.com/powershell/module/az.keyvault//) nebo rozhraní příkazového [řádku Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). Odvolání přístupu efektivně zablokuje přístup ke všem datům v prostředku Cognitive Services, protože šifrovací klíč není přístupný Cognitive Services.

## <a name="next-steps"></a>Další kroky

* [Formulář žádosti o klíč spravovaný zákazníkem služby LUIS](https://aka.ms/cogsvc-cmk)
* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
