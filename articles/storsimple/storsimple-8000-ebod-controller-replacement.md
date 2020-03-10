---
title: Výměna EBOD kontroleru StorSimple 8600 | Microsoft Docs
description: Vysvětluje, jak odebrat a nahradit jeden nebo oba EBOD řadiče na zařízení StorSimple 8600.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: b05d1f36d1e74b3d915e216676859654fbcbacf3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365974"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Výměna EBOD kontroleru na zařízení StorSimple

## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak nahradit vadný modul EBOD Controller na zařízení Microsoft Azure StorSimple. Chcete-li nahradit modul EBOD Controller, je třeba:

* Odebrat vadný kontroler EBOD
* Nainstalovat nový kontroler EBOD

Než začnete, vezměte v úvahu následující informace:

* Prázdné moduly EBOD musí být vloženy do všech nepoužitých slotů. Když zůstane otvor otevřený, skříň se neprojeví správně.
* Kontroler EBOD je horká a může být odebraný nebo nahrazený. Neodstraňujte modul, který selhal, dokud nebudete mít náhradu. Když zahájíte proces nahrazení, musíte ho dokončit do 10 minut.

> [!IMPORTANT]
> Před pokusem o odebrání nebo nahrazení jakékoli součásti StorSimple se ujistěte, že jste provedli kontrolu [bezpečnostních ikon](storsimple-safety.md#safety-icon-conventions) a další [bezpečnostní opatření](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Odebrání kontroleru EBOD
Než v zařízení StorSimple nahradíte modul neúspěšného EBOD kontroleru, ujistěte se, že je aktivní a spuštěný modul EBOD Controller. Následující postup a tabulka vysvětlují, jak odebrat modul EBOD Controller.

#### <a name="to-remove-an-ebod-module"></a>Odebrání modulu EBOD
1. Otevřete web Azure Portal.
2. Přejděte na své zařízení, přejděte na **nastavení** > **stav hardwaru**a ověřte, že je stav indikátoru LED pro modul Active eBOD Controller zelený a indikátor LED pro modul eBOD Controller, který selhal, je červený.
3. Na zadní straně zařízení Najděte modul EBOD Controller, který selhal.
4. Než eBOD modul EBOD ze systému, odeberte kabely, které připojovat modul kontroleru k řadiči.
5. Poznamenejte si přesný port SAS modulu EBOD Controller, který byl připojen k řadiči. Po nahrazení modulu EBOD budete muset obnovit systém do této konfigurace.
   
   > [!NOTE]
   > Obvykle se jedná o port A, který je označen jako **hostitel v** následujícím diagramu.
   
    ![Plán EBOD kontroleru](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Obrázek 1** Zpět modul EBOD
   
   | Popisek | Popis |
   |:--- |:--- |
   | 1 |INDIKÁTOR chyby |
   | 2 |Indikátor napájení |
   | 3 |Konektory SAS |
   | 4 |Indikátory LED SAS |
   | 5 |Sériové porty jenom pro tovární použití |
   | 6 |Port A (hostitel v) |
   | 7 |Port B (hostitel) |
   | 8 |Port C (jenom pro tovární použití) |

## <a name="install-a-new-ebod-controller"></a>Nainstalovat nový kontroler EBOD
Následující postup a tabulka vysvětlují, jak nainstalovat modul EBOD Controller do zařízení StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Instalace kontroleru EBOD
1. Ověřte, jestli je u zařízení EBOD poškození, zejména konektor rozhraní. Neinstalujte nový kontroler EBOD, pokud jsou kódy PIN ohnuté.
2. Pomocí zámků v otevřené pozici posuňte modul do skříně, dokud se západky nespustí.
   
    ![Instalace kontroleru EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Obrázek 2**  Instalace modulu EBOD Controller
3. Zavřete západku. Po kliknutí na západku byste měli slyšet.
   
    ![Uvolňování západky EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Obrázek 3**  Zavírá se západka modulu EBOD.
4. Znovu připojte kabely. Použijte přesnou konfiguraci, která byla přítomna před nahrazením. Podrobnosti o tom, jak připojit kabely, najdete v následujícím diagramu a tabulce.
   
    ![Kabel zařízení 4U pro napájení](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Obrázek 4**. Opětovné připojování kabelů
   
   | Popisek | Popis |
   |:--- |:--- |
   | 1 |Primární skříň |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Kontroler 0 |
   | 5 |Kontroler 1 |
   | 6 |EBOD Controller 0 |
   | 7 |EBOD řadič 1 |
   | 8 |EBOD skříň |
   | 9 |Jednotky distribuce napájení |

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [nahrazení StorSimple hardwarové součásti](storsimple-8000-hardware-component-replacement.md).

