---
title: Rychlý Start – vytvoření úlohy Azure Stream Analytics v Visual Studio Code
description: V tomto rychlém startu se dozvíte, jak začít vytvořením Stream Analytics úlohy, konfigurací vstupů a výstupů a definováním dotazu Visual Studio Code.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/16/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 236d29d2c50a792bc4dc11916964db47ca092807
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659589"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-in-visual-studio-code-preview"></a>Rychlý Start: vytvoření úlohy Azure Stream Analytics v Visual Studio Code (Preview)

V tomto rychlém startu se dozvíte, jak vytvořit a spustit úlohu Azure Stream Analytics pomocí rozšíření Azure Stream Analytics Tools pro Visual Studio Code. Ukázková úloha načte streamovaná data ze zařízení Azure IoT Hub. Nadefinujete úlohu, která vypočítá průměrnou teplotu při více než 27 ° a zapíše výsledné výstupní události do nového souboru v úložišti objektů BLOB.

## <a name="before-you-begin"></a>Než začnete

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Přihlaste se na web [Azure Portal](https://portal.azure.com/).

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-tools-extension"></a>Instalace rozšíření Azure Stream Analytics Tools

1. Otevřete Visual Studio Code.

2. V nabídce **rozšíření** v levém podokně vyhledejte **Stream Analytics** a v rozšíření **Azure Stream Analytics nástroje** vyberte **instalovat** .

3. Po instalaci rozšíření ověřte, že **Azure Stream Analytics nástroje** jsou v **povolených rozšířeních**viditelné.

   ![Azure Stream Analytics nástroje v části povolená rozšíření v Visual Studio Code](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-tools-extension"></a>Aktivace rozšíření Azure Stream Analytics nástrojů

1. Na řádku Visual Studio Code aktivity vyberte ikonu **Azure** . V části **Stream Analytics** na bočním panelu vyberte **Přihlásit se k Azure**.

   ![Přihlaste se k Azure v Visual Studio Code](./media/quick-create-vs-code/azure-sign-in.png)

2. Když jste přihlášení, váš název účtu Azure se zobrazí na stavovém řádku v levém dolním rohu okna Visual Studio Code.

> [!NOTE]
> Rozšíření Azure Stream Analytics nástroje vás při příštím přihlášení automaticky odhlásí. Pokud má váš účet dvojúrovňové ověřování, doporučujeme místo kódu PIN používat ověřování pomocí telefonu.
> Pokud máte problémy se seznamem prostředků, odhlášení a opětovné přihlášení obvykle pomáhá. Pokud se chcete odhlásit, zadejte příkaz `Azure: Sign Out`.

## <a name="prepare-the-input-data"></a>Příprava vstupních dat

Před definováním Stream Analytics úlohy byste měli připravit data, která jsou později nakonfigurovaná jako vstup úlohy. Pokud chcete připravit vstupní data, která úloha potřebuje, proveďte následující kroky:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

2. Vyberte **Vytvořit prostředek** > **Internet věcí** > **IoT Hub**.

3. V podokně **IoT Hub** zadejte následující informace:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Předplatné  | \<Vaše předplatné\> |  Vyberte předplatné Azure, které chcete použít. |
   |Skupina prostředků   |   asaquickstart-resourcegroup  |   Vyberte **Vytvořit nový** a zadejte název nové skupiny prostředků pro váš účet. |
   |Region (Oblast)  |  \<Vyberte oblast nejbližší vašim uživatelům.\> | Vyberte zeměpisnou polohu, kde můžete hostovat centrum IoT. Použijte umístění, které je nejblíže vašim uživatelům. |
   |Název služby IoT Hub  | MyASAIoTHub  |   Vyberte název centra IoT.   |

   ![Vytvoření IoT Hubu](./media/quick-create-vs-code/create-iot-hub.png)

4. Vyberte **Další: nastavit velikost a měřítko**.

5. Vytvořte výběr pro **cenovou a škálnou úroveň**. Pro tento rychlý Start vyberte úroveň **F1** , pokud je stále k dispozici ve vašem předplatném. Pokud úroveň Free není k dispozici, vyberte nejnižší dostupnou úroveň. Další informace najdete v tématu [ceny služby Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Velikost a škálování centra IoT](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Vyberte **Zkontrolovat a vytvořit**. Zkontrolujte informace o službě IoT Hub a vyberte **vytvořit**. Vytvoření centra IoT může trvat několik minut. Průběh můžete sledovat v podokně **oznámení** .

7. V navigační nabídce centra IoT vyberte **Přidat** v části **zařízení IoT**. Přidejte ID pro **ID zařízení**a vyberte **Uložit**.

   ![Přidání zařízení do služby IoT Hub](./media/quick-create-vs-code/add-device-iot-hub.png)

8. Po vytvoření zařízení otevřete zařízení ze seznamu **zařízení IoT** . Zkopírujte řetězec v **připojovacím řetězci (primární klíč)** a uložte ho do poznámkového bloku pro pozdější použití.

   ![Kopírovat připojovací řetězec zařízení IoT Hub](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="run-the-iot-simulator"></a>Spuštění simulátoru IoT

1. Otevřete [online simulátor Azure IoT PI](https://azure-samples.github.io/raspberry-pi-web-simulator/) na nové kartě nebo v okně prohlížeče.

2. Zástupný text na řádku 15 nahraďte připojovacím řetězcem zařízení IoT Hub, který jste předtím uložili.

3. Vyberte **Run** (Spustit). Výstup by měl zobrazovat data senzorů a zprávy, které se odesílají do služby IoT Hub.

   ![Simulátor Azure IoT online v aplikaci malin PI s výstupem](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="create-blob-storage"></a>Vytvoření úložiště objektů BLOB

1. V levém horním rohu Azure Portal vyberte **vytvořit prostředek** > **úložiště** > **účet úložiště**.

2. V podokně **vytvořit účet úložiště** zadejte název účtu úložiště, umístění a skupinu prostředků. Vyberte stejné umístění a skupinu prostředků jako centrum IoT, které jste vytvořili. Pak vyberte **zkontrolovat + vytvořit** a vytvořte účet.

   ![Vytvoření účtu úložiště](./media/quick-create-vs-code/create-storage-account.png)

3. Po vytvoření účtu úložiště vyberte dlaždici **objektů BLOB** v podokně **Přehled** .

   ![Přehled účtu úložiště](./media/quick-create-vs-code/blob-storage.png)

4. Na stránce **BLOB Service** vyberte **kontejner** a zadejte název svého kontejneru, například **container1**. Ponechte **úroveň veřejného přístupu** jako **soukromou (bez anonymního přístupu)** a vyberte **OK**.

   ![Vytvoření kontejneru objektů blob](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Vytvoření projektu Stream Analytics

1. V Visual Studio Code vyberte **CTRL + SHIFT + P** a otevřete paletu příkazů. Pak zadejte **ASA** a vyberte **ASA: vytvořit nový projekt**.

   ![Vytvoření nového projektu](./media/quick-create-vs-code/create-new-project.png)

2. Zadejte název projektu, například **myASAproj**, a vyberte složku pro svůj projekt.

    ![Vytvořit název projektu](./media/quick-create-vs-code/create-project-name.png)

3. Nový projekt se přidá do vašeho pracovního prostoru. Stream Analytics projekt se skládá ze tří složek: **vstupy**, **výstupy**a **funkce**. Obsahuje také skript dotazu **(*. asaql)** , soubor **JobConfig. JSON** a konfigurační soubor **asaproj. JSON** .

    Konfigurační soubor **asaproj. JSON** obsahuje informace o vstupech, výstupech a konfiguračních souborech úloh potřebných k odeslání Stream Analytics úlohy do Azure.

    ![Stream Analytics soubory projektu v Visual Studio Code](./media/quick-create-vs-code/asa-project-files.png)

> [!Note]
> Když přidáváte vstupy a výstupy z palety příkazů, odpovídající cesty se automaticky přidají do **asaproj. JSON** . Pokud přidáváte nebo odebíráte vstupy nebo výstupy přímo na disku, musíte je ručně přidat nebo odebrat z **asaproj. JSON**. Vstupy a výstupy si můžete umístit na jednom místě a pak je odkazovat v různých úlohách zadáním cest v každém souboru **asaproj. JSON** .

## <a name="define-the-transformation-query"></a>Definice transformačního dotazu

1. Otevřete **myASAproj. asaql** z vaší složky projektu.

2. Přidejte následující dotaz:

   ```sql
   SELECT *
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="test-the-query-locally-with-sample-data"></a>Test dotazu místně pomocí ukázkových dat

Než spustíte dotaz v cloudu, můžete ho místně otestovat pomocí místního ukázkového datového souboru nebo s daty zachycenými ze živého vstupu a ověřit logiku dotazu.

Postupujte podle pokynů v části [test dotazů místně s ukázkovými daty](visual-studio-code-local-run.md) , kde najdete další podrobnosti.

 ![Testování s ukázkovými daty v Visual Studio Code](./media/vscode-local-run/localrun-localinput.gif)

## <a name="define-a-live-input"></a>Definování živého vstupu

1. V projektu Stream Analytics klikněte pravým tlačítkem na složku **vstupy** . Pak vyberte **ASA: přidat vstup** z místní nabídky.

    ![Přidat vstup ze složky Inputs](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

    Nebo vyberte **CTRL + SHIFT + P** a otevřete paletu příkazů a zadejte **ASA: přidat vstup**.

   ![Přidat vstup Stream Analytics v Visual Studio Code](./media/quick-create-vs-code/add-input.png)

2. Jako typ vstupu vyberte **IoT Hub** .

   ![Jako vstupní možnost vyberte IoT Hub.](./media/quick-create-vs-code/iot-hub.png)

3. Pokud jste přidali vstup z palety příkazů, vyberte Stream Analytics skript dotazu, který bude používat vstup. Měl by se automaticky naplnit cestou k souboru **myASAproj. asaql**.

   ![Vyberte Stream Analytics skript v Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. V rozevírací nabídce **Vyberte vybrat z předplatných Azure** .

    ![Vybrat z předplatných](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Upravte nově vygenerovaný soubor **IoTHub1. JSON** následujícími hodnotami. Ponechat výchozí hodnoty pro pole, která zde nejsou uvedena.

   |Nastavení|Navrhovaná hodnota|Popis|
   |-------|---------------|-----------|
   |Name (Název)|Vstup|Zadejte název pro identifikaci vstupu úlohy.|
   |IotHubNamespace|MyASAIoTHub|Vyberte nebo zadejte název centra IoT. Názvy centra IoT se zjišťují automaticky, pokud se vytvářejí ve stejném předplatném.|
   |SharedAccessPolicyName|iothubowner| |

   Můžete použít funkci CodeLens, která vám umožní zadat řetězec, vybrat z rozevíracího seznamu nebo změnit text přímo v souboru. Následující snímek obrazovky ukazuje jako příklad **možnost vybrat z vašich předplatných** .

   ![Konfigurace vstupu v Visual Studio Code](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>Náhled vstupu

V horním řádku vyberte **data náhledu** v **IoTHub1. JSON** . Některá vstupní data budou načtena z centra IoT a zobrazí se v okně náhledu. Tato operace může chvíli trvat.

 ![Náhled živého vstupu](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-an-output"></a>Definování výstup

1. Kliknutím na **CTRL + SHIFT + P** otevřete paletu příkazů. Pak zadejte **ASA: Přidat výstup**.

   ![Přidat výstup Stream Analytics v Visual Studio Code](./media/quick-create-vs-code/add-output.png)

2. Jako typ jímky vyberte **BLOB Storage** .

3. Vyberte Stream Analytics skript dotazu, který bude používat tento vstup.

4. Jako **BlobStorage**zadejte název výstupního souboru.

5. Upravte **BlobStorage** pomocí následujících hodnot. Ponechat výchozí hodnoty pro pole, která zde nejsou uvedena. Použijte funkci CodeLens, která vám umožní vybrat z rozevíracího seznamu nebo zadat řetězec.

   |Nastavení|Navrhovaná hodnota|Popis|
   |-------|---------------|-----------|
   |Name (Název)|Výstup| Zadejte název pro identifikaci výstupu úlohy.|
   |Účet úložiště|asaquickstartstorage|Vyberte nebo zadejte název svého účtu úložiště. Názvy účtů úložiště se automaticky zjišťují, pokud se vytvářejí ve stejném předplatném.|
   |Kontejner|container1|Vyberte existující kontejner, který jste vytvořili v účtu úložiště.|
   |Vzor cesty|output|Zadejte název cesty k souboru, která se vytvoří v rámci kontejneru.|

   ![Konfigurace výstupu v Visual Studio Code](./media/quick-create-vs-code/configure-output.png)

## <a name="compile-the-script"></a>Zkompilujte skript

Kompilace skriptu kontroluje syntaxi a generuje šablony Azure Resource Manager pro automatické nasazení.

Existují dva způsoby, jak spustit kompilaci skriptu:

- Vyberte z pracovního prostoru skript a potom zkompilujte z palety příkazů.

   ![Použití palety příkazů Visual Studio Code ke kompilaci skriptu](./media/quick-create-vs-code/compile-script1.png)

- Klikněte pravým tlačítkem na skript a vyberte **ASA: kompilovat skript**.

    ![Klikněte pravým tlačítkem na skript Stream Analytics ke kompilaci.](./media/quick-create-vs-code/compile-script2.png)

Po kompilaci můžete najít dvě vygenerované Azure Resource Manager šablony ve složce **Deploy** v projektu. Tyto dva soubory se používají pro automatické nasazení.

![Šablony nasazení Stream Analytics v Průzkumníkovi souborů](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Odeslání úlohy Stream Analytics do Azure

1. V okně Editor skriptů ve vašem skriptu dotazu vyberte **Odeslat do Azure**.

   ![Výběr z textu vašich předplatných v editoru skriptu](./media/quick-create-vs-code/submit-job.png)

2. V místním seznamu vyberte své předplatné.

3. Zvolte **Vybrat úlohu**. Pak zvolte **vytvořit novou úlohu**.

4. Zadejte název své úlohy, **myASAjob**. Pak podle pokynů zvolte skupinu prostředků a její umístění.

5. Vyberte **Odeslat do Azure**. Protokoly najdete v okně výstup. 

6. Když je vaše úloha vytvořená, můžete ji zobrazit v **průzkumníkovi Stream Analytics**.

    ![Uvedená úloha v Průzkumníkovi Stream Analytics](./media/quick-create-vs-code/list-job.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

1. Otevřete **Stream Analytics Explorer** v Visual Studio Code a najděte svoji úlohu **myASAJob**.

2. Klikněte pravým tlačítkem myši na název úlohy. Pak v místní nabídce vyberte možnost **Spustit** .

   ![Spustit úlohu Stream Analytics v Visual Studio Code](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. V místním okně vyberte **nyní** a spusťte úlohu.

4. Všimněte si, že stav úlohy se změnil na **spuštěno**. Kliknutím pravým tlačítkem myši na název úlohy a výběrem **Otevřít zobrazení úlohy na portálu** Zobrazte metriky vstupní a výstupní události. Tato akce může trvat několik minut.

5. Chcete-li zobrazit výsledky, otevřete úložiště objektů BLOB v rozšíření Visual Studio Code nebo v Azure Portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky. Odstraněním úlohy se vyhnete účtování jednotek streamování, které úloha spotřebovává. 

Pokud plánujete používat tuto úlohu v budoucnu, můžete ji zastavit a znovu ji spustit později. Pokud tuto úlohu nebudete znovu používat, odstraňte všechny prostředky, které jste vytvořili v tomto rychlém startu, pomocí následujícího postupu:

1. V nabídce vlevo v Azure Portal vyberte **skupiny prostředků** a potom vyberte název prostředku, který jste vytvořili.  

2. Na stránce vaší skupiny prostředků vyberte **Odstranit**. Do textového pole zadejte název prostředku, který chcete odstranit, a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jednoduchou Stream Analyticsovou úlohu pomocí Visual Studio Code. Stream Analytics úlohy můžete nasadit také pomocí [Azure Portal](stream-analytics-quick-create-portal.md), [PowerShellu](stream-analytics-quick-create-powershell.md)a sady [Visual Studio](stream-analytics-quick-create-vs.md).

Další informace o Azure Stream Analytics nástrojů pro Visual Studio Code najdete v následujících článcích:

* [Testování Azure Stream Analytics úloh místně proti živému vstupu pomocí Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Použití Visual Studio Code k zobrazení úloh Azure Stream Analytics](visual-studio-code-explore-jobs.md)

* [Nastavení kanálů CI/CD pomocí balíčku npm](setup-cicd-vs-code.md)
