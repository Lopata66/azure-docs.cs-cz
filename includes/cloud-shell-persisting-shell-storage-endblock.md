---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66125220"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Přenos místních souborů do služby Cloud Shell
`clouddrive` Directory se synchronizuje s okna portálu služby Azure storage. Pomocí tohoto okna přenos místních souborů nebo ze sdílené složky. Aktualizace souborů z v rámci Cloud Shell se projeví ve službě file storage grafického uživatelského rozhraní při aktualizaci okna.

### <a name="download-files"></a>Stažení souborů

![Seznam místních souborů](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. Na webu Azure Portal přejděte do sdílené složky připojené.
2. Vyberte cílový soubor.
3. Vyberte **Stáhnout** tlačítko.

### <a name="upload-files"></a>Nahrát soubory

![Místní soubory k odeslání](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Přejdete na připojené sdílené složky.
2. Vyberte tlačítko **Nahrát**.
3. Vyberte požadovaný soubor nebo soubory, které chcete nahrát.
4. Potvrďte odeslání.

Teď byste měli vidět soubory, které jsou k dispozici v vaše `clouddrive` adresáře ve službě Cloud Shell.