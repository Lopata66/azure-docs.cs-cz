---
title: Řešení potíží s odchozím připojením SMTP v Azure | Microsoft Docs
description: Naučte se řešit potíže s odchozím připojením SMTP v Azure.
services: virtual-network
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 64cf633d50fc81ae8d53f2b4ee2a9975a756f0c7
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972477"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Řešení potíží s odchozím připojením SMTP v Azure

Od 15. listopadu 2017 se odchozí e-mailové zprávy, které se odesílají přímo do externích domén (například outlook.com a gmail.com) z virtuálního počítače, zpřístupňují jenom pro určité typy předplatného v Microsoft Azure. Odchozí připojení SMTP, která používají port TCP 25, byla zablokována. (Port 25 se primárně používá pro neověřené doručování e-mailů.)

Tato změna chování se týká jenom nových předplatných a nových nasazení od 15. listopadu 2017.

## <a name="recommended-method-of-sending-email"></a>Doporučený způsob odesílání e-mailů
Pro posílání e-mailů z virtuálních počítačů Azure nebo z Azure App Services doporučujeme použít ověřené služby SMTP relay (které se obvykle připojují prostřednictvím portu TCP 587 nebo 443, ale podporují jiné porty). Tyto služby se používají k údržbě IP nebo pověsti domény, aby se minimalizovala možnost, kterou poskytovatelé e-mailů od jiných výrobců odmítnou zprávu. Takové služby SMTP relay zahrnují, ale nejsou omezené na [SendGrid](https://sendgrid.com/partners/azure/). Je také možné, že máte zabezpečenou službu SMTP relay, která běží místně, kterou můžete použít.

Použití těchto služeb doručování e-mailů není v Azure omezené bez ohledu na typ předplatného.

## <a name="enterprise-agreement"></a>Smlouva Enterprise
U smlouva Enterprise uživatelů Azure se nemění technická schopnost posílat e-maily bez použití ověřeného přenosu. Noví i stávající smlouva Enterprise uživatelé můžou zkusit odchozí e-mailové doručování z virtuálních počítačů Azure přímo externím poskytovatelům e-mailu bez jakýchkoli omezení platformy Azure. I když není zaručeno, že poskytovatelé e-mailů budou přijímat příchozí e-maily od kteréhokoli daného uživatele, nebudou pro virtuální počítače v rámci předplatných smlouva Enterprise zablokovány žádné pokusy o doručení. Budete muset pracovat přímo s poskytovateli e-mailu, aby opravili problémy s doručováním zpráv nebo filtrování SPAMu, které zahrnují konkrétní poskytovatele.

## <a name="pay-as-you-go"></a>Průběžné platby
Pokud jste se zaregistrovali do 15. listopadu 2017 pro nabídky předplatného s průběžnými platbami nebo Microsoft Partner Network, nebudete moct při pokusu o doručení odchozího e-mailu měnit technickou schopnost. V rámci těchto předplatných se budete moci pokusit odchozí e-maily z virtuálních počítačů Azure přímo k externím poskytovatelům e-mailů bez jakýchkoli omezení platformy Azure. Nezaručujeme, že poskytovatelé e-mailů budou přijímat příchozí e-maily od kteréhokoli daného uživatele a uživatelé budou muset pracovat přímo s poskytovateli e-mailu, aby opravili problémy s doručováním zpráv nebo filtrováním nevyžádané pošty, které zahrnují konkrétní poskytovatele

U předplatných s průběžnými platbami nebo Microsoft Partner Network předplatných vytvořených po 15. listopadu 2017 budou k dispozici technická omezení, která blokují e-mail, který se odesílá přímo z virtuálních počítačů v rámci těchto předplatných. Pokud chcete mít možnost odesílat e-maily z virtuálních počítačů Azure přímo externím poskytovatelům e-mailu (bez použití ověřovaného přenosu SMTP), můžete vytvořit žádost o odebrání tohoto omezení. Žádosti budou přezkoumány a schváleny na základě rozhodnutí společnosti Microsoft a budou uděleny až po provedení dalších kontrol boje proti podvodům. Pokud chcete vytvořit žádost, otevřete případ podpory pomocí následujícího typu problému: **Technické** > **Virtual Network**připojenínemůže > odesílat e-maily (SMTP/port 25). >  Ujistěte se, že jste přidali podrobnosti o tom, proč musí vaše nasazení odesílat e-maily přímo poskytovatelům pošty namísto použití ověřeného přenosu.

Po vyloučení nebo Microsoft Partner Network předplatného s průběžnými platbami a u virtuálních počítačů, u kterých je & spuštěný, se z Azure Portal vyloučí všechny virtuální počítače v tomto předplatném, budou předány dál. Výjimka se vztahuje pouze na požadované předplatné a vztahuje se pouze na provoz virtuálního počítače směrovaný přímo na Internet. Směrování portu 25 přenosů prostřednictvím služeb Azure PaaS, jako je [Azure firewall](https://azure.microsoft.com/services/azure-firewall/) , se nepodporuje.

> [!NOTE]
> Společnost Microsoft si vyhrazuje právo tuto výjimku odvolat, pokud se zjistí, že došlo k porušení podmínek služby.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Systém Azure v rámci licenčního programu Open, vzdělávání, BizSpark a bezplatná zkušební verze
Pokud jste vytvořili MSDN, Azure Pass, Systém Azure v rámci licenčního programu Open, vzdělávání, BizSpark, Azure Sponsorship, Azure student, bezplatnou zkušební verzi nebo jakékoli předplatné sady Visual Studio po 15. listopadu 2017, budete mít technická omezení, která blokují e-mail odeslaný z virtuálních počítačů v rámci těchto odběry přímo poskytovatelům e-mailu. Omezení jsou prováděna za účelem zabránění zneužití. Nebudou uděleny žádné žádosti o odebrání tohoto omezení.

Pokud používáte tyto typy předplatného, doporučujeme používat předávací služby SMTP, jak je uvedeno výše v tomto článku, nebo změnit typ předplatného.

## <a name="cloud-service-provider-csp"></a>Poskytovatel cloudových služeb (CSP)

Pokud používáte prostředky Azure prostřednictvím CSP, můžete požádat poskytovatele CSP o vytvoření žádosti o výjimku odblokování u Microsoftu vaším jménem, pokud nelze použít zabezpečený přenos SMTP.

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktujte podporu

Pokud stále potřebujete pomoc, obraťte se na [podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , abyste mohli rychle vyřešit problém pomocí následujícího typu problému: **Správa** předplatných Typ problému: **Požadavek na povolení toku e-mailu portu 25**
