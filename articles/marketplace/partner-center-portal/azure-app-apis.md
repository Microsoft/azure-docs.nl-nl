---
title: Partner Center-API voor het onboarden van Azure-apps in de commerciële marketplace van Microsoft
description: Meer informatie over de vereisten voor het gebruik van Partner Center submission-API voor Azure-apps in de commerciële marketplace op Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: ed8f5b8567e5808ebccfdc2a6d2d3b5d530da5cc
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107884008"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>Partner Center-API voor het onboarden van Azure-apps in Partner Center

Gebruik de *Partner Center submission-API om* programmatisch te zoeken, inzendingen te maken voor Azure-aanbiedingen en deze te publiceren.  Deze API is handig als uw account veel aanbiedingen beheert en u het indieningsproces voor deze aanbiedingen wilt automatiseren en optimaliseren.

## <a name="api-prerequisites"></a>API-vereisten

Er zijn enkele programmatische assets die u nodig hebt om de api voor azure-producten Partner Center gebruiken: 

- een Azure Active Directory toepassing.
- een Azure Active Directory (Azure AD)-toegangsken.

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Stap 1: vereisten voltooien voor het gebruik van de Partner Center submission-API

Voordat u begint met het schrijven van code voor het aanroepen van Partner Center submission-API, moet u ervoor zorgen dat u aan de volgende vereisten hebt voltooid.

- U (of uw organisatie) moet een Azure AD-directory hebben en u moet een [Globale beheerder](../../active-directory/roles/permissions-reference.md) voor de directory hebben. Als u al gebruik Microsoft 365 of andere zakelijke services van Microsoft, hebt u al een Azure AD-directory. Anders kunt u [een nieuwe Azure AD in](/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) Partner Center zonder extra kosten.

- U moet [een Azure AD-toepassing koppelen aan uw Partner Center account en](/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) uw tenant-id, client-id en sleutel verkrijgen. U hebt deze waarden nodig om een Azure AD-toegangsken te verkrijgen, dat u gebruikt in aanroepen naar de API voor Microsoft Store-inzendingen.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Een Azure AD-toepassing koppelen aan uw Partner Center account

Als u de API voor Microsoft Store-inzendingen wilt gebruiken, moet u een Azure AD-toepassing koppelen aan uw Partner Center-account, de tenant-id en client-id voor de toepassing ophalen en een sleutel genereren. De Azure AD-toepassing vertegenwoordigt de app of service van waaruit u de indienings-API voor Partner Center aanroepen. U hebt de tenant-id, client-id en -sleutel nodig om een Azure AD-toegang token te verkrijgen dat u aan de API door geeft.

>[!Note]
>U hoeft deze taak maar één keer uit te voeren. Nadat u de tenant-id, client-id en -sleutel hebt, kunt u deze op elk moment opnieuw gebruiken wanneer u een nieuw Azure AD-toegang token moet maken.

1. In Partner Center koppelt u het Partner Center van uw organisatie [aan de Azure AD-directory van uw organisatie.](/windows/uwp/publish/associate-azure-ad-with-partner-center)
1. Voeg vervolgens  op de pagina Gebruikers in de sectie **Accountinstellingen** van Partner Center de [Azure AD-toepassing](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) toe die staat voor de app of service die u gebruikt voor toegang tot inzendingen voor uw Partner Center-account. Zorg ervoor dat u aan deze toepassing de **rol Manager** toewijst. Als de toepassing nog niet bestaat in uw Azure AD-directory, kunt u een nieuwe [Azure AD-toepassing maken in Partner Center](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Ga terug naar de **pagina Gebruikers,** klik op de naam van uw Azure AD-toepassing om naar de toepassingsinstellingen te gaan en kopieer de **waarden tenant-id** en **client-id.**
1. Klik **op Nieuwe sleutel toevoegen.** Kopieer in het volgende scherm de waarde **van Sleutel.** U hebt geen toegang meer tot deze gegevens nadat u deze pagina hebt verlaten. Zie Sleutels voor een [Azure AD-toepassing beheren voor meer informatie.](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys)

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Stap 2: Een Azure AD-toegangsteken verkrijgen

Voordat u een van de methoden in de Partner Center submission-API aanroept, moet u  eerst een Azure AD-toegangs token verkrijgen dat u door geeft aan de autorisatie-header van elke methode in de API. Nadat u een toegangsteken hebt ontvangen, hebt u 60 minuten om het te gebruiken voordat het verloopt. Nadat het token is verlopen, kunt u het token vernieuwen zodat u het in toekomstige aanroepen naar de API kunt blijven gebruiken.

Volg de instructies in Service [to Service Calls Using Client Credentials (Service-naar-serviceaanroepen met clientreferenties)](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) om een naar het eindpunt te verzenden om het toegang token `HTTP POST` te `https://login.microsoftonline.com/<tenant_id>/oauth2/token` verkrijgen. Hier is een voorbeeldaanvraag:

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

Geef voor *de tenant_id-waarde* in de parameters en client_id en client_secret de `POST URI` tenant-id,  client-id en sleutel op voor uw toepassing die u in de vorige sectie hebt opgehaald uit Partner Center.  Voor de *resourceparameter* moet u `https://api.partner.microsoft.com` opgeven.

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Stap 3: gebruik de API voor Microsoft Store-inzendingen

Nadat u een Azure AD-toegangs token hebt, kunt u methoden aanroepen in de Partner Center submission-API. Als u inzendingen wilt maken of bijwerken, roept u doorgaans meerdere methoden in de Partner Center submission-API in een specifieke volgorde aan. Zie de Swagger voor de Opname-API voor informatie over elk scenario en de syntaxis van elke methode.

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>Volgende stappen

* [Een technische asset voor Azure Container maken](../azure-container-technical-assets.md)
* [Een Azure Container-aanbieding maken](../azure-container-offer-setup.md)