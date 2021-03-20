---
title: Verificatie van de eind gebruiker-REST met Data Lake Storage Gen1-Azure
description: Meer informatie over het bezorgen van de verificatie van eind gebruikers met Azure Data Lake Storage Gen1 met behulp van Azure Active Directory met behulp van REST API
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 462cd06c9da3b1f0a57c293d52c59181372b709b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103744"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Verificatie door eind gebruikers met Azure Data Lake Storage Gen1 met behulp van REST API
> [!div class="op_single_selector"]
> * [Java gebruiken](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK gebruiken](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python gebruiken](data-lake-store-end-user-authenticate-python.md)
> * [REST-API gebruiken](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

In dit artikel vindt u informatie over het gebruik van de REST API voor de verificatie van eind gebruikers met Azure Data Lake Storage Gen1. Zie [service-to-service-verificatie met Data Lake Storage Gen1 rest API](data-lake-store-service-to-service-authenticate-rest-api.md)voor service-naar-service verificatie met data Lake Storage gen1 met behulp van rest API.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Maak een Azure Active Directory systeem eigen toepassing**. U moet de stappen in de [verificatie van eind gebruikers hebben voltooid met data Lake Storage gen1 met behulp van Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[krul](https://curl.haxx.se/)**. Dit artikel maakt gebruik van krul om te laten zien hoe u REST API aanroepen voor een Data Lake Storage Gen1 account maakt.

## <a name="end-user-authentication"></a>Verificatie van de eindgebruiker
Verificatie door eind gebruikers is de aanbevolen methode als u wilt dat een gebruiker zich bij uw toepassing aanmeldt met behulp van Azure AD. Uw toepassing kan toegang krijgen tot Azure-resources met hetzelfde toegangs niveau als de aangemelde gebruiker. De gebruiker moet regel matig hun referenties opgeven om de toegang tot de toepassing te behouden.

Het resultaat van het aanmelden van de eind gebruiker is dat uw toepassing een toegangs token en een vernieuwings token krijgt. Het toegangs token wordt gekoppeld aan elke aanvraag van Data Lake Storage Gen1 of Data Lake Analytics en is standaard één uur geldig. Het vernieuwings token kan worden gebruikt om een nieuw toegangs token op te halen en is Maxi maal twee weken standaard geldig, als u regel matig gebruikt. U kunt twee verschillende benaderingen gebruiken voor aanmelding door de eind gebruiker.

In dit scenario wordt de gebruiker via de toepassing gevraagd om zich te melden en worden alle bewerkingen uitgevoerd in de context van de gebruiker. Voer de volgende stappen uit:

1. Leid de gebruiker via de toepassing om naar de volgende URL:

    `https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>`

   > [!NOTE]
   > \<REDIRECT-URI> moet worden gecodeerd voor gebruik in een URL. Voor https://localhost , gebruiken `https%3A%2F%2Flocalhost` )

    Voor deze zelfstudie kunt u de waarden van de tijdelijke aanduiding in bovenstaande URL vervangen en deze in de adresbalk van de webbrowser plakken. U wordt omgeleid om u te verifiëren met uw Azure-aanmelding. Wanneer u bent aangemeld, wordt het antwoord weergegeven in de adresbalk van de browser. Het antwoord heeft de volgende indeling:

    `http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>`

2. Leg de autorisatiecode uit het antwoord vast. Voor deze zelf studie kunt u de autorisatie code kopiëren uit de adres balk van de webbrowser en deze door geven in de POST-aanvraag naar het eind punt van het token, zoals wordt weer gegeven in het volgende code fragment:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
    -F redirect_uri=<REDIRECT-URI> \
    -F grant_type=authorization_code \
    -F resource=https://management.core.windows.net/ \
    -F client_id=<APPLICATION-ID> \
    -F code=<AUTHORIZATION-CODE>
    ```

   > [!NOTE]
   > In dit geval is de \<REDIRECT-URI> nood zaak niet gecodeerd.
   > 
   > 

3. Het antwoord is een JSON-object dat een toegangs token bevat (bijvoorbeeld `"access_token": "<ACCESS_TOKEN>"` ) en een vernieuwings token (bijvoorbeeld `"refresh_token": "<REFRESH_TOKEN>"` ). Uw toepassing gebruikt het toegangs token bij het openen van Azure Data Lake Storage Gen1 en het vernieuwings token om een ander toegangs token op te halen wanneer een toegangs token verloopt.

    ```json
    {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
    ```

4. Wanneer het toegangs token is verlopen, kunt u een nieuw toegangs token aanvragen met het vernieuwings token, zoals wordt weer gegeven in het volgende code fragment:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
         -F grant_type=refresh_token \
         -F resource=https://management.core.windows.net/ \
         -F client_id=<APPLICATION-ID> \
         -F refresh_token=<REFRESH-TOKEN>
    ```

Zie [De stroom voor autorisatiecodetoekenning](/previous-versions/azure/dn645542(v=azure.100)) voor meer informatie over interactieve gebruikersverificatie.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u service-naar-service-verificatie kunt gebruiken om te verifiëren met Azure Data Lake Storage Gen1 met behulp van REST API. U kunt nu de volgende artikelen bekijken om te praten over het gebruik van de REST API om met Azure Data Lake Storage Gen1 te werken.

* [Account beheer bewerkingen op Data Lake Storage Gen1 met behulp van REST API](data-lake-store-get-started-rest-api.md)
* [Gegevens bewerkingen op Data Lake Storage Gen1 met behulp van REST API](data-lake-store-data-operations-rest-api.md)