---
title: zahrnout soubor
description: zahrnout soubor
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
origin.date: 03/30/2018
ms.date: 04/08/2019
ms.author: v-biyu
ms.custom: include file
ms.openlocfilehash: ce8496596f25b85719b8a6dff849ebf0fc3e5dc3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560638"
---
V této části odešlete nejnovější zprávy jako šablonové oznámení se značkami z konzolové aplikace .NET. 

1. V sadě Visual Studio vytvořte nový vizuál C# konzolové aplikace:. V nabídce vyberte **souboru** > **nový** > **projektu**.
    b. Rozbalte **Visual C#** a vyberte **Windows Desktop**. 
    c. Vyberte **Konzolová aplikace (.NET Framework)** v seznamu šablon. 
    d. Zadejte **název** pro aplikaci. 
    e. Vyberte **složky** pro aplikaci.
    f. Vyberte **OK** a vytvořte projekt. 
2. V hlavní nabídce sady Visual Studio, vyberte **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků** a poté v okně konzoly Zadejte následující řetězec:
   
    ```
    Install-Package Microsoft.Azure.NotificationHubs
    ```
   
3. Stiskněte **Enter**.  
    Tato akce přidá referenci na sadu SDK služby Azure Notification Hubs pomocí [Balíček NuGet Microsoft.Azure.Notification Hubs].

4. Otevřete soubor Program.cs a přidejte následující příkaz `using`:
   
    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

5. Do třídy `Program` přidejte následující metodu, nebo ji nahraďte, pokud už existuje:
   
    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```   
   
    Tento kód odešle šablonové oznámení pro každou z šesti značek v poli řetězců. Použití značek zajišťuje, že zařízení budou přijímat oznámení jenom pro registrované kategorie.

5. V předchozím kódu nahraďte zástupné symboly `<hub name>` a `<connection string with full access>` názvem vašeho centra oznámení a připojovacím řetězcem pro *DefaultFullSharedAccessSignature* z řídicího panelu vašeho centra oznámení.

6. Do metody **Main** přidejte následující řádky:
   
    ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

7. Sestavte konzolovou aplikaci.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Balíček NuGet Microsoft.Azure.Notification Hubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
