---
title: Vývojářské nástroje
titleSuffix: Azure Data Science Virtual Machine
description: Seznamte se s nástroji a integrovanými vývojovými prostředími dostupnými na Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c48cf6a7a82e90d3c9d8dc4c35e37dfb944af99f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80282677"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Vývojové nástroje na Data Science Virtual Machine Azure

Sada Data Science Virtual Machine (DSVM) má několik oblíbených nástrojů v vysoce produktivním integrovaném vývojovém prostředí (IDE). Tady je několik nástrojů, které jsou k dispozici na DSVM.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE pro obecné účely      |
| Podporované verze DSVM      | Windows: Visual Studio 2017, Windows 2019: Visual Studio 2019      |
| Typická použití      | Vývoj softwaru    |
| Jak je nakonfigurovaná a nainstalovaná na DSVM?      | Úlohy pro datové vědy (Python a R Tools), úlohy Azure (Hadoop, Data Lake), Node.js, SQL Server nástroje [Azure Machine Learning pro Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Jak použít a spustit      | Zástupce na ploše ( `C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe` ). Graficky otevřete Visual Studio pomocí ikony na ploše nebo nabídky **Start** . Vyhledejte programy (klávesu S logem Windows + S) a potom **Visual Studio**. Odtud můžete vytvářet projekty v jazycích, jako je C#, Python, R a Node.js.   |
| Související nástroje na DSVM      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Může se zobrazit zpráva, že platnost zkušebního období vypršela. Zadejte přihlašovací údaje pro účet Microsoft. Nebo vytvořte nový bezplatný účet pro získání přístupu k Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE pro obecné účely      |
| Podporované verze DSVM      | Windows, Linux     |
| Typická použití      | Editor kódu a integrace Gitu   |
| Jak použít a spustit      | Zástupce na ploše ( `C:\Program Files (x86)\Microsoft VS Code\Code.exe` ) v systému Windows, zástupce na ploše nebo terminálu ( `code` ) v systému Linux    |
| Související nástroje na DSVM      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE klienta pro jazyk R   |
| Podporované verze DSVM      | Windows, Linux      |
| Typická použití      |  Vývoj v jazyce R     |
| Jak použít a spustit      | Zástupce na ploše ( `C:\Program Files\RStudio\bin\rstudio.exe` ) ve Windows, zástupce na ploše ( `/usr/bin/rstudio` ) v systému Linux      |
| Související nástroje na DSVM      |   Visual Studio, Visual Studio Code, Juno      |

## <a name="rstudio-server"></a>RStudio Server

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE klienta pro jazyk R   |
| Co je to?   | Webové prostředí IDE pro R    |
| Podporované verze DSVM      | Linux      |
| Typická použití      |  Vývoj v jazyce R     |
| Jak použít a spustit      | Povolte službu pomocí nástroje _systemctl Enable RStudio-Server_a pak službu spusťte pomocí _systemctl Start RStudio-Server_. Pak se přihlaste k serveru RStudio na adrese http: \/ /Your-VM-IP: 8787.       |
| Související nástroje na DSVM      |   Visual Studio, Visual Studio Code RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE klienta pro jazyk Helena   |
| Podporované verze DSVM      | Windows, Linux      |
| Typická použití      |  Vývoj Helena     |
| Jak použít a spustit      | Zástupce na ploše ( `C:\JuliaPro-0.5.1.1\Juno.bat` ) ve Windows, zástupce na ploše ( `/opt/JuliaPro-VERSION/Juno` ) v systému Linux      |
| Související nástroje na DSVM      |   Visual Studio, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>PyCharm

|    |           |
| ------------- | ------------- |
| Co je to?   | Integrované vývojové prostředí (IDE) klienta jazyka Python    |
| Podporované verze DSVM      | Windows 2019, Linux      |
| Typická použití      |  Vývoj v Pythonu     |
| Jak použít a spustit      | Zástupce na ploše ( `C:\Program Files\tk` ) ve Windows. Zástupce na ploše ( `/usr/bin/pycharm` ) v systému Linux      |
| Související nástroje na DSVM      |   Visual Studio, Visual Studio Code, RStudio      |
