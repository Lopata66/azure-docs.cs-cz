---
title: Korelace cloudových inteligentních výstrah v Azure Security Center (incidenty) | Microsoft Docs
description: Toto téma vysvětluje, jak Fusion používá relaci cloudových inteligentních výstrah ke generování incidentů zabezpečení v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 4c2f084fe03271b29a12aa6906f5e36612d50674
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202692"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Korelace cloudových inteligentních výstrah v Azure Security Center (incidenty)

Azure Security Center průběžně analyzuje hybridní cloudové úlohy pomocí pokročilých analýz a analýzy hrozeb, které vás upozorňují na škodlivou aktivitu.

Šířka pokrytí hrozbami roste. Nutnost detekovat i mírné ohrožení zabezpečení je důležité a může být náročné na to, aby analytici zabezpečení mohli určit různé výstrahy a identifikovat skutečný útok. Security Center pomáhá analytikům vypořádat se s tímto neúnavou výstrahou. Pomáhá diagnostikovat útoky při jejich výskytu tím, že koreluje různé výstrahy a signály s nízkou věrností do incidentů zabezpečení.

Fusion Analytics je technologický a analytický back-end, který splňuje Security Center incidenty a umožňuje tak korelaci různých výstrah a kontextových signálů. Fúze vychází z různých signálů hlášených u předplatného napříč prostředky. Fusion vyhledá vzory, které odhalí průběh útoku nebo signály se sdílenými kontexty informací, což značí, že byste pro ně měli použít jednotný postup odezvy.

Analytics Analytics kombinuje znalostní bázi zabezpečení domény se systémem AI a analyzuje výstrahy a zjišťuje nové vzory útoků, když k nim dojde. 

Security Center využívá matrici útoků MITRE k přidružení výstrah k jejich vnímanému záměru a pomáhá znalostní bázi formalizovat zabezpečení domény. Kromě toho můžete pomocí informací shromážděných pro každý krok útoku Security Center vyfiltrovat aktivitu, která se jeví jako kroky útoku, ale ve skutečnosti ne.  

Vzhledem k tomu, že k útokům často dochází v různých klientech, Security Center mohou kombinovat algoritmy AI a analyzovat tak sekvence útoků, které jsou hlášeny v každém předplatném Tato technika identifikuje posloupnosti útoků jako vzorové výstrahy, místo toho, aby se k sobě navzájemně nepřidružil.

V průběhu vyšetřování incidentu často potřebují analytikům přístup k závěru o povaze hrozby a o tom, jak ho zmírnit. Například, i když je zjištěna anomálie sítě, bez pochopení, co se děje v síti nebo s ohledem na cílový prostředek, je obtížné pochopit, jaké akce mají být další. Pro usnadnění může incident zabezpečení zahrnovat artefakty, související události a informace. Další informace, které jsou k dispozici pro incidenty zabezpečení, se liší v závislosti na typu zjištěné hrozby a konfiguraci vašeho prostředí. 

![Snímek obrazovky s zjištěnou událostí bezpečnostního incidentu](./media/security-center-alerts-cloud-smart/security-incident.png)

Chcete-li lépe porozumět incidentům zabezpečení, přečtěte si téma [jak řešit incidenty zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-incident).

