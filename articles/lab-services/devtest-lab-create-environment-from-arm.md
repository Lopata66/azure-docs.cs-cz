---
title: Vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru | Dokumentace Microsoftu
description: Informace o vytvoření prostředí více virtuálních počítačů a prostředků PaaS ve službě Azure DevTest Labs ze šablony Azure Resource Manageru
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: spelluru
ms.openlocfilehash: 4dc61258df2311c4e7ccd4c05ebe077b3e2343eb
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222280"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru

[Webu Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) vám umožní snadno [přidat jeden virtuální počítač současně do testovacího prostředí](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-add-vm). Nicméně pokud prostředí obsahuje několik virtuálních počítačů, každý virtuální počítač musí být jednotlivě vytvořeny. Pro scénáře, jako je například vícevrstvou webovou aplikaci nebo farmu služby SharePoint mechanismus je potřeba povolit pro vytvoření několika virtuálních počítačů v jednom kroku. Pomocí šablon Azure Resource Manageru můžete definovat infrastrukturu a konfiguraci vašeho řešení Azure a opakovaně nasazovat více virtuálních počítačů v konzistentním stavu. Tato funkce poskytuje následující výhody:

- Šablony Azure Resource Manageru jsou načítány přímo ze svým úložištěm řízení zdrojů (GitHub nebo Azure DevOps služby Git).
- Po nakonfigurování mohou uživatelé vytvářet prostředí díky šablonu Azure Resource Manageru z portálu Azure portal, stejně jako u jiných typů [bází virtuálních počítačů](./devtest-lab-comparing-vm-base-image-types.md).
- V prostředí ze šablony Azure Resource Manageru kromě virtuální počítače IaaS se dá zřídit prostředky Azure PaaS.
- Náklady na prostředí můžete sledovat v testovacím prostředí kromě jednotlivé virtuální počítače vytvořené pomocí jiných typů základních tříd.
- Prostředky PaaS se vytvoří a zobrazí se v sledování; nákladů ale automatické vypnutí virtuálního počítače se nevztahuje k prostředkům PaaS.

Další informace o dalších [výhody pomocí šablon Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager) Pokud chcete nasadit, aktualizovat nebo odstranit všechny prostředky testovacího prostředí v rámci jedné operace.

> [!NOTE]
> Když použijete šablonu Resource Manageru jako základ k vytvoření testovacího prostředí více virtuálních počítačů, existují určité rozdíly brát v úvahu, ať už vytváříte více virtuálních počítačů nebo jedním virtuálním počítačům. [Pomocí šablony Azure Resource Manageru pro virtuální počítač](devtest-lab-use-resource-manager-template.md) vysvětluje tyto rozdíly podrobněji.
>

## <a name="devtest-labs-public-environments"></a>Veřejná prostředí DevTest Labs
Azure DevTest Labs má [veřejném úložišti šablon Azure Resource Manageru](https://github.com/Azure/azure-devtestlab/tree/master/Environments) , můžete použít k vytvoření prostředí bez nutnosti připojení k externímu zdroji Githubu sami. Toto úložiště obsahuje často používané šablony, jako je Azure Web Apps, Service Fabric Cluster a vývojové prostředí farmy služby SharePoint. Tato funkce je podobný veřejné úložiště artefaktů, který je součástí pro každý testovací prostředí, který vytvoříte. Úložiště prostředí umožňuje rychle začít s předem vytvořené prostředí šablony s minimální vstupní parametry, kde přinášejí smooth úvodní prostředí pro PaaS prostředky v rámci testovacích prostředí. Další informace najdete v tématu [konfigurace a použití veřejného prostředí v DevTest Labs](devtest-lab-configure-use-public-environments.md).

## <a name="configure-your-own-template-repositories"></a>Konfigurace vlastní šablony úložiště
Šablony prostředí mají spravovat jako jeden z osvědčených postupů pomocí infrastruktury jako kódu a konfigurace jako kódu ve správě zdrojového kódu. Azure DevTest Labs následuje tento postup a načte všechny šablony Azure Resource Manageru přímo z Githubu nebo služby Git v Azure DevOps úložišť. V důsledku toho je možné šablon Resource Manageru v cyklu kompletního postupu vydávání verzí, z testovacího prostředí do produkčního prostředí.

Podívejte se na šablony vytvořené týmem DevTest Labs v [veřejného úložiště GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Environments). V této veřejné úložiště můžete zobrazit šablony, které sdílí ostatní, můžete použít přímo nebo přizpůsobit je tak, aby odpovídala vašim potřebám. Jakmile vytvoříte šablonu, uložte ho v tomto úložišti můžete sdílet s ostatními. Můžete také nastavit vlastní úložiště Git se šablonami, které je možné nastavit prostředí v cloudu. 

Existuje několik pravidel pro uspořádání vašich šablon Azure Resource Manageru v úložišti:

- Soubor hlavní šablony musí mít název `azuredeploy.json`. 

    ![Soubory šablon klíč Azure Resource Manageru](./media/devtest-lab-create-environment-from-arm/master-template.png)

- Pokud chcete použít hodnoty parametrů definované v souboru parametrů, musí mít název souboru parametrů `azuredeploy.parameters.json`.
- Při použití parametrů `_artifactsLocation` a `_artifactsLocationSasToken` vytvořit hodnotu identifikátoru URI parametersLink, a umožnil DevTest Labs k automatické správě vnořené šablony. Další informace najdete v tématu [jak službě Azure DevTest Labs usnadňuje vnořené Resource Manageru šablony nasazení pro testovací prostředí](https://blogs.msdn.microsoft.com/devtestlab/2017/05/23/how-azure-devtest-labs-makes-nested-arm-template-deployments-easier-for-testing-environments/).
- Metadata lze definovat za účelem zadejte zobrazovaný název šablony a popis. Tato metadata musí být v souboru s názvem `metadata.json`. Soubor metadat následující příklad ukazuje, jak zadat zobrazovaný název a popis: 

    ```json
    { 
        "itemDisplayName": "<your template name>", 
        "description": "<description of the template>" 
    }
    ```

Následující kroky vás provedou přidání úložiště do testovacího prostředí pomocí webu Azure portal. 

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
1. V seznamu testovacích prostředí vyberte požadované prostředí.   
1. Cvičení **přehled** vyberte **konfigurace a zásad**.

    ![Konfigurace a zásady](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Z **konfigurace a zásad** seznam nastavení, vyberte **úložišť**. **Úložišť** podokně zobrazí seznam úložišť, které byly přidány do testovacího prostředí. Úložiště s názvem `Public Repo` není automaticky vygenerován pro všechny testovací prostředí a připojí k [úložiště DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab) , která obsahuje několik artefaktů virtuálního počítače pro použití.

    ![Veřejné úložiště](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Vyberte **přidat +** přidat úložišti šablon Azure Resource Manageru.
1. Při druhém **úložišť** otevře se podokno, následujícím způsobem zadejte informace potřebné:
    - **Název** – zadejte název úložiště, který se používá v testovacím prostředí.
    - **Adresa URL klonu Git** – zadejte adresu URL klonu GIT HTTPS z Githubu nebo služby Azure DevOps.  
    - **Větev** – zadejte název větve pro přístup k vaše definice šablon Azure Resource Manageru. 
    - **Token pat** – osobní přístupový token se používá zabezpečený přístup k úložišti. Chcete-li získat token ze služby Azure DevOps, vyberte  **&lt;Vaše_jméno >> Můj profil > zabezpečení > token veřejného přístupu**. K získání tokenu z Githubu, vyberte svou miniaturu a potom výběrem **Nastavení > token veřejného přístupu**. 
    - **Cesty ke složkám** – jedním ze dvou vstupních polí, zadejte cestu ke složce, která začíná lomítkem - / – a je relativní vzhledem k váš identifikátor URI klonu Gitu buď definice artefaktů (první vstupní pole) nebo vaše definice šablon Azure Resource Manageru .   
    
        ![Veřejné úložiště](./media/devtest-lab-create-environment-from-arm/repo-values.png)


1. Jakmile všechna povinná pole jsou zadány a předat ověření, vyberte **Uložit**.

Následující části vás provede procesem vytvoření prostředí ze šablony Azure Resource Manageru.

## <a name="create-an-environment-from-a-resource-manager-template-using-the-azure-portal"></a>Vytvoření prostředí ze šablony Resource Manageru pomocí webu Azure portal

Jakmile je úložiště na šablony Azure Resource Manageru není nakonfigurovaná v testovacím prostředí, můžete uživatelům testovacího prostředí vytvořit prostředí pomocí webu Azure portal následujícím postupem:

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
1. V seznamu testovacích prostředí vyberte požadované prostředí.   
1. V podokně testovacího prostředí, vyberte **přidat +**.
1. **Vyberte bázi** podokno zobrazuje základní Image, můžete použít s šablonami Azure Resource Manageru uvedená jako první. Vyberte požadovanou šablonu Azure Resource Manageru.

    ![Výběr základní image](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. Na **přidat** podokně, zadejte **název prostředí** hodnotu. Název prostředí je, co se zobrazí uživatelům v testovacím prostředí. Zbývající vstupní pole jsou definována v šabloně Azure Resource Manageru. Pokud výchozí hodnoty jsou definovány v šabloně nebo `azuredeploy.parameter.json` soubor je k dispozici, výchozí hodnoty jsou zobrazeny v tyto vstupní pole. Pro parametry typu *zabezpečený řetězec*, můžete použít k tajným klíčům uloženým v trezoru klíčů Azure. Další informace o ukládání tajných klíčů v trezoru klíčů a jejich používání při vytváření prostředků testovacího prostředí, najdete v článku [Store tajné kódy ve službě Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).  

    ![Přidání podokna](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > Existuje několik hodnot parametrů, které – i v případě, že je zadaný – zobrazují jako prázdné hodnoty. Proto pokud uživatelé přiřazovat tyto hodnoty parametrů v šabloně Azure Resource Manageru, DevTest Labs nezobrazí hodnoty. Prázdný vstupní pole místo toho se zobrazí, kde uživatelé testovacího prostředí musíte zadat hodnotu, při vytváření prostředí.
    > 
    > - OBECNÉ JEDINEČNÝ
    > - -JEDINEČNÉ - GEN [N]
    > - OBECNÉ SSH-PUB-KEY
    > - OBECNÉ – HESLO 
 
1. Vyberte **přidat** vytvořte prostředí. Prostředí spustí zřizování okamžitě v zobrazení stavu v **Moje virtual machines** seznamu. Podle testovacího prostředí a zřiďte všechny prostředky definované v šabloně Azure Resource Manageru se automaticky vytvoří novou skupinu prostředků.
1. Po vytvoření prostředí, vyberte prostředí v **Moje virtual machines** seznamu otevřete podokno skupiny prostředků a procházet všechny prostředky, které jsou zřízené v prostředí.
    
    ![Můj seznam virtuálních počítačů](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   Můžete také rozšířit prostředí a zobrazit jenom seznam virtuálních počítačů, které jsou zřízené v prostředí.
    
    ![Můj seznam virtuálních počítačů](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Získáte po kliknutí na prostředí zobrazíte dostupné akce – třeba použít artefakty, připojit datové disky, změnu čas automatického vypnutí a další.

    ![Akce prostředí](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="deploy-a-resource-manager-template-to-create-a-vm"></a>Nasazení šablony Resource Manageru k vytvoření virtuálního počítače
Po uložení šablony Resource Manageru a přizpůsobené vašim potřebám, můžete k automatizovanému vytvoření virtuálního počítače. 
- [Nasazení prostředků pomocí šablon Resource Manageru a prostředí Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) popisuje, jak nasadit prostředky do Azure pomocí šablon Resource Manageru pomocí prostředí Azure PowerShell. 
- [Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) popisuje, jak nasadit prostředky do Azure pomocí rozhraní příkazového řádku Azure pomocí šablon Resource Manageru.

> [!NOTE]
> Pouze uživatel s oprávněními vlastníka testovacího prostředí můžete vytvořit virtuální počítače ze šablony Resource Manageru pomocí prostředí Azure PowerShell. Pokud chcete automatizovat vytvoření virtuálního počítače pomocí šablony Resource Manageru a máte oprávnění pro uživatele, můžete použít [ **az lab vm vytvořit** v rozhraní příkazového řádku příkaz](https://docs.microsoft.com/cli/azure/lab/vm#az-lab-vm-create).

### <a name="general-limitations"></a>Obecná omezení 

Při použití šablony Resource Manageru ve službě DevTest Labs vezměte v úvahu tato omezení:

- Všechny šablony Resource Manageru, které vytvoříte nemůže odkazovat na existující prostředky; mohou odkazovat pouze na nové prostředky. Kromě toho pokud máte existující šablonu Resource Manageru, které obvykle nasadit mimo DevTest Labs a obsahuje odkazy na stávající prostředky, ho nelze použít v testovacím prostředí.

   Jedinou výjimkou je, že jste **můžete** odkazovat na existující virtuální sítě. 

- Vzorce nelze vytvořit z testovacího prostředí virtuálních počítačů, které byly vytvořeny pomocí šablony Resource Manageru. 

- Nelze vytvořit vlastní Image z testovacího prostředí virtuálních počítačů, které byly vytvořeny pomocí šablony Resource Manageru. 

- Většina zásad se nevyhodnocují při nasazování šablony Resource Manageru.

   Například můžete mít testovacího prostředí zásady určující, uživatel může vytvořit pouze pět virtuálních počítačů. Uživatel však můžete nasadit šablonu Resource Manageru, která vytvoří desítek virtuálních počítačů. Zásady, které se nevyhodnotily patří:

   - Počet virtuálních počítačů na uživatele
   - Počet virtuálních počítačů služby premium za uživatele testovacího prostředí.
   - Počet disků premium uživatele testovacího prostředí.


### <a name="configure-environment-resource-group-access-rights-for-lab-users"></a>Konfigurace prostředí prostředků skupiny přístupová práva uživatelů testovacího prostředí

Uživatelé testovacího prostředí můžete nasadit šablonu Resource Manageru. Ale standardně mají přístupová práva čtečky, což znamená, že nemohou měnit prostředky ve skupině prostředků prostředí. Například nelze zastavit nebo spustit jejich prostředků.

Použijte následující postup měl Přispěvatel přístupová práva uživatelům testovacího prostředí. Když nasadí šablony Resource Manageru, budou moct upravovat prostředky v daném prostředí. 


1. Na vašem testovacím prostředí **přehled** vyberte **konfigurace a zásad**.
1. Vyberte **nastavení testovacího prostředí**.
1. V podokně nastavení testovacího prostředí, vyberte **Přispěvatel** udělit oprávnění k zápisu uživatelům testovacího prostředí.

    ![Nakonfigurovat přístupová práva uživatelů testovacího prostředí](./media/devtest-lab-create-environment-from-arm/configure-access-rights.png)

1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další postup
* Po vytvoření virtuálního počítače může připojit k virtuálnímu počítači tak, že vyberete **připojit** v podokně Správa Virtuálního počítače.
* Umožňuje zobrazit a spravovat prostředky v prostředí tak, že vyberete prostředí **Moje virtual machines** seznamu ve vaší laboratoři. 
* Prozkoumejte [šablon Azure Resource Manageru z Galerie šablon Azure Quickstart](https://github.com/Azure/azure-quickstart-templates).
