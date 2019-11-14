---
title: Přesun souborů do a z virtuálních počítačů Azure Linux pomocí spojovacího bodu služby
description: Zabezpečený přesun souborů do a z virtuálního počítače se systémem Linux v Azure pomocí spojovacího bodu služby a páru klíčů SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 07/12/2017
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: d78a8e59a55718048df2022cec75c7a2b56f1a6b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036586"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Přesunutí souborů do a z virtuálního počítače se systémem Linux pomocí spojovacího bodu služby

Tento článek ukazuje, jak přesunout soubory z pracovní stanice až do virtuálního počítače Azure Linux nebo z virtuálního počítače Azure Linux na pracovní stanici pomocí zabezpečeného kopírování (SCP). Rychlé a bezpečné přesouvání souborů mezi pracovními stanicemi a VIRTUÁLNÍmi počítači Linux je velmi důležité pro správu infrastruktury Azure. 

Pro tento článek budete potřebovat virtuální počítač se systémem Linux nasazený v Azure pomocí [souborů veřejného a privátního klíče SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Pro místní počítač budete také potřebovat klienta SCP. Je postavená na SSH a je součástí výchozího prostředí bash většiny počítačů se systémy Linux a Mac a některých prostředí Windows.

## <a name="quick-commands"></a>Rychlé příkazy

Zkopírování souboru do virtuálního počítače se systémem Linux

```bash
scp file azureuser@azurehost:directory/targetfile
```

Kopírování souboru z virtuálního počítače se systémem Linux

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Podrobný postup

Příkladem je přesunutí konfiguračního souboru Azure do virtuálního počítače se systémem Linux a stažení adresáře souboru protokolu s použitím klíče SCP a klíčů SSH.   

## <a name="ssh-key-pair-authentication"></a>Ověřování páru klíčů SSH

BOD připojení služby používá SSH pro přenosovou vrstvu. SSH zpracovává ověřování na cílovém hostiteli a přesune soubor do šifrovaného tunelu, který je ve výchozím nastavení poskytnutý pomocí SSH. Pro ověřování SSH se dají použít uživatelská jména a hesla. Jako osvědčený postup zabezpečení se ale doporučuje ověřování pomocí veřejného a privátního klíče SSH. Po ověření připojení přes SSH pak zahájí kopírování souboru. Pomocí správně nakonfigurovaných `~/.ssh/config` a veřejných a privátních klíčů SSH se připojení SCP dá vytvořit jenom pomocí názvu serveru (nebo IP adresy). Pokud máte jenom jeden klíč SSH, spojovací bod služby ho vyhledá v adresáři `~/.ssh/` a použije se ve výchozím nastavení pro přihlášení k virtuálnímu počítači.

Další informace o konfiguraci `~/.ssh/config` a veřejných a privátních klíčů SSH najdete v tématu [vytvoření klíčů ssh](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>Spojovací bod služby soubor k virtuálnímu počítači se systémem Linux

V prvním příkladu jsme zkopírovali konfigurační soubor Azure do virtuálního počítače se systémem Linux, který se používá k nasazení automatizace. Vzhledem k tomu, že tento soubor obsahuje přihlašovací údaje rozhraní Azure API, které zahrnují tajné klíče, zabezpečení je důležité. Šifrované tunely, které poskytuje SSH, chrání obsah souboru.

Následující příkaz zkopíruje místní soubor *. Azure/config* do virtuálního počítače Azure s plně kvalifikovaným názvem domény *MyServer.eastus.cloudapp.Azure.com*. Uživatelské jméno správce na virtuálním počítači Azure je *azureuser*. Soubor je cílem adresáře */Home/azureuser/* . V tomto příkazu nahraďte vlastní hodnoty.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>BOD připojení adresáře z virtuálního počítače se systémem Linux

V tomto příkladu zkopírujeme adresář souborů protokolu z virtuálního počítače se systémem Linux do pracovní stanice. Soubor protokolu může nebo nemusí obsahovat citlivá nebo tajná data. Pomocí spojovacího bodu služby se ale zajišťuje šifrování obsahu souborů protokolu. Použití spojovacího bodu služby k přenosu souborů je nejjednodušší způsob, jak získat adresář protokolů a soubory na pracovní stanici a zároveň je zabezpečit.

Následující příkaz zkopíruje soubory z adresáře */Home/azureuser/logs/* na virtuálním počítači Azure do místního adresáře adresáře/tmp:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

Příznak `-r` CLI instruuje spojovací bod služby k rekurzivnímu kopírování souborů a adresářů z místa v adresáři uvedeném v příkazu.  Všimněte si také, že syntaxe příkazového řádku je podobná příkazu `cp` kopírování.

## <a name="next-steps"></a>Další kroky

* [Správa uživatelů, SSH a kontroly a opravy disků na virtuálních počítačích Azure Linux pomocí rozšíření VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
