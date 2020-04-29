---
title: Microsoft Threat Modeling Tool verze 02/11/2020 – Azure
description: Dokumentující poznámky k verzi pro nástroj pro modelování hrozeb
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 354707aec90375b4bf25aea6e1baa743d85f20aa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77624836"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Threat Modeling Tool vydání aktualizace 7.3.00206.1-02/11/2020

7.3.00206.1 verze Microsoft Threat Modeling Tool (TMT) byla vydaná v únoru 11 2020 a obsahuje následující změny:

- Opravy chyb

## <a name="notable-bug-fixes"></a>Významné opravy chyb

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Chyby související s prioritními hodnotami mimo očekávané rozsahy

Někteří zákazníci oznámili, že při otevírání souborů vytvořených v Threat Modeling Tool 2016 nebo ve vlastních šablonách obdrželi následující chybovou zprávu:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Tento problém byl vyřešen v této verzi.

## <a name="system-requirements"></a>Požadavky na systém

- Podporované operační systémy
  - [Microsoft Windows 10 – aktualizace pro výročí](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) nebo novější
- Vyžaduje se verze .NET.
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) nebo novější
- Další požadavky
  - K získání aktualizací nástroje a také šablon je vyžadováno připojení k Internetu.

## <a name="documentation-and-feedback"></a>Dokumentace a zpětná vazba

- Dokumentace k Threat Modeling Tool je umístěna na [docs.Microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)a obsahuje informace [o použití nástroje](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Další kroky

Stáhněte si nejnovější verzi [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
