---
title: Zabezpečení databází PaaS v Azure | Microsoft Docs
description: 'Přečtěte si o Azure SQL Database a SQL Data Warehouse osvědčených postupech zabezpečení pro zabezpečení webových a mobilních aplikací PaaS. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: d96c453821ddca3c2d54916132b9ae95a01ca536
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68612727"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Osvědčené postupy pro zabezpečení databází PaaS v Azure

V tomto článku probereme kolekci [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) a [SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) osvědčených postupů zabezpečení pro zabezpečení vašich webových a mobilních aplikací PaaS (Platform as a Service). Tyto osvědčené postupy se odvozují z našich zkušeností s Azure a zkušeností zákazníků, jako je sami.

Azure SQL Database a SQL Data Warehouse poskytují službu relačních databází pro aplikace založené na internetu. Pojďme se podívat na služby, které vám pomůžou chránit vaše aplikace a data při použití Azure SQL Database a SQL Data Warehouse v nasazení PaaS:

- Ověřování Azure Active Directory (místo ověřování SQL Server)
- Azure SQL firewall
- Transparentní šifrování dat (TDE)

## <a name="use-a-centralized-identity-repository"></a>Použití centralizovaného úložiště identit
Databáze SQL Azure je možné nakonfigurovat tak, aby používaly jeden ze dvou typů ověřování:

- **Ověřování SQL** používá uživatelské jméno a heslo. Když jste vytvářeli logický server databáze, zadali jste uživatelské jméno a heslo účtu „server admin“. Pomocí těchto přihlašovacích údajů se můžete na tomto serveru ověřit jako vlastník databáze.

- **Ověřování Azure Active Directory** používá identity spravované pomocí Azure Active Directory a je podporované pro spravované a integrované domény. Pokud chcete použít Azure Active Directory ověřování, musíte vytvořit jiného správce serveru, který se nazývá správce Azure AD, což může spravovat uživatele a skupiny Azure AD. Tento správce také smí provádět všechny operace jako běžný správce serveru.

[Ověřování Azure Active Directory](../../active-directory/develop/authentication-scenarios.md) je mechanismus připojení k Azure SQL Database a SQL Data Warehouse pomocí identit v Azure Active Directory (AD). Azure AD poskytuje alternativu k ověřování SQL Server, abyste mohli zastavit šíření uživatelských identit mezi databázovými servery. Ověřování Azure AD umožňuje centrálně spravovat identity uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění. Centrální správa ID poskytuje jediné místo pro správu uživatelů databáze a zjednodušuje správu oprávnění.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Výhody použití Azure AD místo ověřování SQL
- Umožňuje otočení hesla na jednom místě.
- Spravuje oprávnění databáze pomocí externích skupin Azure AD.
- Eliminuje ukládání hesel povolením integrovaného ověřování systému Windows a dalších forem ověřování, které Azure AD podporuje.
- Používá uživatele databáze s omezením k ověřování identit na úrovni databáze.
- Podporuje ověřování založené na tokenech pro aplikace, které se připojují k SQL Database.
- Podporuje federaci doménové federace s Active Directory Federation Services (AD FS) (ADFS) nebo nativním ověřováním uživatele a hesla pro místní službu Azure AD bez synchronizace domén.
- Podporuje připojení z SQL Server Management Studio, která používají univerzální ověřování služby Active Directory, které zahrnuje službu [Multi-Factor Authentication (MFA)](/azure/active-directory/authentication/multi-factor-authentication). MFA zahrnuje silné ověřování s využitím celé řady možností ověření – telefonických hovorů, textových zpráv, čipových karet s kódem PIN nebo oznámení mobilní aplikace. Další informace najdete v tématu [univerzální ověřování pomocí SQL Database a SQL Data Warehouse](../../sql-database/sql-database-ssms-mfa-authentication.md).

Další informace o ověřování Azure AD najdete tady:

- [Ověřování pomocí Azure Active Directory ověřování pomocí SQL Database, spravované instance nebo SQL Data Warehouse](../../sql-database/sql-database-aad-authentication.md)
- [Ověřování do Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Podpora ověřování na základě tokenů pro Azure SQL DB pomocí ověřování Azure AD](../../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> Pokud chcete zajistit, aby pro vaše prostředí byla Azure Active Directory vhodná, přečtěte si téma [funkce a omezení služby Azure AD](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).
>
>

## <a name="restrict-access-based-on-ip-address"></a>Omezení přístupu na základě IP adresy
Můžete vytvořit pravidla brány firewall, která určují rozsah přípustných IP adres. Tato pravidla je možné cílit na úrovni serveru i databáze. Doporučujeme používat pravidla brány firewall na úrovni databáze, kdykoli je to možné, zvýšit zabezpečení a zvýšit přenositelnost databáze. Pravidla brány firewall na úrovni serveru se nejlépe používají pro správce a pokud máte mnoho databází se stejnými požadavky na přístup, ale nechcete ztrácet čas konfigurací jednotlivých databází zvlášť.

SQL Database výchozí omezení zdrojové IP adresy umožňují přístup z jakékoli adresy Azure, včetně dalších předplatných a klientů. To můžete omezit tak, aby umožňovaly přístup k instanci jenom na IP adresy. I v případě, že máte omezení brány firewall a IP adresy SQL, je stále potřeba silné ověřování. Přečtěte si doporučení uvedená výše v tomto článku.

Další informace o bráně firewall Azure SQL a omezeních IP adres najdete v těchto tématech:

- [Řízení přístupu Azure SQL Database a SQL Data Warehouse](../../sql-database/sql-database-control-access.md)
- [Pravidla brány firewall Azure SQL Database a SQL Data Warehouse](../../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Šifrování dat v klidovém umístění
Ve výchozím nastavení je povolená [transparentní šifrování dat (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) . TDE transparentně šifruje data a soubory protokolů SQL Server, Azure SQL Database a Azure SQL Data Warehouse. TDE chrání před ohrožením přímého přístupu k souborům nebo jejich záloze. To umožňuje šifrování neaktivních dat bez změny stávajících aplikací. TDE by měl vždycky zůstat zapnuté. Tím se ale nezastaví útočník s použitím cesty Normal (běžný přístup). TDE poskytuje možnost dodržovat řadu zákonů, předpisů a pokynů v různých oborech.

Azure SQL spravuje klíčové problémy související s TDE. Stejně jako u TDE musí být pro zajištění možnosti obnovy a při přesunu databází nutná zvláštní péče o místní péči. Ve složitějších scénářích je možné klíče v Azure Key Vault explicitně spravovat prostřednictvím správy rozšiřitelných klíčů. Přečtěte si téma [povolení TDE na SQL Server pomocí EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). To také umožňuje Bring Your Own Key (BYOK) prostřednictvím BYOK funkce trezory klíčů Azure.

Azure SQL poskytuje šifrování pro sloupce prostřednictvím [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). To umožňuje přístup pouze autorizovaným aplikacím k citlivým sloupcům. Použití tohoto typu šifrování omezuje dotazy SQL na šifrované sloupce na hodnoty založené na rovnosti.

Šifrování na úrovni aplikace by se mělo používat i pro selektivní data. Je možné, že se při zašifrování dat pomocí klíče, který je uložený ve správné zemi nebo oblasti, může v některých případech zmírnit data suverenity. To brání dokonce náhodnému přenosu dat z důvodu problému, protože není možné dešifrovat data bez klíče, za předpokladu, že se používá silný algoritmus (například AES 256).

Další opatření můžete použít k zabezpečení databáze, jako je například návrh zabezpečeného systému, šifrování důvěrných prostředků a vytváření brány firewall pro databázové servery.

## <a name="next-steps"></a>Další kroky
Tento článek vás zavedl do shromažďování SQL Database a SQL Data Warehousech osvědčených postupů zabezpečení pro zabezpečení webových a mobilních aplikací PaaS. Další informace o zabezpečení nasazení PaaS najdete v těchto tématech:

- [Zabezpečení nasazení PaaS](paas-deployments.md)
- [Zabezpečení webových a mobilních aplikací PaaS pomocí Azure App Services](paas-applications-using-app-services.md)
