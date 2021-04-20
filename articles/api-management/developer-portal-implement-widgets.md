---
title: Widgets implementeren in de ontwikkelaarsportal
titleSuffix: Azure API Management
description: Meer informatie over het implementeren van widgets die gegevens uit externe API's verbruiken en weergeven in API Management ontwikkelaarsportal.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 4bda136a1abe8f9ffb443973731ebbe13b56ac3b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741617"
---
# <a name="implement-widgets-in-the-developer-portal"></a>Widgets implementeren in de ontwikkelaarsportal

In deze zelfstudie implementeert u een widget die gegevens uit een externe API verbruikt en deze we bekijken in API Management ontwikkelaarsportal.

De widget haalt sessiebeschrijvingen op uit de [voorbeeldvergadering-API.](https://conferenceapi.azurewebsites.net/?format=json) De sessie-id wordt ingesteld via een aangewezen widgeteditor.

Als hulp bij het ontwikkelingsproces raadpleegt u de voltooide widget in de map van de `examples` GitHub-opslagplaats API Management [ontwikkelaarsportal:](https://github.com/Azure/api-management-developer-portal/) `/examples/widgets/conference-session` .

:::image type="content" source="media/developer-portal-implement-widgets/widget-published.png" alt-text="Schermopname van gepubliceerde widget":::

## <a name="prerequisites"></a>Vereisten

* Stel een lokale [omgeving in voor](developer-portal-self-host.md#step-1-set-up-local-environment) de nieuwste versie van de ontwikkelaarsportal.

* U moet de anatomie van [de Paperbits-widget begrijpen.](https://paperbits.io/wiki/widget-anatomy)


## <a name="copy-the-scaffold"></a>De scaffold kopiëren

Gebruik een `widget` scaffold uit de map als uitgangspunt om de nieuwe widget te `/scaffolds` bouwen.

1. Kopieer de map `/scaffolds/widget` naar `/community/widgets` .
1. Wijzig de naam van de map in `conference-session` .

## <a name="rename-exported-module-classes"></a>Naam van geëxporteerde moduleklassen wijzigen

Wijzig de naam van de geëxporteerde moduleklassen door het `Widget` voorvoegsel te vervangen `ConferenceSession` door in deze bestanden:

- `widget.design.module.ts`

- `widget.publish.module.ts`

- `widget.runtime.module.ts`
    
Wijzig bijvoorbeeld in het `widget.design.module.ts` bestand `WidgetDesignModule` in `ConferenceSessionDesignModule` :
    
```typescript
export class WidgetDesignModule implements IInjectorModule {
```
tot 
    
```typescript
export class ConferenceSessionDesignModule implements IInjectorModule {
```
    
   
## <a name="register-the-widget"></a>De widget registreren

Registreer de modules van de widget in de hoofdmodules van de portal door de volgende regels toe te voegen in de respectieve bestanden:

1. `src/apim.design.module.ts` - een module die ontwerptijdafhankelijkheden registreert.

   ```typescript
   import { ConferenceSessionDesignModule } from "../community/widgets/conference-session/widget.design.module";
   
   ...
   injector.bindModule(new ConferenceSessionDesignModule());
   ```
1. `src/apim.publish.module.ts` : een module die publiceertijdafhankelijkheden registreert.

   ```typescript
   import { ConferenceSessionPublishModule } from "../community/widgets/conference-session/widget.publish.module";

    ...

    injector.bindModule(new ConferenceSessionPublishModule());
    ```

1. `src/apim.runtime.module.ts` - runtime-afhankelijkheden.

    ```typescript
    import { ConferenceSessionRuntimeModule } from "../community/widgets/conference-session/widget.runtime.module";

    ...

    injector.bindModule(new ConferenceSessionRuntimeModule());
    ```

## <a name="place-the-widget-in-the-portal"></a>De widget in de portal plaatsen

U bent nu klaar om de gedupliceerde scaffold in te sluiten en te gebruiken in de ontwikkelaarsportal.

1. Voer de opdracht `npm start` uit.

1. Wanneer de toepassing wordt geladen, plaats u de nieuwe widget op een pagina. U vindt deze onder de naam `Your widget` in de categorie in de `Community` widget-selector.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-selector.png" alt-text="Schermopname van widget-selector":::

1. Sla de pagina op door **op Ctrl** + **S (of** **⌘** + **S in** macOS) te drukken.

    > [!NOTE]
    > In de ontwerptijd kunt u nog steeds communiceren met de website door de **Ctrl-toets** (of **⌘**) ingedrukt te houden.

## <a name="add-custom-properties"></a>Aangepaste eigenschappen toevoegen

De widget kan alleen sessiebeschrijvingen ophalen als deze op de hoogte is van de sessie-id. Voeg de `Session ID` eigenschap toe aan de respectieve interfaces en klassen:

De widget kan alleen de sessiebeschrijving ophalen als deze op de hoogte is van de sessie-id. Voeg de eigenschap sessie-id toe aan de respectieve interfaces en klassen:

1. `widgetContract.ts` - gegevenscontract (gegevenslaag) die definiëren hoe de widgetconfiguratie persistent wordt.

    ```typescript
    export interface WidgetContract extends Contract {
        sessionNumber: string;
    }
    ```

1. `widgetModel.ts` - model (bedrijfslaag) - een primaire weergave van de widget in het systeem. Het wordt bijgewerkt door editors en weergegeven door de presentatielaag.

    ```typescript
    export class WidgetModel {
        public sessionNumber: string;
    }
    ```

1. `ko/widgetViewModel.ts` - viewmodel (presentatielaag) - een ui-framework-specifiek object dat door de ontwikkelaarsportal wordt weergegeven met de HTML-sjabloon.

    > [!NOTE]
    > U hoeft niets in dit bestand te wijzigen.

## <a name="configure-binders"></a>Binders configureren

Schakel de stroom van de in `sessionNumber` van de gegevensbron naar de widgetpresentatie. Bewerk `ModelBinder` de entiteiten en `ViewModelBinder` :

1. `widgetModelBinder.ts` helpt bij het voorbereiden van het model met behulp van de gegevens die in het contract worden beschreven.

    ```typescript
    export class WidgetModelBinder implements IModelBinder<WidgetModel> {
        public async contractToModel(contract: WidgetContract): Promise<WidgetModel> {
            model.sessionNumber = contract.sessionNumber || "107"; // 107 is the default session id
            ...
        }
    
        public modelToContract(model: WidgetModel): Contract {
            const contract: WidgetContract = {
                sessionNumber: model.sessionNumber
                ...
            };
            ...
        }
    }
    ```

1. `ko/widgetViewModelBinder.ts` weet hoe de ontwikkelaarsportal het model (als een viewmodel) moet presenteren in een specifiek UI-framework.

    ```typescript
    ...
    public async updateViewModel(model: WidgetModel, viewModel: WidgetViewModel): Promise<void> {
            viewModel.runtimeConfig(JSON.stringify({
                sessionNumber: model.sessionNumber
            }));
        }
    }
    ...
    ```

## <a name="adjust-design-time-widget-template"></a>Widgetsjabloon voor ontwerp aanpassen

De onderdelen van elk bereik worden onafhankelijk uitgevoerd. Ze hebben afzonderlijke containers voor afhankelijkheidsinjectie, hun eigen configuratie, levenscyclus, enzovoort. Ze kunnen zelfs powered by ui-frameworks zijn (in dit voorbeeld is dit De js van Dek).

Vanuit het perspectief van ontwerptijd is elk runtime-onderdeel slechts een HTML-tag met bepaalde kenmerken en/of inhoud. Configuratie indien nodig wordt doorgegeven met gewone markeringen. In eenvoudige gevallen, zoals in dit voorbeeld, wordt de parameter doorgegeven in het kenmerk . Als de configuratie complexer is, kunt u een id gebruiken van de vereiste instellingen die zijn opgehaald door een aangewezen configuratieprovider (bijvoorbeeld `ISettingsProvider` ).

1. Werk het bestand `ko/widgetView.html` bij:

    ```html
    <widget-runtime data-bind="attr: { params: runtimeConfig }"></widget-runtime>
    ```

    Wanneer in de ontwikkelaarsportal `attr` de binding wordt uitgevoerd in *ontwerp-* of *publicatietijd,* is de resulterende HTML:

    ```html
    <widget-runtime params="{ sessionNumber: 107 }"></widget-runtime>
    ```

    Vervolgens wordt het onderdeel in runtime gelezen en gebruikt `widget-runtime` `sessionNumber` in de initialisatiecode (zie hieronder).

1. Werk het bestand `widgetHandlers.ts` bij om de sessie-id toe te wijzen bij het maken:

    ```typescript
    ...
    createModel: async () => {
        var model = new ConferenceSessionModel();
        model.sessionNumber = "107";
            return model;
        }
    ...
    ```

## <a name="revise-runtime-view-model"></a>Runtimeweergavemodel herzien

Runtimeonderdelen zijn de code die wordt uitgevoerd op de website zelf. In de API Management-ontwikkelaarsportal zijn dit bijvoorbeeld alle scripts achter dynamische onderdelen (bijvoorbeeld *API-details,* *API-console),* verwerking van bewerkingen zoals het genereren van codevoorbeelden, het verzenden van aanvragen, enzovoort.

Het weergavemodel van uw runtimeonderdeel moet de volgende methoden en eigenschappen hebben:

- De eigenschap (gemarkeerd met deator) die wordt gebruikt als een parameter voor onderdeelinvoer die van buiten wordt doorgegeven (de markering die is gegenereerd `sessionNumber` `Param` in de ontwerptijd; zie de vorige stap).
- De `sessionDescription` eigenschap die is gebonden aan de widgetsjabloon `widget-runtime.html` (zie verder in dit artikel).
- De `initialize` methode (met deator) die wordt aangeroepen nadat de widget is gemaakt en alle parameters ervan zijn `OnMounted` toegewezen. Dit is een goede plaats om de te lezen en de `sessionNumber` API aan te roepen met behulp van de `HttpClient` . De `HttpClient` is een afhankelijkheid die wordt geïnjecteerd door de IoC-container (Inversion of Control).

- Eerst maakt de ontwikkelaarsportal de widget en wijst u alle parameters toe. Vervolgens wordt de methode `initialize` aanroepen.

    ```typescript
    ...
    import * as ko from "knockout";
    import { Component, RuntimeComponent, OnMounted, OnDestroyed, Param } from "@paperbits/common/ko/decorators";
    import { HttpClient, HttpRequest } from "@paperbits/common/http";
    ...

    export class WidgetRuntime {
        public readonly sessionDescription: ko.Observable<string>;

        constructor(private readonly httpClient: HttpClient) {
            ...
            this.sessionNumber = ko.observable();
            this.sessionDescription = ko.observable();
            ...
        }

        @Param()
        public readonly sessionNumber: ko.Observable<string>;

        @OnMounted()
        public async initialize(): Promise<void> {
            ...
            const sessionNumber = this.sessionNumber();

            const request: HttpRequest = {
                url: `https://conferenceapi.azurewebsites.net/session/${sessionNumber}`,
                method: "GET"
            };

            const response = await this.httpClient.send<string>(request);
            const sessionDescription = response.toText();
    
            this.sessionDescription(sessionDescription);
            ...
        }
        ...
    }
    ```

## <a name="tweak-the-widget-template"></a>De widgetsjabloon aanpassen

Werk uw widget bij om de sessiebeschrijving weer te geven.

Gebruik een alineatag en een `markdown` (of `text` ) binding in het bestand om de beschrijving weer te `ko/runtime/widget-runtime.html` geven:

```html
<p data-bind="markdown: sessionDescription"></p>
```

## <a name="add-the-widget-editor"></a>De widgeteditor toevoegen

De widget is nu geconfigureerd om de beschrijving van de sessie op te `107` halen. U hebt `107` in de code opgegeven als de standaardsessie. Als u wilt controleren of u alles goed hebt gedaan, moet u uitvoeren `npm start` en controleren of in de ontwikkelaarsportal de beschrijving op de pagina wordt weergegeven.

Voer nu deze stappen uit zodat de gebruiker de sessie-id kan instellen via een widgeteditor:

1. Werk het bestand `ko/widgetEditorViewModel.ts` bij:

    ```typescript
    export class WidgetEditor implements WidgetEditor<WidgetModel> {
        public readonly sessionNumber: ko.Observable<string>;

        constructor() {
            this.sessionNumber = ko.observable();
        }

        @Param()
        public model: WidgetModel;

        @Event()
        public onChange: (model: WidgetModel) => void;

        @OnMounted()
        public async initialize(): Promise<void> {
            this.sessionNumber(this.model.sessionNumber);
            this.sessionNumber.subscribe(this.applyChanges);
        }

        private applyChanges(): void {
            this.model.sessionNumber = this.sessionNumber();
            this.onChange(this.model);
        }
    }
    ```

    Het weergavemodel van de editor gebruikt dezelfde benadering die u eerder hebt gezien, maar er is een nieuwe eigenschap `onChange` , voorzien van `@Event()` . De callback wordt doorbekabeld om de listeners (in dit geval een inhoudseditor) op de hoogte te stellen van wijzigingen in het model.

1. Werk het bestand `ko/widgetEditorView.html` bij:

    ```html
    <input type="text" class="form-control" data-bind="textInput: sessionNumber" />
    ```

1. Voer `npm start` opnieuw uit. U moet in de `sessionNumber` widgeteditor kunnen wijzigen. Wijzig de id in `108` , sla de wijzigingen op en vernieuw het tabblad van de browser. Als u problemen ondervindt, moet u de widget mogelijk opnieuw toevoegen aan de pagina.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-editor.png" alt-text="Schermopname van widgeteditor":::

## <a name="rename-the-widget"></a>De naam van de widget wijzigen

Wijzig de naam van de widget in het `constants.ts` bestand:

```typescript
...
export const widgetName = "conference-session";
export const widgetDisplayName = "Conference session";
...
```

> [!NOTE]
> Als u de widget aan de opslagplaats bijdraagt, moet de dezelfde naam hebben als de mapnaam en moet deze worden afgeleid van de weergavenaam (kleine letters en spaties vervangen door `widgetName` streepjes). De categorie moet `Community` blijven.

## <a name="next-steps"></a>Volgende stappen


Meer informatie over de ontwikkelaarsportal:

- [Overzicht van de Azure API Management-ontwikkelaarsportal](api-management-howto-developer-portal.md)

- [Widgets voor bijdragen:](developer-portal-widget-contribution-guidelines.md) we zijn welkom en stimuleren bijdragen van de community.

- Zie [Communitywidgets gebruiken](developer-portal-use-community-widgets.md) voor meer informatie over het gebruik van widgets die zijn bijgedragen door de community.
