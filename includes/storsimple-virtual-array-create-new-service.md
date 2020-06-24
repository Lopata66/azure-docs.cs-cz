---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: alkohli
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 4ba5c8b69776b39d8a6640744b0c24600f3a0d5b
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "67175194"
---
#### <a name="to-create-a-new-service"></a>Vytvoření nové služby

1.  Pomocí přihlašovacích údajů pro účet Microsoft se přihlaste k Azure Portal na této adrese URL: <https://portal.azure.com/> . Pokud se zařízení nasazuje na portál pro státní správu, přihlaste se:<https://portal.azure.us/>

2.  V Azure Portal klikněte na **+ vytvořit prostředek** &gt; **úložiště** &gt; **StorSimple virtuální řady**.

    ![Vytvořit novou službu](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  V okně **StorSimple Device Manager** , které se otevře, udělejte toto:

    1.  Zadejte pro službu jedinečný **Název prostředku**. Název prostředku je popisný název, který se dá použít k identifikaci služby. Název může být tvořen 2 až 50 písmeny, číslicemi a spojovníky. Název musí začínat a končit písmenem nebo číslicí.

    2.  V rozevíracím seznamu **Předplatné** vyberte předplatné. Předplatné je propojeno s vaším fakturačním účtem. Toto pole není dostupné, pokud máte pouze jedno předplatné.

    3.  V případě **skupiny prostředků**vyberte existující skupinu nebo vytvořte novou. Další informace najdete v tématu [Skupiny prostředků Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4.  Zadejte **Umístění** služby. V tématu [oblasti Azure](https://azure.microsoft.com/regions/#services) najdete další informace o tom, které služby jsou dostupné v jednotlivých oblastech. V části Obecné vyberte **umístění** , které je nejblíže geografické oblasti, kde chcete zařízení nasadit. Zvažte i následující okolnosti:

        -   Pokud máte v Azure existující úlohy, které máte taky v úmyslu nasadit pomocí zařízení StorSimple, doporučujeme, abyste používali toto datové centrum.

        -   Vaše StorSimple Device Manager a Azure Storage můžou být ve dvou různých umístěních. V takovém případě je nutné vytvořit Správce zařízení StorSimple a účet úložiště Azure odděleně. Pokud chcete vytvořit účet úložiště Azure, přejděte na Azure Storage v Azure Portal a postupujte podle kroků popsaných v tématu [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). Po vytvoření účtu jej přidejte do služby Správce zařízení StorSimple pomocí kroků popsaných v tématu [Konfigurace nového účtu úložiště pro službu](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service).

        -   Pokud nasazujete virtuální zařízení na portálu pro státní správu, Služba StorSimple Device Manager je k dispozici v umístění US Iowa a US) – Virginia.

    5.  Vyberte **vytvořit nový účet úložiště Azure** pro automatické vytvoření účtu úložiště pomocí služby. Zadejte **název účtu úložiště**. Pokud potřebujete mít data v jiném umístění, zaškrtnutí políčka zrušte.

    6.  Pokud chcete mít na řídicím panelu rychlý odkaz na tuto službu, zaškrtněte políčko **Připnout na řídicí panel**.

    7.  Kliknutím na **Vytvořit** vytvořte Správce zařízení StorSimple.

        ![Vytvořit novou službu](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

Budete přesměrováni na cílovou stránku **služby** . Vytvoření služby trvá několik minut. Po úspěšném vytvoření služby budete odpovídajícím způsobem upozorněni a stav služby se změní na **Aktivní**.


