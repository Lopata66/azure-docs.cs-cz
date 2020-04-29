---
title: 'Kurz: inicializace hardwaru – Azure FXT Edge souborového'
description: Jak nastavit počáteční heslo na uzlech souborového Azure FXT Edge
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 8cb5f639deb0630575c46db30efe70ad967324a8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "75550887"
---
# <a name="tutorial-set-hardware-passwords"></a>Kurz: Nastavení hardwarových hesel

Při prvním zapnutí uzlu Azure FXT Edge souborového je nutné nastavit kořenové heslo. Hardwarové uzly nejsou dodávány s výchozím heslem. 

Síťové porty jsou zakázané, dokud se heslo nenastaví a uživatel root se přihlásí.

Tento krok proveďte po instalaci a propojení uzlu, ale před tím, než se pokusíte vytvořit cluster. 

V tomto kurzu se dozvíte, jak se připojit k uzlu hardwaru a nastavit heslo. 

V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Připojte klávesnici a monitor k uzlu a zapněte ho.
> * Nastavte hesla pro port iDRAC a uživatele root v tomto uzlu.
> * Přihlásit se jako kořen 

Opakujte tyto kroky pro každý uzel, který budete používat ve vašem clusteru. 

Dokončení tohoto kurzu trvá přibližně 15 minut. 

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu proveďte tyto kroky: 

* [Nainstalujte](fxt-install.md) každý uzel Azure FXT Edge souborového do racku zařízení a připojte napájecí kabely a přístup k síti, jak je popsáno v [předchozím kurzu](fxt-network-power.md). 
* Vyhledejte klávesnici připojenou k USB a monitor připojený přes VGA, který se může připojit k hardwarovým uzlům. (Sériový port uzlu je neaktivní před tím, než nastavíte heslo.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Připojit klávesnici a monitor k uzlu

Fyzicky Připojte monitor a klávesnici k uzlu Azure FXT Edge souborového. 

* Připojte monitor k portu VGA.
* Připojte klávesnici k jednomu z portů USB. 

Tento diagram odkazů použijte k vyhledání portů na zadní straně skříně. 

> [!NOTE]
> Sériový port je neaktivní, dokud není heslo nastaveno. 

![Diagram zadní části Azure FXT Edge souborového s porty sériového, VGA a USB s označením](media/fxt-back-serial-vga-usb.png)

Přepínač KVM můžete použít, pokud chcete ke stejným periferním zařízením připojit více než jeden uzel. 

Zapněte uzel stisknutím tlačítka napájení na přední straně. 

![Diagram přední části Azure FXT Edge souborového – tlačítko s kulatým tlačítkem Power je označeno v blízkosti pravé horní části.](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Nastavit počáteční hesla 

Uzel Azure FXT Edge souborového při spuštění vytiskne různé zprávy na monitorování. Po chvíli se zobrazí úvodní obrazovka s nastavením, například:

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set iDRAC and temporary root password.
Minimum password length is 8.
Enter new password:
```

Heslo, které zadáte, se používá pro dvě věci: 

* Jedná se o dočasné kořenové heslo pro tento uzel Azure FXT Edge souborového. 

  Toto heslo se změní, když vytvoříte cluster pomocí tohoto uzlu, nebo když přidáte tento uzel do clusteru. Heslo pro správu clusteru (přidružené uživateli ``admin``) je také kořenovým heslem pro všechny uzly v clusteru.

* Jedná se o dlouhodobé heslo pro port správy hardwaru iDRAC/IPMI.

  Ujistěte se, že jste si zapomněli heslo pro případ, že se k potížím s hardwarem později budete muset přihlásit pomocí rozhraní IPMI.

Zadejte a potvrďte heslo: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Po zadání hesla systém pokračuje v spouštění. Po jeho dokončení se zobrazí ``login:`` výzva. 

## <a name="sign-in-as-root"></a>Přihlásit se jako kořen

Přihlaste ``root`` se jako s heslem, které jste právě nastavili. 

```
login: root
Password:**********
```

Po přihlášení jako kořenového adresáře jsou síťové porty aktivní a budou kontaktovat server DHCP pro IP adresy. 

## <a name="next-steps"></a>Další kroky

Uzel je připravený jako součást clusteru. Můžete ho použít k vytvoření clusteru Azure FXT Edge souborového nebo [ho můžete přidat do existujícího clusteru](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Vytvoření clusteru](fxt-cluster-create.md)
