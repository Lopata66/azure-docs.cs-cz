---
title: 'Kurz: nasazení aplikace Django v Pythonu pomocí Postgres'
description: Vytvořte webovou aplikaci v Pythonu s databází PostgreSQL a nasaďte ji do Azure. V tomto kurzu se používá Django Framework a aplikace je hostována na Azure App Service v systému Linux.
ms.devlang: python
ms.topic: tutorial
ms.date: 07/22/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: 5ddd40f00bab0e1c115a31ac87ee3fb7bf45e089
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2020
ms.locfileid: "88084448"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-in-azure-app-service"></a>Kurz: nasazení webové aplikace v Django s PostgreSQL v Azure App Service

V tomto kurzu se dozvíte, jak nasadit webovou aplikaci v Pythonu [Django](https://www.djangoproject.com/) řízené daty, která se [Azure App Service](overview.md) a připojí ji k databázi Azure Database for Postgres. App Service poskytuje vysoce škálovatelnou službu s automatickými opravami pro hostování webů.

V tomto kurzu provedete pomocí rozhraní příkazového řádku Azure CLI následující úlohy:

> [!div class="checklist"]
> * Nastavení počátečního prostředí pomocí Pythonu a rozhraní příkazového řádku Azure
> * Vytvoření databáze Azure Database for PostgreSQL
> * Nasazení kódu pro Azure App Service a připojení k Postgres
> * Aktualizace kódu a opětovné nasazení
> * Zobrazit diagnostické protokoly
> * Správa webové aplikace v Azure Portal

Můžete také použít [Azure Portal verzi tohoto kurzu](/azure/developer/python/tutorial-python-postgresql-app-portal).


## <a name="set-up-your-initial-environment"></a>Nastavení počátečního prostředí

1. Mít účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Nainstalujte <a href="https://www.python.org/downloads/" target="_blank">Python 3,6 nebo vyšší</a>.
1. Nainstalujte rozhraní příkazového <a href="/cli/azure/install-azure-cli" target="_blank">řádku Azure CLI</a> 2.0.80 nebo novější, ve kterém spouštíte příkazy v jakémkoli prostředí pro zřizování a konfiguraci prostředků Azure.

Otevřete okno terminálu a ověřte, jestli je verze Pythonu 3,6 nebo vyšší:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Ověřte, jestli je verze Azure CLI 2.0.80á nebo vyšší:

```azurecli
az --version
```

Pak se přihlaste k Azure prostřednictvím rozhraní příkazového řádku:

```azurecli
az login
```

Tento příkaz otevře prohlížeč pro shromáždění vašich přihlašovacích údajů. Po dokončení příkazu se zobrazí výstup JSON obsahující informace o vašich předplatných.

Po přihlášení můžete spustit příkazy Azure pomocí Azure CLI a pracovat s prostředky ve vašem předplatném.


## <a name="clone-or-download-the-sample-app"></a>Klonování nebo stažení ukázkové aplikace

# <a name="git-clone"></a>[Klon Git](#tab/clone)

Naklonujte ukázkové úložiště:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Pak přejít do této složky:

```terminal
cd djangoapp
```

# <a name="download"></a>[Stáhnout](#tab/download)

Přejděte na [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) , vyberte **klonovat**a pak vyberte **Stáhnout ZIP**. 

Rozbalte soubor ZIP do složky s názvem *djangoapp*. 

Pak v této složce *djangoapp* otevřete okno terminálu.

---

Ukázka djangoapp obsahuje aplikaci s dotazy založenými na datech Django, kterou dostanete při [psaní první aplikace Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) v dokumentaci Django. Dokončená aplikace je zde k dispozici pro vaši pohodlí.

Ukázka se také upraví tak, aby běžela v produkčním prostředí, jako je App Service:

- Nastavení výroby jsou v souboru *azuresite/produkční. py* . Podrobnosti o vývoji jsou v *azuresite/Settings. py*.
- Pokud `DJANGO_ENV` je proměnná prostředí nastavená na produkční, aplikace používá nastavení produkčního prostředí. Tuto proměnnou prostředí vytvoříte později v kurzu spolu s dalšími uživateli používanými pro konfiguraci databáze PostgreSQL.

Tyto změny jsou specifické pro konfiguraci Django, aby běžely v jakémkoli provozním prostředí, a nekonkrétně se App Service. Další informace najdete v tématu [Kontrolní seznam nasazení Django](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

## <a name="create-postgres-database-in-azure"></a>Vytvoření databáze Postgres v Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. -->

Nainstalovat `db-up` rozšíření pro rozhraní příkazového řádku Azure:

```azurecli
az extension add --name db-up
```

Pokud `az` příkaz není rozpoznán, ujistěte se, že máte nainstalované rozhraní příkazového řádku Azure, jak je popsáno v tématu [Nastavení počátečního prostředí](#set-up-your-initial-environment).

Pak vytvořte databázi Postgres v Azure pomocí [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) příkazu:

```azurecli
az postgres up --resource-group DjangoPostgres-tutorial-rg --location westus2 --sku-name B_Gen5_1 --server-name <postgre-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- Nahraďte *\<postgres-server-name>* názvem, který je jedinečný v rámci všech Azure (koncový bod serveru je `https://\<postgres-server-name>.postgres.database.azure.com` ). Dobrým vzorem je použití kombinace názvu vaší společnosti a jiné jedinečné hodnoty.
- V případě *\<admin-username>* a *\<admin-password>* Zadejte přihlašovací údaje pro vytvoření uživatele správce pro tento server Postgres.
- [Cenová úroveň](../postgresql/concepts-pricing-tiers.md) B_Gen5_1 (Basic, Gen5, 1 jádro), která se tady používá, je nejlevnější. V případě produkčních databází vynechejte `--sku-name` místo toho argument pro použití úrovně GP_Gen5_2 (pro obecné účely, Gen 5, 2 jader).

Tento příkaz provede následující akce, což může trvat několik minut:

- Vytvořte [skupinu prostředků](../azure-resource-manager/management/overview.md#terminology) s názvem `DjangoPostgres-tutorial-rg` , pokud ještě neexistuje.
- Vytvořte server Postgres.
- Vytvořte výchozí účet správce s jedinečným uživatelským jménem a heslem. (Pokud chcete zadat vlastní přihlašovací údaje, použijte `--admin-user` `--admin-password` argumenty a pomocí `az postgres up` příkazu.)
- Vytvořte `pollsdb` databázi.
- Povolte přístup z místní IP adresy.
- Povolte přístup ze služeb Azure.
- Vytvořte uživatele databáze s přístupem k `pollsdb` databázi.

Všechny kroky můžete provádět samostatně s `az postgres` `psql` příkazy a, ale `az postgres up` provede všechny kroky dohromady.

Po dokončení příkazu vytvoří výstup objektu JSON, který obsahuje různé připojovací řetězce pro databázi, spolu s adresou URL serveru, vygenerovaným uživatelským jménem (například " joyfulKoala@msdocs-djangodb-12345 ") a heslem GUID. Zkopírujte uživatelské jméno a heslo k dočasnému textovému souboru, jak je budete potřebovat později v tomto kurzu.

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>`, lze nastavit na některou z [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/). Pomocí příkazu můžete získat oblasti dostupné pro vaše předplatné [`az account list-locations`](/cli/azure/account#az-account-list-locations) . V produkčních aplikacích uložte svou databázi a aplikaci do stejného umístění.

## <a name="deploy-the-code-to-azure-app-service"></a>Nasaďte kód pro Azure App Service

V této části vytvoříte hostitele aplikací v aplikaci App Service, připojíte tuto aplikaci k databázi Postgres a potom do tohoto hostitele nasadíte svůj kód.

### <a name="create-the-app-service-app"></a>Vytvoření aplikace App Service

V terminálu se ujistěte, že jste v kořenovém adresáři úložiště ( `djangoapp` ), který obsahuje kód aplikace.

Vytvořte aplikaci App Service (hostitelský proces) pomocí [`az webapp up`](/cli/azure/webapp#az-webapp-up) příkazu:

```azurecli
az webapp up --resource-group DjangoPostgres-tutorial-rg --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- Pro `--location` argument použijte stejné umístění jako u databáze v předchozí části.
- Nahraďte *\<app-name>* jedinečným názvem v rámci všech Azure (koncový bod serveru je `https://\<app-name>.azurewebsites.net` ). Povolené znaky pro *\<app-name>* jsou `A` - `Z` , `0` - `9` a `-` . Dobrým vzorem je použití kombinace názvu vaší společnosti a identifikátoru aplikace.

Tento příkaz provede následující akce, což může trvat několik minut:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Vytvořte [skupinu prostředků](../azure-resource-manager/management/overview.md#terminology) , pokud ještě neexistuje. (V tomto příkazu použijete stejnou skupinu prostředků, ve které jste databázi vytvořili dříve.)
- Pokud neexistuje, vytvořte plán [App Service](overview-hosting-plans.md) *DjangoPostgres-tutorial* v cenové úrovni Basic (B1). `--plan`a `--sku` jsou volitelné.
- Pokud neexistuje, vytvořte aplikaci App Service.
- Povolí výchozí protokolování pro aplikaci, pokud ještě není povolené.
- Nahrajte úložiště pomocí nasazení ZIP s povoleným automatizací buildu.

Po úspěšném nasazení příkaz vygeneruje výstup JSON podobný následujícímu příkladu:

![Příklad AZ WebApp up Command Output](./media/tutorial-python-postgresql-app/az-webapp-up-output.png)

> [!TIP]
> Mnoho příkazů rozhraní příkazového řádku Azure v mezipaměti běžně ukládá společné parametry, jako je název skupiny prostředků a plán App Service, do souboru *. Azure/config*. V důsledku toho nemusíte zadávat stejný parametr s pozdějšími příkazy. Například pro opětovné nasazení aplikace po provedení změn můžete spustit `az webapp up` znovu bez parametrů. Příkazy, které pocházejí z rozšíření CLI, `az postgres up` ale v současné době nepoužívají mezipaměť. to je důvod, proč jste si v tomto případě museli zadat skupinu prostředků a umístění `az webapp up` .

> [!NOTE]
> Pokud se v tomto okamžiku pokusíte navštívit adresu URL aplikace, narazíte na chybu "DisallowedHost on/". K této chybě dochází, protože jste ještě nenakonfigurovali aplikaci tak, aby používala nastavení produkčního prostředí popsané dříve, které jste provedli v následující části.

### <a name="configure-environment-variables-to-connect-the-database"></a>Konfigurace proměnných prostředí pro připojení databáze

S kódem teď nasazeným do App Service je dalším krokem připojení aplikace k databázi Postgres v Azure.

Kód aplikace očekává nalezení informací o databázi v řadě proměnných prostředí. K nastavení proměnných prostředí v App Service vytvoříte nastavení aplikace pomocí příkazu [AZ WebApp config appSettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) .

```azurecli
az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="pollsdb" DBUSER="<username>" DBPASS="<password>"
```

- Nahraďte *\<postgres-server-name>* názvem, který jste použili dříve v `az postgres up` příkazu.
- Nahraďte *\<username>* a *\<password>* s přihlašovacími údaji, které příkaz vygeneroval také.
- Název skupiny prostředků a aplikace se vykreslí z hodnot uložených v mezipaměti v souboru *. Azure/config* .
- Příkaz vytvoří nastavení s názvem `DJANGO_ENV` , `DBHOST` , `DBNAME` , `DBUSER` a `DBPASS` podle očekávání v kódu aplikace.
- V kódu Pythonu získáte přístup k těmto nastavením jako k proměnným prostředí s příkazy, jako je `os.environ.get('DJANGO_ENV')` . Další informace najdete v tématu [přístup k proměnným prostředí](configure-language-python.md#access-environment-variables).

### <a name="run-django-database-migrations"></a>Spustit migrace databáze Django

Migrace databáze Django zajišťují, že schéma v PostgreSQL ve službě Azure Database se shoduje s hodnotami popsanými v kódu.

1. V prohlížeči otevřete relaci SSH tak, že přejdete na *https:// \<app-name> . SCM.azurewebsites.NET/webssh/Host* a přihlásíte se pomocí svých přihlašovacích údajů k účtu Azure (nejedná se o přihlašovací údaje k databázovému serveru).

1. V relaci SSH spusťte následující příkazy (příkazy můžete vložit pomocí **kombinace kláves CTRL** + **SHIFT** + **V**):

    ```bash
    cd site/wwwroot
    
    # Activate default virtual environment in App Service container
    source /antenv/bin/activate
    # Install packages
    pip install -r requirements.txt
    # Run database migrations
    python manage.py migrate
    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```
    
1. `createsuperuser`Příkaz vás vyzve k zadání přihlašovacích údajů pro uživatele. Pro účely tohoto kurzu použijte výchozí uživatelské jméno `root` , stiskněte klávesu **ENTER** pro e-mailovou adresu, aby byla ponechána prázdná, a zadejte `Pollsdb1` heslo.
    
### <a name="create-a-poll-question-in-the-app"></a>Vytvoření otázky dotazování v aplikaci

1. V prohlížeči otevřete adresu URL *http: \/ / \<app-name> . azurewebsites.NET*. Aplikace by měla zobrazovat zprávu "nejsou k dispozici žádná hlasování", protože v databázi zatím nejsou žádná konkrétní hlasování.

1. Přejděte na *http: \/ / \<app-name> . azurewebsites.NET/admin*. Přihlaste se pomocí přihlašovacích údajů naduživatelem z předchozí části ( `root` a `Pollsdb1` ). V části **cyklické dotazování**vyberte **Přidat** vedle **otázky** a vytvořte otázku dotazování s některými možnostmi.

1. Přejděte znovu na *http: \/ / \<app-name> . azurewebsites.NET/* a potvrďte, že se teď otázky zobrazují uživateli. Odpovězte na otázky, ale chcete v databázi generovat nějaká data.

**Blahopřejeme!** Spouštíte webovou aplikaci Python Django v Azure App Service pro Linux s aktivní databází Postgres.

> [!NOTE]
> App Service detekuje projekt Django hledáním souboru *WSGI.py* v každé podsložce, která se `manage.py startproject` ve výchozím nastavení vytvoří. Když App Service najde tento soubor, načte webovou aplikaci Django. Další informace najdete v tématu [Konfigurace integrované image Pythonu](configure-language-python.md).


## <a name="make-code-changes-and-redeploy"></a>Provádění změn kódu a opětovné nasazení

V této části provedete místní změny aplikace a znovu nasadíte kód do App Service. V tomto procesu nastavíte virtuální prostředí Python, které podporuje průběžnou práci.

### <a name="run-the-app-locally"></a>Místní spuštění aplikace

V okně terminálu spusťte následující příkazy. Při vytváření naduživatelem nezapomeňte postupovat podle pokynů:

# <a name="bash"></a>[bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[PŘEPSAT](#tab/cmd)

```cmd
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install packages
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

Po úplném načtení webové aplikace poskytuje vývojový server Django v této zprávě adresu URL místní aplikace – "spuštění vývojového serveru na adrese http://127.0.0.1:8000/ . Ukončete Server pomocí kombinace kláves CTRL + BREAK.

![Příklad výstupu vývojového serveru Django](./media/tutorial-python-postgresql-app/django-dev-server-output.png)

Otestujte aplikaci místně pomocí následujících kroků:

1. V prohlížeči otevřete *http: \/ /localhost: 8000* , ve kterém by se měla zobrazit zpráva "žádné dotazy nejsou k dispozici". 

1. Přejít na *http: \/ /localhost: 8000/admin* a přihlaste se pomocí uživatele s oprávněními, kterého jste vytvořili dříve. V části **cyklické dotazování**znovu vyberte **Přidat** další **otázky** a vytvořte otázku dotazování s některými možnostmi. 

1. Přejít na *http: \/ /localhost: 8000* znovu a odpovědět na dotaz pro otestování aplikace. 

1. Zastavte Server Django stisknutím **kombinace kláves CTRL** + **C**.

Při místním spuštění aplikace používá místní databázi sqlite3 a nekoliduje s vaší provozní databází. V případě potřeby můžete také místní databázi PostgreSQL použít k lepšímu simulaci provozního prostředí.

### <a name="update-the-app"></a>Aktualizace aplikace

V nástroji `polls/models.py` Najděte řádek, který začíná `choice_text` a změňte `max_length` parametr na 100:

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Vzhledem k tomu, že jste změnili datový model, vytvořte novou migraci Django a migrujte databázi:

```
python manage.py makemigrations
python manage.py migrate
```

Spusťte vývojový server znovu s `python manage.py runserver` a otestujte aplikaci na adrese *http: \/ /localhost: 8000/admin*:

Zastavte webový server Django znovu s **kombinací CTRL** + **C**.

### <a name="redeploy-the-code-to-azure"></a>Opětovné nasazení kódu do Azure

V kořenovém adresáři úložiště spusťte následující příkaz:

```azurecli
az webapp up
```

Tento příkaz používá parametry uložené v mezipaměti v souboru *. Azure/config* . Vzhledem k tomu, že aplikace App Service zjistí, že aplikace již existuje, pouze znovu nasadí kód.

### <a name="rerun-migrations-in-azure"></a>Znovu spustit migrace v Azure

Vzhledem k tomu, že jste provedli změny v datovém modelu, je nutné znovu spustit migrace databáze v App Service.

Otevřete znovu relaci SSH v prohlížeči tak, že přejdete na *https:// \<app-name> . SCM.azurewebsites.NET/webssh/Host*. Potom spusťte následující příkazy:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Kontrola aplikace v produkčním prostředí

Přejděte na *http: \/ / \<app-name> . azurewebsites.NET* a otestujte aplikaci znovu v produkčním prostředí. (Vzhledem k tomu, že jste změnili jenom délku databázového pole, změna se dá poznamenat jenom v případě, že se při vytváření otázky pokusíte zadat delší odpověď.)

## <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

Můžete získat přístup k protokolům konzoly generovaným z kontejneru, který hostuje aplikaci v Azure.

Spuštěním následujícího příkazu rozhraní příkazového řádku Azure zobrazíte datový proud protokolu. Tento příkaz používá parametry uložené v mezipaměti v souboru *. Azure/config* .

```azurecli
az webapp log tail
```

Pokud nevidíte protokoly konzoly okamžitě, podívejte se znovu za 30 sekund.

Pokud chcete streamování protokolů kdykoli zastavit, zadejte **CTRL** + **C**.

> [!NOTE]
> Soubory protokolu můžete také zkontrolovat v prohlížeči na adrese `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .
>
> `az webapp up`zapne pro vás výchozí protokolování. Z důvodu výkonu se toto protokolování po určitou dobu vypne, ale zase se `az webapp up` znovu spustí. Pokud ho chcete zapnout ručně, spusťte následující příkaz:
>
> ```azurecli
> az webapp log config --docker-container-logging filesystem
> ```

## <a name="manage-your-app-in-the-azure-portal"></a>Správa aplikace v Azure Portal

V [Azure Portal](https://portal.azure.com)vyhledejte název aplikace a ve výsledcích vyberte aplikaci.

![V Azure Portal přejděte do vaší aplikace Python Django](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Ve výchozím nastavení na portálu se zobrazí stránka **Přehled** vaší aplikace, která poskytuje obecné zobrazení výkonu. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, restartování a odstranění. Karty na levé straně stránky obsahují různé stránky konfigurace, které můžete otevřít.

![Správa aplikace Python Django na stránce Přehled v Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete aplikaci zachovat nebo pokračovat v dalším kurzu, přeskočte dopředu k [dalším krokům](#next-steps). Jinak, abyste se vyhnuli průběžným poplatkům, můžete odstranit skupinu prostředků vytvořenou pro tento kurz:

```azurecli
az group delete
```

Příkaz používá název skupiny prostředků uložený v souboru *. Azure/config* . Odstraněním skupiny prostředků také zrušíte přidělení a odstraníte všechny prostředky, které jsou v ní obsažené.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak namapovat vlastní název DNS na aplikaci:

> [!div class="nextstepaction"]
> [Kurz: mapování vlastního názvu DNS na aplikaci](app-service-web-tutorial-custom-domain.md)

Přečtěte si, jak App Service spouští aplikaci v Pythonu:

> [!div class="nextstepaction"]
> [Konfigurace aplikace v Pythonu](configure-language-python.md)
