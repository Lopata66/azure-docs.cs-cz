---
title: Informace o Azure Key Vault klíčů, tajných klíčích a certifikátech – Azure Key Vault
description: Přehled rozhraní Azure Key Vault REST a podrobností pro vývojáře pro klíče, tajné klíče a certifikáty.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: cb8a29c5d2eff46eecb2cf977bfb492f28731e68
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87043627"
---
# <a name="about-keys-secrets-and-certificates"></a>Informace o klíčích, tajných kódech a certifikátech

Azure Key Vault umožňuje aplikacím a uživatelům Microsoft Azure ukládat a používat několik typů tajných dat a klíčů:

- Kryptografické klíče: podporuje více typů klíčů a algoritmů a umožňuje použití modulů hardwarového zabezpečení (HSM) pro klíče s vysokou hodnotou. Další informace najdete v tématu [o klíčích](../keys/about-keys.md).
- Tajné kódy: poskytuje zabezpečené úložiště tajných kódů, jako jsou hesla a databázové připojovací řetězce. Další informace najdete v tématu [o tajných klíčích](../secrets/about-secrets.md).
- Certifikáty: podporuje certifikáty, které jsou postavené na klíčích a tajných klíčích a přidávají funkci automatického obnovení. Další informace najdete v tématu [o certifikátech](../certificates/about-certificates.md).
- Azure Storage: může spravovat klíče účtu Azure Storage za vás. Interně Key Vault možné vypsat (synchronizovat) klíče s účtem Azure Storage a pravidelně je znovu vygenerovat (otočit) klíče. Další informace najdete v tématu [Správa klíčů účtu úložiště pomocí Key Vault](../secrets/overview-storage-keys.md).

Obecnější informace o Key Vault najdete v tématu [o Azure Key Vault](overview.md).

## <a name="data-types"></a>Datové typy

V tématu Specifikace JOSE najdete relevantní datové typy pro klíče, šifrování a podepisování.  

-   **Algorithm** – podporovaný algoritmus pro klíčové operace, například RSA1_5  
-   **šifrovaný text-hodnota** – oktety textu šifry, kódované pomocí Base64URL  
-   **Digest-Value** – výstup algoritmu hash, kódovaný pomocí Base64URL  
-   **typ klíče** – jeden z podporovaných typů klíčů, například RSA (Rivest-Shamir-Adleman).  
-   oktety ve formátu **prostého** textu a prostého textu kódované pomocí Base64URL  
-   **Signatura-hodnota** – výstup algoritmu podpisu kódovanýho pomocí Base64URL  
-   **base64URL** – binární hodnota kódovaná v BASE64URL [RFC4648]  
-   **Boolean** – buď true, nebo false  
-   **Identita** – identita z Azure Active Directory (AAD).  
-   **IntDate** – Desítková hodnota JSON představující počet sekund od roku 1970-01-01T0:0: 0Z UTC až do zadaného data a času UTC. Podrobnosti týkající se data a času, obecně a UTC, najdete v tématu RFC3339.  

## <a name="objects-identifiers-and-versioning"></a>Objekty, identifikátory a správa verzí

Objekty uložené v Key Vault jsou tvořeny verzí pokaždé, když je vytvořena nová instance objektu. Každé verzi je přiřazen jedinečný identifikátor a adresa URL. Při prvním vytvoření objektu se mu přidaný jedinečný identifikátor verze a označen jako aktuální verze objektu. Vytvoření nové instance se stejným názvem objektu udělí novému objektu jedinečný identifikátor verze, což způsobí, že se stane aktuální verzí.  

Objekty v Key Vault lze řešit pomocí určující verze nebo vynechání verze pro operace s aktuální verzí objektu. Například při zadání klíče s názvem `MasterKey` , provedení operací bez určující verze způsobí, že systém použije nejnovější dostupnou verzi. Provádění operací s identifikátorem specifickým pro verzi způsobí, že systém použije určitou konkrétní verzi objektu.  

Objekty se jednoznačně identifikují v rámci Key Vault pomocí adresy URL. Žádné dva objekty v systému nemají stejnou adresu URL, bez ohledu na geografickou polohu. Úplná adresa URL objektu se nazývá identifikátor objektu. Adresa URL se skládá z předpony, která identifikuje Key Vault, typ objektu, uživatelem poskytnutý název objektu a verzi objektu. Název objektu rozlišuje velká a malá písmena. Identifikátory, které neobsahují verzi objektu, jsou označovány jako základní identifikátory.  

Další informace najdete v tématu [ověřování, žádosti a odpovědi](authentication-requests-and-responses.md) .

Identifikátor objektu má následující obecný formát:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Kde:  

| Element | Popis |  
|-|-|  
|`keyvault-name`|Název trezoru klíčů ve službě Microsoft Azure Key Vault.<br /><br /> Key Vault jména vybere uživatel a jsou globálně jedinečné.<br /><br /> Key Vault název musí být řetězec znaků 3-24, který obsahuje pouze 0-9, a-z, a-Z a-.|  
|`object-type`|Typ objektu, "Keys", "tajné" nebo "certifikáty".|  
|`object-name`|`object-name`Je název zadaný uživatelem pro a musí být jedinečný v rámci Key Vault. Název musí být řetězec znaků 1-127, který začíná písmenem a obsahuje pouze 0-9, a-z, A-Z a-.|  
|`object-version`|`object-version`Je systémem generovaný identifikátor řetězce znaků 32, který lze volitelně použít k adresování jedinečné verze objektu.|  

## <a name="next-steps"></a>Další kroky

- [Informace o klíčích](../keys/about-keys.md)
- [Informace o tajných kódech](../secrets/about-secrets.md)
- [Informace o certifikátech](../certificates/about-certificates.md)
- [Ověřování, žádosti a odpovědi](../general/authentication-requests-and-responses.md)
- [Průvodce vývojáře pro službu Key Vault](../general/developers-guide.md)
