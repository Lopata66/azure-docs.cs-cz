---
title: Obchodní karty – rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Přečtěte si o konceptech souvisejících s analýzou vizitky pomocí rozhraní API pro rozpoznávání formulářů – využití a omezení.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 8519daffd8ee0ad6c12be2f40fd8030caaaf34c8
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2020
ms.locfileid: "88724973"
---
# <a name="business-card-concepts"></a>Koncepce obchodních karet

Nástroj pro rozpoznávání formulářů Azure dokáže analyzovat a extrahovat páry klíčových hodnot z obchodních karet pomocí jednoho z předem připravených modelů. Rozhraní API pro vizitky kombinuje výkonné funkce optického rozpoznávání znaků (OCR) s využitím modelu porozumění naší firmou pro extrakci klíčových informací z vizitek v angličtině. Extrahuje osobní kontaktní údaje, název společnosti, pracovní zařazení a další. Předem připravené rozhraní API na vizitce je veřejně dostupné v nástroji pro rozpoznávání formulářů v 2.1 Preview. 

## <a name="what-does-the-business-card-api-do"></a>Co dělá rozhraní API na vizitce?

Rozhraní API pro vizitky extrahuje klíčová pole z obchodních karet a vrátí je v organizované odpovědi JSON.

![Z výstupu FOTT + JSON se jedná o položku contoso s použitím obrázku](./media/business-card-english.jpg)

### <a name="fields-extracted"></a>Extrahovaná pole: 
* Jména kontaktů 
* Jméno 
* Příjmení 
* Názvy společností 
* Oddělení 
* Pracovní tituly 
* E-maily 
* Websites 
* Adresy 
* Telefonní čísla 
  * Mobilní telefony 
  * Faxy 
  * Pracovní telefony 
  * Další telefony 

Rozhraní API pro vizitky vrátí také veškerý rozpoznaný text z vizitky. Tento výstup optického rozpoznávání znaků je zahrnutý v odpovědi JSON.  

### <a name="input-requirements"></a>Požadavky na vstup 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-business-card-operation"></a>Operace analyzovat obchodní kartu

[Karta analyzovat obchodní kartu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) přebírá obrázek nebo PDF vizitky jako vstup a získává hodnoty zájmu a textu. Volání vrátí pole hlavičky odpovědi s názvem `Operation-Location` . `Operation-Location`Hodnota je adresa URL, která obsahuje ID výsledku, které se má použít v dalším kroku.

|Hlavička odpovědi| Adresa URL výsledku |
|:-----|:----|
|Operace – umístění | `https://cognitiveservice/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>Operace získání výsledku analýzy obchodních karet

Druhým krokem je zavolat operaci [získat výsledky analýzy obchodních karet](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/GetAnalyzeBusinessCardResult) . Tato operace přijímá jako vstup ID výsledku, které bylo vytvořeno operací analyzovat obchodní kartu. Vrátí odpověď JSON, která obsahuje pole **stav** s následujícími možnými hodnotami. Tuto operaci zavoláte iterativní, dokud se nevrátí s hodnotou **úspěch** . Použijte interval 3 až 5 sekund, abyste předešli překročení sazby za sekundu (RPS).

|Pole| Typ | Možné hodnoty |
|:-----|:----:|:----|
|status | řetězec | notStarted: operace analýzy nebyla spuštěna. |
| |  | běží: probíhá operace analýzy. |
| |  | Nepovedlo se: operace analýzy se nezdařila. |
| |  | úspěch: operace analýzy byla úspěšná. |

Když pole **Status (stav** ) má hodnotu **úspěch** , odpověď JSON bude obsahovat informace o porozumění a výsledcích rozpoznávání textu na vizitce. Výsledek porozumění vizitce se organizuje jako slovník hodnot pojmenovaných polí, kde každá hodnota obsahuje extrahovaný text, normalizovanou hodnotu, ohraničovací rámeček, spolehlivost a odpovídající prvky slova. Výsledek rozpoznávání textu je uspořádán jako hierarchie řádků a slov s textem, ohraničujícím polem a informacemi o spolehlivosti.

### <a name="sample-json-output"></a>Ukázkový výstup JSON

Podívejte se na následující příklad úspěšné odpovědi JSON: uzel "readResults" obsahuje veškerý rozpoznaný text. Text je uspořádán podle stránky, potom podle řádku, podle jednotlivých slov. Uzel "documentResults" obsahuje hodnoty specifické pro obchodní karty, které model zjistil. Tady najdete užitečné páry klíč/hodnota jako křestní jméno, příjmení, název společnosti a další.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
    "lines": 
         {
                        "text": "Dr. Avery Smith",
                        "boundingBox": [
                            419.3,
                            1154.6,
                            1589.6,
                            877.9,
                            1618.9,
                            1001.7,
                            448.6,
                            1278.4
                        ],
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]
    
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Postupujte podle pokynů v části rychlý Start pro extrakci [dat obchodních karet](./QuickStarts/python-business-cards.md) a implementujte extrakci dat vizitky pomocí Pythonu a REST API.

## <a name="customer-scenarios"></a>Scénáře zákazníků  

Data extrahovaná pomocí rozhraní API na vizitce lze použít k provádění různých úloh. Extrakce těchto kontaktních informací automaticky šetří čas pro ty v rolích klientů. Tady je několik příkladů toho, co naši zákazníci dokončí s rozhraním API na vizitce:

* Rozbalí kontaktní údaje z obchodních karet a rychle vytvoří telefonní kontakty. 
* Integrací s CRM můžete automaticky vytvářet kontakty s použitím obrázků vizitek. 
* Udržujte si přehled o potenciálních prodejích.  
* Hromadně extrahuje kontaktní informace z existujících imagí vizitky. 

Rozhraní API na vizitce také zajišťuje [funkci pro zpracování AIBuilder obchodních karet](https://docs.microsoft.com/ai-builder/prebuilt-business-card).

## <a name="next-steps"></a>Další kroky

- Začněte pomocí rychlého startu. 
- [Rychlé zprovoznění Pythonu na vizitkách API](./quickstarts/python-business-cards.md)
- Další informace o [REST API pro rozpoznávání formulářů](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer/api)
- Další informace o funkci [pro rozpoznávání formulářů](overview.md)


