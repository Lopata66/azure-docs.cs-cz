---
title: Značky služby skupiny zabezpečení sítě (NSG) pro Azure HDInsight
description: Pomocí značek služby HDInsight můžete povolit příchozí provoz do vašeho clusteru z uzlů služeb stavu a správy, aniž byste museli přidávat IP adresy do skupin zabezpečení sítě.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 5608d0cd83e506bc6b30337db5148f344f59f80e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81410850"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>Značky služby NSG pro Azure HDInsight

Značky služby Azure HDInsight pro skupiny zabezpečení sítě (skupin zabezpečení sítě) jsou skupiny IP adres pro služby stavu a správy. Tyto skupiny vám pomůžou minimalizovat složitost pro vytváření pravidel zabezpečení. [Značky služby](../virtual-network/security-overview.md#service-tags) umožňují příchozí provoz z konkrétních IP adres bez nutnosti ZADÁVAT jednotlivé [IP adresy pro správu](hdinsight-management-ip-addresses.md) v skupin zabezpečení sítě.

Služba HDInsight spravuje tyto značky služeb. Nemůžete vytvořit vlastní značku služby ani upravit existující značku. Společnost Microsoft spravuje předpony adres, které se shodují se značkou služby, a automaticky aktualizuje značku služby na změny adres.

## <a name="get-started-with-service-tags"></a>Začínáme s visačkami služeb

Ve skupinách zabezpečení sítě máte dvě možnosti použití značek služeb:

- **Použijte jednu globální značku služby HDInsight**: Tato možnost otevře virtuální síť se všemi IP adresami, které služba HDInsight používá pro monitorování clusterů ve všech oblastech. Tato možnost je nejjednodušší způsob, ale nemusí být vhodný, pokud máte omezující požadavky na zabezpečení.

- **Použít více značek regionální služby**: Tato možnost otevře virtuální síť pouze s IP adresami, které HDInsight používá v konkrétní oblasti. Pokud ale používáte více oblastí, budete muset do své virtuální sítě přidat několik značek služeb.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Použít jednu globální značku služby HDInsight

Nejjednodušší způsob, jak začít používat značky služeb v clusteru HDInsight, je přidat globální značku `HDInsight` do pravidla NSG.

1. Z [Azure Portal](https://portal.azure.com/)vyberte skupinu zabezpečení sítě.

1. V části **Nastavení**vyberte **příchozí pravidla zabezpečení**a pak vyberte **+ Přidat**.

1. V rozevíracím seznamu **zdroj** vyberte možnost **značka služby**.

1. V rozevíracím seznamu **tag zdrojové služby** vyberte **HDInsight**.

    ![Přidat značku služby z Azure Portal](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Tato značka obsahuje IP adresy služeb stavu a správy pro všechny oblasti, kde je HDInsight k dispozici. Značka zajistí, že cluster bude moci komunikovat s nezbytnými službami pro stav a správu bez ohledu na to, kde je vytvořen.

## <a name="use-regional-hdinsight-service-tags"></a>Použití regionálních značek služby HDInsight

Pokud možnost globální značky nebude fungovat, protože potřebujete více omezující oprávnění, můžete pro vaši oblast použít jenom značky služeb. V závislosti na oblasti, ve které se cluster vytváří, může existovat několik značek služeb.

Pokud chcete zjistit, které značky služby se mají přidat do vaší oblasti, přečtěte si následující části článku.

### <a name="use-a-single-regional-service-tag"></a>Použít jednu regionální značku služby

Pokud je váš cluster umístěný v oblasti uvedené v této tabulce, stačí do NSG přidat jednu regionální značku služby.

| Země | Oblast | Značka služby |
| ---- | ---- | ---- |
| Austrálie | Austrálie – východ | HDInsight. AustraliaEast |
| &nbsp; | Austrálie – jihovýchod | HDInsight. AustraliaSoutheast |
| &nbsp; | Austrálie – střed | HDInsight. AustraliaCentral |
| Čína | Čína – východ 2 | HDInsight. ChinaEast2 |
| &nbsp; | Čína – sever 2 | HDInsight. ChinaNorth2 |
| Spojené státy | USA – středosever | HDInsight. NorthCentralUS |
| &nbsp; | USA – západ 2 | HDInsight. WestUS2 |
| &nbsp; | USA – středozápad | HDInsight. WestCentralUS |
| Kanada | Kanada – východ | HDInsight. CanadaEast |
| Brazílie | Brazílie – jih | HDInsight. BrazilSouth |
| Jižní Korea | Jižní Korea – střed | HDInsight. KoreaCentral |
| &nbsp; | Jižní Korea – jih | HDInsight. KoreaSouth |
| Indie | Indie – střed | HDInsight. CentralIndia |
| &nbsp; | Indie – jih | HDInsight. SouthIndia |
| Japonsko | Japonsko – západ | HDInsight. JapanWest |
| Francie | Francie – střed| HDInsight. FranceCentral |
| UK | Spojené království – jih | HDInsight. UKSouth |
| Azure Government | USDoD – střed | HDInsight. USDoDCentral |
| &nbsp; | USGov Texas | HDInsight. USGovTexas |
| &nbsp; | UsDoD východ | HDInsight. USDoDEast |
| &nbsp; | USGov Arizona | HDInsight. USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Použití více značek regionální služby

Pokud oblast, ve které jste cluster vytvořili, není uvedená v předchozí tabulce, je potřeba, abyste povolili více značek regionální služby. Nutnost použít více než jednu z nich je kvůli rozdílům v uspořádání poskytovatelů prostředků pro různé oblasti.

Zbývající oblasti jsou rozdělené do skupin podle toho, které značky regionální služby používají.

#### <a name="group-1"></a>Skupina 1

Pokud je cluster vytvořen v jedné z oblastí v následující tabulce, povolte značky `HDInsight.WestUS` služby a. `HDInsight.EastUS` Také uvedená označení místní služby. Oblasti v této části vyžadují tři značky služeb.

Pokud se například v dané `East US 2` oblasti vytvoří cluster, budete muset do skupiny zabezpečení sítě přidat tyto značky služby:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Země | Oblast | Značka služby |
| ---- | ---- | ---- |
| Spojené státy | USA – východ 2 | HDInsight. EastUS2 |
| &nbsp; | USA – střed | HDInsight. CentralUS |
| &nbsp; | NorthCentral nás | HDInsight. NorthCentralUS |
| &nbsp; | USA – středojih | HDInsight. SouthCentralUS |
| &nbsp; | USA – východ | HDInsight. EastUS |
| &nbsp; | USA – západ | HDInsight. WestUS |
| Japonsko | Japonsko – východ | HDInsight. JapanEast |
| Evropa | Severní Evropa | HDInsight. NorthEurope |
| &nbsp; | Západní Evropa| HDInsight. WestEurope |
| Asie | Východní Asie | HDInsight. EastAsia |
| &nbsp; | Jihovýchodní Asie | HDInsight. SoutheastAsia |
| Austrálie | Austrálie – východ | HDInsight. AustraliaEast |

#### <a name="group-2"></a>Skupina 2

Clustery v oblastech *Čína – sever* a *Čína – východ* musí umožňovat dvě značky služeb: `HDInsight.ChinaNorth` a. `HDInsight.ChinaEast`

#### <a name="group-3"></a>Skupina 3

Clustery v oblastech *US gov – Iowa* a *US gov – Virginie* musí umožňovat dvě značky služeb: `HDInsight.USGovIowa` a. `HDInsight.USGovVirginia`

#### <a name="group-4"></a>Skupina 4

Clustery v oblastech *Německa střední* a *Německo – severovýchod* potřebují umožňovat dvě značky služeb: `HDInsight.GermanyCentral` a. `HDInsight.GermanyNortheast`

## <a name="next-steps"></a>Další kroky

- [Skupiny zabezpečení sítě: značky služeb](../virtual-network/security-overview.md#security-rules)
- [Vytváření virtuálních sítí pro clustery Azure HDInsight](hdinsight-create-virtual-network.md)
