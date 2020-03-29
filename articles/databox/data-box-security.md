---
title: Zabezpečení zařízení Microsoft Azure Data Box – přehled | Microsoft Docs
description: Popisuje funkce zabezpečení zařízení Azure Data Box v zařízení, službě a datech uložených v zařízení Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: alkohli
ms.openlocfilehash: 21b05631f1c225c9c4b1f7c65d18588900850b8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77911727"
---
# <a name="azure-data-box-security-and-data-protection"></a>Zabezpečení a ochrana dat v zařízení Azure Data Box

Data Box představuje bezpečné řešení pro přenos dat díky tomu, že prohlížet, upravovat a mazat data mohou jen oprávněné entity. Tento článek popisuje funkce zabezpečení zařízení Azure Data Box, které pomáhají chránit všechny komponenty řešení Data Box i v něm uložená data. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="data-flow-through-components"></a>Tok dat přes jednotlivé komponenty

Řešení Microsoft Azure Data Box se skládá ze čtyř hlavních komponent, které spolu navzájem spolupracují:

- **Služba Azure Data Box hostovaná v Azure** – služba pro správu, kterou použijete pro vytváření pořadí a konfiguraci zařízení a sledování průběhu operací.
- **Zařízení Data Box** – zařízení pro přenos, které vám bude doručeno za účelem přenosu vašich místních dat do Azure. 
- **Klienti/hostitelé připojení k zařízení** – klienti ve vaší infrastruktuře připojující se k zařízení Data Box a obsahující data, která je třeba přenést a chránit.
- **Cloudové úložiště** – umístění v cloudu Azure, kam se data uloží. Typicky jde o účet úložiště spojený s prostředkem Azure Data Box, který jste vytvořili.

Následující schéma představuje tok dat přes řešení Azure Data Box od místních systémů až do Azure.

![Zabezpečení zařízení Data Box](media/data-box-security/data-box-security-2.png)

Jako data toky prostřednictvím tohoto řešení jsou zaznamenány události a protokoly jsou generovány. Další informace najdete v webu [Sledování a protokolování událostí pro azure data box](data-box-logs.md).

## <a name="security-features"></a>Funkce zabezpečení

Data Box představuje bezpečné řešení pro přenos dat díky tomu, že prohlížet, upravovat a mazat data mohou jen oprávněné entity. Bezpečnostní funkce tohoto řešení se týkají disku a související služby a zajišťují maximální bezpečí uložených dat. 

### <a name="data-box-device-protection"></a>Ochrana zařízení Data Box

Zařízení Data Box je chráněno následujícími funkcemi.

- Odolné pouzdro zařízení, které odolá nárazům, nešetrné přepravě a vnějším podmínkám. 
- Pečetě, které odhalují manipulaci se zařízením při přepravě.
- Hardwarová a softwarová detekce nedovolené manipulace, která zabrání dalšímu používání zařízení.
- Běží pouze na softwaru specifickém pro Data Box.
- Spouští se v uzamčeném stavu.
- Řídí přístup k zařízení přes heslo pro odemčení zařízení.
- Přístupové přihlašovací údaje ke kopírování na zařízení a ze zařízení. Veškerý přístup k stránce **přihlašovacích údajů zařízení** na webu Azure Portal se zaznamenává do [protokolů aktivit](data-box-logs.md#query-activity-logs-during-setup).

### <a name="data-box-data-protection"></a>Ochrana dat na zařízení Data Box

Data, která se na zařízení Data Box ukládají a která se z něho kopírují, jsou chráněna následujícími funkcemi:

- 256bitové šifrování AES pro neaktivní uložená data.
- Pro přenášená data je možné použít šifrované protokoly.
- Bezpečné vymazání dat ze zařízení po dokončení jejich nahrání do Azure. Vymazání dat je v souladu s pokyny v [dodatku A pro pevné disky ATA ve standardech NIST 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf). Událost vymazání dat je zaznamenána v [historii pořadí](data-box-logs.md#download-order-history).

### <a name="data-box-service-protection"></a>Ochrana služby Data Box

Služba Data Box Disk je chráněná následujícími funkcemi.

- Přístup ke službě Data Box vyžaduje, aby vaše organizace měla předplatné Azure, které zahrnuje Data Box. Vaše předplatné určuje funkce, které máte na webu Azure Portal dostupné.
- Protože služba Data Box je hostovaná v Azure, je chráněna funkcemi zabezpečení Azure. Další informace o funkcích zabezpečení poskytovaných v prostředí Microsoft Azure najdete v [Centru zabezpečení Microsoft Azure](https://www.microsoft.com/TrustCenter/Security/default.aspx).
- Přístup k pořadí datových schrápů lze řídit pomocí rolí Řízení přístupu na základě rolí (RBAC) na základě rolí. Další informace naleznete v tématu [Nastavení řízení přístupu pro pořadí datových schrátek.](data-box-logs.md#set-up-access-control-on-the-order)
- Služba Data Box ukládá heslo pro odemčení, které slouží k odemčení zařízení ve službě.
- Služba Data Box ukládá údaje o objednávce a stavu přenosu v úložišti služby. Tyto informace se odstraní při smazání objednávky.

## <a name="managing-personal-data"></a>Správa osobních údajů

Azure Data Box shromažďuje a zobrazuje osobní údaje v následujících klíčových instancích služby:

- **Nastavení oznámení** – při vytvoření objednávky můžete nastavit e-mailovou adresu uživatele v rámci nastavení oznámení. Tyto údaje může prohlížet pouze správce. Tyto údaje se odstraní, jakmile úloha dosáhne konečného stavu nebo když objednávku smažete.

- **Údaje objednávky** – po vytvoření objednávky se na webu Azure Portal uloží dodací adresa, e-mail a kontaktní informace uživatelů. Uložené údaje zahrnují:

  - Jméno kontaktu
  - Telefonní číslo
  - E-mail
  - Ulice a číslo
  - Město
  - PSČ
  - Stav
  - Provincie/kraj/oblast
  - Číslo účtu dopravce
  - Sledovací číslo zásilky

    Údaje objednávky služba Data Box smaže po dokončení úlohy nebo když objednávku smažete.

- **Dodací adresa** – po objednání služba Data Box předá dodací adresu externímu dopravci, jako je například UPS nebo DHL. 

Další informace najdete v Zásadách ochrany osobních údajů společnosti Microsoft v [Centru zabezpečení](https://www.microsoft.com/trustcenter).


## <a name="security-guidelines-reference"></a>Referenční pokyny pro zabezpečení

V zařízení Data Box jsou implementovány následující pokyny pro zabezpečení: 

|Pokyn   |Popis   |
|---------|---------|
|[IEC 60529 IP52](https://www.iec.ch/)    | Ochrana před vodou a prachem         |
|[ISTA 2A](https://ista.org/docs/2Aoverview.pdf)     | Odolnost před nešetrným zacházením při přepravě          |
|[NIST SP 800-147](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-147.pdf)      | Bezpečná aktualizace firmwaru         |
|[FIPS 140-2 Level 2](https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf)      | Ochrana dat         |
|Dodatek A pro pevné disky ATA v [NIST SP 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf)      | Sanitizace dat         |

## <a name="next-steps"></a>Další kroky

- Projděte si [požadavky zařízení Data Box](data-box-system-requirements.md).
- Seznamte se s [omezeními služby Data Box](data-box-limits.md).
- Rychle nasaďte [Azure Data Box](data-box-quickstart-portal.md) na webu Azure Portal.
