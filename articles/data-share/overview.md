---
title: Co je Azure Data Share?
description: Přečtěte si o tom, jak jednoduše a bezpečně sdílet data pro více zákazníků a partnerů pomocí sdílené složky Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.openlocfilehash: d1665ef3e845491f116174cf1914c38e7cf5c691
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660796"
---
# <a name="what-is-azure-data-share"></a>Co je Azure Data Share?

V dnešním světě se data zobrazují jako klíčový strategický prostředek, který mnoho organizací potřebuje jednoduše a bezpečně sdílet se svými zákazníky a partnery. Existuje mnoho způsobů, jak to zákazníci dnes dělají, včetně FTP, e-mailu, rozhraní API k pojmenování. Organizace můžou snadno ztratit přehled o tom, pomocí kterých data sdíleli. Sdílení dat prostřednictvím FTP nebo zaznamenání vlastní infrastruktury rozhraní API je často nákladné při zřizování a správě. Existují režijní náklady na správu spojené s používáním těchto metod sdílení ve velkém měřítku. 

Mnoho organizací musí být pro data, která sdíleli, nutné. Kromě zodpovědnosti by mohli spousta organizací kontrolovat, spravovat a monitorovat všechna sdílení svých dat jednoduchým způsobem. V dnešním světě, kde se očekává, že se data budou dál rozšiřovat na exponenciální tempo, organizace potřebují jednoduše sdílet velké objemy dat. Zákazníci požadují nejaktuálnější data, aby se zajistilo, že budou moci odvodit včas přehledy.

Azure Data Share umožňuje organizacím jednoduše a bezpečně sdílet data s více zákazníky a partnery. V několika kliknutích můžete zřídit nový účet pro sdílení dat, přidat datové sady a pozvat své zákazníky a partnery do vaší sdílené složky. Poskytovatelé dat vždy ovládají data, která sdíleli. Azure Data Share usnadňuje správu a sledování dat, která byla sdílena, kdy a kým. 

Poskytovatel dat může zůstat v řízení toho, jak se jejich data zpracovávají, a to zadáním podmínek použití pro jejich sdílení dat. Příjemce dat musí tyto podmínky přijmout, aby mohl data přijmout. Poskytovatelé dat můžou určit frekvenci, s jakou jejich příjemci dat obdrží aktualizace. Poskytovatel dat může přístup k novým aktualizacím kdykoli odvolat. 

Azure Data Share pomáhá zdokonalit přehledy tím, že usnadňuje kombinování dat od třetích stran až po obohacení analytických scénářů a scénářů AI. K přípravě, zpracování a analýze dat sdílených pomocí Azure Data Share můžete snadno použít nástroje Power Analytics pro Azure Analytics. 

Poskytovatel dat i příjemce dat musí mít ke sdílení a přijímání dat předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="scenarios-for-azure-data-share"></a>Scénáře pro Azure Data Share

Sdílenou složku Azure můžete použít v mnoha různých oborech. Maloobchodníka může například chtít sdílet poslední bod Sales data se svými dodavateli. Pomocí Azure Data share může prodejce nastavit sdílení dat obsahující body prodeje pro všechny své dodavatele a sdílet prodej na každou hodinu nebo každý den. 

Sdílenou složku Azure můžete také použít k vytvoření Tržiště dat pro konkrétní odvětví. Například státní nebo výzkumné instituce, která pravidelně sdílí data o růstu populace s třetími stranami. 

Dalším případem použití pro sdílenou složku Azure je vytvořit datovou Consortium. Například řada různých výzkumných institucí může sdílet data s jedním důvěryhodným subjektem. Data se analyzují, agreguje nebo zpracovávají pomocí analytických nástrojů Azure a pak se sdílejí se zúčastněnými stranami. 

## <a name="how-it-works"></a>Jak to funguje

Azure Data Share v současné době nabízí sdílení na základě snímků a místní sdílení (ve verzi s omezením verze Preview). 

V případě sdílení na základě snímků se data pohybují z předplatného Azure poskytovatele dat a v rámci předplatného Azure datového příjemce. Jako poskytovatel dat zřizujete sdílenou složku a budete pozvat příjemce ke sdílení dat. Příjemci dat obdrží pozvánku do sdílené složky prostřednictvím e-mailu. Jakmile příjemce dat pozvánku přijme, může aktivovat úplný snímek dat, která jsou s nimi sdílená. Tato data se přijímají do účtu úložiště dat pro uživatele. Příjemci dat můžou dostávat pravidelné a přírůstkové aktualizace dat, která jsou s nimi sdílená, aby vždy měli nejnovější verzi dat. 

Poskytovatelé dat můžou svým spotřebitelům dat postupně aktualizovat data, která s nimi sdílí, prostřednictvím plánu snímků. Plány snímků jsou nabízeny každou hodinu nebo každý den. Když příjemce dat přijme a nakonfiguruje jejich sdílení dat, může se přihlásit k odběru plánu snímku. To je užitečné ve scénářích, kdy se v pravidelných intervalech aktualizují sdílená data a spotřebitel dat potřebuje nejaktuálnější data. 

![tok sdílení dat](media/data-share-flow.png)

Když příjemce dat přijme sdílenou složku, může přijímat data v úložišti dat podle svého výběru. Pokud například poskytovatel dat sdílí data pomocí Azure Blob Storage, příjemce dat může tato data přijímat v Azure Data Lake Store. Podobně platí, že pokud poskytovatel dat sdílí data z Azure SQL Data Warehouse, příjemce dat si může vybrat, jestli chtějí data přijímat, do Azure Data Lake Store, Azure SQL Database nebo Azure SQL Data Warehouse. V případě sdílení ze zdrojů založených na jazyce SQL může příjemce dat také zvolit, zda budou přijímat data v Parquet nebo CSV. 

Místní sdílení je momentálně ve verzi omezené verze Preview pro Azure Průzkumník dat. Poskytovatelé dat můžou sdílet data, kde se nacházejí, bez přesunu dat prostřednictvím symbolického odkazu. Zaregistrujte si omezené verze Preview služby Azure Průzkumník dat [místní sdílení.](https://aka.ms/azuredatasharepreviewsignup) 

## <a name="key-capabilities"></a>Klíčové funkce

Azure Data Share umožňuje zprostředkovatelům dat:

* Sdílení dat ze seznamu [podporovaných úložišť dat](supported-data-stores.md) se zákazníky a partnery mimo vaši organizaci

* Udržujte si přehled o tom, se kterými jste data sdíleli

* Jak často příjemci dat přijímají aktualizace vašich dat

* Umožněte zákazníkům, aby si vyžádají nejnovější verzi vašich dat, jak je potřeba, nebo jim umožněte automatické přijímání přírůstkových změn dat v intervalu, který jste definovali.

Azure Data Share umožňuje příjemcům dat: 

* Zobrazit popis typu sdílených dat

* Zobrazení podmínek použití pro data

* Přijmout nebo odmítnout pozvánku ke sdílené složce Azure Data Share

* Aktivace úplného nebo přírůstkového snímku sdílené složky dat, kterou s vámi organizace sdílí

* Přihlaste se k odběru sdílení dat, abyste získali nejnovější kopii dat prostřednictvím přírůstkové kopie snímku.

* Přijměte data sdílená s vámi do [podporovaného úložiště dat](supported-data-stores.md).

Všechny výše uvedené klíčové funkce jsou podporované prostřednictvím Azure nebo přes rozhraní REST API. Další informace o používání sdílené složky Azure prostřednictvím rozhraní REST API najdete v naší referenční dokumentaci. 

## <a name="security"></a>Zabezpečení

Azure Data Share využívá základní zabezpečení, které Azure nabízí k ochraně dat v klidovém provozu a při přenosu. Data jsou zašifrovaná v klidovém stavu, kde jsou podporovaná podkladovým úložištěm dat. Data se šifrují i při přenosu. Metadata o sdílené složce jsou také šifrována v klidovém stavu a při přenosu. 

Řízení přístupu lze nastavit na úrovni prostředků sdílené složky Azure, aby bylo zajištěno, že k nim mají přístup autorizované aplikace. 

Azure Data Share využívá spravované identity pro prostředky Azure (dříve označované jako MSIs) pro automatickou správu identit v Azure Active Directory. Spravované identity pro prostředky Azure se využívají pro přístup k úložištím dat, která se používají ke sdílení dat. Neexistuje žádná výměna přihlašovacích údajů mezi poskytovatelem dat a příjemcem dat. Další informace najdete na [stránce spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). 


## <a name="supported-regions"></a>Podporované oblasti

Seznam oblastí Azure, které zpřístupňují sdílenou složku Azure Data Share, najdete na stránce [produkty dostupné v oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=data-share/) a ve službě Azure Data Share. 

Azure Data Share neukládá žádná data. Data jsou uložena v podkladovém úložišti dat, které je sdíleno. Pokud například producent dat ukládá svá data do účtu Azure Data Lake Store, který se nachází v Západní USA, kde jsou data uložená. Pokud sdílí data s účtem Azure Storage umístěným v Západní Evropa, data se přenesou přímo na účet Azure Storage umístěný v Západní Evropa. 

Služba Azure Data Share není ve vaší oblasti k dispozici, aby ji bylo možné využívat. Pokud máte například data uložená v účtu Azure Storage umístěném v oblasti, kde není ještě dostupná služba Azure Data Share, můžete tuto službu využít ke sdílení dat. 

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .
