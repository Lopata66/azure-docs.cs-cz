---
title: Seskupit počítače s využitím závislostí počítačů pomocí služby Azure Migrate | Dokumentace Microsoftu
description: Popisuje, jak vytvořit posouzení závislostí počítačů pomocí služby Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: af47678b19209936aed86c132a8a3f400c3a7e8f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795028"
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Seskupení počítačů s využitím mapování závislostí počítačů

Tento článek popisuje, jak vytvořit skupinu počítačů pro [Azure Migrate](migrate-overview.md) posouzení vizualizací závislostí počítačů. Obvykle použijete tuto metodu, pokud chcete posouzení skupiny virtuálních počítačů s vyšší úrovní spolehlivosti mezi Kontrola závislostí počítačů, před spuštěním posouzení. Vizualizace závislostí můžete efektivně naplánovat migraci do Azure. To vám pomůže zajistit, že nic se zachovají a překvapením, že není dojde k výpadku při migraci do Azure. Je možné vyhledat všechny vzájemně závislých systémů, které je potřeba migrovat společně a zjistit, jestli spuštěný systém stále poskytuje uživatelům nebo je kandidátem pro vyřazení z provozu místo migrace.

> [!NOTE]
> Funkce vizualizace závislostí není k dispozici ve službě Azure Government.

## <a name="prepare-for-dependency-visualization"></a>Příprava pro vizualizace závislostí
Azure Migrate využívá řešení Service Map v protokolech Azure Monitor umožňuje vizualizace závislostí počítačů.

### <a name="associate-a-log-analytics-workspace"></a>Přiřadit pracovní prostor Log Analytics
Využití vizualizace závislostí, je potřeba přiřadit pracovní prostor Log Analytics, novou nebo existující s projekt Azure Migrate. Můžete pouze vytvářet nebo připojit pracovní prostor v rámci stejného předplatného, ve kterém se vytvoří projekt migrace.

- K připojení do pracovního prostoru Log Analytics do projektu, v **přehled**, přejděte na stránku **Essentials** části projektu klikněte na tlačítko **vyžaduje konfiguraci**

    ![Přiřadit pracovní prostor Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Při přiřazení pracovního prostoru, bude mít možnost vytvořit nový pracovní prostor nebo připojení existující:
  - Když vytvoříte nový pracovní prostor, musíte zadat název pracovního prostoru. Pracovní prostor se pak vytvoří v oblasti, ve stejném [zeměpisná oblast Azure](https://azure.microsoft.com/global-infrastructure/geographies/) jako projekt migrace.
  - Po připojení existujícího pracovního prostoru, můžete vybrat ze všech dostupných pracovních prostorů ve stejném předplatném jako projekt migrace. Všimněte si, že jsou uvedené pouze ty pracovní prostory, které byly vytvořeny v oblasti kde [Service Map je podporována](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). Aby bylo možné se připojit s pracovním prostorem, ujistěte se, že máte "Čtečky" přístup do pracovního prostoru.

> [!NOTE]
> Nelze změnit pracovní prostor, přidružené k projektu migrace.

### <a name="download-and-install-the-vm-agents"></a>Stažení a instalace agentů virtuálního počítače
Po konfiguraci pracovního prostoru, budete muset stáhnout a nainstalovat agenty na každém v místním počítači, který chcete vyhodnotit. Kromě toho, pokud máte počítače bez připojení k Internetu, musíte stáhnout a nainstalovat [Log Analytics gateway](../azure-monitor/platform/gateway.md) na ně.

1. V **přehled**, klikněte na tlačítko **spravovat** > **počítače**a vyberte požadovaný počítač.
2. V **závislosti** sloupce, klikněte na tlačítko **instalovat agenty**.
3. Na **závislosti** stránce, stáhněte a nainstalujte Microsoft Monitoring Agent (MMA) a agenta závislostí na každém virtuálním počítači, na kterou chcete vyhodnotit.
4. Zkopírujte ID a klíč pracovního prostoru. Budete je potřebovat při instalaci agenta MMA na místním počítači.

> [!NOTE]
> K automatizaci instalace agentů můžete použít libovolný nástroj nasazení, jako je System Center Configuration Manager nebo použijte nástroj našich partnerů [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), který obsahuje řešení nasazení agenta ke službě Azure Migrate.

### <a name="install-the-mma"></a>Instalace agenta MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Nainstalujte agenta na počítači s Windows

Instalace agenta na počítači s Windows:

1. Dvakrát klikněte na staženého agenta.
2. Na **úvodní** stránce klikněte na **Další**. Na stránce **Licenční podmínky** kliknutím na **Souhlasím** přijměte licenci.
3. V **cílovou složku**, udržovat nebo změnit výchozí instalační složku > **Další**.
4. V **možnosti instalace agenta**vyberte **Azure Log Analytics** > **Další**.
5. Klikněte na tlačítko **přidat** přidáte nový pracovní prostor Log Analytics. Vložte ID pracovního prostoru a klíč, který jste zkopírovali z portálu. Klikněte na **Další**.

Agenta můžete nainstalovat z příkazového řádku nebo pomocí automatizovaného metody jako je System Center Configuration Manager. [Další informace](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) o použití těchto metod k instalaci agenta MMA.

#### <a name="install-the-agent-on-a-linux-machine"></a>Nainstalujte agenta na počítači s Linuxem

Instalace agenta na počítači s Linuxem:

1. Přeneste příslušný balíček (x86 nebo x64) na počítači s Linuxem pomocí příkazu scp/sftp.
2. Instalaci sady pomocí argumentu--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Další informace](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) o seznam operačních systémů Linux nepodporuje agenta MMA.

#### <a name="install-the-agent-on-a-machine-monitored-by-scom"></a>Nainstalujte agenta na počítači sledováno SCOM

Počítače monitorované pomocí System Center Operations Manager 2012 R2 nebo novější není nutné k instalaci agenta MMA. Řešení Service Map je integrovaná se sadou SCOM, které využívá připojení MMA SCOM ke shromažďování dat potřebné závislosti. Můžete povolit podle pokynů k integraci [tady](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Všimněte si však, že agenta závislostí, bude nutné nainstalovat na těchto počítačích.


### <a name="install-the-dependency-agent"></a>Instalace agenta závislostí
1. Instalace agenta závislostí na počítači s Windows, klikněte dvakrát na instalační soubor a postupujte podle pokynů průvodce.
2. Pokud chcete nainstalovat agenta závislostí na počítači s Linuxem, nainstalujte jako uživatel root pomocí následujícího příkazu:

    ```sh InstallDependencyAgent-Linux64.bin```

Další informace o podpoře agenta závislostí [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) a [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) operačních systémů.

[Další informace](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) o použití skriptů k instalaci agenta závislostí.


## <a name="create-a-group"></a>Vytvoření skupiny

1. Po instalaci agentů, přejděte na portál a klikněte na tlačítko **spravovat** > **počítače**.
2. Hledání počítače, kam jste nainstalovali agenty.
3. **Závislosti** sloupec pro počítač by měl nyní zobrazit jako **zobrazit závislosti**. Klikněte na sloupec a zobrazit závislosti počítače.
4. Mapa závislostí pro počítač se zobrazí následující podrobnosti:
    - Odchozí připojení (servery) TCP do/z počítače a příchozí (klientů)
        - Závislé počítače, které nemají nainstalovaného agenta MMA a závislosti jsou seskupené podle čísla portů
        - Závislé počítače, které mají agenta MMA a instalaci agenta závislosti jsou uvedeny v samostatných polích.
    - Spuštěné procesy v rámci počítače, můžete rozbalit každé pole počítače zobrazit procesy
    - Vlastnosti, jako je plně kvalifikovaný název domény, operačního systému, atd. adresu MAC každého počítače, můžete kliknout na každé pole počítače zobrazit tyto podrobnosti

      ![Zobrazení závislostí počítačů](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. Po kliknutí na dobu trvání rozsah popisku čas můžete prohlédnout závislosti pro různé dob trvání. Rozsah ve výchozím nastavení je jedna hodina. Můžete upravit časový rozsah, nebo zadat počáteční a koncové datum a dobu trvání.

   > [!NOTE]
   >    Vizualizace závislostí uživatelského rozhraní v současné době nepodporuje výběr časový rozsah, který je delší než hodinu. Použití Azure monitoru zaznamená do [zadávat dotazy na data závislostí](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) za delší dobu.

5. Po identifikaci závislých počítačů, které chcete seskupit dohromady pomocí Ctrl + kliknutí výběr více počítačů na mapě, a klikněte na **skupiny počítačů**.
6. Zadejte název skupiny. Ověřte, že se službou Azure Migrate zjistí závislých počítačů.

    > [!NOTE]
    > Pokud se službou Azure Migrate nenašel se závislý počítač, nelze jej přidat do skupiny. Tyto počítače přidat do skupiny, budete muset spustit proces zjišťování znovu s správný obor v systému vCenter Server a ujistěte se, že na počítači zjišťuje službou Azure Migrate.  

7. Pokud chcete vytvořit posouzení pro tuto skupinu, vyberte zaškrtávací políčko, chcete-li vytvořit nové posouzení pro skupinu.
8. Klikněte na tlačítko **OK** uložte skupinu.

Jakmile se vytvoří skupina, se doporučuje nainstalovat agenty na všech počítačích skupině a skupinu Upřesnit vizualizací závislostí celou skupinu.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Dotazování na závislost data z protokolů Azure Monitor

Závislost data zachycená pomocí mapy služeb je k dispozici pro dotazování v pracovním prostoru Log Analytics přidružený k projektu Azure Migrate. [Další informace](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) tabulky dat Service Map pro dotazování ve službě Azure Monitor protokoly. 

Spuštění dotazů Kusto:

1. Po instalaci agentů, přejděte na portál a klikněte na tlačítko **přehled**.
2. V **přehled**, přejděte na stránku **Essentials** části projektu a klikněte na název pracovního prostoru k dispozici vedle **pracovní prostor OMS**.
3. Na stránce pracovního prostoru Log Analytics, klikněte na tlačítko **Obecné** > **protokoly**.
4. Napsat dotaz pro shromažďování dat závislosti pomocí protokolů z Azure monitoru. Ukázkové dotazy najdete v další části.
5. Spusťte dotaz kliknutím na spustit. 

[Další informace](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) o tom, jak psát dotazy Kusto. 

### <a name="sample-azure-monitor-logs-queries"></a>Ukázka Azure Monitor zaznamenává dotazy

Tady jsou ukázkové dotazy, které vám umožní extrahovat data závislostí. Můžete upravit dotazy k extrakci upřednostňované datových bodů. Úplný seznam polí v závislosti datových záznamů je k dispozici [tady](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records). Najít další ukázkové dotazy [tady](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Shrnutí příchozí připojení na sadu počítačů

Všimněte si, že záznamy v tabulce metrik připojení, VMConnection, nepředstavují jednotlivých fyzických síťových připojení. Víc fyzických síťových připojení jsou seskupeny do logických připojení. [Další informace](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) o jak fyzické síťové připojení se data agregují do jednoho logického záznamu v VMConnection. 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Shrnutí objem dat odeslaných a přijatých u příchozích připojení mezi sadu počítačů

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Další postup

- [Další informace](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) o nejčastějších dotazech na vizualizaci závislostí.
- [Zjistěte, jak](how-to-create-group-dependencies.md) pro upřesnění skupiny pomocí vizualizace závislostí skupin.
- [Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
