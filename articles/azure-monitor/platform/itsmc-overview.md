---
title: IT Service Management Connector v Azure Log Analytics | Microsoft Docs
description: Tento článek poskytuje přehled konektoru pro správu služeb IT (ITSMC) a informace o tom, jak toto řešení použít k centrálnímu monitorování a správě pracovních položek ITSM v Azure Log Analytics a k řešení případných problémů rychle.
services: log-analytics
documentationcenter: ''
author: jyothirmaisuri
manager: riyazp
editor: ''
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: v-jysur
ms.openlocfilehash: eb9d803bcc9667c26acecbfd098a3022b7421478
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177649"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Připojení Azure k nástrojům ITSM pomocí konektoru pro správu služeb IT

![Symbol IT Service Management Connectoru](media/itsmc-overview/itsmc-symbol.png)

Služba IT Service Management Connector (ITSMC) umožňuje připojit Azure a podporovaný produkt/službu IT Service Management (ITSM).

Služby Azure, jako jsou Log Analytics a Azure Monitor poskytují nástroje pro detekci, analýzu a řešení potíží s prostředky Azure a mimo Azure. Pracovní položky týkající se problému se ale obvykle nacházejí v ITSMm produktu nebo službě. Konektor ITSM poskytuje obousměrné propojení mezi nástroji Azure a ITSM, které vám pomůžou rychleji řešit problémy.

ITSMC podporuje připojení s následujícími ITSM nástroji:

-   ServiceNow
-   System Center Service Manager
-   Prov
-   Cherwell

Pomocí ITSMC můžete:

-  Vytvořte pracovní položky v nástroji ITSM na základě vašich výstrah Azure (výstrahy metrik, výstrahy protokolu aktivit a výstrahy Log Analytics).
-  Volitelně můžete svůj incident a data žádostí o změnu z nástroje ITSM na pracovní prostor služby Azure Log Analytics synchronizovat.

Přečtěte si další informace o [právních podmínek a zásadách ochrany osobních údajů](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Můžete začít používat konektor ITSM pomocí následujících kroků:

1.  [Přidat řešení konektoru ITSM](#adding-the-it-service-management-connector-solution)
2.  Vytvoření připojení ITSM
3.  [Použít připojení](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Přidání řešení IT Service Management Connector

Než budete moct vytvořit připojení, musíte přidat řešení konektoru ITSM.

1. V Azure Portal klikněte na **+ Nová** ikona.

   ![Nový prostředek Azure](media/itsmc-overview/azure-add-new-resource.png)

2. Vyhledejte na webu Marketplace **konektor pro správu služeb IT** a klikněte na **vytvořit**.

   ![Přidat řešení ITSMC](media/itsmc-overview/add-itsmc-solution.png)

3. V části **pracovní prostor OMS** vyberte pracovní prostor Azure Log Analytics, do kterého chcete řešení nainstalovat.
   >[!NOTE]
   > * V rámci průběžného přechodu z Microsoft Operations Management Suite (OMS) na Azure Monitor se teď pracovní prostory OMS označují jako Log Analytics pracovní prostory.
   > * Konektor ITSM se dá nainstalovat jenom v Log Analytics pracovních prostorech v následujících oblastech: Východní USA, Západní Evropa, jihovýchodní Asie, jihovýchodní Austrálie, Středozápadní USA, Východní Japonsko, Jižní Británie, Střed Indie, střední Kanada.

4. V části **Nastavení pracovního prostoru OMS** vyberte zdrojovou položku, kde chcete vytvořit prostředek řešení.

   ![Pracovní prostor ITSMC](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >V rámci průběžného přechodu z Microsoft Operations Management Suite (OMS) na Azure Monitor se teď pracovní prostory OMS označují jako Log Analytics pracovní prostory.

5. Klikněte na **vytvořit**.

Po nasazení prostředku řešení se v pravém horním rohu okna zobrazí oznámení.


## <a name="creating-an-itsm--connection"></a>Vytvoření připojení ITSM

Po instalaci řešení můžete vytvořit připojení.

Pro vytvoření připojení budete muset nástroj ITSM použít k tomu, aby bylo možné připojení z řešení konektoru ITSM.  

V závislosti na ITSM produktu, ke kterému se připojujete, použijte následující postup:

- [System Center Service Manager (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Prov](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Jakmile budete mít připravenou nástroje ITSM, vytvořte připojení pomocí následujících kroků:

1. Přejít na **všechny prostředky**, vyhledejte **partnera (YourWorkspaceName)** .
2. V části **zdroje dat pracovního prostoru** v levém podokně klikněte na **připojení ITSM**.
   připojení @no__t 0ITSM @ no__t-1

   Tato stránka zobrazuje seznam připojení.
3. Klikněte na **Přidat připojení**.

   ![Přidat připojení ITSM](media/itsmc-overview/add-new-itsm-connection.png)

4. Zadejte nastavení připojení, jak je popsáno v [tématu Konfigurace připojení ITSMC k článku ITSM Products/Services](../../azure-monitor/platform/itsmc-connections.md).

   > [!NOTE]
   >
   > Ve výchozím nastavení ITSMC aktualizuje konfigurační data připojení jednou za každých 24 hodin. Pokud chcete data připojení okamžitě aktualizovat pro jakékoli úpravy nebo aktualizace šablon, které uděláte, klikněte na tlačítko **synchronizovat** v okně připojení.

   ![Aktualizace připojení](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Použití řešení
   Pomocí řešení konektoru ITSM můžete vytvářet pracovní položky z výstrah Azure, Log Analytics upozornění a záznamů protokolu Log Analytics.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Vytváření pracovních položek ITSM z výstrah Azure

Po vytvoření připojení ITSM můžete vytvořit pracovní položku v nástroji ITSM na základě výstrah Azure pomocí **Akce ITSM** ve **skupinách akcí**.

Skupiny akcí poskytují modulární a opakovaně použitelný způsob aktivace akcí pro vaše výstrahy Azure. Skupiny akcí s upozorněními na metriky, upozornění protokolu aktivit a výstrahy služby Azure Log Analytics můžete používat v Azure Portal.

Použijte následující postup:

1. V Azure Portal klikněte na **monitorování**.
2. V levém podokně klikněte na **skupiny akcí**. Zobrazí se okno **Přidat skupinu akcí** .

    ![Skupiny akcí](media/itsmc-overview/action-groups.png)

3. Zadejte **název** a **krátký** název skupiny akcí. Vyberte **skupinu prostředků** a **předplatné** , ve kterém chcete vytvořit skupinu akcí.

    ![Podrobnosti o skupinách akcí](media/itsmc-overview/action-groups-details.png)

4. V seznamu akce vyberte možnost **ITSM** z rozevírací nabídky pro **typ akce**. Zadejte **název** akce a klikněte na **Upravit podrobnosti**.
5. Vyberte **předplatné** , ve kterém se nachází váš Log Analytics pracovní prostor. Vyberte název **připojení** (název konektoru ITSM) následovaný názvem vašeho pracovního prostoru. Například "MyITSMMConnector (MyWorkspace)."

    ![Podrobnosti o akci ITSM](media/itsmc-overview/itsm-action-details.png)

6. Z rozevírací nabídky vyberte typ **pracovní položky** .
   Vyberte možnost použít existující šablonu nebo vyplňte pole požadovaná produktem ITSM.
7. Klikněte na tlačítko **OK**.

Při vytváření nebo úpravách pravidla upozornění Azure použijte skupinu akcí, která má akci ITSM. Když se aktivuje výstraha, v nástroji ITSM se vytvoří nebo aktualizuje pracovní položka.

> [!NOTE]
>
> Informace o cenách ITSM akce najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/) pro skupiny akcí.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Vizualizujte a analyzujte data o incidentech a žádostech o změnu.

V závislosti na vaší konfiguraci při nastavování připojení může konektor ITSM synchronizovat až 120 dní a data žádosti o změnu. Schéma záznamu protokolu pro tato data je k dispozici v [následující části](#additional-information).

Data incidentu a žádosti o změnu lze vizuálně vylepšovat pomocí řídicího panelu konektoru ITSM v řešení.

![Log Analytics obrazovce](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Řídicí panel také poskytuje informace o stavu konektoru, který lze použít jako výchozí bod k analýze případných problémů s připojením.

Můžete také vizualizovat incidenty synchronizované s ovlivněnými počítači v rámci řešení Service Map.

Service Map automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikaci mezi službami. Umožňuje vám zobrazit vaše servery podle toho, jak si je považujete – jako propojené systémy, které poskytují důležité služby. Service Map zobrazuje připojení mezi servery, procesy a porty v rámci libovolné architektury připojené k protokolu TCP bez nutnosti jiné konfigurace než instalace agenta. [Další informace](../../azure-monitor/insights/service-map.md)

Pokud používáte řešení Service Map, můžete zobrazit položky oddělení služeb vytvořené v řešeních ITSM, jak je znázorněno v následujícím příkladu:

![Log Analytics obrazovce](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Další informace: [Service map](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>Další informace

### <a name="data-synced-from-itsm-product"></a>Data synchronizovaná z produktu ITSM
Incidenty a žádosti o změnu se synchronizují z vašeho ITSM produktu do vašeho pracovního prostoru Log Analytics na základě konfigurace připojení.

Následující informace ukazují příklady dat shromažďovaných nástrojem ITSMC:

> [!NOTE]
>
> V závislosti na typu pracovní položky importované do Log Analytics obsahuje **ServiceDesk_CL** následující pole:

**Pracovní položka:** **incidenty**  
ServiceDeskWorkItemType_s = incident

**Pole**

- ServiceDeskConnectionName
- ID oddělení služeb
- Stav
- Kterou
- Dopad
- Priorita
- Eskalace
- Vytvořil
- Vyřešil
- Uzavřel
- Zdroj
- Přiřazeno
- Kategorie
- Název
- Popis
- Datum vytvoření
- Datum uzavření
- Datum vyřešení
- Datum poslední změny
- Počítač


**Pracovní položka:** **žádosti o změnu**

ServiceDeskWorkItemType_s = "žádost o změnu"

**Pole**
- ServiceDeskConnectionName
- ID oddělení služeb
- Vytvořil
- Uzavřel
- Zdroj
- Přiřazeno
- Název
- Typ
- Kategorie
- Stav
- Eskalace
- Stav konfliktu
- Kterou
- Priorita
- Riziko
- Dopad
- Přiřazeno
- Datum vytvoření
- Datum uzavření
- Datum poslední změny
- Požadované datum
- Plánované počáteční datum
- Plánované koncové datum
- Datum zahájení práce
- Datum ukončení práce
- Popis
- Počítač

## <a name="output-data-for-a-servicenow-incident"></a>Výstupní data pro incident ServiceNow

| Log Analytics pole | Pole ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Číslo |
| IncidentState_s | Stav |
| Urgency_s |Kterou |
| Impact_s |Dopad|
| Priority_s | Priorita |
| CreatedBy_s | Otevřel |
| ResolvedBy_s | Vyřešil|
| ClosedBy_s  | Uzavřel |
| Source_s| Typ kontaktu |
| AssignedTo_s | Přiřazeno  |
| Category_s | Kategorie |
| Title_s|  Krátký popis |
| Description_s|  Poznámky |
| CreatedDate_t|  Otevřít |
| ClosedDate_t| ukončit|
| ResolvedDate_t|Přeložit|
| Počítač  | Položka konfigurace |

## <a name="output-data-for-a-servicenow-change-request"></a>Výstupní data pro žádost o změnu ServiceNow

| Log Analytics | Pole ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Číslo |
| CreatedBy_s | Požadoval (a) |
| ClosedBy_s | Uzavřel |
| AssignedTo_s | Přiřazeno  |
| Title_s|  Krátký popis |
| Type_s|  Typ |
| Category_s|  Kategorie |
| CRState_s|  Stav|
| Urgency_s|  Kterou |
| Priority_s| Priorita|
| Risk_s| Riziko|
| Impact_s| Dopad|
| RequestedDate_t  | Požadováno podle data |
| ClosedDate_t | Datum uzavření |
| PlannedStartDate_t  |     Plánované počáteční datum |
| PlannedEndDate_t  |   Plánované koncové datum |
| WorkStartDate_t  | Skutečné datum zahájení |
| WorkEndDate_t | Skutečné datum ukončení|
| Description_s | Popis |
| Počítač  | Položka konfigurace |


## <a name="troubleshoot-itsm-connections"></a>Řešení potíží s připojením ITSM
1. Pokud se připojení nezdařilo z uživatelského rozhraní připojeného zdroje s **chybou při ukládání** zprávy o připojení, proveďte následující kroky:
   - Pro připojení ServiceNow, Cherwell a prov,  
   - Ujistěte se, že jste správně zadali uživatelské jméno, heslo, ID klienta a tajný klíč klienta pro každé připojení.  
   - Zkontrolujte, jestli máte v odpovídajícím ITSM produktu dostatečná oprávnění pro vytvoření připojení.  
   - Pro Service Manager připojení,  
   - Zajistěte, aby byla webová aplikace úspěšně nasazena a vytvořilo se hybridní připojení. Pokud chcete ověřit, že se připojení úspěšně navázalo na místním Service Manager počítači, přejděte na adresu URL webové aplikace, jak je popsáno v dokumentaci pro vytvoření [hybridního připojení](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection).  

2. Pokud se data z ServiceNow nesynchronizují Log Analytics, ujistěte se, že instance ServiceNow není v režimu spánku. Instance pro vývoj v ServiceNow se někdy po dlouhou dobu nečinným přechodem do režimu spánku. Jinak nahlaste problém.
3. Pokud se Log Analytics výstrahy aktivují, ale pracovní položky se nevytvoří v produktu ITSM nebo položky konfigurace nejsou vytvořené/propojené s pracovními položkami nebo pro jiné obecné informace, podívejte se do následujících umístění:
   -  ITSMC: řešení zobrazuje souhrn připojení/pracovních položek/počítačů atd. Klikněte na dlaždici se **stavem konektoru**, který vám umožní **Protokolovat hledání** pomocí příslušného dotazu. Další informace najdete v záznamech protokolu s LogType_S jako chyba.
   - Stránka **prohledávání protokolu** : zobrazení chyb/souvisejících informací přímo pomocí dotazu `*`ServiceDeskLog_CL @ no__t-2.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Řešení potíží s nasazením Service Manager Web App
1.  V případě jakýchkoli problémů s nasazením webové aplikace se ujistěte, že máte dostatečná oprávnění v předplatném, zmíněné pro vytváření a nasazování prostředků.
2.  Pokud se při spuštění [skriptu](itsmc-service-manager-script.md)zobrazí chyba **"odkaz na objekt není nastaven na instanci objektu"** , ujistěte se, že jste zadali platné hodnoty v části **Konfigurace uživatele** .
3.  Pokud se vám nepodaří vytvořit obor názvů služby Service Bus Relay, zajistěte, aby byl v předplatném zaregistrován požadovaný poskytovatel prostředků. Pokud není zaregistrován, ručně vytvořte obor názvů služby Service Bus Relay z Azure Portal. Můžete ho také vytvořit při [vytváření hybridního připojení](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection) z Azure Portal.


## <a name="contact-us"></a>Kontaktujte nás

Pro všechny dotazy nebo názory na IT konektor pro IT služby kontaktujte nás na adrese [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Další kroky
[Přidejte ITSM produkty/služby do konektoru správy služeb IT](../../azure-monitor/platform/itsmc-connections.md).
