---
title: Ověřovací aktivita v Azure Data Factory
description: Ověřovací aktivita nepokračuje v provádění kanálu, dokud neověřuje připojenou datovou sadu s určitými kritérii, které uživatel zadá.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 764b41d1823e8edce134c5099e066486f4f08acc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81417925"
---
# <a name="validation-activity-in-azure-data-factory"></a>Ověřovací aktivita v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ověřování v kanálu můžete použít k tomu, abyste zajistili, že kanál bude pokračovat jenom po ověření, že existuje odkaz na připojenou datovou sadu, který splňuje zadaná kritéria, nebo se dosáhlo vypršení časového limitu.


## <a name="syntax"></a>Syntax

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Povolené hodnoty | Vyžadováno
-------- | ----------- | -------------- | --------
name | Název aktivity ověření | Řetězec | Ano |
typ | Musí být nastavené na **ověřování**. | Řetězec | Ano |
integrován | Aktivita bude blokovat provádění, dokud neověří, zda existuje tento odkaz na datovou sadu a zda splňuje zadaná kritéria, nebo byl dosažen časový limit. Zadaná datová sada by měla podporovat vlastnost "MinimumSize" nebo "ChildItems". | Odkaz na datovou sadu | Ano |
timeout | Určuje časový limit pro spuštění aktivity. Pokud není zadaná žádná hodnota, výchozí hodnota je 7 dní ("7.00:00:00"). Formát je d. hh: mm: SS | Řetězec | Ne |
spat | Zpoždění v sekundách mezi pokusy o ověření. Pokud není zadaná žádná hodnota, výchozí hodnota je 10 sekund. | Integer | Ne |
childItems | Kontroluje, zda složka obsahuje podřízené položky. Lze nastavit na hodnotu-true: Ověřte, zda složka existuje a zda má položky. Blokuje, dokud není k dispozici alespoň jedna položka ve složce nebo hodnota časového limitu.-false: Ověřte, zda složka existuje a zda je prázdná. Blokuje, dokud není složka prázdná, nebo dokud nedosáhnete hodnoty časového limitu. Pokud není zadaná žádná hodnota, aktivita se zablokuje, dokud složka neexistuje nebo dokud nebude dosaženo časového limitu. | Logická hodnota | Ne |
minimumSize | Minimální velikost souboru v bajtech Pokud není zadaná žádná hodnota, výchozí hodnota je 0 bajtů. | Integer | No |


## <a name="next-steps"></a>Další kroky
Podívejte se na další aktivity toku řízení podporované Data Factory:

- [Aktivita podmínky Když](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Aktivita For Each](control-flow-for-each-activity.md)
- [Aktivita získání metadat](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
