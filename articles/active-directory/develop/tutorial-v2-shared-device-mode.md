---
title: Použití režimu sdíleného zařízení s MSAL Androidem | Azure
description: Naučte se, jak připravit zařízení s Androidem ke spuštění ve sdíleném režimu a spustit aplikaci Firstline Worker.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: bf7e6bb22ce89d6be3f79efad1f1a3679e8780e7
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086063"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Kurz: použití režimu sdíleného zařízení v aplikaci pro Android

> [!NOTE]
> Tato funkce je ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="developer-guide"></a>Příručka pro vývojáře

Tato příručka poskytuje pokyny pro vývojáře k implementaci režimu sdíleného zařízení v aplikaci pro Android pomocí knihovny Microsoft Authentication Library (MSAL). Podívejte se na [kurz k MSAL Androidu](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android) , kde najdete informace o integraci MSAL s aplikací pro Android, přihlášení uživatele, volání Microsoft graphu a odhlášení uživatele.

### <a name="download-the-sample"></a>Stažení ukázky

Naklonujte [ukázkovou aplikaci](https://github.com/Azure-Samples/ms-identity-android-java/) z GitHubu. Ukázka nabízí možnost pracovat v [režimu jednoho nebo více účtů](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account).

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>Přidání sady MSAL SDK do místního úložiště Maven

Pokud ukázkovou aplikaci nepoužíváte, přidejte knihovnu MSAL jako závislost do souboru Build. Gradle, například takto:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Konfigurace aplikace tak, aby používala režim sdíleného zařízení

Další informace o nastavení konfiguračního souboru najdete v [dokumentaci ke konfiguraci](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration) .

Nastavte `"shared_device_mode_supported"` pro `true` v konfiguračním souboru MSAL.

Možná neplánujete podporovat režim více účtů. To může být v případě, že nepoužíváte sdílené zařízení a uživatel se může přihlásit k aplikaci s více než jedním účtem ve stejnou dobu. Pokud ano, nastavte `"account_mode"` na `"SINGLE"`. To zaručuje, že vaše aplikace bude vždycky `ISingleAccountPublicClientApplication`a významně zjednodušuje integraci MSAL. Výchozí hodnota `"account_mode"` je `"MULTIPLE"`, takže je důležité změnit tuto hodnotu v konfiguračním souboru, pokud používáte režim `"single account"`.

Tady je příklad souboru auth_config. JSON, který je součástí **aplikace**>**main**>**res**>**nezpracovaný** adresář ukázkové aplikace:

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>Detekovat režim sdíleného zařízení

Režim sdíleného zařízení umožňuje nakonfigurovat zařízení s Androidem, která se mají sdílet s více zaměstnanci, a zajistit tak správu zařízení pomocí Microsoft identity. Zaměstnanci se můžou ke svým zařízením přihlašovat a rychle získat informace o zákaznících. Až se dokončí s jejich posunem nebo úlohou, budou se moct pomocí jediného kliknutí přihlásit ke všem aplikacím na sdíleném zařízení a zařízení se hned připraví na dalšího zaměstnance.

Pomocí `isSharedDevice()` můžete zjistit, jestli je aplikace spuštěná na zařízení, které je v režimu sdíleného zařízení. Vaše aplikace by mohla pomocí tohoto příznaku určit, jestli má odpovídajícím způsobem upravit uživatelské rozhraní.

Zde je fragment kódu, který ukazuje, jak můžete použít `isSharedDevice()`.  Je z třídy `SingleAccountModeFragment` v ukázkové aplikaci:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>Inicializovat objekt PublicClientApplication

Pokud nastavíte `"account_mode":"SINGLE"` v konfiguračním souboru MSAL, můžete objekt vrácené aplikace bezpečně přetypovat jako `ISingleAccountPublicCLientApplication`.

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/ 
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config, 
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});  
```

### <a name="detect-single-vs-multiple-account-mode"></a>Zjišťování jednoduchého režimu a více účtů

Pokud vytváříte aplikaci, která se bude používat jenom pro Firstline pracovní procesy na sdíleném zařízení, doporučujeme napsat aplikaci tak, aby podporovala pouze režim jednoho účtu. To zahrnuje většinu aplikací, které jsou zaměřeny na úlohy, jako jsou lékařské záznamy aplikací, fakturační aplikace a většina obchodních aplikací. Tím se zjednoduší váš vývoj, protože celá řada funkcí sady SDK nebude muset vyhovovat.

Pokud vaše aplikace podporuje víc účtů a také režim sdíleného zařízení, musíte provést kontrolu typu a přetypovat na příslušné rozhraní, jak je znázorněno níže.

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Získá přihlášeného uživatele a určí, jestli se na zařízení změnil nějaký uživatel.

Metoda `loadAccount` načte účet přihlášeného uživatele. Metoda `onAccountChanged` určuje, zda se přihlášený uživatel změnil, a pokud ano, vyčištění:

```java 
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override 
    public void onError(@NonNull Exception exception) 
    {
    }
  }
}  
```

### <a name="globally-sign-in-a-user"></a>Globálně přihlašovat uživatele

Následující znaménko uživatele v zařízení k jiným aplikacím, které používají MSAL, s aplikací ověřovatele:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Globálně odhlásit uživatele

Následující příkaz odebere přihlášený účet a smaže tokeny uložené v mezipaměti nejen z aplikace, ale také ze zařízení, které je v režimu sdíleného zařízení:

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>Příručka správce

Následující kroky popisují nastavení aplikace v Azure Portal a vložení zařízení do režimu sdíleného zařízení.

### <a name="register-your-application-in-azure-active-directory"></a>Registrace aplikace v Azure Active Directory

Nejdřív Zaregistrujte svoji aplikaci v tenantovi vaší organizace. Poté zadejte následující hodnoty do auth_config. JSON, aby vaše aplikace běžela správně.

Informace o tom, jak to provést, najdete v tématu [Registrace aplikace](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android#register-your-application).

> [!NOTE]
> Při registraci aplikace prosím použijte příručku pro rychlý Start na levé straně a pak vyberte **Android**. Tím přejdete na stránku, kde budete požádáni o zadání **názvu balíčku** a **hodnoty hash podpisu** pro vaši aplikaci. To je velmi důležité, abyste zajistili, že konfigurace vaší aplikace bude fungovat. Potom obdržíte objekt konfigurace, který můžete použít pro vaši aplikaci, kterou vyjmete a vložíte do souboru auth_config. JSON.

obrazovka pro registraci aplikace ![](media/tutorial-v2-shared-device-mode/register-app.png) měli byste vybrat **provést tuto změnu pro mě** a pak zadat hodnoty, na které se bude rychlý Start dotazovat v Azure Portal. Až to bude hotové, vygenerujeme všechny konfigurační soubory, které potřebujete.

![Obrazovka informace o konfiguraci aplikace](media/tutorial-v2-shared-device-mode/config-info.png)

## <a name="set-up-a-tenant"></a>Nastavení tenanta

Pro účely testování nastavte ve vašem tenantovi následující: aspoň dva zaměstnance, jednoho správce cloudového zařízení a jednoho globálního správce. V Azure Portal nastavte správce cloudového zařízení úpravou organizačních rolí. V Azure Portal získáte přístup k rolím organizace tak, že vyberete **Azure Active Directory** > **role a správci** > **správce cloudového zařízení**. Přidejte uživatele, kteří můžou umístit zařízení do sdíleného režimu.

## <a name="set-up-an-android-device-in-shared-mode"></a>Nastavení zařízení s Androidem ve sdíleném režimu

### <a name="download-the-authenticator-app"></a>Stáhnout ověřovací aplikaci

Stáhněte si aplikaci Microsoft Authenticator z Google Play Storu. Pokud jste aplikaci již stáhli, ujistěte se, že je její nejnovější verze.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Nastavení aplikace ověřovatele & registraci zařízení v cloudu

Spusťte aplikaci ověřovatele a přejděte na stránku hlavní účet. Jakmile se zobrazí stránka **Přidat účet** , budete připraveni zařízení nastavit jako sdílenou.

![Obrazovka Přidat účet ověřovatele](media/tutorial-v2-shared-device-mode/authenticator-add-account.png)

 Přejděte do podokna **Nastavení** pomocí panelu nabídek na pravé straně. V části **pracovní & školní účty**vyberte **registrace zařízení** .
 
 ![Obrazovka Přidat účet ověřovatele](media/tutorial-v2-shared-device-mode/authenticator-settings.png)

 Po kliknutí na toto tlačítko budete požádáni o autorizaci přístupu k kontaktům zařízení. Důvodem je integrace účtu Androidu na zařízení. Vyberte možnost **udělit**.

 ![Obrazovka Přidat účet ověřovatele](media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png)

Správce cloudového zařízení by měl zadat svůj e-mailovou adresu organizace v rámci **nebo zaregistrovat jako sdílené zařízení**. Pak klikněte na tlačítko **registrovat jako sdílené zařízení** a zadejte své přihlašovací údaje.

![obrazovka registrace – zařízení](media/tutorial-v2-shared-device-mode/register-device.png)

![přihlášení](media/tutorial-v2-shared-device-mode/sign-in.png)

Zařízení je nyní ve sdíleném režimu.

![obrazovka registrace – zařízení](media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png)

 Všechna přihlášení a přihlášení v zařízení budou globální, což znamená, že se vztahují na všechny aplikace, které jsou integrované s MSAL a Microsoft Authenticator na zařízení. Aplikace teď můžete nasadit do zařízení, které používá funkce režimu sdíleného zařízení.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Zobrazení sdíleného zařízení v Azure Portal

Jakmile zařízení umístíte do sdíleného režimu, bude známo vaší organizaci a bude sledováno v tenantovi vaší organizace. Sdílená zařízení si můžete zobrazit tak, že v okně Azure Active Directory svého Azure Portal prohlížíte **typ spojení** .

![Okno všechna zařízení v Azure Portal](media/tutorial-v2-shared-device-mode/registered-device-screen.png)

## <a name="running-the-sample-app"></a>Spuštění ukázkové aplikace

Ukázková aplikace je jednoduchá aplikace, která bude volat Graph API vaší organizace. n při prvním spuštění se zobrazí výzva k vyjádření souhlasu s tím, že je aplikace pro váš účet zaměstnance nová.

![Obrazovka informace o konfiguraci aplikace](media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png)

## <a name="next-steps"></a>Další kroky

Další informace o sdíleném režimu v [režimu sdíleného zařízení pro zařízení s Androidem](shared-device-mode.md)