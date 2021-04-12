---
title: 'Zelf studie: toegang tot gegevens in Cosmos DB met behulp van Mongoose met statische Azure-Web Apps'
description: Leer hoe u met Mongoose toegang krijgt tot gegevens in Cosmos DB met behulp van een statische Web Apps API-functie van Azure.
author: GeekTrainer
ms.author: chrhar
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: f64cc67ad6f0296ad289d858795ee783943f3daf
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107260061"
---
# <a name="tutorial-access-data-in-cosmos-db-using-mongoose-with-azure-static-web-apps"></a>Zelf studie: toegang tot gegevens in Cosmos DB met behulp van Mongoose met statische Azure-Web Apps

[Mongoose](https://mongoosejs.com/) is de populairste ODM-client (object document mapping) voor Node.js. Als u een gegevens structuur wilt ontwerpen en validatie wilt afdwingen, biedt Mongoose alle hulp middelen die nodig zijn om te communiceren met data bases die ondersteuning bieden voor de Mongoose-API. [Cosmos DB](../cosmos-db/mongodb-introduction.md) ondersteunt de benodigde Mongoose-api's en is beschikbaar als een back-endserver-Server optie in Azure.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Een Cosmos DB server zonder account maken
> - Statische Azure-Web Apps maken
> - Toepassings instellingen bijwerken om de connection string op te slaan

Als u geen Azure-abonnement hebt, kunt u een [gratis proef account](https://azure.microsoft.com/free/)maken.

## <a name="prerequisites"></a>Vereisten

- Een [Azure-account](https://azure.microsoft.com/free/)
- Een [GitHub](https://github.com/join)-account

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-cosmos-db-serverless-database"></a>Een Cosmos DB serverloze data base maken

Maak eerst een [Cosmos DB server](https://docs.microsoft.com/azure/cosmos-db/serverless) zonder account. Als u een serverloze account gebruikt, betaalt u alleen voor de resources die worden gebruikt en hoeft u geen volledige infra structuur te maken.

1. Ga naar [https://portal.azure.com](https://portal.azure.com)
2. Klik op **een resource maken**
3. Voer **Azure Cosmos DB** in het zoekvak in
4. Klik op **Azure Cosmos DB**
5. Klik op **Maken**.
6. Configureer uw Azure Cosmos DB-account met de volgende informatie
    - Abonnement: Kies het abonnement dat u wilt gebruiken
    - Resource: Klik op **nieuwe maken** en stel de naam in op **ASWA-Mongoose**
    - Account naam: een unieke waarde is vereist
    - API: **Azure Cosmos DB voor MongoDb-API**
    - Notebooks (preview): **uit**
    - Locatie: **VS-West 2**
    - Capaciteits modus: **serverloos (preview-versie)**
    - Versie: **3,6**
    - Beschikbaarheidszones:  
 :::image type="content" source="media/add-mongoose/cosmos-db.png" alt-text="nieuwe Cosmos DB instantie maken"::: uitschakelen
7. Klik op **beoordeling + maken**
8. Klik op **Maken**.

Het maken van het proces duurt enkele minuten. Later gaat u terug naar de data base om de connection string te verzamelen.

## <a name="create-a-static-web-app"></a>Statische web-app maken

In deze zelf studie wordt gebruikgemaakt van een opslag plaats voor GitHub om u te helpen bij het maken van uw toepassing.

1. Ga naar de [Start sjabloon](https://github.com/login?return_to=/staticwebdev/mongoose-starter/generate)
2. De **eigenaar** kiezen (als u een andere organisatie dan uw hoofd account gebruikt)
3. Uw opslag plaats een naam **ASWA-Mongoose-zelf studie**
4. Klik op **opslag plaats maken op basis van sjabloon**
5. Terug naar de [Azure Portal](https://portal.azure.com)
6. Klik op **een resource maken**
7. **Statische web-apps** in het zoekvak typen
8. **Statische web-app selecteren (preview-versie)**
9. Klik op **Maken**.
10. Configureer uw Azure statische web-app met de volgende informatie
    - Abonnement: hetzelfde abonnement kiezen als voorheen
    - Resource groep: Selecteer **ASWA-Mongoose**
    - Naam: **ASWA-Mongoose-zelf studie**
    - Regio: **VS-West 2**
    - Klik op **Aanmelden met github**
    - Klik op **autoriseren** als u wordt gevraagd om de GitHub-actie te laten maken door Azure static web apps om implementatie in te scha kelen
    - Organisatie: uw account naam
    - Opslag plaats: **ASWA-Mongoose-zelf studie**
    - Vertakking: **hoofd**
    - Presets bouwen: **aangepaste** kiezen
    - App-locatie: **/Public**
    - API-locatie: **API**
    - Locatie van het app-artefact: *laat leeg* 
     :::image type="content" source="media/add-mongoose/azure-static-web-apps.png" alt-text="voltooide Azure static web apps formulier":::
11. Klik op **controleren en maken**
12. Klik op **Maken**.

Het maken van het proces duurt enkele minuten, maar u kunt ook klikken op de **resource gaan** nadat de app is ingericht.

## <a name="configure-database-connection-string"></a>De databaseverbindingsreeks configureren

Als u wilt dat de web-app kan communiceren met de data base, wordt de data base connection string opgeslagen als een toepassings instelling. Instellings waarden zijn toegankelijk in Node.js met behulp van het- `process.env` object.

1. Klik op **Start** in de linkerbovenhoek van de Azure Portal (of ga terug naar [https://portal.azure.com](https://portal.azure.com) )
2. Klik op **resource groepen**
3. Klik op **ASWA-Mongoose**
4. Klik op de naam van het database account. het heeft een type **Azure Cosmos DB account**
5. Klik onder **instellingen** op **verbindings reeks**
6. De connection string kopiëren die worden weer gegeven onder de **primaire VERBINDINGS reeks**
7. Klik in de brood kruimels op **ASWA-Mongoose**
8. Klik op **ASWA-Mongoose-zelf studie** om terug te keren naar het website-exemplaar
9. Klik onder **instellingen** op **configuratie**
10. Klik op **toevoegen** en maak een nieuwe toepassings instelling met de volgende waarden
    - Naam: **CONNECTION_STRING**
    - Waarde: plak de connection string die u eerder hebt gekopieerd
11. Klik op **OK**
12. Klik op **Opslaan**.

## <a name="navigate-to-your-site"></a>Navigeer naar uw site

U kunt nu de statische web-app verkennen.

1. Klik op **overzicht**
1. Klik op de URL die in de rechter bovenhoek wordt weer gegeven
    1. Deze ziet er ongeveer uit als `https://calm-pond-05fcdb.azurestaticapps.net`
1. Maak een nieuwe taak door een titel te typen en op **taak toevoegen** te klikken.
1. Bevestigen dat de taak wordt weer gegeven (dit kan even duren)
1. Markeer de taak als voltooid door **te klikken op het selectie vakje**
1. **Vernieuw de pagina** om te bevestigen dat een Data Base wordt gebruikt

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijder dan de resourcegroep met de volgende stappen:

1. Terug naar de [Azure Portal](https://portal.azure.com)
2. Klik op **resource groepen**
3. Klik op **ASWA-Mongoose**
4. Klik op **resource groep verwijderen**
5. Typ **ASWA-Mongoose** in het tekstvak
6. Klik op **Verwijderen**

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het configureren van lokale ontwikkeling...
> [!div class="nextstepaction"]
> [Lokale ontwikkeling instellen](./local-development.md)
 
