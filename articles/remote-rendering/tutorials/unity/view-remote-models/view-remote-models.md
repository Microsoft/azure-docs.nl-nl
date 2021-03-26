---
title: U bekijkt een model dat extern wordt weergegeven
description: In de zelfstudie 'Hallo wereld' van Azure Remote Rendering ontdekt u hoe u een Model dat extern gegenereerd is door Azure kunt weergeven
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: b98f5253cc60edc8949c06a645d81849dff2de7c
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543683"
---
# <a name="tutorial-viewing-a-remotely-rendered-model"></a>Zelfstudie: U bekijkt een model dat extern wordt weergegeven

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een instantie van Azure Remote Rendering (ARR) inrichten
> * Een rendering-sessie maken en stoppen
> * Een bestaande rendering-sessie opnieuw gebruiken
> * Verbinding maken en de verbinding verbreken met sessies
> * Modellen laden in een rendering-sessie

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Een actief Azure-abonnement voor betalen naar gebruik [Een account maken](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)
* Windows SDK 10.0.18362.0 [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* De nieuwste versie van Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* Git [(downloaden)](https://git-scm.com/downloads)
* Unity (Zie [systeem vereisten](../../../overview/system-requirements.md#unity) voor ondersteunde versies)
* Basiskennis van Unity en de C# -taal (bijvoorbeeld: scripts en objecten maken, prefabs gebruiken, Unity-gebeurtenissen configureren enz.)

## <a name="provision-an-azure-remote-rendering-arr-instance"></a>Een instantie van Azure Remote Rendering (ARR) inrichten

Als u toegang wilt krijgen tot de Azure Remote Rendering-service, moet u eerst [een account maken](../../../how-tos/create-an-account.md#create-an-account).

## <a name="create-a-new-unity-project"></a>Een nieuw Unity-project maken

> [!TIP]
> De [opslagplaats voor ARR-voorbeelden](https://github.com/Azure/azure-remote-rendering) bevat een project waarin alle zelfstudies voltooid zijn, u kunt dit gebruiken ter referentie. Kijk in *Unity\Tutorial-Complete* voor het voltooide Unity-project.

Maak vanuit de Unity-hub een nieuw project.
In dit voorbeeld wordt ervan uitgegaan dat het project wordt gemaakt in een map met de naam **RemoteRendering**.

:::image type="content" source="./media/unity-new-project.PNG" alt-text="Nieuw Unity-project":::

## <a name="include-the-azure-remote-rendering-package"></a>Het Azure Remote Rendering-pakket toevoegen

[Volg de instructies](../../../how-tos/unity/install-remote-rendering-unity-package.md) voor het toevoegen van het pakket voor externe rendering van Azure aan een Unity-project.


## <a name="configure-the-camera"></a>De camera configureren

1. Selecteer het **hoofdcamera**-knooppunt.

1. Open het contextmenu door met de rechtermuisknop op de component *Transformatie* te klikken en de optie **Opnieuw instellen** te selecteren:

    ![cameratransformatie opnieuw instellen](./media/camera-reset-transform.png)

1. Stel **Vlaggen wissen** in op *Effen kleur*

1. Stel **Achtergrond** in op *Zwart* (#000000), met volledig transparante (0) alfa (A)

    ![Kleurenwiel](./media/color-wheel-black.png)

1. Stel **Grensvlakken** in op *Nabij = 0,3* en *Ver = 20*. Dit betekent dat rendering geometrie zal afknippen/bijsnijden van dichter dan 30 cm of verder dan 20 meter.

    ![Eigenschappen van Unity-camera](./media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>De projectinstellingen aanpassen

1. Open *Bewerken > Projectinstellingen...*
1. Selecteer **Kwaliteit** in het lijstmenu links
1. Wijzig het **Standaardkwaliteitsniveau** van alle platforms naar *Laag*. Deze instelling zorgt voor een efficiëntere rendering van lokale inhoud en heeft geen invloed op de kwaliteit van extern gegenereerde inhoud.

    ![instellingen voor projectkwaliteit wijzigen](./media/settings-quality.png)

1. Selecteer **Afbeeldingen** in het lijstmenu links
1. Wijzig de instelling **Scriptbare rendering-pijplijn** in *HybridRenderingPipeline*.\
    ![Schermopname met pijl die wijst naar plek waar u de instelling Scriptable Rendering Pipeline wijzigt in HybridRenderingPipeline.](./media/settings-graphics-render-pipeline.png)\
    Soms vult de gebruikersinterface de lijst met beschikbare pijplijntypen van de pakketten niet in. Als dit het geval is, moet de *HybridRenderingPipeline*-asset handmatig naar het veld worden gesleept:\
    ![instellingen voor projectafbeeldingen wijzigen](./media/hybrid-rendering-pipeline.png)

    > [!NOTE]
    > Als u de *HybridRenderingPipeline*-asset niet kunt slepen en neerzetten in het veld Pijplijn-asset renderen (mogelijk omdat het veld niet bestaat!), zorg er dan voor dat uw pakketconfiguratie het `com.unity.render-pipelines.universal`-pakket bevat.

1. Selecteer **Speler** in het lijstmenu links
1. Selecteer het tabblad **Instellingen universeel Windows-platform**, dat wordt weergegeven als een Windows-pictogram.
1. Wijzig de **XR-instellingen** om Windows Mixed Reality te ondersteunen zoals hieronder te zien is:
    1. Schakel **Virtual Reality ondersteund** in
    1. Druk op de knop '+' en voeg **Windows Mixed Reality** toe
    1. **Diepte-indeling instellen** op *16-bits diepte*
    1. Zorg ervoor dat **Diepte buffer delen** is ingeschakeld
    1. Stel **Stereo renderingsmodus** in op *Single Pass Instanced*

    ![instellingen voor speler](./media/xr-player-settings.png)

1. Vouw in hetzelfde venster boven **XR-instellingen** **Publicatie-instellingen uit.**
1. Schuif omlaag naar **Mogelijkheden** en selecteer:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * **PrivateNetworkClientServer** (*optioneel*). Selecteer deze optie als u de Unity-foutopsporing met uw apparaat wilt verbinden.

1. Schakel onder **Ondersteunde apparaten**, **Holographic** en **Desktop** in
1. Sluit of zet het paneel **Projectinstellingen**
1. Open *Bestand->Build-instellingen*
1. Selecteer **Universeel Windows-platform**
1. Configureer uw instellingen zodat deze overeenkomen met die hieronder
1. Druk op de knop **Platform veranderen**.\
![build-instellingen](./media/build-settings.png)
1. Nadat Unity het platform heeft veranderd, sluit u het build-deelvenster.

## <a name="validate-project-setup"></a>Projectinstellingen valideren

Voer de volgende stappen uit om te controleren of de projectinstellingen juist zijn.

1. Kies de vermelding **ValidateProject** in het menu **RemoteRendering** in de werkbalk van de Unity-editor.
1. Controleer het venster **ValidateProject** op fouten en herstel waar nodig projectinstellingen.

    ![Projectvalidatie in Unity-editor](./media/remote-render-unity-validation.png)

## <a name="create-a-script-to-coordinate-azure-remote-rendering-connection-and-state"></a>Een script maken om de verbinding en status van Azure Remote Rendering te coördineren

Er zijn vier basisfasen voor het weergeven van extern gegenereerde modellen, zoals te zien is in het onderstaande stroomdiagram. Elke fase moet in de aangegeven volgorde worden uitgevoerd. De volgende stap is om een script te maken voor het beheren van de toepassingsstatus en alle vereiste fasen te doorlopen.

![ARR-stack 0](./media/remote-render-stack-0.png)

1. Maak in het deelvenster *Project* onder **Assets** een nieuwe map met de naam *RemoteRenderingCore*. Maak vervolgens in *RemoteRenderingCore* een andere map met de naam *Scripts*.

1. Maak een [nieuw C#-script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) met de naam **RemoteRenderingCoordinator**.
Uw project moet er als volgt uitzien:

    ![Projecthiërarchie](./media/project-structure.png)

    Met dit coördinatorscript kunt u de status van de externe rendering opvolgen en beheren. Merk op dat een deel van deze code gebruikt wordt om status te onderhouden, functionaliteit weer te geven aan andere componenten, gebeurtenissen te activeren en toepassingsspecifieke gegevens die niet *rechtstreeks* verwant zijn met Azure Remote Rendering op te slaan. Gebruik de onderstaande code als uitgangspunt. We bespreken en implementeren de specifieke code van Azure Remote Rendering verderop in de zelfstudie.

1. Open **RemoteRenderingCoordinator** in uw code-editor en vervang de volledige inhoud door de onderstaande code:

```cs
// Copyright (c) Microsoft Corporation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Linq;
using System.Threading.Tasks;
using UnityEngine;
using UnityEngine.Events;

#if UNITY_WSA
using UnityEngine.XR.WSA;
#endif

/// <summary>
/// Remote Rendering Coordinator is the controller for all Remote Rendering operations.
/// </summary>

// Require the GameObject with a RemoteRenderingCoordinator to also have the ARRServiceUnity component
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRenderingCoordinator : MonoBehaviour
{
    public enum RemoteRenderingState
    {
        NotSet,
        NotInitialized,
        NotAuthorized,
        NoSession,
        ConnectingToExistingRemoteSession,
        ConnectingToNewRemoteSession,
        RemoteSessionReady,
        ConnectingToRuntime,
        RuntimeConnected
    }

    public static RemoteRenderingCoordinator instance;

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // The list of regions is available at https://docs.microsoft.com/azure/remote-rendering/reference/regions
    [SerializeField]
    private string accountDomain = "westus2.mixedreality.azure.com";
    public string AccountDomain
    {
        get => accountDomain.Trim();
        set => accountDomain = value;
    }

    [Header("Development Account Credentials")]
    [SerializeField]
    private string accountId = "<enter your account id here>";
    public string AccountId {
        get => accountId.Trim();
        set => accountId = value;
    }

    [SerializeField]
    private string accountAuthenticationDomain = "<enter your account authentication domain here>";
    public string AccountAuthenticationDomain
    {
        get => accountAuthenticationDomain.Trim();
        set => accountAuthenticationDomain = value;
    }   

    [SerializeField]
    private string accountKey = "<enter your account key here>";
    public string AccountKey {
        get => accountKey.Trim();
        set => accountKey = value;
    }

    // These settings are important. All three should be set as low as possible, while maintaining a good user experience
    // See the documentation around session management and the technical differences in session VM size
    [Header("New Session Defaults")]

    public RenderingSessionVmSize renderingSessionVmSize = RenderingSessionVmSize.Standard;
    public uint maxLeaseHours = 0;
    public uint maxLeaseMinutes = 20;

    [Header("Other Configuration")]

    [Tooltip("If you have a known active SessionID, you can fill it in here before connecting")]
    [SerializeField]
    private string sessionIDOverride;
    public string SessionIDOverride {
        get => sessionIDOverride.Trim();
        set => sessionIDOverride = value;
    }

    // When Automatic Mode is true, the coordinator will attempt to automatically proceed through the process of connecting and loading a model
    public bool automaticMode = true;

    public event Action RequestingAuthorization;
    public UnityEvent OnRequestingAuthorization = new UnityEvent();

    public event Action AuthorizedChanged;
    public UnityEvent OnAuthorizationChanged = new UnityEvent();
    private bool authorized;
    public bool Authorized
    {
        get => authorized;
        set
        {
            if (value == true) //This is a one-way value, once we're authorized it lasts until the app is shutdown.
            {
                authorized = value;
                AuthorizedChanged?.Invoke();
            }
        }
    }

    public delegate Task<SessionConfiguration> AccountInfoGetter();

    public static AccountInfoGetter ARRCredentialGetter
    {
        private get;
        set;
    }

    private RemoteRenderingState currentCoordinatorState = RemoteRenderingState.NotSet;
    public RemoteRenderingState CurrentCoordinatorState
    {
        get => currentCoordinatorState;
        private set
        {
            if (currentCoordinatorState != value)
            {
                currentCoordinatorState = value;
                Debug.Log($"State changed to: {currentCoordinatorState}");
                CoordinatorStateChange?.Invoke(currentCoordinatorState);
            }
        }
    }

    public static event Action<RemoteRenderingState> CoordinatorStateChange;

    public static RenderingSession CurrentSession => instance?.ARRSessionService?.CurrentActiveSession;

    private ARRServiceUnity arrSessionService;

    private ARRServiceUnity ARRSessionService
    {
        get
        {
            if (arrSessionService == null)
                arrSessionService = GetComponent<ARRServiceUnity>();
            return arrSessionService;
        }
    }

    private async Task<SessionConfiguration> GetDevelopmentCredentials()
    {
        Debug.LogWarning("Using development credentials! Not recommended for production.");
        return await Task.FromResult(new SessionConfiguration(AccountAuthenticationDomain, AccountDomain, AccountId, AccountKey));
    }

    /// <summary>
    /// Keep the last used SessionID, when launching, connect to this session if its available
    /// </summary>
    private string LastUsedSessionID
    {
        get
        {
            if (!string.IsNullOrEmpty(SessionIDOverride))
                return SessionIDOverride;

            if (PlayerPrefs.HasKey("LastUsedSessionID"))
                return PlayerPrefs.GetString("LastUsedSessionID");
            else
                return null;
        }
        set
        {
            PlayerPrefs.SetString("LastUsedSessionID", value);
        }
    }

    public void Awake()
    {
        //Forward events to Unity events
        RequestingAuthorization += () => OnRequestingAuthorization?.Invoke();
        AuthorizedChanged += () => OnAuthorizationChanged?.Invoke();

        //Attach to event
        AuthorizedChanged += RemoteRenderingCoordinator_AuthorizedChanged;

        if (instance == null)
            instance = this;
        else
            Destroy(this);

        CoordinatorStateChange += AutomaticMode;

        CurrentCoordinatorState = RemoteRenderingState.NotInitialized;
    }

    private void RemoteRenderingCoordinator_AuthorizedChanged()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.NotAuthorized)
            return; //This isn't valid from any other state than NotAuthorized


        //We just became authorized to connect to Azure
        InitializeSessionService();
    }

    /// <summary>
    /// Automatic mode attempts to automatically progress through the connection and loading steps. Doesn't handle error states.
    /// </summary>
    /// <param name="currentState">The current state</param>
    private async void AutomaticMode(RemoteRenderingState currentState)
    {
        if (!automaticMode)
            return;

        //Add a small delay for visual effect
        await Task.Delay(1500);
        switch (currentState)
        {
            case RemoteRenderingState.NotInitialized:
                InitializeARR();
                break;
            case RemoteRenderingState.NotAuthorized:
                RequestAuthorization();
                break;
            case RemoteRenderingState.NoSession:
                JoinRemoteSession();
                break;
            case RemoteRenderingState.RemoteSessionReady:
                ConnectRuntimeToRemoteSession();
                break;
        }
    }

    /// <summary>
    /// Initializes ARR, associating the main camera
    /// Note: This must be called on the main Unity thread
    /// </summary>
    public void InitializeARR()
    {
        //Implement me
    }

    /// <summary>
    /// Create a new remote session manager
    /// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
    /// </summary>
    public async void InitializeSessionService()
    {
        //Implement me
    }

    /// <summary>
    /// Trigger the event for checking authorization, respond to this event by prompting the user for authentication
    /// If authorized, set Authorized = true
    /// </summary>
    public void RequestAuthorization()
    {
        RequestingAuthorization?.Invoke();
    }

    public void BypassAuthorization()
    {
        Authorized = true;
    }

    /// <summary>
    /// Attempts to join an existing session or start a new session
    /// </summary>
    public async void JoinRemoteSession()
    {
        //Implement me
    }

    public void StopRemoteSession()
    {
        //Implement me
    }

    private async Task<bool> IsSessionAvailable(string sessionID)
    {
        var allSessions = await ARRSessionService.Client.GetCurrentRenderingSessionsAsync();
        return allSessions.SessionProperties.Any(x => x.Id == sessionID && (x.Status == RenderingSessionStatus.Ready || x.Status == RenderingSessionStatus.Starting));
    }

    /// <summary>
    /// Connects the local runtime to the current active session, if there's a session available
    /// </summary>
    public void ConnectRuntimeToRemoteSession()
    {
        //Implement me
    }

    public void DisconnectRuntimeFromRemoteSession()
    {
        //Implement me
    }

    /// <summary>
    /// The session must have its runtime pump updated.
    /// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
    /// </summary>
    private void LateUpdate()
    {
        ARRSessionService?.CurrentActiveSession?.Connection?.Update();
    }

    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelPath">The model's path</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Implement me
        return null;
    }

    private async void OnRemoteSessionStatusChanged(ARRServiceUnity caller, RenderingSession session)
    {
        var properties = await session.GetPropertiesAsync();

        switch (properties.SessionProperties.Status)
        {
            case RenderingSessionStatus.Error:
            case RenderingSessionStatus.Expired:
            case RenderingSessionStatus.Stopped:
            case RenderingSessionStatus.Unknown:
                CurrentCoordinatorState = RemoteRenderingState.NoSession;
                break;
            case RenderingSessionStatus.Starting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
                break;
            case RenderingSessionStatus.Ready:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }

    private void OnLocalRuntimeStatusChanged(ConnectionStatus status, Result error)
    {
        switch (status)
        {
            case ConnectionStatus.Connected:
                CurrentCoordinatorState = RemoteRenderingState.RuntimeConnected;
                break;
            case ConnectionStatus.Connecting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
                break;
            case ConnectionStatus.Disconnected:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }
}
```

## <a name="create-the-azure-remote-rendering-gameobject"></a>Het GameObject voor Azure Remote Rendering maken

De remote rendering-coördinator en het vereiste script (*ARRServiceUnity*) zijn beide MonoBehaviours die gekoppeld moeten worden aan een GameObject in de scène. Het *ARRServiceUnity*-script wordt door ARR verschaft om een groot deel van de ARR-functionaliteit beschikbaar te maken om verbinding te maken met externe sessies en ze te beheren.

1. Maak een nieuw GameObject in de scène (Ctrl+Shift+N of *GameObject->Lege maken*) en geef het de naam **RemoteRenderingCoordinator**.
1. Voeg het *RemoteRenderingCoordinator*-script toe aan het GameObject **RemoteRenderingCoordinator**.\
![RemoteRenderingCoordinator-component toevoegen](./media/add-coordinator-script.png)
1. Controleer of het *ARRServiceUnity*-script, dat wordt weergegeven als *Service* in de Inspector, automatisch wordt toegevoegd aan het GameObject. Ter informatie, dit is een resultaat dat `[RequireComponent(typeof(ARRServiceUnity))]` heeft bovenaan het **RemoteRenderingCoordinator**-script.
1. Voeg uw Azure Remote Rendering-referenties, uw accountverificatiedomein en het accountdomein toe aan het coördinatorscript:\
![Uw referenties toevoegen](./media/configure-coordinator-script.png)

## <a name="initialize-azure-remote-rendering"></a>Azure Remote Rendering initialiseren

Nu we het framework voor onze coördinator hebben, gaan we elk van de vier fasen implementeren, te beginnen met **Remote Rendering initialiseren**.

![ARR-stack 1](./media/remote-render-stack-1.png)

**Initialiseren** vertelt Azure Remote Rendering welk camera-object moet worden gebruikt voor rendering en werkt de statusmachine bij naar **NotAuthorized**. Dit betekent dat het is geïnitialiseerd, maar nog geen toestemming heeft om verbinding te maken met een sessie. Aangezien een ARR-sessie beginnen kosten met zich meebrengt, moeten we bevestigen dat de gebruiker wilt doorgaan.

Wanneer de status **NotAuthorized** van toepassing is, wordt **CheckAuthorization** aangeroepen, wat vervolgens de gebeurtenis **RequestingAuthorization** aanroept en bepaalt welke accountreferenties gebruikt moeten worden (**AccountInfo** wordt opgegeven bovenaan de klasse en gebruikt de referenties die u hebt opgegeven in de Unity Inspector in de bovenstaande stap).

   > [!NOTE]
   > Hercompilatie van runtime wordt niet ondersteund door ARR. Als u het script wijzigt en opslaat terwijl de afspeelmodus actief is, kan dit ertoe leiden dat Unity vastloopt en u dit moet afsluiten in Taakbeheer. Zorg er altijd voor dat u de afspeelmodus hebt gestopt voor u uw scripts bewerkt.

1. Vervang de inhoud van **InitializeARR** en **InitializeSessionService** door de voltooide code hieronder:

 ```cs
/// <summary>
/// Initializes ARR, associating the main camera
/// Note: This must be called on the main Unity thread
/// </summary>
public void InitializeARR()
{
    RemoteManagerUnity.InitializeManager(new RemoteUnityClientInit(Camera.main));

    CurrentCoordinatorState = RemoteRenderingState.NotAuthorized;
}

/// <summary>
/// Create a new remote session manager
/// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
/// </summary>
public async void InitializeSessionService()
{
    if (ARRCredentialGetter == null)
        ARRCredentialGetter = GetDevelopmentCredentials;

    var accountInfo = await ARRCredentialGetter.Invoke();

    ARRSessionService.OnSessionStatusChanged += OnRemoteSessionStatusChanged;

    ARRSessionService.Initialize(accountInfo);

    CurrentCoordinatorState = RemoteRenderingState.NoSession;
}
```

Om verder te gaan van **NotAuthorized** naar **NoSession**, presenteren we meestal een modaal dialoogvenster aan de gebruiker zodat deze kan kiezen (dat laten we zien in een ander hoofdstuk). Hier gaan we automatisch de autorisatiecontrole overslaan door **ByPassAuthentication** aan te roepen zodra de **RequestingAuthorization**-gebeurtenis geactiveerd wordt.

1. Selecteer het GameObject **RemoteRenderingCoordinator** en zoek de Unity-gebeurtenis **OnRequestingAuthorization** in de Inspector van de component **RemoteRenderingCoordinator**.

1. Voeg een nieuwe gebeurtenis toe door op de '+' te drukken links onderaan.
1. Sleep de component naar de eigen gebeurtenis om naar zichzelf te verwijzen.\
![Verificatie overslaan](./media/bypass-authorization-add-event.png)\
1. Selecteer in de vervolgkeuzelijst **RemoteRenderingCoordinator-> BypassAuthorization**.\
![Schermopname met de geselecteerde optie RemoteRenderingCoordinator.BypassAuthorization.](./media/bypass-authorization-event.png)

## <a name="create-or-join-a-remote-session"></a>Een externe sessie maken of eraan deelnemen

De tweede fase is om een Remote Rendering-sessie te maken of eraan deel te nemen (zie [Remote Rendering-sessies](../../../concepts/sessions.md) voor meer informatie).

![ARR-stack 2](./media/remote-render-stack-2.png)

De externe sessie is de plaats waar de modellen worden gegenereerd. De methode **JoinRemoteSession ()** probeert deel te nemen aan een bestaande sessie, opgevolgd door de eigenschap **LastUsedSessionID** of als er een toegewezen actieve sessie-ID is ingesteld op **SessionIDOverride**. **SessionIDOverride** dient enkel voor foutopsporing, en mag enkel gebruikt worden wanneer u weet dat de sessie bestaat en u er expliciet verbinding mee wilt maken.

Als er geen sessies beschikbaar zijn, wordt er een nieuwe sessie gemaakt. Een nieuwe sessie maken is echter een tijdrovende bewerking. Probeer daarom enkel sessies te maken wanneer dat vereist is, en hergebruik ze indien mogelijk (zie [Commercial Ready: Sessiepooling, planning en aanbevolen procedures](../commercial-ready/commercial-ready.md#fast-startup-time-strategies) voor meer informatie over het beheren van sessies).

> [!TIP]
> **StopRemoteSession()** maakt een einde aan de actieve sessie. Als u onnodige kosten wilt voorkomen, moet u sessies altijd stopzetten wanneer ze niet meer nodig zijn.

De toestandsmachine gaat nu verder naar **ConnectingToNewRemoteSession** of **ConnectingToExistingRemoteSession**, afhankelijk van de beschikbare sessies. Zowel een bestaande sessie openen als een nieuwe sessie maken, activeert de gebeurtenis **ARRSessionService.OnSessionStatusChanged** waardoor onze methode **OnremoteSessionStatusChanged** wordt uitgevoerd. Idealiter gaat de toestandsmachine verder naar **RemoteSessionReady**.

1. Als u wilt deelnemen aan een nieuwe sessie, wijzig dan de code om de methodes **JoinRemoteSession( )** en **StopRemoteSession( )** te vervangen door de voltooide voorbeelden:

```cs
/// <summary>
/// Attempts to join an existing session or start a new session
/// </summary>
public async void JoinRemoteSession()
{
    //If there's a session available that previously belonged to us, and it's ready, use it. Otherwise start a new session.
    RenderingSessionProperties joinResult;
    if (await IsSessionAvailable(LastUsedSessionID))
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToExistingRemoteSession;
        joinResult = await ARRSessionService.OpenSession(LastUsedSessionID);
    }
    else
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
        joinResult = await ARRSessionService.StartSession(new RenderingSessionCreationOptions(renderingSessionVmSize, (int)maxLeaseHours, (int)maxLeaseMinutes));
    }

    if (joinResult.Status == RenderingSessionStatus.Ready || joinResult.Status == RenderingSessionStatus.Starting)
    {
        LastUsedSessionID = joinResult.Id;
    }
    else
    {
        //The session should be ready or starting, if it's not, something went wrong
        await ARRSessionService.StopSession();
        if(LastUsedSessionID == SessionIDOverride)
            SessionIDOverride = "";
        CurrentCoordinatorState = RemoteRenderingState.NoSession;
    }
}

public void StopRemoteSession()
{
    if (ARRSessionService.CurrentActiveSession != null)
    {
        ARRSessionService.CurrentActiveSession.StopAsync();
    }
}
```

Als u tijd wilt besparen door sessies opnieuw te gebruiken, deactiveer dan de optie **Sessie automatisch stoppen** in de component *ARRServiceUnity*. Houd er rekening mee dat er op deze manier sessies actief blijven, zelfs wanneer niemand ermee verbonden is. Uw sessie kan actief blijven zolang als uw *MaxLeaseTime* voordat deze wordt afgesloten door de server (de waarde voor *MaxLeaseTime* kan worden gewijzigd in de Remote Rendering Coordinator, onder *Standaardwaarden voor nieuwe sessie*). Als u daarentegen elke sessie automatisch afsluit wanneer u de verbinding verbreekt, dan moet u elke keer wachten tot een nieuwe sessie is opgestart, wat behoorlijk lang kan duren.

> [!NOTE]
> Het stoppen van een sessie heeft direct effect en kan niet ongedaan worden gemaakt. Nadat de functie is gestopt, moet u een nieuwe sessie maken, met dezelfde opstart-overhead.

## <a name="connect-the-local-runtime-to-the-remote-session"></a>De lokale runtime verbinden met de externe sessie

Vervolgens moet de toepassing de lokale runtime verbinden met de externe sessie.

![ARR-stack 3](./media/remote-render-stack-3.png)

De toepassing moet ook luisteren naar gebeurtenissen over de verbinding tussen de runtime en de huidige sessie. Deze statuswijzigingen worden verwerkt in **OnLocalRuntimeStatusChanged**. Deze code werkt onze status bij naar **ConnectingToRuntime**. Zodra u verbonden bent in **OnLocalRuntimeStatusChanged** wordt de status bijgewerkt naar **RuntimeConnected**. Verbinding maken met de runtime is de laatste status die de coördinator voor zijn rekening neemt, wat betekent dat de toepassing klaar is met alle algemene configuraties en kan beginnen met het sessiespecifieke werk van het laden en renderen van modellen.

 1. Vervang de methodes **ConnectRuntimeToRemoteSession( )** en **DisconnectRuntimeFromRemoteSession( )** door de voltooide versies hieronder.
 1. Het is belangrijk dat u rekening houdt met de Unity-methode **LateUpdate** en dat de huidige actieve sessie wordt bijgewerkt. Hierdoor kan de huidige sessie berichten verzenden/ontvangen en de framebuffer bijwerken met de frames die ontvangen worden via de externe sessie. Dat is essentieel voor de goede werking van ARR.

```cs
/// <summary>
/// Connects the local runtime to the current active session, if there's a session available
/// </summary>
public void ConnectRuntimeToRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null)
    {
        Debug.LogError("Not ready to connect runtime");
        return;
    }

    //Connect the local runtime to the currently connected session
    //This session is set when connecting to a new or existing session

    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged += OnLocalRuntimeStatusChanged;
    ARRSessionService.CurrentActiveSession.ConnectAsync(new RendererInitOptions());
    CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
}

public void DisconnectRuntimeFromRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null || ARRSessionService.CurrentActiveSession.ConnectionStatus != ConnectionStatus.Connected)
    {
        Debug.LogError("Runtime not connected!");
        return;
    }

    ARRSessionService.CurrentActiveSession.Disconnect();
    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged -= OnLocalRuntimeStatusChanged;
    CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
}

/// <summary>
/// The session must have its runtime pump updated.
/// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
/// </summary>
private void LateUpdate()
{
    ARRSessionService?.CurrentActiveSession?.Connection?.Update();
}
```

> [!NOTE]
> De lokale runtime verbinden met een externe sessie is afhankelijk van het aanroepen van **Bijwerken** in de huidige actieve sessie. Als u merkt dat uw toepassing nooit verder raakt dan de status **ConnectingToRuntime**, zorg er dan voor dat u **Bijwerken** regelmatig aanroept in de actieve sessie.

## <a name="load-a-model"></a>Een model laden

Nu de nodige basis geïmplementeerd is, kunt u een model laden in de externe sessie en frames beginnen ontvangen.

![Diagram waarin de processtroom wordt weergegeven voor het voorbereiden van het laden en het weergeven van een model.](./media/remote-render-stack-4.png)

De methode **LoadModel** is ontworpen om een modelpad, voortgangshandler en bovenliggende transformatie te accepteren. Deze argumenten worden gebruikt om een model te laden in de externe sessie, de gebruiker te informeren over de laadvoortgang en het extern gegenereerde model in te richten op basis van de bovenliggende transformatie.

1. Vervang de methode **LoadModel** volledig door de onderstaande code:

    ```cs
    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelName">The model's path</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Connection.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelFromSasOptions(modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Connection.LoadModelFromSasAsync(loadModelParams, progress);
        var result = await loadModelAsync;
        return modelEntity;
    }
    ```

De bovenstaande code voert de volgende stappen uit:

1. Maak een [Externe entiteit](../../../concepts/entities.md).
1. Maak een lokaal GameObject om de externe entiteit te vertegenwoordigen.
1. Configureer het lokale GameObject om de status (bv. Transformeren) te synchroniseren met de externe entiteit voor elk frame.
1. Stel een naam in en voeg een [**WorldAnchor**](https://docs.unity3d.com/550/Documentation/ScriptReference/VR.WSA.WorldAnchor.html) toe om stabilisatie te bevorderen.
1. Laad modelgegevens van Blob Storage in de externe entiteit.
1. Retourneer de bovenliggende Entiteit, voor later.

## <a name="view-the-test-model"></a>Het testmodel weergeven

We hebben nu alle code die vereist is om een extern gegenereerd model weer te geven. alle vier de vereiste fasen voor externe rendering zijn voltooid. Nu moeten we een beetje code toevoegen om het model te beginnen laden.

![ARR-stack 4](./media/remote-render-stack-5.png)

1. Voeg de volgende code toe aan de **RemoteRenderingCoordinator**-klasse, net onder de **LoadModel**-methode bijvoorbeeld:

    ```cs
    private bool loadingTestModel = false;
    [ContextMenu("Load Test Model")]
    public async void LoadTestModel()
    {
        if(CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if(loadingTestModel)
        {
            Debug.Log("Test model already loading or loaded!");
            return;
        }
        loadingTestModel = true;
    
        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("TestModelParent");
        testParent.transform.position = new Vector3(0f, 0f, 3f);
    
        // The 'built in engine path' is a special path that references a test model built into Azure Remote Rendering.
        await LoadModel("builtin://Engine", testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```
    
    Deze code maakt een GameObject dat dient als de bovenliggende entiteit voor het testmodel. Vervolgens roept het de methode **LoadModel** aan om het model 'builtin://Engine' te laden. Deze asset is ingebouwd in Azure Remote Rendering om de rendering te testen.

1. Sla uw code op.
1. Druk op de Afspeelknop in de Unity Editor om verbinding te maken met Azure Remote Rendering en een nieuwe sessie te maken.
1. Er is niet veel te zien in de Gameweergave, maar de Console laat zien hoe de status van de toepassing verandert. Deze wordt wellicht bijgewerkt naar `ConnectingToNewRemoteSession` en blijft daar tot vijf minuten op staan.
1. Selecteer het GameObject **RemoteRenderingCoordinator** om de bijgevoegde scripts te bekijken in de inspector. Kijk hoe de component **Service** wordt bijgewerkt terwijl deze de stappen voor initialisatie en verbinding doorloopt.
1. Controleer de uitvoer van de Console, en wacht tot de status verandert naar **RuntimeConnected**.
1. Zodra de runtime verbonden is, klikt u met de rechtermuisknop op de **RemoteRenderingCoordinator** in de inspector om het contextmenu weer te geven. Klik vervolgens op de optie **Testmodel laden** in het contextmenu, dat is toegevoegd door het deel `[ContextMenu("Load Test Model")]` van onze bovenstaande code.

    ![Laden vanuit contextmenu](./media/load-test-model.png)

1. Controleer de Console op uitvoer van de **ProgressHandler** die we in de methode **Loadmodel** hebben ingevoerd.
1. U ziet het extern gegenereerde model!

> [!NOTE]
> Dit extern model zal nooit zichtbaar zijn in de Scèneweergave, enkel in de gameweergave. Dit komt doordat ARR de frames extern genereert, specifiek voor het perspectief van de camera voor Gameweergave. Het is zich niet bewust van de Editor-camera (die gebruikt wordt om de Scèneweergave te genereren).

## <a name="next-steps"></a>Volgende stappen

![Model geladen](./media/test-model-rendered.png)

Gefeliciteerd! U hebt een basistoepassing gemaakt waarmee extern gegenereerde modellen kunnen worden bekeken met behulp van Azure Remote Rendering. In de volgende zelfstudie integreren we MRTK en importeren we onze eigen modellen.

> [!div class="nextstepaction"]
> [Volgende: Interfaces en aangepaste modellen](../custom-models/custom-models.md).
