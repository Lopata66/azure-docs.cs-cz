---
title: Správa Azure DDoS Protection Standard s využitím webu Azure portal
titlesuffix: Azure Virtual Network
description: Další informace o použití Azure DDoS Protection Standard telemetrických dat ve službě Azure Monitor k zmírní útok.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: kumud
ms.openlocfilehash: a053beb121e1b3c0db020094c29a9a1e0117da87
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203535"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Správa Azure DDoS Protection Standard s využitím webu Azure portal

Zjistěte, jak povolit a zakázat distribuované s cílem odepření služeb (DDoS) protection a zmírnění útoků DDoS s Azure DDoS Protection Standard pomocí telemetrie. Chrání před útoky DDoS Protection standardní prostředky Azure, jako jsou virtuální počítače, nástroje pro vyrovnávání zatížení a brány application Gateway, které máte Azure [veřejnou IP adresu](virtual-network-public-ip-address.md) přiřazené. Další informace o standardních před útoky DDoS Protection a její možnosti najdete v tématu [před útoky DDoS Protection standardní přehled](ddos-protection-overview.md).

Před dokončením některé kroky v tomto kurzu, přihlaste se k webu Azure portal na https://portal.azure.com se účet přiřazený k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , který je přiřazen odpovídající akce uvedené v [oprávnění](#permissions).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-ddos-protection-plan"></a>Vytvořit plán DDoS Protection

Plán DDoS protection definuje sadu virtuálních sítí, které mají před útoky DDoS standard protection povolené napříč předplatnými. Můžete nakonfigurovat jeden plán DDoS protection vaší organizace a propojení virtuálních sítí z několika předplatných stejný plán. Plán DDoS Protection samotného je také přidružený k předplatnému, kterou jste vybrali při vytváření plánu. Plánu služby DDoS Protection funguje napříč oblastmi a předplatných. Příklad – můžete vytvořit plán v oblasti East-US a odkaz k předplatnému #1 ve vašem tenantovi. Stejný plán, který lze propojit k virtuálním sítím z jiných předplatných v různých oblastech, ve vašem tenantovi. Předplatné plánu souvisí s sebou nese náklady na měsíční opakované náklady pro plán, jakož i poplatky za Nadlimitní využití, v případě, že počet chráněné veřejné IP adresy překračuje 100. Další informace o cenách za DDoS najdete v tématu [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/ddos-protection/).

Vytvoření více než jeden plán není vyžadováno pro většinu organizací. Plán nejde mezi předplatnými přesunout. Pokud chcete změnit přihlášení k odběru plánu je v, budete muset [odstranit existující plán](#work-with-ddos-protection-plans) a vytvořte novou.

1. Vyberte **vytvořit prostředek** v levém horním rohu webu Azure portal.
2. Vyhledejte *před útoky DDoS*. Když **plán DDos protection** se zobrazí ve výsledcích hledání vyberte ji.
3. Vyberte **Vytvořit**.
4. Zadejte nebo vyberte vlastní hodnoty, nebo zadat, nebo vyberte následující ukázkové hodnoty a pak vyberte **vytvořit**:

    |Nastavení        |Hodnota                                              |
    |---------      |---------                                          |
    |Název           | myDdosProtectionPlan                              |
    |Předplatné   | Vyberte své předplatné.                         |
    |Skupina prostředků | Vyberte **vytvořit nový** a zadejte *myResourceGroup* |
    |Location       | USA – východ                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Povolit před útoky DDoS pro nové virtuální sítě

1. Vyberte **vytvořit prostředek** v levém horním rohu webu Azure portal.
2. Vyberte **Sítě** a pak vyberte **Virtuální síť**.
3. Zadejte nebo vyberte vlastní hodnoty, zadejte nebo vyberte následující ukázkové hodnoty, potvrďte zbývající výchozí nastavení a pak vyberte **vytvořit**:

    | Nastavení         | Hodnota                                                        |
    | ---------       | ---------                                                    |
    | Název            | myVirtualNetwork                                             |
    | Předplatné    | Vyberte své předplatné.                                    |
    | Skupina prostředků  | Vyberte **Použít existující** a pak vyberte **myResourceGroup**. |
    | Location        | USA – východ                                                      |
    | Služba DDos protection | Vyberte **standardní** a potom v části **DDoS protection**vyberte **myDdosProtectionPlan**. Plán, který vyberete, může být ve stejném nebo jiném předplatném než virtuální sítě, ale obě předplatná musí být přidružený ke stejnému tenantovi Azure Active Directory.|

Virtuální síť nelze přesunout do jiné skupiny prostředků nebo předplatného, pokud před útoky DDoS Standard je povolena pro virtuální síť. Pokud potřebujete přesunout virtuální síť s před útoky DDoS Standard povoleno, nejprve zakázat před útoky DDoS Standard, přesunout virtuální síť a pak povolte před útoky DDoS standard. Po přesunutí se obnoví automaticky laděná zásad prahové hodnoty pro všechny chráněné veřejné IP adresy ve virtuální síti.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Povolit před útoky DDoS pro existující virtuální sítě

1. Vytvořit plán DDoS protection pomocí kroků v [vytvořit plán DDoS protection](#create-a-ddos-protection-plan), pokud nemáte existující plán DDoS protection.
2. Vyberte **vytvořit prostředek** v levém horním rohu webu Azure portal.
3. Zadejte název virtuální sítě, kterou chcete zapnout DDoS Protection úrovně Standard pro v **hledat prostředky, služby a pole dokumentace** v horní části portálu. Když se ve výsledcích hledání zobrazí název virtuální sítě, vyberte ji.
4. Vyberte **DDoS protection**v části **nastavení**.
5. Vyberte **standardní**. V části **plán DDoS protection**, vyberte existující plán DDoS protection nebo plán, který jste vytvořili v kroku 1 a pak vyberte **Uložit**. Plán, který vyberete, může být ve stejném nebo jiném předplatném než virtuální sítě, ale obě předplatná musí být přidružený ke stejnému tenantovi Azure Active Directory.

## <a name="disable-ddos-for-a-virtual-network"></a>Zakázat před útoky DDoS pro virtuální síť

1. Zadejte název virtuální sítě, kterou chcete zakázat před útoky DDoS standard protection pro v **hledat prostředky, služby a pole dokumentace** v horní části portálu. Když se ve výsledcích hledání zobrazí název virtuální sítě, vyberte ji.
2. Vyberte **DDoS protection**v části **nastavení**.
3. Vyberte **základní** pod **plán DDoS protection** a pak vyberte **Uložit**.

## <a name="work-with-ddos-protection-plans"></a>Práce s plány DDoS protection

1. Vyberte **všechny služby** nahoře, levém rohu portálu.
2. Zadejte *před útoky DDoS* v **filtr** pole. Když **plány DDoS protection** ve výsledcích se zobrazí, vyberte ji.
3. Vyberte plán ochrany, které chcete zobrazit v seznamu.
4. Jsou uvedeny všechny virtuální sítě, které jsou přidružené k plánu.
5. Pokud chcete odstranit plán, musíte nejprve oddělit všechny virtuální sítě z ní. Pokud chcete zrušit přidružení plánu z virtuální sítě, přečtěte si téma [zakázat před útoky DDoS pro virtuální síť](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Konfigurace výstrah pro metrik DDoS protection

Můžete vybrat libovolný dostupných metrik DDoS protection vás upozorní, když je aktivní ke zmírnění během útoku, pomocí konfigurace výstrahy monitorování Azure. Pokud jsou splněny podmínky, zadaná adresa obdrží e-mailové upozornění:

1. Vyberte **všechny služby** nahoře, levém rohu portálu.
2. Zadejte *monitorování* v **filtr** pole. Když **monitorování** se zobrazí ve výsledcích, vyberte ji.
3. Vyberte **metriky** pod **SHARED SERVICES**.
4. Zadejte, nebo vyberte vlastní hodnoty, nebo zadejte následující ukázkové hodnoty, potvrďte zbývající výchozí nastavení a pak vyberte **OK**:

    |Nastavení                  |Hodnota                                                                                               |
    |---------                |---------                                                                                           |
    |Název                     | myDdosAlert                                                                                        |
    |Předplatné             | Vyberte předplatné, které obsahuje veřejnou IP adresu, kterou chcete dostávat upozornění na.        |
    |Skupina prostředků           | Vyberte skupinu prostředků, která obsahuje veřejnou IP adresu, kterou chcete dostávat upozornění na.      |
    |Resource                 | Vyberte veřejnou IP adresu, která obsahuje veřejnou IP adresu, kterou chcete dostávat upozornění na. Před útoky DDoS monitoruje veřejné IP adresy přiřazené k prostředkům ve virtuální síti. Pokud nemáte k dispozici žádné prostředky s veřejnými IP adresami ve virtuální síti, musíte nejprve vytvořit prostředek s veřejnou IP adresu. Můžete monitorovat veřejnou IP adresu všechny prostředky nasazena prostřednictvím Resource Manageru (ne classic) uvedené v [virtuální síť pro služby Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), s výjimkou služby Azure App Service Environment a Azure VPN Gateway. Chcete-li pokračovat v tomto kurzu, můžete rychle vytvářet [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuálního počítače.                   |
    |Metrika                   | V části před útoky DDoS útoku nebo ne                                                                            |
    |Prahová hodnota                | 1 - **1** znamená, že jsou terčem útoku. **0** znamená, že nejste terčem útoku.                         |
    |Období                   | Vyberte jakékoli hodnotu zvolíte.                                                                   |
    |Upozornění prostřednictvím e-mailu         | Zaškrtávací políčko                                                                                  |
    |Další správce | Zadejte e-mailovou adresu, pokud ještě nejste e-mailu vlastníkem, přispěvatelem nebo Čtenář pro předplatné. |

    Během několika minut detekce útoku obdržíte e-mail ze metrik Azure monitoru, který vypadá podobně jako na následujícím obrázku:

    ![Výstraha na útok](./media/manage-ddos-protection/ddos-alert.png)


Pro simulaci s útoky DDoS pro ověření upozornění, najdete v článku [ověření před útoky DDoS detekce](#validate-ddos-detection).

Můžete také další informace o [konfiguraci webhooků](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [logic apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro vytvoření výstrahy.

## <a name="use-ddos-protection-telemetry"></a>Použití před útoky DDoS protection telemetrie

Telemetrie na útok je zajišťováno prostřednictvím Azure monitorování v reálném čase. Telemetrie je k dispozici pouze po dobu trvání, který se veřejná IP adresa je v rámci omezení rizik. Nevidíte telemetrických dat před nebo po vyřešení útoku.

1. Vyberte **všechny služby** nahoře, levém rohu portálu.
2. Zadejte *monitorování* v **filtr** pole. Když **monitorování** se zobrazí ve výsledcích, vyberte ji.
3. Vyberte **metriky**v části **SHARED SERVICES**.
4. Vyberte **předplatné** a **skupiny prostředků** obsahující veřejnou IP adresu, který chcete telemetrii.
5. Vyberte **veřejnou IP adresu** pro **typ prostředku**, pak vyberte konkrétní veřejná IP adresa má telemetrii.
6. Řada **dostupné metriky** se zobrazí na levé straně obrazovky. Tyto metriky, pokud je vybráno, vykreslovacích v **graf metrik Azure monitoru** na obrazovce Přehled.
7. Vyberte **agregace** zadejte jako **Max**

Názvy metrik prezentují paketů různých typů a bajtů vs. pakety, základní konstrukce názvy značek na každou metriku následujícím způsobem:

- **Název značky zahozené** (například **příchozí pakety vyřadit DDoS**): Počet paketů vyřadit/odstranil v systému ochrany před útoky DDoS.
- **Název značky předané** (například **příchozí pakety předané DDoS**): Počet pakety předané v systému před útoky DDoS na cílové virtuální IP adresy – přenos, který nebyl filtrovat.
- **Žádný název značky** (například **příchozí pakety DDoS**): Celkový počet paketů, které přišel do scrubbingu systému – představuje součet pakety zahozena a předaných dál.

Pro simulaci s útoky DDoS pro ověření telemetrická data, najdete v článku [ověření před útoky DDoS detekce](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Zobrazit zásady omezení rizik před útoky DDoS

DDoS Protection standardní použije tři zásady automaticky laděná omezení rizik (TCP SYN, TCP a UDP) pro každou veřejnou IP adresu chráněných prostředků ve virtuální síti, který má povolené před útoky DDoS. Prahové hodnoty zásady můžete zobrazit tak, že vyberete **TCP příchozí pakety pro aktivaci omezení rizik útoků DDoS** a **UDP příchozí pakety pro aktivaci omezení rizik útoků DDoS** metriky ve službě **agregace** zadejte jako "Max", jak je znázorněno na následujícím obrázku:

![Zobrazit zásady omezení rizik](./media/manage-ddos-protection/view-mitigation-policies.png)

Prahové hodnoty zásad jsou automaticky nakonfigurované přes Azure machine learning-based sítě provoz profilace. Pouze v případě, že zásady prahové hodnoty dochází omezení rizik útoků DDoS pro IP adresu terčem útoku.

## <a name="configure-ddos-attack-analytics"></a>Konfigurace analýzy útoku DDoS
Azure DDoS Protection standard poskytuje útoku podrobné přehledy a vizualizace pomocí Analytics útoku DDoS. Ochrana před útoky DDoS jejich virtuálních sítích zákazníků obsahují podrobné přehled o provozu útoku a akce ke zmírnění tohoto útoku prostřednictvím sestav omezení rizik útoků & protokolů toku omezení rizik. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Konfigurace sestavy ke zmírnění útoků DDoS
Sestavy omezení rizik útoků používá Netflow data protokolu, který je agregován, aby poskytoval podrobné informace o útoku na váš prostředek. Kdykoli je prostředek veřejné IP adresy terčem útoku, generování sestav se spustí poté, co spustí omezení rizik. Bude vygenerována přírůstkové zpráva dobu omezení rizik celý každých 5 minut a sestavu po omezení rizik. Tím je zajištěno, že v události, které útoky DDoS pokračuje delší dobu, bude moci zobrazit aktuální snímek sestavy ke zmírnění každých 5 minut a úplný přehled jednou zmírnění útoků je nad. 

1. Vyberte **všechny služby** nahoře, levém rohu portálu.
2. Zadejte *monitorování* v **filtr** pole. Když **monitorování** se zobrazí ve výsledcích, vyberte ji.
3. V části **nastavení**vyberte **nastavení diagnostiky**.
4. Vyberte **předplatné** a **skupiny prostředků** obsahující veřejnou IP adresu, kterou chcete protokolovat.
5. Vyberte **veřejnou IP adresu** pro **typ prostředku**, pak vyberte konkrétní veřejná IP adresa chcete protokolovat metriky pro.
6. Vyberte **zapnout diagnostiku pro shromažďování protokolů DDoSMitigationReports** a podle potřeby vyberte jako mnoho z následujících možností:

    - **Archivovat do účtu úložiště**: Data se zapisují do účtu služby Azure Storage. Další informace o této možnosti najdete v tématu [archivace diagnostických protokolů](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Stream do centra událostí**: Povolí protokol příjemce pro sbírání protokolů pomocí Azure Event Hubs. Služba Event hubs povolení integrace se službou Splunk nebo jiných systémů SIEM. Další informace o této možnosti najdete v tématu [Stream diagnostických protokolů do služby event hub](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Odeslání do Log Analytics**: Zapisuje protokoly do služby Azure Monitor. Další informace o této možnosti najdete v tématu [shromáždí protokoly pro použití v Azure Monitor protokoly](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Přírůstkové & po útoku zmírnění sestavy obsahovat následující pole
- Vektory útoku
- Statistiky provozu
- Důvod vynechaných paketů
- Protokoly
- Prvních 10 zdroj zemích nebo oblastech
- Prvních 10 zdroj čísla ASN

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Konfigurace protokolů toku omezení rizik útoků DDoS
Protokoly toku omezení rizik útoků umožní zkontrolovat zhoršení provozu předávají provoz a další zajímavé datapoints během aktivního útoku DDoS v téměř reálném čase. Můžete ingestovat nepřetržitý datový proud těchto dat do systémů SIEM prostřednictvím centra událostí pro monitorování v téměř reálném čase, potenciální akcí a potřebu operací ochrany před mobilními. 

1. Vyberte **všechny služby** nahoře, levém rohu portálu.
2. Zadejte *monitorování* v **filtr** pole. Když **monitorování** se zobrazí ve výsledcích, vyberte ji.
3. V části **nastavení**vyberte **nastavení diagnostiky**.
4. Vyberte **předplatné** a **skupiny prostředků** obsahující veřejnou IP adresu, kterou chcete protokolovat.
5. Vyberte **veřejnou IP adresu** pro **typ prostředku**, pak vyberte konkrétní veřejná IP adresa chcete protokolovat metriky pro.
6. Vyberte **zapnout diagnostiku pro shromažďování protokolů DDoSMitigationFlowLogs** a podle potřeby vyberte jako mnoho z následujících možností:

    - **Archivovat do účtu úložiště**: Data se zapisují do účtu služby Azure Storage. Další informace o této možnosti najdete v tématu [archivace diagnostických protokolů](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Stream do centra událostí**: Povolí protokol příjemce pro sbírání protokolů pomocí Azure Event Hubs. Služba Event hubs povolení integrace se službou Splunk nebo jiných systémů SIEM. Další informace o této možnosti najdete v tématu [Stream diagnostických protokolů do služby event hub](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Odeslání do Log Analytics**: Zapisuje protokoly do služby Azure Monitor. Další informace o této možnosti najdete v tématu [shromáždí protokoly pro použití v Azure Monitor protokoly](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Chcete-li zobrazit data protokolů toku v řídicím panelu Azure analytics, můžete importovat ukázkový řídicí panel z https://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

Protokoly toku bude mít tahle pole: 
- Zdrojová IP adresa
- Cílová IP adresa
- Zdrojový port 
- Cílový port 
- Typ protokolu 
- Akce při omezení rizik



## <a name="validate-ddos-detection"></a>Ověření zjišťování před útoky DDoS

Microsoft uzavřel partnerství s [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) k vytváření rozhraní ve kterém můžete vygenerovat provoz s podporou služba DDoS Protection veřejné IP adresy pro simulace. Simulace zarážku Cloud vám umožní:

- Ověření, jak Microsoft Azure DDoS Protection chrání vaše prostředky Azure před útoky DDoS
- Optimalizace procesu reakce na incidenty v rámci útoky DDoS
- Dodržování předpisů dokument před útoky DDoS
- Trénování vaše týmy zabezpečení sítě

## <a name="permissions"></a>Oprávnění

Pro práci s plány DDoS protection, musí mít váš účet přiřazenou k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) role, která je přiřazena příslušné akce uvedené v následující tabulce:

| Akce                                            | Název                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Přečtěte si plán DDoS protection              |
| Microsoft.Network/ddosProtectionPlans/write       | Vytvořit nebo aktualizovat plán DDoS protection  |
| Microsoft.Network/ddosProtectionPlans/delete      | Odstranit plán DDoS protection            |
| Microsoft.Network/ddosProtectionPlans/join/action | Připojte se k plánu DDoS protection              |

Povolit ochranu před útoky DDoS pro virtuální síť, váš účet musí také mít přiřazenou příslušnou [akce pro virtuální sítě](manage-virtual-network.md#permissions).

## <a name="next-steps"></a>Další postup

- Vytvoření a použití [Azure policy](policy-samples.md) pro virtuální sítě