---
title: Řešení potíží s Azure Data Factory
description: Naučte se řešit potíže s používáním Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: craigg
robots: noindex
ms.openlocfilehash: d729fd11f355650b1476e6864a6d70219bf37e12
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135124"
---
# <a name="troubleshoot-data-factory-issues"></a>Poradce při potížích se službou Data Factory
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. 

Tento článek popisuje tipy k odstraňování potíží při použití Azure Data Factory. Tento článek neuvádí všechny možné problémy při používání služby, ale zabývá se některými problémy a obecnými tipy pro řešení potíží.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Chyba: U předplatného není zaregistrované používání oboru názvů Microsoft. DataFactory.
Pokud se zobrazí tato chyba, poskytovatel prostředků Azure Data Factory není na vašem počítači zaregistrovaný. Udělejte toto:

1. Spusťte Azure PowerShell.
2. Přihlaste se k účtu Azure pomocí následujícího příkazu.

    ```powershell
    Connect-AzAccount
    ```
3. Spuštěním následujícího příkazu zaregistrujte poskytovatele Azure Data Factory.

    ```powershell        
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Řešení Neoprávněná Chyba při spuštění rutiny Data Factory
Pravděpodobně pro Azure PowerShell nepoužíváte správné předplatné nebo účet Azure. Pomocí následujících rutin vyberte správné předplatné a účet Azure pro použití s Azure PowerShellem.

1. Connect-AzAccount – použijte správné ID a heslo uživatele.
2. Get-AzSubscription – zobrazí všechna předplatná pro tento účet.
3. &lt;Název&gt; předplatného Select-AzSubscription – vyberte správné předplatné. Použijte stejný ten, který použijete k vytvoření datové továrny na Azure Portal.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Řešení Nepodařilo se spustit instalaci expresní brány Správa dat z Azure Portal
Expresní instalace pro Bránu pro správu dat vyžaduje Internet Explorer nebo webový prohlížeč kompatibilní s technologií Microsoft ClickOnce. Pokud se expresní instalace nezdaří, použijte jeden z následujících postupů:

* Použijte Internet Explorer nebo webový prohlížeč kompatibilní s Microsoft ClickOnce.

    Pokud používáte Chrome, přejděte na [internetový obchod Chrome](https://chrome.google.com/webstore/), dejte hledat klíčové slovo ClickOnce, vyberte některé z rozšíření ClickOnce a nainstalujte je.

    To samé udělejte pro Firefox (instalovat doplněk). Na panelu nástrojů klikněte na tlačítko Otevřít nabídku (tři vodorovné čáry v pravém horním rohu), klikněte na Doplňky, dejte hledat klíčové slovo ClickOnce, vyberte některé z rozšíření ClickOnce a nainstalujte je.
* Použijte odkaz na **ruční instalaci** , který je zobrazený ve stejném okně na portálu. Tento přístup slouží ke stažení instalačního souboru a jeho spuštění ručně. Po úspěšném dokončení instalace se zobrazí dialogové okno Správa dat konfigurace brány. Zkopírujte **klíč** z okna portálu a použijte ho ve správci konfigurace k ruční registraci brány v příslušné službě.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Řešení Nepodařilo se připojit k místním SQL Server
Na počítači brány spusťte **Správa dat Configuration Manager brány** a pomocí karty **Poradce při potížích** otestujte připojení k SQL Server z počítače brány. Tipy k odstraňování potíží souvisejících s připojením nebo bránou najdete v tématu [řešení](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) potíží s bránou.   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Řešení Vstupní řezy jsou napořád ve stavu Čekání.
Řezy mohou být ve stavu **čekání** z různých důvodů. Jedním z běžných důvodů je, že vlastnost **External** není nastavená na **hodnotu true**. Jakákoli datová sada vytvořená mimo rozsah Azure Data Factory by měla být označená **externí** vlastností. Tato vlastnost označuje, že data jsou externá a nezálohovaná žádnými kanály v rámci datové továrny. Jakmile jsou data v příslušných úložištích dostupná, datové řezy se označí jako **připravené**.

Použití vlastnosti **external** si můžete prohlédnout v následujícím příkladu. Volitelně můžete zadat **externalData** *, pokud nastavíte možnost externí na hodnotu true.

Další podrobnosti o této vlastnosti najdete v článku [Datové sady](data-factory-create-datasets.md).

```json
{
  "name": "CustomerTable",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "MyLinkedService",
    "typeProperties": {
      "folderPath": "MyContainer/MySubFolder/",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      }
    }
  }
}
```

Pokud chcete tuto chybu vyřešit, přidejte vlastnost **external** a volitelný oddíl **externalData** do definice JSON vstupní tabulky a potom tuto tabulku vytvořte znovu.

### <a name="problem-hybrid-copy-operation-fails"></a>Řešení Operace hybridního kopírování se nezdařila
Postup řešení potíží s kopírováním do nebo z místního úložiště dat pomocí Správa dat brány najdete v tématu [řešení](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) potíží s bránou.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Řešení Zřizování HDInsight na vyžádání se nepovede.
Při použití propojené služby typu HDInsightOnDemand je nutné zadat linkedServiceName, který odkazuje na Blob Storage Azure. Služba Data Factory využívá toto úložiště k ukládání protokolů a podpůrných souborů pro cluster HDInsight na vyžádání.  Někdy se zřizování clusteru HDInsight na vyžádání nezdaří s následující chybou:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Tato zpráva obvykle indikuje, že umístění účtu úložiště zadané vlastností linkedServiceName se neshoduje s umístěním datového centra, ve kterém dochází ke zřizování HDInsightu. Příklad: Pokud je vaše Datová továrna v Západní USA a služba Azure Storage je v Východní USA, zřizování na vyžádání se v Západní USA nezdařilo.

Navíc existuje ještě druhá vlastnost additionalLinkedServiceNames JSON, která umožňuje zadat další účty úložiště v HDInsightu na vyžádání. Tyto další propojené účty úložiště by měly být ve stejném umístění jako cluster HDInsight, nebo dojde k chybě se stejnou chybou.

### <a name="problem-custom-net-activity-fails"></a>Řešení Vlastní aktivita technologie .NET selhala
Podrobný postup najdete v tématu [ladění kanálu pomocí vlastní aktivity](data-factory-use-custom-activities.md#troubleshoot-failures) .

## <a name="use-azure-portal-to-troubleshoot"></a>Řešení potíží pomocí Azure Portal
### <a name="using-portal-blades"></a>Používání oken portálu
Postup najdete v tématu [monitorování kanálu](data-factory-monitor-manage-pipelines.md) .

### <a name="using-monitor-and-manage-app"></a>Pomocí aplikace Monitorování a správa
Podrobnosti najdete v tématu [monitorování a Správa kanálů služby Data Factory pomocí monitorování a správy aplikace](data-factory-monitor-manage-app.md) .

## <a name="use-azure-powershell-to-troubleshoot"></a>Řešení potíží pomocí Azure PowerShell
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Řešení chyby pomocí Azure PowerShell
Podrobnosti najdete v tématu [monitorování data Factorych kanálů pomocí Azure PowerShell](data-factory-monitor-manage-pipelines.md) .

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: https://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
