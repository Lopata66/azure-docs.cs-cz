---
title: Podporované funkce FHIR v Azure – Azure API pro FHIR
description: Tento článek vysvětluje, které funkce specifikace FHIR jsou implementované v Azure API pro FHIR.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 1cb3af32f1ad16218c82f91c3f28d4f4ab47e677
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843500"
---
# <a name="features"></a>Funkce

Azure API for FHIR poskytuje plně spravované nasazení serveru Microsoft FHIR pro Azure. Server je implementací standardu [FHIR](https://hl7.org/fhir) . Tento dokument obsahuje seznam hlavních funkcí serveru FHIR.

## <a name="fhir-version"></a>Verze FHIR

Nejnovější verze je podporována:`4.0.1`

Předchozí verze, které jsou aktuálně podporované, zahrnují:`3.0.2`

## <a name="rest-api"></a>REST API

| Rozhraní API                            | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| read                           | Ano       | Ano       | Ano       |                                                     |
| vread                          | Ano       | Ano       | Ano       |                                                     |
| update                         | Ano       | Ano       | Ano       |                                                     |
| aktualizace s optimistické uzamykání | Ano       | Ano       | Ano       |                                                     |
| aktualizovat (podmíněné)           | Ano       | Ano       | Ano       |                                                     |
| použita                          | Ne        | Ne        | Ne        |                                                     |
| odstranění                         | Ano       | Ano       | Ano       |                                                     |
| Odstranit (podmíněné)           | Ne        | Ne        | Ne        |                                                     |
| vytvoření                         | Ano       | Ano       | Ano       | Podpora pro POST/PUT                               |
| vytvořit (podmíněné)           | Ano       | Ano       | Ano       |                                                     |
| search                         | Částečné   | Částečné   | Částečné   | Viz níže                                           |
| zřetězené hledání                 | Ne        | Ano       | Ne        |                                           |
| zpětné zřetězené hledání         | Ne        | Ne        | Ne        |                                            |
| možnosti                   | Ano       | Ano       | Ano       |                                                     |
| dávka                          | Ano       | Ano       | Ano       |                                                     |
| transakce                    | Ne        | Ano       | Ne        |                                                     |
| historie                        | Ano       | Ano       | Ano       |                                                     |
| přenosu                         | Částečné   | Částečné   | Částečné   | `self`a `next` jsou podporovány                     |
| prostředníci                 | Ne        | Ne        | Ne        |                                                     |

## <a name="search"></a>Search

Všechny typy parametrů vyhledávání jsou podporovány. 

| Typ parametru hledání | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-----------------------|-----------|-----------|-----------|---------|
| Číslo                | Ano       | Ano       | Ano       |         |
| Datum/datum a čas         | Ano       | Ano       | Ano       |         |
| Řetězec                | Ano       | Ano       | Ano       |         |
| Token                 | Ano       | Ano       | Ano       |         |
| Referenční informace             | Ano       | Ano       | Ano       |         |
| Složený             | Ano       | Ano       | Ano       |         |
| Množství              | Ano       | Ano       | Ano       |         |
| Identifikátor URI                   | Ano       | Ano       | Ano       |         |
| Speciální               | Ne        | Ne        | Ne        |         |


| Modifikátory             | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Ano       | Ano       | Ano       |         |
|`:exact`               | Ano       | Ano       | Ano       |         |
|`:contains`            | Ano       | Ano       | Ano       |         |
|`:text`                | Ano       | Ano       | Ano       |         |
|`:in`klíčové          | Ne        | Ne        | Ne        |         |
|`:below`klíčové       | Ne        | Ne        | Ne        |         |
|`:above`klíčové       | Ne        | Ne        | Ne        |         |
|`:not-in`klíčové      | Ne        | Ne        | Ne        |         |
|`:[type]`odkaz  | Ne        | Ne        | Ne        |         |
|`:below`identifikátor URI         | Ano       | Ano       | Ano       |         |
|`:not`                 | Ne        | Ne        | Ne        |         |
|`:above`identifikátor URI         | Ne        | Ne        | Ne        | Problém [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Společný parametr hledání | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Ano       | Ano       | Ano       |         |
| `_lastUpdated`          | Ano       | Ano       | Ano       |         |
| `_tag`                  | Ano       | Ano       | Ano       |         |
| `_profile`              | Ano       | Ano       | Ano       |         |
| `_security`             | Ano       | Ano       | Ano       |         |
| `_text`                 | Ne        | Ne        | Ne        |         |
| `_content`              | Ne        | Ne        | Ne        |         |
| `_list`                 | Ne        | Ano       | Ano       |         |
| `_has`                  | Ne        | Ne        | Ne        |         |
| `_type`                 | Ano       | Ano       | Ano       |         |
| `_query`                | Ne        | Ne        | Ne        |         |

| Operace hledání       | Podporováno – PaaS | Podporováno – OSS (SQL) | Podporované – OSS (Cosmos DB) | Komentář |
|-------------------------|-----------|-----------|-----------|---------|
| `_filter`               | Ne        | Ne        | Ne        |         |
| `_sort`                 | Ne        | Ne        | Ne        |         |
| `_score`                | Ne        | Ne        | Ne        |         |
| `_count`                | Ano       | Ano       | Ano       |         |
| `_summary`              | Částečné   | Částečné   | Částečné   | `_summary=count`je podporováno |
| `_include`              | Ne        | Ano       | Ne        |         |
| `_revinclude`           | Ne        | Ne        | Ne        |         |
| `_contained`            | Ne        | Ne        | Ne        |         |
| `_elements`             | Ne        | Ne        | Ne        |         |

## <a name="persistence"></a>Uchování

Server Microsoft FHIR má modul připojitelná Persistence (viz [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

V současné době je otevřený zdrojový kód FHIR serveru, který obsahuje implementaci [Azure Cosmos DB](../cosmos-db/index-overview.md) a [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB je globálně distribuovaná databáze s více modely (rozhraní API SQL, rozhraní MongoDB API atd.). Podporuje různé [úrovně konzistence](../cosmos-db/consistency-levels.md). Výchozí šablona nasazení konfiguruje server FHIR s `Strong` konzistencí, ale zásady konzistence je možné upravit (obecně zmírnit) na žádost pomocí `x-ms-consistency-level` hlavičky žádosti.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Server FHIR používá pro řízení přístupu [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) . Konkrétně se vynutila Access Control založená na rolích (RBAC), pokud je `FhirServer:Security:Enabled` parametr konfigurace nastavený na hodnotu `true` a všechny požadavky (kromě `/metadata` ) na server FHIR musí mít jako `Authorization` hlavičku Request nastavenou na `Bearer <TOKEN>` . Token musí obsahovat jednu nebo více rolí, jak je definováno v `roles` deklaraci identity. Požadavek bude povolen, pokud token obsahuje roli, která umožňuje zadanou akci u zadaného prostředku.

V současné době se povolené akce pro danou roli aplikují *globálně* na rozhraní API.

## <a name="next-steps"></a>Další kroky

V tomto článku jste si přečetli informace o podporovaných funkcích FHIR v rozhraní Azure API pro FHIR. Dále nasaďte rozhraní Azure API pro FHIR.
 
>[!div class="nextstepaction"]
>[Nasazení Azure API for FHIR](fhir-paas-portal-quickstart.md)
