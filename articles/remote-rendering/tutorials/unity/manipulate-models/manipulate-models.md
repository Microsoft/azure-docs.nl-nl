---
title: Modellen bewerken
description: Extern gegenereerde modellen bewerken door ze te verplaatsen, draaien, schalen en meer
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 301d6eb0867604a6e780eb1d059eef0c153f246f
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574506"
---
# <a name="tutorial-manipulating-models"></a>Zelfstudie: Modellen bewerken

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Visuele en bewerkingsgrenzen toevoegen rond extern gegenereerde modellen
> * Verplaatsen, draaien en schalen
> * Raycast met ruimtelijke query's
> * Eenvoudige animaties voor extern gegenereerde objecten toevoegen

## <a name="prerequisites"></a>Vereisten

* Deze zelfstudie gaat verder op de [zelfstudie: Interfaces en aangepaste modellen](../custom-models/custom-models.md).

## <a name="query-remote-object-bounds-and-apply-to-local-bounds"></a>Grenzen van externe objecten opvragen en toepassen op lokale grenzen

Om externe objecten te gebruiken, hebben we eerst een lokale vertegenwoordiging nodig om te gebruiken. De [objectgrenzen](../../../concepts/object-bounds.md) zijn nuttig om snel een extern object te bewerken. De externe grenzen kunnen opgevraagd worden bij ARR met de lokale Entiteit als referentie. De grenzen worden opgevraagd wanneer het model in de externe sessie is geladen.

De grenzen van een model worden vastgelegd door het vak dat het volledige model bevat, net zoals de [**BoxCollider**](https://docs.unity3d.com/Manual/class-BoxCollider.html) van Unity, waarvan het centrum en de grootte zijn vastgelegd voor de x-, y- en z-assen. In feite gebruiken we de **BoxCollider** van Unity om de grenzen van het externe model weer te geven.

1. Maak een nieuw script in dezelfde map als **RemoteRenderedModel** en noem het **RemoteBounds**.
1. Vervang de inhoud van het script door de volgende code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(BaseRemoteRenderedModel))]
    public class RemoteBounds : BaseRemoteBounds
    {
        //Remote bounds works with a specific remotely rendered model
        private BaseRemoteRenderedModel targetModel = null;

        private RemoteBoundsState currentBoundsState = RemoteBoundsState.NotReady;

        public override RemoteBoundsState CurrentBoundsState
        {
            get => currentBoundsState;
            protected set
            {
                if (currentBoundsState != value)
                {
                    currentBoundsState = value;
                    BoundsStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<RemoteBoundsState> BoundsStateChange;

        public void Awake()
        {
            BoundsStateChange += HandleUnityEvents;
            targetModel = GetComponent<BaseRemoteRenderedModel>();

            targetModel.ModelStateChange += TargetModel_OnModelStateChange;
            TargetModel_OnModelStateChange(targetModel.CurrentModelState);
        }

        private void TargetModel_OnModelStateChange(ModelState state)
        {
            switch (state)
            {
                case ModelState.Loaded:
                    QueryBounds();
                    break;
                default:
                    BoundsBoxCollider.enabled = false;
                    CurrentBoundsState = RemoteBoundsState.NotReady;
                    break;
            }
        }

        // Create an async query using the model entity
        async private void QueryBounds()
        {
            //Implement me
        }
    }
    ```

    > [!NOTE]
    > Als er een fout wordt weergegeven in Visual Studio die claimt dat *Functie 'X' niet beschikbaar is in C# 6. Gebruik dan taalversie 7.0 of hoger*, deze foutmelding mag u negeren. Dit heeft te maken met het genereren van de oplossingen en projecten door Unity.

    Dit script dient toegevoegd te worden aan hetzelfde GameObject als het script dat **BaseRemoteRenderedModel** implementeert. In dit geval is dat **RemoteRenderedModel**. Net zoals bij eerdere scripts verwerkt deze initiële code alle statuswijzigingen, gebeurtenissen en informatie over externe grenzen.

    Er is slechts één methode naar links om het volgende te implementeren: **QueryBounds**. **QueryBounds** haalt de grenzen asynchroon op, neemt het resultaat van de query en past deze toe op de lokale **BoxCollider**.

    De methode **QueryBounds** is eenvoudig: verzend een query naar de externe rendering-sessie en wacht op het resultaat.

1. Vervang de **QueryBounds**-methode door de volgende voltooide methode:

    ```cs
    // Create a query using the model entity
    async private void QueryBounds()
    {
        var remoteBounds = targetModel.ModelEntity.QueryLocalBoundsAsync();
        CurrentBoundsState = RemoteBoundsState.Updating;
        await remoteBounds;

        if (remoteBounds.IsCompleted)
        {
            var newBounds = remoteBounds.Result.toUnity();
            BoundsBoxCollider.center = newBounds.center;
            BoundsBoxCollider.size = newBounds.size;
            BoundsBoxCollider.enabled = true;
            CurrentBoundsState = RemoteBoundsState.Ready;
        }
        else
        {
            CurrentBoundsState = RemoteBoundsState.Error;
        }
    }
    ```

    Het resultaat van de query wordt gecontroleerd om te zien of het is gelukt. Zo ja, zet de geretourneerde grenzen dan om in een indeling die **BoxCollider** kan accepteren en pas ze toe.

Wanneer het **RemoteBounds**-script wordt toegevoegd aan hetzelfde gameobject als de **RemoteRenderedModel**, wordt indien nodig een **BoxCollider** toegevoegd. Wanneer het model de status `Loaded` bereikt, worden de grenzen automatisch opgevraagd en toegepast op de **BoxCollider**.

1. Voeg de component **RemoteBounds** toe met behulp van het GameObject **TestModel** dat u eerder hebt gemaakt.
1. Bevestig dat het script is toegevoegd.

     ![RemoteBounds-componenten toevoegen](./media/remote-bounds-script.png)

1. Voer de toepassing opnieuw uit. Kort nadat het model is geladen, ziet u de grenzen voor het externe object. U ziet iets zoals de onderstaande waarden:

     ![Schermopname van het voorbeeld van de grenzen van het externe object.](./media/updated-bounds.png)

Nu hebben we een lokale **BoxCollider** geconfigureerd met nauwkeurige grenzen voor het Unity-object. Met de grenzen kunnen voor visualisatie en interactie dezelfde strategieën gebruikt worden als voor een lokaal gegenereerd object. Bijvoorbeeld scripts die de Transformatie, fysica en meer veranderen.

## <a name="move-rotate-and-scale"></a>Verplaatsen, draaien en schalen  

Het verplaatsen, roteren en schalen van extern gegenereerde objecten werkt hetzelfde als voor elk ander Unity-object. De **RemoteRenderingCoordinator** roept, in de `LateUpdate`-methode, `Update` aan voor de huidige actieve sessie. `Update` synchroniseert transformaties van lokale modelentiteiten met hun externe tegenhangers. Als u een extern gegenereerd model wilt verplaatsen, draaien of schalen, hoeft u enkel maar de transformatie van het GameObject dat het extern model vertegenwoordigt te verplaatsen, draaien of schalen. Hier gaan we de transformatie wijzigen van het bovenliggende GameObject waaraan het **RemoteRenderedModel**-script is gekoppeld.

In deze zelfstudie wordt MRTK gebruikt voor interactie met objecten. Het grootste deel van de specifieke implementatie van MRKTK om een object te verplaatsen, draaien of schalen valt buiten het bereik van deze zelfstudie. Er staat een controller voor modelweergave die vooraf is geconfigureerd in het **AppMenu**, in het menu **Hulpprogramma's voor modellen**.

1. Zorg ervoor dat het GameObject **TestModel** dat u eerder hebt aangemaakt zich in de scène bevindt.
1. Zorg ervoor dat de prefab **AppMenu** zich in de scène bevindt.
1. Druk op de Afspeelknop van Unity om de scène af te spelen en open het menu **Hulpprogramma's voor modellen** in het **AppMenu**.
![Controller weergeven](./media/model-with-view-controller.png)

Het **AppMenu** heeft een submenu **Hulpprogramma's voor modellen** dat een weergavecontroller implementeert voor binding met het model. Wanneer het GameObject een **RemoteBounds**-component bevat, wordt door de weergavecontroller een [**BoundingBox**](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_BoundingBox.html)-onderdeel toegevoegd. Dit is een MRTK-component dat een begrenzingsvak rond een object met een **BoxCollider** genereert. Een [**ObjectManipulator**](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/api/Microsoft.MixedReality.Toolkit.UI.ObjectManipulator.html), verantwoordelijk is voor interacties met de hand. Met deze scripts gecombineerd kunnen we het extern gegenereerde modellen verplaatsen, draaien en schalen.

1. Verplaats uw muis naar het deelvenster Game en klik erin voor focus.
1. Gebruik de [Handsimulatie van MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/InputSimulation/InputSimulationService.html#hand-simulation) en houd de Shift-toets aande linkerzijde ingedrukt.
1. Bestuur de handsimulatie, zodat de handstraal naar het testmodel wijst.

    ![Wijzende handstraal](./media/handray-engine.png)

1. Houd de linkermuisknop ingedrukt en sleep het model om het te verplaatsen.

U ziet dat de extern gegenereerde inhoud samen met het begrenzingsvak wordt verplaatst. Er kan een vertraging zitten tussen het begrenzingsvak en de externe inhoud. Deze vertraging is afhankelijk van uw internetlatentie en bandbreedte.

## <a name="ray-cast-and-spatial-queries-of-remote-models"></a>Ray cast en ruimtelijke query's van externe modellen

Een box collider is geschikt voor interactie met het hele model, maar is niet gedetailleerd genoeg om te communiceren met de afzonderlijke onderdelen van een model. Om dit op te lossen, kunnen we gebruikmaken van [externe raycasting](../../../overview/features/spatial-queries.md#ray-casts). Externe raycasting is een API van Azure Remote Rending om stralen te casten in de externe scène en treffers lokaal te retourneren. Deze techniek kan worden gebruikt om onderliggende entiteiten van een groot model te selecteren of om informatie te verkrijgen over treffers zoals positie, oppervlaktenormaal en afstand.

Het testmodel heeft een aantal onderliggende entiteiten die kunnen worden opgevraagd en geselecteerd. Hier geeft de selectie de naam van de geselecteerde Entiteit door aan de Unity Console. Bekijk het hoofdstuk [Materialen, belichting en effecten](../materials-lighting-effects/materials-lighting-effects.md#highlighting-and-outlining) om de geselecteerde Entiteit te markeren.

Laten we eerst een statische wrapper maken rond de query's van de externe raycast. Met dit script wordt een positie en richting in Unity-ruimte geaccepteerd, geconverteerd naar de gegevenstypen die de externe raycast accepteert en worden de resultaten geretourneerd. Het script maakt gebruik van de `RayCastQueryAsync`-API.

1. Maak een nieuw script met de naam **RemoteRayCaster** en vervang de inhoud door de volgende code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    /// <summary>
    /// Wraps the Azure Remote Rendering RayCast queries to easily send requests using Unity data types
    /// </summary>
    public class RemoteRayCaster
    {
        public static double maxDistance = 30.0;

        public static async Task<RayCastHit[]> RemoteRayCast(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            if(RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
            {
                var rayCast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), maxDistance, hitPolicy);
                var result = await RemoteRenderingCoordinator.CurrentSession.Connection.RayCastQueryAsync(rayCast);
                return result.Hits;
            }
            else
            {
                return new RayCastHit[0];
            }
        }

        public static async Task<Entity[]> RemoteRayCastEntities(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            var hits = await RemoteRayCast(origin, dir, hitPolicy);
            return hits.Select(hit => hit.HitEntity).Where(entity => entity != null).ToArray();
        }
    }
    ```

    > [!NOTE]
    > Unity heeft een klasse met de naam [**RaycastHit**](https://docs.unity3d.com/ScriptReference/RaycastHit.html)en Azure Remote Rendering heeft een klasse met de naam [**RayCastHit**](/dotnet/api/microsoft.azure.remoterendering.raycasthit). De hoofdletter **C** is een belangrijk verschil om compilatiefouten te voorkomen.

    **RemoteRayCaster** biedt een gemeenschappelijk toegangspunt om externe stralen in de huidige sessie te casten. Meer specifiek gaan we een handler voor een MRTK-pointer implementeren. Het script zal de `IMixedRealityPointerHandler`-interface implementeren, die MRTK zal vertellen dat we willen dat dit script luistert naar [Mixed Reality Pointer](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/Input/Pointers.html)-gebeurtenissen.

1. Maak een nieuw script met de naam **RemoteRayCastPointerHandler** en vervang de code door de volgende code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.MixedReality.Toolkit.Input;
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    public class RemoteRayCastPointerHandler : BaseRemoteRayCastPointerHandler, IMixedRealityPointerHandler
    {
        public UnityRemoteEntityEvent OnRemoteEntityClicked = new UnityRemoteEntityEvent();

        public override event Action<Entity> RemoteEntityClicked;

        public void Awake()
        {
            // Forward events to Unity events
            RemoteEntityClicked += (entity) => OnRemoteEntityClicked?.Invoke(entity);
        }

        public async void OnPointerClicked(MixedRealityPointerEventData eventData)
        {
            if (RemoteEntityClicked != null) //Ensure someone is listening before we do the work
            {
                var firstHit = await PointerDataToRemoteRayCast(eventData.Pointer);
                if (firstHit.success)
                    RemoteEntityClicked.Invoke(firstHit.hit.HitEntity);
            }
        }

        public void OnPointerDown(MixedRealityPointerEventData eventData) { }

        public void OnPointerDragged(MixedRealityPointerEventData eventData) { }

        public void OnPointerUp(MixedRealityPointerEventData eventData) { }

        private async Task<(bool success, RayCastHit hit)> PointerDataToRemoteRayCast(IMixedRealityPointer pointer, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            RayCastHit hit;
            var result = pointer.Result;
            if (result != null)
            {
                var endPoint = result.Details.Point;
                var direction = pointer.Rays[pointer.Result.RayStepIndex].Direction;
                Debug.DrawRay(endPoint, direction, Color.green, 0);
                hit = (await RemoteRayCaster.RemoteRayCast(endPoint, direction, hitPolicy)).FirstOrDefault();
            }
            else
            {
                hit = new RayCastHit();
            }
            return (hit.HitEntity != null, hit);
        }
    }
    ```

De `OnPointerClicked`-methode van **RemoteRayCastPointerHandler** wordt aangeroepen door MRTK wanneer een Pointer 'klikt' op een collider, zoals onze box collider. Daarna wordt `PointerDataToRemoteRayCast` aangeroepen om de resultaten van de pointer om te zetten in een punt en een richting. Dat punt en deze richting worden vervolgens gebruikt om een externe straal te casten in de externe sessie.

![Grenzen bijgewerkt](./media/raycast-local-remote.png)

Verzoeken versturen voor raycasting met een klik is een efficiënte strategie om externe objecten te bevragen. Het biedt echter geen ideale gebruikerservaring omdat de cursor botst met de box collider, en niet met het model zelf.

U kunt ook een nieuwe MRTK-pointer maken die vaker zijn stralen cast in de externe sessie. Hoewel dit ingewikkelder is, wordt de gebruikerservaring hierdoor beter. Deze strategie valt buiten het bereik van deze zelfstudie, maar een voorbeeld hiervan is te zien in de Showcase App, die te vinden is in de [opslagplaats met ARR-voorbeelden](https://github.com/Azure/azure-remote-rendering/tree/master/Unity/Showcase).

Wanneer een raycast is voltooid in de **RemoteRayCastPointerHandler**, wordt de treffer `Entity` verzonden vanaf de Unity-gebeurtenis `OnRemoteEntityClicked`. Om op deze gebeurtenis te reageren, maken we een hulpscript dat de `Entity` accepteert en er een actie op uitvoert. Laten we beginnen door het script de naam van de `Entity` te laten afdrukken naar het foutopsporingslogboek.

1. Maak een nieuw script met de naam **RemoteEntityHelper** en vervang de inhoud door de onderstaande code:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.
    
    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using UnityEngine;
    
    public class RemoteEntityHelper : MonoBehaviour
    {
        public void EntityToDebugLog(Entity entity)
        {
            Debug.Log(entity.Name);
        }
    }
    ```

2. Voeg op het GameObject **TestModel** dat u eerder hebt gemaakt, de componenten **RemoteRayCastPointerHandler** en **RemoteEntityHelper** toe.
1. Wijs de `EntityToDebugLog`-methode toe aan de `OnRemoteEntityClicked`-gebeurtenis. Wanneer het uitvoertype van de gebeurtenis en het invoertype van de methode overeenkomen, kunnen we de dynamische gebeurtenishookup van Unity gebruiken om de waarde van de gebeurtenis automatisch over te dragen naar de methode.
    1. Een nieuw callback-veld ![Add callback](./media/add-callback-remote-entity-clicked.png) maken
    1. Sleep de component **Remote Entity Helper** naar het veld Object om te verwijzen naar het bovenliggende GameObject ![Assign object](./media/assign-object.png)
    1. Wijs `EntityToDebugLog` toe als de callback ![Assign callback](./media/remote-entity-event.png)
1. Druk op afspelen in de Unity Editor om de scène te starten, verbinding te maken met een externe sessie en het testmodel te laden.
1. Gebruik de Handsimulatie van MRTK en houd de Shift-toets aande linkerzijde ingedrukt.
1. Bestuur de handsimulatie, zodat de handstraal naar het testmodel wijst.
1. Klik lang om een tik in de lucht te simuleren en zo de `OnPointerClicked`-gebeurtenis uit te voeren.
1. Controle of de Unity Console een logboekbericht bevat met de naam van de geselecteerde onderliggende entiteit. Bijvoorbeeld: ![Voorbeeld van onderliggende entiteit](./media/child-entity-example.png)

## <a name="synchronizing-the-remote-object-graph-into-the-unity-hierarchy"></a>De grafiek van het externe object synchroniseren met de Unity-hiërarchie

Tot nu toe hebben we slechts één lokaal GameObject gezien dat het volledige model vertegenwoordigt. Dit werkt goed om het hele model te genereren en bewerken. Als we echter effecten wilt toepassen of specifieke onderliggende entiteiten willen bewerken, dan moeten we lokale GameObjects maken om die entiteiten weer te geven. Eerst kunnen we het testmodel handmatig verkennen.

1. Start de scène en laad het testmodel.
1. Vouw de onderliggende items van het GameObject **TestModel** in de Unity-hiërarchie uit en selecteer het GameObject **TestModel_Entity**.
1. Klik in de Inspector op de knop *Onderliggende items tonen*.
![Onderliggende items tonen](./media/show-remote-children.png)
1. Ga verder met het uitvouwen van onderliggende items in de hiërarchie en klik op *Onderliggende items weergeven* totdat een grote lijst met onderliggende items wordt weer gegeven.
![Alle onderliggende items](./media/test-model-children.png)

Er is nu een lijst met tientallen entiteiten te zien in de hiërarchie. Als u er een selecteert, worden de componenten `Transform` en `RemoteEntitySyncObject` weergegeven in de Inspector. Standaard wordt niet elke entiteit automatisch gesynchroniseerd met elk frame, waardoor lokale wijzigingen in de `Transform` niet gesynchroniseerd worden naar de server. U kunt *Elk frame synchroniseren* aanvinken en vervolgens de transformatie verplaatsen, schalen of draaien in de Scèneweergave. U zult het gegenereerde model niet zien in de scèneweergave, bekijk de Gameweergave om de nieuwe positie en draaiing van het model te zien.

Hetzelfde proces kan via een programma worden uitgevoerd en vormt de eerste stap om specifieke externe entiteiten te veranderen.

1. Wijzig het **RemoteEntityHelper**-script zodat het ook de volgende methode bevat:

    ```cs
    public void MakeSyncedGameObject(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var sync = entityGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;
    }
    ```

1. Voeg een extra callback toe aan de gebeurtenis `OnRemoteEntityClicked` van **RemoteRayCastPointerHandler** en stel deze in op `MakeSyncedGameObject`.
![Extra callback](./media/additional-callback.png)
1. Gebruik de Handsimulatie van MRTK en houd de Shift-toets aande linkerzijde ingedrukt.
1. Bestuur de handsimulatie, zodat de handstraal naar het testmodel wijst.
1. Klik lang om een tik in de lucht te simuleren en zo de `OnPointerClicked`-gebeurtenis uit te voeren.
1. Controleer de hiërarchie en vouw deze uit om een nieuw onderliggend object te zien, dat de aangeklikte entiteit weergeeft.
![Weergave GameObject](./media/gameobject-representing-entity.png)
1. Verwijder na het testen de callback voor `MakeSyncedGameObject`, aangezien we deze later gaan opnemen in andere effecten.

> [!NOTE]
> Elk frame synchroniseren is enkel vereist wanneer u de transformatiegegevens wilt synchroniseren. Bij het synchroniseren van transformaties komt een zekere overhead kijken, gebruik dit dus spaarzaam.

Een lokale instantie maken en deze automatisch synchroniseren is de eerste stap om onderliggende entiteiten te bewerken. Dezelfde technieken die we hebben gebruikt om het volledige model te bewerken, kunnen ook worden gebruikt voor de onderliggende entiteiten. Nadat u bijvoorbeeld een gesynchroniseerde lokale instantie van een entiteit hebt gemaakt, kunt u de grenzen ervan opvragen en handlers voor bewerking toevoegen zodat de entiteit kan worden verplaatst met de handstralen van de gebruiker.

## <a name="next-steps"></a>Volgende stappen

U kunt nu extern gegenereerde modellen bewerken en ze gebruiken! In de volgende zelfstudie behandelen we het aanpassen van materialen, het veranderen van de belichting en het toepassen van effecten op extern gegenereerde modellen.

> [!div class="nextstepaction"]
> [Volgende: Materialen, belichting en effecten verfijnen](../materials-lighting-effects/materials-lighting-effects.md)