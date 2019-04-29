---
title: Profil uživatele a ID pro použití s poznámkovými bloky Azure
description: Jak vytvořit a spravovat váš profil uživatele a ID uživatele s poznámkovými bloky Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 7d069d86-660f-4c94-b6e3-0c0f38c52d0e
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: b8c21b908ca9162a7e44c7af1e222babc6ee1eb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631932"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>Váš profil a ID uživatele pro poznámkové bloky Azure

Profil uživatele v prostoru výkonné a spolupráci poznámkových bloků Azure představuje veřejné image ostatním uživatelům:

[![Na stránce profilu poznámkových bloků Azure](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Vaše ID uživatele je součástí adresy URL můžete použít ke sdílení projektů a poznámkové bloky. Následující seznam popisuje různé vzory adres URL:

- `https://notebooks.azure.com/<user_id>`: Stránce svého profilu.
- `https://notebooks.azure.com/<user_id>/projects`: Vaše projekty. Zobrazí všechny projekty; ostatní uživatelé uvidí jenom veřejné projekty.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Soubory projektu.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Duplicity konkrétních projektů.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: Náhled HTML konkrétní Poznámkový blok nebo souboru.

## <a name="your-user-id"></a>Vaše ID uživatele

Při přihlašování do poznámkových bloků Azure poprvé, váš účet se automaticky přiřadí dočasné uživatelské ID, jako je například "anonymně idr3ca". Za předpokladu, máte ID uživatele, který začíná "anonymně-", poznámkových bloků Azure vás vyzve, abyste ho změnit pokaždé, když se přihlásíte:

![Zobrazit výzvu k vytvoření ID uživatele při přihlašování do poznámkových bloků Azure](media/accounts/create-user-id.png)

A **nakonfigurovat ID uživatele** příkaz také zobrazí vedle názvu dočasného uživatele:

![Příkaz ID uživatele, který se zobrazí, když používáte dočasné ID konfigurace](media/accounts/configure-user-id-command.png)

Vaše ID uživatele můžete kdykoli změnit také na stránce svého profilu.

ID uživatele musí být složený z mezi čtyřmi a šestnáct písmena, číslice a pomlčky. Žádná ostatní znaky jsou povolené a ID uživatele nesmí začínat nebo končit pomlčkou ani použít víc spojovníků za sebou. Vzhledem k tomu, že ID uživatele jsou jedinečná ve všech účtech poznámkových bloků Azure, zobrazí se zpráva, "ID uživatele je již používán." (Se zpráva zobrazí také při pokusu o použití ochranná známka společnosti Microsoft jako ID uživatele.) V těchto případech vyberte ID jiné uživatele.

> [!Important]
> Změna ID vašeho zruší platnost žádné adresy URL možná jste sdíleli pomocí předchozí ID. Můžete změnit ID zpět k předchozí ID na znovu ověřit odkazy. Nicméně je možné pro jiného uživatele, pokud chcete uplatnit nárok nepoužité ID do té doby.

## <a name="your-profile"></a>Váš profil

Váš profil se skládá z veřejně zobrazitelné informace na adrese URL, `https://notebooks.azure.com/<user_id>`. Stránku s vaším profilem také ukazuje naposledy použitých projekty i jakékoli označených hvězdičkou.

Chcete-li upravit svůj profil, použijte **úprava informací profilu** příkaz na stránce svého profilu. V částech vašeho profilu jsou následující:

| Sekce | Obsah |
| --- | --- |
| Profilové fotky | Obrázek, který se zobrazí na stránce svého profilu. |
| Informace o účtu | Zobrazované jméno, ID uživatele a veřejné e-mailový účet. E-mailový účet poskytuje ostatním uživatelům mean se vás kontaktovat a může se lišit od [účet](azure-notebooks-user-account.md) používáte k přihlášení do Azure poznámkových bloků, samotného. |
| Informace o profilu | Vaše poloha, společnosti, pracovní pozice, web a krátký popis sami. |
| Sociální profily | GItHub, Twitter a Facebook ID, pokud chcete sdílet. |
| Nastavení ochrany osobních údajů | Poskytuje dva příkazy:<ul><li>**Exportovat profil**: vytvoří a stáhne *ZIP* soubor, který obsahuje všechny informace, které se uloží poznámkových bloků Azure ve vašem profilu, včetně fotografie, informace o profilu a protokoly zabezpečení.</li><li>**Odstranit svůj účet**: Trvale odstraní všechny vaše osobní údaje uložené v poznámkových bloků Azure.</li></ul> |
| Povolení funkce webu | Umožňuje ovládat chování poznámkových bloků Azure:<ul><li>**Sjednocené front-endu pro poznámkové bloky**: umožňuje rychlejší spuštění poznámkového bloku a lepší trvalosti.</li><li>**Ve výchozím nastavení spouští v JupyterLab**: Ve výchozím nastavení poznámkových bloků Azure poskytuje jednoduché uživatelské rozhraní, který je vhodný pro většinu uživatelů. JupyterLab poskytuje širší ale složitější rozhraní pro zkušené uživatele.</li><li>**Web VNext**: Povolí rozložení modernizované webové stránky zobrazené v této dokumentaci.</li></ul> |

## <a name="next-steps"></a>Další postup  

> [!div class="nextstepaction"]
> [Kurz: vytvoření spustit Poznámkový blok Jupyter provedete lineární regrese](tutorial-create-run-jupyter-notebook.md)
