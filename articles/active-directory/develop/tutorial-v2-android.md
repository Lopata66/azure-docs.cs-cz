---
title: Podepsat uživatele on/out & volání Microsoft Graph (Android) – Microsoft Identity Platform | Azure
description: Získání přístupového tokenu a volání Microsoft Graph nebo rozhraní API, která vyžadují přístupové tokeny z platformy Microsoft Identity Platform (Android)
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4c4c9bc025e8fd506b298ed676674899e318481
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689344"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-application"></a>Kurz: přihlášení uživatelů a volání Microsoft Graph z aplikace pro Android 

>[!NOTE]
>Tento kurz ukazuje zjednodušené příklady práce s MSAL pro Android. Pro zjednodušení používá tento kurz jenom režim jednoho účtu. Můžete také zobrazit úložiště a klonovat [předem nakonfigurovanou ukázkovou aplikaci](https://github.com/Azure-Samples/ms-identity-android-java/) a prozkoumat tak složitější scénáře. Podívejte se na [rychlý Start](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-android) , kde najdete další informace o ukázkové aplikaci, konfiguraci a registraci. 

V tomto kurzu se dozvíte, jak integrovat aplikaci pro Android s platformou Microsoft identity pomocí knihovny ověřování Microsoft pro Android. Naučíte se, jak se přihlásit a odhlásit uživatele, získat přístupový token pro volání rozhraní Microsoft Graph API a vytvořit žádost o Graph API. 

> [!div class="checklist"]
> * Integrujte svou aplikaci pro Android s platformou Microsoft identity 
> * Přihlášení uživatele 
> * Získání přístupového tokenu pro volání rozhraní Microsoft Graph API 
> * Volání rozhraní API pro Microsoft Graph 
> * Odhlásit uživatele 

Po dokončení tohoto kurzu bude vaše aplikace přijímat přihlašování osobních účtů Microsoft (včetně outlook.com, live.com a dalších) a také pracovních nebo školních účtů z jakékoli společnosti nebo organizace, která používá Azure Active Directory.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="how-this-tutorial-works"></a>Jak tento kurz funguje

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Aplikace v tomto kurzu se bude přihlašovat uživatelům a získat data jménem. Tato data budou k dispozici prostřednictvím chráněného rozhraní API (Microsoft Graph API), které vyžaduje autorizaci a jsou chráněny platformou Microsoft identity.

A to konkrétně:

* Vaše aplikace se přihlásí k uživateli přes prohlížeč nebo Microsoft Authenticator a Portál společnosti Intune.
* Koncový uživatel bude akceptovat oprávnění, která vaše aplikace požadovala.
* Vaše aplikace se vydá přístupový token pro rozhraní Microsoft Graph API.
* Přístupový token bude součástí požadavku HTTP webovému rozhraní API.
* Zpracujte odpověď Microsoft Graph.

Tato ukázka používá knihovnu Microsoft Authentication Library pro Android (MSAL) k implementaci ověřování: [com. Microsoft. identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 MSAL bude automaticky obnovovat tokeny, poskytovat jednotné přihlašování (SSO) mezi ostatními aplikacemi na zařízení a spravovat účty.

### <a name="prerequisites"></a>Požadavky

* Tento kurz vyžaduje Android Studio verze 3.5 +.

## <a name="create-a-project"></a>Vytvořit projekt
Pokud ještě nemáte aplikaci pro Android, postupujte podle těchto kroků a nastavte nový projekt. 

1. Otevřete Android Studio a vyberte **Spustit nový Android Studio projekt**.
2. Vyberte možnost **základní aktivita** a vyberte možnost **Další**.
3. Pojmenujte svoji aplikaci.
4. Uložte název balíčku. Později ji zadáte do Azure Portal.
5. Změňte jazyk z **Kotlin** na **Java**.
6. Nastavte **minimální úroveň rozhraní API** na **rozhraní API 19** nebo vyšší a klikněte na **Dokončit**.
7. V zobrazení projektu vyberte v rozevíracím seznamu **projekt** a zobrazte zdrojové a nezdrojové soubory projektu, otevřete **App/Build. gradle** a nastavte `targetSdkVersion` na `28`.

## <a name="integrate-with-microsoft-authentication-library"></a>Integrace s Microsoft Authentication Library 

### <a name="register-your-application"></a>Registrace vaší aplikace

1. Přejděte na [portál Azure](https://aka.ms/MobileAppReg).
2. Otevřete okno [Registrace aplikací](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) a klikněte na **+ Nová registrace**.
3. Zadejte **název** vaší aplikace a pak **bez** nastavení identifikátoru URI přesměrování klikněte na **zaregistrovat**.
4. V části **Správa** v podokně, které se zobrazí, vyberte **ověřování** >  **+ Přidat platformu** > **Android**. (V horní části okna možná budete muset vybrat možnost "přepnout na nové prostředí", abyste viděli tuto část.)
5. Zadejte název balíčku vašeho projektu. Pokud jste kód stáhli, je tato hodnota `com.azuresamples.msalandroidapp`.
6. V části **Signature hash** na stránce **Konfigurace aplikace pro Android** klikněte na **vygenerovat hodnotu hash signatury pro vývoj.** a zkopírujte příkaz nástroje nástroje, který se má použít pro vaši platformu.

   > [!Note]
   > Nástroj Tool. exe je nainstalován jako součást sady Java Development Kit (JDK). Je také nutné nainstalovat nástroj OpenSSL pro spuštění příkazu nástroje.

7. Zadejte **hodnotu hash podpisu** generovanou nástrojem.
8. Klikněte na `Configure` a uložte **konfiguraci MSAL** , která se zobrazí na stránce **Konfigurace Androidu** , abyste ji mohli zadat při pozdější konfiguraci aplikace.  Klikněte na **Done** (Hotovo).

### <a name="configure-your-application"></a>Konfigurace aplikace 

1. V podokně projektu Android Studio přejděte na **app\src\main\res**.
2. Klikněte pravým tlačítkem na **res** a vyberte **Nový** > **adresář**. Jako název nového adresáře zadejte `raw` a klikněte na **OK**.
3. V části **app** > **src** > **Main** > **res** > **raw**vytvořte nový soubor JSON s názvem `auth_configbn_single_account.json` a vložte konfiguraci MSAL, kterou jste předtím uložili. 

    Pod identifikátorem URI přesměrování vložte: 
    ```json
      "account_mode" : "SINGLE",
    ```
    Konfigurační soubor by měl vypadat podobně jako v tomto příkladu: 
    ```json   
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "account_mode" : "SINGLE",
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
   ```
    
   >[!NOTE]
   >Tento kurz ukazuje, jak nakonfigurovat aplikaci v režimu jednoho účtu. Podívejte se na dokumentaci, kde najdete další informace o [jednom nebo víc režimech účtů](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account) a [konfiguraci aplikace](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration) .
   
4. Do části **app** > **src** > **Main** > **souboru AndroidManifest. XML**přidejte `BrowserTabActivity` aktivitu do těla aplikace. Tato položka umožňuje, aby Microsoft po dokončení ověřování vrátil zpět do vaší aplikace:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Pro `android:host=` hodnotu nahraďte název balíčku, který jste zaregistrovali v Azure Portal.
    Hodnotu hash klíče, kterou jste zaregistrovali v Azure Portal, nahraďte hodnotou `android:path=`. Hodnota hash **podpisu by neměla být kódovaná** v adrese URL. Zajistěte, aby na začátku hodnoty hash podpisu existovala úvodní `/`. 
    >[!NOTE]
    >Název balíčku nahradíte `android:host` hodnota by měla vypadat nějak takto: "com. azuresamples. msalandroidapp" signatura "hash", nahradíte hodnotu `android:path` by měla vypadat nějak takto: "/1wIqXSqBj7w + h11ZifsnqwgyKrY =" tyto hodnoty bude možné najít v okně ověřování registrace vaší aplikace. Všimněte si, že váš identifikátor URI pro přesměrování bude vypadat nějak takto: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". I když je hodnota hash podpisu na konci této hodnoty zakódovaná, hodnota hash signatury by **neměla být v** hodnotě `android:path` kódovaná. 

## <a name="use-msal"></a>Použití MSAL 

### <a name="add-msal-to-your-project"></a>Přidání MSAL do projektu

1. V okně Android Studio projektu přejděte do **app** > **Src** > **Build. Gradle** a přidejte následující: 

    ```gradle
    repositories{
        jcenter()
    }  
    dependencies{
        implementation 'com.microsoft.identity.client:msal:1.0.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    ```
    [Další informace o sadě Microsoft Graph SDK](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Požadované importy 

Do horní části **aplikace** přidejte následující > **src** > **Main**> **Java** > **com. Příklad (yourapp)**  > **MainActivity. Java** 

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>Vytvoření instance PublicClientApplication
#### <a name="initialize-variables"></a>Inicializovat proměnné 
```java
private final static String[] SCOPES = {"User.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
V rámci třídy `MainActivity` použijte následující metodu Create () pro vytvoření instance MSAL pomocí `SingleAccountPublicClientApplication`.

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount 

```java
//When app comes to the foreground, load existing account to determine if user is signed in 
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }
        
        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
}
```

### <a name="initializeui"></a>initializeUI
Naslouchat tlačítkům a volat metody nebo protokolovat chyby. 
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);
        
        //Sign in user 
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });
        
        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });
        
        //Interactive 
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> Odhlášení pomocí MSAL odebere všechny známé informace o uživateli z aplikace, ale uživatel bude na svém zařízení mít stále aktivní relaci. Pokud se uživatel pokusí přihlásit znovu, může se jim zobrazit přihlašovací uživatelské rozhraní, ale nemusí znovu zadávat svoje přihlašovací údaje, protože relace zařízení je stále aktivní. 

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
Zpětné volání používané pro interaktivní požadavky.

```java 
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
Zpětné volání používané pro tiché požadavky 
```java 
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Volání rozhraní API Microsoft Graphu 

Následující kód ukazuje, jak volat GraphAPI pomocí sady Graph SDK. 

### <a name="callgraphapi"></a>callGraphAPI 

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>Přidat uživatelské rozhraní
### <a name="activity"></a>Aktivita 
Pokud chcete vytvořit model uživatelského rozhraní mimo tento kurz, následující metody poskytují návod k aktualizaci textu a naslouchání tlačítkům.

#### <a name="updateui"></a>updateUI
Povolí nebo zakáže tlačítka na základě stavu přihlášení a nastavení textu.  
```java 
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java 
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
Metoda, která aktualizuje text v uživatelském rozhraní, aby odrážel odhlášení 

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Layout 

Ukázka `activity_main.xml` souboru pro zobrazení tlačítek a textových polí. 

```xml 
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>Testování aplikace

### <a name="run-locally"></a>Spuštění v místním prostředí

Sestavte a nasaďte aplikaci do testovacího zařízení nebo emulátoru. Měli byste být schopni se přihlásit a získat tokeny pro účty Azure AD nebo osobní účty Microsoft.

Po přihlášení aplikace zobrazí data vrácená z Microsoft Graph koncového bodu `/me`.

### <a name="consent"></a>Souhlas

Když se uživatel poprvé přihlásí do vaší aplikace, zobrazí se mu výzva společnosti Microsoft pro vyjádření souhlasu s požadovanými oprávněními. Někteří klienti Azure AD mají zakázaný souhlas s uživatelem, který vyžaduje, aby správci měli souhlas jménem všech uživatelů. Pro podporu tohoto scénáře musíte buď vytvořit vlastního tenanta, nebo přijmout souhlas správce. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte objekt aplikace, který jste vytvořili v kroku [Registrace aplikace](#register-your-application) .

## <a name="get-help"></a>Získání nápovědy

Pokud máte potíže s tímto kurzem nebo s platformou Microsoft identity, přejděte na [pomoc a podpora](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) .

Pomůžeme nám vylepšit platformu Microsoft identity. Řekněte nám, co si myslíte, díky krátkému průzkumu dvou dotazů.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform Survey](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
