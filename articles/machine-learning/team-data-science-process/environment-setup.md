---
title: Nastavení data science prostředí v Azure – vědecké zpracování týmových dat
description: Nastavení data science prostředí v Azure pro použití v vědecké zpracování týmových dat.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 028f289092a0183072ab0ba0be29f9d1f79781bc
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534317"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Nastavení prostředí vědeckého zkoumání dat pro použití v rámci vědeckého zpracování týmových dat
Proces vědecké účely Team dat používá různé vědě prostředí datových pro úložiště, zpracování a analýzu dat. Patří mezi ně Azure Blob Storage, několik typů virtuálních počítačů Azure, clustery HDInsight (Hadoop) a pracovní prostory Azure Machine Learning. Rozhodnutí o prostředí, ve kterém chcete použít závisí na typu a množství dat, modelovat a cílového místa pro tato data v cloudu. 

* Pokyny na otázky, které je při tomto rozhodnutí zvažte najdete v tématu [plánování si Azure Machine Learning prostředí pro datové vědy](plan-your-environment.md). 
* Katalog některé scénáře, se mohou vyskytnout při provádění pokročilých analýz, naleznete v tématu [scénáře pro vědecké zpracování týmových dat](plan-sample-scenarios.md)

Následující články popisují, jak vytvořit různé prostředí pro datové vědy používat vědecké zpracování týmových dat.

* [Účet Azure storage](../../storage/common/storage-quickstart-create-account.md)
* [Cluster HDInsight (Hadoop)](customize-hadoop-cluster.md)
* [Pracovní prostor Azure Machine Learning Studio](../studio/create-workspace.md)

**Microsoft Data virtuálního počítače VĚDY** jsou také dostupné jako image virtuálních počítačů (VM) Azure. Tento virtuální počítač je předem nainstalovaný a nakonfigurovaný s několika oblíbenými nástroji, které se běžně používají pro analýzu dat a strojové učení. Datové VĚDY je dostupná ve Windows a Linuxu. Další informace najdete v tématu [Úvod do cloudové virtuální počítač pro datové vědy pro Linux a Windows](../data-science-virtual-machine/overview.md).

Zjistěte, jak vytvořit:

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
