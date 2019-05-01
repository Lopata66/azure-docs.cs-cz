---
title: Omezení a konfigurace – Azure Logic Apps | Dokumentace Microsoftu
description: Omezení služby a konfigurační hodnoty pro Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 11/16/2018
ms.openlocfilehash: f371376a7c801eecb6231d551546b13dbc68dd26
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916818"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Omezení a konfigurační informace pro Azure Logic Apps

Tento článek popisuje, omezeních a konfiguracích pro vytváření a spouštění automatizovaných pracovních postupů s Azure Logic Apps. Microsoft Flow, najdete v části [omezení a konfigurace v Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Omezení definic

Tady jsou limity pro definici aplikace logiky jeden:

| Název | Omezení | Poznámky |
| ---- | ----- | ----- |
| Akce na pracovní postup | 500 | Abychom rozšířili tento limit, můžete přidat vnořené pracovní postupy podle potřeby. |
| Povolená hloubka vnoření akcí | 8 | Abychom rozšířili tento limit, můžete přidat vnořené pracovní postupy podle potřeby. |
| Pracovní postupy na oblast a předplatné | 1 000 | |
| Aktivační události na pracovní postup | 10 | Při práci v zobrazení kódu, nikoli návrháře |
| Přepínač oboru případů limit | 25 | |
| Proměnné na pracovní postup | 250 | |
| Znaků na výraz | 8 192 | |
| Maximální velikost `trackedProperties` | 16 000 znaků |
| Název pro `action` nebo `trigger` | 80 znaků. | |
| Délka `description` | 256 znaků. | |
| Maximální počet `parameters` | 50 | |
| Maximální počet `outputs` | 10 | |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Limity uchování a doba trvání spuštění

Tady jsou limity pro běh aplikace logiky jeden:

| Název | Omezení | Poznámky |
|------|-------|-------|
| Doba trvání běhu | 90 dnů | Chcete-li tento limit změnit, přečtěte si téma [doba trvání běhu změnit](#change-duration). |
| Interval minimální opakování | 1 sekunda | |
| Maximální počet opakování interval | 500 dnů | |
| Uchování úložiště | čas zahájení 90 dnů od spuštění | Chcete-li tento limit můžete změnit na hodnotu 7 dní až 90 dnů, přečtěte si téma [změnit uchování úložiště](#change-retention). |
||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Změna spuštění uchovávání doba trvání a ukládání

Chcete-li změnit výchozí limit 7 dní až 90 dnů, postupujte podle těchto kroků. Pokud budete muset přejít nad maximální limit [obraťte se na tým Logic Apps](mailto://logicappsemail@microsoft.com) nápovědu k vašim požadavkům.

1. Na webu Azure Portal, v nabídce aplikace logiky zvolte **nastavení pracovního postupu**.

2. V části **možnosti modulu Runtime**, z **uchování historie spuštění ve dnech** klikněte na položku **vlastní**.

3. Zadejte nebo přetažením posuvníku pro počet dní, po které chcete.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Souběžnost, cykly a rušení dávek omezení

Tady jsou limity pro běh aplikace logiky jeden:

| Název | Omezení | Poznámky |
| ---- | ----- | ----- |
| Souběžnosti triggeru | * Neomezené při vypnuté řízení souběžnosti <p><p>* 25 je výchozí omezení, pokud řízení souběžnosti je zapnuté, což nelze vrátit zpět po zapnutí ovládacího prvku. Výchozí nastavení můžete změnit na hodnotu mezi 1 až 50 znaků (včetně). | Tato omezení popisuje nejvyšší počet instancí aplikace logiky, spuštěné ve stejnou dobu nebo paralelně. <p><p>Chcete-li změnit výchozí omezení na hodnotu mezi 1 až 50 znaků (včetně), [omezení souběžnosti aktivační události změnit](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) nebo [aktivovat instance postupně](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Maximální čekací spuštění | Pokud je zapnuté řízení souběžnosti, je minimální počet čekajících běhů 10 plus počet souběžných spuštění (souběžnosti aktivační události). Můžete změnit maximální počet až 100 (včetně). | Tato omezení popisuje nejvyšší počet instancí aplikace logiky, které můžete počkat a spouštět, když vaše aplikace logiky je již spuštěn maximální počet souběžných instancí. <p><p>Chcete-li změnit výchozí omezení, [omezit spuštění čekajících změn](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Pole položek foreach | 100 000 | Tato omezení popisuje nejvyšší počet položek pole, které může zpracovat smyčka "for each". <p><p>K filtrování větších polí, můžete použít [akce dotazu](../connectors/connectors-native-query.md). |
| Foreach souběžnosti | 20 je výchozí omezení, když je vypnutý řízení souběžnosti. Výchozí nastavení můžete změnit na hodnotu mezi 1 až 50 znaků (včetně). | Toto omezení je nejvyšší počet "pro každý" iterací, které lze spustit ve stejnou dobu nebo paralelní smyčky. <p><p>Chcete-li změnit výchozí omezení na hodnotu mezi 1 až 50 znaků (včetně), [změnit "for each" souběžnosti limit](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) nebo [spuštění "for each" smyčky postupně](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| Položky SplitOn | 100 000 | Pro aktivační události, které vrací pole můžete zadat výraz, který se používá 'Vlastnost SplitOn' vlastnost, která [rozdělí nebo debatches položek pole do několika instancí pracovních postupů](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) pro zpracování, místo použití "Foreach" smyčky. Tento výraz odkazuje na pole, které chcete použít pro vytvoření a spuštění instance pracovního postupu pro každou položku pole. |
| Do iterací | 5 000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Omezení propustnosti

Tady jsou limity pro běh aplikace logiky jeden:

### <a name="global-logic-apps-service"></a>Globální služba Logic Apps

| Název | Omezení | Poznámky |
| ---- | ----- | ----- |
| Akce: Spuštění za 5 minut | Výchozí limit je 100 000, ale maximální limit je 300 000. | Chcete-li změnit výchozí omezení, [spuštění aplikace logiky v režimu "Vysoká propustnost"](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), což je ve verzi preview. Nebo můžete distribuovat zatížení napříč více než jedné aplikace logiky podle potřeby. |
| Akce: Odchozí souběžných volání | ~2,500 | Můžete snížit počet souběžných požadavků nebo zkrátit dobu trvání podle potřeby. |
| Koncový bod modulu runtime: Příchozí souběžných volání | ~1,000 | Můžete snížit počet souběžných požadavků nebo zkrátit dobu trvání podle potřeby. |
| Koncový bod modulu runtime: Čtení volání za 5 minut  | 60,000 | Rozdělit zatížení mezi více než jednu aplikaci podle potřeby. |
| Koncový bod modulu runtime: Vyvolání volání za 5 minut | 45,000 | Rozdělit zatížení mezi více než jednu aplikaci podle potřeby. |
| Obsahu propustnost za 5 minut | 600 MB | Rozdělit zatížení mezi více než jednu aplikaci podle potřeby. |
||||

### <a name="integration-service-environment-ise"></a>Prostředí integrační služby (ISE)

| Název | Omezení | Poznámky |
|------|-------|-------|
| Základní jednotka limit provádění. | 10 000 spuštění akcí za 5 minut <br>což je přibližně 80 milionů spuštění akcí za měsíc | |
| Limit škálování jednotek spuštění | 5 000 akcí spuštění za 5 minut <br>což je přibližně 40 milionů spuštění akcí za měsíc | |
| Maximální škálování jednotek, které můžete přidat | 3 | |
||||

Ambice tato omezení v normálním zpracování nebo spustit zátěžové testování, který může přejít nad tyto limity [obraťte se na tým Logic Apps](mailto://logicappsemail@microsoft.com) nápovědu k vašim požadavkům.

<a name="request-limits"></a>

## <a name="http-limits"></a>Omezení protokolu HTTP

Tady jsou limity pro jednoho požadavku HTTP nebo volání synchronní konektoru:

#### <a name="timeout"></a>Vypršení časového limitu

Některé konektor operace asynchronního volání nebo naslouchání požadavkům webhooku, abyste časový limit pro tyto operace může být delší než tato omezení. Další informace najdete v tématu technické podrobnosti pro konkrétní konektor a také [triggerů pracovního postupu a akce](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Název | Omezení | Poznámky |
| ---- | ----- | ----- |
| Odchozí žádosti | 120 sekund | Pro delší probíhajících operací, použijte [asynchronního dotazování vzor](../logic-apps/logic-apps-create-api-app.md#async-pattern) nebo [smyčka until](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). |
| Synchronní odpověď | 120 sekund | Pro původní požadavek na získání odpovědi musí dokončit všechny kroky v odpovědi v rámci limitu není-li volat jiné aplikace logiky jako vnořený pracovní postup. Další informace najdete v tématu [volání, aktivační události, nebo vnořené aplikace logiky](../logic-apps/logic-apps-http-endpoint.md). |
|||| 

#### <a name="message-size"></a>Velikost zpráv

| Název | Omezení | Poznámky |
| ---- | ----- | ----- |
| Velikost zpráv | 100 MB | Chcete-li toto omezení obejít, přečtěte si téma [zpracování velkých zpráv s dělením dat do bloků](../logic-apps/logic-apps-handle-large-messages.md). Ale některé konektory a rozhraní API nemusí podporovat bloků nebo dokonce výchozí omezení. |
| Velikost zprávy s dělením dat do bloků | 1 GB | Toto omezení se vztahuje na akce, které nativně podporují dat nebo umožňují povolit bloků v jejich konfigurace modulu runtime. Další informace najdete v tématu [zpracování velkých zpráv s dělením dat do bloků](../logic-apps/logic-apps-handle-large-messages.md). |
| Omezení pro vyhodnocení výrazu | 131 072 znaků | `@concat()`, `@base64()`, `@string()` Výrazů nemůže být delší než toto omezení. |
||||

#### <a name="retry-policy"></a>Zásady opakování

| Název | Omezení | Poznámky |
| ---- | ----- | ----- |
| Opakované pokusy | 90 | Výchozí hodnota je 4. Chcete-li změnit výchozí nastavení, použijte [opakujte parametr zásad](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Maximální zpoždění při opakování | 1 den | Chcete-li změnit výchozí nastavení, použijte [opakujte parametr zásad](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Min zpoždění při opakování | 5 sekund | Chcete-li změnit výchozí nastavení, použijte [opakujte parametr zásad](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Omezení vlastního konektoru

Tady jsou limity pro vlastní konektory, které můžete vytvořit z webových rozhraní API.

| Název | Omezení |
| ---- | ----- |
| Počet vlastních konektorů | 1 000 na předplatné Azure |
| Počet požadavků za minutu pro každé připojení vytvořené vlastním konektorem | 500 požadavků za připojení |
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Spravované identity

| Název | Omezení |
| ---- | ----- |
| Počet aplikací logiky s využitím systém přiřadil spravovat identity jedno předplatné Azure | 10 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limity účtu integrace

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Omezení pro artefaktu účtu pro integraci

Tady je omezený počet artefaktů pro každý účet integrace. Další informace najdete v tématu [ceny Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

> [!NOTE] 
> Úroveň Free můžete používejte jenom pro scénáře průzkumného testování, nikoli produkčních scénářů. Tato vrstva omezuje propustnost a využití a nemá žádné smlouvu o úrovni služeb (SLA).

| Artefakt | Free | Basic | Standard |
|----------|------|-------|----------|
| Obchodní smluv EDI | 10 | 1 | 500 |
| Obchodní partneři EDI | 25 | 2 | 500 |
| Maps | 25 | 500 | 1 000 |
| Schémata | 25 | 500 | 1 000 |
| Sestavení | 10 | 25 | 50 |
| Certifikáty | 25 | 2 | 500 |
| Konfigurace dávek | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limity kapacity artefaktu

| Artefakt | Omezení | Poznámky |
| -------- | ----- | ----- |
| Sestavení | 8 MB | Pokud chcete nahrát soubory větší než 2 MB, použijte [účet a objektů blob v kontejneru úložiště Azure](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Mapu (soubor XSLT) | 8 MB | Pokud chcete nahrát soubory větší než 2 MB, použijte [Azure Logic Apps REST API – mapuje](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). |
| Schéma | 8 MB | Pokud chcete nahrát soubory větší než 2 MB, použijte [účet a objektů blob v kontejneru úložiště Azure](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

| Koncový bod modulu runtime | Omezení | Poznámky |
|------------------|-------|-------|
| Čtení volání za 5 minut | 60,000 | Distribuuje úlohy mezi víc než jeden účet podle potřeby. |
| Vyvolání volání za 5 minut | 45,000 | Distribuuje úlohy mezi víc než jeden účet podle potřeby. |
| Sledovací volání za 5 minut | 45,000 | Distribuuje úlohy mezi víc než jeden účet podle potřeby. |
| Blokování souběžných volání | ~1,000 | Můžete snížit počet souběžných požadavků nebo zkrátit dobu trvání podle potřeby. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Velikost zprávy B2B protocol (AS2, X12 EDIFACT)

Tady jsou omezení, které platí pro B2B protokoly:

| Název | Omezení | Poznámky |
| ---- | ----- | ----- |
| AS2 | 50 MB | Použije kódování a dekódování |
| X12 | 50 MB | Použije kódování a dekódování |
| EDIFACT | 50 MB | Použije kódování a dekódování |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Zakázání nebo odstranění aplikace logiky

Pokud zakážete, aplikace logiky, žádná nová spuštění jsou vytvořena instance. Všechny probíhající a čeká se na spuštění pokračovat, dokud jejich dokončení, což může chvíli trvat.

Pokud odstraníte aplikaci logiky, nebudou se vytvářet žádné nové instance spuštění. Všechna probíhající a čekající spuštění se zruší. Pokud máte tisíce spuštění, jejich zrušení může trvat značnou dobu.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses"></a>Konfigurace brány firewall: IP adresy

Všechny aplikace logiky ve stejné oblasti používat stejné rozsahy IP adres. Pro podporu volání, kterým je aplikace logiky přímo s [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)a další požadavky HTTP, nastavení brány firewall s *všechny* [ příchozí](#inbound) *a* [odchozí](#outbound) IP adresy používané službou aplikace logiky podle oblastí, kde existují logic apps. Tyto adresy se zobrazí v rámci **příchozí** a **odchozí** záhlaví v této části a jsou seřazeny podle oblasti.

Pro podporu volání, která [konektorů spravovaných microsoftem](../connectors/apis-list.md) zkontrolujte nastavení brány firewall s *všechny* [odchozí](#outbound) IP adresy používané službou tyto konektory podle oblastí Pokud existují logic apps. Tyto adresy se zobrazí v rámci **odchozí** záhlaví v této části a jsou seřazeny podle oblasti.

Pro [Azure Government](../azure-government/documentation-government-overview.md) a [Azure China 21Vianet](/azure/china/china-welcome), rezervované IP adresy pro konektory nejsou nyní k dispozici.

> [!IMPORTANT]
>
> Pokud máte existující konfigurace, aktualizujte je **co nejdříve před 1. září 2018** tak zahrnout a shodují s IP adresou v těchto seznamů pro oblasti, kde existují logic apps.

Logic Apps nepodporuje přímého připojení k účtům úložiště Azure přes brány firewall. Pro přístup k tyto účty úložiště, použijte jednu z možností tady:

* Vytvoření [prostředí integrační služby](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), který umožňuje připojení k prostředkům ve virtuální síti Azure.

* Pokud už používáte API Management, můžete tuto službu pro tento scénář. Další informace najdete v tématu [jednoduché podnikové integrace architektury](https://aka.ms/aisarch).

<a name="inbound"></a>

### <a name="inbound-ip-addresses---logic-apps-service-only"></a>Příchozí IP adresy – pouze služba Logic Apps

| Oblast | IP adresa |
|--------|----|
| Austrálie – východ | 13.75.153.66, 52.187.231.161, 104.210.89.222, 104.210.89.244 |
| Austrálie – jihovýchod | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brazílie – jih | 191.234.166.198, 191.235.86.199, 191.235.94.220, 191.235.95.229 |
| Kanada – střed | 13.88.249.209, 40.85.241.105, 52.233.29.79, 52.233.30.218 |
| Kanada – východ | 40.86.202.42, 52.229.125.57, 52.232.129.143, 52.232.133.109 |
| Střed Indie | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| USA – střed | 13.67.236.76, 40.77.31.87, 40.77.111.254, 104.43.243.39 |
| Východní Asie | 13.75.89.159, 23.97.68.172, 40.83.98.194, 168.63.200.173 |
| USA – východ | 40.117.99.79, 40.117.100.228, 137.116.126.165, 137.135.106.54 |
| Východní USA 2 | 40.70.27.253, 40.79.44.7, 40.84.25.234, 40.84.59.136 |
| Japonsko – východ | 13.71.146.140, 13.78.43.164, 13.78.62.130, 13.78.84.187 |
| Japonsko – západ | 40.74.68.85, 40.74.81.13, 40.74.85.215, 40.74.140.173 |
| Středoseverní USA | 65.52.9.64, 65.52.211.164, 168.62.249.81, 157.56.12.202 |
| Severní Evropa | 13.79.173.49, 40.112.90.39, 52.169.218.253, 52.169.220.174 |
| Středojižní USA | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Indie – jih | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Jihovýchodní Asie | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Západní střed USA | 13.78.137.247, 52.161.8.128, 52.161.19.82, 52.161.26.172 |
| Západní Evropa | 13.95.155.53, 51.144.176.185, 52.174.49.6, 52.174.54.218 |
| Indie – západ | 104.211.157.237, 104.211.164.25, 104.211.164.112, 104.211.165.81 |
| Západní USA | 13.91.252.184, 52.160.90.237, 138.91.188.137, 157.56.160.212 |
| Západní USA 2 | 13.66.128.68, 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Velká Británie – jih | 51.140.78.71, 51.140.79.109, 51.140.84.39, 51.140.155.81 |
| Spojené království – západ | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| | |

<a name="outbound"></a>

### <a name="outbound-ip-addresses---logic-apps-service--managed-connectors"></a>Odchozí IP adresy – služba Logic Apps & spravované konektory

| Oblast | Logic Apps IP | Spravované konektory IP |
|--------|---------------|-----------------------|
| Austrálie – východ | 13.75.149.4, 52.187.226.96, 52.187.226.139, 52.187.227.245, 52.187.229.130, 52.187.231.184, 104.210.90.241, 104.210.91.55 | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213 |
| Austrálie – jihovýchod | 13.70.159.205, 13.73.114.207, 13.77.3.139, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75, 52.189.222.77 | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34 |
| Brazílie – jih | 191.234.161.28, 191.234.161.168, 191.234.162.131, 191.234.162.178, 191.234.182.26, 191.235.82.221, 191.235.91.7, 191.237.255.116 | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207 | 
| Kanada – střed | 13.71.184.150, 13.71.186.1, 40.85.250.135, 40.85.250.212, 40.85.252.47, 52.233.29.92, 52.228.39.241, 52.228.39.244 | 13.71.170.208 - 13.71.170.223, 13.71.170.224 - 13.71.170.239, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.26.83, 52.233.31.197, 52.237.24.126 |
| Kanada – východ | 40.86.203.228, 40.86.216.241, 40.86.217.241, 40.86.226.149, 40.86.228.93, 52.229.120.45, 52.229.126.25, 52.232.128.155 | 40.69.106.240 - 40.69.106.255, 52.229.120.52, 52.229.120.131, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152 |
| Střed Indie | 52.172.154.168, 52.172.185.79, 52.172.186.159, 104.211.74.145, 104.211.90.162, 104.211.90.169, 104.211.101.108, 104.211.102.62 | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164 |
| USA – střed | 13.67.236.125, 23.100.82.16, 23.100.86.139, 23.100.87.24, 23.100.87.56, 40.113.218.230, 40.122.170.198, 104.208.25.27 | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164 |
| Východní Asie | 13.75.94.173, 40.83.73.39, 40.83.75.165, 40.83.77.208, 40.83.100.69, 40.83.127.19, 52.175.33.254, 65.52.175.34 | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169 |
| USA – východ | 13.92.98.111, 23.100.29.190, 23.101.132.208, 23.101.136.201, 23.101.139.153, 40.114.82.191, 40.121.91.41, 104.45.153.81 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 |
| Východní USA 2 | 40.70.26.154, 40.70.27.236, 40.70.29.214, 40.70.131.151, 40.84.30.147, 104.208.140.40, 104.208.155.200, 104.208.158.174 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100 |
| Japonsko – východ | 13.71.158.3, 13.71.158.120, 13.73.4.207, 13.78.18.168, 13.78.20.232, 13.78.21.155, 13.78.35.229, 13.78.42.223 | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96 |
| Japonsko – západ | 40.74.64.207, 40.74.68.85, 40.74.74.21, 40.74.76.213, 40.74.77.205, 40.74.140.4, 104.214.137.243, 138.91.26.45 | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248 |
| Středoseverní USA | 52.162.208.216, 52.162.213.231, 65.52.8.225, 65.52.9.96, 65.52.10.183, 157.55.210.61, 157.55.212.238, 168.62.248.37 | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230 |
| Severní Evropa | 40.112.92.104, 40.112.95.216, 40.113.1.181, 40.113.3.202, 40.113.4.18, 40.113.12.95, 52.178.165.215, 52.178.166.21 | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9 |
| Středojižní USA | 13.65.82.17, 13.66.52.232, 23.100.124.84, 23.100.127.172, 23.101.183.225, 70.37.54.122, 70.37.50.6, 104.210.144.48 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191 |
| Indie – jih | 52.172.50.24, 52.172.52.0, 52.172.55.231, 104.211.227.229, 104.211.229.115, 104.211.230.126, 104.211.230.129, 104.211.231.39 | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225 |
| Jihovýchodní Asie | 13.67.91.135, 13.67.107.128, 13.67.110.109, 13.76.4.194, 13.76.5.96, 13.76.133.155, 52.163.228.93, 52.163.230.166 | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19 |
| Západní střed USA | 13.78.129.20, 13.78.137.179, 13.78.141.75, 13.78.148.140, 13.78.151.161, 52.161.18.218, 52.161.9.108, 52.161.27.190 | 13.71.195.32 - 13.71.195.47, 52.161.24.128, 52.161.26.212, 52.161.27.108, 52.161.29.35, 52.161.30.5, 52.161.102.22 |
| Západní Evropa | 13.95.147.65, 23.97.210.126, 23.97.211.179, 23.97.218.130, 40.68.209.23, 40.68.222.65, 51.144.182.201, 104.45.9.52 | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118 |
| Indie – západ | 104.211.154.7, 104.211.154.59, 104.211.156.153, 104.211.158.123, 104.211.158.127, 104.211.162.205, 104.211.164.80, 104.211.164.136 | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218 |
| Západní USA | 40.83.164.80, 40.118.244.241, 40.118.241.243, 52.160.92.112, 104.42.38.32, 104.42.49.145, 157.56.162.53, 157.56.167.147 |40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49 |
| Západní USA 2 | 13.66.201.169, 13.66.210.167, 13.66.246.219, 13.77.149.159, 52.175.198.132, 52.183.29.132, 52.183.30.169 | 13.66.140.128 - 13.66.140.143, 13.66.218.78, 13.66.219.14, 13.66.220.135, 13.66.221.19, 13.66.225.219, 52.183.78.157 |
| Velká Británie – jih | 51.140.28.225, 51.140.73.85, 51.140.74.14, 51.140.78.44, 51.140.137.190, 51.140.142.28, 51.140.153.135, 51.140.158.24 | 51.140.80.51, 51.140.148.0 - 51.140.148.15 |
| Spojené království – západ | 51.141.45.238, 51.141.47.136, 51.141.54.185, 51.141.112.112, 51.141.113.36, 51.141.114.77, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105 |
||||

## <a name="next-steps"></a>Další postup  

* Zjistěte, jak [vytvořit svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Další informace o [běžných příkladů a scénářů](../logic-apps/logic-apps-examples-and-scenarios.md)
