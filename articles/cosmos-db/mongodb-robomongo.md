---
title: Slouží k připojení ke službě Azure Cosmos DB Robo 3T
description: Zjistěte, jak se připojit ke službě Azure Cosmos DB pomocí Robo 3T a Azure Cosmos DB přes rozhraní API pro MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
origin.date: 12/26/2018
ms.date: 03/18/2019
author: rockboyfor
ms.author: v-yeche
ms.openlocfilehash: ab066fef4bffe35408fdc5f3ad6d66796b4d0818
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61372500"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Používáte Robo 3T s rozhraním API pro Azure Cosmos DB pro MongoDB.

Pro připojení k účtu Cosmos pomocí Robo 3T, musíte mít:

* Stáhněte a nainstalujte [Robo 3T](https://robomongo.org/)
* Cosmos DB mají [připojovací řetězec](connect-mongodb-account.md) informace

## <a name="connect-using-robo-3t"></a>Připojte se pomocí Robo 3T
Pokud chcete přidat do správce Robo 3T připojení účtu Cosmos, proveďte následující kroky:

1. Načíst informace o připojení účtu Cosmos nakonfigurovanou API MongoDB služby Azure Cosmos DB pomocí pokynů [tady](connect-mongodb-account.md).

    ![Snímek obrazovky okna připojovací řetězec](./media/mongodb-robomongo/connectionstringblade.png)
2. Run *Robomongo.exe*

3. Klikněte na tlačítko připojení v rámci **souboru** ke správě vašeho připojení. Potom klikněte na **vytvořit** v **připojení MongoDB** okno, které se otevře **nastavení připojení** okna.

4. V **nastavení připojení** okna, vyberte název. Potom vyhledejte **hostitele** a **Port** z informací o připojení v kroku 1 a zadejte je do **adresu** a **Port**v uvedeném pořadí.

    ![Snímek obrazovky Robomongo Správa připojení](./media/mongodb-robomongo/manageconnections.png)
5. Na **ověřování** klikněte na tlačítko **provádět ověřování**. Zadejte databázi (výchozí hodnota je *správce*), **uživatelské jméno** a **heslo**.
Obě **uživatelské jméno** a **heslo** najdete informace o připojení v kroku 1.

    ![Snímek obrazovky na kartu ověřování Robomongo](./media/mongodb-robomongo/authentication.png)
6. Na **SSL** kartě **protokolu SSL pomocí**, změňte **metodu ověřování** k **certifikát podepsaný svým držitelem**.

    ![Snímek obrazovky s kartou Robomongo SSL](./media/mongodb-robomongo/SSL.png)
7. Nakonec klikněte na tlačítko **testovací** k ověření, že se můžete připojit, pak **Uložit**.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [použití studia 3T](mongodb-mongochef.md) s rozhraním API služby Azure Cosmos DB pro MongoDB.
- Prozkoumejte MongoDB [ukázky](mongodb-samples.md) s rozhraním API služby Azure Cosmos DB pro MongoDB.

<!-- Update_Description: update meta properties, wording update -->