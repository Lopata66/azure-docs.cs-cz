---
title: Kódování pomocí Git - vědecké zpracování týmových dat založené na spolupráci
description: Postup vývoje spolupráci kódu pro projekty datových věd používat Git na agilní plánování.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 08/23/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3b57621fcec654f11c8e9a68e4568f332dbf9ac6
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195437"
---
# <a name="collaborative-coding-with-git"></a>Kódování založené na spolupráci s využitím Gitu

Tento článek popisuje, jak použít Git jako architekturu vývoje kódu pro spolupráci pro projekty pro datové vědy. Tento článek popisuje, jak propojit kód v Azure Repos s [agilním vývojem](agile-development.md) pracovních položek v Azure boards, jak provádět revize kódu a jak vytvořit a sloučit žádosti o přijetí změn.

## <a name='Linkaworkitemwithagitbranch-1'></a>Propojení pracovní položky s Azure Reposovou větví 

Azure DevOps nabízí pohodlný způsob, jak připojit Azure Boards uživatelský scénář nebo pracovní položku úkolu pomocí Azure Repos větve úložiště Git. Svůj uživatelský scénář nebo úkol můžete propojit přímo s kódem, který je k němu přidružený. 

Chcete-li připojit pracovní položku k nové větvi, vyberte tři tečky ( **...** ) vedle pracovní položky a v místní nabídce přejděte na a vyberte možnost **Nová větev**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

V dialogovém okně **vytvořit větev** zadejte název nové větve a základní Azure Repos úložiště Git a větev. Základní úložiště musí být ve stejném projektu Azure DevOps jako pracovní položka. Základní větví může být hlavní větev nebo jiná existující větev. Vyberte **vytvořit větev**. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Novou větev můžete vytvořit také pomocí následujícího příkazu git bash v systému Windows nebo Linux:

```bash
git checkout -b <new branch name> <base branch name>

```
Pokud nezadáte \<název základní větve >, je nová větev založena na `master`. 

Chcete-li přepnout do své pracovní větve, spusťte následující příkaz: 

```bash
git checkout <working branch name>
```

Po přepnutí do pracovní větve můžete začít vyvíjet artefakty kódu nebo dokumentace a dokončit tak pracovní položku. Přepínání `git checkout master` se spouští zpátky `master` do větve.

Je dobrým zvykem vytvořit větev Git pro každou pracovní položku uživatelského scénáře. Pak můžete pro každou pracovní položku úkolu vytvořit větev na základě větve uživatelského scénáře. Uspořádejte větve v hierarchii, které odpovídají vztahu uživatelský scénář – úkol, pokud máte více lidí pracujících v různých uživatelských scénářích pro stejný projekt nebo v různých úlohách pro stejný uživatelský scénář. Můžete minimalizovat konflikty tím, že každý člen týmu pracuje na jiné větvi nebo v jiném kódu nebo jiné artefakty při sdílení větve. 

Následující diagram znázorňuje doporučenou strategii větvení pro TDSP. Možná nebudete potřebovat tolik větví, jak je znázorněno zde, zejména v případě, že na projektu pracuje pouze jedna nebo dvě osoby, nebo pouze jedna osoba pracuje na všech úkolech uživatelského scénáře. Ale oddělení vývojové větve od hlavní větve je vždycky dobrým zvykem a může přispět k tomu, aby se větev vydaných verzí přerušila vývojovým aktivitami. Úplný popis modelu větve Git najdete v [úspěšném modelu větvení Git](https://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Můžete také propojit pracovní položky do existující větev. Na stránce **podrobností** pracovní položky vyberte možnost **Přidat odkaz**. Pak vyberte existující větev, na kterou chcete propojit pracovní položku, a vyberte **OK**. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name='WorkonaBranchandCommittheChanges-2'></a>Práce na větvi a potvrzení změn 

Po provedení změny pracovní položky, jako je například přidání souboru skriptu R do `script` větve místního počítače, můžete pomocí následujících příkazů Git bash Potvrdit změnu z místní větve na opačnou pracovní větev:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name='CreateapullrequestonVSTS-3'></a>Vytvoření žádosti o získání dat

Po jednom nebo několika potvrzeních a nabízených oznámeních, až budete připraveni k sloučení aktuální pracovní větve do své základní větve, můžete vytvořit a odeslat *žádost o* přijetí změn v Azure Repos. 

Na hlavní stránce projektu Azure DevOps ukažte > na úložiště**žádostí o získání dat** v levém navigačním panelu. Pak vyberte jednu z **nových tlačítek žádosti o** přijetí změn nebo vytvořte odkaz na **žádost** o přijetí změn.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Na obrazovce **Nová žádost o** přijetí změn v případě potřeby přejděte do úložiště Git a větev, do které chcete sloučit změny. Přidejte nebo změňte jakékoli jiné požadované informace. V části **revidující**přidejte názvy, které potřebujete ke kontrole změn, a pak vyberte **vytvořit**. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name='ReviewandMerge-4'></a>Revize a sloučení

Po vytvoření žádosti o přijetí změn obdrží kontroloři e-mailové oznámení, aby zkontrolovali žádost o přijetí změn. Kontroloři otestují, zda změny fungují, a pokud je to možné, zkontrolujte změny v žadateli. Recenzenti mohou provádět komentáře, požadovat změny a schvalovat nebo odmítat žádosti o přijetí změn na základě jejich posouzení. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

Jakmile recenzenti schválí změny, můžete vy nebo někdo jiný s oprávněním sloučit sloučit pracovní větev do její základní větve. Vyberte **Dokončit**a pak v dialogovém okně **kompletní žádost o** přijetí změn vyberte **Dokončit sloučení** . Pracovní větev můžete odstranit poté, co byla sloučena. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Potvrďte, že je žádost označená jako **Dokončená**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Až se vrátíte do **úložišť** v levém navigačním panelu, uvidíte, že jste přešli do hlavní větve, protože se `script` větev odstranila.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Pomocí následujících příkazů Git bash můžete sloučit `script` pracovní větev do své základní větve a po sloučení odstranit pracovní větev:

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>Další postup

[Úlohy pro datovou vědu](execute-data-science-tasks.md) ukazují, jak pomocí nástrojů provádět několik běžných úloh vědeckého zpracování dat, jako jsou interaktivní zkoumání dat, analýza dat, generování sestav a vytváření modelů.

[Příklady návodů](walkthroughs.md) obsahuje návody pro konkrétní scénáře s odkazy a popisy miniatur. Propojené scénáře znázorňují, jak kombinovat cloudové a místní nástroje a služby do pracovních postupů nebo kanálů k vytváření inteligentních aplikací. 

