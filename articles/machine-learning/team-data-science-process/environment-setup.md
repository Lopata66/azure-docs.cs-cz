---
title: Nastavení prostředí pro datové vědy v Azure – Týmová věda – zpracování dat
description: Nastavte prostředí pro datové vědy v Azure pro použití v rámci vědeckého zpracování týmových dat.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6d1185e13397b3e466bd1e8d609a46f2f68bf390
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063935"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Nastavení prostředí vědeckého zkoumání dat pro použití v rámci vědeckého zpracování týmových dat
Vědecké zpracování týmových dat používá pro ukládání, zpracování a analýzu dat různé prostředí pro datové vědy. Zahrnují Azure Blob Storage, několik typů clusterů Azure Virtual Machine, HDInsight (Hadoop) a pracovní prostory Azure Machine Learning. Rozhodnutí o tom, které prostředí se má použít, závisí na typu a množství dat, která se mají modelovat, a na cílovém cíli pro tato data v cloudu. 

* Pokyny ohledně otázek, které je potřeba vzít v úvahu při rozhodování, najdete v tématu [plánování Azure Machine Learningho prostředí pro datové vědy](plan-your-environment.md). 
* Katalog některých scénářů, se kterými se můžete setkat při provádění pokročilých analýz, najdete v tématu [scénáře pro vědecké zpracování týmových dat](plan-sample-scenarios.md) .

Následující články popisují, jak nastavit různá prostředí pro datové vědy, která používá proces vědeckého zpracování dat týmu.

* [Účet úložiště Azure](../../storage/common/storage-account-create.md)
* [Cluster HDInsight (Hadoop)](customize-hadoop-cluster.md)
* [Pracovní prostor Azure Machine Learning Studio (klasický)](../studio/create-workspace.md)

**Microsoft Data Science Virtual Machine (DSVM)** je také k dispozici jako image virtuálních počítačů Azure. Tento virtuální počítač je předem nainstalovaný a nakonfigurovaný pomocí několika oblíbených nástrojů, které se běžně používají pro analýzu dat a strojové učení. DSVM je k dispozici v systémech Windows i Linux. Další informace najdete v tématu [Úvod do cloudového Data Science Virtual Machine pro Linux a Windows](../data-science-virtual-machine/overview.md).

Přečtěte si, jak vytvořit:

- [DSVM s Windows](../data-science-virtual-machine/provision-vm.md)
- [DSVM s Ubuntu](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [DSVM s CentOS](../data-science-virtual-machine/linux-dsvm-intro.md)
