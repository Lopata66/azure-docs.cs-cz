---
title: Běžné výjimky FabricClient | Dokumentace Microsoftu
description: Popisuje běžné výjimky a chyby, které mohou být vyvolány pomocí rozhraní API FabricClient při provádění aplikace a operace správy clusterů.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: bb821313-b221-479f-b08e-36cf07e60a07
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/20/2018
ms.author: oanapl
ms.openlocfilehash: 5bf17f4ced6bb01d8b62b6fa40ed1aeffe6f712f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946528"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Běžné výjimky a chyby při práci s rozhraními API FabricClient
[FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) rozhraní API umožňují správcům aplikací a clusteru k provádění úloh správy v aplikaci Service Fabric, služby nebo clusteru. Například nasazení aplikace, upgrade a odebrání, kontrola stavu clusteru nebo testování služby. Vývojáři aplikací a Správce clusterů můžete použít rozhraní API FabricClient pro vývoj aplikací pro správu clusteru Service Fabric a aplikací.

Existuje mnoho různých typů operací, které je možné provádět pomocí FabricClient.  Každá metoda může vyvolat výjimky, chyby kvůli nesprávný vstup, chyby za běhu nebo problémů s infrastrukturou přechodné.  Zobrazit referenční dokumentaci rozhraní API k vyhledání výjimek, které jsou vyvolány pomocí konkrétní metody. Existují některé výjimky, ale které mohou být vyvolány mnoho různých [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) rozhraní API. V následující tabulce jsou uvedeny výjimky, které jsou společné pro rozhraní API FabricClient.

| Výjimka | Vyvolána, když |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |[FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objektu je v uzavřeném stavu. Vyřazení [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) používáte a vytvoření instance nového objektu [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objektu. |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |Vypršel časový limit operace. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) je vrácena, když operace trvá déle než MaxOperationTimeout dokončete. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |Kontrola přístupu pro operace se nezdařila. E_ACCESSDENIED je vrácena. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Při provádění operace došlo k chybě za běhu. Některé z metod FabricClient může potenciálně vyvolat [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception), [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode) vlastnost určuje přesné příčině výjimky. Kódy chyb jsou definovány v [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) výčtu. |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |Operace se nezdařila z důvodu přechodných chybový stav určitého druhu. Operace může například selhat, protože kvorum replik je dočasně nedostupná. Přechodným výjimkám odpovídají neúspěšné operace, které umožňují opakovaný pokus. |

Některé běžné [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) chyby, které mohou být vráceny v [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception):

| Chyba | Podmínka |
| --- |:--- |
| CommunicationError |Komunikační chyba způsobila operace, která se nezdaří, zkuste operaci zopakovat. |
| InvalidCredentialType |Typ přihlašovacích údajů je neplatný. |
| InvalidX509FindType |X509FindType je neplatný. |
| InvalidX509StoreLocation |X509 umístění úložiště je neplatný. |
| InvalidX509StoreName |X509 název úložiště je neplatný. |
| InvalidX509Thumbprint |X509 řetězec kryptografického otisku certifikátu je neplatný. |
| InvalidProtectionLevel |Úroveň ochrany je neplatný. |
| InvalidX509Store |X509 nelze otevřít úložiště certifikátů. |
| InvalidSubjectName |Název předmětu není platný. |
| InvalidAllowedCommonNameList |Formát řetězce seznam běžných název je neplatný. Měla by být seznam oddělený čárkami. |

