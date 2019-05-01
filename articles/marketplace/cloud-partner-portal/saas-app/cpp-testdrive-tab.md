---
title: Azure SaaS aplikace nabídka testu jednotky konfigurace | Azure Marketplace
description: Konfigurace testovací verze nabídky SaaS aplikace na Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: c76290d6c96108ff04799151c82334264a4c3dd0
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64941705"
---
# <a name="saas-application-test-drive-tab"></a>Karta testovací verzi aplikace SaaS

Na kartě testovací verze pro zajištění zkušební prostředí pro vaše zákazníky.

## <a name="test-drive-benefits"></a>Test jednotky výhody

Vytvoření zkušebního prostředí pro vaše zákazníky je osvědčeným postupem je zajistit, že můžete koupit bez obav. Možnosti zkušebních verzí dostupné Test Drive je nejúčinnější pro vysoce kvalitní generování potenciálních zákazníků a větší převodu těchto potenciálních zákazníků.

Testovací verze poskytuje zákazníkům s praktické, samostatně prováděného zkušební klíčové funkce a výhody, které jsme vám ukázali ve scénáři Skutečná implementace váš produkt.


## <a name="how-a-test-drive-works"></a>Jak funguje testovací verze

Potenciální zákazník prohledá a zjistí aplikace na webu Marketplace. Zákazník přihlásí a souhlasí s podmínkami použití. V tomto okamžiku zákazník přijme předem nakonfigurované prostředí pro akci pro pevný počet hodin, když obdržíte vysoce kvalifikovaných potenciálního zákazníka do se. Další informace najdete v tématu [co je to Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)


## <a name="publishing-steps"></a>Postup publikování

Tady jsou hlavní publikování kroky pro přidání testu jednotky:

1. Definování scénáře vaše testovací verze
2. Vytvořit nebo upravit šablony Resource Manageru
3. Vytvoření podrobné příručce k testovací verzi
4. Nové publikování vaší nabídky


## <a name="setting-up-a-test-drive"></a>Nastavení testovací verze

K dispozici jsou čtyři různé druhy testů jednotek, každý podle typu produktu, scénář a marketplace, které používáte.

|  **Typ**          |  **Popis**  |  **Pokyny k instalaci**  |
|  ---------------   |  ---------------  |  ---------------  |
|     Azure Resource Manager               |    Vyzkoušejte Azure Resource Manageru je šablonu nasazení, která obsahuje všechny prostředky Azure, které tvoří vytváří Vydavatel řešení. Produkty, které patří tento typ testovací verze jsou ty, které používají jenom prostředky Azure.               |       [Testovací verze Azure Resource Manageru](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)            |
|       Hostovaný             |       Hostovaná testovací verze složitost instalační program odebere služba Microsoft hosting a zachovat službu, která provádí Test Drive uživatele zřizování a zrušení zřizování.             |         [Hostovaná testovací verze](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive)          |
|      Aplikace logiky              |       Test aplikace logiky jednotka je šablonu nasazení, která slouží k zahrnovat všechny architektury komplexní řešení. Všechny aplikace Dynamics nebo vlastní produkty by měl použít tento typ testovací verze.            |      [Jednotky Test aplikace logiky](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)             |
|       Power BI             |         Power BI testovací verze se skládá z vložený odkaz na míru sestavené řídicí panel. Jakýkoli produkt, který chce prokázat, že že interaktivní vizuálu Power BI by měl použít tento typ testovací verze. Vše, co potřebujete k nahrání je vaše adresa URL vložené Power BI.          |        [Vyzkoušejte Power BI](#power-bi-test-drive)           |
|   |   |   |


### <a name="power-bi-test-drive"></a>Testovací verze Power BI

Pomocí následujícího postupu můžete nakonfigurovat si testovací jízdu.

1. V části Nová nabídka, vyberte **Test Drive**.
2. Na Test Drive, vyberte **Ano**.

   ![Povolit testovací verze](./media/saas-enable-test-drive.png)

   Když povolíte si testovací jízdu, uvidíte podrobnosti a technické konfigurace formulářů, které jsou uvedeny v následující snímek obrazovky.

   ![Testovací formulář konfigurace disku](./media/saas-test-drive-yes.png)

3. V části **podrobnosti**, zadejte informace pro následující pole:
  
   - Popis – popisují vaše testovací verze a které můžou uživatelé provádět s ním. Základní značky HTML můžete použít k formátování tento popis.
   - Uživatelská příručka – nahrání uživatelská příručka pro dokument, který vaši zákazníci můžou využít při jejich už s ohledem na testovací verzi. Tato příručka musí být soubor pdf.
   - Test Drive ukázkové Video (volitelné) – můžete zadat videa (YouTube nebo Vimeo) pro vaše zákazníky a sledujte předtím, než na testovací verzi. Zadejte URL adresu videa.

4. V části **technické konfigurace**, zadejte informace pro následující pole:

   - Typ testu jednotky – vyberte **Power BI** z rozevíracího seznamu.
   - Odkaz na řídicí panel Power BI – odkaz na řídicí panel.

5. Po dokončení konfigurace na testovací verzi, vyberte **Uložit**.


## <a name="next-steps"></a>Další postup

[Karta podrobností o prodejních místech](./cpp-storefront-tab.md)
