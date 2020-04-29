---
title: Zálohování farmy služby SharePoint do Azure pomocí DPM
description: Tento článek poskytuje přehled ochrany DPM/Azure Backup serveru SharePointové farmy do Azure.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 0199495e3b0eb002e58c096ed9abf05d46f43f97
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80054117"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Zálohování farmy služby SharePoint do Azure pomocí DPM

Můžete zálohovat farmu služby SharePoint a Microsoft Azure pomocí nástroje System Center Data Protection Manager (DPM), a to téměř stejným způsobem jako v případě zálohování jiných zdrojů dat. Azure Backup poskytuje flexibilitu v plánu zálohování k vytvoření denních, týdenních, měsíčních nebo ročních záložních bodů a poskytuje možnosti zásad uchovávání pro různé body zálohování. DPM poskytuje možnost ukládat kopie místních disků pro cíle rychlého obnovení (RTO) a ukládat kopie do Azure pro účely ekonomického a dlouhodobého uchovávání.

Zálohování SharePointu do Azure pomocí DPM je velice podobný proces zálohování SharePointu na DPM místně. V tomto článku se budou poznamenat konkrétní předpoklady pro Azure.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Podporované verze SharePointu a související scénáře ochrany

Seznam podporovaných verzí SharePointu a verzí DPM, které jsou nezbytné k jejich zálohování, najdete v článku [Co může aplikace DPM zálohovat?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019#applications-backup).

## <a name="before-you-start"></a>Než začnete

Před zálohováním farmy služby SharePoint do Azure je třeba potvrdit několik věcí.

### <a name="prerequisites"></a>Požadavky

Než budete pokračovat, ujistěte se, že jste splnili všechny [předpoklady pro použití Microsoft Azure Backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) k ochraně úloh. Mezi úlohy týkající se požadavků patří: vytvoření trezoru záloh, stažení přihlašovacích údajů trezoru, instalace agenta Azure Backup a registrace DPM/Azure Backup Server s trezorem.

Další předpoklady a omezení najdete v článku [zálohování SharePointu pomocí DPM](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#prerequisites-and-limitations) .

## <a name="configure-backup"></a>Konfigurace zálohování

Abyste mohli zálohovat farmu SharePointu, nakonfigurujte ochranu SharePointu pomocí ConfigureSharePoint.exe a potom v DPM vytvořte skupinu ochrany. Pokyny najdete v dokumentaci k aplikaci DPM v tématu [Konfigurace zálohování](https://docs.microsoft.com//system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#configure-backup) .

## <a name="monitoring"></a>Monitorování

Chcete-li monitorovat úlohu zálohování, postupujte podle pokynů v tématu [monitorování zálohy aplikace DPM](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#monitoring) .

## <a name="restore-sharepoint-data"></a>Obnovení dat SharePointu

Informace o tom, jak obnovit položku SharePointu z disku s DPM, najdete v tématu [obnovení dat SharePointu](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#restore-sharepoint-data).

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Obnovení databáze služby SharePoint z Azure pomocí DPM

1. Chcete-li obnovit databázi obsahu služby SharePoint, procházejte různými body obnovení (jak je uvedeno dříve) a vyberte bod obnovení, který chcete obnovit.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dvojím kliknutím na bod obnovení služby SharePoint zobrazíte dostupné informace o katalogu služby SharePoint.

   > [!NOTE]
   > Protože je farma služby SharePoint chráněná pro dlouhodobé uchovávání v Azure, na serveru DPM nejsou k dispozici žádné informace o katalogu (metadata). V důsledku toho je potřeba obnovit katalog farmy služby SharePoint, kdykoli bude nutné obnovit databázi obsahu služby SharePoint v čase.
   >
   >
3. Klikněte na **znovu zařadit do katalogu**.

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Otevře se okno stav **opětovného zařazení do katalogu v cloudu** .

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Po dokončení katalogu se stav změní na *úspěch*. Klikněte na **Zavřít**.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klikněte na objekt služby SharePoint zobrazený na kartě **obnovení** DPM a získejte strukturu databáze obsahu. Klikněte na položku pravým tlačítkem myši a pak klikněte na tlačítko **obnovit**.

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. V tomto okamžiku postupujte podle kroků pro obnovení výše v tomto článku a obnovte databázi obsahu služby SharePoint z disku.

## <a name="switching-the-front-end-web-server"></a>Přepínání předřazeného webového serveru

Pokud máte více než jeden front-end webový server a chcete přepnout Server, který aplikace DPM používá k ochraně farmy, postupujte podle pokynů v části [Přepnutí front-end webového serveru](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#switching-the-front-end-web-server).

## <a name="next-steps"></a>Další kroky

* [Azure Backup Server a DPM – Nejčastější dotazy](backup-azure-dpm-azure-server-faq.md)
* [Řešení potíží se System Center Data Protection Managerem](backup-azure-scdpm-troubleshooting.md)
