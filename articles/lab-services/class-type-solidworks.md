---
title: Nastavení testovacího prostředí SolidWorks pro inženýry s Azure Lab Services | Microsoft Docs
description: Naučte se, jak nastavit testovací prostředí pro technické kurzy pomocí SolidWorks.
services: lab-services
author: nicolela
ms.service: lab-services
ms.topic: article
ms.date: 06/03/2020
ms.author: nicolela
ms.openlocfilehash: ee2f1636547378102f35b432914426b3e24d92ef
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84898987"
---
# <a name="set-up-a-lab-for-engineering-classes-using-solidworks"></a>Nastavení testovacího prostředí pro inženýrské třídy pomocí SolidWorks

[SolidWorks](https://www.solidworks.com/) poskytuje prostředí CAD pro vytváření modelů pro objekty Solid a používá se v různých inženýrských polích.  Pomocí SolidWorks můžou technici snadno vytvářet, vizualizovat, simulovat a zdokumentovat jejich návrhy.

Možnost licencování běžně používaná vysokými školami je SolidWorks "licencování sítě.   Tato možnost uživatelům sdílí fond licencí, který je spravovaný licenčním serverem.  Tento typ licence se někdy označuje jako "plovoucí" licence, protože potřebujete mít jenom dostatek licencí pro počet souběžných uživatelů.  Když se uživatel s použitím SolidWorks, vrátí se mu licence zpátky do fondu spravované licence, aby ho mohl znovu použít jiný uživatel.

V tomto článku si ukážeme, jak nastavit třídu, která používá SolidWorks 2019 a síťové licencování.

## <a name="license-server"></a>Licenční server

Licencování sítě SolidWorks vyžaduje, abyste na licenčním serveru nainstalovali a aktivovali SolidNetWork License Manager.  Tento licenční server se obvykle nachází v místní síti nebo v privátní síti v rámci Azure.  Další informace o tom, jak nastavit správce licencí SolidNetWork na vašem serveru, najdete v tématu [instalace a aktivace správce licencí](https://help.solidworks.com/2019/English/Installation/install_guide/t_installing_snl_lic_mgr.htm) v příručce pro instalaci SOLIDWORKS.  Při nastavování si zapamatujte **číslo portu** a [**sériové číslo**](https://help.solidworks.com/2019/english/installation/install_guide/r_hid_state_serial_number.htm) , které se použijí, protože budou potřeba v pozdějších krocích.

Po nastavení licenčního serveru budete muset vytvořit partnerský vztah k [virtuální síti (VNET)](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) na [účet testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account).  Partnerský vztah k síti je třeba provést před vytvořením testovacího prostředí, aby virtuální počítače testovacího prostředí měly přístup k licenčnímu serveru a naopak.

> [!NOTE]
> Měli byste ověřit, jestli jsou na branách firewall otevřené příslušné porty, abyste umožnili komunikaci mezi virtuálními počítači testovacího prostředí a licenčním serverem.  Podívejte se například na pokyny k [úpravám portů počítače správce licencí pro bránu Windows Firewall](http://help.solidworks.com/2019/english/installation/install_guide/t_mod_ports_on_lic_mgr_for_firewall.htm) , které ukazují, jak přidat pravidla pro příchozí a odchozí spojení do brány firewall licenčního serveru.  Je také možné, že budete muset otevřít porty pro virtuální počítače testovacího prostředí.  Další informace o tom, jak získat veřejnou IP adresu testovacího prostředí, najdete v článku o [nastavení brány firewall pro laboratoře](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-firewall-settings) .

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí

K nastavení tohoto testovacího prostředí potřebujete předplatné Azure a účet testovacího prostředí, abyste mohli začít. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/), ještě než začnete. Po získání předplatného Azure můžete vytvořit nový účet testovacího prostředí v Azure Lab Services. Další informace o vytvoření nového účtu testovacího prostředí najdete v kurzu [jak nastavit účet testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account). Můžete použít i existující účet testovacího prostředí.

### <a name="lab-account-settings"></a>Nastavení účtu testovacího prostředí

Pro účet testovacího prostředí povolte nastavení popsaná v následující tabulce. Další informace o tom, jak povolit image Marketplace, najdete v článku o [tom, jak zadat image z Marketplace dostupné pro tvůrce testovacích prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

| Nastavení účtu testovacího prostředí | Pokyny |
| ------------------- | ------------ |
|Obrázek Marketplace| Povolte image Windows 10 pro pro použití v rámci vašeho účtu testovacího prostředí.|

> [!NOTE]
> Kromě Windows 10 podporuje SolidWorks i další verze Windows.  Podrobnosti najdete v tématu [požadavky na systém SolidWorks](https://www.solidworks.com/sw/support/SystemRequirements.html) .

### <a name="lab-settings"></a>Nastavení testovacího prostředí

Při nastavování testovacího prostředí učebny použijte nastavení v následující tabulce. Další informace o tom, jak vytvořit prostředí učebny, najdete v tématu Nastavení kurzu pro prostředí učebny.

| Nastavení testovacího prostředí | Hodnota/pokyny |
| ------------ | ------------------ |
|Velikost virtuálního počítače| **Malý grafický procesor (vizualizace)**.  Tento virtuální počítač je vhodný pro vzdálenou vizualizaci, streamování, hraní a kódování pomocí platforem, jako je OpenGL a DirectX.|  
|Image virtuálního počítače| Windows 10 Pro|

> [!NOTE]
> Velikost virtuálního počítače **malého GPU (vizualizace)** je nakonfigurovaná tak, aby umožňovala vysoce náročné grafické prostředí.  Další informace o této velikosti virtuálního počítače najdete v článku o [Nastavení testovacího prostředí pomocí GPU](./how-to-setup-lab-gpu.md).

> [!WARNING]
> **Před** vytvořením testovacího prostředí nezapomeňte [virtuální síť](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) pro účet testovacího prostředí na virtuální síť pro daný licenční server.

## <a name="template-virtual-machine-configuration"></a>Konfigurace virtuálního počítače šablony

Kroky v této části ukazují, jak nastavit virtuální počítač šablony stažením instalačních souborů SolidWorks a instalací klientského softwaru:

1. Spusťte virtuální počítač šablony a připojte se k počítači pomocí protokolu RDP.

1. Stáhněte si instalační soubory pro klientský software SolidWorks. Máte dvě možnosti, jak si stáhnout:
   - Stáhněte si z [portálu SolidWorks Customer](https://login.solidworks.com/nidp/idff/sso?id=cpenglish&sid=1&option=credential&sid=1&target=https%3A%2F%2Fcustomerportal.solidworks.com%2F).
   - Stažení z adresáře na serveru.  Pokud jste použili tuto možnost, musíte zajistit, aby byl server přístupný z virtuálního počítače šablony.  Například tento server se může nacházet ve stejné virtuální síti, která je v partnerském vztahu s vaším účtem testovacího prostředí.
  
    Podrobnosti najdete v tématu [instalace na jednotlivých počítačích v SolidWorks](http://help.solidworks.com/2019/english/Installation/install_guide/c_installing_on_individual_computers.htm?id=fc149e8a968a422a89e2a943265758d3#Pg0) v příručce pro instalaci SOLIDWORKS.

1. Po stažení instalačních souborů nainstalujte klientský software pomocí Správce instalace SolidWorks. Přečtěte si podrobnosti o [instalaci klienta licencí](http://help.solidworks.com/2019/english/installation/install_guide/t_installing_snl_license_client.htm) v instalační příručce SOLIDWORKS.

    > [!NOTE]
    > V dialogovém okně **Přidat server** se zobrazí výzva k zadání **čísla portu** používaného pro váš licenční server a názvu nebo IP adresy licenčního serveru.

1. Po dokončení instalace klientského softwaru musíte taky povolit, aby se grafický procesor používal přes připojení RDP. Další informace najdete v článku o [tom, jak povolit připojení GPU přes připojení RDP](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms) .

## <a name="cost"></a>Náklady

Pojďme pro tuto třídu pokrýt možné náklady. Tento odhad nezahrnuje náklady na provozování licenčního serveru. Použijeme třídu 25 studentů. Naplánovaný čas třídy je 20 hodin. Každý student navíc získá kvótu 10 hodin pro domácí nebo přiřazení mimo plánovanou dobu třídy. Velikost virtuálního počítače, kterou jsme zvolili, byla **malá GPU (vizualizace)**, což je 160 jednotek testovacího prostředí.

25 studentů \* (20 naplánovaných hodin + 10 hodin kvóty) \* 160 jednotek testovacího prostředí × 0,01 USD za hodinu = 1200,00 USD

>[!IMPORTANT]
> Odhad nákladů slouží pouze jako příklad pro účely.  Aktuální podrobnosti o cenách najdete v tématu [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Další kroky

Další kroky jsou běžné pro nastavení testovacího prostředí.

- [Vytvoření a Správa šablony](how-to-create-manage-template.md)
- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users)
