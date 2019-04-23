---
title: Připojte se k virtuálnímu počítači s Windows serverem do Azure Active Directory Domain Services | Dokumentace Microsoftu
description: Připojte se k virtuálnímu počítači s Windows serverem do spravované domény pomocí šablon Azure Resource Manageru.
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 5b70a16d1da8f939a23bf7a96a03098463cfc529
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60417159"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Připojte se k virtuálnímu počítači s Windows serverem do spravované domény pomocí šablony Resource Manageru
V tomto článku se dozvíte, jak propojit virtuální počítače s Windows serverem do spravované domény služby Azure AD Domain Services pomocí šablon Resource Manageru.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Než začnete
K provádění úkolů uvedených v tomto článku, budete potřebovat:
1. Platný **předplatného Azure**.
2. **Adresář Azure AD** – buď synchronizaci s místním adresářem nebo výhradně cloudový adresář.
3. **Azure AD Domain Services** musí být povolené pro adresář Azure AD. Pokud jste neudělali, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).
4. Ujistěte se, že jste nakonfigurovali IP adres spravované domény jako servery DNS pro virtuální síť. Další informace najdete v tématu [postup aktualizace nastavení DNS pro virtuální síť Azure](active-directory-ds-getting-started-dns.md)
5. Dokončete kroky potřebné k [synchronizace hesel do spravované domény služby Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="install-and-configure-required-tools"></a>Instalace a konfigurace požadované nástroje
K provedení kroků uvedených v tomto dokumentu můžete použít kteroukoli z následujících možností:
* **Azure PowerShell**: [Instalace a konfigurace](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Azure CLI**: [Instalace a konfigurace](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Option 1: Zřízení nového virtuálního počítače Windows Server a připojte ho ke spravované doméně
**Název šablony rychlý start**: [201-vm-domain-join](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Nasazení virtuálního počítače s Windows serverem a připojte ho ke spravované doméně, proveďte následující kroky:
1. Přejděte [šablony rychlý start](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Klikněte na **Deploy to Azure** (Nasadit do Azure).
3. V **vlastní nasazení** zadejte požadované informace pro zřízení virtuálního počítače.
4. Vyberte **předplatného Azure** ve které chcete zřídit virtuální počítač. Můžete si vyberte jednoho předplatného Azure, ve kterém jste povolili službu Azure AD Domain Services.
5. Vybrat existující **skupiny prostředků** nebo vytvořte novou.
6. Vyberte **umístění** ve které chcete nasadit nový virtuální počítač.
7. V **existující název virtuální sítě**, zadejte virtuální síť, ve které jste nasadili vaší spravované doméně Azure AD Domain Services.
8. V **existující název podsítě**, zadejte podsíť ve virtuální síti, kde chcete nasadit tento virtuální počítač. Nevybírejte podsíť brány ve virtuální síti. Kromě toho nesmí být zvolen vyhrazenou podsíť, ve kterém je nasazený vaší spravované domény.
9. V **Popisek Předpona DNS**, zadejte název hostitele pro virtuální počítač zřizuje. Například "contoso-win".
10. Vyberte příslušné **velikost virtuálního počítače** pro virtuální počítač.
11. V **k připojení k doméně**, zadejte název domény DNS vaší spravované domény.
12. V **doména uživatelské jméno**, zadejte název uživatelského účtu ve vaší spravované doméně, který se má použít k připojení virtuálního počítače k spravované doméně.
13. V **heslo domény**, zadejte heslo uživatelského účtu domény, na které odkazuje parametr "domainUsername".
14. Volitelné: Můžete zadat **organizační jednotky cesta** do vlastní organizační jednotky, ve které chcete přidat virtuální počítač. Pokud žádnou hodnotu pro tento parametr nezadáte, virtuální počítač se přidá do výchozí **AAD DC počítače** OU ve spravované doméně.
15. V **uživatelské jméno správce virtuálního počítače** pole, zadejte název účtu místního správce pro virtuální počítač.
16. V **heslo správce virtuálního počítače** pole, zadejte heslo místního správce pro virtuální počítač. Zadejte heslo silné místního správce pro virtuální počítač k ochraně proti útokům na hesla hrubou silou.
17. Klikněte na tlačítko **vyjadřuji souhlas s podmínkami a ujednáními uvedenými nahoře**.
18. Klikněte na tlačítko **nákupní** ke zřízení virtuálního počítače.

> [!WARNING]
> **Zpracování hesel opatrně.**
> Soubor parametrů šablony obsahuje hesla pro doménové účty a hesla místního správce pro virtuální počítač. Ujistěte se, že nechcete tento soubor kolem ležící ve sdílených složkách nebo jiného sdíleného umístění. Doporučujeme, abyste vyřazení tohoto souboru po dokončení nasazení virtuálních počítačů.
>

Po úspěšném dokončení nasazení vaše nově zřízeného virtuálního počítače Windows je připojen ke spravované doméně.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Option 2: Připojení existujícího virtuálního počítače serveru systému Windows k spravované doméně
**Šablony rychlý start**: [201-vm-domain-join-existing](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Připojit existující virtuální počítač Windows Server ke spravované doméně, proveďte následující kroky:
1. Přejděte [šablony rychlý start](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Klikněte na **Deploy to Azure** (Nasadit do Azure).
3. V **vlastní nasazení** zadejte požadované informace pro zřízení virtuálního počítače.
4. Vyberte **předplatného Azure** ve které chcete zřídit virtuální počítač. Můžete si vyberte jednoho předplatného Azure, ve kterém jste povolili službu Azure AD Domain Services.
5. Vybrat existující **skupiny prostředků** nebo vytvořte novou.
6. Vyberte **umístění** ve které chcete nasadit nový virtuální počítač.
7. V **seznamu virtuálních počítačů** zadejte názvy stávajících virtuálních počítačů, který se má spojit do spravované domény. K oddělení jednotlivé názvy virtuálních počítačů použijte čárku. Například **web společnosti contoso, contoso-api**.
8. V **uživatelské jméno domény spojení**, zadejte název uživatelského účtu ve vaší spravované doméně, který se má použít k připojení virtuálního počítače k spravované doméně.
9. V **heslo uživatele domény připojit**, zadejte heslo uživatelského účtu domény, na které odkazuje parametr "domainUsername".
10. V **domény plně kvalifikovaný název domény**, zadejte název domény DNS vaší spravované domény.
11. Volitelné: Můžete zadat **organizační jednotky cesta** do vlastní organizační jednotky, ve které chcete přidat virtuální počítač. Pokud žádnou hodnotu pro tento parametr nezadáte, virtuální počítač se přidá do výchozí **AAD DC počítače** OU ve spravované doméně.
12. Klikněte na tlačítko **vyjadřuji souhlas s podmínkami a ujednáními uvedenými nahoře**.
13. Klikněte na tlačítko **nákupní** ke zřízení virtuálního počítače.

> [!WARNING]
> **Zpracování hesel opatrně.**
> Soubor parametrů šablony obsahuje hesla pro doménové účty a hesla místního správce pro virtuální počítač. Ujistěte se, že nechcete tento soubor kolem ležící ve sdílených složkách nebo jiného sdíleného umístění. Doporučujeme, abyste vyřazení tohoto souboru po dokončení nasazení virtuálních počítačů.
>

Po úspěšném dokončení nasazení zadaný virtuální počítače Windows jsou připojené ke spravované doméně.


## <a name="related-content"></a>Související obsah
* [Přehled Azure PowerShellu](/powershell/azure/overview)
* [Šablony rychlý start Azure – nový virtuální počítač připojit k doméně](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Šablony rychlý start Azure – stávající virtuální počítače připojit k doméně](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../azure-resource-manager/resource-group-template-deploy.md)
