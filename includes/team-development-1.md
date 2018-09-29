---
title: zahrnout soubor
description: zahrnout soubor
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: fb62468f2746072cea02185dbda022a1a5830fcf
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410441"
---
Zatím jste kód aplikace spouštěli tak, jako byste byli jediným vývojářem, který na aplikaci pracuje. V této části se dozvíte, jak služba Azure Dev Spaces zjednodušuje týmový vývoj:
* Umožňuje týmu vývojářů podle potřeby pracovat ve stejném prostředí, ať už ve sdíleném pracovním prostoru nebo v oddělených pracovních prostorech.
* Podporuje iterace kódu každého vývojáře v izolaci a bez obav z narušování práce ostatních.
* Umožňuje komplexní testování kódu před jeho potvrzením, bez nutnosti vytvářet napodobení nebo simulovat závislosti.

### <a name="challenges-with-developing-microservices"></a>Problémy při vývoji mikroslužeb
V tuto chvíli není ukázková aplikace moc složitá. Když ale při vývoji v reálném světě budete přidávat další služby a vývojový tým se bude zvětšovat, začnou brzy vznikat problémy.

Předpokládejme, že pracujete na službě, která komunikuje s řadou dalších služeb.

- Může se stát, že spouštění všech služeb místně nebude během vývoje reálné. Počítač používaný pro vývoj nemusí mít dostatek prostředků ke spuštění celé aplikace. Anebo třeba vaše aplikace obsahuje koncové body, které musí být veřejně dostupné (aplikace například odpovídá na webhook z aplikace SaaS).

- Můžete se pokusit spustit jenom služby, na kterých jste závislí, ale to znamená, že byste museli podrobně znát všechny závislosti (třeba i závislosti závislostí). Může také nastat situace, že vytvoření a spuštění vašich závislostí nebude snadné, protože jste na nich nepracovali.
- Někteří vývojáři se uchylují k simulacím nebo napodobením řady závislostí svých služeb. Tento přístup někdy může pomoci, ale správa těchto napodobení může brzy vyžadovat svůj vlastní náročný vývoj. Navíc to vede k tomu, že vývojový prostor vypadá zcela jinak než produkční prostředí a můžou se tak vloudit drobné chyby.
- Z toho vyplývá, že jakékoli komplexní testování kódu bude obtížné. Testování integrace se dá věrohodně provést až po potvrzení, což znamená, že se problémy projeví později ve vývojovém cyklu.

![](../articles/dev-spaces/media/common/microservices-challenges.png)


### <a name="work-in-a-shared-dev-space"></a>Práce ve sdíleném vývojovém prostoru
Pomocí služby Azure Dev Spaces můžete nastavit *sdílený* vývojový prostor v Azure. Každý vývojář se může zaměřit jenom na svoji část aplikace a může iterativně vyvíjet *kód před potvrzením* ve vývojovém prostoru, který už obsahuje všechny ostatní služby a cloudové prostředky, na kterých jsou příslušné scénáře závislé. Závislosti jsou vždycky aktuální a vývojáři pracují způsobem, který odpovídá produkčnímu prostředí.

### <a name="work-in-your-own-space"></a>Práce ve vašem vlastním prostoru
Než je kód při vývoji pro vaši službu připravený k odevzdání, často není ve funkčním stavu. Pořád ho iterativně vytváříte, testujete ho a experimentujete s různými řešeními. Služba Azure Dev Spaces nabízí koncept **prostoru**, který umožňuje pracovat v izolaci a bez obav z narušení práce jiných členů vašeho týmu.

> [!Note]
> Než budete pokračovat, zavřete všechna okna VS Code u obou služeb a potom spusťte příkaz `azds up -d` ve všech kořenových složkách služby. (Toto je omezení verze Preview.)

Podívejme se podrobněji na to, kde služby momentálně běží. Spusťte příkaz `azds list-up` a zobrazí se podobný výstup:

```
Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      default   Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      default   Pod      3m ago   Running
webfrontend                   default   Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  default   Pod      1m ago   Running
```

Ve sloupci DevSpace se zobrazuje, že obě služby běží v prostoru s názvem `default`. Kdokoli, kdo otevře veřejnou adresu URL a přejde k webové aplikaci, vyvolá zapsanou cestu kódu, která spouští obě služby. Nyní předpokládejme, že chcete pokračovat ve vývoji `mywebapi`. Jak provedete změny kódu a otestujete je bez toho, abyste přerušili práci ostatních vývojářů, kteří používají stejné vývojové prostředí? Vytvoříte si k tomu vlastní prostor.

### <a name="create-a-dev-space"></a>Vytvoření vývojového prostoru
Pokud chcete používat vlastní verzi `mywebapi` v jiném prostoru než `default`, můžete si vytvořit vlastní prostor pomocí následujícího příkazu:

``` 
azds space select --name scott
```

Po zobrazení výzvy jako **nadřazený vývojový prostor** vyberte `default`. To znamená, že náš nový prostor `default/scott` bude odvozený z prostoru `default`. Za chvíli si ukážeme, jak nám to pomůže s testováním. 

V příkladu výše bylo pro nový prostor použito vlastní jméno, aby ostatní vývojáři věděli, že se jedná o prostor, ve kterém pracuje daný vývojář. Název si ale můžete zvolit podle toho, jak vám to vyhovuje a jak potřebujete, například „sprint4“ nebo „demo“.

Spuštěním příkazu `azds space list` zobrazíte seznam všech prostorů ve vývojovém prostředí. Vedle aktuálně vybraného prostoru se zobrazí hvězdička (*). V našem případě se při vytvoření automaticky vybral prostor default/scott. Kdykoli si můžete vybrat jiný prostor pomocí příkazu `azds space select`.
