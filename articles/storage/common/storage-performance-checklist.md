---
title: Azure kontrolní seznam pro výkon a škálovatelnost úložiště | Dokumentace Microsoftu
description: Kontrolní seznam osvědčených postupů pro použití s Azure Storage v vývoj výkonných aplikací.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/07/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c5bbd19969349965ea20fa4cfc09e10119a9a86c
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295753"
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Kontrolní seznam výkonu a škálovatelnosti Microsoft Azure Storage

Od verze služby Microsoft Azure Storage Microsoft vyvinul několik osvědčených postupů pro použití těchto služeb v podobě výkonných a tento článek slouží ke sloučení vašich nejdůležitějších z nich do kontrolní seznam ve stylu seznamu. Záměr tohoto článku je k ověření, že používají osvědčených postupů pomocí služby Azure Storage vývojářům aplikací pomáhá a aby to pomohl ostatním identifikovat dalších osvědčených postupů, zvažte využití. Tento článek nebude pokoušet o zahrnují všechny možné optimalizace výkonu a škálovatelnosti – ty, které jsou malá v jejich dopadu nebo lze široce aplikovat vyloučí. Do té míry, že během návrhu lze předpovídat chování aplikace, je vhodné ponechat v paměti již v rané fázi k Vyhněte se návrhům, které se spustí o problémech s výkonem.  

Všem vývojářům aplikací pomocí služby Azure Storage zabere čas v tomto článku a zkontrolujte, že jejich aplikace následuje všech osvědčených postupů uvedených níže.  

## <a name="checklist"></a>Kontrolní seznam

Tento článek slouží k uspořádání osvědčených postupů do následujících skupin. Osvědčené postupy platí pro:  

* Všechny služby Azure Storage (objekty BLOB, tabulky, fronty a soubory)
* Objekty blob
* Tabulky
* Fronty  

| Hotovo | Oblast | Category | Otázka |
| --- | --- | --- | --- |
| &nbsp; | Všechny služby |Cíle škálovatelnosti |[Je vaše aplikace navržená tak, aby blíží cíle škálovatelnosti?](#subheading1) |
| &nbsp; | Všechny služby |Cíle škálovatelnosti |[Slouží k povolení lepší služby Vyrovnávání zatížení svých zásad vytváření názvů?](#subheading47) |
| &nbsp; | Všechny služby |Sítě |[Mají zařízení na straně klienta dostatečně velkou šířku pásma a nízkou latenci pro zajištění výkonu potřeba?](#subheading2) |
| &nbsp; | Všechny služby |Sítě |[Mají zařízení na straně klienta dostatečně vysokou kvalitu odkaz?](#subheading3) |
| &nbsp; | Všechny služby |Sítě |[Klientská aplikace se nachází "u" účet úložiště?](#subheading4) |
| &nbsp; | Všechny služby |Distribuce obsahu |[Používáte pro distribuci obsahu CDN?](#subheading5) |
| &nbsp; | Všechny služby |Přímý přístup do klienta |[Používáte umožňující přímý přístup k úložišti místo proxy SAS a CORS?](#subheading6) |
| &nbsp; | Všechny služby |Caching |[Je vaše aplikace ukládání do mezipaměti dat, který se opakovaně používá změny jen zřídka?](#subheading7) |
| &nbsp; | Všechny služby |Caching |[Je vaše aplikace dávkové aktualizace (je do mezipaměti na straně klienta a odesláním v větší sady)?](#subheading8) |
| &nbsp; | Všechny služby |.NET Configuration |[Nakonfigurovali jste vašeho klienta pro použití dostatečný počet souběžných připojení?](#subheading9) |
| &nbsp; | Všechny služby |.NET Configuration |[Nakonfigurovali jste .NET použijte dostatečný počet vláken?](#subheading10) |
| &nbsp; | Všechny služby |.NET Configuration |[Používáte .NET 4.5 nebo novější, která zvýšil uvolňování paměti?](#subheading11) |
| &nbsp; | Všechny služby |Paralelismus |[Můžete zajistíte, že paralelismu ohraničená odpovídajícím způsobem tak, aby není přetížení vaše možnosti klienta nebo cíle škálovatelnosti?](#subheading12) |
| &nbsp; | Všechny služby |Nástroje |[Používáte nejnovější verzi Microsoft klientské knihovny a nástroje jsou poskytovány?](#subheading13) |
| &nbsp; | Všechny služby |Opakování |[Jsou pomocí exponenciálního omezení rychlosti zásady pro omezení chyb a vypršení časových limitů opakování?](#subheading14) |
| &nbsp; | Všechny služby |Opakování |[Je vaše aplikace nejkonkrétnější – vyhněte opakování neopakovatelnou chyby?](#subheading15) |
| &nbsp; | Objekty blob |Cíle škálovatelnosti |[Máte velký počet klientů přistupujících k jeden objekt současně?](#subheading46) |
| &nbsp; | Objekty blob |Cíle škálovatelnosti |[Je vaše aplikace zůstává v rámci šířky pásma nebo operace cíle škálovatelnosti pro jeden objekt blob?](#subheading16) |
| &nbsp; | Objekty blob |Kopírování objektů BLOB |[Je kopírování objektů BLOB jsou efektivním způsobem?](#subheading17) |
| &nbsp; | Objekty blob |Kopírování objektů BLOB |[Používáte pro hromadné kopie objektů BLOB AzCopy?](#subheading18) |
| &nbsp; | Objekty blob |Kopírování objektů BLOB |[Používáte Azure Import/Export pro přenos velkých objemů dat?](#subheading19) |
| &nbsp; | Objekty blob |Používání metadat |[Jsou ukládání často používaných metadata o objektech BLOB ve svých metadatech?](#subheading20) |
| &nbsp; | Objekty blob |Rychlé nahrávání |[Při pokusu nahrát objekt blob jeden rychle, patří vámi nahrávaný bloky současně?](#subheading21) |
| &nbsp; | Objekty blob |Rychlé nahrávání |[Při pokusu o nahrání rychle velký počet objektů BLOB, patří vámi nahrávaný objekty BLOB současně?](#subheading22) |
| &nbsp; | Objekty blob |Opravte typ objektu Blob |[Používáte objekty BLOB stránky nebo objekty BLOB bloku v případě potřeby?](#subheading23) |
| &nbsp; | Tabulky |Cíle škálovatelnosti |[Se blíží cíle škálovatelnosti pro entity za sekundu?](#subheading24) |
| &nbsp; | Tabulky |Konfigurace |[Používáte JSON pro vaše požadavky na tabulky?](#subheading25) |
| &nbsp; | Tabulky |Konfigurace |[Můžete vypnout Nagle ke zlepšení výkonu malých požadavků?](#subheading26) |
| &nbsp; | Tabulky |Tabulky a oddíly |[Jste správně data oddílů?](#subheading27) |
| &nbsp; | Tabulky |Aktivních oddílů |[Vyloučení jsou jen pro připojení a jen pro předřaďte vzory?](#subheading28) |
| &nbsp; | Tabulky |Aktivních oddílů |[Jsou vaše vkládání/aktualizace rozloženy mnoho oddílů?](#subheading29) |
| &nbsp; | Tabulky |Rozsah dotazu |[Vytvořili jste schéma umožňující bodových dotazů, který se má použít ve většině případů a dotazy na tabulku pro měly používat střídmě?](#subheading30) |
| &nbsp; | Tabulky |Hustota dotazu |[Provést kontrolu, obvykle pouze vaše dotazy a vrátí řádky, které bude aplikace používat?](#subheading31) |
| &nbsp; | Tabulky |Omezení vrácených dat. |[Používáte filtrování vrací entity, které nejsou potřeba, aby?](#subheading32) |
| &nbsp; | Tabulky |Omezení vrácených dat. |[Používáte vrátí vlastnosti, které nejsou potřeba, aby projekce?](#subheading33) |
| &nbsp; | Tabulky |Denormalizace |[Máte tak, že při pokusu o získání dat se vyhněte neefektivní dotazy nebo požadavky na více čtení Nenormalizovaná data?](#subheading34) |
| &nbsp; | Tabulky |Příkaz Insert/Update/Delete |[Jsou vám dávkové zpracování požadavků, které je potřeba využívat transakce nebo můžete udělat ve stejnou dobu ke snížení doby odezvy?](#subheading35) |
| &nbsp; | Tabulky |Příkaz Insert/Update/Delete |[Se můžete vyhnout, načtení entity jenom k určení, jestli se má volat, insert nebo update?](#subheading36) |
| &nbsp; | Tabulky |Příkaz Insert/Update/Delete |[Zvážení ukládání řady dat, která se často načíst společně v jedné entity jako vlastnosti namísto více entit](#subheading37) |
| &nbsp; | Tabulky |Příkaz Insert/Update/Delete |[Entity, které budou načítat vždy společně a je možné psát v dávkách (například data časových řad) mají je nepovažuje za místo tabulky objektů BLOB?](#subheading38) |
| &nbsp; | Fronty |Cíle škálovatelnosti |[Se blíží cíle škálovatelnosti pro zpráv za sekundu?](#subheading39) |
| &nbsp; | Fronty |Konfigurace |[Můžete vypnout Nagle ke zlepšení výkonu malých požadavků?](#subheading40) |
| &nbsp; | Fronty |Velikost zprávy |[Jsou vaše zprávy compact ke zlepšení výkonu fronty?](#subheading41) |
| &nbsp; | Fronty |Hromadné načtení |[Načítá se více zpráv v rámci jedné operace "Get"?](#subheading42) |
| &nbsp; | Fronty |Frekvence cyklického dotazování |[Jsou vám dotazování dostatečně často na snížení latence zjištěné aplikace?](#subheading43) |
| &nbsp; | Fronty |Aktualizace zprávy |[Používáte UpdateMessage k ukládání průběh zpracování zpráv, jak se vyhnout museli znovu zpracovat celá zpráva, pokud dojde k chybě?](#subheading44) |
| &nbsp; | Fronty |Architektura |[Používáte pro zajištění větší škálovatelnost celého aplikace udržováním dlouho běžící úlohy z kritické cesty a nezávisle škálovat pak fronty?](#subheading45) |

## <a name="allservices"></a>Všechny služby

Tato část obsahuje seznam osvědčených postupech, které se vztahují na používání služby Azure Storage (objekty BLOB, tabulky, fronty nebo soubory).  

### <a name="subheading1"></a>cíle škálovatelnosti

Azure Storage, samotný má omezení 250 účtů úložiště na oblast a předplatné. Pokud dosáhnete tohoto limitu, v dané kombinaci předplatného a oblasti již nebudete moct vytvářet účty úložiště.

Každou ze služeb Azure Storage má cíle škálovatelnosti pro kapacita (GB), rychlost transakcí a šířka pásma. Pokud vaše aplikace blíží nebo přesahují některý z cíle škálovatelnosti, setkat transakce vyšší latencí nebo omezení šířky pásma. Pokud službu úložiště omezuje propustnost vaší aplikace, služby zahájí vrátit "503 Server je zaneprázdněn" nebo "časový limit 500 operace" kódy chyb pro některé transakce služby storage. Tato část popisuje obecný přístup k zejména zabývají cíle škálovatelnost a cíle škálovatelnosti šířky pásma. Novější, které se zabývají služby úložiště v jednotlivých částech cíle škálovatelnosti v kontextu této konkrétní služby:  

* [BLOB šířky pásma a počet požadavků za sekundu](#subheading16)
* [Tabulka entity za sekundu](#subheading24)
* [Fronta zpráv za sekundu](#subheading39)  

#### <a name="sub1bandwidth"></a>Cíle škálovatelnosti šířky pásma pro všechny služby

V době psaní cíle šířky pásma v USA pro účet geograficky redundantní úložiště (GRS) jsou 10 gigabitů za sekundu (GB/s) pro příchozí přenos dat (data odeslaná do účtu úložiště) a 20 GB/s pro výchozí přenos dat (data odeslaná z účtu úložiště). Pro účet místně redundantního úložiště (LRS), omezení jsou vyšší – 20 GB/s pro příchozí přenos dat a 30 GB/s pro výchozí přenos.  Omezení šířky pásma mezinárodní může být nižší a můžete najít na naší [stránky cíle škálovatelnosti](https://msdn.microsoft.com/library/azure/dn249410.aspx).  Další informace o možnostech redundance úložiště najdete v článku odkazy v užitečné zdroje informací.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>Co dělat, když se blíží cíle škálovatelnosti

Pokud se blíží se limit účty úložiště, které můžete použít v kombinaci konkrétní předplatné nebo oblast, vyhodnoťte svoje aplikace a používání účtů úložiště a určit, pokud platí kterákoli z těchto podmínek.

* Použít účty úložiště jako nespravované disky a přidáte tyto disky do virtuálních počítačů. V tomto scénáři doporučujeme použít [spravované disky](../../virtual-machines/windows/managed-disks-overview.md), jak zpracovat škálovatelnosti úložiště disku pro vás aniž byste je museli vytvářet a spravovat jednotlivé účty.
* Na základě za zákazníků pro účely izolace dat pomocí jednoho účtu úložiště. V tomto scénáři doporučujeme použít kontejnery úložiště pro každý zákazník, nikoli celý účet úložiště. Úložiště Azure teď umožňuje nastavit řízení přístupu na základě rolí na za [kontejneru základ](storage-auth-aad-rbac-portal.md).
* Použití více účtů úložiště do horizontálního oddílu pro větší škálovatelnost příchozí a výchozí přenos dat/vstupně-výstupních operací/kapacity. V tomto scénáři, pokud je to možné, doporučujeme využít výhod [zvýšit limity](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) o účtech úložiště úrovně standard, abyste snížili počet účtů úložiště potřebné pro vaši úlohu.

Pokud vaše aplikace se blíží cíle škálovatelnosti pro jeden účet úložiště, vezměte v úvahu přijetím jedné z následujících postupů:  

* Zvažte na zatížení, které způsobí, že vaše aplikace přístup nebo překročení cíle škálovatelnosti. Můžete ho jinak na použití menší šířku pásma nebo kapacity nebo méně transakce návrhu?
* Pokud aplikace musí být delší než jeden z cílů škálovatelnosti, měli byste vytvořit více účtů úložiště a oddíl vašich aplikačních dat napříč více účtů úložiště. Pokud používáte tento vzor, je nutné navrhnout aplikaci tak, že můžete přidat další účty úložiště v budoucnosti pro vyrovnávání zatížení. V době psaní každé předplatné Azure, může mít až 250 účtů úložiště v jedné oblasti (nasazené pomocí modelu Azure Resource Manager).  Účty úložiště také mít žádné náklady než využití z hlediska uložených dat, transakcí provedených nebo přenesená data.
* Pokud vaše aplikace narazí na šířku pásma cíle, vezměte v úvahu komprese dat v klientovi ke snížení šířky pásma potřebné k odesílání dat do služby storage.  I když to může snížit využití šířky pásma a zlepšit výkon sítě, můžete mít také některé negativní dopad.  By se měl vyhodnotit dopad na výkon této z důvodu požadavků na další zpracování pro kompresi a dekompresi dat v klientovi. Kromě toho ukládání komprimovaných dat může to ztížit k řešení potíží, protože může být obtížnější, chcete-li zobrazit uložená data pomocí standardních nástrojů.
* Pokud vaše aplikace narazí na cíle škálovatelnosti, zajistěte, že používáte exponenciálního omezení rychlosti pro opakování (viz [opakování](#subheading14)).  Je lepší Ujistěte se, že nikdy přístup (s použitím jedné z výše uvedených metod) cíle škálovatelnosti, ale tím se zajistí, že vaše aplikace nebude právě i nadále pokoušet rychle, provádění omezování horší.  

#### <a name="useful-resources"></a>Užitečné materiály

Následující odkazy obsahují další podrobnosti o cíle škálovatelnosti:

* Zobrazit [Azure Storage škálovatelnost a cíle výkonnosti](storage-scalability-targets.md) informace o cíle škálovatelnosti.
* Zobrazit [replikace Azure Storage](storage-redundancy.md) a v blogovém příspěvku [možnosti redundance Azure Storage a geograficky redundantní úložiště s přístupem pro čtení](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) informace o možnostech redundance úložiště.
* Aktuální informace o cenách pro Azure services najdete v tématu [ceny za Azure](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Zásady vytváření názvů oddílů

Azure Storage používá založený na rozsahu schématu oddílů vzali na škálování a vyrovnávání zatížení systému. Klíč oddílu (účet + kontejner objektů blob.) se používá k oddílu, který data do oblastí a tyto rozsahy jsou vyrovnávání zatížení napříč systému. To znamená, že zásady vytváření názvů jako je například řazení slov (například *mypayroll*, *myperformance*, *myemployees*atd) nebo pomocí časová razítka ( *log20160101*, *log20160102*, *log20160102*atd) bude řešení k oddílům se potenciálně společně umístěný na stejném serveru oddílu, dokud operace služby Vyrovnávání zatížení je rozdělí do menších rozsahy. Například všechny objekty BLOB v kontejneru je možné dodávat jediný server až do zatížení těchto objektů BLOB vyžaduje další znovu vyrovnávání rozsahů oddílů. Obdobně skupinu lehce načíst účty s jejich názvy uspořádané podle lexikální mohou být obsluhovány jediný server až do zatížení na jednu nebo všechny tyto účty požadují, aby se rozdělit mezi několik serverů oddíly. Každou operaci Vyrovnávání zatížení může mít vliv latence volání úložiště během operace. V systému možnost zpracování náhlého provozu do oddílu je omezená škálovatelnost serveru jeden oddíl dokud operace služby Vyrovnávání zatížení zejména kopance v a znovu vyrovnává rozsah klíče oddílu.

Můžete použít některé osvědčené postupy, abyste snížili četnost těchto operací.  

* Pokud je to možné použijte větší Put Blob nebo Vložit blok velikosti (větší než 4 MiB pro standardní účty a je větší než 256 KiB pro účty premium) pro aktivaci vysoce propustné Block Blob (HTBB). HTBB poskytuje vysoký výkon zpracování příjmu, které nemá vliv pojmenování oddílů.
* Zásady vytváření názvů, který používáte pro účty, kontejnery, objekty BLOB, tabulek a front, pečlivě zkontrolujte. Vezměte v úvahu předpony názvy objektů blob, kontejneru nebo účtu 3 číslice hash pomocí hashovací funkce, která nejlépe vyhovuje vašim potřebám.  
* Můžete uspořádat data pomocí časová razítka nebo číselné identifikátory, budete muset zajistit, že nepoužíváte vzory provozu nabízí jen možnost připojovat (nebo jen předřaďte). Tyto modely nejsou vhodné pro rozsah – na základě dělení systému, a může vést k veškerý provoz, že přejdete do jednoho oddílu a efektivně omezení systému z vyrovnávání zatížení. Například pokud máte každodenní operace, které používají objekt blob s časovým razítkem, jako *RRRRMMDD*, pak všechny přenosy pro každodenní operace směřuje na jeden objekt, který je poskytovaný serverem jednoho oddílu. Podívejte se na, jestli na omezení objektů blob na oddíl omezení vašim potřebám a rozdělení tato operace do více objektů BLOB v případě potřeby. Podobně pokud uchováváte data časových řad v tabulkách, vše, co může být provoz přesměruje na poslední část klíče oboru názvů. Pokud je třeba použít časové razítko nebo číselnými ID, předponu ID s křížkem 3 číslice nebo v případě časové razítko, jako předponu sekundová část času *ssyyyymmdd*. Pokud probíhají pravidelně výpis a operace dotazování, vyberte hashovací funkce, které omezí počet dotazů. V jiných případech může být předponu náhodné.  
* Další informace o dělení schéma používané ve službě Azure Storage najdete v tématu [služby Azure Storage: Služby s vysokou dostupností cloudového úložiště se silnou konzistenci](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

### <a name="networking"></a>Sítě

Při volání rozhraní API věci, často omezení fyzické síťové aplikace mít významný dopad na výkon. Následující popisují některá omezení, které uživatelé mohou nastat.  

#### <a name="client-network-capability"></a>Možnost klienta sítě

##### <a name="subheading2"></a>Propustnost

Pro šířku pásma problém je často možnosti klienta. Například, když jeden účet úložiště může zpracovávat 10 GB/s nebo více z příchozího přenosu dat (viz [cíle škálovatelnosti šířky pásma](#sub1bandwidth)), rychlost sítě v instanci Role pracovního procesu "Malé" Azure je jenom zvládne přibližně 100 MB/s. Větší Azure instance mají síťové adaptéry s větší kapacitou, měli byste zvážit použití více virtuálních počítačů nebo větší instance, pokud budete potřebovat vyšší limity sítě z jednoho počítače. Pokud spouštíte služby Storage z aplikace v místním prostředí, pak stejné pravidlo platí: Možnosti sítě klientském zařízení a síťové připojení k umístění služby Azure Storage a je buď podle potřeby vylepšit nebo návrh vašeho aplikace pro spolupráci v rámci jejich schopnosti.  

##### <a name="subheading3"></a>Odkaz kvality

Stejně jako u jakékoli využití sítě, mějte na paměti, že síťové podmínky, což vede k chybám a ztrátě paketů, dojde ke zpomalení efektivní propustnost.  Pomocí WireShark nebo NetMon může pomoci při diagnostice tento problém.  

##### <a name="useful-resources"></a>Užitečné materiály

Další informace o přidělené šířky pásma a velikosti virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů s Windows](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nebo [velikosti virtuálního počítače s Linuxem](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>umístění

V distribuovaném prostředí umístění klienta blízko serveru poskytuje nejlepší výkon. Pro přístup k Azure Storage s nejnižší latenci, je nejlepší umístění pro vašeho klienta v rámci stejné oblasti Azure. Například pokud máte web služby Azure, která používá službu Azure Storage, byste měli vyhledat je v rámci jedné oblasti (například USA – západ nebo jihovýchodní Asie). To snižuje latenci a náklady – v době psaní využití šířky pásma v rámci jedné oblasti je zdarma.  

Pokud vaše klientské aplikace nejsou hostované v rámci Azure (například aplikace pro mobilní zařízení nebo místní služby enterprise), pak znovu umístění účtu úložiště do oblasti blízko zařízení, která bude získávat přístup, bude obecně snížit latenci. Pokud vaši klienti jsou široce distribuovat (pro příklad, některé v Severní Americe a některé v Evropě), pak byste měli zvážit použití více účtů úložiště: jeden umístěný v oblasti Severní Ameriky a druhý v evropských oblastech. To vám pomůže snížit latenci pro uživatele v obou oblastech. Tento přístup je snáze se implementace, pokud ale aplikace ukládá data jsou specifické pro jednotlivé uživatele a nevyžaduje, aby replikace dat mezi účty úložiště.  Pro širokou distribuci obsahu se doporučuje sítě CDN – naleznete v části Další podrobnosti.  

### <a name="subheading5"></a>Distribuce obsahu

V některých případech aplikace potřebuje k poskytování stejný obsah pro mnoho uživatelů (například produktu ukázkové video použít na domovské stránce webu), umístěný ve stejné nebo několika oblastech. V tomto scénáři by měl používat Content Delivery Network (CDN) jako je Azure CDN a CDN by použití služby Azure storage jako zdroj dat. Na rozdíl od účtu služby Azure Storage, který existuje v jedné oblasti a, který nelze doručovat obsah s nízkou latencí do jiných oblastí Azure CDN používá serverech v několika datových centrech po celém světě. Kromě toho sítě CDN podporují obvykle mnohem vyšší omezení odchozího přenosu dat než na jeden účet úložiště.  

Další informace o Azure CDN najdete v tématu [Azure CDN](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>Pomocí SAS a CORS

Když budete potřebovat k autorizaci kódu, jako je JavaScript v prohlížeči uživatele nebo aplikaci mobilního telefonu pro přístup k datům ve službě Azure Storage, jedním z přístupů je použití aplikace ve webové roli jako proxy server: ověřuje uživatele zařízení s webovou rolí , který pak povoluje přístup k prostředkům úložiště. Tímto způsobem vyhnete úniku klíče účtu úložiště na nezabezpečené zařízení. Ale to umístí významné režijní náklady na webové role vzhledem k tomu, že všechna data přenášená mezi zařízení uživatele a služba úložiště musí projít přes webové role. Můžete vyhnout použitím webové role jako proxy pro službu storage pomocí sdílených přístupových podpisů (SAS), někdy společně s záhlaví sdílení prostředků mezi zdroji (CORS). Pomocí SAS, můžete povolit zařízení uživatele, aby požadavků přímo do služby úložiště pomocí tokenu omezený přístup. Například pokud chce uživatel odeslat fotografii do vaší aplikace, webové role můžete generovat a odeslat do zařízení uživatele token SAS, který uděluje oprávnění k zápisu do konkrétní objekt blob nebo kontejneru pro následujících 30 minut (po jejichž uplynutí SAS vyprší platnost tokenu).

Za normálních okolností neumožní prohlížeči JavaScript na stránce hostovány webu na jednu doménu, abyste prováděli konkrétní operace, jako je například "Vložit" do jiné domény. Například pokud hostovat webovou roli na "contosomarketing.cloudapp.net" a chcete nahrát objekt blob do účtu úložiště na "contosoproducts.blob.core.windows.net" pomocí jazyka JavaScript na straně klienta, prohlížečů "zásada stejného zdroje" Zakázat tuto operaci. CORS je funkce, prohlížeče, která umožňuje cílové doméně (v tomto případě účtu úložiště) do prohlížeče komunikovat se, že důvěřuje požadavků pocházejících ve zdrojové doméně (v tomto případě webová role).  

Obě tyto technologie vám může pomoct vyhnout nepotřebné zatížení (a kritická místa) ve webové aplikaci.  

#### <a name="useful-resources"></a>Užitečné materiály

Další informace o SAS najdete v tématu [sdílené přístupové podpisy, část 1: Vysvětlení modelu SAS](../storage-dotnet-shared-access-signature-part-1.md).  

Další informace o CORS, najdete v části [podporu sdílení prostředků mezi zdroji (CORS) pro služby Azure Storage](https://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Caching

#### <a name="subheading7"></a>Získání dat

Po získání dat ze služby je obecně lepší než dvakrát pro spolupráci. Podívejte se na příklad z webové aplikace MVC s ve webové roli, která již načtena 50 MB blob ze služby storage, která bude sloužit jako obsah pro uživatele. Aplikace může potom získá tohoto stejného objektu blob pokaždé, když si uživatel vyžádá ji nebo ji může ukládat do mezipaměti je místně na disk a opakovaně používat jeho verzi v mezipaměti pro následné požadavky uživatelů. Kromě toho vždy, když si uživatel vyžádá data, aplikace by mohla vydávání příkazů GET s podmíněnou hlavičku pro čas změny, které by se tak získání celého objektu blob, pokud byl změněn. Práce s entitami tabulku můžete použít tento stejný vzor.  

V některých případech se může rozhodnout, že vaše aplikace můžete předpokládat, že objekt blob zůstává v platnosti na krátkou dobu po jeho načtení a, že během tohoto období aplikace není potřeba zkontrolovat, pokud byl upraven objekt blob.

Konfigurace, vyhledávání a další data, která se vždycky použijí aplikací jsou skvělými kandidáty pro ukládání do mezipaměti.  

Příklad toho, jak získat objekt blob vlastnosti zjišťování datum poslední změny pomocí .NET, naleznete v tématu [nastavení a načtení vlastností a metadat](../blobs/storage-properties-metadata.md). Další informace o podmíněné soubory ke stažení najdete v tématu [podmíněně aktualizovat místní kopii objektu Blob](https://msdn.microsoft.com/library/azure/dd179371.aspx).  

#### <a name="subheading8"></a>Nahrávání dat do dávek

V některých scénářích aplikace můžete agregovat data místně a pravidelně nahrajte ji ve službě batch namísto odesílání každá část dat okamžitě. Například webové aplikace mohou mít soubor protokolu aktivit: aplikace by buď nahrát podrobnosti o každé aktivitě, protože se stane jako tabulka entity (která vyžaduje mnoho operací úložiště), nebo podrobnosti o aktivitě může uložit do místního souboru protokolu a pak všechny podrobnosti o aktivitě pravidelně nahrajte jako souboru s oddělovači do objektu blob. Pokud každá položka protokolu má velikost 1 KB, můžete nahrát tisíců v rámci jedné transakce "Put Blob" (můžete nahrát objekt blob velikost v rámci jedné transakce až 64 MB). Pokud v místním počítači dojde k chybě před nahrávání, bude případně může přijít o data protokolu: musí vývojář aplikace návrhu pro možnost zařízení klienta nebo chyby nahrávání.  Pokud se data aktivit je potřeba stáhnout pro časových rozpětí (jenom jedna aktivita), se doporučují objekty BLOB přes tabulky.

### <a name="net-configuration"></a>Konfigurace rozhraní .NET

Pokud používáte rozhraní .NET Framework, tato část obsahuje seznam několika rychlé nastavení, které vám umožní provést výrazné zlepšení výkonu.  Pokud používáte jiné jazyky, zkontrolujte, pokud podobně jako koncepty platí ve zvoleném jazyce.  

#### <a name="subheading9"></a>Zvyšte výchozí limit připojení

Následující kód v .NET, zvyšuje výchozí limit připojení (což je obvykle 2 v prostředí klienta nebo 10 v prostředí serveru) do 100. Obvykle byste měli nastavit hodnotu na přibližně počet podprocesů používaný vaší aplikací.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Je nutné nastavit limit připojení před otevřením jakékoliv připojení.  

Jiných programovacích jazycích najdete v daném jazyce dokumentaci a zjistit, jak nastavit limit připojení.  

Další informace naleznete v příspěvku blogu [webové služby: Souběžná připojení](https://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="subheading10"></a>Použití synchronního kódu se asynchronní úlohy zvýšení minimální počet vláken

Tento kód se zvýší minimální počet vláken ve fondu vláken:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Další informace najdete v tématu [ThreadPool.SetMinThreads metoda](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Využijte .NET 4.5 a vyšší uvolňování paměti

Pomocí rozhraní .NET 4.5 nebo novější klientské aplikace využít k vylepšení výkonu v uvolňování paměti serveru.

Další informace najdete v článku [přehled o vylepšení výkonu v rozhraní .NET 4.5](https://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Bez vazby paralelismu

Paralelismus mohou být ideální pro výkon, buďte opatrní při použití bez vazby paralelismus (bez omezení počtu vláken a/nebo paralelní požadavky) k ukládání nebo stahování dat pomocí několika pracovních procesů pro přístup k více oddílů (kontejnery, fronty, nebo oddíly tabulky) v rámci stejného účtu úložiště nebo pro přístup k více položek do stejného oddílu. Pokud paralelismus je bez vazby, vaše aplikace může být delší než možnosti klienta zařízení nebo škálovatelnosti účtu úložiště, zaměřuje omezování a výsledkem je delší latence.  

### <a name="subheading13"></a>Klientské knihovny pro úložiště a nástroje

Vždy používejte nejnovější Microsoft poskytuje klientské knihovny a nástroje. V době psaní jsou klientské knihovny pro .NET, Windows Phone, Windows Runtime, Java a C++ k dispozici, stejně jako verze preview knihovny pro jiné jazyky. Kromě toho společnost Microsoft vydala rutiny prostředí PowerShell a příkazy Azure CLI pro práci s Azure Storage. Microsoft aktivně vyvíjí těchto nástrojů s výkonem v úvahu, udržuje je aktuální pomocí nejnovější verze služby a zajišťuje, že celá řada postupů prověřeného výkonu, interně zpracovat.  

### <a name="retries"></a>Opakování

#### <a name="subheading14"></a>Chyby kvůli zaneprázdněnosti omezení využití sítě a serveru

V některých případech může služba úložiště může omezit aplikace nebo může být jednoduše nemůže požadavek vyřídit, protože některé přechodné podmínky a vrátit zprávu "503 Server je zaneprázdněn" nebo "časový limit 500".  Tomu může dojít, pokud vaše aplikace se blíží některé z cíle škálovatelnosti, nebo pokud je systém vyrovnávání oddílů dat umožňuje vyšší propustnost.  Klientská aplikace obvykle opakovat operaci, která způsobí, že takové chyby: později pokouší stejný požadavek může uspět. Ale pokud službu storage je omezení aplikace, protože to je překročení cíle škálovatelnosti, nebo i v případě, že služba nemohla obsluhovat žádosti z nějakého důvodu, agresivní opakování obvykle provést ještě hůř problém. Z tohoto důvodu byste měli použít exponenciální regresní (klient knihovny výchozí toto chování). Vaše aplikace například může opakovat po 2 sekundy, pak 4 sekundami a 10 sekund a potom 30 sekund a zcela vzdát. Toto chování vyplývá ve vaší aplikaci výrazně snížit jeho zátěž služby spíše než ke zhoršení jakékoli problémy.  

K chybám připojení můžete okamžitě, opakovat, protože nejsou výsledek omezení šířky pásma a se očekává, že přechodná.  

#### <a name="subheading15"></a>Bez možnosti opakování chyby

Klientské knihovny jsou vědět, které chyby jsou možné opakovat a které nejsou. Nezapomeňte však, při psaní vlastního kódu pro rozhraní REST API služby storage jsou nějaké chyby, které se nesmí opakovat: například 400 (Chybný požadavek) v odpovědi vyplývá, že klientská aplikace odesílat žádosti, která nelze zpracovat, protože nebylo v očekávaných formuláře. Tuto žádost se posílá znovu způsobí stejnou odpověď pokaždé, když, proto nemá smysl to zkusíte znovu ji. Při psaní vlastního kódu pro rozhraní REST API služby storage, uvědomte si, co znamená kódy chyb a správný způsob, jak to chcete zkusit znovu (nebo nemusíte) pro každý z nich.  

#### <a name="useful-resources"></a>Užitečné materiály

Další informace o chybových kódech úložiště najdete v tématu [stavové a chybové kódy](https://msdn.microsoft.com/library/azure/dd179382.aspx) na webu Microsoft Azure.  

## <a name="blobs"></a>Objekty blob

Kromě osvědčených postupů pro [všechny služby](#allservices) je popsáno výše, následující osvědčené postupy platí výhradně pro službu Blob service.  

### <a name="blob-specific-scalability-targets"></a>Cíle škálovatelnosti pro konkrétní objekt BLOB

#### <a name="subheading46"></a>Přístup k jeden objekt současně více klientů

Pokud máte velké množství klientů souběžně přístup k jeden objekt, je potřeba vzít v úvahu na objekt a úložiště cíle škálovatelnosti účtu. Přesný počet klientů, kteří mohou přistupovat k jeden objekt se liší v závislosti na faktorech jako počet klientů najednou, požaduje objekt velikost objektu, síťové podmínky atd.

Pokud objekt je možné distribuovat prostřednictvím obsluhovat CDN například obrázky nebo videa z webu, pak byste měli použít síť CDN. Zobrazit [tady](#subheading5).

U dalších scénářů, jako je například vědecké simulace, kde jsou důvěrné máte dvě možnosti. První je rozložte úlohy vaší přístup tak, že je objekt zpřístupněný po určitou dobu vs, ke kterému přistupujete současně. Alternativně můžete dočasně kopírovat objekt do více účtů úložiště, čímž se zvýšilo celkový počet IOPS na objekt a mezi různými účty úložiště. Při testování omezené, zjistili jsme, že přibližně 25 virtuálních počítačů může současně stažení 100 GB objektu blob paralelně (každý virtuální počítač byl paralelním zpracováním stahování použití 32 vláken). Pokud máte 100 klientům, kteří potřebují přístup k objektu, nejprve zkopírovat do druhého účtu úložiště a pak je mít přístup k první objektů blob a druhý 50 přístup virtuálních počítačů druhý objekt blob prvních 50 virtuálních počítačů. Výsledky se budou lišit v závislosti na chování vaší aplikace, takže byste měli otestovat během návrhu. 

#### <a name="subheading16"></a>Operace na objekt blob a šířky pásma

Může číst nebo zapisovat do jednoho objektu blob v až do maximálního počtu 60 MB za sekundu (to je přibližně 480 MB/s, což překračuje možnosti mnoha sítě na straně klienta (včetně fyzický síťový adaptér v klientském zařízení). Kromě toho jeden objekt blob podporuje až 500 požadavků za sekundu. Pokud máte více klientů, které je třeba číst stejného objektu blob a může tato omezení překročí, zvažte použití sítě CDN pro distribuci objektu blob.  

Další informace o propustnosti cílové objektů BLOB najdete v tématu [Azure Storage škálovatelnost a cíle výkonnosti](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Kopírování a přesouvání objektů BLOB

#### <a name="subheading17"></a>Zkopírování objektu blob

Zavedená užitečná schopnost kopírování objektů BLOB v účtech úložiště rozhraní REST API verze 2012-02-12: klientská aplikace může dáte pokyn, aby službu storage ke kopírování objektu blob z jiného zdroje (například ve jiný účet úložiště) a nechte provést služby kopie asynchronně. To může výrazně snížit šířku pásma potřebnou pro aplikaci, když se migraci dat z jiné účty úložiště, protože není potřeba stáhnout a nahrát data.  

Jedním z faktorů, ale je, že při kopírování mezi účty úložiště, není zaručeno čas na při kopírování dokončí. Pokud vaše aplikace potřebuje na dokončení kopírování objektu blob rychle pod vaší kontrolou, může být lepší kopírování objektu blob stažením k virtuálnímu počítači a následně pak ho nahrát do cíle.  Pro úplnou předvídatelnost v takové situaci Ujistěte se, že kopírování se provádí pomocí virtuálního počítače s ve stejné oblasti Azure, jinak síťové podmínky mohou (a pravděpodobně bude) ovlivnit výkon kopírování.  Kromě toho můžete monitorovat průběh asynchronní kopírování prostřednictvím kódu programu.  

Kopie v rámci stejného účtu úložiště, samotné se obecně provádí rychle.  

Další informace najdete v tématu [objekt Blob kopírování](https://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>Pomocí nástroje AzCopy

Tým Azure Storage vydala nástroje příkazového řádku "AzCopy", který je určen usnadňující hromadného přenosu velký počet objektů BLOB do, z a mezi různými účty úložiště.  Tento nástroj je optimalizovaná pro tento scénář a dosáhnout vysoké přenosové rychlosti.  Jeho použití je podporováno pro hromadné nahrávání, stahování a kopírování scénáře. Další informace o něm a stáhněte ho najdete v tématu [přenos dat pomocí nástroje příkazového řádku Azcopy](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Služba Azure Import/Export

Pro velké objemy dat (více než 1 TB) úložiště Azure nabízí službu Import/Export, která umožňuje odesílání a stahování z úložiště objektů blob pomocí přenosů pevné disky.  Nechte svá data na pevném disku a odesílají je společnosti Microsoft pro nahrávání nebo poslat Microsoftu ke stahování dat prázdný pevný disk.  Další informace najdete v článku o [použití služby Microsoft Azure Import/Export k přenosu dat do služby Blob Storage](../storage-import-export-service.md).  To může být mnohem efektivnější než nahrávání a stahování velkého objemu dat přes síť.  

### <a name="subheading20"></a>Používání metadat

Služba objektů Blob podporuje hlavní požadavky, které můžou být metadata o objektu blob. Například dat EXIF mimo fotografii potřeby vaší aplikace se může načíst fotky a rozbalte ho. Pokud chcete snížit využití šířky pásma a zvýšit výkon, vaše aplikace může uchovávat datech EXIF v metadata objektu blob až se aplikace nahraje fotografii: pak můžete načítat data EXIF v metadatech pomocí pouze požadavek HEAD, ukládání významných šířky pásma a zpracování čas potřebný k extrahování dat EXIF pokaždé, když je pro čtení objektu blob. To může být užitečné v situacích, kdy potřebujete jenom metadata a ne celý obsah objektu blob.  Pouze 8 KB metadat je možné uložit na objekt blob (služba nebude přijímat žádosti na úložiště větší), takže pokud data se nevejde do této velikosti, nemusí být schopen tuto metodu použijte.  

Příklad toho, jak získat metadata objekt blob pomocí .NET, naleznete v tématu [nastavení a načtení vlastností a metadat](../blobs/storage-properties-metadata.md).  

### <a name="rapid-uploading"></a>Rychlé nahrávání

K rychlému nahrání objektů BLOB, je první otázku odpovědět: je, že nahrání objektů blob v jedné nebo více?  Použít následujících pokynů určete správný způsob, kterým v závislosti na vašem scénáři.  

#### <a name="subheading21"></a>Nahrávání velkých objektů blob v jedné rychle

Rychle nahrát jeden velký objekt blob, měli klientské aplikace nahrávat jeho bloků nebo stránky paralelně (jsou cíle škálovatelnosti pro jednotlivé objekty BLOB a účtu úložiště jako celek s vědomím).  Oficiální poskytovaný společností Microsoft RTM úložiště klientských knihoven (.NET, Java) se budou moct provést.  Pro každou z knihoven, použijte nižší než zadaný objekt nebo vlastnost nastavit úroveň souběžnosti:  

* .NET: Nastavit ParallelOperationThreadCount BlobRequestOptions objektu, který se má použít.
* Java/Android: Use BlobRequestOptions.setConcurrentRequestCount()
* Node.js: Použijte parallelOperationThreadCount v možnosti žádosti nebo na službu Blob service.
* C++: Použijte metodu blob_request_options::set_parallelism_factor.

#### <a name="subheading22"></a>Rychle nahrává velký počet objektů BLOB

Pokud chcete rychle nahrát velký počet objektů BLOB, nahrání objektů BLOB paralelně. Toto je rychlejší, než nahrání jednoho objekty BLOB najednou paralelní blokovat nahrávání ve vzhledem k tomu, že se šíří nahrávání napříč několika oddíly na službu storage. Jeden objekt blob podporuje pouze propustnost 60 MB za sekundu (přibližně 480 MB/s). V době psaní účet LRS státech podporuje až 20 GB/s příchozí, což je mnohem více než propustnost podporuje jednotlivých objektů blob.  [AzCopy](#subheading18) ve výchozím nastavení provádí nahrávání paralelně a doporučuje se pro tento scénář.  

### <a name="subheading23"></a>Výběr správného typu objektu blob

Azure Storage podporuje dva typy objektů blob: *stránky* objekty BLOB a *bloku* objekty BLOB. Pro jednotlivé scénáře použití daného zvoleného typu Objekt blob ovlivní výkon a škálovatelnost řešení. Objekty BLOB bloku jsou vhodné, pokud chcete nahrávání velkých objemů dat, efektivně: například může klientská aplikace musí se nahrát fotografie a videa do úložiště objektů blob. Objekty BLOB stránky jsou vhodné v případě musí aplikace provádět náhodné zápisy dat: například virtuální pevné disky Azure jsou uložené jako objekty BLOB stránky.  

Další informace najdete v tématu [vysvětlení objektů BLOB bloku, doplňovací objekty BLOB a objekty BLOB stránky](https://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Tabulky

Kromě osvědčených postupů pro [všechny služby](#allservices) je popsáno výše, následující osvědčené postupy platí výhradně pro služby table service.  

### <a name="subheading24"></a>Cíle škálovatelnosti konkrétní tabulky

Kromě omezení šířky pásma celý účet úložiště tabulky mají následující omezení konkrétní škálovatelnost.  Systém bude Vyrovnávání zatížení jako vaše zvýšení provozu, ale pokud náhlým nárůstům provozu, nebudete moci okamžitě získejte tento svazek propustnosti.  Pokud váš model obsahuje často shluky, měli byste očekávat zobrazíte omezení šířky pásma nebo vypršení časového limitu během burst jako úložiště služby automaticky vyrovnává zatížení na tabulku.  Ramping nahoru pomalu obecně má lepší výsledky, protože poskytuje systémového času pro vyrovnávání zatížení správně.  

#### <a name="entities-per-second-storage-account"></a>Entity za sekundu (účet služby storage)

Omezení škálovatelnosti pro přístup k tabulek je až 20 000 entity (1 KB každé) za sekundu pro účet.  Obecně platí každá entita, která je vložena, aktualizována, odstraněn nebo zkontrolovat počty směrem k této cílové.  Takže dávkové vložení, který obsahuje 100 entit by byl započítán jako 100 entit.  Dotaz, který vyhledá 1000 entity a vrátí 5 by byl započítán jako 1000 entity.  

#### <a name="entities-per-second-partition"></a>Entity za sekundu (oddílu)

Cíle škálovatelnosti pro přístup k tabulek v rámci jednoho oddílu, je 2 000 entity (1 KB každé) za sekundu, pomocí stejné inventury, jak je popsáno v předchozí části.  

### <a name="configuration"></a>Konfigurace

Tato část obsahuje seznam několika rychlé nastavení, které vám umožní provést výrazné zlepšení výkonu ve službě table service:  

#### <a name="subheading25"></a>Použití JSON

Počínaje verzí služby úložiště 2013-08-15, služby table service podporuje používání JSON namísto formátu AtomPub založený na formátu XML pro přenos dat tabulky. Tím můžete snížit až o 75 % velikosti datových částí a může výrazně zlepšit výkon aplikace.

Další informace najdete v příspěvku [tabulek aplikace Microsoft Azure: Úvod k formátu JSON](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) a [Formát datové části pro operace služby s tabulkou](https://msdn.microsoft.com/library/azure/dn535600.aspx).

#### <a name="subheading26"></a>Zakázat Nagle

Algoritmus pro Nagle je široce implementováno v sítích TCP/IP jako prostředek ke zlepšení výkonu sítě. To však není optimální za všech okolností (jako jsou vysoce interaktivní prostředí). Pro službu Azure Storage na Nagle algoritmus využívající dlaždice má negativní dopad na výkon požadavků na tabulky a fronty služby a měli byste zakázat Pokud je to možné.

### <a name="schema"></a>Schéma

Jak reprezentaci a dotazování na data je největší jednomu faktoru, který ovlivňuje výkon služby table service. Když každá aplikace, se liší, tato část popisuje některé obecné osvědčené postupy, které se týkají:  

* Návrh tabulky
* Efektivní dotazy
* Efektivní datové aktualizace  

#### <a name="subheading27"></a>Tabulky a oddíly

Tabulky jsou rozděleny do oddílů. Každá entita, uložená v oddílu sdílí stejný klíč oddílu a klíčem jedinečném řádku pro jeho rozpoznání v rámci daného oddílu. Oddíly poskytují výhody, ale přinášejí s omezením škálovatelnosti.  

* Výhody: Můžete aktualizovat entity do stejného oddílu v rámci jediné, atomické, batch transakce, který obsahuje až 100 samostatné úložiště operací (limit celkové velikosti 4 MB). Za předpokladu, že stejný počet entit, které se mají načíst, můžete také zadávat dotazy v rámci jednoho oddílu efektivnější než data, která zahrnuje oddíly (i když číst na Další doporučení na dotazy na data tabulky).
* Omezení škálovatelnosti: Přístup k entitám ukládala do jednoho oddílu nesmí být vyrovnáváním zatížení protože oddíly podporuje atomic dávkové transakce. Z tohoto důvodu cíle škálovatelnosti pro oddíl jednotlivé tabulky je nižší než služby table service jako celek.  

Z důvodu tyto vlastnosti z tabulky a oddíly, které musí přijmout tyto principy návrhu:  

* Data, která klientské aplikace často aktualizovány nebo spustit dotaz ve stejné logické jednotky práce se musí nacházet ve stejném oddílu.  To může být, protože aplikace je agregace zápisy, nebo budete chtít využít výhod atomic dávkových operací.  Navíc dat do jednoho oddílu může být efektivnější dotazována v jediném dotazu než dat napříč oddíly.
* Data, která klientské aplikace není insert nebo update nebo dotazu ve stejné logické jednotce práce (jeden dotaz nebo dávkové aktualizace) se musí nacházet v samostatných oddílech.  Jedna důležitá poznámka je, že neexistuje žádné omezení počtu klíčů oddílů v jedné tabulce, tak s miliony klíče oddílů není problém a nebude mít vliv na výkon.  Například pokud vaše aplikace je Oblíbené web s přihlášení uživatele, pak pomocí ID uživatele jako klíč oddílu může být dobrou volbou.  

#### <a name="hot-partitions"></a>Aktivních oddílů

Horkými oddíly je ten, který přijímá nepřiměřené procento provozu k účtu a nemůže být s vyrovnáváním zatížení protože je jeden oddíl.  Obecně platí aktivních oddílů jsou vytvořeny jedním ze dvou způsobů:  

##### <a name="subheading28"></a>Připojit pouze a Předřaďte pouze vzory

Vzor "Pouze přidat" je jedním kde všechny (téměř všechny) provozu do daného PK zvyšuje nebo snižuje podle aktuální čas.  Příkladem je, pokud vaše aplikace používá aktuální datum jako klíč oddílu dat protokolu.  Výsledkem je všechny probíhající operace vložení na poslední oddíl v tabulce a systém nedá vyrovnávat zatížení, protože všechny zápisy se chystáte konec tabulky.  Pokud svazek provozu do daného oddílu je větší než cílový oddíl úroveň škálovatelnosti, je výsledkem omezení šířky pásma.  Je lepší Ujistěte se, že k nižšímu přenosu ke více oddílů, aby nástroj pro vyrovnávání zatížení požadavků ve vaší tabulce.  

##### <a name="subheading29"></a>Data velkým provozem

Pokud schématu dělení výsledkem jednoho oddílu, který obsahuje jenom data, která se používá mnohem více než ostatní oddíly, může také zobrazit jako omezení, že blíží oddílu cíle škálovatelnosti pro jeden oddíl.  Je lepší, abyste měli jistotu, že schéma oddílů má za následek žádné jednoho oddílu blíží cíle škálovatelnosti.  

#### <a name="querying"></a>Dotazování

Tato část popisuje osvědčené postupy pro dotazování služby table service.  

##### <a name="subheading30"></a>Rozsah dotazu

Existuje několik způsobů, jak určit rozsah dotazované entity.  Následuje diskuzi o používání jednotlivých.  

Obecně platí Vyhněte se kontroly (větší než jedna entita dotazy), ale pokud musíte kontrolovat, zkuste uspořádat data, aby prohledávání načítat data, která potřebujete, bez vyhledávání nebo vrací velké množství entit, které nepotřebujete.  

###### <a name="point-queries"></a>Bodových dotazů

Bod dotaz načte přesně jednu entitu. Dělá to tak, že zadáte klíče oddílu a klíč řádku entity načíst. Tyto dotazy jsou účinné, a jejich použití kdykoli je to možné.  

###### <a name="partition-queries"></a>Dotazy na oddíl

Oddíl dotaz je dotaz, který načte sadu dat, které sdílejí společný klíč oddílu. Dotaz určuje obvykle rozsahu hodnot klíčů řádků nebo rozsah hodnot pro některé vlastnosti entity kromě klíče oddílu. Tyto jsou míň efektivní než bodových dotazů a měly používat střídmě.  

###### <a name="table-queries"></a>Dotazy na tabulku

Tabulkový dotaz je dotaz, který načte sadu entit, která se nesmí sdílet společný klíč oddílu. Tyto dotazy nejsou efektivní a neměli byste je-li to možné.  

##### <a name="subheading31"></a>Hustota dotazu

Dalším faktorem klíče v účinnost dotazu je počtu entit vrácených než počet zkontrolovaných najít vrácené sady entit. Pokud aplikace provádí tabulkový dotaz s filtrem pro hodnotu vlastnosti, že pouze 1 % sdílení dat, dotaz bude kontrolovat 100 entit pro každou jednu entitu, který vrátí. Cíle škálovatelnosti tabulky popsané dříve všechny se týkají počet entit, zkontrolovat a ne počtu entit vrácených: dotaz s nízkou hustotu může snadno způsobit omezení aplikace, protože se musí kontrolovat mnoho entit služby table service načtete entitu, kterou jste hledali.  Projděte si část níže na [denormalizace](#subheading34) Další informace o tom, jak tomu předešli.  

##### <a name="limiting-the-amount-of-data-returned"></a>Omezit velikost dat vrácená

###### <a name="subheading32"></a>Filtrování

Pokud víte, že dotaz vrátí entity, které není nutné v klientské aplikaci, vezměte v úvahu pomocí filtru, aby se zmenšila velikost vrácené sady. Při entity není vrácen do klienta stále počítají limity škálovatelnosti mého zlepší výkon vašich aplikací z důvodu velikost datové části snížená Síťová a nižší počet entit, které se musí zpracovat klientské aplikace.  Viz výše Poznámka na [dotazu hustota](#subheading31), ale – cíle škálovatelnosti souvisí počet entit Prohledané, takže dotaz, který odfiltruje mnoho entit může stále vést k omezení šířky pásma, i v případě, že jsou vráceny několik entit.  

###### <a name="subheading33"></a>Projekce

Pokud vaše klientská aplikace potřebuje pouze omezená sada vlastností z entit v tabulce, můžete omezit velikost vrácené sady dat projekce. Stejně jako u filtrování, to pomáhá snížit zatížení sítě a zpracování na straně klienta.  

##### <a name="subheading34"></a>Denormalizace

Na rozdíl od práce s relačními databázemi, osvědčené postupy pro efektivní dotazování na data v tabulce způsobit denormalizing vaše data. To znamená, že duplikování stejná data více entit (jeden pro každý klíč můžete použít k vyhledání dat) Chcete-li minimalizovat počet entit, které dotaz musí zkontrolovat vyhledat potřebná data klient potřebuje, namísto nutnosti prohledávání velkého počtu entit vyhledat potřebná data vaší aplikace lication potřebuje.  Například na webu elektronického obchodování můžete chtít najít objednávky obě ID zákazníka (uvést objednávek tohoto zákazníka) a k datu (uvést objednávky k datu).  Ve službě Table Storage, je nejvhodnější pro uložení entity (nebo na ni odkaz) dvakrát – jednou s názvem tabulky, PK a VÝCH pro usnadnění vyhledání zákazníka ID, jednou pro usnadnění hledání podle data.  

#### <a name="insertupdatedelete"></a>Příkaz Insert/Update/Delete

Tato část popisuje osvědčené postupy pro úpravu entity uložená ve službě table service.  

##### <a name="subheading35"></a>Dávkové zpracování

Dávkové transakce jsou označovány jako Entity skupiny transakce (ETG) ve službě Azure Storage; všechny operace v rámci ETG musí být na jeden oddíl v jediné tabulce. Kde je to možné, použijte ETGs provést vložení, aktualizace a odstranění v dávkách. To snižuje počet zpátečních cest z klientské aplikace na server, sníží počet fakturovatelných transakcí (ETG počítá jako jedna transakce pro účely fakturace a může obsahovat až 100 operace úložiště) a umožňuje atomické aktualizace (vše operace úspěšně nebo ne v rámci ETG). Prostředí s vysokou latencí, jako jsou například mobilní zařízení budou využívat výrazně ETGs.  

##### <a name="subheading36"></a>Upsertovat

Použití tabulky **Upsert** operace kdykoli je to možné. Existují dva typy **Upsert**, které může být efektivnější než tradiční **vložit** a **aktualizace** operace:  

* **InsertOrMerge**: Použijte ho, když chcete odeslat podmnožinu vlastností entity, ale nejste si jistí, jestli je entita již existuje. Pokud existuje entita toto volání aktualizuje vlastnosti součástí **Upsert** operace a nechá všechny existující vlastnosti jsou, pokud entita neexistuje, vloží nové entity. Jedná se používá projekce v dotazu, podobně jako v tom, že budete muset nahrát vlastnosti, které se mění.
* **InsertOrReplace**: Použijte ho, když chcete odeslat zcela nové entity, ale nejste si jisti, zda již existuje. Tento postup byste měli používat jenom v, když víte, že nově nahraných entity je úplně správná, protože zcela přepíše původní entita. Například chcete aktualizovat entitu, která ukládá aktuální umístění uživatele bez ohledu na to, jestli aplikace dříve uložil data o poloze pro uživatele. Nová entita umístění je kompletní a není nutné žádné informace z jakékoli předchozí entity.

##### <a name="subheading37"></a>Ukládání datových řad v jedné entity

V některých případech aplikace ukládá řadu data, která se často potřebuje načítat všechny najednou: například aplikace může sledovat využití procesoru v čase k vykreslení grafu postupné data z posledních 24 hodin. Jedním z přístupů je mít jednu entitu tabulky za hodinu, se každá entita představující konkrétní hodiny a ukládání využití procesoru pro určitou hodinu. K vykreslení tato data, aplikace potřebuje k načtení entity, která uchovává data z posledních 24 hodin.  

Alternativně může aplikace ukládat využití procesoru za každou hodinu jako samostatné vlastnost jednu entitu: aktualizovat každou hodinu, může vaše aplikace používat jediný **InsertOrMerge Upsert** volání k poslední aktualizaci hodnoty Hodina. K vykreslení dat, aplikace potřebuje pouze k načtení jedné entity místo 24, tak pro efektivní dotazování (viz výše diskuze na [dotazování oboru](#subheading30)).

##### <a name="subheading38"></a>Ukládání strukturovaných dat v objektech BLOB

Někdy strukturovaná data, jako třeba by měl přejít v tabulkách, ale rozsahy entity jsou vždy načteny společně a lze vložit služby batch.  Dobrým příkladem je soubor protokolu.  V takovém případě může několik minut protokolů služby batch, vkládat v případě potřeby a potom jsou vždy načítání několik minut protokolů současně také.  V takovém případě pro výkon, je lepší místo tabulky, protože může výrazně snížit počet objektů, které jsou napsané/vrátila, a také obvykle číslo žádosti, které je třeba použít objekty BLOB.  

## <a name="queues"></a>Fronty

Kromě osvědčených postupů pro [všechny služby](#allservices) je popsáno výše, následující osvědčené postupy platí výhradně pro službu front.  

### <a name="subheading39"></a>Omezení škálovatelnosti

Jednou frontou může zpracovat přibližně 2 000 zpráv (1 KB každé) za sekundu (AddMessage GetMessage a DeleteMessage počítají jako zpráva tady). Pokud to není dostatečné pro vaši aplikaci, by měl používat více fronty a šíří zprávy mezi nimi.  

Zobrazit aktuální cíle škálovatelnosti v [Azure Storage škálovatelnost a cíle výkonnosti](storage-scalability-targets.md).  

### <a name="subheading40"></a>Zakázat Nagle

Projděte část o konfiguraci tabulky, který popisuje algoritmus Nagle – algoritmus Nagle je obvykle chybná výkonu fronty požadavků, a měli byste zakázat.  

### <a name="subheading41"></a>Velikost zprávy

Fronty výkon a škálovatelnost snížit jako zvýšení velikosti zprávy. Měli byste umístit pouze informace potřebuje přijímač ve zprávě.  

### <a name="subheading42"></a>Načtení služby batch

Můžete načíst až 32 zpráv z fronty v rámci jedné operace. To může snížit počet výměn dat z klientské aplikace, což je užitečné zejména v prostředích, jako jsou například mobilní zařízení, s vysokou latencí.  

### <a name="subheading43"></a>Interval dotazování fronty

Většina aplikací se dotazovat na zprávy z fronty, která může být jedna z největších zdroje transakce pro danou aplikaci. Vyberte uvážlivě vašeho interval dotazování: dotazování příliš často může způsobit, že aplikace, abyste cíle škálovatelnosti pro frontu. Ale na 200 000 transakcí pro $0.01 (v době psaní) na jeden procesor dotazování po méně než 15 centů tak náklady a náklady každou sekundu po dobu jednoho měsíce není obvykle faktorem, který má vliv podle vašeho výběru interval dotazování.  

Náklady na aktuální informace najdete v tématu [ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>Aktualizace zprávy

Můžete použít **aktualizovat zpráva** operace zvýšit časový limit neviditelnosti nebo aktualizovat informace o stavu zprávy. Když je výkonný, nezapomeňte se každý **zpráva aktualizace** operace započítává cíle škálovatelnosti. Nicméně to může být mnohem efektivnější přístup než po dokončení každého kroku úlohy, když pracovní postup, který se předá úlohu z jedné fronty na další. Použití **zpráva aktualizace** operace umožňuje vaší aplikaci uložit stav úlohy pro zprávu a potom pokračovat v práci, namísto requeuing zprávy na další krok úlohy pokaždé, když se krok dokončí.  

Další informace najdete v článku [jak: Změna obsahu zpráv zařazených ve frontě](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message).  

### <a name="subheading45"></a>Architektura aplikace

Aby bylo škálovatelné architektuře aplikace by měla pomocí front. V následujícím seznamu uvedeny některé způsobů využití fronty pro zajištění větší škálovatelnost aplikace:  

* Fronty můžete použít k vytvoření nevyřízených položek práce pro zpracování a vyhlazení úlohy ve vaší aplikaci. Například může fronty požadavků od uživatelů k vykonání práce náročné na procesor například změny velikosti nahraných obrázků.
* Fronty můžete použít k oddělení částí aplikace tak, že je možné škálovat nezávisle. Webového front-endu může například umístit výsledky zjišťování uživatelů do fronty pro pozdější analýzu a úložiště. Můžete přidat další instance role pracovního procesu ke zpracování dat fronty podle potřeby.  

## <a name="conclusion"></a>Závěr

Tento článek byl věnován některé z nejčastěji používaných a prověřených postupů pro optimalizaci výkonu při použití služby Azure Storage. Všem vývojářům aplikací doporučujeme, aby svoje aplikace vyhodnotili ve světle výše uvedených postupů a zvážili jednotlivá doporučení, která aplikacím využívajícím Azure Storage zajišťují skvělý výkon.
