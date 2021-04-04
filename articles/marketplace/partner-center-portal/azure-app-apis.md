---
title: Partner Center-API voor het indienen van Azure-apps in de micro soft Commercial Marketplace
description: Lees de vereisten voor het gebruik van de Partner Center-API voor het indienen van Azure-apps in commerciële Marketplace op het micro soft partner centrum.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 9863ed24da9e427f885a4794bda7e103b0c1cc8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96455456"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>Partner Center-API voor het indienen van Azure-apps in het partner centrum

Gebruik de *inzendings-API van partner Center* om programmatisch query's uit te geven, inzendingen te maken en Azure-aanbiedingen te publiceren.  Deze API is handig als uw account veel aanbiedingen beheert en u het verzend proces voor deze aanbiedingen wilt automatiseren en optimaliseren.

## <a name="api-prerequisites"></a>API-vereisten

Er zijn enkele programmatische activa die u nodig hebt om de Partner Center-API voor Azure-producten te kunnen gebruiken: 

- een Azure Active Directory-toepassing.
- een toegangs token voor Azure Active Directory (Azure AD).

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Stap 1: vereisten voor het gebruik van de inzendings-API van partner Center volt ooien

Voordat u begint met het schrijven van code voor het aanroepen van de inzendings-API van het partner centrum, moet u ervoor zorgen dat u de volgende vereisten hebt voltooid.

- U (of uw organisatie) moet een Azure AD-adres lijst hebben en u moet over [globale beheerders](../../active-directory/roles/permissions-reference.md) machtigingen voor de map beschikken. Als u Microsoft 365 of andere zakelijke services van micro soft al gebruikt, hebt u al een Azure AD-adres lijst. Als dat niet het geval is, kunt u [een nieuwe Azure AD in het partner centrum maken](/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) zonder dat hiervoor extra kosten in rekening worden gebracht.

- U moet [een Azure AD-toepassing koppelen aan uw partner centrum-account](/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) en uw Tenant-id, client-ID en-sleutel verkrijgen. U hebt deze waarden nodig voor het verkrijgen van een Azure AD-toegangs token dat u gebruikt in aanroepen van de Microsoft Store Submission-API.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Een Azure AD-toepassing koppelen aan uw partner centrum-account

Als u de Microsoft Store indienings-API wilt gebruiken, moet u een Azure AD-toepassing koppelen aan uw partner centrum-account, de Tenant-ID en de client-ID voor de toepassing ophalen en een sleutel genereren. De Azure AD-toepassing vertegenwoordigt de app of service van waaruit u de inzendings-API van het partner centrum wilt aanroepen. U hebt de Tenant-ID, de client-ID en de sleutel nodig voor het verkrijgen van een Azure AD-toegangs token dat u doorgeeft aan de API.

>[!Note]
>U hoeft deze taak slechts één keer uit te voeren. Nadat u de Tenant-ID, client-ID en sleutel hebt, kunt u deze telkens opnieuw gebruiken wanneer u een nieuw Azure AD-toegangs token wilt maken.

1. Koppel in partner centrum het [partner centrum-account van uw organisatie aan de Azure AD-Directory van uw organisatie](/windows/uwp/publish/associate-azure-ad-with-partner-center).
1. Voeg vervolgens vanaf de pagina **gebruikers** in de sectie **account instellingen** van het partner centrum [de Azure AD-toepassing toe](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) die de app of service vertegenwoordigt die u gaat gebruiken voor toegang tot inzendingen voor uw partner centrum-account. Zorg ervoor dat u deze toepassing de rol **Manager** toewijst. Als de toepassing nog niet bestaat in uw Azure AD-adres lijst, kunt u [een nieuwe Azure AD-toepassing maken in partner centrum](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Ga terug naar de pagina **gebruikers** , klik op de naam van uw Azure AD-toepassing om naar de toepassings instellingen te gaan en kopieer de **Tenant-ID** en **client-id-** waarden.
1. Klik op **nieuwe sleutel toevoegen**. Kopieer de **sleutel** waarde op het volgende scherm. Nadat u deze pagina verlaat, hebt u geen toegang meer tot deze gegevens. Zie [sleutels voor een Azure AD-toepassing beheren](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys)voor meer informatie.

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Stap 2: een Azure AD-toegangs token verkrijgen

Voordat u een van de methoden in de inzendings-API van partner Center aanroept, moet u eerst een Azure AD-toegangs token verkrijgen dat u doorgeeft aan de **autorisatie** -header van elke methode in de API. Nadat u een toegangs token hebt verkregen, hebt u 60 minuten om het te gebruiken voordat het verloopt. Nadat het token is verlopen, kunt u het token vernieuwen zodat u het kunt blijven gebruiken in toekomstige aanroepen naar de API.

Als u het toegangs token wilt verkrijgen, volgt u de instructies in [service to service-aanroepen met behulp van client referenties](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) om een `HTTP POST` naar het `https://login.microsoftonline.com/<tenant_id>/oauth2/token` eind punt te verzenden. Hier volgt een voorbeeld aanvraag:

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

Voor de *tenant_id* waarde in de `POST URI` para meters en *client_id* en *client_secret* geeft u de Tenant-id, de client-id en de sleutel voor uw toepassing op die u in het vorige gedeelte van partner centrum hebt opgehaald. Voor de para meter *resource* moet u opgeven `https://api.partner.microsoft.com` .

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Stap 3: de Microsoft Store inzendings-API gebruiken

Nadat u een Azure AD-toegangs token hebt, kunt u methoden aanroepen in de inzendings-API van partner Center. Als u verzen dingen wilt maken of bijwerken, roept u doorgaans meerdere methoden in de Partner Center-API voor verzenden in een specifieke volg orde aan. Voor informatie over elk scenario en de syntaxis van elke methode raadpleegt u de API-Swagger voor opname-api's.

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een [technische Asset](../create-azure-container-technical-assets.md) voor het maken van een Azure-VM
* Meer informatie over het maken van een [Azure-container aanbieding](../create-azure-container-offer.md)