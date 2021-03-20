---
title: Aan de slag met de Azure CDN SDK voor Node.js | Microsoft Docs
description: Meer informatie over het maken van een eenvoudige Node.js-console toepassing die laat zien hoe u het maken en beheren van Azure CDN profielen en eind punten kunt automatiseren.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.custom: devx-track-js
ms.openlocfilehash: f5d5c7a6e1f6993b19f38db2ae846b213a1d553e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95993362"
---
# <a name="get-started-with-azure-cdn-development"></a>Aan de slag met Azure CDN-ontwikkeling
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

U kunt de [Azure CDN SDK voor Node.js](https://www.npmjs.com/package/azure-arm-cdn) gebruiken om het maken en beheren van CDN-profielen en-eind punten te automatiseren.  In deze zelf studie wordt uitgelegd hoe u een eenvoudige Node.js-console toepassing maakt die verschillende van de beschik bare bewerkingen toont.  Deze zelf studie is niet bedoeld om alle aspecten van de Azure CDN SDK voor Node.js in detail te beschrijven.

Als u deze zelf studie wilt volt ooien, moet u [Node.js](https://www.nodejs.org) **4. x. x** of hoger al hebben geïnstalleerd en geconfigureerd.  U kunt elke gewenste tekst editor gebruiken om uw Node.js-toepassing te maken.  Als u deze zelf studie wilt schrijven, gebruikt u [Visual Studio code](https://code.visualstudio.com).  

> [!TIP]
> Het [voltooide project van deze zelf studie](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) is beschikbaar voor downloaden op MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Uw project maken en NPM-afhankelijkheden toevoegen
Nu we een resource groep voor onze CDN-profielen hebben gemaakt en u de machtiging van de Azure AD-toepassing hebt gegeven voor het beheren van CDN-profielen en eind punten binnen die groep, kunnen we de toepassing gaan maken.

Maak een map voor het opslaan van uw toepassing.  Vanuit een-console met de Node.js-hulpprogram ma's in uw huidige pad, stelt u uw huidige locatie in op deze nieuwe map en initialiseert u het project door het volgende uit te voeren:

```console
npm init
```

Vervolgens wordt er een reeks vragen weer gegeven om uw project te initialiseren.  Voor **ingangs punt** gebruikt deze zelf studie *app.js*.  In het volgende voor beeld ziet u de andere opties.

![NPM init-uitvoer](./media/cdn-app-dev-node/cdn-npm-init.png)

Ons project is nu geïnitialiseerd met een *packages.jsin* het bestand.  Ons project gaat gebruikmaken van enkele Azure-bibliotheken die zijn opgenomen in NPM-pakketten.  We gebruiken Azure client runtime for Node.js (MS-rest-Azure) en de Azure CDN-client bibliotheek voor Node.js (Azure-arm-cd).  Laten we die als afhankelijkheden toevoegen aan het project.

```console
npm install --save ms-rest-azure
npm install --save azure-arm-cdn
```

Nadat de installatie van de pakketten is voltooid, moet de *package.jsin* het bestand er ongeveer als volgt uitzien (versie nummers kunnen verschillen):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Maak ten slotte een leeg tekst bestand met behulp van de tekst editor en sla het op in de hoofdmap van de projectmap als *app.js*.  U kunt nu beginnen met het schrijven van code.

## <a name="requires-constants-authentication-and-structure"></a>Vereist, constanten, verificatie en structuur
Als *app.js* in onze editor hebt geopend, krijgen we de basis structuur van ons programma dat is geschreven.

1. Voeg aan het begin de vereiste voor onze NPM-pakketten toe met het volgende:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. We moeten enkele constanten definiëren die door de methoden worden gebruikt.  Voeg het volgende toe.  Zorg ervoor dat u de tijdelijke aanduidingen, inclusief de **&lt; punt haken &gt;**, vervangt door uw eigen waarden, indien nodig.
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Vervolgens wordt de CDN-beheer client geïnstantieerd en worden deze referenties verstrekt.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Als u afzonderlijke gebruikers verificatie gebruikt, zien deze twee regels er iets anders uit.
   
   > [!IMPORTANT]
   > Gebruik dit code voorbeeld alleen als u ervoor kiest om afzonderlijke gebruikers verificatie te hebben in plaats van een service-principal.  Zorg ervoor dat u uw individuele gebruikers referenties beschermt en geheim blijft.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Zorg ervoor dat u de items tussen **&lt; punt haken &gt;** vervangt door de juiste gegevens.  Voor `<redirect URI>` gebruikt u de omleidings-URI die u hebt ingevoerd bij het registreren van de toepassing in azure AD.
4. In onze Node.js-console toepassing worden enkele opdracht regel parameters toegepast.  Laten we eens controleren of er ten minste één para meter is door gegeven.
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. Dit brengt ons naar het hoofd gedeelte van ons programma, waar we voor andere functies vertakkingen op basis van welke para meters zijn door gegeven.
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. Op verschillende plaatsen in ons programma moeten we ervoor zorgen dat het juiste aantal para meters is door gegeven en dat er een aantal Help-informatie wordt weer gegeven als ze niet correct zijn.  U kunt hiervoor functies maken.
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. Ten slotte zijn de functies die we op de CDN-beheer-client gebruiken, asynchroon, zodat ze een methode nodig hebben om terug te bellen wanneer ze klaar zijn.  We maken er een die de uitvoer van de CDN-beheer client kan weer geven (indien van toepassing) en het programma op de juiste manier afsluiten.
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Nu de basis structuur van ons programma is geschreven, moeten we de functies maken die worden aangeroepen op basis van de para meters.

## <a name="list-cdn-profiles-and-endpoints"></a>CDN-profielen en eind punten weer geven
Laten we beginnen met code om onze bestaande profielen en eind punten weer te geven.  Mijn opmerkingen over de code bieden de verwachte syntaxis zodat we weten waar elke para meter mee gaat.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>CDN-profielen en-eind punten maken
Vervolgens schrijven we de functies voor het maken van profielen en eind punten.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>Een eind punt leegmaken
Ervan uitgaande dat het eind punt is gemaakt, kan een veelvoorkomende taak die we in ons programma willen uitvoeren, inhoud in ons eind punt verwijderen.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN-profielen en eind punten verwijderen
De laatste functie bevat de eind punten en profielen.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Het programma uitvoeren
We kunnen ons Node.js-programma nu uitvoeren met onze favoriete fout opsporing of via de-console.

> [!TIP]
> Als u Visual Studio code gebruikt als het fout opsporingsprogramma, moet u uw omgeving instellen op het door geven van de opdracht regel parameters.  Visual Studio code doet dit in de **launch.js** in het bestand.  Zoek naar een eigenschap met de naam **args** en voeg een matrix van teken reeks waarden toe voor uw para meters, zodat deze er ongeveer als volgt uitziet:  `"args": ["list", "profiles"]` .
> 
> 

Laten we eerst onze profielen vermelden.

![Profielen weer geven](./media/cdn-app-dev-node/cdn-list-profiles.png)

Er is een lege matrix geretourneerd.  Omdat we geen profielen hebben in de resource groep, wordt dat verwacht.  We gaan nu een profiel maken.

![Profiel maken](./media/cdn-app-dev-node/cdn-create-profile.png)

Nu gaan we een eind punt toevoegen.

![Eind punt maken](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Ten slotte gaan we ons profiel verwijderen.

![Profiel verwijderen](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Volgende stappen
[Down load het voor beeld](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74)om het voltooide project in dit overzicht te bekijken.

Als u de referentie voor de Azure CDN SDK voor Node.js wilt weer geven, bekijkt u de [verwijzing](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Raadpleeg de [volledige naslag informatie](https://azure.github.io/azure-sdk-for-node/)voor aanvullende documentatie over de Azure SDK voor Node.js.

Uw CDN-resources beheren met [Power shell](cdn-manage-powershell.md).

