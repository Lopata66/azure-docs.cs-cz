---
title: Grafika – vazba
description: Nastavení grafických vazeb a případů použití
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.custom: devx-track-csharp
ms.openlocfilehash: f769036ac9e5a6945e7ecad30e021d377cabd358
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020265"
---
# <a name="graphics-binding"></a>Grafika – vazba

Aby bylo možné používat vzdálené vykreslování Azure ve vlastní aplikaci, musí být integrovány do kanálu vykreslování aplikace. Tato integrace je odpovědností vazby grafiky.

Po nastavení vám vazba grafiky poskytne přístup k různým funkcím, které mají vliv na vykreslený obrázek. Tyto funkce mohou být rozděleny do dvou kategorií: Obecné funkce, které jsou vždy dostupné a specifické funkce, které jsou relevantní pouze pro vybrané `Microsoft.Azure.RemoteRendering.GraphicsApiType` .

## <a name="graphics-binding-in-unity"></a>Vazba grafiky v Unity

V Unity je celá vazba zpracována pomocí `RemoteUnityClientInit` předané struktury `RemoteManagerUnity.InitializeManager` . Chcete-li nastavit režim grafiky, musí `GraphicsApiType` být pole nastaveno na zvolenou vazbu. Pole se vyplní automaticky v závislosti na tom, jestli je k dispozici XRDevice. Chování se dá ručně přepsat pomocí následujícího chování:

* **HoloLens 2**: je vždycky používána vazba grafiky [Windows Mixed reality](#windows-mixed-reality) .
* **Plochá desktopová aplikace UWP**: [simulace](#simulation) se vždycky používá.
* **Editor Unity**: [simulace](#simulation) se vždycky používá, pokud se nepřipojí náhlavní souprava WMR VR. v takovém případě se zakáže, aby bylo možné ladit části aplikace související s nezávislou na Arr. Viz také [holografická Vzdálená komunikace](../how-tos/unity/holographic-remoting.md).

Jediná jiná relevantní část pro Unity přistupuje k [základní vazbě](#access). všechny ostatní níže uvedené oddíly lze vynechat.

## <a name="graphics-binding-setup-in-custom-applications"></a>Nastavení vazeb grafiky ve vlastních aplikacích

Chcete-li vybrat vazbu grafiky, proveďte následující dva kroky: nejprve je nutné, aby byla vazba grafiky staticky inicializována při inicializaci programu:

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.graphicsApi = GraphicsApiType::WmrD3D11;
managerInit.connectionType = ConnectionType::General;
managerInit.right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering
```

Výše uvedené volání je nezbytné k inicializaci vzdáleného vykreslování Azure do holografických rozhraní API. Tato funkce musí být volána před voláním jakéhokoli holografického rozhraní API a před tím, než budou k dispozici další rozhraní API pro vzdálené vykreslení. Podobně by měla být volána odpovídající funkce de-init po tom, `RemoteManagerStatic.ShutdownRemoteRendering();` co již nejsou volána žádná holografická rozhraní API.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Přístup k vazbě grafiky

Po nastavení klienta je k základní datové vazbě možné přistupovat pomocí `AzureSession.GraphicsBinding` metody getter. Jako příklad můžete získat statistiku posledního snímku takto:

```cs
AzureSession currentSession = ...;
if (currentSession.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (*binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>Rozhraní API grafiky

V současné době existují dvě grafická rozhraní API, která lze vybrat `WmrD3D11` a `SimD3D11` . Třetí existuje, `Headless` ale na straně klienta se ještě nepodporuje.

### <a name="windows-mixed-reality"></a>Windows Mixed reality

`GraphicsApiType.WmrD3D11` je výchozí vazba spouštěná na HoloLens 2. Vytvoří `GraphicsBindingWmrD3d11` vazbu. V tomto režimu se vzdálené vykreslování Azure zavěsí přímo do holografických rozhraní API.

Chcete-li získat přístup k odvozeným grafickým vazbám, musí `GraphicsBinding` být základem přetypování.
K použití vazby WMR je potřeba udělat dvě věci:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Informování o vzdáleném vykreslování použitého systému souřadnic

```cs
AzureSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (*wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```


Tam, kde výše `ptr` musí být ukazatel na nativní `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` objekt, který definuje systém souřadnic World Space, ve kterém jsou vyjádřeny souřadnice v rozhraní API.

#### <a name="render-remote-image"></a>Vykreslit vzdálenou bitovou kopii

Na začátku každého snímku musí být vzdálený rámec vykreslen do vyrovnávací paměti pro zpětnou kopii. To se provádí voláním metody `BlitRemoteFrame` , která do aktuálně vázaného cíle vykreslování vyplní informace o barvě a hloubce. Proto je důležité, aby se to dokončilo po vytvoření vazby back-bufferu jako cíle vykreslování.

```cs
AzureSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>Simulace

`GraphicsApiType.SimD3D11` je vazba simulace a pokud je vybrána, vytvoří se `GraphicsBindingSimD3d11` vazba grafiky. Toto rozhraní se používá k simulaci přesunu hlav, například v desktopové aplikaci, a vykreslí monoscopic obrázek.
Instalace je trochu větší a funguje takto:

#### <a name="create-proxy-render-target"></a>Vytvořit cíl vykreslování proxy

Vzdálený a místní obsah je potřeba vykreslit pro vykreslování barvy mimo obrazovku s názvem "proxy" pomocí dat z kamery proxy, které poskytuje `GraphicsBindingSimD3d11.Update` funkce. Proxy se musí shodovat s rozlišením pro zpětnou vyrovnávací paměť. Jakmile je relace připravena, je `GraphicsBindingSimD3d11.InitSimulation` nutné ji volat před připojením k této relaci:

```cs
AzureSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

Funkci init je nutné poskytnout ukazatelům na nativní zařízení D3D a také k barvě a hloubkové textuře cíle vykreslování proxy serveru. Po inicializaci `AzureSession.ConnectToRuntime` a `DisconnectFromRuntime` může být volána několikrát, ale při přepnutí na jinou relaci `GraphicsBindingSimD3d11.DeinitSimulation` je nutné nejprve volat původní relaci, aby bylo `GraphicsBindingSimD3d11.InitSimulation` možné je volat v jiné relaci.

#### <a name="render-loop-update"></a>Aktualizace smyčky vykreslování

Aktualizace smyčky vykreslování se skládá z několika kroků:

1. Každý rámec, před tím, než proběhne jakékoli vykreslování, `GraphicsBindingSimD3d11.Update` je volána s aktuální transformací kamery, která je odeslána na server, který má být vykreslen. V současné době by se měla vrátit vrácená transformace proxy serveru na kameru proxy serveru, aby se vykreslila do cíle vykreslování proxy serveru.
Pokud je vrácená aktualizace proxy `SimulationUpdate.frameId` null, zatím neexistují žádná Vzdálená data. V tomto případě, místo vykreslování do cíle vykreslování proxy, by měl být veškerý místní obsah vykreslen do vyrovnávací paměti přímo pomocí aktuálních dat kamery a následující dva kroky byly vynechány.
1. Aplikace by nyní měla vytvořit vazby k cíli vykreslování proxy a volání `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy` . Tím se zaplní informace o vzdálené barvě a hloubce do cíle vykreslování proxy serveru. Libovolný místní obsah se teď dá na proxy server vykreslovat pomocí transformace kamery proxy.
1. Dále musí být zpětná vyrovnávací paměť vázána jako cíl vykreslování a `GraphicsBindingSimD3d11.ReprojectProxy` volána, aby bylo možné zobrazit zpětnou vyrovnávací paměť.

```cs
AzureSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdate update;
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate;
simBinding->Update(update, &proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="next-steps"></a>Další kroky

* [Kurz: zobrazení vzdáleně generovaných modelů](../tutorials/unity/view-remote-models/view-remote-models.md)
