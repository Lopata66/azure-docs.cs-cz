---
title: Zasílání oznámení určitým zařízením (Univerzální platforma Windows) | Microsoft Docs
description: Pomocí Azure Notification Hubs se značkami v registraci můžete odesílat nejnovější zprávy do aplikace pro Univerzální platformu Windows.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: efe668e42e04942cc0d9fc99670057ab5bdd302a
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212126"
---
# <a name="tutorial-push-notifications-to-specific-windows-devices-running-universal-windows-platform-applications"></a>Kurz: Nabízená oznámení na konkrétní zařízení s Windows, na kterých běží aplikace Univerzální platforma Windows

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Přehled

V tomto kurzu zjistíte, jak používat Azure Notification Hubs k vysílání oznámení o nejnovějších zprávách do aplikací pro Windows Store a Windows Phone 8.1 (bez Silverlight). Pokud cílíte na platformu Windows Phone 8.1 Silverlight, podívejte se na kurz pro verzi [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

V tomto kurzu zjistíte, jak pomocí služby Azure Notification Hubs odesílat nabízená oznámení určitým zařízením Windows, na kterých běží aplikace pro Univerzální platformu Windows (UPW). Po dokončení kurzu si můžete zaregistrovat kategorie nejnovějších zpráv, které vás zajímají, a budete dostávat nabízená oznámení, která se týkají jenom těchto kategorií.

Scénáře vysílání povolíte tak, že při vytvoření registrace v centru oznámení přidáte jednu nebo více *značek*. Při posílání oznámení značce toto oznámení přijde všem zařízením, která si danou značku zaregistrovala. Další informace o značkách najdete v článku o [značkách používaných k registraci](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> Windows Store a verze projektu Windows Phone 8.1 a dřívější nejsou v sadě Visual Studio 2017 podporované. Další informace najdete v tématu [Cílení na platformy a kompatibilita v sadě Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Přidání výběru kategorií do mobilní aplikace
> * Registrace oznámení
> * Posílání značených oznámení
> * Spuštění aplikace a generování oznámení

## <a name="prerequisites"></a>Požadavky

Dokončete [kurz: Než začnete s tímto kurzem, pošlete][get-started] oznámení do Univerzální platforma Windows aplikací pomocí Azure Notification Hubs.  

## <a name="add-category-selection-to-the-app"></a>Přidání výběru kategorií do aplikace

První krok spočívá v přidání prvků uživatelského rozhraní na stávající hlavní stránku, aby uživatelé mohli vybrat kategorie, které chtějí zaregistrovat. Vybrané kategorie se uloží do zařízení. Při spuštění aplikace se v centru oznámení provede registrace zařízení s vybranými kategoriemi v podobě značek.

1. Otevřete soubor projektu MainPage. XAML a poté zkopírujte následující kód v `Grid` elementu:

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
    </Grid>
    ```

2. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a přidejte novou třídu: **Oznámení**. Do definice třídy přidejte modifikátor **Public** a přidejte následující `using` příkazy do nového souboru kódu:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

3. Zkopírujte následující kód do nové `Notifications` třídy:

    ```csharp
    private NotificationHub hub;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        return await SubscribeToCategories(categories);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
        return categories != null ? categories.Split(','): new string[0];
    }

    public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                categories);
    }
    ```

    Tato třída ukládá kategorie novinek, které bude zařízení dostávat, do místního úložiště. Namísto volání `RegisterNativeAsync` metody zavolejte `RegisterTemplateAsync` k registraci pro kategorie pomocí registrace šablony.

    Pokud chcete zaregistrovat více než jednu šablonu (například jednu pro informační oznámení a druhou pro dlaždice), zadejte název šablony (například simpleWNSTemplateExample). Názvy šablon se zadávají proto, aby je bylo možné aktualizovat nebo odstranit.

    >[!NOTE]
    >Pokud si zařízení zaregistruje více šablon se stejnou značkou, příchozí zpráva cílící na danou značku způsobí, že se do daného zařízení doručí více oznámení (pro každou šablonu jedno). Toto chování je užitečné, pokud má mít stejná logická zpráva za následek více vizuálních oznámení (například zobrazení oznámení i informační zprávy v aplikaci pro Windows Store).

    Další informace najdete v tématu [Šablony](notification-hubs-templates-cross-platform-push-messages.md).

4. V souboru projektu app.XAML.cs přidejte do `App` třídy následující vlastnost:

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    Tato vlastnost slouží k vytvoření instance a k jejímu `Notifications` přístupu.

    Nahraďte v kódu zástupné symboly `<hub name>` a `<connection string with listen access>` názvem vašeho centra oznámení a připojovacím řetězcem pro *DefaultListenSharedAccessSignature*, který jste získali dříve.

   > [!NOTE]
   > Přihlašovací údaje distribuované s klientskou aplikací obvykle nejsou zabezpečené, a proto s klientskou aplikací distribuujte jenom přístupový klíč pro *naslouchání*. Přístup pro naslouchání umožňuje aplikaci registrovat oznámení, ale neumožňuje měnit stávající registrace ani odesílat oznámení. Přístupový klíč pro úplný přístup se používá v zabezpečené službě back-end k posílání oznámení a změně stávajících registrací.

5. `MainPage.xaml.cs` Do souboru přidejte následující řádek:

    ```csharp
    using Windows.UI.Popups;
    ```

6. `MainPage.xaml.cs` Do souboru přidejte následující metodu:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```

    Tato metoda vytvoří seznam kategorií a pomocí `Notifications` třídy uloží seznam do místního úložiště. Dále zaregistruje odpovídající značky v centru oznámení. Při změně kategorií se registrace vytvoří znovu s novými kategoriemi.

Aplikace teď může uchovávat sadu kategorií v místním úložišti v zařízení. Aplikace se zaregistruje v centru oznámení vždy, když uživatelé změní výběr kategorií.

## <a name="register-for-notifications"></a>Registrace oznámení

V této části provedete registraci v centru oznámení při spuštění pomocí kategorií, které jste uložili v místním úložišti.

> [!NOTE]
> Identifikátor URI kanálu přiřazený Službou nabízených oznámení Windows se může kdykoli změnit, a proto byste měli oznámení často registrovat, abyste se vyhnuli chybám v oznámeních. V tomto příkladu se oznámení registrují při každém spuštění aplikace. Pokud spouštíte aplikace často (více než jednou denně), můžete kvůli úspoře šířky pásma registraci pravděpodobně přeskočit, pokud od předchozí registrace uplynul méně než jeden den.

1. Chcete-li `notifications` použít třídu k přihlášení k odběru na základě kategorií, otevřete soubor App.XAML.cs a pak `InitNotificationsAsync` aktualizujte metodu.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Tento proces zajistí, aby aplikace při spuštění načetla kategorie z místního úložiště a vyžadovala registraci těchto kategorií. Tuto `InitNotificationsAsync` metodu jste vytvořili jako součást kurzu [Začínáme s Notification Hubs][get-started] .
2. `OnNavigatedTo` V souboru `MainPage.xaml.cs` projektu přidejte následující kód do metody:

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;
    }
    ```

    Tento kód aktualizuje hlavní stránku na základě stavu dříve uložených kategorií.

Aplikace je teď hotová. Může uchovávat sadu kategorií v místním úložišti zařízení, které se používá k registraci v centru oznámení, když uživatelé změní výběr kategorií. V další části definujete back-end, který aplikaci posílá oznámení kategorií.

## <a name="run-the-uwp-app"></a>Spuštění aplikace pro UWP 
1. Pokud chcete v sadě Visual Studio kompilovat aplikaci a spustit ji, stiskněte **F5**. Uživatelské rozhraní aplikace nabízí sadu přepínačů, kterými můžete vybrat kategorie přihlášené k odběru.

    ![Aplikace Nejnovější zprávy](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png)

2. Zapněte jeden nebo více přepínačů kategorií a klikněte na **Přihlásit k odběru**.

    Aplikace převede vybrané kategorie na značky a u vybraných značek požádá centrum oznámení o registraci nových zařízení. Zaregistrované kategorie se vrátí a zobrazí v dialogovém okně.

    ![Přepínače kategorií a tlačítko Přihlásit k odběru](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Vytvoření konzolové aplikace pro odesílání oznámení s příznakem

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>Spuštění konzolové aplikace pro posílání oznámení s příznakem

1. Spusťte aplikaci vytvořenou v předchozí části.
2. Oznámení pro vybrané kategorie se zobrazí jako informační zprávy. Pokud vyberete oznámení, zobrazí se první okno aplikace UWP. 

     ![Informační zprávy](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png)


## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak vysílat nejnovější zprávy podle kategorií. Back-end aplikace odesílá značená oznámení zařízením, která jsou zaregistrovaná pro příjem oznámení s danou značkou. Pokud se chcete naučit posílat nabízená oznámení určitým uživatelům bez ohledu na to, jaká zařízení používají, pokračujte následujícím kurzem:

> [!div class="nextstepaction"]
> [Odesílání lokalizovaných oznámení](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- URLs.-->
[get-started]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: https://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
