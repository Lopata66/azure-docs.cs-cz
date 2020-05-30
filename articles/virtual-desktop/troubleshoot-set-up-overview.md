---
title: Přehled řešení potíží s virtuálním počítačem s Windows – Azure
description: Přehled řešení potíží při nastavování prostředí klienta virtuální plochy Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c5be26509eccdaebf1b504c1b0b8c7edb35e101c
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2020
ms.locfileid: "84203849"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Přehled řešení potíží, zpětná vazba a podpora

>[!IMPORTANT]
>Tento obsah se vztahuje na jarní 2020 aktualizaci s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows na verzi 2019 bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md).
>
> V současnosti je ve verzi Public Preview na jaře 2020 aktualizace virtuálních počítačů s Windows. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. 
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek poskytuje přehled o problémech, se kterými se můžete setkat při nastavování prostředí tenanta virtuálních počítačů s Windows, a poskytuje způsoby, jak tyto problémy vyřešit.

## <a name="report-issues-during-public-preview"></a>Nahlásit problémy během veřejné verze Preview

Pokud chcete ohlásit problémy nebo navrhnout funkce během veřejné verze Preview pro vydání jarní 2020, navštivte [technickou komunitu pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop). Pomocí odborné komunity můžete diskutovat na osvědčené postupy nebo navrhnout a hlasovat o nových funkcích. Když nahlásíte problém týkající se vydání verze Public Preview, nezapomeňte typ problému označit jako **jarní 2020 Update (Preview)**.

Když vytvoříte příspěvek s žádostí o nápovědu nebo navrhujete novou funkci, ujistěte se, že jste téma popsali co nejvíce podrobností. Podrobné informace mohou ostatním uživatelům pomáhat s zodpovězením vaší otázky nebo porozumět funkci, pro kterou navrhujete hlasovat.

## <a name="escalation-tracks"></a>Cvičení eskalace

Předtím, než provedete cokoli jiného, nezapomeňte zkontrolovat [stránku stavu Azure](https://status.azure.com/status) a [Azure Service Health](https://azure.microsoft.com/features/service-health/) , abyste se ujistili, že služba Azure funguje správně.

Pomocí následující tabulky můžete identifikovat a vyřešit problémy, se kterými se můžete setkat při nastavování prostředí klienta pomocí klienta vzdálené plochy. Po nastavení vašeho tenanta můžete využít naši novou [službu diagnostiky](diagnostics-role-service.md) k identifikaci problémů pro běžné scénáře.

| **Chybu**                                                            | **Navrhované řešení**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Přístup k šablonám Marketplace v Azure Portal       | Azure Marketplace šablony virtuálních klientů Windows jsou volně dostupné.|
| Fond hostitelů relace – Azure Virtual Network (VNET) a nastavení expresní trasy               | [Otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/)a pak vyberte příslušnou službu (v kategorii síť). |
| Vytvoření virtuálního počítače fondu hostitelů relace, když se nepoužívají šablony Azure Resource Manager s virtuálním počítačem s Windows | [Otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/)a pak pro tuto službu vyberte **virtuální počítač s Windows** . <br> <br> Problémy s Azure Resource Manager šablonami, které jsou k dispozici s virtuálním počítačem s Windows, najdete v části Vytvoření tenanta virtuálních počítačů s Windows v tématu [prostředí a vytvoření fondu hostitelů](troubleshoot-set-up-issues.md). |
| Správa prostředí hostitele relace virtuálních počítačů s Windows z Azure Portal    | [Otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Problémy se správou při používání PowerShellu služby Vzdálená plocha/prostředí Windows Virtual Desktop PowerShell najdete v tématu virtuální plocha [Windows PowerShell](troubleshoot-powershell.md) nebo [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/), vyberte **virtuální plochu Windows** pro tuto službu, vyberte **Konfigurace a Správa** pro typ problému a pak vyberte **problémy s konfigurací tenanta pomocí PowerShellu** pro problémový podtyp. |
| Správa konfigurace virtuálních klientských počítačů s Windows vázaných na fondy hostitelů a skupiny aplikací (skupiny aplikací)      | Podívejte se na téma [PowerShell virtuální plochy Windows](troubleshoot-powershell.md)nebo [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/), vyberte **virtuální plochu Windows** pro tuto službu a potom vyberte vhodný typ problému.|
| Nasazení a Správa kontejnerů profilů FSLogix | Přečtěte si [příručku Poradce při potížích s produkty FSLogix](/fslogix/fslogix-trouble-shooting-ht/) . Pokud se problém nevyřeší, [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/), vyberte **virtuální plochu Windows** pro tuto službu, pro typ problému vyberte **FSLogix** a pak vyberte příslušný podtyp problému. |
| Selhání klientů vzdálené plochy při spuštění                                                 | Podívejte [se na téma Poradce při potížích s klientem vzdálené plochy](troubleshoot-client.md) a v případě, že problém nevyřeší, [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/), vyberte **virtuální plochu Windows** pro tuto službu a pak jako typ problému vyberte **Klienti vzdálené plochy** .  <br> <br> Pokud se jedná o problém v síti, uživatelé musí kontaktovat svého správce sítě. |
| Připojeno, ale bez kanálu                                                                 | Řešení potíží pomocí nástroje se [připojuje, ale nic se nezobrazuje (bez informačního kanálu)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) v [připojeních služby Virtual Desktop systému Windows](troubleshoot-service-connection.md). <br> <br> Pokud byli vaši uživatelé přiřazeni ke skupině aplikací, [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/), vyberte **virtuální plochu Windows** pro tuto službu a pak jako typ problému vyberte **Klienti vzdálené plochy** . |
| Podávat potíže se zjišťováním v důsledku sítě                                            | Uživatelé musí kontaktovat svého správce sítě. |
| Připojování klientů                                                                    | Viz [připojení k virtuálním počítačům s Windows](troubleshoot-service-connection.md) a pokud to problém nevyřeší, přečtěte si téma [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md). |
| Reakce vzdálených aplikací nebo plochy                                      | Pokud jsou problémy svázané s konkrétní aplikací nebo produktem, obraťte se na tým, který je zodpovědný za daný produkt. |
| Licenční zprávy a chyby                                                          | Pokud jsou problémy svázané s konkrétní aplikací nebo produktem, obraťte se na tým, který je zodpovědný za daný produkt. |
| Problémy s metodami ověřování třetích stran | Ověřte, jestli váš poskytovatel třetí strany podporuje scénáře virtuálních počítačů s Windows, a přiblíží se k nim ohledně známých problémů. |
| Problémy s používáním Log Analytics pro virtuální počítače s Windows | V případě problémů se schématem diagnostiky [otevřete žádost o podporu Azure](https://azure.microsoft.com/support/create-ticket/).<br><br>Pro dotazy, vizualizaci nebo jiné problémy v Log Analytics v části Log Analytics vyberte příslušný typ problému. |
| Problémy s používáním aplikací M365 | Obraťte se na centrum pro správu M365 s jedním z [možností pomocníka centra pro správu M365](/microsoft-365/admin/contact-support-for-business-products/). |

## <a name="next-steps"></a>Další kroky

- Informace o řešení potíží při vytváření fondu hostitelů v prostředí virtuálních ploch Windows najdete v tématu [Vytvoření fondu prostředí a hostitele](troubleshoot-set-up-issues.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače na virtuálním počítači s Windows najdete v tématu [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md).
- Informace o řešení potíží s klientskými připojeními k virtuální ploše Windows najdete v tématu [připojení ke službě Virtual Desktop systému Windows](troubleshoot-service-connection.md).
- Řešení potíží s klienty vzdálené plochy najdete v tématu [řešení potíží s klientem vzdálené plochy](troubleshoot-client.md) .
- Pokud chcete řešit problémy při používání PowerShellu s virtuálním počítačem s Windows, přečtěte si téma [virtuální plocha Windows PowerShell](troubleshoot-powershell.md).
- Další informace o této službě najdete v tématu [prostředí virtuálních počítačů s Windows](environment-setup.md).
- Kurz řešení potíží najdete v tématu [kurz: řešení potíží s nasazením správce prostředků šablon](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Další informace o akcích auditování najdete v tématu věnovaném [operacím auditu správce prostředků](../azure-resource-manager/management/view-activity-logs.md).
- Další informace o akcích k určení chyb během nasazení najdete v tématu [Zobrazení operací nasazení](../azure-resource-manager/templates/deployment-history.md).
