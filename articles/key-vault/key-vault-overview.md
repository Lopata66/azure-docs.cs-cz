---
title: Přehled služby Azure Key Vault | Microsoft Docs
description: Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných klíčů.
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 34af20ee-3fa7-4f28-9d98-6168b1759764
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 07/17/2018
ms.author: barclayn
ms.openlocfilehash: aae7836448ff27b4c80d7bb53e108034ee52db1c
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47586287"
---
# <a name="what-is-azure-key-vault"></a>Co je Azure Key Vault?

Azure Key Vault pomáhá řešit následující problémy
- **Správa tajných klíčů** – Azure Key Vault je možné využít k zabezpečenému ukládání tokenů, hesel, certifikátů, klíčů rozhraní API a dalších tajných klíčů a důsledné kontrole přístupu k nim.
- **Správa klíčů** – Azure Key Vault se dá použít taky jako řešení pro správu klíčů. Azure Key Vault usnadňuje vytváření a správu šifrovacích klíčů sloužících k šifrování dat. 
- **Správa certifikátů** – Azure Key Vault je taky služba, která umožňuje snadné zřizování, správu a nasazování veřejných a privátních certifikátů SSL/TLS (Secure Sockets Layer/Transport Layer Security) pro použití s Azure a interními připojenými prostředky. 
- **Ukládání tajných klíčů chráněných moduly hardwarového zabezpečení (HSM)** – klíče a tajné klíče je možné chránit pomocí softwaru nebo modulů HSM ověřených podle standardu FIPS 140-2 Level 2.

## <a name="why-use-azure-key-vault"></a>Proč používat Azure Key Vault?

### <a name="centralize-application-secrets"></a>Centralizace tajných klíčů aplikací

Centralizace ukládání tajných klíčů aplikací ve službě Azure Key Vault umožňuje řídit jejich distribuci. Key Vault výrazně snižuje riziko nechtěného úniku tajných klíčů. Při použití služby Key Vault už vývojáři aplikací nemusejí ukládat informace o zabezpečení ve svých aplikacích. Tím se eliminuje potřeba zahrnout tyto informace do kódu. Aplikace se například může potřebovat připojit k databázi. Místo uložení připojovacího řetězce v kódu aplikace ho můžete bezpečně uložit ve službě Key Vault.

Vaše aplikace můžou v případě potřeby bezpečně přistupovat k těmto informacím pomocí identifikátorů URI, které jim po uložení klíče nebo tajného klíče aplikace ve službě Azure Key Vault umožňují načíst konkrétní verzi tajného klíče. Toto se děje bez nutnosti psát vlastní kód pro zajištění ochrany tajných informací.

### <a name="securely-store-secrets-and-keys"></a>Bezpečné ukládání tajných klíčů a klíčů

Tajné kódy a klíče jsou chráněné systémem Azure pomocí standardních algoritmů, délek klíčů a modulů hardwarového zabezpečení (HSM). Použité moduly HMS jsou ověřené podle standardu FIPS (Federal Information Processing Standards) 140-2 Level 2.

Pro přístup k trezoru klíčů se vyžaduje řádné ověření a autorizace volajícího (uživatel nebo aplikace). Ověření určí identitu volajícího a autorizace následně určí, které operace má volající povoleno provádět.

Ověření se provádí prostřednictvím Azure Active Directory. Autorizace se může provádět prostřednictvím řízení přístupu na základě role (RBAC) nebo zásad přístupu trezoru klíčů. RBAC se používá při správě trezorů a zásady přístupu trezoru klíčů se používají při pokusu o přístup k datům uloženým v trezoru.

Trezory klíčů Azure můžou být chráněné softwarovým nebo hardwarovým modulem HSM. Pro situace, kdy potřebujete lepší kontrolu, můžete v modulech hardwarového zabezpečení (HSM) importovat nebo generovat klíče, které nikdy neopustí hranice HSM. Microsoft využívá moduly hardwarového zabezpečení Thales. K přesunu klíče z vašeho HSM do služby Azure Key Vault můžete použít nástroje Thales.

Služba Azure Key Vault je navržená tak, aby Microsoft vaše data neviděl ani je nemohl extrahovat.

### <a name="monitor-access-and-use"></a>Monitorování přístupu a využití

Po vytvoření několika trezorů klíčů budete chtít monitorovat, jak a kdy se k vašim klíčům a tajným klíčům přistupuje. Můžete to provést povolením protokolování služby Key Vault. Ve službě Azure Key Vault můžete nakonfigurovat následující:

- Archivace do účtu úložiště
- Streamování do centra událostí
- Odesílání protokolů do Log Analytics

Máte kontrolu nad svými protokoly, které můžete zabezpečit prostřednictvím omezení přístupu, a můžete také odstranit protokoly, které už nepotřebujete.

### <a name="simplified-administration-of-application-secrets"></a>Zjednodušená správa tajných klíčů aplikací

Pokud ukládáte cenná data, musíte provést několik kroků. Informace o zabezpečení musí být zabezpečené, odpovídat životnímu cyklu a musí být vysoce dostupné. Azure Key Vault spoustu toho zjednodušuje tím, že:

- Odstraňuje potřebu interní znalosti modulů hardwarového zabezpečení.
- Rychle vertikálně navyšuje kapacitu s ohledem na špičky využití ve vaší organizaci.
- Replikuje obsah služby Key Vault v jedné oblasti do sekundární oblasti. To zajišťuje vysokou dostupnost a eliminuje potřebu jakékoli akce od správce k aktivaci převzetí služeb při selhání.
- Poskytuje standardní možnosti správy Azure prostřednictvím portálu, Azure CLI nebo PowerShellu.
- Automatizuje určité úlohy prováděné s certifikáty, které jste zakoupili od veřejných certifikačních autorit, třeba jejich registraci a obnovení.

Kromě toho trezory klíčů Azure umožňují oddělení tajných klíčů aplikací. Aplikace můžou přistupovat pouze k trezoru, ke kterému mají povolený přístup, a provádět pouze určité operace. Službu Azure Key Vault můžete vytvořit pro jednotlivé aplikace a omezit přístup k tajným klíčům uloženým ve službě Key Vault na konkrétní aplikaci a tým vývojářů.

### <a name="integrate-with-other-azure-services"></a>Integrace s ostatními službami Azure

Služba Key Vault se jako zabezpečené úložiště v Azure používá ke zjednodušení scénářů, jako je služba [Azure Disk Encryption](../security/azure-security-disk-encryption.md), funkce [Always Encrypted]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) v SQL Serveru a Azure SQL Database a [webové aplikace Azure]( https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site). Samotná služba Key Vault se může integrovat s účty úložiště, centry událostí a Log Analytics.

## <a name="next-steps"></a>Další kroky

- [Rychlý start: Vytvoření služby Azure Key Vault pomocí rozhraní příkazového řádku](quick-create-cli.md)
- [Konfigurace webové aplikace Azure pro čtení tajného klíče ze služby Key Vault](tutorial-web-application-keyvault.md)
