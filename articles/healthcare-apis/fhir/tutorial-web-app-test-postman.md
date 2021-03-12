---
title: Web App-zelfstudie - Verbinding maken met Azure API for FHIR testen
description: Deze zelfstudies laten zien hoe u een eenvoudige webtoepassing implementeert. In dit gedeelte van de zelfstudie wordt uitgelegd hoe u verbinding maakt met de FHIR-server met Postman
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 1c64468a2e420734ca51a5b9308bb52e13712c51
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018156"
---
# <a name="testing-the-fhir-api"></a>De FHIR-API testen
In de vorige twee stappen hebt u de Azure API for FHIR geïmplementeerd en uw clienttoepassing geregistreerd. U bent nu klaar om te testen of uw Azure API for FHIR is ingesteld met uw clienttoepassing. 

## <a name="retrieve-capability-statement"></a>Mogelijkheidsinstructie ophalen
Eerst halen we de mogelijkheidsinstructie op voor uw Azure API for FHIR. 
1. Open Postman
1. Haal de mogelijkheidsinstructie op met GET https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com/metadata. In de onderstaande afbeelding is de FHIR-servernaam **fhirserver**.

![Mogelijkheidsinstructie](media/tutorial-web-app/postman-capability-statement.png)

Vervolgens proberen we een patiënt op te halen. Als u een patiënt wilt ophalen, voert u GET https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com/Patient in. U ontvangt een foutcode 401, niet geautoriseerd. Deze fout ontstaat omdat u niet hebt bewezen dat u toegang mag hebben tot patiëntgegevens.

## <a name="get-patient-from-fhir-server"></a>Patiënt ophalen van FHIR-server
![Mislukte patiënt](media/tutorial-web-app/postman-patient-authorization-failed.png)

Als u toegang wilt krijgen, hebt u een toegangstoken nodig.
1. Selecteer in Postman **Autorisatie** en stel het type in op **OAuth 2.0**
1. Selecteer **nieuw toegangstoken ophalen**
1. Vul de velden in en selecteer **Token aanvragen**. Hieronder ziet u de waarden voor elk veld voor deze zelfstudie.

|Veld                |Waarde                                                               |
|---------------------|--------------------------------------------------------------------|
|Tokennaam           |Een naam voor uw token                                               |
|Toekenningstype           |Autorisatiecode                                                  |
|URL voor aanroep         |https://www.getpostman.com/oauth2/callback                          |
|URL van autorisatie             |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID> /oauth2/?resource=https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com|
|URL van toegangstoken     |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID> /oauth2/token|
|Client-id            |De client-id die u tijdens de vorige stappen hebt gekopieerd             |
|Clientgeheim        |\<BLANK>                                                            |
|Bereik                |\<BLANK>                                                            |
|Status                |1234                                                                |
|Clientauthenticatie|Clientreferenties in hoofdtekst verzenden                                     |

4. Meld u aan met uw referenties en selecteer **Accepteren**
1. Schuif omlaag in het resultaat en selecteer **Token gebruiken**
1. Selecteer bovenaan nogmaals **Verzenden** en deze keer zou u een resultaat moeten krijgen ![Geslaagde patiënt](media/tutorial-web-app/postman-patient-authorization-success.png)

## <a name="post-patient-into-fhir-server"></a>Een patiënt in FHIR-server plaatsen
Nu u toegang hebt, kunt u een nieuwe patiënt maken. Hier volgt een voorbeeld van een eenvoudige patiënt die u kunt toevoegen aan uw FHIR-server. Voer in het gedeelte **Hoofdtekst** van Postman de onderstaande code in.

``` json
    {
        "resourceType": "Patient",
        "active": true,
        "name": [
            {
                "use": "official",
                "family": "Kirk",
                "given": [
                    "James",
                    "Tiberious"
                ]
            },
            {
                "use": "usual",
                "given": [
                    "Jim"
                ]
            }
        ],
        "gender": "male",
        "birthDate": "1960-12-25"
    }
```
Met deze POST wordt een nieuwe patiënt in uw FHIR-server gemaakt met de naam James Tiberious Kirk.
![Patiënt plaatsen](media/tutorial-web-app/postman-post-patient.png)

Als u de GET-stap hierboven uitvoert om een ​​patiënt op te halen, ziet u James Tiberious Kirk vermeld in de uitvoer.

## <a name="troubleshooting-access-issues"></a>Toegangsproblemen oplossen
Als u tijdens een van deze stappen problemen ondervindt, bekijk dan de documenten die we hebben samengesteld over Azure Active Directory en de Azure API for FHIR. 

* [Azure AD en Azure API for FHIR-](azure-ad-hcapi.md): dit document bevat een overzicht van de basisprincipes van Azure Active Directory en hoe deze samenwerken met de Azure API for FHIR.
* [Validatie van toegangstoken](azure-ad-hcapi-token-validation.md): deze instructiegids bevat meer specifieke details over de validatie van het toegangstoken en de stappen die u moet ondernemen om toegangsproblemen op te lossen.

## <a name="next-steps"></a>Volgende stappen
Nu u verbinding kunt maken met uw clienttoepassing, bent u klaar om uw webtoepassing te schrijven.

>[!div class="nextstepaction"]
>[Een webtoepassing schrijven](tutorial-web-app-write-web-app.md)



