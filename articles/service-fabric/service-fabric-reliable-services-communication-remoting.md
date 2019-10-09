---
title: Vzdálená komunikace služby pomocí C# v Service Fabric | Microsoft Docs
description: Vzdálená komunikace Service Fabric umožňuje klientům a službám komunikovat se C# službami pomocí vzdáleného volání procedur.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 1654a7be8c3aba4efa6fcf96024ea987e2957e73
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173449"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Vzdálená komunikace služby C# v nástroji s Reliable Services

> [!div class="op_single_selector"]
> * [C#ve Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java v systému Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Pro služby, které nejsou vázané na konkrétní komunikační protokol ani zásobník, jako je například webové rozhraní API, Windows Communication Foundation nebo jiné, Reliable Services Framework poskytuje mechanismus vzdálené komunikace pro rychlé a snadné nastavení vzdálených volání procedur pro orgány. Tento článek popisuje, jak nastavit vzdálená volání procedur pro služby napsané pomocí C#.

## <a name="set-up-remoting-on-a-service"></a>Nastavení vzdálené komunikace u služby

Vzdálenou komunikaci pro službu můžete nastavit ve dvou jednoduchých krocích:

1. Vytvořte rozhraní pro implementaci služby. Toto rozhraní definuje metody, které jsou k dispozici pro vzdálené volání procedur ve vaší službě. Metody musí být asynchronní metody vracející úlohy. Rozhraní musí implementovat `Microsoft.ServiceFabric.Services.Remoting.IService` k signalizaci, že služba má rozhraní vzdálené komunikace.
2. V rámci služby použijte naslouchací proces vzdálené komunikace. Naslouchací proces vzdálené komunikace je implementace @no__t 0, která poskytuje funkce vzdálené komunikace. Obor názvů `Microsoft.ServiceFabric.Services.Remoting.Runtime` obsahuje metodu rozšíření `CreateServiceRemotingInstanceListeners` pro bezstavové a stavové služby, které lze použít k vytvoření naslouchacího procesu vzdálené komunikace pomocí výchozího přenosového protokolu pro vzdálenou komunikaci.

>[!NOTE]
>Obor názvů `Remoting` je k dispozici jako samostatný balíček NuGet nazvaný `Microsoft.ServiceFabric.Services.Remoting`.

Například následující Bezstavová služba zpřístupňuje jedinou metodu pro získání "Hello World" prostřednictvím vzdáleného volání procedury.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```

> [!NOTE]
> Argumenty a návratové typy v rozhraní služby mohou být jakékoli jednoduché, komplexní nebo vlastní typy, ale musí být schopny být serializovány rozhraním .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Volání metod vzdálené služby

Volání metod na službu pomocí zásobníku vzdálené komunikace se provádí pomocí místního proxy serveru přes třídu `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy`. Metoda `ServiceProxy` vytvoří místní proxy server pomocí stejného rozhraní, které služba implementuje. Pomocí tohoto proxy serveru můžete volat metody na rozhraní vzdáleně.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Rozhraní vzdálené komunikace šíří výjimky vyvolané službou klientovi. Výsledkem je, že při použití `ServiceProxy`is je klient zodpovědný za zpracování výjimek vyvolaných službou.

## <a name="service-proxy-lifetime"></a>Doba života proxy služby

Vytvoření proxy služby je jednoduchá operace, takže můžete vytvořit tolik, kolik potřebujete. Instance proxy služby se dají znovu použít po dobu potřebnou k jejich použití. Pokud vzdálené volání procedury vyvolá výjimku, můžete přesto použít stejnou instanci proxy. Každý proxy server služby obsahuje komunikačního klienta, který slouží k posílání zpráv přes drát. Při vyvolání vzdálených volání se provádí interní kontroly, které určují, jestli je komunikační klient platný. Na základě výsledků těchto kontrol se komunikační klient v případě potřeby znovu vytvoří. Proto pokud dojde k výjimce, nemusíte znovu vytvářet `ServiceProxy`.

### <a name="service-proxy-factory-lifetime"></a>Doba života objektu proxy služby

[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) je továrna, která vytváří proxy instance pro různá rozhraní vzdálené komunikace. Pokud k vytvoření proxy serveru použijete rozhraní API `ServiceProxyFactory.CreateServiceProxy`, rozhraní vytvoří proxy služby s jedním objektem.
Je vhodné ho vytvořit ručně, když potřebujete přepsat vlastnosti [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) .
Vytváření továrny je náročná operace. Objekt pro vytváření proxy služby udržuje interní mezipaměť komunikačního klienta.
Osvědčeným postupem je, aby objekt pro vytváření objektů proxy služby byl co nejkratší.

## <a name="remoting-exception-handling"></a>Zpracování výjimek vzdálené komunikace

Všechny vzdálené výjimky vyvolané rozhraním API služby se odesílají zpátky klientovi jako AggregateException. Vzdálené výjimky by měly být schopné serializovat pomocí kontraktu dat. Pokud nejsou, rozhraní API proxy vyvolá [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) s chybou serializace.

Proxy služby zpracovává všechny výjimky převzetí služeb při selhání pro oddíl služby, pro který je vytvořen. Pokud jsou výjimky převzetí služeb při selhání (nepřechodnými výjimkami) a opakované pokusy o volání se správným koncovým bodem, přeloží koncové body. Počet opakování pro výjimky převzetí služeb při selhání je nekonečný.
Pokud dojde k přechodným výjimkám, proxy opakuje volání.

Výchozí parametry opakování jsou poskytovány pomocí [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Uživatel může tyto hodnoty nakonfigurovat předáním objektu OperationRetrySettings konstruktoru ServiceProxyFactory.

## <a name="use-the-remoting-v2-stack"></a>Použití zásobníku komunikace v2

Od verze 2,8 balíčku vzdálené komunikace NuGet máte možnost použít sadu vzdálené komunikace v2. Vykonávání zásobníku vzdálené komunikace v2 je lepší. Poskytuje také funkce jako vlastní serializace a další připojitelná rozhraní API.
Kód šablony stále používá sadu vzdálené komunikace v1.
Vzdálená komunikace v2 není kompatibilní s V1 (předchozí zásobník vzdálené komunikace). Postupujte podle pokynů v článku [upgrade z verze V1 na v2](#upgrade-from-remoting-v1-to-remoting-v2) , aby nedocházelo k vlivům na dostupnost služeb.

Pro povolení zásobníku v2 jsou k dispozici následující přístupy.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Použití atributu Assembly pro použití zásobníku v2

Tyto kroky mění kód šablony pro použití zásobníku v2 pomocí atributu Assembly.

1. Změňte prostředek koncového bodu z `"ServiceEndpoint"` na `"ServiceEndpointV2"` v manifestu služby.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Pomocí metody rozšíření `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` můžete vytvořit naslouchací procesy vzdálené komunikace (stejné pro V1 a v2).

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Označte sestavení, které obsahuje rozhraní vzdálené komunikace s atributem `FabricTransportServiceRemotingProvider`.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

V klientském projektu nejsou vyžadovány žádné změny kódu.
Sestavte sestavení klienta se sestavením rozhraní, abyste se ujistili, že je použit atribut Assembly, který byl dříve zobrazen.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Použití explicitních tříd v2 pro použití zásobníku v2

Jako alternativu k použití atributu sestavení může být zásobník v2 také povolen pomocí explicitních tříd v2.

Tyto kroky mění kód šablony pro použití zásobníku v2 pomocí explicitních tříd v2.

1. Změňte prostředek koncového bodu z `"ServiceEndpoint"` na `"ServiceEndpointV2"` v manifestu služby.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Použijte [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) z oboru názvů `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime`.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
   ```

3. Pro vytváření klientů použijte [FabricTransportServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) z oboru názvů `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client`.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Upgrade ze vzdálené verze V1 na vzdálenou verzi v2

Aby bylo možné upgradovat z verze V1 na verzi 2, jsou vyžadovány dva kroky upgradu. Postupujte podle kroků v této sekvenci.

1. Upgradujte službu V1 na verzi v2 pomocí tohoto atributu.
Tato změna zajistí, že služba naslouchá na naslouchací službě V1 a v2.

    a. Přidejte prostředek koncového bodu s názvem "ServiceEndpointV2" v manifestu služby.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Pomocí následující metody rozšíření vytvořte naslouchací proces vzdálené komunikace.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    r. Přidejte atribut assembly do rozhraní vzdálené komunikace, aby používal naslouchací proces V1 a v2 a klienta v2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Upgradujte klienta V1 na klienta v2 pomocí atributu klienta v2.
Tento krok zajistí, že klient používá sadu v2 Stack.
V klientském projektu/službě se nevyžaduje žádná změna. Vytváření klientských projektů s aktualizovaným sestavením rozhraní je dostatečné.

3. Tento krok je nepovinný. Použijte atribut naslouchacího procesu v2 a potom upgradujte službu v2.
Tento krok zajistí, že služba naslouchá jenom na naslouchací službě v2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>Použití zásobníku vzdálené komunikace v2 (rozhraní kompatibilní s rozhraním)

 Zásobník vzdálené komunikace v2 (rozhraní kompatibilní se systémem, který se označuje jako V2_1) obsahuje všechny funkce sady vzdálené komunikace v2. Jeho zásobník rozhraní je kompatibilní se zásobníkem vzdálené komunikace V1, ale není zpětně kompatibilní s v2 a v1. Pokud chcete upgradovat z verze V1 na V2_1, aniž by to ovlivnilo dostupnost služby, postupujte podle kroků v článku Upgrade z verze V1 na v2 (rozhraní kompatibilní s rozhraním).


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Použití atributu Assembly pro použití zásobníku vzdálené komunikace v2 (rozhraní kompatibilní s rozhraním)

Pomocí těchto kroků můžete změnit V2_1 Stack.

1. Přidejte prostředek koncového bodu s názvem "ServiceEndpointV2_1" v manifestu služby.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Pomocí metody rozšíření vzdálené komunikace vytvořte naslouchací proces vzdálené komunikace.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Přidejte [atribut Assembly](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) na rozhraních vzdálené komunikace.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

V klientském projektu nejsou vyžadovány žádné změny.
Sestavte sestavení klienta se sestavením rozhraní, abyste se ujistili, že se používá předchozí atribut Assembly.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Použití explicitních tříd vzdálené komunikace k vytvoření naslouchacího procesu/továrny klienta pro verzi v2 (kompatibilní s rozhraním)

Postupujte podle těchto kroků:

1. Přidejte prostředek koncového bodu s názvem "ServiceEndpointV2_1" v manifestu služby.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Použijte [naslouchací proces vzdálené komunikace v2](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Výchozí použitý název prostředku koncového bodu služby je "ServiceEndpointV2_1". Musí být definován v manifestu služby.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
   ```

3. Použijte [klientskou továrnu](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet)v2.
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Upgrade ze vzdálené verze V1 na vzdálenou verzi v2 (kompatibilní s rozhraním)

Aby bylo možné upgradovat z verze V1 na v2 (rozhraní kompatibilní s rozhraním, označované jako V2_1), jsou vyžadovány dva kroky upgradu. Postupujte podle kroků v této sekvenci.

> [!NOTE]
> Při upgradu z verze V1 na v2 se ujistěte, že je obor názvů `Remoting` aktualizovaný na použití verze v2. Příklad: Microsoft. ServiceFabric. Services. Vzdálená komunikace. v2. FabricTransport. Client
>
>

1. Upgradujte službu V1 na službu V2_1 pomocí následujícího atributu.
Tato změna zajistí, že služba naslouchá na V1 a naslouchací službě V2_1.

    a. Přidejte prostředek koncového bodu s názvem "ServiceEndpointV2_1" v manifestu služby.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Pomocí následující metody rozšíření vytvořte naslouchací proces vzdálené komunikace.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    r. Přidejte atribut Assembly na rozhraních vzdálené komunikace, abyste mohli používat klienta V1, V2_1 Listener a klienta V2_1.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Upgradujte klienta V1 na klienta V2_1 pomocí atributu klienta V2_1.
Tento krok zajistí, že klient používá V2_1 Stack.
V klientském projektu/službě se nevyžaduje žádná změna. Vytváření klientských projektů s aktualizovaným sestavením rozhraní je dostatečné.

3. Tento krok je nepovinný. Odeberte verzi naslouchacího procesu v1 z atributu a poté proveďte upgrade služby v2.
Tento krok zajistí, že služba naslouchá jenom na naslouchací službě v2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Použití vlastní serializace se zprávou zabalené vzdálené komunikace

U zprávy zabalené vzdálené komunikace vytvoříme jeden zabalený objekt se všemi parametry jako pole v něm.
Postupujte podle těchto kroků:

1. Implementujte rozhraní @no__t 0 k poskytnutí implementace pro vlastní serializaci.
    Tento fragment kódu ukazuje, jak implementace vypadá jako.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2. Pro naslouchací proces vzdálené komunikace přepište výchozího zprostředkovatele serializace hodnotou `JsonSerializationProvider`.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
   ```

3. Přepište výchozího zprostředkovatele serializace hodnotou `JsonSerializationProvider` u objektu pro vzdálenou komunikaci klienta.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Další kroky

* [Webové rozhraní API s OWIN v Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Windows Communication Foundation komunikace s Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Zabezpečená komunikace pro Reliable Services](service-fabric-reliable-services-secure-communication.md)
