---
title: Registrace předplatného Azure u Cloudyn | Microsoft Docs
description: Tento rychlý start podrobně popisuje proces registrace nezbytný k vytvoření zkušebního předplatného a přihlášení k portálu Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2018
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management
manager: benshy
ms.openlocfilehash: 28fdf869fd2df8078c5c318f89f859645ec62665
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002525"
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Registrace samostatného předplatného Azure a zobrazení informací o nákladech

Pomocí svého předplatného Azure se zaregistrujete u Cloudyn. Registrací získáte přístup k portálu Cloudyn. Tento rychlý start podrobně popisuje proces registrace nezbytný k vytvoření zkušebního předplatného a přihlášení k portálu Cloudyn. Ukazuje také, jak rovnou začít zobrazovat informace o nákladech.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="register-with-cloudyn"></a>Registrace u Cloudyn

1. Na webu Azure Portal klikněte v seznamu služeb na **Cost Management a fakturace**.
2. V části **Přehled** klikněte na **Cloudyn**.  
    ![Cloudyn stránka zobrazená na webu Azure Portal](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. Na stránce **Cost Management** klikněte na **Přejít na Cloudyn** a v novém okně se otevře stránka pro registraci u Cloudyn.
4. Na stránce pro registraci zkušební verze portálu Cloudyn zadejte název vaší společnosti, vyberte **Vlastník samostatného předplatného Azure** a pak klikněte na **Další**. Do formuláře se automaticky přidá název vašeho účtu a ID tenanta.  
    ![Registrace zkušební verze stránky, kde zadáte informace o registraci](./media/quick-register-azure-sub/trial-reg-ind.png)
5. Vyberte **ID nabídky – Název** přidružený k vašemu předplatnému. Pokud si nejste jisti, jaké je ID sazby pro vaše předplatné, můžete se podívat na fakturu za Azure, na které najdete **ID nabídky**.
6. Vyjádřete souhlas s podmínkami použití, ověřte své údaje a pak klikněte na **Další**.
7. Na stránce **Shromažďování dalších dat** kliknutím na **Další** udělte Cloudyn oprávnění ke shromažďování dat o prostředcích Azure. Mezi shromažďovaná data patří informace o využití, výkonu, fakturaci a značkách z vašich předplatných.  
    ![Shromažďovat další data stránky, kde udělte Cloudyn oprávnění](./media/quick-register-azure-sub/gather-additional.png)
8. Váš prohlížeč vás přesměruje na přihlašovací stránku pro Cloudyn. Přihlaste se pomocí přihlašovacích údajů vašeho předplatného Azure.
9. Kliknutím na **Přejít do Cloudyn** otevřete portál Cloudyn a na stránce **Správa účtů** by se měly zobrazit informace o vašem účtu předplatného Azure.  
    ![Účty správy stránka zobrazuje informace o předplatném Azure](./media/quick-register-azure-sub/accounts-mgt.png)

Pokud chcete zhlédnout videokurz k registraci předplatného Azure, podívejte se na video [Finding your Directory GUID and Rate ID for use in Cloudyn](https://youtu.be/PaRjnyaNGMI) (Vyhledání GUID adresáře a ID sazby pro použití v Cloudyn).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste použili informace o svém předplatném Azure k registraci u Cloudyn. Také jste se přihlásili k portálu Cloudyn a začali jste zobrazovat informace o nákladech. Další informace o Cloudyn najdete v kurzu pro Cloudyn.

> [!div class="nextstepaction"]
> [Kontrola využití a nákladů](./tutorial-review-usage.md)
