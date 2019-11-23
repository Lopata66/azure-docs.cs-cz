---
title: Ladění úloh U-SQL – kód Nástroje Azure Data Lake pro Visual Studio
description: Naučte se používat kód Nástroje Azure Data Lake pro Visual Studio ke spouštění a ladění úloh U-SQL místně.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: e51b5640163546c673a1b0f61da47ccd992f27ad
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030033"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Spuštění U-SQL a místní ladění v Visual Studio Code
Tento článek popisuje, jak spustit úlohy U-SQL na místním vývojovém počítači, abyste urychlili fáze počátečního kódování nebo mohli ladit kód místně v Visual Studio Code. Pokyny k nástroji Azure Data Lake Tool for Visual Studio Code najdete v tématu [použití kódu nástroje Azure Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-for-vscode.md).

Pouze instalace systému Windows Nástroje Azure Data Lake pro Visual Studio podporují akci místního spuštění U-SQL a místní ladění U-SQL. Instalace v operačních systémech macOS a Linux tuto funkci nepodporují.

## <a name="set-up-the-u-sql-local-run-environment"></a>Nastavení místního prostředí pro spuštění U-SQL

1. Stisknutím kombinace kláves CTRL + SHIFT + P otevřete paletu příkazů a pak zadejte **ADL: stáhnout místní spuštění balíčku** pro stažení balíčků.  

   ![Stáhnout balíčky závislostí ADL LocalRun](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Vyhledejte balíčky závislostí z cesty zobrazené v podokně **výstup** a pak nainstalujte BuildTools a Win10SDK 10240. Tady je příklad cesty:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Vyhledat balíčky závislostí](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2,1 Pokud chcete nainstalovat **BuildTools**, klikněte ve složce LocalRunDependency na soubor visualcppbuildtools_full. exe a postupujte podle pokynů průvodce.   

    ![Nainstalovat BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2,2 Chcete-li nainstalovat **Win10SDK 10240**, klikněte na soubor sdksetup. exe ve složce LocalRunDependency/Win10SDK_10.0.10240 _2 a postupujte podle pokynů průvodce.  

    ![Instalace Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Nastavte proměnnou prostředí. Nastavte proměnnou prostředí **SCOPE_CPP_SDK** na:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Spuštění místní služby spuštění a odeslání úlohy U-SQL do místního účtu 
V případě prvního uživatele použijte **ADL: Stáhněte si místní spuštění balíčku** pro stažení místních balíčků Run, pokud jste nastavili [místní prostředí pro spuštění U-SQL](#set-up-the-u-sql-local-run-environment).

1. Stisknutím kombinace kláves CTRL + SHIFT + P otevřete paletu příkazů a pak zadejte **ADL: Spusťte místní spuštění služby**.   
2. Pokud chcete licenční podmínky pro software společnosti Microsoft přijměte poprvé, vyberte **přijmout** . 

   ![Přijmout licenční podmínky pro software společnosti Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Otevře se konzola cmd. Pro uživatele v prvním čase musíte zadat **3**a potom najít cestu k místní složce pro vstup a výstup dat. Pokud nejste úspěšně definovali cestu pomocí zpětných lomítek, zkuste lomítko. Pro jiné možnosti můžete použít výchozí hodnoty.

   ![Data Lake nástroje pro Visual Studio Code místní spuštění cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Vyberte CTRL + SHIFT + P a otevřete paletu příkazů, zadejte **ADL: odeslat úlohu**a potom vyberte **místní** a odešlete úlohu do místního účtu.

   ![Data Lake nástroje pro Visual Studio Code výběr místní](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Po odeslání úlohy si můžete zobrazit podrobnosti o odeslání. Chcete-li zobrazit podrobnosti o odeslání, vyberte v okně **výstup** možnost **jobUrl** . Stav odeslání úlohy můžete také zobrazit z konzoly cmd. Pokud chcete získat další informace o úloze, zadejte **7** v konzole cmd.

   ![Data Lake nástroje pro Visual Studio Code místní výstup spuštění](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Data Lake nástroje pro Visual Studio Code místní spuštění – stav příkazu cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Spustit místní ladění pro úlohu U-SQL  
Pro uživatele v prvním čase:

1. Pomocí **ADL: stáhnout místní balíček** pro spuštění pro stažení místních balíčků spuštění, pokud jste nastavili [místní prostředí pro spuštění U-SQL](#set-up-the-u-sql-local-run-environment).
2. Pokud není nainstalována, nainstalujte .NET Core SDK 2,0, jak je navrženo v okně se zprávou.
 
  ![připomenutí nainstaluje dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Pokud C# není nainstalovaný, nainstalujte pro Visual Studio Code, jak je navrženo v okně se zprávou. Pokračujte kliknutím na tlačítko **nainstalovat** a pak restartujte VSCode.

    ![Připomenutí k instalaciC#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Pomocí následujících kroků proveďte místní ladění:
  
1. Stisknutím kombinace kláves CTRL + SHIFT + P otevřete paletu příkazů a pak zadejte **ADL: Spusťte místní spuštění služby**. Otevře se konzola cmd. Ujistěte se, že je nastavena sada **dataroot** .
2. Nastavte zarážku v C# kódu na pozadí.
3. Zpět na editor skriptů, klikněte pravým tlačítkem a vyberte **ADL: místní ladění**.
    
   ![Data Lake nástroje pro Visual Studio Code výsledek místního ladění](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Další kroky
* [Použití nástrojů Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Vývoj U-SQL pomocí Pythonu, R a CSharp pro Azure Data Lake Analytics v VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Začínáme s Data Lake Analytics pomocí prostředí PowerShell](data-lake-analytics-get-started-powershell.md)
* [Začínáme s Data Lake Analytics pomocí Azure Portal](data-lake-analytics-get-started-portal.md)
* [Použití Data Lake nástrojů pro Visual Studio pro vývoj aplikací U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Použití katalogu Data Lake Analytics (U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
