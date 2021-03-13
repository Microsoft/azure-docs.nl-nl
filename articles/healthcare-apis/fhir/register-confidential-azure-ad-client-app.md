---
title: Een vertrouwelijke client-app registreren in azure AD-Azure API voor FHIR
description: Registreer een vertrouwelijke client toepassing in Azure Active Directory die namens de gebruiker verifieert en toegang tot bron toepassingen vraagt.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8021fb3fa9f11ef895569f48a2ae21b3f7adcd36
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018211"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Een vertrouwelijke client toepassing registreren in Azure Active Directory

In deze zelf studie leert u hoe u een vertrouwelijke client toepassing in Azure Active Directory kunt registreren. 

Registratie van een client toepassing is een Azure Active Directory representatie van een toepassing die kan worden gebruikt om namens een gebruiker te verifiëren en om toegang tot [bron toepassingen](register-resource-azure-ad-client-app.md)aan te vragen. Een vertrouwelijke client toepassing is een toepassing die kan worden vertrouwd voor het bewaren van een geheim en die geheim presenteert bij het aanvragen van toegangs tokens. Voor beelden van vertrouwelijke toepassingen zijn toepassingen aan de server zijde.

Voer de volgende stappen uit om een nieuwe vertrouwelijke toepassing te registreren in de portal.

## <a name="register-a-new-application"></a>Een nieuwe toepassing registreren

1. Ga in het [Azure Portal](https://portal.azure.com)naar **Azure Active Directory**.

1. Selecteer **App-registraties**.

    ![Azure Portal. Nieuwe app-registratie.](media/how-to-aad/portal-aad-new-app-registration.png)

1. Selecteer **Nieuwe registratie**.

1. Geef de toepassing een weergave naam.

1. Geef een antwoord-URL op. Deze gegevens kunnen later worden gewijzigd, maar als u de antwoord-URL van uw toepassing weet, voert u deze nu in.

    ![Nieuwe registratie van vertrouwelijke client-app.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)
1. Selecteer **Registreren**.

## <a name="api-permissions"></a>API-machtigingen

Nu u uw toepassing hebt geregistreerd, moet u selecteren welke API-machtigingen deze toepassing moet kunnen aanvragen namens gebruikers:

1. Selecteer **API-machtigingen**.

    ![Vertrouwelijke client. API-machtigingen](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

1. Selecteer **een machtiging toevoegen**.

    Als u de Azure API voor FHIR gebruikt, voegt u een machtiging toe aan de Azure-gezondheids zorg Api's door te zoeken naar **Azure-gezondheids zorg api's** onder **api's mijn organisatie gebruikt**. U kunt dit alleen vinden als u [de Azure-API voor FHIR](fhir-paas-powershell-quickstart.md)al hebt geïmplementeerd.

    Als u naar een andere bron toepassing verwijst, selecteert u de [registratie van de FHIR API-resource toepassing](register-resource-azure-ad-client-app.md) die u eerder hebt gemaakt onder **mijn api's**.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Vertrouwelijke client. Mijn organisatie-Api's" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

3. Selecteer bereiken (machtigingen) die de vertrouwelijke toepassing moet kunnen vragen namens een gebruiker:

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Vertrouwelijke client. Gedelegeerde machtigingen":::

## <a name="application-secret"></a>Toepassingsgeheim

1. Selecteer **Certificaten en geheimen**.
1. Selecteer **Nieuw clientgeheim**. 

    ![Vertrouwelijke client. Toepassings geheim](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Geef een beschrijving en duur van het geheim op (ofwel 1 jaar, 2 jaar of nooit).

3. Zodra de app is gegenereerd, wordt deze weer gegeven in de portal. Noteer deze versie en bewaar deze veilig.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een vertrouwelijke client toepassing in Azure Active Directory kunt registreren. Vervolgens krijgt u toegang tot uw FHIR-server met behulp van postman
 
>[!div class="nextstepaction"]
>[Azure API for FHIR openen met Postman](access-fhir-postman-tutorial.md)
