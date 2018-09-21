---
title: Návrh první databáze SQL Azure – Jazyk C# | Microsoft Docs
description: Zjistěte, jak navrhnout první databázi SQL Azure a jak se k ní připojit pomocí programu v jazyce C# s využitím ADO.NET.
services: sql-database
author: MightyPen
manager: craigg-msft
ms.reviewer: CarlRabeler
ms.service: sql-database
ms.custom: develop databases, mvc, devcenter
ms.topic: tutorial
ms.author: genemi
ms.date: 06/07/2018
ms.openlocfilehash: 9fdc23eb2e607d1264e1bd2bc9dc85ef8d9be642
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498149"
---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Návrh databáze SQL Azure a její připojení pomocí C&#x23; a ADO.NET

Azure SQL Database je relační databáze jako služba (DBaaS) v cloudu Microsoftu (Azure). V tomto kurzu zjistíte, jak pomocí webu Azure Portal a ADO.NET v sadě Visual Studio provádět následující úlohy: 

> [!div class="checklist"]
> * Vytvořit databázi na webu Azure Portal
> * Vytvořit pravidlo brány firewall na úrovni serveru na webu Azure Portal
> * Připojit se k databázi pomocí ADO.NET a sady Visual Studio
> * Vytvářet tabulky pomocí ADO.NET
> * Vkládat, aktualizovat a odstraňovat data pomocí ADO.NET 
> * Dotazovat data pomocí ADO.NET

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Instalaci sady [Visual Studio Community 2017, Visual Studio Professional 2017 nebo Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/).

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili základním úkonům při práci s databází, jako je vytvoření databáze a tabulek, načtení a dotazování dat a obnovení databáze k určitému bodu v čase. Naučili jste se tyto postupy:
> [!div class="checklist"]
> * Vytvoření databáze
> * Nastavení pravidla brány firewall
> * Připojení k databázi pomocí sady [Visual Studio a jazyka C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Vytvoření tabulek
> * Vložení, aktualizace a odstranění dat
> * Dotazování dat

V dalším kurzu se dozvíte, jak migrovat data.

> [!div class="nextstepaction"]
> [Migrace databáze SQL Serveru do služby Azure SQL Database](sql-database-migrate-your-sql-server-database.md)

