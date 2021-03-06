---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 5743d785afb87aef6b3a89af6dc8eb18f66b164d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "68854658"
---
Teď můžete pomocí nástroje Průzkumník dat na webu Azure Portal vytvořit databázi a tabulku. 

1. Vyberte **Průzkumník dat**  >  **Nová tabulka**. 
    
    Úplně vpravo se zobrazí oblast **Přidat tabulku**. Pokud ji nevidíte, možná se budete muset posunout doprava.

    ![Průzkumník dat na webu Azure Portal](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. Na stránce **Přidat tabulku** zadejte nastavení pro novou tabulku.

    Nastavení|Navrhovaná hodnota|Popis
    ---|---|---
    ID tabulky|sample-table|ID vaší nové tabulky. Názvy tabulek mají stejné požadavky na znaky jako ID databází. Názvy databází musí mít délku 1 až 255 znaků a nesmí obsahovat znaky `/ \ # ?` ani koncové mezery.
    Propustnost|400 RU/s|Změňte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.

3. Vyberte **OK**.

4. Průzkumník dat zobrazí novou databázi a tabulku.

   ![Průzkumník dat na webu Azure Portal zobrazující novou databázi a tabulku](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)