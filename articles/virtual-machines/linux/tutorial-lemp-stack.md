---
title: Výuka – nasazení LEMP na virtuálním počítači s Linuxem v Azure
description: V tomto kurzu zjistíte, jak nainstalovat stack LEMP na virtuální počítač s Linuxem v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 6d603dbf2746608f499ba37b4f17b533b64bc941
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80154351"
---
# <a name="tutorial-install-a-lemp-web-server-on-a-linux-virtual-machine-in-azure"></a>Kurz: Instalace webového serveru LEMP na virtuální počítač s Linuxem v Azure

Tento článek vás provede nasazením webového serveru NGINX, MySQL a PHP (stack LEMP) na virtuální počítač s Ubuntu v Azure. Stack LEMP představuje alternativu oblíbeného [stacku LAMP](tutorial-lamp-stack.md), který můžete v Azure také nainstalovat. Pokud chcete zobrazit server LEMP v akci, můžete volitelně nainstalovat a nakonfigurovat web WordPress. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Ubuntu (písmeno L ve stacku LEMP)
> * Otevření portu 80 pro webový provoz
> * Instalace NGINX, MySQL a PHP
> * Ověření instalace a konfigurace
> * Instalace WordPressu na server LEMP

Toto nastavení je určené pro rychlé testy nebo testování konceptu.

Tento kurz používá vynesené mezizaviny příkazového příkazové číslo v rámci [prostředí Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), které se neustále aktualizuje na nejnovější verzi. Chcete-li otevřít prostředí Cloud Shell, vyberte **Vyzkoušet** v horní části libovolného bloku kódu.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>Instalace NGINX, MySQL a PHP

Spuštěním následujícího příkazu aktualizujte zdroje balíčků Ubuntu a nainstalujte NGINX, MySQL a PHP. 

```bash
sudo apt update && sudo apt install nginx && sudo apt install mysql-server php-mysql php-fpm
```

Zobrazí se výzva k instalaci balíčků a dalších závislostí. Tímto postupem se nainstalují minimální požadovaná rozšíření PHP potřebná k používání PHP a MySQL.  

## <a name="verify-installation-and-configuration"></a>Ověření instalace a konfigurace


### <a name="verify-nginx"></a>Ověření NGINX

Zkontrolujte verzi serveru NGINX pomocí následujícího příkazu:
```bash
nginx -v
```

Když je teď server NGINX nainstalovaný a port 80 k virtuálnímu počítači otevřený, webový server je přístupný z internetu. Pokud chcete zobrazit úvodní stránku serveru NGINX, otevřete webový prohlížeč a zadejte veřejnou IP adresu virtuálního počítače. Použijte veřejnou IP adresu, kterou jste použili k připojení přes SSH k virtuálnímu počítači:

![Výchozí stránka serveru NGINX][3]


### <a name="verify-and-secure-mysql"></a>Ověřit a zabezpečit MySQL

Zkontrolujte verzi MySQL pomocí následujícího příkazu (všimněte si parametru velké `V`):

```bash
mysql -V
```

Chcete-li pomoci zabezpečit instalaci MySQL, včetně `mysql_secure_installation` nastavení kořenového hesla, spusťte skript. 

```bash
sudo mysql_secure_installation
```

Volitelně můžete nastavit modul plug-in ověření hesla (doporučeno). Poté nastavte heslo pro uživatele kořenového adresáře MySQL a nakonfigurujte zbývající nastavení zabezpečení pro vaše prostředí. Doporučujeme odpovědět na všechny otázky "Y" (ano).

Pokud chcete vyzkoušet funkce MySQL (vytvoření databáze MySQL, přidání uživatelů nebo změna nastavení konfigurace), přihlaste se k MySQL. Tento krok není nezbytný k dokončení kurzu. 


```bash
sudo mysql -u root -p
```

Jakmile budete hotovi, ukončete příkazový řádek mysql zadáním `\q`.

### <a name="verify-php"></a>Ověření PHP

Zkontrolujte verzi PHP pomocí následujícího příkazu:

```bash
php -v
```

Nakonfigurujte server NGINX tak, aby používal správce procesu PHP FastCGI (PHP-FPM). Spuštěním následujících příkazů zálohujte původní konfigurační soubor serverového bloku NGINX a pak v libovolném editoru upravte původní soubor:

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

V editoru nahraďte obsah souboru `/etc/nginx/sites-available/default` následujícím kódem. Komentáře obsahují vysvětlení jednotlivých nastavení. Nahraďte veřejnou IP adresu virtuálního počítače vaší *PublicIPAddress*, potvrďte verzi PHP v `fastcgi_pass`aplikaci a zbývající nastavení ponechte. Pak soubor uložte.

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    # Homepage of website is index.php
    index index.php;

    server_name yourPublicIPAddress;

    location / {
        try_files $uri $uri/ =404;
    }

    # Include FastCGI configuration for NGINX
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
    }
}
```

Zkontrolujte chyby syntaxe v konfiguraci serveru NGINX:

```bash
sudo nginx -t
```

Pokud je syntaxe správná, restartujte server NGINX pomocí následujícího příkazu:

```bash
sudo service nginx restart
```

Pokud chcete podrobnější test, vytvořte jednoduchou informační stránku PHP a zobrazte ji v prohlížeči. Následující příkaz vytvoří informační stránku PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```



Teď můžete zkontrolovat informační stránku PHP, kterou jste vytvořili. Otevřete prohlížeč a přejděte na adresu `http://yourPublicIPAddress/info.php`. Použijte veřejnou IP adresu svého virtuálního počítače. Informační stránka by měla vypadat podobně jako na tomto obrázku.

![Informační stránka PHP][2]


[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili server LEMP v Azure. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Ubuntu
> * Otevření portu 80 pro webový provoz
> * Instalace NGINX, MySQL a PHP
> * Ověření instalace a konfigurace
> * Instalace WordPressu ve stacku LEMP

Přejdete k dalšímu kurzu, kde se dozvíte, jak zabezpečit webové servery pomocí certifikátů TLS/SSL.

> [!div class="nextstepaction"]
> [Zabezpečený webový server s TLS](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png
