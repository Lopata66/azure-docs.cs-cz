---
title: Použití privátního odkazu Azure k bezpečnému připojení sítí k Azure Monitor
description: Použití privátního odkazu Azure k bezpečnému připojení sítí k Azure Monitor
author: nkiest
ms.author: nikiest
ms.topic: conceptual
ms.date: 05/20/2020
ms.subservice: ''
ms.openlocfilehash: 4ef7e4058c4f9cb458f4036ad4b315f5e85036b1
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170711"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Použití privátního odkazu Azure k bezpečnému připojení sítí k Azure Monitor

> [!IMPORTANT]
> V tuto chvíli musíte **požádat o přístup** k používání této možnosti. Můžete požádat o přístup pomocí [formuláře registrace](https://aka.ms/AzMonPrivateLinkSignup).


[Privátní odkaz Azure](../../private-link/private-link-overview.md) umožňuje bezpečně propojit služby Azure PaaS s vaší virtuální sítí pomocí privátních koncových bodů. U mnoha služeb stačí nastavit koncový bod na prostředek. Azure Monitor je ale Constellation různých vzájemně propojených služeb, které vzájemně spolupracují za účelem monitorování vašich úloh. V důsledku toho jsme vytvořili prostředek nazvaný Azure Monitor obor privátních odkazů (AMPLS), který umožňuje definovat hranice vaší sítě pro monitorování a připojit se k virtuální síti. Tento článek popisuje, kdy použít a jak nastavit obor privátního odkazu Azure Monitor.

## <a name="advantages"></a>Výhody

Pomocí privátního odkazu můžete:

- Připojte soukromě k Azure Monitor bez nutnosti otevírat přístup k veřejné síti.
- Ujistěte se, že jsou k datům pro monitorování dostupná jenom prostřednictvím autorizovaných privátních sítí.
- Zabránění exfiltrace dat ze svých privátních sítí definováním konkrétních prostředků Azure Monitor, které se připojují prostřednictvím privátního koncového bodu
- Bezpečně připojte soukromou místní síť k Azure Monitor pomocí ExpressRoute a privátního odkazu.
- Zachovat veškerý provoz v síti Microsoft Azure páteřní sítě

Další informace najdete v tématu [klíčové výhody privátního propojení](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Jak to funguje

Azure Monitor obor privátních odkazů je prostředek seskupení pro připojení jednoho nebo více privátních koncových bodů (a proto virtuální sítě, které jsou součástí) k jednomu nebo více prostředkům Azure Monitor. Prostředky zahrnují Log Analytics pracovní prostory a Application Insights součásti.

![Diagram topologie prostředků](./media/private-link-security/private-link-topology-1.png)

> [!NOTE]
> Jeden prostředek Azure Monitor může patřit do více AMPLSs, ale nemůžete připojit jednu virtuální síť k více než jednomu AMPLS. 

## <a name="planning-based-on-your-network"></a>Plánování na základě vaší sítě

Před nastavením prostředků AMPLS Zvažte požadavky na izolaci sítě. Vyhodnoťte přístup k virtuálním sítím k veřejnému Internetu a omezení přístupu každého z vašich Azure Monitorch prostředků (tj. Application Insights komponent a Log Analytics pracovních prostorů).

### <a name="evaluate-which-virtual-networks-should-connect-to-a-private-link"></a>Vyhodnotit, které virtuální sítě by se měly připojit k privátnímu propojení

Začněte tím, že vyhodnocujete, které z vašich virtuálních sítí (virtuální sítě) mají omezený přístup k Internetu. Virtuální sítě, které mají bezplatný internet, nemusí vyžadovat privátní odkaz pro přístup k prostředkům Azure Monitor. Monitorování prostředků, ke kterým se virtuální sítě připojuje, může omezit příchozí provoz a vyžadovat připojení k privátnímu propojení (buď pro ingestování protokolu, nebo pro dotaz). V takových případech dokonce i virtuální síť, která má přístup k veřejnému Internetu, se musí připojit k těmto prostředkům přes privátní odkaz a prostřednictvím AMPLS.

### <a name="evaluate-which-azure-monitor-resources-should-have-a-private-link"></a>Vyhodnotit, které Azure Monitor prostředky by měly mít privátní odkaz

Projděte si všechny prostředky Azure Monitor:

- Má prostředek umožňovat přijímání protokolů z prostředků umístěných pouze v konkrétních virtuální sítě?
- Má se prostředek dotazovat jenom klienti umístění na konkrétní virtuální sítě?

Pokud je odpověď na kteroukoli z těchto otázek Ano, nastavte omezení popsané v tématu [konfigurace log Analyticsch](#configure-log-analytics) pracovních prostorů a [Konfigurace Application Insightsch komponent](#configure-application-insights) a přidružte tyto prostředky k jednomu nebo několika AMPLS. Virtuální sítě, které by měly mít přístup k těmto prostředkům monitorování, musí mít privátní koncový bod, který se připojí k příslušnému AMPLS.
Nezapomeňte – stejné pracovní prostory nebo aplikace můžete propojit s několika AMPLS, aby mohly být dostupné v různých sítích.

### <a name="group-together-monitoring-resources-by-network-accessibility"></a>Seskupit dohromady monitorování prostředků podle dostupnosti sítě

Vzhledem k tomu, že se každá virtuální síť může připojovat pouze k jednomu AMPLS prostředku, musíte seskupit společné prostředky monitorování, které by měly být přístupné pro stejné sítě. Nejjednodušší způsob, jak spravovat toto seskupení, je vytvořit jeden AMPLS na virtuální síť a vybrat prostředky pro připojení k této síti. Chcete-li však omezit prostředky a zlepšit spravovatelnost, můžete chtít znovu použít AMPLS napříč sítěmi.

Pokud se například vaše interní virtuální sítě VNet1 a VNet2 by měly připojit k pracovním prostorům pracovního prostoru workspace1 a Workspace2 a komponenta Application Insights Application Insights 3, přidružte ke stejnému AMPLS všechny tři prostředky. Pokud má síti vnet3 jenom přístup k pracovního prostoru workspace1, vytvořte další prostředek AMPLS, přidružte k němu pracovního prostoru workspace1 a připojte síti vnet3, jak je znázorněno v následujících diagramech:

![Diagram AMPLS topologie](./media/private-link-security/ampls-topology-a-1.png)

![Diagram topologie AMPLS B](./media/private-link-security/ampls-topology-b-1.png)

## <a name="example-connection"></a>Příklad připojení

Začněte vytvořením prostředku oboru privátního propojení Azure Monitor.

1. V Azure Portal **vytvořte prostředek** a vyhledejte **Azure monitor oboru privátního odkazu**. 
2. Klikněte na **vytvořit**. 
3. Vyberte předplatné a skupinu prostředků. 
4. Zadejte název AMPLS. Je nejvhodnější použít název, který je jasný a jaký je účel a hranice zabezpečení, pro které se rozsah používá, aby někdo nechtěně nenarušil hranice zabezpečení sítě. Například "AppServerProdTelem". 
5. Klikněte na tlačítko **zkontrolovat + vytvořit**. 
6. Potvrďte ověření a pak klikněte na **vytvořit**.

## <a name="connect-azure-monitor-resources"></a>Připojit Azure Monitor prostředky

AMPLS se můžete připojit jako první k soukromým koncovým bodům a potom k Azure Monitor prostředkům nebo naopak, ale proces připojení pokračuje rychleji, pokud začnete s prostředky Azure Monitor. Tady je postup připojení Azure Monitor Log Analytics pracovní prostory a Application Insights komponenty k AMPLS

1. V Azure Monitor oboru privátního propojení klikněte na **Azure monitor prostředky** v nabídce vlevo. Klikněte na tlačítko **Přidat** .
2. Přidejte pracovní prostor nebo komponentu. Kliknutím na tlačítko **Přidat** zobrazíte dialogové okno, kde můžete vybrat Azure monitor prostředky. Můžete procházet předplatná a skupiny prostředků, nebo můžete zadat jejich jméno, abyste je mohli vyfiltrovat. Vyberte pracovní prostor nebo komponentu a kliknutím na tlačítko **použít** je přidejte do svého oboru.

    ![Snímek obrazovky s výběrem oboru uživatelského rozhraní](./media/private-link-security/ampls-select-2.png)

### <a name="connect-to-a-private-endpoint"></a>Připojení k privátnímu koncovému bodu

Teď, když máte prostředky připojené k AMPLS, vytvořte privátní koncový bod pro připojení k naší síti. Tuto úlohu můžete provést v [Azure Portal privátním linkovém centru](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)nebo v Azure monitor oboru privátních odkazů, jak to udělalo v tomto příkladu.

1. V prostředku oboru klikněte v nabídce prostředky na možnost **připojení privátního koncového bodu** . Kliknutím na **soukromý koncový bod** spusťte proces vytváření koncových bodů. Můžete také schválit připojení, která byla zahájena v centru privátních propojení, a to tak, že je vyberete a kliknete na tlačítko **schválit**.

    ![Snímek obrazovky uživatelského rozhraní připojení privátního koncového bodu](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Vyberte předplatné, skupinu prostředků a název koncového bodu a oblast, ve které by měla být aktivní. Region musí být stejná oblast jako virtuální síť, ke které se připojíte.

3. Klikněte na **Další: prostředek**. 

4. Na obrazovce prostředek

   a. Vyberte **předplatné** , které obsahuje prostředek privátního oboru Azure monitor. 

   b. Jako **typ prostředku**vyberte **Microsoft. Insights/privateLinkScopes**. 

   c. V rozevíracím seznamu **prostředek** vyberte obor privátního propojení, který jste vytvořili dříve. 

   d. Klikněte na **Další: >konfigurace **.
      ![Snímek obrazovky výběru vytvoření privátního koncového bodu](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. V podokně Konfigurace

   a.    Vyberte **virtuální síť** a **podsíť** , ke kterým se chcete připojit, k prostředkům Azure monitor. 
 
   b.    Pro **integraci s privátní zónou DNS**vyberte **Ano** a umožněte tak, aby se automaticky vytvořila nová zóna privátní DNS. 
 
   c.    Klikněte na **Zkontrolovat a vytvořit**.
 
   d.    Nechte ověření Pass. 
 
   e.    Klikněte na **Vytvořit**. 

    ![Snímek obrazovky s výběrem vytvořit privátní pro endpoint2 u](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Nyní jste vytvořili nový privátní koncový bod, který je připojen k tomuto Azure Monitor oboru privátního propojení.

## <a name="configure-log-analytics"></a>Konfigurace Log Analytics

Přejděte na web Azure Portal. Ve vašem prostředku pracovního prostoru Azure Monitor Log Analytics je položka nabídky **izolace sítě** na levé straně. Z této nabídky můžete řídit dva různé stavy. 

![Izolace sítě LA](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

Nejprve můžete připojit tento prostředek Log Analytics k jakýmkoli Azure Monitorm oborům privátního propojení, ke kterým máte přístup. Klikněte na tlačítko **Přidat** a vyberte Azure monitor obor privátních odkazů.  Kliknutím na **použít** ho připojte. Na této obrazovce se zobrazí všechny připojené obory. Když se toto připojení připojuje k tomuto pracovnímu prostoru, umožní síťovému provozu v připojených virtuálních sítích. Vytvoření připojení má stejný účinek jako připojení z oboru, jako jsme provedli [připojení Azure Monitorch prostředků](#connect-azure-monitor-resources).  

Za druhé můžete řídit, jak se tento prostředek dá oslovit mimo rozsahy privátních odkazů uvedených výše. Pokud nastavíte možnost **povolí přístup k veřejné síti pro** ingestování na **ne**, počítače mimo připojené obory nemůžou do tohoto pracovního prostoru nahrávat data. Pokud nastavíte možnost **povolí přístup k veřejné síti pro dotazy** na **ne**, počítače mimo rozsah nebudou mít přístup k datům v tomto pracovním prostoru. Tato data zahrnují přístup k sešitům, řídicím panelům, klientským prostředím založeném na rozhraní API pro dotazy, přehledům v Azure Portal a dalším. Prostředí běžící mimo Azure Portal, které spotřebovává Log Analytics data, musí být spuštěná také v rámci virtuální sítě propojené s privátní sítí.

Přístup tímto způsobem se omezuje jenom na data v pracovním prostoru. Změny konfigurace, včetně zapnutí nebo vypnutí těchto nastavení přístupu, se spravují pomocí Azure Resource Manager. Omezte přístup k Správce prostředků pomocí příslušných rolí, oprávnění, síťových ovládacích prvků a auditování. Další informace najdete v tématu [Azure monitor role, oprávnění a zabezpečení](roles-permissions-security.md).

> [!NOTE]
> Protokoly a metriky nahrané do pracovního prostoru prostřednictvím [nastavení diagnostiky](diagnostic-settings.md) přecházejí přes zabezpečený privátní kanál Microsoft a tato nastavení se nekontrolují.

## <a name="configure-application-insights"></a>Konfigurace Application Insights

Přejděte na web Azure Portal. Ve složce Azure Monitor Application Insights prostředek součásti je **izolace sítě** položky nabídky na levé straně. Z této nabídky můžete řídit dva různé stavy.

![Izolace sítě AI](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Nejdřív můžete připojit tento prostředek Application Insights, abyste Azure Monitor obory privátních odkazů, ke kterým máte přístup. Klikněte na tlačítko **Přidat** a vyberte **Azure monitor obor privátních odkazů**. Kliknutím na použít ho připojte. Na této obrazovce se zobrazí všechny připojené obory. Když se toto připojení připojuje k této součásti, umožní síťovému provozu v propojených virtuálních sítích. Vytvoření připojení má stejný účinek jako připojení z oboru, jako jsme provedli [připojení Azure Monitorch prostředků](#connect-azure-monitor-resources). 

Za druhé můžete řídit, jak se tento prostředek dá oslovit mimo rozsahy privátních vazeb, které jsou uvedené dřív. Pokud nastavíte možnost **povolí přístup k veřejné síti pro** ingestování na **ne**, počítače nebo sady SDK mimo připojené obory nemůžou do této součásti nahrávat data. Pokud nastavíte možnost **povolí přístup k veřejné síti pro dotazy** na **ne**, počítače mimo rozsah nebudou mít přístup k datům v tomto prostředku Application Insights. Tato data zahrnují přístup k protokolům APM, metrikám a živému streamu metrik a také k prostředí postaveným nahoře, jako jsou sešity, řídicí panely, prostředí klientů založené na rozhraní API, přehledy v Azure Portal a další. 

Všimněte si, že prostředí pro využívání mimo portál musí také běžet v rámci veřejné virtuální sítě, která obsahuje monitorované úlohy. 

K privátnímu odkazu budete muset přidat prostředky hostující monitorovaná zatížení. Zde najdete [dokumentaci](https://docs.microsoft.com/azure/app-service/networking/private-endpoint) , jak to udělat App Services.

Přístup tímto způsobem se omezuje jenom na data v prostředku Application Insights. Změny konfigurace, včetně zapnutí nebo vypnutí těchto nastavení přístupu, se spravují pomocí Azure Resource Manager. Místo toho omezte přístup k Správce prostředků pomocí příslušných rolí, oprávnění, síťových ovládacích prvků a auditování. Další informace najdete v tématu [Azure monitor role, oprávnění a zabezpečení](roles-permissions-security.md).

> [!NOTE]
> Aby bylo možné plně zabezpečit Application Insights na základě pracovních prostorů, musíte uzamknout přístup k prostředkům Application Insights a také k příslušnému pracovnímu prostoru Log Analytics.

> [!NOTE]
> Diagnostika na úrovni kódu (Profiler/ladicí program) aktuálně nepodporuje privátní propojení.

## <a name="use-apis-and-command-line"></a>Použití rozhraní API a příkazového řádku

Proces popsaný výše můžete automatizovat pomocí Azure Resource Manager šablon a rozhraní příkazového řádku.

Chcete-li vytvořit a spravovat obory privátních propojení, použijte příkaz [AZ monitor Private-Link-Scope](https://docs.microsoft.com/cli/azure/monitor/private-link-scope?view=azure-cli-latest). Pomocí tohoto příkazu můžete vytvořit obory, přidružit Log Analytics pracovní prostory a Application Insights komponenty a přidávat/odebírat a schvalovat soukromé koncové body.

Ke správě přístupu k síti použijte příznaky `[--ingestion-access {Disabled, Enabled}]` a `[--query-access {Disabled, Enabled}]` v [Log Analytics pracovní prostory](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest) nebo [Application Insights součásti](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest).

## <a name="collect-custom-logs-over-private-link"></a>Shromažďovat vlastní protokoly přes privátní propojení

Účty úložiště se používají v procesu přijímání vlastních protokolů. Ve výchozím nastavení se používají účty úložiště spravované službou. Pokud ale chcete ingestovat vlastní protokoly na soukromých odkazech, musíte použít vlastní účty úložiště a přidružit je k Log Analyticsm pracovním prostorům. Další informace o tom, jak tyto účty nastavit pomocí [příkazového řádku](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace/linked-storage?view=azure-cli-latest), najdete v tématu.

Další informace o zavedení vlastního účtu úložiště najdete v tématu [účty úložiště vlastněné zákazníkem pro](private-storage.md) ingestování protokolů.

## <a name="restrictions-and-limitations"></a>Omezení a omezení

### <a name="agents"></a>Agenti

Nejnovější verze agentů systému Windows a Linux musí být používány v privátních sítích, aby bylo možné přijímat zabezpečené ingestování telemetrie do Log Analytics pracovních prostorů. Starší verze nemůžou nahrávat data monitorování v privátní síti.

**Agent Log Analytics pro Windows**

Použijte Log Analytics agenta verze 10.20.18038.0 nebo novější.

**Linuxový agent Log Analytics**

Použijte agenta verze 1.12.25 nebo novější. Pokud nemůžete, spusťte na svém VIRTUÁLNÍm počítači následující příkazy.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>portál Azure

Chcete-li použít Azure Monitor portálu, jako je Application Insights a Log Analytics, je nutné, aby byla rozšíření Azure Portal a Azure Monitor dostupná v privátních sítích. Do brány firewall přidejte [značky služby](../../firewall/service-tags.md) **azureactivedirectory selhala**, **AzureResourceManager**, **AzureFrontDoor. FirstParty**a **AzureFrontDoor. front-endu** .

### <a name="programmatic-access"></a>Programový přístup

Pokud chcete použít REST API, [CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest) nebo PowerShell s Azure monitor v privátních sítích, přidejte do brány firewall [značky služby](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)  **azureactivedirectory selhala** a **AzureResourceManager** .

Přidání těchto značek vám umožní provádět akce, jako je například dotazování dat protokolu, vytváření a Správa Log Analyticsch pracovních prostorů a komponent AI.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Stažení sady SDK Application Insights ze sítě pro doručování obsahu

Vytvořte ve svém skriptu kód JavaScriptu tak, aby se prohlížeč nepokoušel stáhnout kód ze sítě CDN. Příklad je k dispozici na [GitHubu](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup) .

### <a name="log-analytics-solution-download"></a>Stažení řešení Log Analytics

Pokud chcete, aby mohl agent Log Analytics stahovat balíčky řešení, přidejte příslušné plně kvalifikované názvy domény do seznamu povolených bran firewall. 


| Cloudové prostředí | Prostředek agenta | Porty | Směr |
|:--|:--|:--|:--|
|Veřejný partnerský vztah Azure     | scadvisor.blob.core.windows.net         | 443 | Odchozí
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Odchozí
|Azure (Čína) 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Odchozí

