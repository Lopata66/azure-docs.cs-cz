---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: 758577f91766d621220d7efdd72e7c2705ccff7f
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56448241"
---
1. Spusťte Eclipse.

1. Ve spouštěcím programu Eclipse zadejte do pole **Workspace** (Pracovní prostor) název nového adresáře pracovního prostoru. Potom vyberte **Launch** (Spustit).

   ![Snímek obrazovky spouštěcího programu Eclipse](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Za chvíli se zobrazí hlavní okno prostředí Eclipse IDE. Pokud se zobrazí úvodní obrazovka, zavřete ji.

1. Vytvořte nový projekt tak, že na řádku nabídek Eclipse vyberete **File** (Soubor)  >  **New** (Nový)  >  **Project** (Projekt).

1. Zobrazí se dialogové okno **Nový projekt**. Vyberte **Java Project** (Projekt Javy) a vyberte **Next** (Další).

   ![Snímek obrazovky dialogového okna New Project (Nový projekt) se zvýrazněnou možností Java Project (Projekt Javy)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. Spustí se průvodce novým projektem Javy. Do pole **Project name** (Název projektu) zadejte **quickstart** a jako spouštěcí prostředí zvolte **JavaSE-1.8**. Vyberte **Finish** (Dokončit).

   ![Snímek obrazovky s průvodcem novým projektem Javy](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. Pokud se zobrazí okno **Open Associated Perspective?** (Otevřít související perspektivu?), vyberte **Open Perspective** (Otevřít perspektivu).

1. V **průzkumníku balíčků** klikněte pravým tlačítkem myši na projekt **quickstart**. V místní nabídce zvolte **Configure** (Konfigurovat)  >  **Convert to Maven Project** (Převést na projekt Maven).

   ![Snímek obrazovky s průzkumníkem balíčků](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Zobrazí se okno **Create new POM** (Vytvořit nový POM). Do pole **Group Id** (ID skupiny) zadejte **com.microsoft.cognitiveservices.speech.samples** a do pole **Artifact Id** (ID artefaktu) zadejte **quickstart**. Pak vyberte **Dokončit**.

   ![Snímek obrazovky s oknem Create new POM (Vytvořit nový POM)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Otevřete soubor **pom.xml** a upravte ho.

   * Na konci souboru před ukončovací značkou `</project>` vytvořte element `repositories` s odkazem na úložiště Maven pro sadu Speech SDK, jak můžete vidět tady:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

   * Také přidat `dependencies` element se sadou SDK pro řeč verze 1.3.0 jako závislost:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * Uložte změny.
