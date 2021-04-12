---
title: De UI voor Angular-app maken met de API van Azure Cosmos DB voor MongoDB (deel 3)
description: Deel 3 van de serie zelfstudies voor het maken van een MongoDB-app met Angular en Node op Azure Cosmos DB, waarbij gebruik wordt gemaakt van dezelfde API's als voor MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: 181af5cf26d19a9f51e8d456e777badf7efa224d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097818"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---build-the-ui-with-angular"></a>Een Angular-app maken met de API voor MongoDB van Azure Cosmos DB - De gebruikersinterface maken met Angular
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In deze meerdelige zelfstudie leert u hoe u een app maakt die is geschreven in Node.js met Express en Angular, en hoe u deze app verbindt met uw [Cosmos-account dat is geconfigureerd met de API van Cosmos DB voor MongoDB](mongodb-introduction.md).

Deel 3 van de zelfstudie bouwt voort op [deel 2](tutorial-develop-mongodb-nodejs-part2.md) en beschrijft de volgende taken:

> [!div class="checklist"]
> * De Angular-gebruikersinterface bouwen
> * CSS gebruiken voor het instellen van het uiterlijk
> * De app lokaal testen

## <a name="video-walkthrough"></a>Video-overzicht

> [!VIDEO https://www.youtube.com/embed/MnxHuqcJVoM]

## <a name="prerequisites"></a>Vereisten

Voltooi de stappen in [deel 2](tutorial-develop-mongodb-nodejs-part2.md) van de zelfstudie voordat u aan dit deel begint.

> [!TIP]
> Aan de hand van deze zelfstudie wordt u stapsgewijs begeleid bij het maken van de app. Als u het voltooide project wilt downloaden, kunt u de voltooide app vinden in de [angular-cosmosdb-opslagplaats](https://github.com/Azure-Samples/angular-cosmosdb) op GitHub.

## <a name="build-the-ui"></a>De gebruikersinterface bouwen

1. Klik in Visual Studio Code op de knop Stop :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part3/stop-button.png"::: om de app Node te stoppen. 

2. Voer in de Windows-opdrachtprompt of in het Mac-terminalvenster de volgende opdracht in om een heroes-component te genereren. In deze code is g=genereren, c=component en heroes=naam van de component, en er wordt een platte bestandsstructuur (--flat) gebruikt zodat er geen submap wordt gemaakt.

    ```
    ng g c heroes --flat 
    ```

    In het terminalvenster wordt een bevestiging getoond van de nieuwe componenten.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/install-heros-component.png" alt-text="Hero-component installeren":::

    Laten we eens kijken naar de bestanden die zijn gemaakt en bijgewerkt. 

3. Ga in Visual Studio Code in het deelvenster **Explorer** naar de nieuwe map **src\app** en open het nieuwe bestand **heroes.component.ts** dat is gegenereerd in de app-map. Dit TypeScript-componentbestand is met de vorige opdracht gemaakt.

    > [!TIP]
    > Als in de app-map geen Visual Studio Code wordt weergegeven, voert u op de Mac Command+Shift+P in, en in Windows Ctrl+Shift+P om het opdrachtpalet te openen. Typ *Reload Window* om de systeemwijziging van kracht te laten worden.

4. Open in dezelfde map het bestand **app.module.ts**. U ziet dat `HeroesComponent` aan de declaraties in regel 5 is toegevoegd en dat het ook in regel 10 is geïmporteerd.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/app-module-file.png" alt-text="Het bestand app-module.ts openen":::

5. Ga terug naar het bestand **heroes.component.html** en kopieer er de volgende code in. `<div>` is de container voor de hele pagina. In de container bevindt zich een lijst met hero's die we moeten maken, zodat als u op een ervan klikt, u deze kunt selecteren en in de gebruikersinterface bewerken of verwijderen. In de HTML staat de stijl, zodat u weet welke is geselecteerd. Er is ook een bewerkingsgebied, waar u een nieuwe hero kunt toevoegen of een bestaande hero kunt bewerken. 

    ```html
    <div>
      <ul class="heroes">
        <li *ngFor="let hero of heroes" (click)="onSelect(hero)" [class.selected]="hero === selectedHero">
          <button class="delete-button" (click)="deleteHero(hero)">Delete</button>
          <div class="hero-element">
            <div class="badge">{{hero.id}}</div>
            <div class="name">{{hero.name}}</div>
            <div class="saying">{{hero.saying}}</div>
          </div>
        </li>
      </ul>
      <div class="editarea">
        <button (click)="enableAddMode()">Add New Hero</button>
        <div *ngIf="selectedHero">
          <div class="editfields">
            <div>
              <label>id: </label>
              <input [(ngModel)]="selectedHero.id" placeholder="id" *ngIf="addingHero" />
              <label *ngIf="!addingHero" class="value">{{selectedHero.id}}</label>
            </div>
            <div>
              <label>name: </label>
              <input [(ngModel)]="selectedHero.name" placeholder="name" />
            </div>
            <div>
              <label>saying: </label>
              <input [(ngModel)]="selectedHero.saying" placeholder="saying" />
            </div>
          </div>
          <button (click)="cancel()">Cancel</button>
          <button (click)="save()">Save</button>
        </div>
      </div>
    </div>
    ```

7. Met de HTML op de juiste plaats voegen we deze toe aan het bestand **heroes.component.ts**, waarna we interactie kunnen aangaan met de sjabloon. Met de volgende code wordt de sjabloon aan het componentbestand toegevoegd. Er is een constructor toegevoegd waarmee enkele hero's worden opgehaald en de hero-servicecomponent wordt geïnitialiseerd om alle gegevens op te halen. De code voegt ook alle vereiste methoden toe voor het afhandelen van gebeurtenissen in de gebruikersinterface. U kunt de volgende code over de bestaande code in **heroes.component.ts** heen kopiëren. U ziet nu waarschijnlijk fouten in de gebieden Hero en HeroService omdat de bijbehorende componenten nog niet zijn geïmporteerd. U lost deze fouten op in de volgende sectie. 

    ```ts
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-heroes',
      templateUrl: './heroes.component.html',
      styleUrls: ['./heroes.component.scss']
    })
    export class HeroesComponent implements OnInit {
      addingHero = false;
      heroes: any = [];
      selectedHero: Hero;
    
      constructor(private heroService: HeroService) {}
    
      ngOnInit() {
       this.getHeroes();
      }

      cancel() {
        this.addingHero = false;
        this.selectedHero = null;
      }

      deleteHero(hero: Hero) {
        this.heroService.deleteHero(hero).subscribe(res => {
          this.heroes = this.heroes.filter(h => h !== hero);
          if (this.selectedHero === hero) {
            this.selectedHero = null;
          }
        });
      }

      getHeroes() {
        return this.heroService.getHeroes().subscribe(heroes => {
          this.heroes = heroes;
        });
      }

      enableAddMode() {
        this.addingHero = true;
        this.selectedHero = new Hero();
      }

      onSelect(hero: Hero) {
        this.addingHero = false;
        this.selectedHero = hero;
      }

      save() {
        if (this.addingHero) {
          this.heroService.addHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
            this.heroes.push(hero);
          });
        } else {
          this.heroService.updateHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
          });
        }
      }
    }
    ```

8. Open in **Explorer** het bestand **app/app.module.ts** en werk de importsectie bij om een import voor een `FormsModule` toe te voegen. De importsectie moet er nu als volgt uitzien:

    ```
    imports: [
      BrowserModule,
      FormsModule
    ],
    ```

9. Voeg in het bestand **app/app.module.ts** een import toe voor de nieuwe module FormsModule op regel 3. 

    ```
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    import { FormsModule } from '@angular/forms';
    ```

## <a name="use-css-to-set-the-look-and-feel"></a>CSS gebruiken voor het instellen van het uiterlijk

1. Open in het deelvenster Explorer het bestand **src/styles.scss**.

2. Kopieer de volgende code in het bestand **styles.scss**, waarmee u de bestaande inhoud van het bestand vervangt.

    ```css
    /* You can add global styles to this file, and also import other style files */

    * {
      font-family: Arial;
    }
    h2 {
      color: #444;
      font-weight: lighter;
    }
    body {
      margin: 2em;
    }
    
    body,
    input[text],
    button {
      color: #888;
      // font-family: Cambria, Georgia;
    }
    button {
      font-size: 14px;
      font-family: Arial;
      background-color: #eee;
      border: none;
      padding: 5px 10px;
      border-radius: 4px;
      cursor: pointer;
      cursor: hand;
      &:hover {
        background-color: #cfd8dc;
      }
      &.delete-button {
        float: right;
        background-color: gray !important;
        background-color: rgb(216, 59, 1) !important;
        color: white;
        padding: 4px;
        position: relative;
        font-size: 12px;
      }
    }
    div {
      margin: .1em;
    }

    .selected {
      background-color: #cfd8dc !important;
      background-color: rgb(0, 120, 215) !important;
      color: white;
    }

    .heroes {
      float: left;
      margin: 0 0 2em 0;
      list-style-type: none;
      padding: 0;
      li {
        cursor: pointer;
        position: relative;
        left: 0;
        background-color: #eee;
        margin: .5em;
        padding: .5em;
        height: 3.0em;
        border-radius: 4px;
        width: 17em;
        &:hover {
          color: #607d8b;
          color: rgb(0, 120, 215);
          background-color: #ddd;
          left: .1em;
        }
        &.selected:hover {
          /*background-color: #BBD8DC !important;*/
          color: white;
        }
      }
      .text {
        position: relative;
        top: -3px;
      }
      .saying {
        margin: 5px 0;
      }
      .name {
        font-weight: bold;
      }
      .badge {
        /* display: inline-block; */
        float: left;
        font-size: small;
        color: white;
        padding: 0.7em 0.7em 0 0.5em;
        background-color: #607d8b;
        background-color: rgb(0, 120, 215);
        background-color:rgb(134, 183, 221);
        line-height: 1em;
        position: relative;
        left: -1px;
        top: -4px;
        height: 3.0em;
        margin-right: .8em;
        border-radius: 4px 0 0 4px;
        width: 1.2em;
      }
    }

    .header-bar {
      background-color: rgb(0, 120, 215);
      height: 4px;
      margin-top: 10px;
      margin-bottom: 10px;
    }

    label {
      display: inline-block;
      width: 4em;
      margin: .5em 0;
      color: #888;
      &.value {
        margin-left: 10px;
        font-size: 14px;
      }
    }

    input {
      height: 2em;
      font-size: 1em;
      padding-left: .4em;
      &::placeholder {
          color: lightgray;
          font-weight: normal;
          font-size: 12px;
          letter-spacing: 3px;
      }
    }

    .editarea {
      float: left;
      input {
        margin: 4px;
        height: 20px;
        color: rgb(0, 120, 215);
      }
      button {
        margin: 8px;
      }
      .editfields {
        margin-left: 12px;
      }
    }
    ``` 
3. Sla het bestand op. 

## <a name="display-the-component"></a>De component weergeven

Hoe kunnen we de component nu op het scherm weergeven? We gaan de standaardcomponenten in **app.component.ts** wijzigen.

1. Open **/app/app.component.ts** in het deelvenster Explorer, wijzig de titel in Heroes en laat de naam van de component die we in **heroes.components.ts** (app-heroes) hebben gemaakt, verwijzen naar de nieuwe component. De inhoud van het bestand ziet er nu als volgt uit: 

    ```ts
    import { Component } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.scss'],
      template: `
      <h1>Heroes</h1>
      <div class="header-bar"></div>
      <app-heroes></app-heroes>
    `
    })
    export class AppComponent {
      title = 'app';
    }

    ```

2. **heroes.components.ts** bevat andere componenten waarnaar wordt verwezen, bijvoorbeeld de component Hero, dus die moeten we ook maken. Gebruik in de Angular CLI-opdrachtprompt de volgende opdracht om een hero-model en een bestand te maken met de naam **hero.ts**, waarin g=genereren, cl=klasse en hero=naam van de klasse.

    ```bash
    ng g cl hero
    ```

3. Open **src\app\hero.ts** in het deelvenster Explorer. Vervang in **hero.ts** de inhoud van het bestand door de volgende code, waardoor aan een Hero-klasse een id, een naam en een uitspraak wordt toegevoegd.

    ```ts
      export class Hero {
      id: number;
      name: string;
      saying: string;
    }
    ```

4. Ga terug naar **heroes.components.ts**. U ziet dat in regel `selectedHero: Hero;` (regel 10), `Hero` rood is onderlijnd. 

5. Klik op de term `Hero` en links van het codeblok wordt een gloeilamppictogram weergegeven. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/light-bulb.png" alt-text="Gloeilamp in Visual Studio Code":::

6. Klik op de gloeilamp en klik op **Import Hero from "/app/hero".** of **Import Hero from "./hero".** (Het bericht dat u ziet, is afhankelijk van uw instellingen)

    In regel 2 verschijnt een nieuwe regel met code. Als regel 2 naar /app/hero verwijst, wijzigt u de regel zodat wordt verwezen naar het hero-bestand vanuit de lokale map (./hero). Regel 2 moet er als volgt uitzien:

   ```
   import { Hero } from "./hero";
   ``` 

    Met het model zijn we nu klaar, maar nu moeten we de service nog maken.

## <a name="create-the-service"></a>De service maken

1. Voer na de opdrachtprompt in Angular CLI de volgende opdracht in om een hero-service te maken in **app.module.ts**, waarin g=genereren, s=service, hero=naam van de service, -m=invoeren in app.module.

    ```bash
    ng g s hero -m app.module
    ```

2. Ga in Visual Studio Code terug naar **heroes.components.ts**. In regel `constructor(private heroService: HeroService) {}` (regel 13) is `HeroService` rood onderlijnd. Klik op `HeroService` en u ziet links van het codeblok de gloeilamp verschijnen. Klik op de gloeilamp en vervolgens op **Import HeroService from "./hero.service ".** of op **Import HeroService from "/app/hero.service ".**

    Als u op de gloeilamp klikt, wordt een nieuwe regel met code aan regel 2 toegevoegd. Als regel 2 naar de map /app/hero.service verwijst, wijzigt u de regel zodat wordt verwezen naar het hero-bestand in de lokale map (./hero.service). Regel 2 moet er als volgt uitzien:
    
    ```javascript
    import { HeroService } from "./hero.service"
    ```

3. Open **hero.service.ts** in Visual Studio Code en kopieer er de volgende code in, waarmee de inhoud van het bestand wordt vervangen.

    ```ts
    import { Injectable } from '@angular/core';
    import { HttpClient } from '@angular/common/http';
    
    import { Hero } from './hero';
    
    const api = '/api';

    @Injectable()
    export class HeroService {
      constructor(private http: HttpClient) {}

      getHeroes() {
        return this.http.get<Array<Hero>>(`${api}/heroes`)
      }

      deleteHero(hero: Hero) {
        return this.http.delete(`${api}/hero/${hero.id}`);
      }

      addHero(hero: Hero) {
        return this.http.post<Hero>(`${api}/hero/`, hero);
      }

      updateHero(hero: Hero) {
        return this.http.put<Hero>(`${api}/hero/${hero.id}`, hero);
      }
    }
    ```

    Deze code maakt gebruik van de nieuwste versie van HttpClient van Angular, een module die u moet opgeven. Dat gaan we nu doen.

4. Open **app.module.ts** in Visual Studio Code en importeer HttpClientModule door de importsectie zodanig bij te werken dat HttpClientModule wordt opgenomen.

    ```ts
    imports: [
      BrowserModule,
      FormsModule,
      HttpClientModule
    ],
    ```

5. Voeg in **app.module.ts** de HttpClientModule-importeerinstructie toe aan de lijst met imports.

    ```ts
    import { HttpClientModule } from '@angular/common/http';
    ```

6. Sla alle bestanden op in Visual Studio Code.

## <a name="build-the-app"></a>De app bouwen

1. Voer achter de opdrachtprompt de volgende opdracht in om de Angular-app te bouwen. 

    ```bash
    ng b
    ``` 

    Als er problemen zijn, wordt er in het terminalvenster informatie over de bestanden weergegeven, zodat ze kunnen worden gerepareerd. Als u klaar bent met bouwen, worden de nieuwe bestanden opgeslagen in de map **dist**. U kunt de nieuwe bestanden desgewenst controleren in de map **dist**.

    Nu gaan we de app uitvoeren.

2. Klik in Visual Studio Code op de knop **Debug** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png"::: aan de linkerkant en klik vervolgens op de knop **Start Debugging** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part3/start-debugging-button.png":::.

3. Open vervolgens een internetbrowser en ga naar **localhost:3000** om te zien hoe de app lokaal wordt uitgevoerd.

     :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/azure-cosmos-db-mongodb-mean-app.png" alt-text="Hero-app wordt lokaal uitgevoerd":::

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * De Angular-gebruikersinterface gebouwd
> * De app lokaal getest

U kunt doorgaan met het volgende deel van de zelfstudie om een Azure Cosmos DB-account te maken.

> [!div class="nextstepaction"]
> [Een Azure Cosmos DB-account maken met de Azure CLI](tutorial-develop-mongodb-nodejs-part4.md)
