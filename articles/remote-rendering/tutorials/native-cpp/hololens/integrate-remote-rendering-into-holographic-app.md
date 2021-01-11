---
title: Remote Rendering integreren in een C++/DirectX11 Holographic-app
description: Hierin wordt uitgelegd hoe u een Remote Rendering integreert in een gewone C++ /DirectX11 Holographic-app die is gemaakt met de wizard Visual Studio-project
author: florianborn71
ms.author: flborn
ms.date: 05/04/2020
ms.topic: tutorial
ms.openlocfilehash: 44c80703466f91ccdfa33934efa0a05e699fd5de
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724388"
---
# <a name="tutorial-integrate-remote-rendering-into-a-hololens-holographic-app"></a>Zelfstudie: Remote Rendering integreren in een HoloLens Holographic-app

In deze zelfstudie leert u:

> [!div class="checklist"]
>
> * Visual Studio gebruiken om een Holographic-app te maken die kan worden geïmplementeerd op HoloLens
> * De benodigde codefragmenten en projectinstellingen toevoegen om lokale rendering te combineren met extern gerenderde inhoud

In deze zelfstudie leert u de benodigde bits aan een systeemeigen `Holographic App`-voorbeeld toe te voegen om lokale rendering met Azure Remote Rendering te combineren. Het enige type statusfeedback in deze app wordt doorgegeven aan het uitvoerpaneel voor foutopsporing in Visual Studio. Het is dan ook raadzaam om het voorbeeld te starten vanuit Visual Studio. Het toevoegen van de juiste in-app-feedback valt buiten het bereik van dit voorbeeld, omdat het maken van een dynamisch tekstpaneel veel codering vergt. Een goed uitgangspunt is klasse `StatusDisplay`, die deel uitmaakt van het [Remoting Player-voorbeeldproject op GitHub](https://github.com/microsoft/MixedReality-HolographicRemoting-Samples/tree/master/player/common/Content). De standaardversie van deze zelfstudie maakt gebruik van een lokale kopie van die klasse.

> [!TIP]
> De [ARR-opslagplaats voor voorbeelden](https://github.com/Azure/azure-remote-rendering) bevat het resultaat van deze zelfstudie als Visual Studio-project dat gereed is voor gebruik. Het wordt ook verrijkt met de juiste fout- en statusrapportage via UI-klasse `StatusDisplay`. In de zelfstudie worden alle ARR-specifieke toevoegingen beperkt door `#ifdef USE_REMOTE_RENDERING` / `#endif`, zodat de toevoegingen voor Remote Rendering gemakkelijk kunnen worden geïdentificeerd.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Uw accountinformatie (account-id, accountsleutel, accountdomein, abonnements-id). Als u nog geen account hebt, [maakt u er een](../../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0 [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk).
* De nieuwste versie van Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/).
* [Visual Studio Tools voor Mixed Reality](/windows/mixed-reality/install-the-tools). Met name de volgende *workload*-installaties zijn verplicht:
  * **Desktopontwikkeling met C++**
  * **Universal Windows Platform (UWP)-ontwikkeling**
* De Windows Mixed Reality-app-sjablonen voor Visual Studio [(download)](https://marketplace.visualstudio.com/items?itemName=WindowsMixedRealityteam.WindowsMixedRealityAppTemplatesVSIX).

## <a name="create-a-new-holographic-app-sample"></a>Een nieuw Holographic-app-voorbeeld maken

We maken eerst een standaardvoorbeeld dat de basis vormt voor de integratie van Remote Rendering. Open Visual Studio en selecteer Een nieuw project maken en zoek naar Holographic DirectX 11-app (Universal Windows) ( C++/WinRT)

![Nieuw project maken](media/new-project-wizard.png)

Voer een projectnaam in en kies een pad. Selecteer de knop Maken.
Schakel in het nieuwe project de configuratie naar **Debug / ARM64**. Nu moet u de app kunnen compileren en implementeren op een verbonden HoloLens 2-apparaat. Als u deze uitvoert op HoloLens, ziet u een roterende kubus.

## <a name="add-remote-rendering-dependencies-through-nuget"></a>Remote Rendering-afhankelijkheden via NuGet toevoegen

Eerst voegt u de afhankelijkheden aan de clientzijde toe wanneer u Remote Rendering-mogelijkheden toevoegt. Relevante afhankelijkheden zijn beschikbaar als een NuGet-pakket.
Klik in Solution Explorer met de rechtermuisknop op het project en selecteer **NuGet-pakketten beheren...** in het contextmenu.

Blader in het dialoogvenster dat wordt geopend naar het NuGet-pakket met de naam **Microsoft.Azure.RemoteRendering.Cpp**:

![Blader naar het NuGet-pakket](media/add-nuget.png)

en voeg dit aan het project toe door het te selecteren en op de knop Installeren te klikken.

Het NuGet-pakket voegt de Remote Rendering-afhankelijkheden aan het project toe. Met name:
* Maak een koppeling voor de clientbibliotheek (RemoteRenderingClient.lib).
* Stel de .dll-afhankelijkheden in.
* Stel het juiste pad naar de map include in.

## <a name="project-preparation"></a>Project voorbereiden

Er moeten kleine wijzigingen in het bestaande project worden ingevoerd. Deze wijzigingen zijn subtiel, maar zijn onmisbaar voor een goede werking van Remote Rendering.

### <a name="enable-multithread-protection-on-directx-device"></a>Multi-threadbeveiliging op DirectX-apparaat inschakelen
Op het `DirectX11`-apparaat moet multi-threadbeveiliging zijn ingeschakeld. Als u dit wilt wijzigen, opent u het bestand DeviceResources.cpp in de map Common en voegt u de volgende code in aan het einde van de functie `DeviceResources::CreateDeviceResources()`:

```cpp
// Enable multi thread protection as now multiple threads use the immediate context.
Microsoft::WRL::ComPtr<ID3D11Multithread> contextMultithread;
if (context.As(&contextMultithread) == S_OK)
{
    contextMultithread->SetMultithreadProtected(true);
}
```

### <a name="enable-network-capabilities-in-the-app-manifest"></a>Netwerkmogelijkheden inschakelen in het app-manifest
Netwerkmogelijkheden moeten expliciet zijn ingeschakeld voor de geïmplementeerde app. Als dit niet is geconfigureerd, zullen verbindingsquery's uiteindelijk time-outs opleveren. Dubbelklik op het item `package.appxmanifest` in Solution Explorer om het in te schakelen. Ga in de volgende gebruikersinterface naar het tabblad **Mogelijkheden** en selecteer:
* Internet (client en server)
* Internet (client)

![Netwerkmogelijkheden](media/appx-manifest-caps.png)


## <a name="integrate-remote-rendering"></a>Remote Rendering integreren

Nu het project is voorbereid, kunt u met de code beginnen. Een goed beginpunt in de toepassing is de klasse `HolographicAppMain`(bestand HolographicAppMain.h/cpp), omdat het alle benodigde hooks voor initialisatie, de-initialisatie en rendering bevat.

### <a name="includes"></a>Includes

Begin met het toevoegen van de benodigde includes. Voeg de volgende include toe aan het bestand HolographicAppMain.h:

```cpp
#include <AzureRemoteRendering.h>
```

...en deze aanvullende `include`-instructies aan het bestand HolographicAppMain.cpp:

```cpp
#include <AzureRemoteRendering.inl>
#include <RemoteRenderingExtensions.h>
#include <windows.perception.spatial.h>
```

Ter vereenvoudiging van de code wordt de volgende naamruimtesnelkoppeling bovenaan het bestand HolographicAppMain.h na de `include`-instructies gedefinieerd:

```cpp
namespace RR = Microsoft::Azure::RemoteRendering;
```

Deze snelkoppeling is handig, omdat niet de volledige naamruimte hoeft te worden geschreven, maar ARR-specifieke gegevensstructuren nog steeds worden herkend. Uiteraard kan de `using namespace...`-instructie ook worden gebruikt.

### <a name="remote-rendering-initialization"></a>Remote Rendering-initialisatie
 
Er moeten enkele objecten voor de sessie enzovoort worden bewaard tijdens de levensduur van de toepassing. De levensduur valt samen met de levensduur van het `HolographicAppMain`-object van de toepassing. Dit is de reden waarom objecten als leden aan klasse `HolographicAppMain` moeten worden toegevoegd. In de volgende stap worden de volgende klasseleden toegevoegd aan het bestand HolographicAppMain.h:

```cpp
class HolographicAppMain
{
    ...
    // members:
    std::string m_sessionOverride;                // if we have a valid session ID, we specify it here. Otherwise a new one is created
    RR::ApiHandle<RR::AzureFrontend> m_frontEnd;  // the front end instance
    RR::ApiHandle<RR::AzureSession> m_session;    // the current remote rendering session
    RR::ApiHandle<RR::RemoteManager> m_api;       // the API instance, that is used to perform all the actions. This is just a shortcut to m_session->Actions()
    RR::ApiHandle<RR::GraphicsBindingWmrD3d11> m_graphicsBinding; // the graphics binding instance
}
```

Een goede plaats voor de daadwerkelijke implementatie is de constructor van klasse `HolographicAppMain`. Er moeten hier drie initialisatietypen worden uitgevoerd:
1. De eenmalige initialisatie van het Remote Rendering-systeem
1. Front-end maken
1. Sessie maken

Dit wordt allemaal opeenvolgend in de constructor uitgevoerd. In echte gebruiksgevallen kan het echter beter zijn om deze stappen afzonderlijk uit te voeren.

Voeg de volgende code toe aan het begin van de constructorhoofdcode in het bestand HolographicAppMain.cpp:

```cpp
HolographicAppMain::HolographicAppMain(std::shared_ptr<DX::DeviceResources> const& deviceResources) :
    m_deviceResources(deviceResources)
{
    // 1. One time initialization
    {
        RR::RemoteRenderingInitialization clientInit;
        clientInit.connectionType = RR::ConnectionType::General;
        clientInit.graphicsApi = RR::GraphicsApiType::WmrD3D11;
        clientInit.toolId = "<sample name goes here>"; // <put your sample name here>
        clientInit.unitsPerMeter = 1.0f;
        clientInit.forward = RR::Axis::Z_Neg;
        clientInit.right = RR::Axis::X;
        clientInit.up = RR::Axis::Y;
        if (RR::StartupRemoteRendering(clientInit) != RR::Result::Success)
        {
            // something fundamental went wrong with the initialization
            throw std::exception("Failed to start remote rendering. Invalid client init data.");
        }
    }


    // 2. Create front end
    {
        // Users need to fill out the following with their account data and model
        RR::AzureFrontendAccountInfo init;
        init.AccountId = "00000000-0000-0000-0000-000000000000";
        init.AccountKey = "<account key>";
        init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to the region that the rendering session should be created in>
        init.AccountAuthenticationDomain = "westus2.mixedreality.azure.com"; // <change to the region the account was created in>
        m_modelURI = "builtin://Engine";
        m_sessionOverride = ""; // If there is a valid session ID to re-use, put it here. Otherwise a new one is created

        m_frontEnd = RR::ApiHandle(RR::AzureFrontend(init));
    }

    // 3. Open/create rendering session
    {
        bool createNewSession = true;

        // If we had an old (valid) session that we can recycle, we call synchronous function m_frontEnd->OpenRenderingSession
        if (!m_sessionOverride.empty())
        {
            auto openSessionRes = m_frontEnd->OpenRenderingSession(m_sessionOverride);
            if (openSessionRes->valid())
            {
                SetNewSession(*openSessionRes);
                createNewSession = false;
            }
        }

        if (createNewSession)
        {
            // create a new session
            RR::RenderingSessionCreationParams init;
            init.MaxLease.minute = 10; // session is leased for 10 minutes
            init.Size = RR::RenderingSessionVmSize::Standard;
            auto createSessionAsync = *m_frontEnd->CreateNewRenderingSessionAsync(init);
            createSessionAsync->Completed([&](auto handler)
                {
                    if (handler->GetStatus() == RR::Result::Success)
                    {
                        SetNewSession(handler->GetResult());
                    }
                    else
                    {
                        SetNewState(AppConnectionStatus::ConnectionFailed, "failed");
                    }
                });
            SetNewState(AppConnectionStatus::CreatingSession, nullptr);
        }
    }

    // Rest of constructor code:
    ...
}
```

De code roept lidfuncties `SetNewSession` en `SetNewState` aan, die in de volgende alinea worden geïmplementeerd, samen met de rest van de statusmachinecode.

Houd er rekening mee dat referenties in het voorbeeld zijn vastgelegd in code en moeten worden ingevuld ([account-id, accountsleutel, accountdomein](../../../how-tos/create-an-account.md#retrieve-the-account-information) en [Remote Rendering-domein](../../../reference/regions.md)).

De de-initialisatie wordt symmetrisch en in omgekeerde volgorde uitgevoerd aan het einde van de destructorhoofdcode:

```cpp
HolographicAppMain::~HolographicAppMain()
{
    // Existing destructor code:
    ...
    
    // Destroy session:
    if (m_session != nullptr)
    {
        m_session->DisconnectFromRuntime();
        m_session = nullptr;
    }

    // Destroy front end:
    m_frontEnd = nullptr;

    // One-time de-initialization:
    RR::ShutdownRemoteRendering();
}
```

## <a name="state-machine"></a>Statuscomputer

In Remote Rendering zijn het maken van een sessie en het laden van een model asynchrone functies. Voor dit account hebt u een eenvoudige statuscomputer nodig die in feite de volgende statussen automatisch doorloopt:

*Initialisatie -> Sessie maken -> Sessie starten-> Model laden (met voortgang)*

In de volgende stap wordt een beetje van de statuscomputerverwerking aan de klasse toegevoegd. Uw enum `AppConnectionStatus` wordt gedeclareerd voor de verschillende statussen die uw toepassing kan hebben. Het is vergelijkbaar met `RR::ConnectionStatus`, maar heeft een extra status voor een mislukte verbinding.

Voeg de volgende leden en functies toe aan de klassedeclaratie:

```cpp
namespace HolographicApp
{
    // Our application's possible states:
    enum class AppConnectionStatus
    {
        Disconnected,

        CreatingSession,
        StartingSession,
        Connecting,
        Connected,

        // error state:
        ConnectionFailed,
    };

    class HolographicAppMain
    {
        ...
        // Member functions for state transition handling
        void OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error);
        void SetNewState(AppConnectionStatus state, const char* statusMsg);
        void SetNewSession(RR::ApiHandle<RR::AzureSession> newSession);
        void StartModelLoading();

        // Members for state handling:

        // Model loading:
        std::string m_modelURI;
        RR::ApiHandle<RR::LoadModelAsync> m_loadModelAsync;

        // Connection state machine:
        AppConnectionStatus m_currentStatus = AppConnectionStatus::Disconnected;
        std::string m_statusMsg;
        RR::Result m_connectionResult = RR::Result::Success;
        RR::Result m_modelLoadResult = RR::Result::Success;
        bool m_isConnected = false;
        bool m_sessionStarted = false;
        RR::ApiHandle<RR::SessionPropertiesAsync> m_sessionPropertiesAsync;
        bool m_modelLoadTriggered = false;
        float m_modelLoadingProgress = 0.f;
        bool m_modelLoadFinished = false;
        double m_timeAtLastRESTCall = 0;
        bool m_needsCoordinateSystemUpdate = true;
    }
```

Voeg de volgende functiehoofdcodes toe aan de implementatiezijde in het .cpp-bestand:

```cpp
void HolographicAppMain::StartModelLoading()
{
    m_modelLoadingProgress = 0.f;

    RR::LoadModelFromSASParams params;
    params.ModelUrl = m_modelURI.c_str();
    params.Parent = nullptr;

    // Start the async model loading
    if (auto loadModel = m_api->LoadModelFromSASAsync(params))
    {
        m_loadModelAsync = *loadModel;
        m_loadModelAsync->Completed([this](const RR::ApiHandle<RR::LoadModelAsync>& async)
        {
            m_modelLoadResult = async->GetStatus();
            m_modelLoadFinished = true; // successful if m_modelLoadResult==RR::Result::Success
            m_loadModelAsync = nullptr;
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading completed. Result: %s\n", RR::ResultToString(m_modelLoadResult));
            OutputDebugStringA(buffer);
            });
        m_loadModelAsync->ProgressUpdated([this](float progress)
        {
            // Progress callback
            m_modelLoadingProgress = progress;

            // Output progress percentage to VS output
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading progress: %.1f\n", m_modelLoadingProgress * 100.f);
            OutputDebugStringA(buffer);
        });
    }
}



void HolographicAppMain::SetNewState(AppConnectionStatus state, const char* statusMsg)
{
    m_currentStatus = state;
    m_statusMsg = statusMsg ? statusMsg : "";

    // Some log for the VS output panel:
    const char* appStatus = nullptr;

    switch (state)
    {
        case AppConnectionStatus::Disconnected: appStatus = "Disconnected"; break;
        case AppConnectionStatus::CreatingSession: appStatus = "CreatingSession"; break;
        case AppConnectionStatus::StartingSession: appStatus = "StartingSession"; break;
        case AppConnectionStatus::Connecting: appStatus = "Connecting"; break;
        case AppConnectionStatus::Connected: appStatus = "Connected"; break;
        case AppConnectionStatus::ConnectionFailed: appStatus = "ConnectionFailed"; break;
    }

    char buffer[1024];
    sprintf_s(buffer, "Remote Rendering: New status: %s, result: %s\n", appStatus, m_statusMsg.c_str());
    OutputDebugStringA(buffer);
}

void HolographicAppMain::SetNewSession(RR::ApiHandle<RR::AzureSession> newSession)
{
    SetNewState(AppConnectionStatus::StartingSession, nullptr);

    m_timeAtLastRESTCall = m_timer.GetTotalSeconds();
    m_session = newSession;
    m_api = m_session->Actions();
    m_graphicsBinding = m_session->GetGraphicsBinding().as<RR::GraphicsBindingWmrD3d11>();
    m_session->ConnectionStatusChanged([this](auto status, auto error)
        {
            OnConnectionStatusChanged(status, error);
        });

};

void HolographicAppMain::OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error)
{
    const char* asString = RR::ResultToString(error);
    m_connectionResult = error;

    switch (status)
    {
    case RR::ConnectionStatus::Connecting:
        SetNewState(AppConnectionStatus::Connecting, asString);
        break;
    case RR::ConnectionStatus::Connected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Connected, asString);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, asString);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = error == RR::Result::Success;
        break;
    case RR::ConnectionStatus::Disconnected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Disconnected, asString);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, asString);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = false;
        break;
    default:
        break;
    }
    
}
```

### <a name="per-frame-update"></a>Update per frame

We moeten de client eenmaal per simulatietik bijwerken en enkele aanvullende status-updates uitvoeren. Functie `HolographicAppMain::Update` biedt een goede hook voor updates per frame.

#### <a name="state-machine-update"></a>Update van toestandsmachine

We moeten de status van de sessie controleren en bekijken of deze is overgegaan naar de status `Ready`. Als er verbinding is gemaakt, gaan we het model laden via `StartModelLoading`.

Voeg de volgende code toe aan de hoofdtekst van de functie `HolographicAppMain::Update`:

```cpp
// Updates the application state once per frame.
HolographicFrame HolographicAppMain::Update()
{
    if (m_session != nullptr)
    {
        // Tick the client to receive messages
        m_api->Update();

        if (!m_sessionStarted)
        {
            // Important: To avoid server-side throttling of the requests, we should call GetPropertiesAsync very infrequently:
            const double delayBetweenRESTCalls = 10.0;

            // query session status periodically until we reach 'session started'
            if (m_sessionPropertiesAsync == nullptr && m_timer.GetTotalSeconds() - m_timeAtLastRESTCall > delayBetweenRESTCalls)
            {
                m_timeAtLastRESTCall = m_timer.GetTotalSeconds();
                if (auto propAsync = m_session->GetPropertiesAsync())
                {
                    m_sessionPropertiesAsync = *propAsync;
                    m_sessionPropertiesAsync->Completed([this](const RR::ApiHandle<RR::SessionPropertiesAsync>& async)
                        {
                            if (async->GetStatus() == RR::Result::Success)
                            {
                                auto res = async->GetResult();
                                switch (res.Status)
                                {
                                case RR::RenderingSessionStatus::Ready:
                                {
                                    // The following is async, but we'll get notifications via OnConnectionStatusChanged
                                    m_sessionStarted = true;
                                    SetNewState(AppConnectionStatus::Connecting, nullptr);
                                    RR::ConnectToRuntimeParams init;
                                    init.ignoreCertificateValidation = false;
                                    init.mode = RR::ServiceRenderMode::Default;
                                    m_session->ConnectToRuntime(init);
                                }
                                break;
                                case RR::RenderingSessionStatus::Error:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session error");
                                    break;
                                case RR::RenderingSessionStatus::Stopped:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session stopped");
                                    break;
                                case RR::RenderingSessionStatus::Expired:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session expired");
                                    break;
                                }
    
                            }
                            else
                            {
                                SetNewState(AppConnectionStatus::ConnectionFailed, "Failed to retrieve session status");
                            }
                            m_sessionPropertiesAsync = nullptr; // next try
                            m_needsStatusUpdate = true;
                        });
                }
            }
        }
        if (m_isConnected && !m_modelLoadTriggered)
        {
            m_modelLoadTriggered = true;
            StartModelLoading();
        }
    }

    if (m_needsCoordinateSystemUpdate && m_stationaryReferenceFrame && m_graphicsBinding)
    {
        // Set the coordinate system once. This must be called again whenever the coordinate system changes.
        winrt::com_ptr<ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem> ptr{ m_stationaryReferenceFrame.CoordinateSystem().as<ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem>() };
        m_graphicsBinding->UpdateUserCoordinateSystem(ptr.get());
        m_needsCoordinateSystemUpdate = false;
    }

    // Rest of the body:
    ...
}
```

#### <a name="coordinate-system-update"></a>Update coördinatensysteem

We moeten overeenstemming bereiken met de rendering-service over welk coördinatensysteem moet worden gebruikt. Om toegang te krijgen tot het coördinatensysteem dat we willen gebruiken, hebben we de `m_stationaryReferenceFrame` nodig die aan het einde van de functie `HolographicAppMain::OnHolographicDisplayIsAvailableChanged` is gemaakt.

Dit coördinatensysteem wordt doorgaans niet gewijzigd, dus dit is een eenmalige initialisatie. Dit moet opnieuw worden aangeroepen als uw toepassing het coördinatensysteem wijzigt.

De bovenstaande code stelt het coördinatensysteem eenmaal in binnen de functie `Update`, zodra er een referentiecoördinatensysteem en een verbonden sessie is.

#### <a name="camera-update"></a>Camera-update

De clipvlakken van de camera moeten worden bijgewerkt, zodat de servercamera gesynchroniseerd blijft met de lokale camera. Dit kan aan het eind van de `Update`functie:

```cpp
    ...
    if (m_isConnected)
    {
        // Any near/far plane values of your choosing.
        constexpr float fNear = 0.1f;
        constexpr float fFar = 10.0f;
        for (HolographicCameraPose const& cameraPose : prediction.CameraPoses())
        {
            // Set near and far to the holographic camera as normal
            cameraPose.HolographicCamera().SetNearPlaneDistance(fNear);
            cameraPose.HolographicCamera().SetFarPlaneDistance(fFar);
        }

        // The API to inform the server always requires near < far. Depth buffer data will be converted locally to match what is set on the HolographicCamera.
        auto settings = m_api->GetCameraSettings();
        settings->SetNearAndFarPlane(std::min(fNear, fFar), std::max(fNear, fFar));
        settings->SetEnableDepth(true);
    }

    // The holographic frame will be used to get up-to-date view and projection matrices and
    // to present the swap chain.
    return holographicFrame;
}

```

### <a name="rendering"></a>Rendering

De laatste stap is het aanroepen van de rendering van de externe inhoud. Deze aanroep moet worden uitgevoerd op exact de juiste positie binnen de renderingspijplijn, nadat het doel voor het renderen is gewist en de viewport is ingesteld. Voeg het volgende fragment in de `UseHolographicCameraResources`-vergrendeling en in de functie `HolographicAppMain::Render` in:

```cpp
        ...
        // Existing clear function:
        context->ClearDepthStencilView(depthStencilView, D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);
        
        // ...

        // Exiting check to test for valid camera:
        bool cameraActive = pCameraResources->AttachViewProjectionBuffer(m_deviceResources);


        // Inject remote rendering: as soon as we are connected, start blitting the remote frame.
        // We do the blitting after the Clear, and before cube rendering.
        if (m_isConnected && cameraActive)
        {
            m_graphicsBinding->BlitRemoteFrame();
        }

        ...
```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Het voorbeeld moet nu een status hebben waarin het wordt gecompileerd en uitgevoerd.

Wanneer het voorbeeld correct wordt uitgevoerd, wordt de roterende kubus weergegeven. Na het maken van een sessie en het laden van een model wordt het enginemodel op de huidige hoofdpositie weergegeven. Het maken van sessies en het laden van modellen kan enkele minuten duren. De huidige status wordt alleen naar het uitvoerpaneel van Visual Studio geschreven. Het wordt dan ook aangeraden om het voorbeeld in Visual Studio te starten.

> [!CAUTION]
> De client verbreekt de verbinding met de server wanneer de tikfunctie een paar seconden niet wordt aangeroepen. Het activeren van onderbrekingspunten kan er zeer gemakkelijk toe leiden dat de toepassing de verbinding verbreekt.

Raadpleeg de standaardversie van deze zelfstudie op GitHub voor een juiste statusweergave met een tekstpaneel.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u alle stappen geleerd die nodig zijn om Remote Rendering toe te voegen aan een standaardvoorbeeld van **Holographic-app** C++/DirectX11.
Raadpleeg de volgende quickstart om uw eigen model te converteren:

> [!div class="nextstepaction"]
> [Snelstart: een model converteren voor rendering](../../../quickstarts/convert-model.md)