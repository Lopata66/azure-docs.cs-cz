---
title: Správa aktualizací v přehledu služby Azure Stack | Dokumentace Microsoftu
description: Další informace o správě aktualizací pro integrované systémy Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2019
ms.author: mabrigg
ms.lastreviewed: 01/22/2019
ms.openlocfilehash: a5fe2d9b295a72b8e2e3adb7e5cffd5a98c03070
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487511"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Správa aktualizací v přehledu služby Azure Stack

*Platí pro: Integrované systémy Azure Stack*

Balíčky aktualizací Microsoftu pro Azure Stack integrované systémy obvykle release kolem čtvrtý úterý v měsíci. Požádejte vašeho OEM dodavatele o proces jejich konkrétní upozornění k zajištění oznámení o aktualizacích kontaktovat vaše organizace. Můžete zkontrolovat v této knihovně dokumentace v části **přehled** > **poznámky k verzi** informace o vydaných verzích, které jsou v aktivní technické podpory. 

Každá verze aktualizace softwaru Microsoftu se dodává v sadě jako jednu aktualizaci balíčku. Jako operátory Azure stacku můžete importovat, instalace a sledování průběhu instalace těchto balíčků z portálu správce aktualizací. 

Dodavatele hardwaru, výrobce OEM (OEM) bude také vydávat aktualizace, jako je například aktualizace ovladače a firmware. Přestože tyto aktualizace poskytuje váš dodavatel hardwaru OEM jako samostatné balíčky, jejich import, instalace a správa probíhá stejně jako v případě balíčků aktualizací od Microsoftu.

Aby váš systém v rámci podpory, je nutné zachovat aktualizovat na úroveň konkrétní verzi služby Azure Stack. Ujistěte se, abyste se seznámili [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md).

> [!NOTE]
> Balíčky aktualizací pro Azure Stack nelze použít pro Azure Stack Development Kit. Balíčky aktualizací jsou navržené pro integrované systémy. Informace najdete v tématu [opětovné nasazení ASDK](https://docs.microsoft.com/azure/azure-stack/asdk).

## <a name="the-update-resource-provider"></a>Aktualizace poskytovatele prostředků

Azure Stack zahrnuje poskytovatele prostředků aktualizace, která orchestruje použití aktualizací softwaru společnosti Microsoft. Tohoto poskytovatele prostředků zajistí, že se aktualizace napříč všechny fyzické hostitele, aplikace Service Fabric a moduly runtime a všechny virtuální počítače infrastruktury a jejich přidružené služby.

Jak nainstalovat aktualizace, můžete zobrazit informace na nejvyšší úrovni jako cíle procesu aktualizace různé subsystémy ve službě Azure Stack (například fyzické hostitele a infrastrukturu virtuálních počítačů).

## <a name="plan-for-updates"></a>Plán pro aktualizace

Důrazně doporučujeme, upozornění uživatelů na jakékoli operace údržby a, abyste naplánovali normální správu a údržbu během mimo pracovní dobu Pokud je to možné. Operace údržby může ovlivnit úlohy klientů a operací na portálu.

- Před instalací této aktualizace, spusťte [testovací AzureStack](azure-stack-diagnostic-test.md) s následujícími parametry do ověřte stav služby Azure Stack a vyřešte všechny provozní problémy zjištěné, včetně všech upozornění a chyby. Také aktivní výstrahy můžete zkontrolovat a vyřešit všechny, které vyžadují nějakou akci.  

  ```powershell
  Test-AzureStack -Group UpdateReadiness
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>Správa aktualizací pomocí aktualizovat dlaždici
Správa aktualizací z portálu správce. Jako operátory Azure stacku můžete aktualizovat dlaždici v řídicím panelu:

- zobrazit důležité informace, jako je aktuální verze.
- instalace aktualizací a sledovat průběh.
- Kontrola historie aktualizace pro dříve nainstalované aktualizace.
 
## <a name="determine-the-current-version"></a>Zjistit aktuální verzi

Aktualizace dlaždice zobrazí aktuální verzi služby Azure Stack. Chcete aktualizovat dlaždici můžete získat pomocí některé z následujících metod na portálu pro správce:

- Na řídicím panelu zobrazovat v aktuální verzi **aktualizace** dlaždici.
 
   ![Aktualizace dlaždice na výchozí řídicí panel](./media/azure-stack-updates/image1.png)
 
- Na **Správa oblastí** dlaždici, klikněte na název oblasti. Zobrazit aktuální verze **aktualizace** dlaždici.

## <a name="next-steps"></a>Další postup

- [Zásady údržby služby Azure Stack](azure-stack-servicing-policy.md) 
- [Správa oblastí ve službě Azure Stack](azure-stack-region-management.md)     


