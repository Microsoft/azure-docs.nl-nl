---
title: CRUD-functies toevoegen aan een Angular-app met de API voor MongoDB van Azure Cosmos DB
description: Deel 6 van de serie zelfstudies voor het maken van een MongoDB-app met Angular en Node op Azure Cosmos DB, waarbij gebruik wordt gemaakt van dezelfde API's als voor MongoDB
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: c8e2c707566b08219b495e76be7f6f6130d876ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93081311"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---add-crud-functions-to-the-app"></a>Een Angular-app maken met de API voor MongoDB van Azure Cosmos DB - CRUD-functies toevoegen aan de app
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In deze meerdelige zelfstudie leert u hoe u een app maakt die is geschreven in Node.js met Express en Angular, en hoe u deze app verbindt met uw [Cosmos-account dat is geconfigureerd met de API van Cosmos DB voor MongoDB](mongodb-introduction.md). Deel 6 van de zelfstudie bouwt voort op [deel 5](tutorial-develop-mongodb-nodejs-part5.md) en beschrijft de volgende taken:

> [!div class="checklist"]
> * De functies Post, Put en Delete voor de hero-service maken
> * De app uitvoeren

> [!VIDEO https://www.youtube.com/embed/Y5mdAlFGZjc]

## <a name="prerequisites"></a>Vereisten

Voltooi de stappen in [deel 5](tutorial-develop-mongodb-nodejs-part5.md) van de zelfstudie voordat u aan dit deel begint.

> [!TIP]
> Aan de hand van deze zelfstudie wordt u stapsgewijs begeleid bij het maken van de toepassing. Als u het voltooide project wilt downloaden, kunt u de voltooide app vinden in de [angular-cosmosdb-opslagplaats](https://github.com/Azure-Samples/angular-cosmosdb) op GitHub.

## <a name="add-a-post-function-to-the-hero-service"></a>Een Post-functie aan de hero-service toevoegen

1. In Visual Studio Code opent u **routes.js** en **hero.service.js** naast elkaar door op de knop **Split Editor** te drukken :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/split-editor-button.png":::.

    U ziet dat regel 7 van routes.js de functie `getHeroes` aanroept in regel 5 van **hero.service.js**.  Voor de functies Post, Put en Delete moet dezelfde koppeling worden gemaakt. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/routes-heroservicejs.png" alt-text="routes.js and hero.service.js in Visual Studio Code":::
    
    We beginnen met het invoeren van code in de hero-service. 

2. Kopieer na de functie `getHeroes` en vóór `module.exports` de volgende code in **hero.service.js**. Deze code:  
   * Maakt gebruik van een hero-model voor het posten van een nieuwe hero.
   * Controleert de responsen om te kijken of er fouten zijn, en retourneert de statuswaarde 500.

   ```javascript
   function postHero(req, res) {
     const originalHero = { uid: req.body.uid, name: req.body.name, saying: req.body.saying };
     const hero = new Hero(originalHero);
     hero.save(error => {
       if (checkServerError(res, error)) return;
       res.status(201).json(hero);
       console.log('Hero created successfully!');
     });
   }

   function checkServerError(res, error) {
     if (error) {
       res.status(500).send(error);
       return error;
     }
   }
   ```

3. Werk in **hero.service.js** de `module.exports` bij zodat de nieuwe functie `postHero` is opgenomen. 

    ```javascript
    module.exports = {
      getHeroes,
      postHero
    };
    ```

4. Voeg in **routes.js** na de `get`-router een router toe voor de functie `post`. Met deze router wordt één hero per keer gepost. Door het routerbestand op deze manier te structureren, kunt u duidelijk alle beschikbare API-eindpunten bekijken, terwijl het echte werk aan het bestand **hero.service.js** wordt overgelaten.

    ```javascript
    router.post('/hero', (req, res) => {
      heroService.postHero(req, res);
    });
    ```

5. Controleer of alles werkt door de app uit te voeren. Sla al uw wijzigingen in Visual Studio Code op, selecteer de knop **Debug** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/debug-button.png"::: aan de linkerkant en selecteer vervolgens de knop **Start Debugging** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/start-debugging-button.png":::.

6. Ga terug naar de internetbrowser en open het tabblad Netwerk in de hulpmiddelen voor ontwikkelaars door op F12 te drukken (op de meeste computers). Ga naar `http://localhost:3000` om de aanroepen te bekijken die via het netwerk zijn gedaan.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/add-new-hero.png" alt-text="Tabblad Netwerk in Chrome met een overzicht van de netwerkactiviteit":::

7. Voeg een nieuwe hero toe door de knop **Add New Hero** te selecteren. Voer de id '999' in, de naam 'Fred' en de uitspraak 'Hello'. Selecteer vervolgens **Opslaan**. Op het tabblad Netwerk zou u een POST-aanvraag voor een nieuwe hero moeten zien. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/post-new-hero.png" alt-text="Tabblad Netwerk in Chrome met de netwerkactiviteit voor de functies Get en Post":::

    We gaan terug en voegen de functies Put en Delete aan de app toe.

## <a name="add-the-put-and-delete-functions"></a>De functies Put en Delete toevoegen

1. Voer in **routes.js** na de post-router de `put`- en `delete`-router toe.

    ```javascript
    router.put('/hero/:uid', (req, res) => {
      heroService.putHero(req, res);
    });

    router.delete('/hero/:uid', (req, res) => {
      heroService.deleteHero(req, res);
    });
    ```

2. Kopieer na de functie `checkServerError` de volgende code in **hero.service.js**. Deze code:
   * Maakt de functies `put` en `delete`
   * Controleert of de hero is gevonden
   * Handelt fouten af 

   ```javascript
   function putHero(req, res) {
     const originalHero = {
       uid: parseInt(req.params.uid, 10),
       name: req.body.name,
       saying: req.body.saying
     };
     Hero.findOne({ uid: originalHero.uid }, (error, hero) => {
       if (checkServerError(res, error)) return;
       if (!checkFound(res, hero)) return;

      hero.name = originalHero.name;
       hero.saying = originalHero.saying;
       hero.save(error => {
         if (checkServerError(res, error)) return;
         res.status(200).json(hero);
         console.log('Hero updated successfully!');
       });
     });
   }

   function deleteHero(req, res) {
     const uid = parseInt(req.params.uid, 10);
     Hero.findOneAndRemove({ uid: uid })
       .then(hero => {
         if (!checkFound(res, hero)) return;
         res.status(200).json(hero);
         console.log('Hero deleted successfully!');
       })
       .catch(error => {
         if (checkServerError(res, error)) return;
       });
   }

   function checkFound(res, hero) {
     if (!hero) {
       res.status(404).send('Hero not found.');
       return;
     }
     return hero;
   }
   ```

3. Exporteer de nieuwe modules in **hero.service.js**:

   ```javascript
    module.exports = {
      getHeroes,
      postHero,
      putHero,
      deleteHero
    };
    ```

4. De code is nu bijgewerkt. Selecteer de knop **Restart** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/restart-debugger-button.png"::: in Visual Studio Code.

5. Vernieuw de pagina in uw internetbrowser en selecteer de knop **Add New Hero**. Voeg een nieuwe hero toe met de id '9', de naam 'Starlord' en de uitspraak 'Hi’. Selecteer de knop **Opslaan** om de nieuwe hero op te slaan.

6. Selecteer de hero **Starlord** en wijzig de uitspraak 'Hi' in 'Bye'. Selecteer vervolgens de knop **Opslaan**. 

    U kunt nu de id selecteren op het tabblad Network om de payload weer te geven. In de payload kunt u zien dat de uitspraak nu is ingesteld op 'Bye'.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/put-hero-function.png" alt-text="De app Heroes en het tabblad Netwerk met de payload"::: 

    U kunt ook een van de hero's in de gebruikersinterface verwijderen en zien hoe lang het duurt om de bewerking Verwijderen te voltooien. Probeer dit uit door de knop Verwijderen voor de hero met de naam Fred te selecteren.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/times.png" alt-text="De app Heroes en het tabblad Netwerk met de tijdsduur voor het voltooien van de functies"::: 

    Als u de pagina vernieuwt, wordt op het tabblad Netwerk weergegeven hoelang het duurt voor de hero's zijn opgehaald. Deze tijden zijn kort, maar het hangt ervan af waar ter wereld uw gegevens zich bevinden en tevens van de mogelijkheid hierop geo-replicatie uit te voeren in een gebied dat in de buurt van uw gebruikers is. Meer informatie over geo-replicatie vindt u in de volgende zelfstudie, die binnenkort wordt uitgebracht.

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * De functies Post, Put en Delete aan de app toegevoegd 

Kom hier snel terug voor aanvullende video's in deze serie zelfstudies.

