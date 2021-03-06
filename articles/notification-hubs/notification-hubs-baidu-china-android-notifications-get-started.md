---
title: Začínáme s používáním Azure Notification Hubs s Baidu | Dokumentace Microsoftu
description: V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do zařízení se systémem Android pomocí Baidu.
services: notification-hubs
documentationcenter: android
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: mobile-baidu
ms.workload: mobile
ms.date: 03/18/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 06/19/2019
ms.custom: devx-track-java, devx-track-csharp
ms.openlocfilehash: 13b05c17cc621660576913c08a1fdbc389365a53
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009096"
---
# <a name="get-started-with-notification-hubs-using-baidu"></a>Začínáme s použitím Notification Hubs pomocí Baidu

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Nabídka cloudu Baidu představuje čínskou cloudovou službu, kterou můžete použít k zasílání nabízených oznámení na mobilní zařízení.

Vzhledem k nedostupnosti obchodu Google Play a služby Firebase Cloud Messaging (FCM) v Číně je nutné pro aplikace používat jiné obchody a služby nabízených oznámení. Baidu je jednou z nich a aktuálně ji používá Centrum oznámení.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu budete potřebovat:

* Sadu Android SDK (předpokládáme, že používáte Android Studio), kterou si můžete stáhnout z [webu Android](https://go.microsoft.com/fwlink/?LinkId=389797)
* [Baidu Push Android SDK]

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).

Chcete-li začít, postupujte následovně:

1. Vytvořte účet Baidu.
2. Vytvořte projekt nabízená oznámení cloudu Baidu a poznamenejte si klíč rozhraní API a tajný klíč.

## <a name="configure-a-new-notification-hub"></a>Konfigurace nového centra oznámení

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. Ve vašem centru oznámení vyberte **Služby oznámení** a pak **Baidu (Android China)**.

&emsp;&emsp;&emsp;&emsp;![Azure Notification Hubs – Baidu](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

&emsp;&emsp;7. Posuňte se dolů do části nastavení oznámení Baidu. Do projektu nabízených oznámení cloudu Baidu zadejte klíč rozhraní API a tajný klíč, které jste získali z konzoly Baidu. Potom klikněte na Uložit.

&emsp;&emsp;&emsp;&emsp;![Azure Notification Hubs – Tajné kódy Baidu](./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png)

Vaše centrum oznámení je teď nakonfigurované pro práci s Baidu. Máte také **připojovací řetězce** pro registraci aplikace k odesílání i příjmu nabízených oznámení.

Poznamenejte si `DefaultListenSharedAccessSignature` a `DefaultFullSharedAccessSignature` z okna Informace o přístupovém připojení.

## <a name="connect-your-app-to-the-notification-hub"></a>Připojte aplikaci k centru oznámení

1. V nástroji Android Studio vytvořte nový projekt pro Android (Soubor > Nový > Nový projekt).

    ![Azure Notification Hubs – Nový projekt Baidu](./media/notification-hubs-baidu-get-started/AndroidNewProject.png)

2. Zadejte Název aplikace a ověřte, že Minimální požadovaná verze sady SDK je nastavená na API 16: Android 4.1. **Ověřte prosím také, že název vašeho balíčku (应用包名) je stejný jako na portálu nabízených oznámení cloudu Baidu.**

    ![Azure Notification Hubs – Baidu min SDK1 ](./media/notification-hubs-baidu-get-started/AndroidMinSDK.png) ![ Azure Notification Hubs-BAIDU min SDK2](./media/notification-hubs-baidu-get-started/AndroidMinSDK2.png)

3. Klikněte na Další a pokračujte podle pokynů průvodce, dokud se nezobrazí okno Vytvořit aktivitu. Ujistěte se, že je zvolena možnost Prázdná aktivita, a nakonec vyberte Dokončit a vytvořte novou aplikaci pro Android.

    ![Azure Notification Hubs – Přidání aktivity Baidu](./media/notification-hubs-baidu-get-started/AndroidAddActivity.png)

4. Ujistěte se, že je správně nastavená možnost Cíl sestavení projektu.

5. Pak přidejte knihovny Azure Notification Hubs. Do souboru `Build.Gradle` pro aplikaci přidejte následující řádky v části obsahující závislosti.

    ```javascript
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

    Přidejte následující úložiště za část obsahující závislosti.

    ```javascript
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

    Chcete-li se vyhnout konfliktu seznamu, přidejte do souboru projektu následující kód `Manifest.xml` :

    ```xml
    <manifest package="YOUR.PACKAGE.NAME"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:android="http://schemas.android.com/apk/res/android">
    ```

    A do značky `<application/>` musíme přidat:

    ```xml
    <application
        tools:replace="android:allowBackup,icon,theme,label">
    ```

6. Stáhněte a rozbalte sadu [Baidu Push Android SDK](https://push.baidu.com/doc/android/api). Zkopírujte soubor `pushservice-x.y.z jar` do složky libs. Pak zkopírujte soubory s příponou `.so` do složek `src/main/jniLibs` (vytvořte novou složku) vaší aplikace pro Android.

    ![Azure Notification Hubs – Knihovny sady Baidu SDK](./media/notification-hubs-baidu-get-started/BaiduSDKLib.png)

7. Ve složce projektu klikněte `libs` pravým tlačítkem na `pushervice-x.y.z.jar` soubor; vyberte **Přidat jako knihovnu** , která zahrnuje tuto knihovnu v projektu.

    ![Azure Notification Hubs – Přidání jako knihovny pro Baidu](./media/notification-hubs-baidu-get-started/BaiduAddAsALib.jpg)

8. Otevřete soubor projektu Android `AndroidManifest.xml` a přidejte oprávnění požadovaná sadou BAIDU SDK. **Nahraďte `YOURPACKAGENAME` názvem vašeho balíčku.**

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
    <uses-permission android:name="android.permission.WRITE_SETTINGS" />
    <uses-permission android:name="android.permission.VIBRATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
    <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />
    <uses-permission android:name="android.permission.EXPAND_STATUS_BAR" />
    !! <uses-permission android:name="baidu.push.permission.WRITE_PUSHINFOPROVIDER.YOURPACKAGENAME" />
    !!<permission android:name="baidu.push.permission.WRITE_PUSHINFOPROVIDER.YOURPACKAGENAME"android:protectionLevel="normal" />

    ```

9. Přidejte následující konfiguraci v rámci elementu aplikace po elementu aktivity `.MainActivity` a nahraďte řetězec *yourprojectname* názvem vašeho projektu (například `com.example.BaiduTest`):

    ```xml
    <activity
        android:name="com.baidu.android.pushservice.richmedia.MediaViewActivity"
        android:configChanges="orientation|keyboardHidden"
        android:label="MediaViewActivity" />
    <activity
        android:name="com.baidu.android.pushservice.richmedia.MediaListActivity"
        android:configChanges="orientation|keyboardHidden"
        android:label="MediaListActivity"
        android:launchMode="singleTask" />

    <!-- Push application definition message -->
    <receiver android:name=".MyPushMessageReceiver">
        <intent-filter>

            <!-- receive push message-->
            <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
            <!-- receive bind,unbind,fetch,delete.. message-->
            <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
            <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
        </intent-filter>
    </receiver>

    <receiver
        android:name="com.baidu.android.pushservice.PushServiceReceiver"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
            <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
            <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            <action android:name="com.baidu.android.pushservice.action.media.CLICK" />
            <action android:name="android.intent.action.MEDIA_MOUNTED" />
            <action android:name="android.intent.action.USER_PRESENT" />
            <action android:name="android.intent.action.ACTION_POWER_CONNECTED" />
            <action android:name="android.intent.action.ACTION_POWER_DISCONNECTED" />
        </intent-filter>
    </receiver>

    <receiver
        android:name="com.baidu.android.pushservice.RegistrationReceiver"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="com.baidu.android.pushservice.action.METHOD" />
            <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
        </intent-filter>
        <intent-filter>
            <action android:name="android.intent.action.PACKAGE_REMOVED" />

            <data android:scheme="package" />
        </intent-filter>
    </receiver>

    <service
        android:name="com.baidu.android.pushservice.PushService"
        android:exported="true"
        android:process=":bdservice_v1">
        <intent-filter>
            <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
        </intent-filter>
    </service>

    <service
        android:name="com.baidu.android.pushservice.CommandService"
        android:exported="true" />

    <!-- Adapt the ContentProvider declaration required for the Android N system, and the write permissions include the application package name-->
    <provider
        android:name="com.baidu.android.pushservice.PushInfoProvider"
        android:authorities="com.baidu.push.example.bdpush"
        android:exported="true"
        android:protectionLevel="signature"
        android:writePermission="baidu.push.permission.WRITE_PUSHINFOPROVIDER. yourprojectname  " />

    <!-- API Key of the Baidu application -->
    <meta-data
        android:name="api_key"
        !!   android:value="api_key" />
    </application>
    ```

10. Přidejte do projektu novou třídu s názvem `ConfigurationSettings.java`.

    ```java
    public class ConfigurationSettings {
        public static String API_KEY = "...";
        public static String NotificationHubName = "...";
        public static String NotificationHubConnectionString = "...";
    }
    ```

    Nastavte hodnotu řetězce `API_KEY` na klíč rozhraní API z cloudového projektu Baidu.

    Nastavte hodnotu řetězce `NotificationHubName` na název vašeho centra oznámení z webu [Azure Portal] a pak nastavte hodnotu `NotificationHubConnectionString` na hodnotu `DefaultListenSharedAccessSignature` z webu [Azure Portal].

11. Otevřete soubor MainActivity.java a přidejte následující kód do metody onCreate:

    ```java
    PushManager.startWork(this, PushConstants.LOGIN_TYPE_API_KEY,  API_KEY );
    ```

12. Přidejte novou třídu s názvem `MyPushMessageReceiver.java` a přidejte do ní následující kód. Tato třída zpracovává nabízená oznámení přijatá ze serveru nabízených oznámení Baidu.

    ```java
    package your.package.name;

    import android.content.Context;
    import android.content.Intent;
    import android.os.AsyncTask;
    import android.text.TextUtils;
    import android.util.Log;

    import com.baidu.android.pushservice.PushMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import org.json.JSONException;
    import org.json.JSONObject;

    import java.util.List;

    public class MyPushMessageReceiver extends PushMessageReceiver {

        public static final String TAG = MyPushMessageReceiver.class
                .getSimpleName();
        public static NotificationHub hub = null;
        public static String mChannelId, mUserId;

        @Override
        public void onBind(Context context, int errorCode, String appid,
                        String userId, String channelId, String requestId) {
            String responseString = "onBind errorCode=" + errorCode + " appid="
                    + appid + " userId=" + userId + " channelId=" + channelId
                    + " requestId=" + requestId;
            Log.d(TAG, responseString);

            if (errorCode == 0) {
                // Binding successful
                Log.d(TAG, " Binding successful");
            }
            try {
                if (hub == null) {
                    hub = new NotificationHub(
                            ConfigurationSettings.NotificationHubName,
                            ConfigurationSettings.NotificationHubConnectionString,
                            context);
                    Log.i(TAG, "Notification hub initialized");
                }
            } catch (Exception e) {
                Log.e(TAG, e.getMessage());
            }
            mChannelId = channelId;
            mUserId = userId;

            registerWithNotificationHubs();
        }
        private void registerWithNotificationHubs() {

            new AsyncTask<Void, Void, Void>() {
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        hub.registerBaidu(mUserId, mChannelId);
                        Log.i(TAG, "Registered with Notification Hub - '"
                                + ConfigurationSettings.NotificationHubName + "'"
                                + " with UserId - '"
                                + mUserId + "' and Channel Id - '"
                                + mChannelId + "'");
                    } catch (Exception e) {
                        Log.e(TAG, e.getMessage());
                    }
                    return null;
                }
            }.execute(null, null, null);
        }

        @Override
        public void onMessage(Context context, String message,
                            String customContentString) {
            String messageString = " onMessage=\"" + message
                    + "\" customContentString=" + customContentString;
            Log.d(TAG, messageString);
            if (!TextUtils.isEmpty(customContentString)) {
                JSONObject customJson = null;
                try {
                    customJson = new JSONObject(customContentString);
                    String myvalue = null;
                    if (!customJson.isNull("mykey")) {
                        myvalue = customJson.getString("mykey");
                    }
                } catch (JSONException e) {
                    e.printStackTrace();
                }
            }

        }

        @Override
        public void onNotificationArrived(Context context, String title, String description, String customContentString) {
            String notifyString = " Notice Arrives onNotificationArrived  title=\"" + title
                    + "\" description=\"" + description + "\" customContent="
                    + customContentString;
            Log.d(TAG, notifyString);
            if (!TextUtils.isEmpty(customContentString)) {
                JSONObject customJson = null;
                try {
                    customJson = new JSONObject(customContentString);
                    String myvalue = null;
                    if (!customJson.isNull("mykey")) {
                        myvalue = customJson.getString("mykey");
                    }
                } catch (JSONException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            }
        }

        @Override
        public void onNotificationClicked(Context context, String title, String description, String customContentString) {
            String notifyString = " onNotificationClicked title=\"" + title + "\" description=\""
                    + description + "\" customContent=" + customContentString;
            Log.d(TAG, notifyString);
            Intent intent = new Intent(context.getApplicationContext(),MainActivity.class);
            intent.putExtra("title",title);
            intent.putExtra("description",description);
            intent.putExtra("isFromNotify",true);
            intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            context.getApplicationContext().startActivity(intent);

        }

        @Override
        public void onSetTags(Context context, int errorCode,
                            List<String> successTags, List<String> failTags, String requestId) {
            String responseString = "onSetTags errorCode=" + errorCode
                    + " successTags=" + successTags + " failTags=" + failTags
                    + " requestId=" + requestId;
            Log.d(TAG, responseString);

        }

        @Override
        public void onDelTags(Context context, int errorCode,
                            List<String> successTags, List<String> failTags, String requestId) {
            String responseString = "onDelTags errorCode=" + errorCode
                    + " successTags=" + successTags + " failTags=" + failTags
                    + " requestId=" + requestId;
            Log.d(TAG, responseString);

        }

        @Override
        public void onListTags(Context context, int errorCode, List<String> tags,
                            String requestId) {
            String responseString = "onListTags errorCode=" + errorCode + " tags="
                    + tags;
            Log.d(TAG, responseString);

        }

        @Override
        public void onUnbind(Context context, int errorCode, String requestId) {
            String responseString = "onUnbind errorCode=" + errorCode
                    + " requestId = " + requestId;
            Log.d(TAG, responseString);

            if (errorCode == 0) {
                // Unbinding is successful
                Log.d(TAG, " Unbinding is successful ");
            }
        }
    }
    ```

## <a name="send-notifications-to-your-app"></a>Odešlete oznámení do aplikace

Příjem oznámení můžete rychle otestovat na webu [Azure Portal]: Použijte tlačítka **Odeslat** na obrazovce pro konfiguraci centra oznámení, jak je znázorněno na následující obrazovce:

![Snímek obrazovky s možností odeslat na webu Azure Portal popsaný červenou a červenou šipkou, na ](./media/notification-hubs-baidu-get-started/BaiduTestSendButton.png)
 ![ kterou se odkazuje Snímek obrazovky se stránkou Baidu test Send v Azure Portal.](./media/notification-hubs-baidu-get-started/BaiduTestSend.png)

Nabízená oznámení se většinou posílají ve službě back-end, jako je služba Mobile Services, nebo v technologii ASP.NET pomocí kompatibilní knihovny. Pokud pro váš back-end není dostupná žádná knihovna, můžete k přímému odesílání oznámení použít také rozhraní REST API.

Tento kurz pro zjednodušení používá konzolovou aplikaci jako ukázku odeslání oznámení pomocí sady .NET SDK. Nicméně jako další krok pro odesílání oznámení z back-endu ASP.NET doporučujeme absolvovat kurz [Použití Notification Hubs k odesílání nabízených oznámení uživatelům](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md). 

Tady jsou různé přístupy k odesílání oznámení:

* **Rozhraní REST**: oznámení můžete podporovat na jakékoli backend platformě pomocí [rozhraní REST](/previous-versions/azure/reference/dn223264(v=azure.100)).
* **Microsoft Azure oznámení centra .NET SDK**: Ve správci balíčků Nuget pro Visual Studio spusťte položku [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* **Node.js:**[Jak používat Notification Hubs z Node.js](notification-hubs-nodejs-push-notification-tutorial.md).
* **Mobile Apps**: Příklad zasílání oznámení z back-endu Azure App Service Mobile Apps integrovaného se službou Notification Hubs najdete v tématu [Přidání nabízených oznámení do mobilních aplikací](/previous-versions/azure/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push).
* **Java / PHP**: Příklad odesílání oznámení pomocí rozhraní API REST najdete v části „Použití centra oznámení z Java/PHP“ ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

## <a name="optional-send-notifications-from-a-net-console-app"></a>(Volitelné) Odesílání oznámení z konzoly aplikace .NET.

V této části ukážeme odesílání oznámení pomocí konzolové aplikace .NET.

1. Vytvořte novou konzolovou aplikaci Visual C#:

    ![Snímek obrazovky dialogového okna Nový projekt s zvýrazněnou možností Visual C# pro konzolovou aplikaci Visual C#](./media/notification-hubs-baidu-get-started/ConsoleProject.png)

2. V okně konzoly Správce balíčků nastavte **Výchozí projekt** na nový projekt konzolové aplikace a pak v okně konzoly spusťte následující příkaz:

    ```shell
    Install-Package Microsoft.Azure.NotificationHubs
    ```

    Tento příkaz přidá referenci na sadu SDK služby Azure Notification Hubs pomocí [balíčku NuGet Microsoft.Azure.Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

    ![Snímek obrazovky dialogového okna konzoly Správce balíčků s možností poslat do centra oznámení červeně v kruhu](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. Otevřete soubor `Program.cs` a přidejte následující příkaz using:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

4. Do třídy `Program` přidejte následující metodu a nahraďte hodnoty `DefaultFullSharedAccessSignatureSASConnectionString` a `NotificationHubName` vlastními hodnotami.

    ```csharp
    private static async void SendNotificationAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
        string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
        var result = await hub.SendBaiduNativeNotificationAsync(message);
    }
    ```

5. Do metody `Main` přidejte následující řádky:

    ```csharp
    SendNotificationAsync();
    Console.ReadLine();
    ```

## <a name="test-your-app"></a>Testování aplikace

K testování této aplikace pomocí skutečného telefonu, jednoduše připojte telefon k počítači pomocí kabelu USB. Tato akce načte aplikaci do připojeného telefonu.

Pokud chcete tuto aplikaci otestovat pomocí emulátoru, na horním panelu nástrojů Android Studio klikněte na **Spustit** a pak vyberte vaši aplikaci: spustí se emulátor, načte se aplikace a spustí se.

Aplikace načte hodnoty `userId` a `channelId` ze služby nabízených oznámení Baidu a zaregistruje se v centru oznámení.

Pro odeslání testovacího oznámení můžete použít kartu ladění na webu [Azure Portal]. Pokud jste vytvořili konzolovou aplikaci .NET pro Visual Studio, jednoduše ke spuštění aplikace stiskněte klávesu F5 ve Visual Studiu. Aplikace odešle oznámení, které se zobrazí v horní oznamovací oblasti vašeho zařízení nebo emulátoru.

<!-- URLs. -->
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Baidu Push Android SDK]: https://push.baidu.com/sdk/push_client_sdk_for_android
[Azure Portal]: https://portal.azure.com/
[Baidu portal]: https://www.baidu.com/
