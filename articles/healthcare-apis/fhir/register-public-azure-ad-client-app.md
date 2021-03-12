---
title: Een open bare client-app registreren in azure AD-Azure API voor FHIR
description: In dit artikel wordt uitgelegd hoe u een open bare client toepassing registreert bij Azure Active Directory, ter voor bereiding op de implementatie van de FHIR-API in Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 3624812cf9119c270c30cf5a7cbebb57030a5113
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018203"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Een open bare client toepassing registreren in Azure Active Directory

In dit artikel leert u hoe u een open bare toepassing in Azure Active Directory kunt registreren.  

Registraties van client toepassingen zijn Azure Active Directory representaties van toepassingen die de API-machtigingen namens een gebruiker kunnen verifiëren en om deze te vragen. Open bare clients zijn toepassingen zoals mobiele toepassingen en Java script-toepassingen met één pagina waarmee geheimen vertrouwelijk niet kunnen worden bewaard. De procedure is vergelijkbaar met het [registreren van een vertrouwelijke client](register-confidential-azure-ad-client-app.md), maar omdat open bare clients niet kunnen worden vertrouwd voor het opslaan van een toepassings geheim, hoeft er geen te worden toegevoegd.

De Snelstartgids bevat algemene informatie over het [registreren van een toepassing met het micro soft Identity-platform](../../active-directory/develop/quickstart-register-app.md).

## <a name="app-registrations-in-azure-portal"></a>App-registraties in Azure Portal

1. Klik in de [Azure-portal](https://portal.azure.com) in het navigatiepaneel aan de linkerkant op **Azure Active Directory**.

2. Op de Blade **Azure Active Directory** klikt u op **app-registraties**:

    ![Azure Portal. Nieuwe app-registratie.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klik op de **nieuwe registratie**.

## <a name="application-registration-overview"></a>Overzicht van toepassings registratie

1. Geef de toepassing een weergave naam.

2. Geef een antwoord-URL op. De antwoord-URL is de locatie van de authenticatie codes worden geretourneerd naar de client toepassing. U kunt later meer antwoord-Url's toevoegen en bestaande bewerken.

    ![Azure Portal. Nieuwe open bare app registreren.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)


Uw [Desktop](../../active-directory/develop/scenario-desktop-app-registration.md)-, [mobiele](../../active-directory/develop/scenario-mobile-app-registration.md) of toepassing met [één pagina](../../active-directory/develop/scenario-spa-app-registration.md) als open bare toepassing configureren:

1. Selecteer in de [Azure Portal](https://portal.azure.com)in **app-registraties** uw app en selecteer vervolgens **verificatie**.

2. Selecteer **Geavanceerde instellingen**  >  **standaard client type**. Selecteer **Ja** **Als u de toepassing wilt behandelen als een open bare client**.

3. Voor een toepassing met één pagina selecteert u **toegangs tokens** en **id-tokens** om een impliciete stroom in te scha kelen.

   - Als uw toepassing zich aanmeldt bij gebruikers, selecteert u **id-tokens**.
   - Als uw toepassing ook een beveiligde web-API moet aanroepen, selecteert u **toegangs tokens**.

## <a name="api-permissions"></a>API-machtigingen

Net als bij de [vertrouwelijke client toepassing](register-confidential-azure-ad-client-app.md)moet u selecteren welke API-machtigingen deze toepassing moet kunnen aanvragen namens gebruikers:

1. Open de **API-machtigingen**.

    Als u de Azure API voor FHIR gebruikt, voegt u een machtiging toe aan de Azure-gezondheids zorg Api's door te zoeken naar Azure-gezondheids zorg Api's onder **api's mijn organisatie gebruikt**. U kunt dit alleen vinden als u [de Azure-API voor FHIR](fhir-paas-powershell-quickstart.md)al hebt geïmplementeerd.

    
    Als u naar een andere bron toepassing verwijst, selecteert u de [registratie van de FHIR API-resource toepassing](register-resource-azure-ad-client-app.md) die u eerder hebt gemaakt onder **mijn api's**:

    ![Azure Portal. Nieuwe open bare API-machtigingen: Azure API for FHIR-standaard](media/public-client-app/api-permissions.png)


2. Selecteer de machtigingen die u wilt dat de toepassing het volgende kan aanvragen: ![ Azure Portal. App-machtigingen](media/public-client-app/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>FHIR Server-instantie valideren
Als de toepassing die u in dit artikel hebt geregistreerd en uw FHIR-server zich in dezelfde Azure AD-Tenant bevinden, kunt u door gaan met de volgende stappen.

Als u uw client toepassing configureert in een andere Azure AD-Tenant vanaf uw FHIR-server, moet u de **instantie** bijwerken. In azure API voor FHIR stelt u de instantie in op instellingen-->-verificatie. Stel uw instantie in op **https://login.microsoftonline.com/\<TENANT-ID>** .

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een open bare client toepassing in Azure Active Directory kunt registreren. Test vervolgens de toegang tot uw FHIR-server met behulp van postman.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR openen met Postman](access-fhir-postman-tutorial.md)