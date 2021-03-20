---
title: Azure FarmBeats-Api's
description: Meer informatie over Azure FarmBeats Api's, die agrarische bedrijven voorzien van een gestandaardiseerde, doorgestuurde interface met antwoorden op basis van JSON.
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 292374bcce90da042dc6bd672feaad3750759aa9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674503"
---
# <a name="azure-farmbeats-apis"></a>Azure FarmBeats-Api's

In dit artikel worden de Azure FarmBeats-Api's beschreven. De Azure FarmBeats-Api's bieden agrarische ondernemingen een gestandaardiseerde, doorgestuurde interface met antwoorden op basis van JSON om u te helpen profiteren van de mogelijkheden van Azure FarmBeats, zoals:

- Api's voor het verkrijgen van sensor-, camera-, Drone-, weers-, satelliet-en geaard-gegevens.
- Normalisatie en contextualization van gegevens over common data providers.
- Geschematiseerde-toegang en query mogelijkheden op alle opgenomen gegevens.
- Automatische generatie van meta gegevens die kunnen worden opgevraagd, op basis van agronomic-functies.
- Automatisch gegenereerde tijdreeks aggregaties voor het snel bouwen van modellen.
- Geïntegreerde Azure Data Factory-Engine om eenvoudig aangepaste gegevens verwerkings pijplijnen te bouwen.

## <a name="application-development"></a>Toepassingen ontwikkelen

De FarmBeats-Api's bevatten technische documentatie voor Swagger. Zie [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)voor meer informatie over alle api's en hun bijbehorende aanvragen of antwoorden.

De volgende tabel bevat een overzicht van alle objecten en resources in FarmBeats Datahub:

| Objecten en bronnen | Beschrijving
--- | ---|
Landbouwbedrijf | De farm komt overeen met een fysieke locatie van belang in het FarmBeats-systeem. Elke farm heeft een farm naam en een unieke Farm-ID. |
Apparaat  | Het apparaat komt overeen met een fysiek apparaat dat aanwezig is op de farm. Elk apparaat heeft een unieke apparaat-ID. Een apparaat wordt doorgaans ingericht in een farm met een farm-ID.
DeviceModel  | DeviceModel komt overeen met de meta gegevens van het apparaat, zoals de fabrikant en het type apparaat, ofwel de gateway ofwel het knoop punt.
Sensoren  | Sensor komt overeen met een fysieke sensor waarmee waarden worden vastgelegd. Een sensor is doorgaans verbonden met een apparaat met een apparaat-ID.
SensorModel  | SensorModel komt overeen met de meta gegevens van de sensor, zoals de fabrikant, het type sensor dat analoog of digitaal is en de sensor meting, zoals omgevings temperatuur en druk.
Telemetrie  | Telemetrie biedt de mogelijkheid om telemetrie-berichten te lezen voor een bepaalde sensor en tijds bereik.
Taak  | De taak komt overeen met elke werk stroom van activiteiten die in het FarmBeats-systeem worden uitgevoerd om een gewenste uitvoer te krijgen. Elke taak is gekoppeld aan een taak-ID en taak type.
Taak type  | Taak type komt overeen met verschillende taak typen die door het systeem worden ondersteund. Er zijn door het systeem gedefinieerde en door de gebruiker gedefinieerde taak typen opgenomen.
ExtendedType  | Extended type komt overeen met de lijst met systeem-en door de gebruiker gedefinieerde typen in het systeem. Extended type helpt bij het instellen van een nieuw sensor-, scène-of scène bestands type in het FarmBeats-systeem.
Partner  | De partner komt overeen met de sensor-en image-integratie partner voor FarmBeats.
Scène  | Scène komt overeen met een gegenereerde uitvoer in de context van een farm. Aan elke scène zijn een scène-ID, scène bron, scène type en Farm-ID gekoppeld. Aan elke scène-ID kunnen meerdere scène bestanden zijn gekoppeld.
SceneFile |SceneFile komt overeen met alle bestanden die voor één scène worden gegenereerd. Er kunnen meerdere SceneFile-Id's aan één scène-ID zijn gekoppeld.
Regel  |De regel komt overeen met een voor waarde voor Farm gegevens om een waarschuwing te activeren. Elke regel bevindt zich in de context van de gegevens van een farm.
Waarschuwing  | De waarschuwing komt overeen met een melding die wordt gegenereerd wanneer aan de voor waarde van een regel wordt voldaan. Elke waarschuwing bevindt zich in de context van een regel.
RoleDefinition  | RoleDefinition definieert toegestane en niet-toegestane acties voor een rol.
RoleAssignment  |RoleAssignment komt overeen met de toewijzing van een rol aan een gebruiker of een service-principal.

### <a name="data-format"></a>Gegevensindeling

JSON is een gemeen schappelijke taal onafhankelijke gegevens indeling die een eenvoudige tekst weergave bevat van wille keurige gegevens structuren. Zie de [JSON-website](https://www.json.org/)voor meer informatie.

## <a name="authentication-and-authorization"></a>Verificatie en autorisatie

HTTP-aanvragen voor de REST API worden beveiligd met Azure Active Directory (Azure AD).
Voor het maken van een geverifieerde aanvraag voor de REST-Api's, vereist de client code verificatie met geldige referenties voordat u de API kunt aanroepen. Verificatie wordt gecoördineerd tussen de verschillende actoren door Azure AD. Het biedt uw client een toegangs token als bewijs van de verificatie. Het token wordt vervolgens verzonden in de HTTP-autorisatie-header van REST API-aanvragen. Zie [Azure Active Directory](https://portal.azure.com) voor ontwikkel aars voor meer informatie over Azure AD-verificatie.

Het toegangs token moet in volgende API-aanvragen worden verzonden in de koptekst sectie, zoals:

```http
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>HTTP-aanvraag headers

Hier volgen de meest voorkomende aanvraag headers die u moet opgeven wanneer u een API-aanroep maakt naar Azure FarmBeats Datahub.


**Header** | **Beschrijving en voor beeld**
--- | ---
Content-Type  | De aanvraag indeling (content-type: Application/ <format> ). Voor Azure FarmBeats Datahub-Api's is de indeling JSON. Content-type: Application/JSON
Autorisatie  | Hiermee geeft u het toegangs token op dat vereist is om een API-aanroep te maken. Autorisatie: Bearer <Access-Token>
Accepteren | De antwoord indeling. Voor Azure FarmBeats Datahub-Api's is de indeling JSON. Accepteren: toepassing/JSON

### <a name="api-requests"></a>API-aanvragen

Als u een REST API aanvraag wilt indienen, combineert u de HTTP-methode (GET, POST, PUT of DELETE), de URL van de API-service, de URI naar een resource om een query uit te voeren, gegevens in te dienen bij, bij te werken of te verwijderen, en vervolgens een of meer HTTP-aanvraag headers toe te voegen.

De URL van de API-service is uw Datahub-URL, bijvoorbeeld https:// \<yourdatahub-website-name> . azurewebsites.net.

U kunt desgewenst query parameters toevoegen aan GET-aanroepen om te filteren, de grootte van de gegevens in de antwoorden te beperken en te sorteren.

De volgende voorbeeld aanvraag wordt gebruikt om de lijst met apparaten op te halen:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

Voor de meeste GET-, POST-en PUT-aanroepen is een JSON-aanvraag tekst vereist.

Met de volgende voorbeeld aanvraag wordt een apparaat gemaakt. Deze aanvraag bevat een invoer-JSON met de aanvraag tekst.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Queryparameters

Voor REST GET-aanroepen kunt u de grootte van de gegevens in een API-antwoord filteren, beperken en sorteren door een of meer query parameters op de aanvraag-URI op te nemen. Voor de query parameters, zie de API-documentatie en de afzonderlijke GET-aanroepen.
Als u bijvoorbeeld een query uitvoert op de lijst met apparaten (aanroep ophalen op/Device), kunnen de volgende query parameters worden opgegeven:

![Lijst met apparaten](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>Foutafhandeling

Azure FarmBeats Datahub Api's retour neren de standaard HTTP-fouten. De meest voorkomende fout codes zijn als volgt:

 |Foutcode             | Beschrijving |
 |---                    | --- |
 |200                    | Geslaagd |
 |201                    | Maken (post) geslaagd |
 |400                    | Ongeldige aanvraag. Er is een fout opgetreden in de aanvraag. |
 |401                    | Gasten. De aanroeper van de API is niet gemachtigd om toegang te krijgen tot de resource. |
 |404                    | Kan de resource niet vinden |
 |5XX                    | Interne server fout. De fout codes die beginnen met 5XX betekenen dat er een fout is opgetreden op de server. Raadpleeg server logboeken en de volgende sectie voor meer informatie. |


Naast de standaard HTTP-fouten retour neren de Azure FarmBeats Datahub-Api's ook interne fouten in de volgende indeling:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

In dit voor beeld, wanneer een farm is gemaakt, is het verplichte veld ' naam ' niet opgegeven in de invoer lading. Het volgende fout bericht wordt weer gegeven:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Gebruikers of app-registraties toevoegen aan Azure Active Directory

Azure FarmBeats-Api's kunnen worden gebruikt door een gebruiker of een app-registratie in Azure Active Directory. Voer de volgende stappen uit om een app-registratie te maken in Azure Active Directory:

1. Ga naar de [Azure Portal](https://portal.azure.com)en selecteer **Azure Active Directory**  >  **app-registraties**  >  **nieuwe registratie**. U kunt ook een bestaand account gebruiken.
2. Ga als volgt te werk voor een nieuw account:

    - Voer een naam in.
    - Selecteer **accounts in deze organisatie-Directory alleen (één Tenant)**.
    - Gebruik de standaard waarden in de rest van de velden.
    - Selecteer **Registreren**.

3. Ga als volgt te werk in het deel venster Nieuw en bestaand app-registratie **overzicht** :

    - Leg de **client-id** en **Tenant-id** vast.
    - Ga naar **certificaten en geheimen** om een nieuw client geheim te genereren en het **client geheim** vast te leggen.
    - Ga terug naar **overzicht** en selecteer de koppeling naast **toepassing beheren in de lokale map**.
    - Ga naar **Eigenschappen** om de **object-id** vast te leggen.

4. Ga naar uw Datahub Swagger (https:// <yourdatahub> . azurewebsites.net/swagger/index.html) en voer de volgende handelingen uit:
    - Ga naar de **RoleAssignment-API**.
    - Voer een bericht uit om een **RoleAssignment** -object te maken voor de **object-id** die u zojuist hebt gemaakt.
 
```json
{
  "roleDefinitionId": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "objectId": "objectId from step 3 above",
  "objectIdType": "ServicePrincipalId",
  "tenantId": "tenant id of your Azure subscription"
}
```

  > [!NOTE]
  > Zie [Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie over het toevoegen van gebruikers en het registreren van Active Directory.

Nadat u de vorige stappen hebt voltooid, kunt u met de app-registratie (client) de Azure FarmBeats-Api's aanroepen met behulp van een toegangs token via Bearer-verificatie.

Gebruik het toegangs token om dit in volgende API-aanvragen in de koptekst sectie te verzenden als:

```http
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```