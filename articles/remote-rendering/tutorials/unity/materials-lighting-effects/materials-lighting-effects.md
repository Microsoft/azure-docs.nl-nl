---
title: Materialen, belichting en effecten verfijnen
description: Materialen en belichting voor het model wijzigen. Extra effecten toevoegen, zoals contouren en knipvlakken.
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 8eb73fcfde7e294896a12289486ff71794a00ae6
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591715"
---
# <a name="tutorial-refining-materials-lighting-and-effects"></a>Zelfstudie: Materialen, belichting en effecten verfijnen

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Modellen en modelonderdelen markeren en omtrekken
> * Verschillende materialen toepassen op modellen
> * Doorsnedes van modellen maken met knipvlakken
> * Eenvoudige animaties voor extern gerenderde objecten toevoegen

## <a name="prerequisites"></a>Vereisten

* Deze zelfstudie gaat verder op de [zelfstudie: Modellen bewerken](..\manipulate-models\manipulate-models.md).

## <a name="highlighting-and-outlining"></a>Markeren en omtrekken

Het bieden van visuele feedback aan de gebruiker is een belangrijk onderdeel van de gebruikerservaring in elke toepassing. Azure Remote Rendering biedt methoden voor visuele feedback via [Hiërarchische statusoverschrijvingen](../../../overview/features/override-hierarchical-state.md). De hiërarchische statusoverschrijvingen worden geïmplementeerd met onderdelen die zijn gekoppeld aan lokale exemplaren van modellen. We hebben geleerd hoe u deze lokale exemplaren maakt in [De externe objectgrafiek synchroniseren in de Unity-hiërarchie](../manipulate-models/manipulate-models.md#synchronizing-the-remote-object-graph-into-the-unity-hierarchy).

Eerst maken we een wrapper om het onderdeel [**HierarchicalStateOverrideComponent**](/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent). **HierarchicalStateOverrideComponent** is het lokale script waarmee de overschrijvingen op de externe entiteit worden beheerd. De [**zelfstudie Assets**](../custom-models/custom-models.md#import-assets-used-by-this-tutorial) bevat een abstracte basisklasse met de naam **BaseEntityOverrideController**, die we uitbreiden om de wrapper te maken.

1. Maak een nieuw script met de naam **EntityOverrideController** en vervang de inhoud door de volgende code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class EntityOverrideController : BaseEntityOverrideController
    {
        public override event Action<HierarchicalStates> FeatureOverrideChange;

        private ARRHierarchicalStateOverrideComponent localOverride;
        public override ARRHierarchicalStateOverrideComponent LocalOverride
        {
            get
            {
                if (localOverride == null)
                {
                    localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();
                    if (localOverride == null)
                    {
                        localOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
                    }

                    var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                    if (remoteStateOverride == null)
                    {
                        // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                        localOverride.Create(RemoteManagerUnity.CurrentSession);
                    }
                    else
                    {
                        // otherwise, bind our local stateOverride component to the remote component
                        localOverride.Bind(remoteStateOverride);

                    }
                }
                return localOverride;
            }
        }

        private RemoteEntitySyncObject targetEntity;
        public override RemoteEntitySyncObject TargetEntity
        {
            get
            {
                if (targetEntity == null)
                    targetEntity = gameObject.GetComponent<RemoteEntitySyncObject>();
                return targetEntity;
            }
        }

        private HierarchicalEnableState ToggleState(HierarchicalStates feature)
        {
            HierarchicalEnableState setToState = HierarchicalEnableState.InheritFromParent;
            switch (LocalOverride.RemoteComponent.GetState(feature))
            {
                case HierarchicalEnableState.ForceOff:
                case HierarchicalEnableState.InheritFromParent:
                    setToState = HierarchicalEnableState.ForceOn;
                    break;
                case HierarchicalEnableState.ForceOn:
                    setToState = HierarchicalEnableState.InheritFromParent;
                    break;
            }

            return SetState(feature, setToState);
        }

        private HierarchicalEnableState SetState(HierarchicalStates feature, HierarchicalEnableState enableState)
        {
            if (GetState(feature) != enableState) //if this is actually different from the current state, act on it
            {
                LocalOverride.RemoteComponent.SetState(feature, enableState);
                FeatureOverrideChange?.Invoke(feature);
            }

            return enableState;
        }

        public override HierarchicalEnableState GetState(HierarchicalStates feature) => LocalOverride.RemoteComponent.GetState(feature);

        public override void ToggleHidden() => ToggleState(HierarchicalStates.Hidden);

        public override void ToggleSelect() => ToggleState(HierarchicalStates.Selected);

        public override void ToggleSeeThrough() => ToggleState(HierarchicalStates.SeeThrough);

        public override void ToggleTint(Color tintColor = default)
        {
            if (tintColor != default) LocalOverride.RemoteComponent.TintColor = tintColor.toRemote();
            ToggleState(HierarchicalStates.UseTintColor);
        }

        public override void ToggleDisabledCollision() => ToggleState(HierarchicalStates.DisableCollision);

        public override void RemoveOverride()
        {
            var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();
            if (remoteStateOverride != null)
            {
                remoteStateOverride.Destroy();
            }

            if (localOverride == null)
                localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();

            if (localOverride != null)
            {
                Destroy(localOverride);
            }
        }
    }
    ```

De hoofdtaak van **LocalOverride** is om een link tussen zichzelf en de bijbehorende `RemoteComponent` te maken. Vervolgens kunnen we met **LocalOverride** statusvlaggen voor het lokale onderdeel instellen, die zijn gebonden aan de externe entiteit. De overschrijvingen en de bijbehorende statuswaarden worden beschreven op de pagina [Hiërarchische statusoverschrijvingen](../../../overview/features/override-hierarchical-state.md). 

Met deze implementatie wordt slechts één status tegelijk gewijzigd. Het is echter ook mogelijk meerdere overschrijvingen voor afzonderlijke entiteiten te combineren en combinaties te maken op verschillende niveaus in de hiërarchie. Als u bijvoorbeeld `Selected` en `SeeThrough` voor één onderdeel combineert, geeft u het een omtrek en maakt u het transparant. Of, als u de overschrijving `Hidden` van de hoofdentiteit instelt op `ForceOn` en de overschrijving `Hidden` van een onderliggende entiteit instelt op `ForceOff`, worden alle items verborgen, behalve de onderliggende entiteit met de overschrijving.

Om statuswaarden toe te passen op entiteiten, kunnen we de eerder gemaakte klasse **RemoteEntityHelper** wijzigen.

1. Wijzig de klasse **RemoteEntityHelper** om de abstracte klasse **BaseRemoteEntityHelper** te implementeren. Dankzij deze wijziging kunnen we een weergavecontroller uit de **zelfstudie Assets** gebruiken. Na de wijziging moet de klasse er als volgt uitzien:

    ```cs
    public class RemoteEntityHelper : BaseRemoteEntityHelper
    ```

2. Overschrijf de abstracte methoden met behulp van de volgende code:

    ```cs
    public override BaseEntityOverrideController EnsureOverrideComponent(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent == null)
            overrideComponent = entityGameObject.AddComponent<EntityOverrideController>();
        return overrideComponent;
    }

    public override HierarchicalEnableState GetState(Entity entity, HierarchicalStates feature)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        return overrideComponent.GetState(feature);
    }

    public override void ToggleHidden(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void ToggleSelect(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSelect();
    }

    public override void ToggleSeeThrough(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSeeThrough();
    }

    public Color TintColor = new Color(0.0f, 1.0f, 0.0f, 0.1f);
    public override void ToggleTint(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleTint(TintColor);
    }

    public override void ToggleDisableCollision(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void RemoveOverrides(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent != null)
        {
            overrideComponent.RemoveOverride();
            Destroy(overrideComponent);
        }
    }
    ```

Deze code zorgt ervoor dat een **EntityOverrideController**-onderdeel wordt toegevoegd aan de doelentiteit en vervolgens een van de wijzigingsmethoden aangeroepen. Desgewenst kunnen, in het GameObject **TestModel**, deze helpermethoden worden aangeroepen door de klasse **RemoteEntityHelper** als een callback toe te voegen aan de gebeurtenis `OnRemoteEntityClicked` in het onderdeel **RemoteRayCastPointerHandler**.

![Aanwijzercallbacks](./media/pointer-event-callbacks.png)

Nu deze scripts aan het model zijn toegevoegd, en nadat u verbinding hebt gemaakt met de runtime, moeten voor de weergavecontroller **AppMenu** extra interfaces zijn ingeschakeld voor interactie met het script **EntityOverrideController**. Bekijk het menu **Model Tools** (Hulpprogramma's voor model) om de ontgrendelde weergavecontrollers weer te geven.

Op dit moment moeten de onderdelen van uw GameObject **TestModel** er ongeveer als volgt uitzien:

![Testmodel met extra scripts](./media/test-model-updated.png)

Hier volgt een voorbeeld van het stapelen van overschrijvingen voor één entiteit. We hebben `Select` en `Tint` gebruikt om de entiteit zowel van een omtrek als kleuren te voorzien:

![Tint van testmodel selecteren](./media/select-tint-test-model.png)

## <a name="cut-planes"></a>Vlakken knippen

De functie [Vlakken knippen](../../../overview/features/cut-planes.md) kan worden toegevoegd aan elke externe entiteit. Meestal maakt u een nieuwe externe entiteit die niet is gekoppeld aan meshgegevens om het knipvlak-onderdeel in te bewaren. De positie en oriëntatie van het knipvlak worden bepaald door de positie en oriëntatie van de externe entiteit waaraan het is gekoppeld.

We maken een script dat automatisch een externe entiteit maakt, een knipvlak-onderdeel toevoegt en de transformatie van een lokaal object synchroniseert met de knipvlak-entiteit. Vervolgens kunnen we de **CutPlaneViewController** gebruiken om het knipvlak in een interface te verpakken zodat we het kunnen bewerken.

1. Maak een nieuw script met de naam **RemoteCutPlane** en vervang de code door de onderstaande code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class RemoteCutPlane : BaseRemoteCutPlane
    {
        public Color SliceColor = new Color(0.5f, 0f, 0f, .5f);
        public float FadeLength = 0.01f;
        public Axis SliceNormal = Axis.Y_Neg;

        public bool AutomaticallyCreate = true;

        private CutPlaneComponent remoteCutPlaneComponent;
        private bool cutPlaneReady = false;

        public override bool CutPlaneReady 
        { 
            get => cutPlaneReady;
            set 
            { 
                cutPlaneReady = value;
                CutPlaneReadyChanged?.Invoke(cutPlaneReady);
            }
        }

        public override event Action<bool> CutPlaneReadyChanged;

        public UnityBoolEvent OnCutPlaneReadyChanged = new UnityBoolEvent();

        public void Start()
        {
            // Hook up the event to the Unity event
            CutPlaneReadyChanged += (ready) => OnCutPlaneReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateCutPlane();
                    break;
                default:
                    DestroyCutPlane();
                    break;
            }
        }

        public override void CreateCutPlane()
        {
            //Implement me
        }

        public override void DestroyCutPlane()
        {
            //Implement me
        }
    }
    ```

    Met deze code wordt de klasse **BaseRemoteCutPlane** uit de **zelfstudie Assets** uitgebreid. Net zoals bij het extern gerenderde model, wordt met dit script een koppeling tot stand gebracht en geluisterd naar wijzigingen in `RemoteRenderingState` vanaf de externe coördinator. Wanneer de coördinator de status `RuntimeConnected` bereikt, wordt automatisch geprobeerd verbinding te maken als dat nodig is. Er is ook een `CutPlaneComponent`-variabele die we gaan bijhouden. Dit is het Azure Remote Rendering-onderdeel dat wordt gesynchroniseerd met het knipvlak in de externe sessie. Laten we eens kijken wat we moeten doen om het knipvlak te maken.

2. Vervang de methode `CreateCutPlane()` door de voltooide versie hieronder:

    ```cs
    public override void CreateCutPlane()
    {
        if (remoteCutPlaneComponent != null)
            return; //Nothing to do!

        //Create a root object for the cut plane
        var cutEntity = RemoteRenderingCoordinator.CurrentSession.Connection.CreateEntity();

        //Bind the remote entity to this game object
        cutEntity.BindToUnityGameObject(this.gameObject);

        //Sync the transform of this object so we can move the cut plane
        var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
        syncComponent.SyncEveryFrame = true;

        //Add a cut plane to the entity
        remoteCutPlaneComponent = RemoteRenderingCoordinator.CurrentSession.Connection.CreateComponent(ObjectType.CutPlaneComponent, cutEntity) as CutPlaneComponent;

        //Configure the cut plane
        remoteCutPlaneComponent.Normal = SliceNormal;
        remoteCutPlaneComponent.FadeColor = SliceColor.toRemote();
        remoteCutPlaneComponent.FadeLength = FadeLength;
        CutPlaneReady = true;
    }
    ```

    Hier maken we een externe entiteit en binden deze aan een lokaal GameObject. We zorgen ervoor dat de transformatie van de externe entiteit wordt gesynchroniseerd met de lokale transformatie door `SyncEveryFrame` in te stellen op `true`. Vervolgens gebruiken we de `CreateComponent`-aanroep om een `CutPlaneComponent` toe te voegen aan het externe object. Ten slotte configureren we het knipvlak met de instellingen die boven aan MonoBehaviour zijn gedefinieerd. Laten we eens kijken hoe we een knipvlak opschonen door de methode `DestroyCutPlane()` te implementeren.

3. Vervang de methode `DestroyCutPlane()` door de voltooide versie hieronder:

    ```cs
    public override void DestroyCutPlane()
    {
        if (remoteCutPlaneComponent == null)
            return; //Nothing to do!

        remoteCutPlaneComponent.Owner.Destroy();
        remoteCutPlaneComponent = null;
        CutPlaneReady = false;
    }
    ```

Omdat het externe object tamelijk eenvoudig is en we alleen de externe zijde opschonen (en het lokale object behouden), is het eenvoudig om `Destroy` op het externe object aan te roepen en onze verwijzing ernaar te wissen.

**AppMenu** bevat een weergavecontroller die automatisch aan uw knipvlak wordt gekoppeld en u in staat stelt ermee te werken. Het is niet verplicht **AppMenu** of een van de weergavecontrollers te gebruiken, maar zorgt wel voor een betere ervaring. Test nu het knipvlak en de bijbehorende weergavecontroller.

1. Maak een nieuwe, lege GameObject in de scène en geef deze de naam **CutPlane** (knipvlak).
1. Voeg het onderdeel **RemoteCutPlane** toe aan **CutPlane** GameObject.

   ![Configuratie van het knipvlakonderdeel](./media/cut-plane-config.png)

1. Druk op Play (Afspelen) in Unity editor om een externe sessie te laden en er verbinding mee te maken.
1. Gebruik de handsimulatie van MRTK om het knipvlak te grijpen en draaien (houdt Ctrl ingedrukt om te draaien) en te bewegen binnen de scène. Zie hoe een doorsnede van **TestModel** wordt gemaakt om interne onderdelen te onthullen.

![Voorbeeld van knipvlak](./media/cut-plane-example-engine.png)

## <a name="configuring-the-remote-lighting"></a>De externe belichting configureren

De externe rendering-sessie ondersteunt een heel scala aan [belichtingsopties](../../../overview/features/lights.md). We maken scripts voor [Sky Texture](../../../overview/features/sky.md) (Patroon van de lucht) en een eenvoudige toewijzing voor twee belichtingstypen in Unity voor gebruik met externe rendering.

### <a name="sky-texture"></a>Sky Texture (Patroon van de lucht)

U kunt kiezen uit een aantal ingebouwde cubemaps wanneer u het patroon van de lucht wilt wijzigen. Deze worden in de sessie geladen en toegepast op het patroon van de lucht. U kunt ook [uw eigen patronen laden](../../../concepts/textures.md) en die gebruiken als licht in de lucht.

We maken een script, **RemoteSky**, dat een lijst bevat met de ingebouwde beschikbare cubemaps in de vorm van laadparameters. Vervolgens kan de gebruiker een van de opties selecteren en laden.

1. Maak een nieuw script met de naam **RemoteSky** en vervang de volledige inhoud door de onderstaande code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using System;
    using System.Collections.Generic;
    using UnityEngine;

    public class RemoteSky : BaseRemoteSky
    {
        public override Dictionary<string, LoadTextureFromSasOptions> AvailableCubemaps => builtInTextures;

        private bool canSetSky;
        public override bool CanSetSky
        {
            get => canSetSky;
            set
            {
                canSetSky = value;
                CanSetSkyChanged?.Invoke(canSetSky);
            }
        }

        private string currentSky = "DefaultSky";
        public override string CurrentSky
        {
            get => currentSky;
            protected set
            {
                currentSky = value;
                SkyChanged?.Invoke(value);
            }
        }

        private Dictionary<string, LoadTextureFromSasOptions> builtInTextures = new Dictionary<string, LoadTextureFromSasOptions>()
        {
            {"Autoshop",new LoadTextureFromSasOptions("builtin://Autoshop", TextureType.CubeMap)},
            {"BoilerRoom",new LoadTextureFromSasOptions("builtin://BoilerRoom", TextureType.CubeMap)},
            {"ColorfulStudio",new LoadTextureFromSasOptions("builtin://ColorfulStudio", TextureType.CubeMap)},
            {"Hangar",new LoadTextureFromSasOptions("builtin://Hangar", TextureType.CubeMap)},
            {"IndustrialPipeAndValve",new LoadTextureFromSasOptions("builtin://IndustrialPipeAndValve", TextureType.CubeMap)},
            {"Lebombo",new LoadTextureFromSasOptions("builtin://Lebombo", TextureType.CubeMap)},
            {"SataraNight",new LoadTextureFromSasOptions("builtin://SataraNight", TextureType.CubeMap)},
            {"SunnyVondelpark",new LoadTextureFromSasOptions("builtin://SunnyVondelpark", TextureType.CubeMap)},
            {"Syferfontein",new LoadTextureFromSasOptions("builtin://Syferfontein", TextureType.CubeMap)},
            {"TearsOfSteelBridge",new LoadTextureFromSasOptions("builtin://TearsOfSteelBridge", TextureType.CubeMap)},
            {"VeniceSunset",new LoadTextureFromSasOptions("builtin://VeniceSunset", TextureType.CubeMap)},
            {"WhippleCreekRegionalPark",new LoadTextureFromSasOptions("builtin://WhippleCreekRegionalPark", TextureType.CubeMap)},
            {"WinterRiver",new LoadTextureFromSasOptions("builtin://WinterRiver", TextureType.CubeMap)},
            {"DefaultSky",new LoadTextureFromSasOptions("builtin://DefaultSky", TextureType.CubeMap)}
        };

        public UnityBoolEvent OnCanSetSkyChanged;
        public override event Action<bool> CanSetSkyChanged;

        public UnityStringEvent OnSkyChanged;
        public override event Action<string> SkyChanged;

        public void Start()
        {
            // Hook up the event to the Unity event
            CanSetSkyChanged += (canSet) => OnCanSetSkyChanged?.Invoke(canSet);
            SkyChanged += (key) => OnSkyChanged?.Invoke(key);

            RemoteRenderingCoordinator.CoordinatorStateChange += ApplyStateToView;
            ApplyStateToView(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void ApplyStateToView(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CanSetSky = true;
                    break;
                default:
                    CanSetSky = false;
                    break;
            }
        }

        public override async void SetSky(string skyKey)
        {
            if (!CanSetSky)
            {
                Debug.Log("Unable to set sky right now");
                return;
            }

            if (AvailableCubemaps.ContainsKey(skyKey))
            {
                Debug.Log("Setting sky to " + skyKey);
                //Load the texture into the session
                var texture = await RemoteRenderingCoordinator.CurrentSession.Connection.LoadTextureFromSasAsync(AvailableCubemaps[skyKey]);

                //Apply the texture to the SkyReflectionSettings
                RemoteRenderingCoordinator.CurrentSession.Connection.SkyReflectionSettings.SkyReflectionTexture = texture;
                SkyChanged?.Invoke(skyKey);
            }
            else
            {
                Debug.Log("Invalid sky key");
            }
        }
    }
    ```

    Het belangrijkste gedeelte van deze code bestaat uit slechts enkele regels:

    ```cs
    //Load the texture into the session
    var texture = await RemoteRenderingCoordinator.CurrentSession.Connection.LoadTextureFromSasAsync(AvailableCubemaps[skyKey]);

    //Apply the texture to the SkyReflectionSettings
    RemoteRenderingCoordinator.CurrentSession.Connection.SkyReflectionSettings.SkyReflectionTexture = texture;
    ```

    Hier wordt een verwijzing naar het te gebruiken patroon opgehaald door het in de sessie te laden vanuit de ingebouwde blobopslag. Vervolgens hoeft u dat patroon alleen aan de `SkyReflectionTexture` van de sessie toe te wijzen om het toe te passen.

1. Maak een leeg GameObject in uw scène en geef deze de naam **SkyLight**.

1. Voeg het script **RemoteSky** toe aan uw GameObject **SkyLight**.

    Schakelen tussen 'sky lights' (belichtingen van de lucht) kan worden gedaan door het aanroepen van `SetSky` met een van de tekenreekssleutels die in `AvailableCubemaps` zijn gedefinieerd. Met de ingebouwde weergavecontroller in **AppMenu** worden automatisch knoppen gemaakt en worden de gebeurtenissen gekoppeld om `SetSky` met hun respectievelijke sleutel aan te roepen.
1. Druk op Play (Afspelen) in de Unity-editor en autoriseer een verbinding.
1. Nadat u de lokale runtime hebt verbonden met een externe sessie, navigeert u naar **AppMenu -> Session Tools -> Remote Sky** om de verschillende opties voor de lucht te verkennen en te zien hoe deze van invloed zijn op het testmodel, **TestModel**.

### <a name="scene-lights"></a>Scènebelichtingen

Externe scènebelichtingen zijn onder andere: punt, spot en directioneel. Net als het knipvlak dat hierboven is gemaakt, zijn deze scènebelichtingen externe entiteiten waaraan onderdelen zijn gekoppeld. Bij het belichten van uw externe scène is het belangrijk dat deze zo goed mogelijk overeenkomt met de belichting in uw lokale scène. Deze strategie is niet altijd mogelijk omdat veel Unity-toepassingen voor HoloLens 2 geen gebruik maken van fysieke rendering voor lokaal gerenderde objecten. Op een bepaald niveau kunnen we echter het eenvoudiger standaardlicht van Unity simuleren.

1. Maak een nieuw script met de naam **RemoteLight** en vervang de code door de onderstaande code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(Light))]
    public class RemoteLight : BaseRemoteLight
    {
        public bool AutomaticallyCreate = true;

        private bool lightReady = false;
        public override bool LightReady 
        {
            get => lightReady;
            set
            {
                lightReady = value;
                LightReadyChanged?.Invoke(lightReady);
            }
        }

        private ObjectType remoteLightType = ObjectType.Invalid;
        public override ObjectType RemoteLightType => remoteLightType;

        public UnityBoolEvent OnLightReadyChanged;

        public override event Action<bool> LightReadyChanged;

        private Light localLight; //Unity Light

        private Entity lightEntity;
        private LightComponentBase remoteLightComponent; //Remote Rendering Light

        private void Awake()
        {
            localLight = GetComponent<Light>();
            switch (localLight.type)
            {
                case LightType.Directional:
                    remoteLightType = ObjectType.DirectionalLightComponent;
                    break;
                case LightType.Point:
                    remoteLightType = ObjectType.PointLightComponent;
                    break;
                case LightType.Spot:
                case LightType.Area:
                    //Not supported in tutorial
                case LightType.Disc:
                    // No direct analog in remote rendering
                    remoteLightType = ObjectType.Invalid;
                    break;
            }
        }

        public void Start()
        {
            // Hook up the event to the Unity event
            LightReadyChanged += (ready) => OnLightReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        public void OnDestroy()
        {
            lightEntity?.Destroy();
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateLight();
                    break;
                default:
                    DestroyLight();
                    break;
            }
        }

        public override void CreateLight()
        {
            if (remoteLightComponent != null)
                return; //Nothing to do!

            //Create a root object for the light
            if(lightEntity == null)
                lightEntity = RemoteRenderingCoordinator.CurrentSession.Connection.CreateEntity();

            //Bind the remote entity to this game object
            lightEntity.BindToUnityGameObject(this.gameObject);

            //Sync the transform of this object so we can move the light
            var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
            syncComponent.SyncEveryFrame = true;

            //Add a light to the entity
            switch (RemoteLightType)
            {
                case ObjectType.DirectionalLightComponent:
                    var remoteDirectional = RemoteRenderingCoordinator.CurrentSession.Connection.CreateComponent(ObjectType.DirectionalLightComponent, lightEntity) as DirectionalLightComponent;
                    //No additional properties
                    remoteLightComponent = remoteDirectional;
                    break;

                case ObjectType.PointLightComponent:
                    var remotePoint = RemoteRenderingCoordinator.CurrentSession.Connection.CreateComponent(ObjectType.PointLightComponent, lightEntity) as PointLightComponent;
                    remotePoint.Radius = 0;
                    remotePoint.Length = localLight.range;
                    //remotePoint.AttenuationCutoff = //No direct analog in Unity legacy lights
                    //remotePoint.ProjectedCubeMap = //No direct analog in Unity legacy lights

                    remoteLightComponent = remotePoint;
                    break;
                default:
                    LightReady = false;
                    return;
            }

            // Set the common values for all light types
            UpdateRemoteLightSettings();

            LightReady = true;
        }

        public override void UpdateRemoteLightSettings()
        {
            remoteLightComponent.Color = localLight.color.toRemote();
            remoteLightComponent.Intensity = localLight.intensity;
        }

        public override void DestroyLight()
        {
            if (remoteLightComponent == null)
                return; //Nothing to do!

            remoteLightComponent.Destroy();
            remoteLightComponent = null;
            LightReady = false;
        }

        [ContextMenu("Sync Remote Light Configuration")]
        public override void RecreateLight()
        {
            DestroyLight();
            CreateLight();
        }

        public override void SetIntensity(float intensity)
        {
            localLight.intensity = Mathf.Clamp(intensity, 0, 1);
            UpdateRemoteLightSettings();
        }

        public override void SetColor(Color color)
        {
            localLight.color = color;
            UpdateRemoteLightSettings();
        }
    }
    ```

    Met dit script worden verschillende typen externe lichten gemaakt, afhankelijk van het type lokale Unity-licht waaraan het script is gekoppeld. Het externe licht dupliceert het lokale licht qua positie, rotatie, kleur en intensiteit. Indien mogelijk, stelt het externe licht ook extra configuratie in. De lichten zullen niet exact hetzelfde zijn, omdat Unity-lichten geen PBR-lichten zijn.

1. Zoek GameObject **DirectionalLight** in uw scène. Als u de standaard **DirectionalLight** uit uw scène hebt verwijderd, selecteert u in de bovenste menubalk *GameObject -> Light -> DirectionalLight* om een nieuw licht in uw scène te maken.

1. Selecteer GameObject **DirectionalLight** en klik op de knop **Add Component** (Onderdeel toevoegen) om het script **RemoteLight** toe te voegen.

1. Omdat dit script de basisklasse `BaseRemoteLight` implementeert, kunt u de meegeleverde weergavecontroller **AppMenu** gebruiken om te werken met het externe licht. Ga naar **AppMenu -> Session Tools -> Directional Light**.

    > [!NOTE]
    > De gebruikersinterface in **AppMenu** is beperkt tot één directioneel licht om redenen van eenvoud. Het is echter nog steeds mogelijk om puntlichten toe te voegen en het script **RemoteLight** eraan te koppelen. Deze extra lichten kunnen worden gewijzigd door de eigenschappen van het Unity-licht te bewerken in de editor. U moet de lokale wijzigingen handmatig naar het externe licht synchroniseren met behulp van het contextmenu **RemoteLight** in de inspector:
    >
    > ![Handmatige synchronisatie van extern licht](./media/sync-remote-light.png)

1. Druk op Play (Afspelen) in de Unity-editor en autoriseer een verbinding.

1. Nadat u uw runtime hebt verbonden met een externe sessie, plaatst en richt u uw camera (gebruik WASD en klik met de rechtermuisknop en beweeg de muis) om de weergavecontroller voor het directionele licht in beeld te krijgen. 
1. Gebruik de weergavecontroller van het externe licht om de eigenschappen van het licht te wijzigen. Met behulp van de handsimulatie van MRTK kunt u het directionele licht oppakken en draaien (houd Ctrl ingedrukt om te draaien) om het effect op de belichting van de scène te bekijken.

    ![Directioneel licht](./media/directional-light-test.png)

## <a name="editing-materials"></a>Bewerkingsmateriaal

Extern gerenderd [materiaal](../../../concepts/materials.md) kan worden aangepast om extra visuele effecten toe te voegen, de visuals van gerenderde modellen te verfijnen of aanvullende feedback te geven aan gebruikers. Materiaal kan om allerlei redenen en op veel manieren worden gewijzigd. Hier wordt uitgelegd hoe u de albedo-kleur van een materiaal wijzigt en de ruwheid en metaalachtigheid van een PBR-materiaal wijzigt.

> [!NOTE]
> Als een functie of effect kan worden geïmplementeerd met behulp van een **HierarchicalStateOverrideComponent**, is het beter om dat te gebruiken in plaats van het materiaal te wijzigen.

We maken een script dat een doelentiteit accepteert en een paar `OverrideMaterialProperty`-objecten configureert om de eigenschappen van het materiaal van de doelentiteit te wijzigen. Eerst halen we de [**MeshComponent**](../../../concepts/meshes.md#meshcomponent) van de doelentiteit op. Deze bevat een lijst met materiaal dat in de mesh is gebruikt. Omwille van eenvoud gebruiken we gewoon het eerste materiaal dat is gevonden. Deze simpele strategie kan gemakkelijk mislukken, afhankelijk van de manier waarop de inhoud is geschreven. Daarom wilt u waarschijnlijk een complexere aanpak gebruiken om het juiste materiaal te selecteren.

Vanuit het materiaal hebben we toegang tot algemene waarden, zoals albedo. Eerst moeten de materialen worden gecast in het juiste type, `PbrMaterial` of `ColorMaterial`, om de betreffende waarden op te halen, zoals u kunt zien in de methode **GetMaterialColor**. Zodra u een verwijzing naar het gewenste materiaal hebt, stelt u de waarden in en wordt de synchronisatie tussen de eigenschappen van het lokale materiaal en het externe materiaal door ARR verwerkt.

1. Maak een nieuw script met de naam **EntityMaterialController** en vervang de inhoud door de volgende code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using System.Linq;
    using UnityEngine;
    // to prevent namespace conflicts
    using ARRMaterial = Microsoft.Azure.RemoteRendering.Material;

    public class EntityMaterialController : BaseEntityMaterialController
    {
        public override bool RevertOnEntityChange { get; set; } = true;

        public override OverrideMaterialProperty<Color> ColorOverride { get; set; }
        public override OverrideMaterialProperty<float> RoughnessOverride { get; set; }
        public override OverrideMaterialProperty<float> MetalnessOverride { get; set; }

        private Entity targetEntity;
        public override Entity TargetEntity
        {
            get => targetEntity;
            set
            {
                if (targetEntity != value)
                {
                    if (targetEntity != null && RevertOnEntityChange)
                    {
                        Revert();
                    }

                    targetEntity = value;
                    ConfigureTargetEntity();
                    TargetEntityChanged?.Invoke(value);
                }
            }
        }

        private ARRMaterial targetMaterial;
        private ARRMeshComponent meshComponent;

        public override event Action<Entity> TargetEntityChanged;
        public UnityRemoteEntityEvent OnTargetEntityChanged;

        public void Start()
        {
            // Forward events to Unity events
            TargetEntityChanged += (entity) => OnTargetEntityChanged?.Invoke(entity);

            // If there happens to be a remote RayCaster on this object, assume we should listen for events from it
            if (GetComponent<BaseRemoteRayCastPointerHandler>() != null)
                GetComponent<BaseRemoteRayCastPointerHandler>().RemoteEntityClicked += (entity) => TargetEntity = entity;
        }

        protected override void ConfigureTargetEntity()
        {
            //Get the Unity object, to get the sync object, to get the mesh component, to get the material.
            var targetEntityGameObject = TargetEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

            var localSyncObject = targetEntityGameObject.GetComponent<RemoteEntitySyncObject>();
            meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
            if (meshComponent == null)
            {
                var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
                if (mesh != null)
                {
                    targetEntityGameObject.BindArrComponent<ARRMeshComponent>(mesh);
                    meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
                }
            }

            meshComponent.enabled = true;

            targetMaterial = meshComponent.RemoteComponent.Mesh.Materials.FirstOrDefault();
            if (targetMaterial == default)
            {
                return;
            }

            ColorOverride = new OverrideMaterialProperty<Color>(
                GetMaterialColor(targetMaterial), //The original value
                targetMaterial, //The target material
                ApplyMaterialColor); //The action to take to apply the override

            //If the material is a PBR material, we can override some additional values
            if (targetMaterial.MaterialSubType == MaterialType.Pbr)
            {
                var firstPBRMaterial = (PbrMaterial)targetMaterial;

                RoughnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Roughness, //The original value
                    targetMaterial, //The target material
                    ApplyRoughnessValue); //The action to take to apply the override

                MetalnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Metalness, //The original value
                    targetMaterial, //The target material
                    ApplyMetalnessValue); //The action to take to apply the override
            }
            else //otherwise, ensure the overrides are cleared out from any previous entity
            {
                RoughnessOverride = null;
                MetalnessOverride = null;
            }
        }

        public override void Revert()
        {
            if (ColorOverride != null)
                ColorOverride.OverrideActive = false;

            if (RoughnessOverride != null)
                RoughnessOverride.OverrideActive = false;

            if (MetalnessOverride != null)
                MetalnessOverride.OverrideActive = false;
        }

        private Color GetMaterialColor(ARRMaterial material)
        {
            if (material == null)
                return default;

            if (material.MaterialSubType == MaterialType.Color)
                return ((ColorMaterial)material).AlbedoColor.toUnity();
            else
                return ((PbrMaterial)material).AlbedoColor.toUnity();
        }

        private void ApplyMaterialColor(ARRMaterial material, Color color)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Color)
                ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
            else
                ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
        }

        private void ApplyRoughnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Roughness
                ((PbrMaterial)material).Roughness = value;
        }

        private void ApplyMetalnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Metalness
                ((PbrMaterial)material).Metalness = value;
        }
    }
    ```

Het type `OverrideMaterialProperty` moet flexibel genoeg zijn om desgewenst enkele andere materiaalwaarden te kunnen wijzigen. Met het type `OverrideMaterialProperty` wordt de status van een overschrijving bijgehouden, worden de oude en nieuwe waarde bewaard en wordt een gemachtigde gebruikt voor het instellen van de overschrijving. Bekijk bijvoorbeeld de `ColorOverride`:

```cs
ColorOverride = new OverrideMaterialProperty<Color>(
    GetMaterialColor(targetMaterial), //The original value
    targetMaterial, //The target material
    ApplyMaterialColor); //The action to take to apply the override
```

Hiermee maakt u een nieuwe `OverrideMaterialProperty`, waarbij de overschrijving het type `Color` verpakt. We leveren de huidige of oorspronkelijke kleur op het moment dat de overschrijving wordt gemaakt. We geven het ook ARR-materiaal om te bewerken. Ten slotte zorgen we voor een gemachtigde waarmee de overschrijving wordt toegepast. De gemachtigde is een methode die een ARR-materiaal accepteert en het type dat met de overschrijving wordt verpakt. Deze methode is het belangrijkste onderdeel om te begrijpen hoe materiaalwaarden in ARR worden gewijzigd.

De `ColorOverride` gebruikt de methode `ApplyMaterialColor` om het werk te doen:

```cs
private void ApplyMaterialColor(ARRMaterial material, Color color)
{
    if (material.MaterialSubType == MaterialType.Color)
        ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
    else
        ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
}
```

Deze code accepteert een materiaal en een kleur. Er wordt gecontroleerd welk soort materiaal het is en vervolgens wordt een cast-conversie uitgevoerd om de kleur toe te passen.

`RoughnessOverride` en `MetalnessOverride` werken ongeveer hetzelfde, waarbij het werk wordt gedaan met behulp van de methoden `ApplyRoughnessValue` en `ApplyMetalnessValue`.

Laten we de materiaalcontroller eens testen.

1. Voeg het script **EntityMaterialController** toe aan uw GameObject **TestModel**.
1. Druk op Play (Afspelen) in Unity om de scène te starten en verbinding te maken met ARR.
1. Nadat u uw runtime hebt verbonden met een externe sessie en het model hebt geladen, gaat u naar **AppMenu -> Model Tools -> Edit Material**
1. Selecteer een entiteit uit het model met behulp van de gesimuleerde handen en klik op **TestModel**.
1. Controleer of de materiaalweergavecontroller (**AppMenu -> Model Tools -> Edit Material**) is bijgewerkt naar de doelentiteit.
1. Gebruik de materiaalweergavecontroller om het materiaal op de doelentiteit aan te passen.

Omdat we alleen het eerste materiaal van de mesh wijzigen, is het mogelijk dat u geen wijziging in het materiaal ziet. Gebruik de hiërarchische overschrijving **SeeThrough** om te zien of het materiaal dat u wijzigt, zich in de mesh bevindt.

![Voorbeeld van materiaalbewerking](./media/material-edit-example.png)

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd! U hebt nu alle kernfunctionaliteit van Azure Remote Rendering geïmplementeerd. In het volgende hoofdstuk krijgt u informatie over het beveiligen van Azure Remote Rendering en blobopslag. Dit zijn de eerste stappen om een commerciële toepassing uit te brengen waarvoor Azure Remote Rendering wordt gebruikt.

> [!div class="nextstepaction"]
> [Volgende: Azure Remote Rendering en modelopslag beveiligen](../security/security.md)