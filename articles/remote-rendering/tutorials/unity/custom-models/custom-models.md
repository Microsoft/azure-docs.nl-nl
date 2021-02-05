---
title: Interfaces en aangepaste modellen
description: Besturingselementen voor weergave toevoegen en aangepaste modellen opnemen om te worden omgezet door Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a278b6e725488d6107e6b0819e002d1dafe4774
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591660"
---
# <a name="tutorial-interfaces-and-custom-models"></a>Zelfstudie: Interfaces en aangepaste modellen

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Mixed Reality Toolkit toevoegen aan het project
> * Status van model beheren
> * Azure Blob-opslag configureren voor modelopname
> * Modellen uploaden en bewerken voor weergave

## <a name="prerequisites"></a>Vereisten

* Deze zelfstudie gaat verder op de [-zelfstudie: U bekijkt een model dat extern wordt weergegeven](../view-remote-models/view-remote-models.md).

## <a name="get-started-with-the-mixed-reality-toolkit-mrtk"></a>Aan de slag met Mixed Reality Toolkit (MRTK)

MRTK is een platformoverschrijdende toolkit voor het bouwen van Mixed Reality-ervaringen. We gebruiken MRTK 2.5.1 voor de interactie- en visualisatiefuncties.

Volg de [Vereiste stappen](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#required) die worden vermeld in de [MRTK-installatiehandleiding](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html) om MRTK toe te voegen.

Die stappen zijn:
 - [De nieuwste MRTK Unity-pakketten ophalen](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#1-get-the-latest-mrtk-unity-packages)
     - Hoewel er 'meest recent' staat, moet u versie 2.5.1 gebruiken van de pagina met MRTK-releases.
     - In deze zelfstudie gebruiken we alleen het *Basis* pakket. De pakketten *Uitbreidingen*, *Hulpmiddelen* en *Voorbeelden* zijn niet vereist.
 - [MRTK-pakketten importeren in uw Unity-project](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#2-import-mrtk-packages-into-your-unity-project)
 - [Uw Unity-project overschakelen naar het doelplatform](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#3-switch-your-unity-project-to-the-target-platform)
     - U hebt deze stap in het eerste hoofdstuk al uitgevoerd, maar dit is het moment om dat nog eens te controleren.
 - [MRTK toevoegen aan een nieuwe scène of nieuw project](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#4-add-and-configure-mrtk-with-a-new-scene)
     - U kunt MRTK toevoegen aan een nieuwe scène en uw coördinator en modelobjecten/-scripts opnieuw toevoegen of u kunt MRTK toevoegen aan uw bestaande scène met de menuopdracht *Mixed Reality Toolkit -> Aan scène toevoegen en configureren*.

## <a name="import-assets-used-by-this-tutorial"></a>Assets importeren voor deze zelfstudie

In dit hoofdstuk beginnen we met het implementeren van een eenvoudig [patroon voor besturingselementen voor weergave](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) voor veel van het materiaal dat wordt behandeld. Het *model* deel van het patroon is de specifieke code van Azure Remote Rendering en het statusbeheer voor Azure Remote Rendering. De onderdelen *weergave* en *besturingselementen* van het patroon worden geïmplementeerd met MRTK-assets en een aantal aangepaste scripts. Het is mogelijk het *model* te gebruiken in deze zelfstudie zonder implementatie van *weergavebesturingselementen*. Door deze scheiding kunt u eenvoudig de code uit deze zelfstudie integreren in uw eigen toepassing waar het het deel *weergavebesturingselementen* van het ontwerppatroon overneemt.

Door de introductie van MRTK zijn er een aantal scripts, prefabs en assets die nu kunnen worden toegevoegd aan het project om interacties en visuele feedback te ondersteunen. Deze assets, **Zelfstudie-assets** genoemd, zijn gebundeld in een [Unity Asset Package](https://docs.unity3d.com/Manual/AssetPackages.html), dat is inbegrepen in de [Azure Remote Rendering GitHub](https://github.com/Azure/azure-remote-rendering) in '\Unity\TutorialAssets\TutorialAssets.unitypackage'.

1. Kloon of download de git-opslagplaats [Azure Remote Rendering](https://github.com/Azure/azure-remote-rendering). Als u het downloadt, moet u het zip-bestand uitpakken in een bekende locatie.
1. Kies in uw Unity-project *Assets -> Pakket importeren -> Aangepast pakket*.
1. Navigeer in de bestandsverkenner naar de map waar u de Azure Remote Rendering-opslagplaats hebt gekloond of uitgepakt, selecteer dan het .unitypackage dat u kunt vinden in **Unity -> Zelfstudie-assets -> TutorialAssets.unitypackage**
1. Selecteer de knop **Importeren** om de inhoud van het pakket te importeren in uw project.
1. Selecteer in de bovenste menubalk in de Unity Editor *Mixed Reality Toolkit -> Hulpprogramma's -> MRTK Standard Shader for Lightweight Render Pipeline bijwerken* en volg de instructies om de shader bij te werken.

Zodra MRTK en de Zelfstudie-assets in het project zijn opgenomen, schakelen we over naar een MRTK-profiel dat beter geschikt is voor de zelfstudie.

1. Selecteer in de scène-hiërarchie **MixedRealityToolkit** GameObject.
1. Schakel in de Inspector, onder het onderdeel **MixedRealityToolkit** het configuratieprofiel over op *ARRMixedRealityToolkitConfigurationProfile*.
1. Druk op *Ctrl+S* om uw wijziging op te slaan.

Hiermee wordt MRTK voornamelijk geconfigureerd met de HoloLens 2-standaardprofielen. De aangeboden profielen zijn vooraf geconfigureerd op de volgende manieren:
 - Schakel de Profiler uit (druk op 9 om het in of uit te schakelen of zeg "Profiler weergeven/verbergen" op het apparaat).
 - Schakel de cursor oogtracering uit.
 - Schakel Unity-muisklikken in, zodat u op MRTK-gebruikersinterface-elementen kunt klikken met de muis in plaats van met de gesimuleerde hand.

## <a name="add-the-app-menu"></a>Het app-menu toevoegen

De meeste besturingselementen voor weergave in deze zelfstudie kunnen worden gebruikt voor abstracte basisklassen in plaats van concrete klassen. Dit patroon biedt meer flexibiliteit en hiermee kunnen we u de weergavebesturingselementen bieden, terwijl u nog steeds de Azure Remote Rendering-code leert kennen. Ter vereenvoudiging heeft de klasse **RemoteRenderingCoordinator** niet een abstracte klasse en kan het weergavebesturingselement rechtstreeks in de concrete klasse worden gebruikt.

U kunt nu de prefab **AppMenu** toevoegen aan de scène voor visuele feedback van de huidige sessiestatus. Dit weergavebesturingselement 'ontgrendelt' meer submenuweergavebesturingselementen wanneer we meer ARR-functies implementeren en integreren in de scène. Het **AppMenu** heeft een visuele indicatie van de ARR-status en toont het modale paneel dat de gebruiker gebruikt om de toepassing te autoriseren voor verbinding met ARR.

1. De prefab **AppMenu** zoeken in *Assets/RemoteRenderingTutorial/Prefabs/AppMenu*
1. Sleep de prefab **AppMenu** in de scène.
1. U ziet een dialoogvenster voor **TMP Importer**, aangezien dit de eerste keer is dat we *Text Mesh Pro*-assets in de scène opnemen. Volg de instructies voor **TMP Essentials importeren**. Sluit vervolgens het dialoogvenster voor importeren, de voorbeelden en extra's zijn niet nodig.
1. Het **AppMenu** is geconfigureerd om automatisch een verbinding te maken en de modaal te bieden voor verbinding met een sessie, zodat we de omleiding die eerder is ingesteld, kunnen verwijderen. Verwijder op het GameObject **RemoteRenderingCoordinator** de omleiding voor autorisatie die we eerder hebben geïmplementeerd door op de knop '-' te drukken op de gebeurtenis **Bij autorisatieaanvraag**.
 ![Verwijder de omleiding](./media/remove-bypass-event.png).
1. Test het weergavebesturingselement door op **Afspelen** te drukken in de Unity Editor.
1. U kunt, nu MRTK is geconfigureerd, in de Editor de WASD-sleutels gebruiken om de positie van uw weergave te wijzigen en de rechtermuisknop vasthouden en de muis verplaatsen om uw weergaverichting te wijzigen. Probeer door de scène te bewegen om te zien hoe de besturingselementen werken.
1. Op het apparaat kunt u uw palm heffen om het **AppMenu** op te roepen, in de Unity Editor gebruikt u de sneltoets 'M'.
1. Als u het menu niet meer kunt vinden, drukt u op de toets 'M' om het menu op te roepen. Het menu wordt geplaatst bij de camera voor eenvoudige interactie.
1. De autorisatie wordt nu weergegeven als een aanvraag aan de rechterkant van het **AppMenu**. Vanaf nu gebruikt u dit om de app te autoriseren om remote rendering-sessies te beheren.
 ![Gebruikersinterface goedkeuren](./media/authorize-request-ui.png)
1. Stop het afspelen van Unity om door te gaan met de zelfstudie.

## <a name="manage-model-state"></a>Status van model beheren

Nu implementeren we een nieuw script **RemoteRenderedModel** voor traceerstatus, reageren op gebeurtenissen, het starten van gebeurtenissen en configuratie. **RemoteRenderedModel** slaat het externe pad op voor de modelgegevens in `modelPath`. Het registreert statuswijzigingen in de **RemoteRenderingCoordinator** om te zien of het model dat wordt gedefinieerd automatisch moet worden geladen of verwijderd. Het GameObject waaraan de **RemoteRenderedModel** is gekoppeld, wordt het lokale bovenliggende element voor de externe inhoud.

U ziet dat het script **RemoteRenderedModel**, **BaseRemoteRenderedModel** uit **Zelfstudie-assets** implementeert. Hiermee kan het weergavebesturingselement voor het extern model worden verbonden met uw script.

1. Maak een nieuw script met de naam **RemoteRenderedModel** in dezelfde map als **RemoteRenderingCoordinator**. Vervang de volledige inhoud door de volgende code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;
    using UnityEngine.Events;

    public class RemoteRenderedModel : BaseRemoteRenderedModel
    {
        public bool AutomaticallyLoad = true;

        private ModelState currentModelState = ModelState.NotReady;

        [SerializeField]
        [Tooltip("The friendly name for this model")]
        private string modelDisplayName;
        public override string ModelDisplayName { get => modelDisplayName; set => modelDisplayName = value; }

        [SerializeField]
        [Tooltip("The URI for this model")]
        private string modelPath;
        public override string ModelPath
        {
            get => modelPath.Trim();
            set => modelPath = value;
        }

        public override ModelState CurrentModelState
        {
            get => currentModelState;
            protected set
            {
                if (currentModelState != value)
                {
                    currentModelState = value;
                    ModelStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<ModelState> ModelStateChange;
        public override event Action<float> LoadProgress;
        public override Entity ModelEntity { get; protected set; }

        public UnityEvent OnModelNotReady = new UnityEvent();
        public UnityEvent OnModelReady = new UnityEvent();
        public UnityEvent OnStartLoading = new UnityEvent();
        public UnityEvent OnModelLoaded = new UnityEvent();
        public UnityEvent OnModelUnloading = new UnityEvent();

        public UnityFloatEvent OnLoadProgress = new UnityFloatEvent();

        public void Awake()
        {
            // Hook up the event to the Unity event
            LoadProgress += (progress) => OnLoadProgress?.Invoke(progress);

            ModelStateChange += HandleUnityStateEvents;
        }

        private void HandleUnityStateEvents(ModelState modelState)
        {
            switch (modelState)
            {
                case ModelState.NotReady:  OnModelNotReady?.Invoke();  break;
                case ModelState.Ready:     OnModelReady?.Invoke();     break;
                case ModelState.Loading:   OnStartLoading?.Invoke();   break;
                case ModelState.Loaded:    OnModelLoaded?.Invoke();    break;
                case ModelState.Unloading: OnModelUnloading?.Invoke(); break;
            }
        }

        private void Start()
        {
            //Attach to and initialize current state (in case we're attaching late)
            RemoteRenderingCoordinator.CoordinatorStateChange += Instance_CoordinatorStateChange;
            Instance_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        /// <summary>
        /// Listen for state changes on the coordinator, clean up this model's remote objects if we're no longer connected.
        /// Automatically load if required
        /// </summary>
        private void Instance_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CurrentModelState = ModelState.Ready;
                    if (AutomaticallyLoad)
                        LoadModel();
                    break;
                default:
                    UnloadModel();
                    break;
            }
        }

        private void OnDestroy()
        {
            RemoteRenderingCoordinator.CoordinatorStateChange -= Instance_CoordinatorStateChange;
            UnloadModel();
        }

        /// <summary>
        /// Asks the coordinator to create a model entity and listens for coordinator state changes
        /// </summary>
        [ContextMenu("Load Model")]
        public override async void LoadModel()
        {
            if (CurrentModelState != ModelState.Ready)
                return; //We're already loaded, currently loading, or not ready to load

            CurrentModelState = ModelState.Loading;

            ModelEntity = await RemoteRenderingCoordinator.instance?.LoadModel(ModelPath, this.transform, SetLoadingProgress);

            if (ModelEntity != null)
                CurrentModelState = ModelState.Loaded;
            else
                CurrentModelState = ModelState.Error;
        }

        /// <summary>
        /// Clean up the local model instances
        /// </summary>
        [ContextMenu("Unload Model")]
        public override void UnloadModel()
        {
            CurrentModelState = ModelState.Unloading;

            if (ModelEntity != null)
            {
                var modelGameObject = ModelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
                Destroy(modelGameObject);
                ModelEntity.Destroy();
                ModelEntity = null;
            }

            if (RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
                CurrentModelState = ModelState.Ready;
            else
                CurrentModelState = ModelState.NotReady;
        }

        /// <summary>
        /// Update the Unity progress event
        /// </summary>
        /// <param name="progressValue"></param>
        public override void SetLoadingProgress(float progressValue)
        {
            LoadProgress?.Invoke(progressValue);
        }
    }
    ```

Eenvoudig gezegd, bevat **RemoteRenderedModel** de gegevens die nodig zijn om een model te laden (in dit geval de SAS of *builtin://* URI) en wordt de status van het externe model bijgehouden. Wanneer het tijd is om te laden, worden de `LoadModel`-methode die wordt aangeroepen op **RemoteRenderingCoordinator** en de entiteit met het model geretourneerd voor referentie en verwijdering.

## <a name="load-the-test-model"></a>Het testmodel laden

We gaan het nieuwe script testen door het testmodel opnieuw te laden. We maken een GameObject dat het script bevat en een bovenliggend element van het testmodel is.

1. Maak een nieuw, leeg GameObject in de scène en geef die de naam **TestModel**.
1. Voeg het script *RemoteRenderedModel* toe aan **TestModel**.
![Component RemoteRenderedModel toevoegen](./media/add-remote-rendered-model-script.png)
1. Voer bij `Model Display Name` en `Model Path` respectievelijk '*TestModel*' en '*builtin://Engine*' in.
![Modelgegevens opgeven](./media/add-model-script.png)
1. Plaats het object **TestModel** voor de camera op positie **x = 0, y = 0, z = 3**.
![Object plaatsen](./media/test-model-position.png)
1. Zorg ervoor dat **AutomaticallyLoad** is ingeschakeld.
1. Druk op **Afspelen** in de Unity Editor om de toepassing te testen.
1. Geef autorisatie door te klikken op de knop *Verbinding maken*, zodat de app een sessie kan maken en er wordt verbinding met een sessie gemaakt en het model wordt automatisch geladen.

Bekijk de console terwijl de toepassing de statussen doorloopt. Houd er rekening mee dat bepaalde statussen enige tijd in beslag kunnen nemen en geen voortgang weergeven. Uiteindelijk ziet u de logboeken van het laden van het model en vervolgens wordt het testmodel in de scène weergegeven.

Probeer het GameObject **TestModel** te bewegen en te roteren via de optie Transformeren in de Inspector of in de scèneweergave. U ziet het model bewegen en roteren in de Game-weergave.

![Unity-logboek](./media/unity-loading-log.png)

## <a name="provision-blob-storage-in-azure-and-custom-model-ingestion"></a>Blob-opslag inrichten in Azure en aangepaste modelopname

Nu kunnen we proberen uw eigen model te laden. Hiervoor moet u Blob-opslag configureren en op Azure een model uploaden en converteren. Daarna laden we het model met het script **RemoteRenderedModel**. De aangepaste stappen voor het laden van een model kunnen veilig worden overgeslagen als u op dit moment geen eigen model hebt om te laden.

Volg de stappen die zijn opgegeven in de [Snelstartgids: Een model converteren voor rendering](../../../quickstarts/convert-model.md). Sla de sectie **Een nieuw model invoegen in Voorbeeldappsnelstartgids**  voor deze zelfstudie. Zodra u de *Shared Access Signature (SAS)* URI van uw opgenomen model hebt, gaat u door met de volgende stap hieronder.

## <a name="load-and-rendering-a-custom-model"></a>Een aangepast model laden en weergeven

1. Maak een nieuw, leeg GameObject in de scène en geef deze een soortgelijke naam als uw aangepast model.
1. Voeg het script *RemoteRenderedModel* toe aan het nieuw gemaakte GameObject.
 ![Component RemoteRenderedModel toevoegen](./media/add-remote-rendered-model-script.png)
1. Vul in de `Model Display Name` een passende naam in voor uw model.
1. Vul de *Shared Access Signature (SAS)* URI in van het model dat u in voorgaande stappen hebt gemaakt in `Model Path`.
1. Plaats het GameObject voor de camera op positie **x = 0, y = 0, z = 3.**
1. Zorg ervoor dat **AutomaticallyLoad** is ingeschakeld.
1. Druk op **Afspelen** in de Unity Editor om de toepassing te testen.

    U zult zien dat de console wordt gevuld met de huidige status en uiteindelijk de berichten over de voortgang van het laden van het model. Uw aangepaste model wordt dan in de scène geladen.

1. Verwijder uw aangepaste modelobject uit de scène. Deze zelfstudie geeft het beste resultaat als u het testmodel gebruikt. Hoewel meerdere modellen worden ondersteund in ARR is deze zelfstudie geschreven om één extern model tegelijk te ondersteunen.

## <a name="next-steps"></a>Volgende stappen

U kunt nu uw eigen modellen in Azure Remote Rendering laden en ze in uw toepassing weergeven. Hierna begeleiden we u bij het manipuleren van uw modellen.

> [!div class="nextstepaction"]
> [Volgende: Modellen manipuleren](../manipulate-models/manipulate-models.md)
