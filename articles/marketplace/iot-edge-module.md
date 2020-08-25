---
title: Nabídky modulu Azure Marketplace IoT Edge
description: Přečtěte si informace o publikování IoT Edge modulu nabídky v Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/15/2020
ms.openlocfilehash: f7a4997117c8730b767f245050caa3f3157fb6fd
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750246"
---
# <a name="iot-edge-modules"></a>Moduly IoT Edge

[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) platforma se zálohuje pomocí Microsoft Azure.  Tato platforma umožňuje uživatelům nasadit cloudové úlohy pro přímé spouštění na zařízeních IoT.  Modul IoT Edge může spouštět offline úlohy a provádět analýzu dat místně. Tento typ nabídky pomáhá ušetřit šířku pásma, chránit místní a citlivá data a nabízí dobu odezvy s nízkou latencí.  Nyní máte možnost využít tyto předem připravené úlohy. Až do této chvíle byly k dispozici jenom několik řešení od společnosti Microsoft.  Museli byste investovat čas a prostředky do sestavení vlastních řešení IoT.

S [IoT Edge moduly v Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)teď máme jediné místo, kde vydavatelé zveřejňují a prodávají svá řešení pro cílovou skupinu IoT. Vývojáři IoT můžou nakonec najít a koupit možnosti pro zrychlení vývoje řešení.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Klíčové výhody IoT Edgech modulů v Azure Marketplace

| **Vydavatelům**    | **Pro zákazníky (vývojáři IoT)**  |
| :------------------- | :-------------------|
| Oslovit miliony vývojářů, kteří chtějí sestavovat a nasazovat řešení IoT Edge.  | Vytvořte IoT Edge řešení s jistotou použití zabezpečených a testovaných komponent. |
| Publikujte jednou a spusťte napříč jakýmkoli IoT Edge hardwarem, který podporuje kontejnery. | Vyhledáním a nasazením IoT Edge modulů od prvního a jiného výrobce se zkrátí doba uvedení na trh pro konkrétní potřeby. |
| Monetizovat s možnostmi flexibilní fakturace <ul> <li> Bezplatné a přineste si vlastní licenci (BYOL). </li> </ul> | Nakupujete podle svého výběru modelů fakturace. <ul> <li> Bezplatné a přineste si vlastní licenci (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Co je modul IoT Edge?

Azure IoT Edge umožňuje nasadit a spravovat obchodní logiku na hraničních zařízeních ve formě modulů. Azure IoT Edge moduly jsou nejmenší výpočetní jednotky spravované IoT Edge a můžou obsahovat služby Microsoftu (například Azure Stream Analytics), služby třetích stran nebo vlastní kód specifický pro řešení. Další informace o IoT Edgech modulech najdete v tématu [principy Azure IoT Edgech modulů](../iot-edge/iot-edge-modules.md).

**Jaký je rozdíl mezi typem nabídky kontejneru a typem nabídky modulu IoT Edge?**

Typ nabídky IoT Edge modul je konkrétní typ kontejneru, který je spuštěný v IoT Edgem zařízení. Je k dispozici s výchozím nastavením konfigurace pro spuštění v kontextu IoT Edge a volitelně používá sadu IoT Edge Module SDK pro integraci s modulem runtime IoT Edge.

## <a name="publishing-your-iot-edge-module"></a>Publikování modulu IoT Edge

**Výběr správného online obchodu**

IoT Edge moduly jsou publikovány pouze do Azure Marketplace, AppSource se nevztahují.  Další informace o rozdílech a cílové skupině v online obchodech najdete v tématu [Určení možnosti publikování](determine-your-listing-type.md).
 
**Možnosti fakturace**

Web Marketplace v současnosti podporuje **bezplatné** možnosti fakturace a přináší **vlastní licenci (BYOL)** pro moduly IoT Edge.
 
**Možnosti publikování**

Ve všech případech by IoT Edge moduly měly vybrat možnost publikování v režimu **Transact** .  Další podrobnosti o možnostech publikování najdete v tématu Volba [Možnosti publikování](determine-your-listing-type.md) .  

## <a name="eligibility-criteria"></a>Kritéria způsobilosti

Všechny podmínky Microsoft Azure Marketplace smluv a zásad se vztahují na nabídky modulu IoT Edge.  Kromě toho existují požadavky a technické požadavky pro IoT Edge moduly.  

**Předpoklady**

Chcete-li publikovat modul IoT Edge do Azure Marketplace, je nutné splnit následující požadavky:

- Přístup k partnerskému centru. Další informace najdete v tématu [Příručka pro publikování Azure Marketplace a AppSource](marketplace-publishers-guide.md).
- Hostování modulu IoT Edge v Azure Container Registry. 
- Připravte si metadata modulu IoT Edge, například (nevyčerpávající seznam): 
    - Název
    - Popis (ve formátu HTML)
    - Obrázek loga (formát PNG a pevné velikosti obrázků, včetně 40x40px, 90x90px, 115x115px, 255x115px)
    - Podmínky použití a zásad ochrany osobních údajů
    - Výchozí konfigurace modulu (trasa, nevlákenovaná požadovaná vlastnost, createOptions, proměnné prostředí)
    - Dokumentace
    - Kontaktní údaje podpory

**Technické požadavky**

Hlavní technické požadavky pro modul IoT Edge, aby bylo možné získat certifikaci a publikovat ho v Azure Marketplace, je podrobně popsáno v [technickém assetu příprava IoT Edge modulu](./partner-center-portal/create-iot-edge-module-asset.md).

## <a name="next-steps"></a>Další kroky

- [Přečtěte si](https://azuremarketplace.microsoft.com/sell) o komerčním tržišti.
- Přihlaste se do [partnerského centra](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) a vytvořte nebo dokončete vaši nabídku.
- [Vytvořte nabídku modulu IoT Edge](./partner-center-portal/azure-iot-edge-module-creation.md) v partnerském centru.
