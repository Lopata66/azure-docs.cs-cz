---
title: 'Rychlý Start: použití mezipaměti Azure pro Redis v .NET Framework'
description: V tomto rychlém startu se dozvíte, jak získat přístup k Azure cache pro Redis z aplikací .NET.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp, mvc
ms.date: 06/18/2020
ms.openlocfilehash: 762fdf0aab0077cfbf8beceeb432dc85695e4176
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002446"
---
# <a name="quickstart-use-azure-cache-for-redis-in-net-framework"></a>Rychlý Start: použití mezipaměti Azure pro Redis v .NET Framework

V tomto rychlém startu zahrňte Azure cache pro Redis do aplikace .NET Framework, abyste měli přístup k zabezpečené vyhrazené mezipaměti, která je přístupná z libovolné aplikace v Azure. Konkrétně použijete klienta [stackexchange. Redis](https://github.com/StackExchange/StackExchange.Redis) s kódem C# v aplikaci konzoly .NET.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Visual Studio 2019](https://www.visualstudio.com/downloads/)
- [.NET Framework 4 nebo vyšší](https://www.microsoft.com/net/download/dotnet-framework-runtime), které vyžaduje klient stackexchange. Redis.

## <a name="create-a-cache"></a>Vytvoření mezipaměti
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Vytvořte v počítači soubor s názvem *TajnéKódyMezipaměti.config* a uložte ho do umístění, které se nebude vracet se zdrojovým kódem této ukázkové aplikace. V tomto rychlém startu je soubor *TajnéKódyMezipaměti.config* umístěný zde: *C:\AppSecrets\TajnéKódyMezipaměti.config*.

Upravte soubor *TajnéKódyMezipaměti.config* a přidejte do něj následující obsah:

```xml
<appSettings>
    <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,allowAdmin=true,password=<access-key>"/>
</appSettings>
```

`<cache-name>` nahraďte názvem hostitele mezipaměti.

`<access-key>` nahraďte primárním klíčem mezipaměti.


## <a name="create-a-console-app"></a>Vytvoření konzolové aplikace

V aplikaci Visual Studio klikněte na **soubor**  >  **Nový**  >  **projekt**.

Vyberte **Konzolová aplikace (.NET Framework)** a **vedle** konfigurace aplikace. Zadejte **název projektu** a kliknutím na **vytvořit** vytvořte novou konzolovou aplikaci.

<a name="configure-the-cache-clients"></a>

## <a name="configure-the-cache-client"></a>Konfigurace klienta mezipaměti

V této části nakonfigurujete konzolovou aplikaci, aby pro .NET používala klienta [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

V aplikaci Visual Studio klikněte na **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků** a spusťte následující příkaz z okna konzoly Správce balíčků.

```powershell
Install-Package StackExchange.Redis
```

Po dokončení instalace budete moct se svým projektem používat klienta mezipaměti *StackExchange.Redis*.


## <a name="connect-to-the-cache"></a>Připojení k mezipaměti

V sadě Visual Studio otevřete soubor *App.config* a aktualizujte ho tak, aby obsahoval atribut `appSettings` `file` odkazující na soubor *CacheSecrets.config*.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.1" />
    </startup>

    <appSettings file="C:\AppSecrets\CacheSecrets.config"></appSettings>
</configuration>
```

V Průzkumníku řešení klikněte pravým tlačítkem myši na **Odkazy** a klikněte na **Přidat odkaz**. Přidejte odkaz na sestavení **System.Configuration**.

Do souboru *Program.cs* přidejte následující příkazy `using`:

```csharp
using StackExchange.Redis;
using System.Configuration;
```

Připojení k mezipaměti Azure pro Redis je spravováno `ConnectionMultiplexer` třídou. V klientské aplikaci byste měli tuto třídu sdílet a opakovaně používat. Nevytvářejte pro každou operaci nové připojení. 

Neuchovávejte přihlašovací údaje ve zdrojovém kódu. Pro zjednodušení této ukázky používám pouze konfigurační soubor externích tajných kódů. Lepším přístupem může být použití řešení [Azure Key Vault s certifikáty](/rest/api/keyvault/certificate-scenarios).

V souboru *Program.cs* přidejte k třídě `Program` konzolové aplikace následující členy:

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
    return ConnectionMultiplexer.Connect(cacheConnection);
});

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```


Tento přístup ke sdílení instance `ConnectionMultiplexer` v aplikaci používá statickou vlastnost, která vrací připojenou instanci. Tento kód poskytuje způsob inicializace pouze jedné připojené instance `ConnectionMultiplexer`, který je bezpečný pro přístup z více vláken. `abortConnect` je nastavené na false, což znamená, že volání je úspěšné i v případě, že není navázáno připojení k mezipaměti Azure pro Redis. Klíčovou vlastností `ConnectionMultiplexer` je automatické obnovení připojení k mezipaměti po vyřešení problémů se sítí nebo jiných příčin.

Hodnota *CacheConnection* appSetting se používá k odkazování na připojovací řetězec mezipaměti z webu Azure Portal ve formě parametru hesla.

## <a name="executing-cache-commands"></a>Provádění příkazů mezipaměti

Pro proceduru `Main` třídy `Program` konzolové aplikace přidejte následující kód:

```csharp
static void Main(string[] args)
{
    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...

    // Simple PING command
    string cacheCommand = "PING";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

    // Simple get and put of integral data types into the cache
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    cacheCommand = "SET Message \"Hello! The cache is working from a .NET console app!\"";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
    Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET console app!").ToString());

    // Demonstrate "SET Message" executed as expected...
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    // Get the client list, useful to see if connection list is growing...
    // Note that this requires the allowAdmin=true
    cacheCommand = "CLIENT LIST";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    var endpoint = (System.Net.DnsEndPoint) Connection.GetEndPoints()[0];
    var server = Connection.GetServer(endpoint.Host, endpoint.Port);

    var clients = server.ClientList(); 
    Console.WriteLine("Cache response :");
    foreach (var client in clients)
    {
        Console.WriteLine(client.Raw);
    }

    lazyConnection.Value.Dispose();
}
```

Azure cache pro Redis má konfigurovatelný počet databází (výchozí hodnota je 16), který se dá použít k logickému oddělení dat v mezipaměti Azure pro Redis. Kód se připojí k výchozí databázi DB 0. Další informace najdete v tématu [Co jsou databáze Redis?](cache-development-faq.md#what-are-redis-databases) a [Výchozí konfigurace serveru Redis](cache-configure.md#default-redis-server-configuration).

Položky v mezipaměti lze ukládat a načítat pomocí metod `StringSet` a `StringGet`.

Redis ukládá většinu dat jako řetězce Redis, ale tyto řetězce mohou obsahovat mnoho typů dat, včetně serializovaných binárních dat, která lze použít při ukládání objektů .NET v mezipaměti.

Stisknutím kombinace kláves **Ctrl+F5** sestavíte a spustíte konzolovou aplikaci.

V níže uvedeném příkladu můžete vidět, že klíč `Message` měl předtím hodnotu z mezipaměti, která byla nastavena pomocí konzoly Redis na webu Azure Portal. Aplikace tuto hodnotu z mezipaměti aktualizovala. Aplikace rovněž spustila příkazy `PING` a `CLIENT LIST`.

![Částečně provedený kód konzolové aplikace](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>Práce s objekty .NET v mezipaměti

Mezipaměť Azure pro Redis může ukládat do mezipaměti objekty .NET i primitivní datové typy, ale před tím, než může být objekt .NET uložen do mezipaměti, musí být serializován. Serializaci objektů .NET má na starosti vývojář aplikace, kterému je tak poskytnuta flexibilita při výběru serializátoru.

Jeden snadný způsob, jak serializovat objekty, je použít metody serializace `JsonConvert` v balíčku [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) a serializovat a deserializovat tak objekty do a z formátu JSON. V této části přidáte do mezipaměti objekt .NET.

V aplikaci Visual Studio klikněte na **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků** a spusťte následující příkaz z okna konzoly Správce balíčků.

```powershell
Install-Package Newtonsoft.Json
```

Na začátek souboru *Program.cs* přidejte následující příkaz `using`:

```csharp
using Newtonsoft.Json;
```

Do souboru *Program.cs* přidejte následující definici třídy `Employee`:

```csharp
class Employee
{
    public string Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }

    public Employee(string EmployeeId, string Name, int Age)
    {
        this.Id = EmployeeId;
        this.Name = Name;
        this.Age = Age;
    }
}
```

Na konec procedury `Main()` a před volání metody `Dispose()` v souboru *Program.cs* přidejte do mezipaměti následující řádky kódu a získejte serializovaný objekt .NET:

```csharp
    // Store .NET object to cache
    Employee e007 = new Employee("007", "Davide Columbo", 100);
    Console.WriteLine("Cache response from storing Employee .NET object : " + 
    cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

    // Retrieve .NET object from cache
    Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
    Console.WriteLine("Deserialized Employee .NET object :\n");
    Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
    Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
    Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

Stisknutím kombinace kláves **Ctrl+F5** sestavte a spusťte konzolovou aplikaci a otestujte serializaci objektů .NET. 

![Dokončená konzolová aplikace](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kurzu, můžete prostředky vytvořené v tomto rychlém startu zachovat a znovu je použít.

V opačném případě, pokud jste už s ukázkovou aplikací v tomto rychlém startu skončili, můžete prostředky Azure vytvořené v tomto rychlém startu odstranit, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a skupina prostředků včetně všech v ní obsažených prostředků bude trvale odstraněna. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky ve stávající skupině prostředků obsahující prostředky, které chcete zachovat, můžete místo odstranění skupiny prostředků odstranit jednotlivé prostředky z jejich odpovídajících oken.
>

Přihlaste se na web [Azure Portal](https://portal.azure.com) a klikněte na **Skupiny prostředků**.

Do textového pole **Filtrovat podle názvu** zadejte název vaší skupiny prostředků. V pokynech v tomto článku se používala skupina prostředků *TestResources*. Ve výsledcích hledání klikněte na **...** u vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.

![Odstranit](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-delete-resource-group.png)

Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění zadáním názvu vaší skupiny prostředků a klikněte na **Odstranit**.

Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.



<a name="next-steps"></a>

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak používat Azure cache pro Redis z aplikace .NET. Přejděte k dalšímu rychlému startu a použijte Azure cache pro Redis s webovou aplikací ASP.NET.

> [!div class="nextstepaction"]
> [Vytvořte webovou aplikaci ASP.NET, která používá Azure cache pro Redis.](./cache-web-app-howto.md)

Chcete optimalizovat a uložit své útraty do cloudu?

> [!div class="nextstepaction"]
> [Zahájení analýzy nákladů pomocí Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)