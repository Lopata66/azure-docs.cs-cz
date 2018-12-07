---
title: Rychlý start k vytvoření aplikace v Pythonu, který používá Azure mezipaměti Redis | Dokumentace Microsoftu
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci Python, která používá mezipaměť Azure Redis
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: v-lincan
ms.assetid: f186202c-fdad-4398-af8c-aee91ec96ba3
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: azure-cache-for-redis
ms.workload: tbd
ms.date: 05/11/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: e9faf30d591cab513695ece1d68704e5bef4ce9c
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53019169"
---
# <a name="quickstart-use-azure-cache-for-redis-with-python"></a>Rychlý start: Použití mezipaměti Azure pro Redis s Pythonem


## <a name="introduction"></a>Úvod

Tento rychlý start ukazuje, jak se připojit k Azure Cache pro Redis s Pythonem ke čtení a zápisu do mezipaměti. 

![Dokončení testu Pythonu](./media/cache-python-get-started/cache-python-completed.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Nainstalované [prostředí Python 2 nebo Python 3](https://www.python.org/downloads/) s nástrojem [pip](https://pypi.org/project/pip/). 

## <a name="create-an-azure-cache-for-redis-on-azure"></a>Vytvoření Azure Cache pro Redis v Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Instalace redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) rozhraní Python pro mezipaměť Azure je pro Redis. Pomocí nástroje pro správu balíčků Pythonu *pip* nainstalujte balíček redis-py. 

Následující příklad pomocí *pip3* pro Python3 nainstaluje balíček redis-py ve Windows 10 s použitím příkazového řádku sady Visual Studio 2017 Developer spuštěného se zvýšenými oprávněními správce.

    pip3 install redis

![Instalace redis-py](./media/cache-python-get-started/cache-python-install-redis-py.png)


## <a name="read-and-write-to-the-cache"></a>Čtení z mezipaměti a zápis do mezipaměti

Spusťte Python a otestujte používání mezipaměti z příkazového řádku. Nahraďte `<Your Host Name>` a `<Your Access Key>` hodnotami pro Azure pro Redis Cache. 

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>.redis.cache.windows.net',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

## <a name="create-a-python-script"></a>Vytvoření skriptu Pythonu

Vytvořte nový textový soubor skriptu *PythonApplication1.py*.

Do souboru *PythonApplication1.py* přidejte následující skript a uložte ho. Tento skript otestuje přístup k mezipaměti. Nahraďte `<Your Host Name>` a `<Your Access Key>` hodnotami pro Azure pro Redis Cache. 

```python
import redis

myHostname = "<Your Host Name>.redis.cache.windows.net"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,password=myPassword,ssl=True)

result = r.ping()
print("Ping returned : " + str(result))

result = r.set("Message", "Hello!, The cache is working with Python!")
print("SET Message returned : " + str(result))

result = r.get("Message")
print("GET Message returned : " + result.decode("utf-8"))

result = r.client_list()
print("CLIENT LIST returned : ") 
for c in result:
    print("id : " + c['id'] + ", addr : " + c['addr'])
```

Spusťte skript s Pythonem.

![Dokončení testu Pythonu](./media/cache-python-get-started/cache-python-completed.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat dalším kurzem, můžete prostředky vytvořené v tomto rychlém startu zachovat a znovu je použít.

V opačném případě, pokud jste už s ukázkovou aplikací v tomto rychlém startu skončili, můžete prostředky Azure vytvořené v tomto rychlém startu odstranit, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a skupina prostředků včetně všech v ní obsažených prostředků bude trvale odstraněna. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky ve stávající skupině prostředků obsahující prostředky, které chcete zachovat, můžete místo odstranění skupiny prostředků odstranit jednotlivé prostředky z jejich odpovídajících oken.
>

Přihlaste se na web [Azure Portal ](https://portal.azure.com) a klikněte na **Skupiny prostředků**.

Do textového pole **Filtrovat podle názvu** zadejte název vaší skupiny prostředků. V pokynech v tomto článku se používala skupina prostředků *TestResources*. Ve výsledcích hledání klikněte na **...** u vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.

![Odstranění](./media/cache-web-app-howto/cache-delete-resource-group.png)

Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění zadáním názvu vaší skupiny prostředků a klikněte na **Odstranit**.

Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření jednoduché webové aplikace v ASP.NET, která používá Azure Cache pro Redis.](./cache-web-app-howto.md)



<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
