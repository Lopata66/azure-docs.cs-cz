---
title: 'Kurz: migrace z Google Maps na Azure Maps | Mapy Microsoft Azure'
description: Kurz migrace z Google Maps na Microsoft Azure Maps Průvodce vás seznámí s postupem, jak přepnout na Azure Maps rozhraní API a sady SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 2422204a809e0b13f4e337d49b851a0338681853
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2020
ms.locfileid: "86249202"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Migrace z Google Maps do Azure Maps

V tomto kurzu najdete informace o tom, jak migrovat webové a mobilní aplikace a aplikace založené na serveru z Google Maps na platformu Microsoft Azure Maps. Tento kurz obsahuje srovnávací ukázky kódu, návrhy migrace a osvědčené postupy pro migraci na Azure Maps.

## <a name="azure-maps-platform-overview"></a>Přehled platformy Azure Maps

Azure Maps poskytuje vývojářům ze všech odvětví výkonné geoprostorové možnosti. Možnosti jsou zabaleny s pravidelnými aktualizovanými daty mapy a poskytují geografickou souvislost pro webové a mobilní aplikace. Azure Maps má sadu rozhraní REST API, která je kompatibilní s rozhraním Azure One API. Rozhraní REST API nabízí mapy pro vykreslování, vyhledávání, směrování, provoz, časová pásma, geografickou polohu, monitorování geografických zón, mapování dat, počasí, mobilitu a prostorové operace. K operacím jsou připojeny webové i sady Android SDK, které usnadňují vývoj, flexibilní a přenosné prostředí napříč různými platformami.

## <a name="high-level-platform-comparison"></a>Porovnání platforem vysoké úrovně

Tabulka poskytuje seznam Azure Mapsch funkcí, které odpovídají funkcím služby Google Maps, na nejvyšší úrovni. V tomto seznamu se nezobrazují všechny funkce Azure Maps. Mezi další Azure Maps funkce patří: usnadnění přístupu, monitorování geografických zón, izochronů, prostorové operace, přímý přístup dlaždic map, služby Batch a porovnání pokrytí dat (tj. pokrytí snímků).

| Funkce mapy Google         | Podpora Azure Maps                     |
|-----------------------------|:--------------------------------------:|
| Sada Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | Plánováno                                |
| Rozhraní API služby REST           | ✓                                      |
| Směry (směrování)        | ✓                                      |
| Matice vzdáleností             | ✓                                      |
| Zvýšení oprávnění                   | Plánováno                                |
| Geografické kódování (přesměrné/obrácené) | ✓                                      |
| Geografická poloha                 | Nelze použít                                    |
| Nejbližší silnice               | ✓                                      |
| Hledání míst               | ✓                                      |
| Podrobnosti o místech              | Není k dispozici – web & telefonní číslo. |
| Místo fotek               | Nelze použít                                    |
| Umístit automatické dokončování          | ✓                                      |
| Přichycení k cestám                | ✓                                      |
| Omezení rychlosti                | ✓                                      |
| Statické mapy                 | ✓                                      |
| Statické zobrazení ulice          | Nelze použít                                    |
| Časové pásmo                   | ✓                                      |
| Rozhraní API pro mapování Embedded           | Nelze použít                                    |
| Mapování adres URL                    | Nelze použít                                    |

Google Maps poskytuje základní ověřování založené na klíčích. Azure Maps poskytuje jak základní ověřování založené na klíčích, tak ověřování Azure Active Directory. Ověřování Azure Active Directory poskytuje více funkcí zabezpečení oproti základnímu ověřování založenému na klíčích.

## <a name="licensing-considerations"></a>Požadavky na licencování

Při migraci na Azure Maps z webu Google Maps zvažte následující body týkající se licencování.

- Azure Maps poplatky za použití interaktivních map, které jsou založené na počtu načtených dlaždic mapy. Na druhé straně se za načtení mapového ovládacího prvku účtují služby Google Maps. V interaktivních sadách Azure Maps SDK se dlaždice map automaticky ukládají do mezipaměti, aby se snížily náklady na vývoj. Jedna Azure Mapsová transakce je vygenerována pro každých 15 dlaždic map, které jsou načteny. Interaktivní Azure Maps sady SDK používají dlaždice 512-pixel a v průměru vygenerují jednu nebo méně transakcí na zobrazení stránky.
- Často platí, že pokud chcete nahradit statické obrázky map z webových služeb Google Maps pomocí Azure Maps webové sady SDK, je jejich cena efektivnější. Sada Azure Maps Web SDK používá dlaždice mapy. Pokud uživatel neposouvá a nezvětšuje mapu, služba často generuje pouze zlomek transakce na jedno načtení mapy. Sada Azure Maps Web SDK obsahuje možnosti pro vypnutí posouvání a přiblížení (v případě potřeby). Sada SDK Azure Maps Web navíc nabízí spoustu dalších možností vizualizace než webová služba statické mapy.
- Azure Maps umožňuje ukládat data z své platformy do Azure. Data je také možné ukládat do mezipaměti jinde po dobu až šesti měsíců podle [podmínek použití](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Tady je několik souvisejících prostředků pro Azure Maps:

- [Stránka s cenami Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure Maps podmínky použití](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (zahrnuté do podmínek služby Microsoft Online Services)
- [Výběr správné cenové úrovně v Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Navrhovaný plán migrace

Následuje plán migrace na vysoké úrovni.

1. Využijte inventarizaci sad SDK a služeb Google Maps, které vaše aplikace používá. Ověřte, že Azure Maps poskytuje alternativní sady SDK a služby.
2. Pokud ho ještě nemáte, vytvořte si předplatné Azure na adrese [https://azure.com](https://azure.com) .
3. Vytvořte účet Azure Maps ([dokumentace](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) a ověřovací klíč nebo Azure Active Directory ([dokumentace](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4. Migrujte kód aplikace.
5. Otestujte migrovaná aplikace.
6. Nasaďte migrovaná aplikace do produkčního prostředí.

## <a name="azure-maps-technical-resources"></a>Azure Maps technické prostředky

Tady je seznam užitečných technických prostředků pro Azure Maps.

- Přehled[https://azure.com/maps](https://azure.com/maps)
- Nápovědě[https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Ukázky kódu pro web SDK:[https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Fóra pro vývojáře:[https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Videa[https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Webový[https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Technický blog:[https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Azure Maps váš názor (UserVoice):[https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Azure Maps Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)

## <a name="migration-support"></a>Podpora migrace

Vývojáři můžou vyhledat podporu migrace prostřednictvím [fór](https://aka.ms/AzureMapsForums) nebo pomocí jedné z mnoha možností podpory Azure:[https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Další kroky

Přečtěte si podrobnosti o migraci aplikace Google Maps pomocí těchto článků:

> [!div class="nextstepaction"]
> [Migrace webové aplikace](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Migrace aplikace pro Android](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Migrace webové služby](migrate-from-google-maps-web-services.md)
