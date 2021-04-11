---
title: Een vertrouwelijke client-app registreren in azure AD-Azure API voor FHIR
description: Registreer een vertrouwelijke client toepassing in Azure Active Directory die namens de gebruiker verifieert en toegang tot bron toepassingen vraagt.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: matjazl
ms.openlocfilehash: c10b27d375e2bfb8c64130eceb416a633241cf68
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284427"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Een vertrouwelijke client toepassing registreren in Azure Active Directory

In deze zelf studie leert u hoe u een vertrouwelijke client toepassing kunt registreren in Azure Active Directory (Azure AD).  

Een registratie van een client toepassing is een Azure AD-weer gave van een toepassing die kan worden gebruikt om namens een gebruiker te verifiëren en om toegang tot [bron toepassingen](register-resource-azure-ad-client-app.md)aan te vragen. Een vertrouwelijke client toepassing is een toepassing die kan worden vertrouwd voor het bewaren van een geheim en die geheim presenteert bij het aanvragen van toegangs tokens. Voor beelden van vertrouwelijke toepassingen zijn toepassingen aan de server zijde. 

Raadpleeg de volgende stappen om een nieuwe vertrouwelijke client toepassing te registreren. 

## <a name="register-a-new-application"></a>Een nieuwe toepassing registreren

1. Selecteer **Azure Active Directory** in de [Azure-portal](https://portal.azure.com).

1. Selecteer **App-registraties**. 

    :::image type="content" source="media/how-to-aad/portal-aad-new-app-registration.png" alt-text="Azure Portal. Nieuwe app-registratie.":::

1. Selecteer **Nieuwe registratie**.

1. Geef de toepassing een door de gebruiker gerichte weergave naam.

1. Selecteer voor **ondersteunde account typen** wie de toepassing kan gebruiken of toegang kan krijgen tot de API.

1. Beschrijving Geef een **omleidings-URI** op. Deze gegevens kunnen later worden gewijzigd, maar als u de antwoord-URL van uw toepassing weet, voert u deze nu in.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png" alt-text="Nieuwe registratie van vertrouwelijke client-app.":::

1. Selecteer **Registreren**.

## <a name="api-permissions"></a>API-machtigingen

Nu u uw toepassing hebt geregistreerd, moet u selecteren welke API-machtigingen deze toepassing moet aanvragen namens gebruikers.

1. Selecteer **API-machtigingen**.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png" alt-text="Vertrouwelijke client. API-machtigingen.":::

1. Selecteer **een machtiging toevoegen**.

    Als u de Azure API voor FHIR gebruikt, voegt u een machtiging toe aan de Azure-gezondheids zorg Api's door te zoeken naar de **Azure-gezondheids zorg** in **api's mijn organisatie gebruikt**. Het Zoek resultaat voor de Azure-gezondheids-API wordt alleen geretourneerd als u [de Azure-API voor FHIR](fhir-paas-powershell-quickstart.md)al hebt geïmplementeerd.

    Als u naar een andere bron toepassing verwijst, selecteert u de [registratie van de FHIR API-resource toepassing](register-resource-azure-ad-client-app.md) die u eerder hebt gemaakt onder **mijn api's**.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Vertrouwelijke client. Mijn organisatie-Api's" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

1. Selecteer bereiken (machtigingen) die de vertrouwelijke client toepassing vraagt voor namens een gebruiker. Selecteer **user_impersonation** en selecteer vervolgens **machtigingen toevoegen**.

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Vertrouwelijke client. Gedelegeerde machtigingen":::


## <a name="application-secret"></a>Toepassingsgeheim

1. Selecteer **certificaten & geheimen** en selecteer vervolgens **Nieuw client geheim**. 

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png" alt-text="Vertrouwelijke client. Toepassings geheim.":::

1. Voer een **beschrijving** voor het clientgeheim in. Selecteer de vervolg keuzelijst **Expires** om een verloop tijd frame te kiezen en klik vervolgens op **toevoegen**.

   :::image type="content" source="media/how-to-aad/add-a-client-secret.png" alt-text="Voeg een client geheim toe.":::

1. Nadat de client Secret string is gemaakt, kopieert u de **waarde** en **id** en slaat u deze op een veilige locatie van uw keuze op.

   :::image type="content" source="media/how-to-aad/client-secret-string-password.png" alt-text="Geheim teken reeks van client."::: 

> [!NOTE]
>De geheim teken reeks van de client is slechts eenmaal zichtbaar in de Azure Portal. Wanneer u de webpagina certificaten & geheimen gaat verlaten en vervolgens weer terugkeert naar de pagina, wordt de waarde teken reeks gemaskeerd. Het is belang rijk dat u de geheime teken reeks van de client kopieert direct nadat deze is gegenereerd. Als u geen back-up van uw client geheim hebt, moet u de bovenstaande stappen herhalen om deze opnieuw te genereren.
 
## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleid door de stappen voor het registreren van een vertrouwelijke client toepassing in azure AD. U bent ook begeleid bij de stappen voor het toevoegen van API-machtigingen aan de Azure-gezondheids zorg-API. U hebt tot slot geleerd hoe u een toepassings geheim maakt. Daarnaast kunt u zien hoe u met behulp van Postman toegang krijgt tot uw FHIR-server.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR openen met Postman](access-fhir-postman-tutorial.md)
