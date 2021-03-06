---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: a8cfdc76694d52acee70cde0e3f1697cd8129d06
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97691984"
---
## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Nasazený prostředek komunikačních služeb. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- A `User Access Token` povolí klienta volání. Další informace o [tom, jak získat `User Access Token` ](../../access-tokens.md)
- Volitelné: dokončete rychlé [zprovoznění, abyste mohli začít s přidáváním volání do aplikace](../getting-started-with-calling.md) .

## <a name="setting-up"></a>Nastavení

### <a name="creating-the-xcode-project"></a>Vytvoření projektu Xcode

V Xcode vytvořte nový projekt iOS a vyberte šablonu aplikace s **jedním zobrazením** . V tomto rychlém startu se používá [SwiftUI Framework](https://developer.apple.com/xcode/swiftui/), takže byste měli nastavit **jazyk** na **SWIFT** a **uživatelské rozhraní** na **SwiftUI**. V rámci tohoto rychlého startu nebudete vytvářet testy jednotek nebo testy uživatelského rozhraní. Nezapomeňte zrušit kontrolu **včetně testů jednotek** a také zrušit kontrolu **včetně testů uživatelského rozhraní**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Snímek obrazovky s oknem pro vytvoření nového projektu v rámci Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Instalace balíčku a závislostí pomocí CocoaPods

1. Vytvořte souboru podfile pro vaši aplikaci takto:

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.5'
     pod 'AzureCommunication', '~> 1.0.0-beta.5'
     pod 'AzureCore', '~> 1.0.0-beta.5'
   end
   ```

2. Spusťte `pod install`.
3. Otevřete `.xcworkspace` s Xcode.

### <a name="request-access-to-the-microphone"></a>Požádat o přístup k mikrofonu

Aby bylo možné získat přístup k mikrofonu zařízení, je třeba aktualizovat seznam vlastností informací o aplikaci pomocí `NSMicrophoneUsageDescription` . Nastavte přidruženou hodnotu na `string` , která bude součástí dialogu, který systém používá k vyžádání žádosti o přístup od uživatele.

Klikněte pravým tlačítkem myši na `Info.plist` položku stromové struktury projektu a vyberte **Otevřít jako**  >  **zdrojový kód**. Přidejte následující řádky do části nejvyšší úrovně `<dict>` a pak soubor uložte.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Otevřete soubor **contentView. SWIFT** projektu a přidejte `import` do horní části souboru deklaraci, která se má importovat `AzureCommunicationCalling library` . Kromě toho ho budeme `AVFoundation` potřebovat pro žádost o oprávnění zvuk v kódu.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce komunikačních služeb Azure, které volají klientské knihovny pro iOS.


| Název                                  | Popis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | ACSCallClient je hlavní vstupní bod pro volání klientské knihovny.|
| ACSCallAgent | ACSCallAgent se používá ke spouštění a správě volání. |
| CommunicationUserCredential | CommunicationUserCredential se používá jako přihlašovací údaje tokenu pro vytvoření instance CallAgent.| 
| CommunicationIndentifier | CommunicationIndentifier se používá k reprezentaci identity uživatele, která může být jedna z následujících: CommunicationUser/PhoneNumber/CallingApplication. |

> [!NOTE]
> Při implementaci delegátů událostí musí aplikace uchovávat silný odkaz na objekty, které vyžadují odběry událostí. Například když `ACSRemoteParticipant` je objekt vrácen při vyvolání `call.addParticipant` metody a aplikace nastaví delegáta na naslouchání `ACSRemoteParticipantDelegate` , aplikace musí obsahovat silný odkaz na `ACSRemoteParticipant` objekt. V opačném případě, pokud se tento objekt získá, delegát vyvolá závažnou výjimku, když se volající sada SDK pokusí objekt vyvolat.

## <a name="initialize-the-acscallagent"></a>Inicializovat ACSCallAgent

Chcete-li vytvořit `ACSCallAgent` instanci z `ACSCallClient` , je nutné použít `callClient.createCallAgent` metodu, která asynchronně vrátí `ACSCallAgent` objekt po jeho inicializaci.

Chcete-li vytvořit klienta volání, musíte předat `CommunicationUserCredential` objekt.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(
        initialToken: tokenString, refreshProactively: true,
        tokenRefresher: self.fetchTokenSync
    )
} catch {
    print("Failed to create CommunicationCredential object")
}

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Předejte objekt CommunicationUserCredential vytvořený výše na ACSCallClient a nastavte zobrazovaný název.

```swift

callClient = CallClient()
let callAgentOptions:CallAgentOptions = CallAgentOptions()
options.displayName = "ACS iOS User"

callClient?.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions,
    completionHandler: { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Umístit odchozí volání

Chcete-li vytvořit a spustit volání, je třeba zavolat jedno z rozhraní API v `ACSCallAgent` a zadat identitu komunikačních služeb uživatele, který jste zřídili pomocí klientské knihovny pro správu komunikačních služeb.

Vytvoření a spuštění volání je synchronní. Zobrazí se instance volání, která vám umožní přihlásit se k odběru všech událostí volání.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Nakoná volání 1:1 uživateli nebo volání 1: n s uživateli a veřejnou telefonní síť.

```swift

let callees = [CommunicationUser(identifier: 'acsUserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Nakonání volání 1: n s uživateli a PSTN
Chcete-li umístit volání do veřejné telefonní sítě, musíte zadat telefonní číslo získané pomocí komunikačních služeb.
```swift

let pstnCallee = PhoneNumber('+1999999999')
let callee = CommunicationUser(identifier: 'acsUserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>Vložení volání 1:1 s videem
Pokud chcete získat instanci správce zařízení, přečtěte si [tady](#device-management) .

```swift

let camera = self.deviceManager!.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUser(identifier: 'acsUserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Připojit se k volání skupiny
Pokud se chcete připojit k volání, musíte zavolat jedno z rozhraní API na *CallAgent* .

```swift

let groupCallContext = GroupCallContext()
groupCallContext?.groupId = UUID(uuidString: "uuid_string")!
let call = self.callAgent?.join(with: groupCallContext, joinCallOptions: JoinCallOptions())

```

### <a name="accept-an-incoming-call"></a>Přijmout příchozí volání
Chcete-li přijmout volání, zavolejte metodu Accept objektu volání.
Nastavit delegáta na CallAgent 
```swift
final class CallHandler: NSObject, CallAgentDelegate
{
    public var incomingCall: Call?
 
    public func onCallsUpdated(_ callAgent: CallAgent!, args: CallsUpdatedEventArgs!) {
        if let incomingCall = args.addedCalls?.first(where: { $0.isIncoming }) {
            self.incomingCall = incomingCall
        }
    }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager?.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: firstCamera)
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions!.videoOptions = VideoOptions(localVideoStream:localVideoStream!)
if let incomingCall = CallHandler().incomingCall {
   incomingCall.accept(options: acceptCallOptions,
                          completionHandler: { (error) in
                           if error == nil {
                               print("Incoming call accepted")
                           } else {
                               print("Failed to accept incoming call")
                           }
                       })
} else {
   print("No incoming call found to accept")
}
```

## <a name="push-notification"></a>Nabízené oznámení

Mobilní nabízené oznámení je místní oznámení, které se zobrazí v mobilním zařízení. Pro volání se budeme soustředit na nabízená oznámení VoIP (Voice over Internet Protocol). Nabídneme vám možnosti registrace nabízeného oznámení, zpracování nabízeného oznámení a zrušení registrace nabízeného oznámení.

### <a name="prerequisite"></a>Požadavek

- Krok 1: Xcode-> podepisování & schopností – > přidání možnosti – > nabízená oznámení
- Krok 2: Xcode-> podepisování možností & – > přidání možnosti – > režimy na pozadí
- Krok 3: "režimy na pozadí" – > vybrat "Voice over IP" a "Vzdálená oznámení"

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Snímek obrazovky, který ukazuje, jak přidat funkce v Xcode." lightbox="../media/ios/xcode-push-notification.png":::

#### <a name="register-for-push-notifications"></a>Registrace nabízených oznámení

Chcete-li provést registraci nabízeného oznámení, zavolejte registerPushNotification () na instanci *CallAgent* s registračním tokenem zařízení.

Zaregistrujte se na nabízená oznámení, která se musí volat po úspěšné inicializaci. Při `callAgent` zničení objektu `logout` bude volána metoda, která automaticky zruší registraci nabízených oznámení.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken,
                completionHandler: { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
})

```

#### <a name="push-notification-handling"></a>Manipulace s nabízenými oznámeními
Aby bylo možné přijímat příchozí volání nabízených oznámení, zavolejte *handlePushNotification ()* na instanci *CallAgent* s datovou částí slovníku.

```swift

let dictionaryPayload = pushPayload?.dictionaryPayload
callAgent.handlePushNotification(payload: dictionaryPayload, completionHandler: { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
})

```
#### <a name="unregister-push-notification"></a>Zrušit registraci nabízeného oznámení

Aplikace můžou kdykoli zrušit registraci nabízených oznámení. Jednoduše zavolejte `unRegisterPushNotification` metodu na *CallAgent*.
> [!NOTE]
> Při odhlášení se aplikace neregistrují automaticky z nabízených oznámení.

```swift

callAgent.unRegisterPushNotifications(completionHandler: { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
})

```

## <a name="mid-call-operations"></a>Operace se středním voláním

Během volání můžete provádět různé operace ke správě nastavení souvisejících s videem a zvukem.

### <a name="mute-and-unmute"></a>Ztlumení a ztlumení

Chcete-li ztlumit nebo zrušit ztlumení místního koncového bodu, můžete použít `mute` `unmute` asynchronní rozhraní API a:

```swift
call!.mute(completionHandler: { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
})

```

Asynchronně Místní ztlumení

```swift
call!.unmute(completionHandler:{ (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
})
```

### <a name="start-and-stop-sending-local-video"></a>Spuštění a zastavení odesílání místního videa

Pokud chcete začít odesílat místní video jiným účastníkům ve volání, použijte `startVideo` rozhraní API a předejte ho. `localVideoStream``camera`

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager?.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    }
    else {
        print("Local video failed to start")
    }
}

```

Po zahájení odesílání videa `ACSLocalVideoStream` je instance přidána do `localVideoStreams` kolekce v instanci volání:

```swift

call.localVideoStreams[0]

```

Asynchronně Pokud chcete zastavit místní video, předejte `localVideoStream` vrácené volání z `call.startVideo` :

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="remote-participants-management"></a>Vzdálená správa účastníků

Všichni vzdálení účastníci jsou zastoupeni `ACSRemoteParticipant` typem a jsou k dispozici prostřednictvím `remoteParticipants` kolekce na instanci volání:

### <a name="list-participants-in-a-call"></a>Výpis účastníků ve volání

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>Vlastnosti vzdáleného účastníka

```swift

// [ACSRemoteParticipantDelegate] delegate - an object you provide to receive events from this ACSRemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
var identity = remoteParticipant.identity

// ACSParticipantStateIdle = 0, ACSParticipantStateEarlyMedia = 1, ACSParticipantStateConnecting = 2, ACSParticipantStateConnected = 3, ACSParticipantStateOnHold = 4, ACSParticipantStateInLobby = 5, ACSParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [ACSError] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// ACSRemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [ACSRemoteVideoStream, ACSRemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Přidání účastníka do volání

Chcete-li přidat účastníka do volání (buď uživatele, nebo telefonní číslo), můžete vyvolat `addParticipant` . Tím se synchronně vrátí instance vzdáleného účastníka.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUser(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Odebrání účastníka z volání
Chcete-li odebrat účastníka z volání (buď uživatele, nebo telefonní číslo), můžete  `removeParticipant` rozhraní API vyvolat. Tato akce bude vyřešena asynchronně.

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>Vykreslovat streamy videa vzdáleného účastníka

Vzdálení účastníci můžou během volání zahájit sdílení videa nebo obrazovky.

### <a name="handle-remote-participant-videoscreen-sharing-streams"></a>Zpracování streamů pro sdílení videí a obrazovek vzdáleného účastníka

Pokud chcete zobrazit seznam datových proudů vzdálených účastníků, Prozkoumejte tyto `videoStreams` kolekce:

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="remote-video-stream-properties"></a>Vlastnosti vzdáleného streamu videa

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'ACSMediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>Vykreslit vzdálený datový proud účastníka

Zahájení vykreslování vzdálených proudů účastníků:

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>Metody a vlastnosti vzdáleného vykreslovacího modulu videa

```swift
// [Bool] isRendering - indicating if stream is being rendered
remoteVideoRenderer.isRendering()
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="device-management"></a>Správa zařízení

`DeviceManager` umožňuje vytvořit výčet místních zařízení, která se dají použít při volání přenosů zvukových a obrazových streamů. Umožňuje taky požádat uživatele o oprávnění k přístupu k mikrofonu a kameře. K objektu můžete přistupovat `deviceManager` `callClient` :

```swift

self.callClient!.getDeviceManager(
    completionHandler: { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    })
```

### <a name="enumerate-local-devices"></a>Zobrazení výčtu místních zařízení

Chcete-li získat přístup k místním zařízením, můžete použít metody výčtu na Device Manager. Výčet je synchronní akce.

```swift
// enumerate local cameras
var localCameras = deviceManager.getCameraList() // [ACSVideoDeviceInfo, ACSVideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.getMicrophoneList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.getSpeakerList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>Nastavit výchozí mikrofon/mluvčí

Správce zařízení umožňuje nastavit výchozí zařízení, které se použije při spuštění volání. Pokud nejsou nastavené výchozí hodnoty zásobníku, komunikační služby se vrátí do výchozího nastavení operačního systému.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.getMicrophoneList()![0]
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.getSpeakerList()![0]
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="local-camera-preview"></a>Místní kamera verze Preview

Můžete použít `ACSRenderer` k zahájení vykreslování datového proudu z místní kamery. Tento datový proud se nebude posílat jiným účastníkům; je to místní kanál verze Preview. Toto je asynchronní akce.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = renderer!.createView()

```

### <a name="local-camera-preview-properties"></a>Vlastnosti místní kamery ve verzi Preview

Zobrazovací jednotka má sadu vlastností a metod, které umožňují řídit vykreslování:

```swift

// Constructor can take in ACSLocalVideoStream or ACSRemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [ACSStreamSize] size of the rendering view
localRenderer.size

// [ACSRendererDelegate] an object you provide to receive events from this ACSRenderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="eventing-model"></a>Model událostí

Můžete se přihlásit k odběru většiny vlastností a kolekcí, které budou oznamovány při změně hodnot.

### <a name="properties"></a>Vlastnosti
Přihlášení k odběru `property changed` událostí:

```swift
call.delegate = self
// Get the property of the call state by doing get on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Kolekce
Přihlášení k odběru `collection updated` událostí:

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: Call!,
                                       args: LocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
