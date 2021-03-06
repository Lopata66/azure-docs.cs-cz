---
title: Úvod do Azure Data Catalog
description: Tento článek obsahuje přehled služby Microsoft Azure Data Catalog, a to včetně jejích funkcí a potíží, na které se zaměřuje. Data Catalog umožňuje všem uživatelům registrovat, objevovat, pochopit a využívat zdroje dat.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: 9b7a5d5ca6cbad897839c8e28c9e3efd1eeabaf0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "68736978"
---
# <a name="what-is-azure-data-catalog"></a>Co je Azure Data Catalog?

Azure Data Catalog je plně spravovaná cloudová služba. Umožňuje uživatelům zjistit zdroje dat, které potřebují, a porozumět zdrojům dat, které hledají. Zároveň Data Catalog pomáhá organizacím vytěžit více z jejich stávajících investic.

Pomocí katalogu Data Catalog může každý uživatel (analytik, odborník přes data nebo vývojář) zdroje dat objevovat, pochopit a využívat. Data Catalog obsahuje crowdsourcingový model metadat a poznámek. Je jediným ústředním místem, kam všichni uživatelé v rámci organizace mohou přispět svými znalostmi a vybudovat datovou komunitu a kulturu.

## <a name="discovery-challenges-for-data-consumers"></a>Problémy zjišťování pro spotřebitele dat

Tradičně je zjišťování zdrojů podnikových dat organický proces založený na kmenových znalostech. Pro společnosti, které chtějí získat maximální hodnotu ze svých informačních prostředků, to představuje mnoho výzev:

* Uživatelé nemusí mít jistotu, že zdroj dat existuje, pokud nepřijde do styku s ním v rámci jiného procesu. Žádné centrální umístění, kde jsou zdroje dat registrovány, neexistuje.
* Pokud uživatel nezná umístění zdroje dat, nemůže se k těmto datům pomocí klientské aplikace připojit. Možnosti využití dat vyžadují, aby uživatelé znali připojovací řetězec nebo cestu.
* Pokud uživatel nezná umístění dokumentace zdroje dat, nemůže zamýšlenému použití dat rozumět. Zdroje dat a dokumentace se mohou nacházet na různých místech a používat se v různých prostředích.
* Pokud mají uživatelé dotazy týkající se informačního prostředku, musí vyhledat odborníka nebo tým odpovědný za příslušná data a zapojit tyto odborníky offline. Mezi daty a odborníky, kteří mají perspektivy používání, neexistuje žádné explicitní spojení.
* Pokud uživatelé nerozumí procesu pro vyžádání přístupu ke zdroji dat, zjištění zdroje dat a jeho dokumentace jim stejně nepomůže k těmto datům získat přístup.

## <a name="discovery-challenges-for-data-producers"></a>Problémy zjišťování pro producenty dat

Přestože se spotřebitelé dat potýkají s těmito dříve popsanými výzvami, uživatelé odpovědní za vytváření a správu informačních prostředků se potýkají s vlastními výzvami:

* Zadávání poznámek ke zdrojům dat s popisnými metadaty je často ztráta času. Klientské aplikace obvykle popisy uložené ve zdroji dat ignorují.
* Vytváření dokumentace pro zdroje dat je také často ztráta času. Udržování dokumentace v synchronizaci se zdroji dat je průběžná zodpovědnost. Uživatelé můžou v dokumentaci, která je zjištěná jako zastaralá, chybět důvěryhodnost.
* Vytváření a správa dokumentace pro zdroje dat je složitá a časově náročná. O to větší je výzva tuto dokumentaci učinit snadno dostupnou pro každého uživatele, který příslušný zdroj dat používá.
* Omezení přístupu ke zdrojům dat a zajištění, aby spotřebitelé dat věděli, jak požádat o přístup, je neustávající výzva.

Když se tyto výzvy zkombinují, představují významnou překážkou pro společnosti, které chtějí podněcovat a podporovat používání a pochopení podnikových dat.

## <a name="azure-data-catalog-can-help"></a>Azure Data Catalog může pomoci

Data Catalog je určen k řešení těchto problémů a pomáhá podnikům získat větší hodnotu ze stávajících prostředků. Data Catalog činí zdroje dat snadno objevitelné a srozumitelné pro uživatele, kteří tato data spravují.

Data Catalog poskytuje službu na principu cloudu, do níž lze zaregistrovat zdroj dat. Data zůstávají uložena ve stávajícím umístění, ale do katalogu Data Catalog se přidá kopie metadat spolu s odkazem na umístění zdroje dat. Tato metadata jsou také indexována, aby byl každý zdroj dat snadno objevitelný prostřednictvím vyhledávání a aby byl srozumitelný uživatelům, kteří ho objevili.

Po zaregistrování zdroje dat lze jeho metadata rozšířit. Metadata může přidat uživatel, který ho zaregistroval, nebo jiní uživatelé v podniku. Každý uživatel může opatřit poznámkami zdroj dat tím, že přidá popisy, značky nebo další metadata, například dokumentaci a procesy pro žádosti o přístup ke zdroji dat. Tato popisná metadata doplňují strukturální metadata (například názvy sloupců a typy dat) zaregistrovaná ze zdroje dat.

Primárním účelem registrace zdrojů dat je zjišťování a porozumění zdrojům, a jejich používání. Podnikoví uživatelé mohou potřebovat data pro business intelligence, vývoj aplikací, datové vědy nebo jiný úkol, ve kterém jsou vyžadována správná data. Mohou využít zkušenosti s objevováním v katalogu Data Catalog, aby rychle našli data, která odpovídají jejich potřebám, pochopili je, vyhodnotili jejich vhodnost pro daný účel a využili je otevřením zdroje dat v upřednostňovaném nástroji. 

Současně mohou uživatelé přispívat do katalogu označováním, dokumentováním a zadáváním poznámek ke zdrojům dat, které jsou již zaregistrovány. Mohou také registrovat nové zdroje dat, které lze poté objevit, pochopit a využít komunitou uživatelů katalogu.

![Možnosti katalogu Data Catalog](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## <a name="learn-more-about-data-catalog"></a>Další informace o katalogu Data Catalog

Další informace o možnostech katalogu Data Catalog naleznete v tématu:

* [Postup registrace zdrojů dat](data-catalog-how-to-register.md)
* [Zjišťování zdrojů dat](data-catalog-how-to-discover.md)
* [Postup přidání poznámek ke zdrojům dat](data-catalog-how-to-annotate.md)
* [Postup dokumentování zdrojů dat](data-catalog-how-to-documentation.md)
* [Jak se připojit ke zdrojům dat](data-catalog-how-to-connect.md)
* [Jak pracovat s velkým objemem dat](data-catalog-how-to-big-data.md)
* [Jak spravovat datové assety](data-catalog-how-to-manage.md)
* [Jak nastavit slovník Business Glossary](data-catalog-how-to-business-glossary.md)
* [Nejčastější dotazy](data-catalog-frequently-asked-questions.md)

## <a name="next-steps"></a>Další kroky

Jak začít s Data Catalog:

* [Rychlý Start: vytvoření Azure Data Catalog](data-catalog-get-started.md)
* [Otevřete Azure Data Catalog](https://www.azuredatacatalog.com)