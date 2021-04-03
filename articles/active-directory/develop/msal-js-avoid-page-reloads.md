---
title: Pagina opnieuw laden (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het voor komen van het opnieuw laden van pagina's bij het op de achtergrond ophalen en vernieuwen van tokens met behulp van de micro soft-verificatie bibliotheek voor Java script (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 0c96d161e55261af1bbe04eae6ead1d245158d02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064824"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Voor komen dat pagina's opnieuw worden geladen bij het terughalen en vernieuwen van tokens op de achtergrond met behulp van MSAL.js
In de micro soft-verificatie bibliotheek voor Java script (MSAL.js) worden verborgen `iframe` elementen gebruikt voor het op de achtergrond verkrijgen en vernieuwen van tokens. Azure AD retourneert het token terug naar de geregistreerde redirect_uri die in de token aanvraag is opgegeven (standaard is dit de hoofd pagina van de app). Omdat het antwoord een 302 is, resulteert dit in de HTML-code die overeenkomt met de `redirect_uri` geladen in de `iframe` . Normaal gesp roken is de app `redirect_uri` de hoofd pagina, waardoor deze opnieuw wordt geladen.

Als er in andere gevallen verificatie is vereist voor het navigeren naar de hoofd pagina van de app, kan dit leiden tot geneste `iframe` elementen of `X-Frame-Options: deny` fouten.

Omdat MSAL.js de 302 die is uitgegeven door Azure AD niet kan negeren en is vereist voor het verwerken van het geretourneerde token, kan niet worden voor komen dat het wordt `redirect_uri` geladen in de `iframe` .

Volg deze tijdelijke oplossingen om te voor komen dat de volledige app opnieuw wordt geladen of andere fouten die zijn veroorzaakt door dit probleem.

## <a name="specify-different-html-for-the-iframe"></a>Geef een andere HTML-code voor het iframe op

Stel de `redirect_uri` eigenschap in op de configuratie van een eenvoudige pagina waarvoor geen verificatie is vereist. U moet ervoor zorgen dat deze overeenkomt met de `redirect_uri` registratie in azure Portal. Dit heeft geen invloed op de aanmeldings ervaring van de gebruiker, omdat MSAL de start pagina opslaat wanneer het aanmeldings proces door de gebruiker wordt gestart en teruggeleid naar de exacte locatie nadat de aanmelding is voltooid.

## <a name="initialization-in-your-main-app-file"></a>Initialisatie in het hoofd bestand van de app

Als uw app zodanig is gestructureerd dat er één centraal Java script-bestand is dat de initialisatie, route ring en andere zaken van de app definieert, kunt u uw app-modules voorwaardelijk laden op basis van het feit of de app in een `iframe` of niet wordt geladen. Bijvoorbeeld:

In AngularJS: app.js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

In hoek: app. module. TS

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

MsalComponent:

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het bouwen van een toepassing met één pagina](scenario-spa-overview.md) met behulp van MSAL.js.