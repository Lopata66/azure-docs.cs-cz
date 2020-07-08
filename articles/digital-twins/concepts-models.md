---
title: Vlastní modely
titleSuffix: Azure Digital Twins
description: Pochopte, jak digitální vlákna Azure používá uživatelsky definované modely k popisu entit ve vašem prostředí.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: a1bb07ee900622b138a556a33469641e4e8310af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392297"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Principy dvojitých modelů v digitálních prozdvojeních Azure

Klíčovou charakteristikou digitálních vláken Azure je možnost definovat vlastní slovník a vytvořit dvojitou graf v rámci podmínek vaší firmy. Tato funkce je k dispozici prostřednictvím uživatelsky definovaných **modelů**. Modely si můžete představit jako podstatná jména v popisu svého světa. 

Model je podobný **třídě** v objektově orientovaném programovacím jazyce, který definuje datový tvar pro jeden konkrétní koncept v reálném pracovním prostředí. Modely mají názvy (například *místnost* nebo *senzor teploty*) a obsahují prvky, jako jsou vlastnosti, telemetrie/události a příkazy, které popisují, co tento typ entity ve vašem prostředí může dělat. Později tyto modely použijete k vytvoření [**digitálních vláken**](concepts-twins-graph.md) , které reprezentují konkrétní entity, které splňují tento popis typu.

Modely se napisují pomocí **digitálního DTDL (Digital vláken Definition Language)** založeného na JSON – ld.  

## <a name="digital-twin-definition-language-dtdl-for-writing-models"></a>DTDL (Digital redefinition Language) pro psaní modelů

Modely pro digitální vlákna Azure jsou definovány pomocí jazyka DTDL (Digital nedefinovaný jazyk). DTDL je založen na JSON-LD a je nezávislý na programovacím jazyce. DTDL není výhradně pro digitální vlákna Azure, ale používá se také k reprezentaci dat zařízení v jiných službách IoT, jako je [IoT technologie Plug and Play](../iot-pnp/overview-iot-plug-and-play.md). Digitální vlákna Azure používá DTDL *verze 2*.

> [!TIP] 
> Ne všechny služby, které používají DTDL, implementují přesně stejné funkce DTDL. Například IoT technologie Plug and Play nepoužívá funkce DTDL, které jsou pro grafy, zatímco digitální vlákna Azure v současné době neimplementují příkazy DTDL. Další informace o funkcích DTDL, které jsou specifické pro funkce digitálních vláken Azure, najdete v části dále v tomto článku týkajícími se [specifických implementací Azure pro digitální vlákna DTDL](#azure-digital-twins-dtdl-implementation-specifics).

Další informace o DTDL obecně najdete v dokumentaci k jejímu specifikaci v GitHubu: [Digital DTDLing Definition Language () – verze 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

## <a name="elements-of-a-model"></a>Prvky modelu

V rámci definice modelu je položka kódu nejvyšší úrovně **rozhraní**. Tím se zapouzdřuje celý model a zbytek modelu je definovaný v rámci rozhraní. 

Rozhraní modelu DTDL může obsahovat nula, jednu nebo mnoho z následujících polí:
* **Vlastnost** -Properties jsou datová pole, která představují stav entity (jako jsou vlastnosti v mnoha objektově orientovaných programovacích jazycích). Na rozdíl od telemetrie, což je událost dat svázaných s časovým limitem, vlastnosti mají záložní úložiště a lze je kdykoli přečíst.
* **Telemetrie** – pole telemetrie představují měření nebo události a často se používají k popisu čtení snímačů zařízení. Telemetrie není uložená na digitálním vlákna; je větší jako datový proud událostí dat, které jsou připraveny k odeslání někam. 
* **Komponenta** – komponenty umožňují sestavit rozhraní modelu jako sestavení jiných rozhraní, pokud chcete. Příkladem součásti je rozhraní FrontCamera (a další *frontCamera* *rozhraní komponenty),* které se používají při definování modelu pro *telefon*. Nejdřív musíte definovat rozhraní pro *frontCamera* , jako by šlo o svůj vlastní model, a pak na něj můžete odkazovat při definování *telefonu*.

    Pomocí komponenty popište něco, co je nedílnou součástí vašeho řešení, ale nepotřebujete samostatnou identitu, a nemusíte ho vytvářet, odstraňovat ani uspořádávat v nezávisle grafu. Pokud chcete, aby entity měly nezávislou existenci v dodaném grafu, reprezentujte je jako samostatné digitální vlákna různých modelů propojených *vztahy* (viz další odrážka).
    
    >[!TIP] 
    >Komponenty lze také použít pro organizaci k seskupení sad souvisejících vlastností v rámci rozhraní modelu. V této situaci si můžete každou komponentu představit jako obor názvů nebo "složka" uvnitř rozhraní.
* Relace **Relationship** vám umožní vyjádřit, jak se může digitální vlákna zapojit do jiných digitálních vláken. Relace mohou představovat různé sémantické významy, jako je například *Contains* ("podlaha obsahuje místnost"), *studená* ("TVK studená místnost"), *isBilledTo* ("kompresor se účtuje uživateli") atd. Relace umožňují řešení poskytovat graf vzájemně souvisejících entit.

> [!NOTE]
> Specifikace DTDL také definuje **příkazy**, které jsou metody, které lze provést na digitálního vlákna (například příkaz reset nebo příkaz pro přepnutí nebo vypnutí ventilátoru). V případě *digitálních vláken Azure se ale příkazy aktuálně nepodporují.*

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure Digital DTDLs – specifické implementace

Aby byl model DTDL kompatibilní s digitálními ovládacími vlákna Azure, musí tyto požadavky splňovat.

* Všechny elementy DTDL nejvyšší úrovně v modelu musí být typu *Interface*. Důvodem je to, že rozhraní API modelu digitálních vláken Azure může přijímat objekty JSON, které reprezentují rozhraní nebo pole rozhraní. V důsledku toho nejsou na nejvyšší úrovni povoleny žádné další typy elementů DTDL.
* DTDL pro digitální vlákna Azure nesmí definovat žádné *příkazy*.
* Funkce digitálních vláken Azure umožňuje pouze jednu úroveň vnoření komponent. To znamená, že rozhraní, které se používá jako součást, nemůže mít samotné součásti. 
* Rozhraní nelze definovat vloženou v jiných rozhraních DTDL; musí být definované jako samostatné entity nejvyšší úrovně s jejich vlastními identifikátory. Až pak jiné rozhraní chce toto rozhraní zahrnout jako komponentu nebo prostřednictvím dědičnosti, může odkazovat na jeho ID.

## <a name="example-model-code"></a>Příklad kódu modelu

Modely s dvojitým typem lze zapsat v libovolném textovém editoru. Jazyk DTDL se řídí syntaxí JSON, takže byste měli ukládat modely s příponou *. JSON*. Použití rozšíření JSON umožní mnoha programovým textovým editorům poskytnout základní kontrolu syntaxe a zvýrazňování pro dokumenty DTDL. K dispozici je také [rozšíření DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) pro [Visual Studio Code](https://code.visualstudio.com/).

Zde je příklad typického modelu, který je napsán jako rozhraní DTDL. Model popisuje Planet, každý s názvem, hmotností a teplotu. Globálním může mít Moons jako satelity a může obsahovat craters.

```json
[
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "Temperature",
        "schema": "double"
      },
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

Pole modelu jsou:

| Pole | Description |
| --- | --- |
| `@id` | Identifikátor modelu. Musí být ve formátu `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | Určuje druh informací, které jsou popsány. Pro rozhraní je typ *rozhraní*. |
| `@context` | Nastaví [kontext](https://niem.github.io/json/reference/json-ld/context/) dokumentu JSON. Modely by měly používat `dtmi:dtdl:context;2` . |
| `displayName` | volitelné V případě potřeby vám umožní model zadat popisný název. |
| `contents` | Všechna zbývající data rozhraní jsou zde umístěna jako pole definic atributů. Každý atribut musí poskytnout `@type` (*vlastnost*, *telemetrie*, *příkaz*, *vztah*nebo *komponentu*) k identifikaci řazení informací o rozhraní, které popisuje, a poté sadu vlastností, které definují skutečný atribut (například `name` a `schema` k definování *vlastnosti*). |

> [!NOTE]
> Všimněte si, že rozhraní komponenty (*Crater* v tomto příkladu) je definováno ve stejném poli jako rozhraní, které ho používá (*globálním*). Komponenty musí být tímto způsobem definovány v volání rozhraní API, aby bylo rozhraní nalezeno.

### <a name="possible-schemas"></a>Možná schémata

Jak na DTDL, schéma pro atributy *vlastností* a *telemetrie* může být standardní primitivní typy –,, `integer` `double` a, `string` `Boolean` a další typy, jako jsou `DateTime` a `Duration` . 

Kromě primitivních typů mohou mít pole *vlastností* a *telemetrie* tyto komplexní typy:
* `Object`
* `Map`
* `Enum`

*Telemetry* Podporují také pole telemetrie `Array` .

### <a name="model-inheritance"></a>Dědičnost modelů

V některých případech můžete chtít model specializovat. Může být například užitečné mít obecnou *místnost*modelu a specializované varianty *ConferenceRoom* a *posilovně*. Pro expresní specializaci DTDL podporuje dědičnost: rozhraní můžou dědit z jednoho nebo více jiných rozhraní. 

Následující příklad znovu představí model *globálním* z předchozího příkladu DTDL jako podtyp většího modelu *CelestialBody* . Jako první se definuje "nadřazený" model a pak se na něj vytvoří podřízený model pomocí pole `extends` .

```json
[
  {
    "@id": "dtmi:com:contoso:CelestialBody;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Celestial body",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "extends": "dtmi:com:contoso:CelestialBody;1",
    "contents": [
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

V tomto příkladu *CelestialBody* přispívá název, hmotnost a teplotu do *globálním*. `extends`Oddíl je název rozhraní nebo pole názvů rozhraní (což umožňuje rozšířit rozhraní, pokud je požadováno, zdědit z více nadřazených modelů).

Po použití dědičnosti zpřístupňuje rozhraní rozšíření všechny vlastnosti z celého řetězce dědičnosti.

Rozhraní rozšíření nemůže změnit žádnou z definic nadřazených rozhraní; dá se do nich přidávat jenom. Také nemůže předefinovat schopnost, která je již definována v některém z jeho nadřazených rozhraní (i v případě, že jsou možnosti definovány jako stejné). Například Pokud nadřazené rozhraní definuje `double` *hmotnost*vlastnosti, rozhraní rozšíření nemůže obsahovat deklaraci *hmotnosti*, a to i v případě, že je také `double` .

## <a name="validating-models"></a>Ověřování modelů

K dispozici je ukázka pro ověřování dokumentů modelu, aby bylo zajištěno, že je DTDL platný. Je postavená na knihovně analyzátorů DTDL a je to Language-nezávislá. Najdete ho tady: [Ukázka validátoru DTDL](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

Nebo, další informace o knihovně analyzátoru, včetně příkladu, jak ji použít přímo, naleznete v tématu [Postupy: analýza a ověření modelů](how-to-use-parser.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si téma Správa modelů pomocí rozhraní DigitalTwinsModels API:
* [Postupy: Správa vlastních modelů](how-to-manage-model.md)

Nebo se dozvíte, jak se vytváří digitální vlákna na základě modelů:
* [Koncepty: digitální vlákna a Dvojitá graf](concepts-twins-graph.md)

