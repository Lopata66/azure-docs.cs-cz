---
title: Použít soukromé balíčky Pythonu
titleSuffix: Azure Machine Learning
description: Z Azure Machine Learningch prostředí se bezpečně přistupuje k soukromým balíčkům Pythonu.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: laobri
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 580525b2e8e408949ce1d8f2d1b8241c431fc755
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209366"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>Použití privátních balíčků Pythonu s Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak bezpečně používat privátní balíčky Pythonu v rámci Azure Machine Learning. Případy použití pro privátní balíčky Pythonu zahrnují:

 * Vyvinuli jste soukromý balíček, který nechcete veřejně sdílet.
 * Chcete použít známé úložiště balíčků uložených v rámci podnikové brány firewall.

Doporučený postup závisí na tom, jestli máte několik balíčků pro jeden Azure Machine Learning pracovní prostor nebo celé úložiště balíčků pro všechny pracovní prostory v rámci organizace.

Soukromé balíčky se používají přes třídu [prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment) . V rámci prostředí deklarujete, které balíčky python se mají použít, včetně soukromých. Další informace o prostředí v Azure Machine Learning všeobecně najdete v tématu [Jak používat prostředí](how-to-use-environments.md). 

## <a name="prerequisites"></a>Předpoklady

 * [Sada SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
 * [Pracovní prostor Azure Machine Learning](how-to-manage-workspace.md)

### <a name="use-small-number-of-packages-for-development-and-testing"></a>Použití malého počtu balíčků pro vývoj a testování

Pro malý počet privátních balíčků pro jeden pracovní prostor použijte statickou [`Environment.add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) metodu. Tento přístup umožňuje rychle přidat do pracovního prostoru soukromý balíček a je vhodný pro účely vývoje a testování.

Nastavte argument cesta k souboru na místní soubor kolečka a spusťte ```add_private_pip_wheel``` příkaz. Příkaz vrátí adresu URL používanou ke sledování umístění balíčku v pracovním prostoru. Zaznamenejte si adresu URL úložiště a předejte ji `add_pip_package()` metodě.

```python
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

Interně Azure Machine Learning služba nahrazuje adresu URL zabezpečenou adresou URL SAS, takže váš soubor kolečka zůstane privátní a zabezpečený.

### <a name="consume-a-repository-of-packages-from-azure-devops-feed"></a>Využití úložiště balíčků z Azure DevOps feed

Pokud aktivně vyvíjíte balíčky Pythonu pro vaši aplikaci Machine Learning, můžete je hostovat do úložiště Azure DevOps jako artefakty a publikovat je jako informační kanál. Tento přístup umožňuje integrovat pracovní postup DevOps pro vytváření balíčků s vaším pracovní prostor Azure Machine Learning. Informace o tom, jak nastavit kanály Pythonu pomocí Azure DevOps, najdete [v článku Začínáme s balíčky Pythonu v Azure Artifacts](https://docs.microsoft.com/azure/devops/artifacts/quickstarts/python-packages?view=azure-devops)

Tento přístup používá k ověření v úložišti token Personal Access. Stejný přístup se vztahuje i na jiná úložiště s ověřováním na základě tokenů, jako jsou například soukromá úložiště GitHub. 

 1. Pro instanci Azure DevOps [Vytvořte osobní přístupový token (Pat)](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page#create-a-pat) . Nastavte rozsah tokenu pro __sbalení > číst__. 

 2. Pomocí metody [Workspace. set_connection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-) přidejte do vlastností pracovního prostoru adresu URL a Pat služby Azure DevOps.

     ```python
    from azureml.core import Workspace
    
    pat_token = input("Enter secret token")
    ws = Workspace.from_config()
    ws.set_connection(name="connection-1", 
        category = "PythonFeed",
        target = "https://<my-org>.pkgs.visualstudio.com", 
        authType = "PAT", 
        value = pat_token) 
     ```

 3. Vytvořte Azure Machine Learning prostředí a přidejte balíčky Pythonu z informačního kanálu.
    
    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies
    
    env = Environment(name="my-env")
    cd = CondaDependencies()
    cd.add_pip_package("<my-package>")
    cd.set_pip_option("--extra-index-url https://<my-org>.pkgs.visualstudio.com/<my-project>/_packaging/<my-feed>/pypi/simple")
    env.python.conda_dependencies=cd
    ```

Prostředí je teď připravené k použití v rámci školicích běhů nebo nasazení koncových bodů webové služby. Při sestavování prostředí používá služba Azure Machine Learning službu PAT k ověřování na informačním kanálu pomocí odpovídajícího základního URL.

### <a name="consume-a-repository-of-packages-from-private-storage"></a>Využití úložiště balíčků z privátního úložiště

Balíčky můžete využívat z účtu služby Azure Storage v rámci brány firewall vaší organizace. Takový účet úložiště může obsahovat uspořádanou sadu balíčků pro podnikové použití nebo interní zrcadlo veřejně dostupných balíčků.

Nastavení takového privátního úložiště:

 1. [Umístěte pracovní prostor do virtuální sítě (VNET)](how-to-enable-virtual-network.md).
 2. Vytvořte účet úložiště a [zakažte veřejný přístup](https://docs.microsoft.com/azure/storage/common/storage-network-security).
 2. Umístěte balíčky Pythonu, které chcete použít, do kontejneru v rámci účtu úložiště. 
 3. [Povolení přístupu účtu úložiště z virtuální sítě pracovního prostoru](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network) 

Pak můžete odkazovat na balíčky v definici prostředí Azure Machine Learning jejich úplnou adresou URL v úložišti objektů BLOB v Azure.

## <a name="next-steps"></a>Další kroky

 * Další informace o [podnikovém zabezpečení v Azure Machine Learning](concept-enterprise-security.md)
