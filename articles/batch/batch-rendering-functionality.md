---
title: Možnosti vykreslování
description: Pro spouštění úloh vykreslování a aplikací se používají standardní Azure Batch možnosti. Batch zahrnuje konkrétní funkce pro podporu vykreslování úloh.
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 3efe1dfa69de5ce41aed2152baa88b313fd928f1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115750"
---
# <a name="azure-batch-rendering-capabilities"></a>Možnosti vykreslování Azure Batch

Pro spouštění úloh vykreslování a aplikací se používají standardní Azure Batch možnosti. Batch zahrnuje také konkrétní funkce pro podporu úloh vykreslování.

Přehled konceptů služby Batch, včetně fondů, úloh a úkolů, najdete v [tomto článku](https://docs.microsoft.com/azure/batch/batch-api-basics).

## <a name="batch-pools"></a>Fondy Batch

### <a name="rendering-application-installation"></a>Vykreslování instalace aplikace

Image virtuálního počítače pro vykreslování Azure Marketplace se dá zadat v konfiguraci fondu, pokud je potřeba použít jenom předem nainstalované aplikace.

K dispozici je bitová kopie systému Windows 2016 a bitová kopie CentOS.  V [Azure Marketplace](https://azuremarketplace.microsoft.com)se image virtuálních počítačů dají najít hledáním dávkového vykreslování.

Příklad konfigurace fondu najdete v [kurzu vykreslování Azure CLI](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli).  Azure Portal a Batch Explorer poskytují nástroje grafického uživatelského rozhraní pro výběr image virtuálního počítače při vytváření fondu.  Pokud používáte rozhraní API služby Batch, zadejte pro [element imagereference](https://docs.microsoft.com/rest/api/batchservice/pool/add#imagereference) při vytváření fondu následující hodnoty vlastností:

| Vydavatel | Nabídka | Skladová jednotka (SKU) | Version |
|---------|---------|---------|--------|
| dávka | vykreslování – centos73 | vykreslování | nejnovější |
| dávka | vykreslování – windows2016 | vykreslování | nejnovější |

Další možnosti jsou k dispozici, pokud jsou na virtuálních počítačích fondu požadovány další aplikace:

* Vlastní image z Galerie sdílených imagí:
  * Pomocí této možnosti můžete na virtuálním počítači nakonfigurovat konkrétní aplikace a verze, které požadujete. Další informace najdete v tématu [Vytvoření fondu pomocí Galerie sdílených imagí](batch-sig-images.md). Skupina Autodesk a chaos upravila Arnold a V-Ray, aby se ověřila proti službě Azure Batch Licensing Service. Ujistěte se, že máte verze těchto aplikací s touto podporou, jinak licencování s platbami za použití nebude fungovat. Aktuální verze Maya nebo 3DS nevyžadují při spuštění bez periferního serveru licenční server (v režimu dávky nebo příkazového řádku). Pokud si nejste jistí, jak v této možnosti pokračovat, obraťte se na podporu Azure.
* [Balíčky aplikací](https://docs.microsoft.com/azure/batch/batch-application-packages):
  * Zabalit soubory aplikace pomocí jednoho nebo více souborů ZIP, nahrajte je přes Azure Portal a určete balíček v konfiguraci fondu. Při vytváření virtuálních počítačů fondu se stáhnou soubory ZIP a extrahované soubory.
* Soubory prostředků:
  * Soubory aplikace se nahrají do úložiště objektů BLOB v Azure a v [úloze spuštění fondu](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)se určí odkazy na soubory. Při vytváření virtuálních počítačů fondu se soubory prostředků stáhnou do každého virtuálního počítače.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Licencování s platbami za použití pro předem nainstalované aplikace

V konfiguraci fondu je nutné zadat aplikace, které budou použity, a mít licenční poplatek.

* Zadejte `applicationLicenses` vlastnost při [vytváření fondu](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body).  V poli řetězců lze zadat následující hodnoty: "Vray", "Arnold", "3dsmax", "Maya".
* Když zadáte jednu nebo více aplikací, náklady na tyto aplikace se přidají do nákladů na virtuální počítače.  Ceny za aplikace jsou uvedené na [stránce s cenami Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Pokud se místo toho připojíte k licenčnímu serveru, abyste mohli používat aplikace pro vykreslování, `applicationLicenses` nezadávejte vlastnost.

Pomocí Azure Portal nebo Batch Explorer můžete vybrat aplikace a zobrazit ceny aplikací.

Pokud dojde k pokusu o použití aplikace, ale aplikace nebyla zadána ve `applicationLicenses` vlastnosti konfigurace fondu nebo nedorazila k licenčnímu serveru, spuštění aplikace se nezdaří s chybou licencování a nenulovým ukončovacím kódem.

### <a name="environment-variables-for-pre-installed-applications"></a>Proměnné prostředí pro předem nainstalované aplikace

Aby bylo možné vytvořit příkazový řádek pro úlohy vykreslování, musí být zadáno umístění instalace spustitelných souborů aplikace.  Na imagích Azure Marketplace virtuálních počítačů se vytvořily systémové proměnné prostředí, které se dají použít místo určení skutečných cest.  Tyto proměnné prostředí jsou kromě [standardních proměnných prostředí služby Batch](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables) vytvořených pro jednotlivé úlohy.

|Aplikace|Spustitelný soubor aplikace|Proměnná prostředí|
|---------|---------|---------|
|Autodesk 3ds Max 2018|3dsmaxcmdio. exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio. exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|Render. exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|Render. exe|MAYA_2018_EXEC|
|Chaos Group V-ray Standalone|Vray. exe|VRAY_3.60.4_EXEC|
Příkazový řádek Arnold 2017|vykázat. exe|ARNOLD_2017_EXEC|
|Příkazový řádek Arnold 2018|vykázat. exe|ARNOLD_2018_EXEC|
|Blenderu|Blend. exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Rodiny virtuálních počítačů Azure

Stejně jako u jiných úloh se různé požadavky na vykreslování a požadavky na výkon pro úlohy a projekty liší.  V Azure je k dispozici velké množství rodin virtuálních počítačů v závislosti na vašich požadavcích – nejnižší náklady, Nejlepší cena/výkon, nejlepší výkon a tak dále.
Některé aplikace pro vykreslování, jako je například Arnold, jsou založené na procesoru. ostatní, například cykly V-Ray a V Blendu, můžou používat procesory nebo GPU.
Popis dostupných rodin virtuálních počítačů a velikostí virtuálních počítačů [najdete v tématu typy a velikosti virtuálních](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)počítačů.

### <a name="low-priority-vms"></a>Virtuální počítače s nízkou prioritou

Stejně jako u jiných úloh je možné virtuální počítače s nízkou prioritou využít ve fondech služby Batch pro vykreslování.  Virtuální počítače s nízkou prioritou fungují stejně jako běžné vyhrazené virtuální počítače, ale využívají nadbytečné kapacity Azure a jsou dostupné pro velkou slevu.  Kompromisy pro použití virtuálních počítačů s nízkou prioritou je, že tyto virtuální počítače nemusí být k dispozici, aby je bylo možné přidělit nebo kdykoli zrušit v závislosti na dostupné kapacitě. Z tohoto důvodu nebudou virtuální počítače s nízkou prioritou vhodné pro všechny úlohy vykreslování. Pokud například obrázky přestanou trvat mnoho hodin, je pravděpodobný, že se vykreslování těchto imagí přerušilo a restartuje kvůli tomu, že virtuální počítače nejsou v důsledku přerušení přijatelné.

Další informace o vlastnostech virtuálních počítačů s nízkou prioritou a různých způsobů jejich konfigurace pomocí služby Batch najdete v tématu [použití virtuálních počítačů s nízkou prioritou ve službě Batch](https://docs.microsoft.com/azure/batch/batch-low-pri-vms).

## <a name="jobs-and-tasks"></a>Úlohy a úlohy

Pro úlohy a úlohy se nevyžaduje žádná podpora specifická pro vykreslování.  Hlavní položka konfigurace je příkazový řádek úkolu, který musí odkazovat na požadovanou aplikaci.
Při použití imagí virtuálních počítačů Azure Marketplace pak osvědčeným postupem použít proměnné prostředí k určení cesty a spustitelného souboru aplikace.

## <a name="next-steps"></a>Další kroky

Příklady dávkového vykreslování si můžete vyzkoušet v těchto dvou kurzech:

* [Vykreslování pomocí Azure CLI](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Vykreslování scén s využitím Batch Exploreru](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
