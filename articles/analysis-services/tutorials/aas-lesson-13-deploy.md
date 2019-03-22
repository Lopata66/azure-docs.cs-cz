---
title: 'Kurz služby Azure Analysis Services lekce 13: Nasazení | Dokumentace Microsoftu'
description: Popisuje, jak nasadit projekt Kurz služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 72fa9bf211139b971afa443af812d644c76062fc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58123466"
---
# <a name="deploy"></a>Nasazení

V této lekci nakonfigurujete vlastnosti nasazení – zadáte server služby Azure Analysis Services, na který se má nasazení provést, a název modelu. Pak model nasadíte do příslušné instance. Jakmile bude váš model nasazen, uživatelé se k němu budou moci připojit pomocí klientské aplikace pro vytváření sestav. Další informace najdete v tématu [Nasazení do služby Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy).  
  
Odhadovaný čas dokončení této lekce: **5 minut**  
  
## <a name="prerequisites"></a>Požadavky  
Tento článek je součástí kurzu tabulkového modelování, který by se měl dokončit v daném pořadí. Před provedením úkolů v této lekci, měli mít dokončenou předchozí lekci: [Lekce 12: Analyzovat v aplikaci Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).  

> [!IMPORTANT]  
> Abyste mohli nasadit vzdálený server služby Analysis Services, musíte k němu mít [oprávnění správce](../analysis-services-server-admins.md).  

> [!IMPORTANT]  
> Pokud jste nainstalovali ukázkovou databázi AdventureWorksDW2014 na místní SQL Server a model nasazujete na server služby Azure Analysis Services, vyžaduje se [Místní brána dat](../analysis-services-gateway.md).
  
## <a name="deploy-the-model"></a>Nasazení modelu  
  
#### <a name="to-configure-deployment-properties"></a>Konfigurace vlastností nasazení  

  
1.  V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt **AW Internet Sales** a pak klikněte na **Vlastnosti**.  
  
2.  V dialogovém okně **AW Internet Sales – Stránky vlastností** v části **Server nasazení** zadejte do vlastnosti **Server** úplný název serveru.  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
  
3.  Do vlastnosti **Databáze** zadejte **Adventure Works Internet Sales**.  
  
4.  Do vlastnosti **Název modelu** zadejte **Adventure Works Internet Sales Model**.  
  
5.  Zkontrolujte výběr a klikněte na **OK**.  
  
#### <a name="to-deploy-the-adventure-works-internet-sales"></a>Nasazení modelu Adventure Works Internet Sales
  
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt **AW Internet Sales** > **Sestavit**.  

2. Klikněte pravým tlačítkem na projekt **AW Internet Sales** > **Nasadit**.

   Při nasazování do služby Azure Analysis Services můžete být vyzváni k zadání účtu. Zadejte účet organizace a heslo, například nancy@adventureworks.com. Tento účet musí být mezi správci serveru.
  
   Zobrazí se dialogové okno Nasadit ukazující stav nasazení metadat a každé tabulky zahrnuté v modelu.  
    
   ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. Po úspěšném dokončení nasazení pokračujte kliknutím na **Zavřít**.  
  

Tato lekce popisuje nejběžnější a nejjednodušší metodu nasazení tabulkového modelu z SSDT. Pokročilé možnosti nasazení, jako je Průvodce nasazením nebo automatizace pomocí XMLA a AMO, poskytují lepší flexibilitu, konzistenci a plánovaná nasazení. Další informace najdete v tématu [Nasazení řešení tabulkového modelu](https://docs.microsoft.com/sql/analysis-services/tabular-models/tabular-model-solution-deployment-ssas-tabular).

## <a name="conclusion"></a>Závěr  
Blahopřejeme! Vytvořili a nasadili jste první tabelární model služby Analysis Services. Tento kurz vás provedl dokončením nejběžnějších úkolů v rámci vytváření tabelárního modelu. Teď, když je váš model Adventure Works Internet Sales nasazený, můžete ho spravovat pomocí aplikace SQL Server Management Studio, vytvořit skripty pro zpracování a plán zálohování. Uživatelé se nyní také můžou k modelu připojit pomocí klientské aplikace pro vytváření sestav, jako je Microsoft Excel nebo Power BI.  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
## <a name="whats-next"></a>Co dále?
[Propojení s Power BI Desktop](../analysis-services-connect-pbi.md)   
[Doplňková lekce – Dynamické zabezpečení](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Doplňková lekce – Řádky podrobností](../tutorials/aas-supplemental-lesson-detail-rows.md)   
[Doplňková lekce – Nepravidelné hierarchie](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
