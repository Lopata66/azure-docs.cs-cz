---
title: Diagnostika s metrikami, výstrahami a stavem prostředků – Azure Standard Load Balancer
description: K diagnostice Standard Load Balancer Azure použijte dostupné metriky, výstrahy a informace o stavu prostředků.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: allensu
ms.openlocfilehash: 9003d35ce2eea18aa912a866802b026bb923aa08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272691"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Diagnostika služby Standard Load Balancer s metrikami, upozorněními a stavem prostředků

Azure Standard Load Balancer zpřístupňuje následující diagnostické možnosti:

* Multidimenzionální **metriky a upozornění**: poskytuje multidimenzionální diagnostické možnosti prostřednictvím [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) pro standardní konfigurace nástroje pro vyrovnávání zatížení. Můžete monitorovat, spravovat a řešit potíže s prostředky standardního nástroje pro vyrovnávání zatížení.

* **Resource Health**: stránka Load Balancer v Azure Portal a stránka Resource Health (pod položkou monitor) zpřístupňuje oddíl Resource Health pro standard Load Balancer. 

Tento článek poskytuje rychlou prohlídku těchto funkcí a nabízí způsoby jejich použití pro Standard Load Balancer. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>Multidimenzionální metriky

Azure Load Balancer poskytuje multidimenzionální metriky prostřednictvím metrik Azure v Azure Portal a pomáhá získat diagnostické přehledy v reálném čase do vašich prostředků nástroje pro vyrovnávání zatížení. 

Různé konfigurace Standard Load Balancer poskytují následující metriky:

| Metrika | Typ prostředku | Popis | Doporučená agregace |
| --- | --- | --- | --- |
| Dostupnost cesty k datům | Veřejný a interní nástroj pro vyrovnávání zatížení | Standard Load Balancer nepřetržitě vykonává cestu k datům z oblasti do front-endu nástroje pro vyrovnávání zatížení, a to až do zásobníku SDN, který podporuje váš virtuální počítač. Pokud zůstanou instance v pořádku, měření se řídí stejnou cestou jako provoz s vyrovnáváním zatížení vaší aplikace. Také se ověří cesta k datům, kterou používají vaši zákazníci. Měření je pro vaši aplikaci neviditelné a neovlivňuje jiné operace.| Průměr |
| Stav sondy stavu | Veřejný a interní nástroj pro vyrovnávání zatížení | Standard Load Balancer používá distribuovanou službu pro zjišťování stavu, která monitoruje stav koncového bodu vaší aplikace podle nastavení konfigurace. Tato metrika poskytuje agregované zobrazení každého koncového bodu instance ve fondu nástroje pro vyrovnávání zatížení, které je filtrované podle počtu koncových bodů. Můžete zjistit, jak Load Balancer zobrazení stavu aplikace, jak je uvedeno v konfiguraci sondy stavu. |  Průměr |
| SYN (synchronizace) paketů | Veřejný a interní nástroj pro vyrovnávání zatížení | Standard Load Balancer neukončuje připojení TCP (Transmission Control Protocol) ani interakci s toky paketů TCP nebo UDP. Toky a jejich metody handshake jsou vždy mezi zdrojem a instancí virtuálního počítače. K lepšímu řešení potíží se scénáři protokolu TCP můžete použít čítače paketů SYN, abyste pochopili, kolik pokusů o připojení TCP je prováděno. Metrika hlásí počet přijatých paketů TCP SYN.| Průměr |
| Připojení SNAT | Veřejný Nástroj pro vyrovnávání zatížení |Standard Load Balancer oznamuje počet odchozích toků, které jsou maskované na front-endu veřejné IP adresy. Porty zdrojového překladu adres (SNAT) jsou prostředek exhaustible. Tato metrika vám může poskytnout informace o tom, jak intenzivně se aplikace spoléhá na SNAT pro odchozí vzniklé toky. Čítače pro úspěšné a neúspěšné odchozí toky SNAT jsou hlášeny a lze je použít k řešení potíží a pochopení stavu odchozích toků.| Průměr |
| Přidělené porty SNAT | Veřejný Nástroj pro vyrovnávání zatížení | Standard Load Balancer oznamuje počet přidělených portů SNAT na back-end instanci. | Vypočítat. |
| Používané porty SNAT | Veřejný Nástroj pro vyrovnávání zatížení | Standard Load Balancer oznamuje počet portů SNAT, které se využívají na instanci back-endu. | Průměr | 
| Čítače bajtů |  Veřejný a interní nástroj pro vyrovnávání zatížení | Standard Load Balancer oznamuje data zpracovaná za front-end. Můžete si všimnout, že bajty nejsou rovnoměrně distribuované napříč instancemi back-endu. Očekává se, že Load Balancer algoritmus Azure je založený na tocích | Průměr |
| Čítače paketů |  Veřejný a interní nástroj pro vyrovnávání zatížení | Standard Load Balancer hlásí zpracované pakety za front-end.| Průměr |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Zobrazit metriky nástroje pro vyrovnávání zatížení v Azure Portal

Azure Portal zpřístupňuje metriky nástroje pro vyrovnávání zatížení prostřednictvím stránky metriky, která je k dispozici na stránce prostředků nástroje pro vyrovnávání zatížení pro konkrétní prostředek a Azure Monitor stránku. 

Chcete-li zobrazit metriky pro prostředky Standard Load Balancer:
1. Přejít na stránku metriky a proveďte jednu z následujících akcí:
   * Na stránce prostředek nástroje pro vyrovnávání zatížení vyberte v rozevíracím seznamu Typ metriky.
   * Na stránce Azure Monitor vyberte prostředek nástroje pro vyrovnávání zatížení.
2. Nastavte vhodný typ agregace metriky.
3. Volitelně můžete nakonfigurovat požadované filtrování a seskupování.
4. Volitelně můžete nakonfigurovat časový rozsah a agregaci. Ve výchozím nastavení se čas zobrazuje v UTC.

  >[!NOTE] 
  >Časová agregace je důležitá při interpretaci určitých metrik, protože data jsou vzorkovat jednou za minutu. Pokud je časová agregace nastavená na pět minut a pro metriky, jako je například přidělování SNAT, se používá typ agregace metriky, zobrazí se v grafu pět navrácených celkových portů SNAT. 

![Metriky pro Standard Load Balancer](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*Obrázek: metrika dostupnosti datové cesty pro Standard Load Balancer*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Načtěte multidimenzionální metriky prostřednictvím rozhraní API.

Pokyny k rozhraní API pro načítání multidimenzionálních definic a hodnot naleznete v tématu [návod k Azure Monitoring REST API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api). Tyto metriky se dají zapsat do účtu úložiště jenom pomocí možnosti všechny metriky. 

### <a name="configure-alerts-for-multi-dimensional-metrics"></a>Konfigurace upozornění na multidimenzionální metriky ###

Azure Standard Load Balancer podporuje snadno konfigurovatelné výstrahy pro multidimenzionální metriky. Nakonfigurujte vlastní prahové hodnoty pro konkrétní metriky, abyste mohli aktivovat výstrahy s různou úrovní závažnosti, abyste mohli využívat možnosti monitorování prostředků bez dotykového ovládání.

Konfigurace upozornění:
1. Přejít na dílčí okno výstrahy pro nástroj pro vyrovnávání zatížení
1. Vytvoření nového pravidla upozornění
    1.  Konfigurace podmínky výstrahy
    1.  Volitelné Přidat skupinu akcí pro automatizovanou opravu
    1.  Přiřazení závažnosti, názvu a popisu výstrahy, která umožňuje intuitivní reakci

  >[!NOTE]
  >Okno Konfigurace podmínky výstrahy zobrazí časové řady pro historii signálu. K dispozici je možnost filtrovat tuto časovou řadu podle dimenzí, jako je například IP adresa back-endu. Tato akce bude filtrovat graf časové řady, ale **ne** samotnou výstrahu. Nemůžete konfigurovat výstrahy pro konkrétní IP adresy back-endu.

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>Běžné diagnostické scénáře a doporučená zobrazení

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-frontend"></a>Je cesta k datům a dostupná pro moje Load Balancer front-end?
<details><summary>Rozbalit</summary>

Metrika dostupnosti datové cesty popisuje stav cesty k datům v rámci oblasti a výpočetnímu hostiteli, ve kterém se nachází vaše virtuální počítače. Metrika je odrazem stavu infrastruktury Azure. Metriku můžete použít k těmto akcím:
- Monitorování externí dostupnosti vaší služby
- Dig hlubší a zjistěte, jestli je platforma, na které je vaše služba nasazená, v dobrém stavu, nebo jestli je instance hostovaného operačního systému nebo aplikace v pořádku.
- Izolujte, jestli událost souvisí se službou nebo základní rovinou dat. Nezaměňujte tuto metriku se stavem sondy stavu ("dostupnost instance back-endu").

Jak získat dostupnost cesty k datům pro prostředky Standard Load Balancer:
1. Ujistěte se, že je vybrán správný prostředek nástroje pro vyrovnávání zatížení. 
2. V rozevíracím seznamu **metrika** vyberte **dostupnost cesty k datům**. 
3. V rozevíracím seznamu **agregace** vyberte **střední**. 
4. Kromě toho přidejte filtr na front-end IP adresu nebo port front-endu jako dimenzi s požadovanou front-end IP adresou nebo front-end port a pak je seskupte podle vybrané dimenze.

![Zjišťování VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Obrázek: Load Balancer podrobnosti o probingu front-endu*

Metrika je generována aktivním měřením v pásmu. Služba zjišťování v rámci této oblasti představuje provoz pro měření. Služba se aktivuje hned po vytvoření nasazení s veřejným front-end a bude pokračovat, dokud neodeberete front-end. 

Paket, který odpovídá front-end a pravidlu nasazení, se generuje pravidelně. Projde oblast ze zdroje na hostitele, kde se nachází virtuální počítač ve fondu back-end. Infrastruktura nástroje pro vyrovnávání zatížení provádí stejné operace vyrovnávání zatížení a překladu, protože funguje pro všechny ostatní přenosy. Tento test je v rámci vašeho koncového bodu s vyrovnáváním zatížení v pásmu. Po doručení testu do výpočetního hostitele, kde je umístěn zdravý virtuální počítač ve fondu back-end, vygeneruje výpočetní hostitel odpověď na službu zjišťování. Váš virtuální počítač tento provoz nevidí.

Dostupnost dostupnosti DataPath se nezdařila z následujících důvodů:
- Vaše nasazení nemá ve fondu back-end žádné dobré virtuální počítače. 
- Došlo k výpadku infrastruktury.

Pro účely diagnostiky můžete použít [metriku dostupnosti datových cest společně se stavem sondy stavu](#vipavailabilityandhealthprobes).

Pro většinu scénářů použijte **průměr** jako agregaci.
</details>

#### <a name="are-the-backend-instances-for-my-load-balancer-responding-to-probes"></a>Reagují back-end instance pro můj Load Balancer k testům?
<details>
  <summary>Rozbalit</summary>
Metrika stavu sondy stavu popisuje stav nasazení aplikace, jak je nakonfigurován při konfiguraci sondy stavu nástroje pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení využívá stav sondy stavu k určení, kam se mají posílat nové toky. Sondy stavu pocházejí z adresy infrastruktury Azure a jsou viditelné v hostovaném operačním systému virtuálního počítače.

Postup získání stavu testu stavu pro prostředky Standard Load Balancer:
1. Vyberte metriku **stavu sondy** stavu s **průměrem** typu agregace. 
2. Použijte filtr na požadované IP adrese nebo portu front-endu (nebo obojí).

Sondy stavu selžou z následujících důvodů:
- Sondu stavu můžete nakonfigurovat na port, který nenaslouchá nebo nereaguje nebo používá nesprávný protokol. Pokud vaše služba používá pravidla přímého vrácení serveru (DSR nebo plovoucí IP adresy), ujistěte se, že služba naslouchá na IP adrese konfigurace protokolu IP síťové karty, a ne jenom na zpětné smyčce, která je nakonfigurovaná s front-end IP adresou.
- Vaše sonda není povolená skupinou zabezpečení sítě, bránou firewall hostovaného operačního systému virtuálního počítače nebo filtry aplikační vrstvy.

Pro většinu scénářů použijte **průměr** jako agregaci.
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Návody se podívat na Statistika odchozího připojení? 
<details>
  <summary>Rozbalit</summary>
Metrika připojení SNAT popisuje objem úspěšných a neúspěšných připojení pro [odchozí toky](https://aka.ms/lboutbound).

Svazek neúspěšných připojení, který je větší než nula, indikuje vyčerpání portů SNAT. Abyste zjistili, co můžou tyto chyby způsobovat, musíte prozkoumat další. Manifesty vyčerpání portů SNAT jako neúspěšné navázání [odchozího toku](https://aka.ms/lboutbound). Přečtěte si článek o odchozích připojeních, abyste porozuměli scénářům a mechanismům v práci, a zjistili si, jak zmírnit a navrhovat, abyste se vyhnuli vyčerpání portů SNAT. 

Získání statistiky připojení SNAT:
1. Vyberte **připojení SNAT** typ metriky a **součet** jako agregaci. 
2. Seskupit podle **stavu připojení** pro úspěšné a neúspěšné počty připojení SNAT, které jsou reprezentovány různými řádky. 

![Připojení SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Obrázek: počet připojení Load Balancer SNAT*
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>Návody kontrolovat využití a přidělování portů SNAT?
<details>
  <summary>Rozbalit</summary>
Metrika využití SNAT indikuje, kolik jedinečných toků mezi internetovým zdrojem a back-end virtuálním počítačem nebo sadou škálování virtuálního počítače, která je za nástrojem pro vyrovnávání zatížení, a nemá veřejnou IP adresu. Porovnáním s metrikou přidělování SNAT můžete zjistit, jestli se vaše služba objevila, nebo na nebezpečí vyčerpání SNAT a výsledný výstup selhání toku. 

Pokud vaše metrika signalizuje riziko selhání [odchozího toku](https://aka.ms/lboutbound) , proveďte odkaz na článek a proveďte kroky, které tuto skutečnost zmírnit, abyste zajistili stav služby.

Zobrazení využití a přidělení portu SNAT:
1. Nastavte časovou agregaci grafu na 1 minutu, aby se zobrazila požadovaná data.
1. Jako typ metriky vyberte **využití SNAT** nebo **alokaci SNAT** a **průměr** jako agregaci.
    * Ve výchozím nastavení se jedná o průměrný počet portů SNAT přidělených nebo používaných jednotlivými virtuálními počítači back-end nebo VMSS, které odpovídají všem veřejným IP adresám front-endu mapovaným na Load Balancer agregované přes protokoly TCP a UDP.
    * Zobrazení celkových portů SNAT používaných nástrojem nebo přiděleným pro nástroj pro vyrovnávání zatížení použití **součtu** agregace metriky
1. Filtr na konkrétní **typ protokolu**, sadu **back-end**serverů nebo **IP adresy front-endu**.
1. Pokud chcete monitorovat stav na back-end nebo front-endové instance, použijte rozdělení. 
    * Rozdělení poznámky umožňuje zobrazit pouze jednu metriku. 
1. Například pro monitorování využití SNAT pro toky TCP na počítač, agregované podle **průměru**, rozdělení podle **IP adresy back-endu** a filtrování podle **typu protokolu**. 

![Přidělení a využití SNAT](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*Obrázek: Průměrná doba přidělení a využití portu TCP SNAT pro sadu back-end virtuálních počítačů*

![Využití SNAT podle instance back-endu](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*Obrázek: využití portu TCP SNAT na back-end instanci*
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Návody kontrolovat pokusy o příchozí a odchozí připojení pro moji službu?
<details>
  <summary>Rozbalit</summary>
Metrika paketů SYN popisuje objem paketů TCP SYN, které byly doručeny nebo odeslány (pro [odchozí toky](https://aka.ms/lboutbound)), které jsou přidruženy k určitému front-endu. Tuto metriku můžete použít k porozumění pokusům o připojení TCP ke službě.

Pro většinu scénářů použijte **součet** jako agregaci.

![Připojení SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Obrázek: Load Balancer počet SYN*
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Návody kontrolovat využití šířky pásma sítě? 
<details>
  <summary>Rozbalit</summary>
Metriky čítače bajtů a paketů popisují objem bajtů a paketů, které vaše služba odesílá nebo přijímá, na front-endové bázi.

Pro většinu scénářů použijte **součet** jako agregaci.

Postup získání statistiky počtu bajtů nebo paketů:
1. Vyberte typ metriky **počet bajtů** nebo **počet paketů** s **průměrem** jako agregaci. 
2. Proveďte jednu z následujících akcí:
   * Použijte filtr na konkrétní IP adresu front-endu, front-end port, back-end IP adresu nebo back-end port.
   * Získejte celkové statistiky prostředku nástroje pro vyrovnávání zatížení bez jakéhokoli filtrování.

![Počet bajtů](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Obrázek: Load Balancer počet bajtů*
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>Návody diagnostikovat nasazení nástroje pro vyrovnávání zatížení?
<details>
  <summary>Rozbalit</summary>
Když použijete kombinaci metrik stavu dostupnosti datové cesty a sondy stavu v jednom grafu, můžete zjistit, kde najít problém a vyřešit problém. Můžete získat jistotu, že Azure pracuje správně, a pomocí tohoto vědomí jednoznačně určit, jestli je konfigurace nebo aplikace hlavní příčinou.

Metriky stavu sondy můžete použít k pochopení, jak Azure zobrazuje stav vašeho nasazení podle konfigurace, kterou jste zadali. Prohlížení sond stavu je vždy skvělým prvním krokem při monitorování nebo určení příčiny.

Můžete si tak učinit krok podrobněji a použít metriku dostupnosti datových cest k získání přehledu o tom, jak Azure zobrazuje stav základní roviny dat, která je zodpovědná za vaše konkrétní nasazení. Když kombinujete obě metriky, můžete izolovat, kde může být chyba, jak je znázorněno v následujícím příkladu:

![Kombinování metrik dostupnosti datových cest a stavu sondy stavu](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Obrázek: kombinování datových cest a metrik stavu sondy stavu*

Graf zobrazuje následující informace:
- Infrastruktura hostující vaše virtuální počítače nebyla k dispozici a na začátku grafu je 0 procent. Později je infrastruktura v pořádku a virtuální počítače byly dosažitelné a více než jeden virtuální počítač byl umístěný do back-endu. Tyto informace jsou označeny modrým trasováním pro dostupnost cesty k datům, které byly později v 100 procentech. 
- Stav sondy stavu, který je označen fialovým trasováním, je na začátku grafu na 0 procent. KRUHOVÁ oblast ve zelených světlech, kde se stav sondy stavu stal v pořádku a v jakém okamžiku bylo nasazení zákazníka schopné přijmout nové toky.

Graf umožňuje zákazníkům řešit vlastní řešení bez nutnosti odhadování nebo požádáte o podporu, jestli dochází k ostatním problémům. Služba není k dispozici, protože sondy stavu selhaly kvůli chybné konfiguraci nebo selhání aplikace.
</details>

## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Stav prostředku

Stav prostředků Standard Load Balancer se zveřejňuje prostřednictvím stávajícího **stavu prostředků** v části **monitorování > Service Health**.

Zobrazení stavu prostředků veřejné Standard Load Balancer:
1. Vyberte **monitor** > **Service Health**.

   ![Monitorovat stránku](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Obrázek: odkaz Service Health na Azure Monitor*

2. Vyberte **Resource Health**a pak se ujistěte, že je vybraná možnost **ID předplatného** a **typ prostředku = Load Balancer** .

   ![Stav prostředku](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Obrázek: výběr prostředku pro zobrazení stavu*

3. V seznamu vyberte prostředek Load Balancer pro zobrazení jeho historických stavů.

    ![Stav Load Balancer](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Obrázek: Load Balancer zobrazení stavu prostředků*
 
V následující tabulce jsou uvedeny různé stavy prostředků a jejich popisy: 

| Stav prostředku | Popis |
| --- | --- |
| K dispozici. | Váš prostředek standardního nástroje pro vyrovnávání zatížení je v pořádku a dostupný. |
| Neaktivní | Váš prostředek standardního nástroje pro vyrovnávání zatížení není v pořádku. Diagnostikujte stav tak, že vyberete **Azure monitor** > **metriky**.<br>(*Nedostupný* stav může také znamenat, že prostředek není připojený k vašemu standardnímu nástroji pro vyrovnávání zatížení.) |
| Není známo | Stav prostředku pro prostředek standardního nástroje pro vyrovnávání zatížení se ještě neaktualizoval.<br>(*Neznámý* stav může také znamenat, že prostředek není připojen k vašemu standardnímu nástroji pro vyrovnávání zatížení.)  |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Standard Load Balancer](load-balancer-standard-overview.md).
- Přečtěte si další informace o [odchozím připojení k nástroji pro vyrovnávání zatížení](https://aka.ms/lboutbound).
- Přečtěte si o [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview).
- Přečtěte si o [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) a [o tom, jak pomocí REST API načíst metriky](/rest/api/monitor/metrics/list).
