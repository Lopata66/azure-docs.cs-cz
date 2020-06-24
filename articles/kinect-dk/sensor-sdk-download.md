---
title: Stažení sady SDK pro sadu Azure Kinect snímače
description: Přečtěte si, jak stáhnout a nainstalovat sadu Azure Kinect senzor SDK v systému Windows a Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, stažení aktualizace, nejnovější, dostupná, instalace
ms.openlocfilehash: 2c23977c7e01a9137b72b44d1bdc0e1373bafa0a
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "85277226"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Stažení sady SDK pro sadu Azure Kinect snímače

Tato stránka obsahuje odkazy ke stažení pro jednotlivé verze sady Azure Kinect senzor SDK. Instalační program poskytuje všechny potřebné soubory pro vývoj pro Azure Kinect.

## <a name="azure-kinect-sensor-sdk-contents"></a>Obsah sady SDK pro Azure Kinect snímač

- Hlavičky a knihovny pro sestavení aplikace s využitím Azure Kinect DK
- Distribuovatelné knihovny DLL, které potřebují aplikace s využitím Azure Kinect DK.
- [Prohlížeč Azure Kinect](azure-kinect-viewer.md).
- [Záznam služby Azure Kinect](azure-kinect-recorder.md).
- [Nástroj pro firmware Azure Kinect](azure-kinect-firmware-tool.md).

## <a name="windows-download-link"></a>Odkaz na stažení Windows

[Instalační služba](https://download.microsoft.com/download/4/5/a/45aa3917-45bf-4f24-b934-5cff74df73e1/Azure%20Kinect%20SDK%201.4.0.exe)  |  společnosti Microsoft [Zdrojový kód GitHubu](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1093)

> [!NOTE]
> Při instalaci sady SDK si zapamatujte cestu, do které jste nainstalovali. Například "C:\Program Files\Azure Kinect SDK 1,2". Nástroje, na které se odkazuje v článcích v této cestě, najdete v článku.

Předchozí verze sady SDK a firmwaru pro Azure Kinect najdete na [GitHubu](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="linux-installation-instructions"></a>Pokyny k instalaci pro Linux

V současné době je jedinou podporovanou distribucí Ubuntu 18,04. Chcete-li požádat o podporu pro další distribuce, přečtěte si [tuto stránku](https://aka.ms/azurekinectfeedback).

Nejdřív budete muset nakonfigurovat [úložiště balíčků Microsoftu](https://packages.microsoft.com/), a to podle pokynů uvedených [tady](https://docs.microsoft.com/windows-server/administration/linux-package-repository-for-microsoft-software).

Nyní můžete nainstalovat potřebné balíčky. `k4a-tools`Balíček zahrnuje [Azure Kinect Viewer](azure-kinect-viewer.md), [zapisovač Azure Kinect](record-sensor-streams-file.md)a [nástroj Azure Kinect firmware](azure-kinect-firmware-tool.md). Pokud ho chcete nainstalovat, spusťte

 `sudo apt install k4a-tools`

 `libk4a<major>.<minor>-dev`Balíček obsahuje hlavičky a soubory cmake pro sestavení `libk4a` .
`libk4a<major>.<minor>`Balíček obsahuje sdílené objekty potřebné ke spouštění spustitelných souborů, které jsou závislé na `libk4a` .

 Základní kurzy vyžadují `libk4a<major>.<minor>-dev` balíček. Pokud ho chcete nainstalovat, spusťte

 `sudo apt install libk4a1.1-dev`

Pokud příkaz uspěje, sada SDK je připravena k použití.

## <a name="change-log-and-older-versions"></a>Změnit protokol a starší verze

Protokol změn pro sadu Azure Kinect senzor SDK najdete [tady](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md).

Pokud potřebujete starší verzi sady Kinect senzorů Azure, Najděte ji [tady](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="next-steps"></a>Další kroky

[Nastavení Azure Kinect DK](set-up-azure-kinect-dk.md)
