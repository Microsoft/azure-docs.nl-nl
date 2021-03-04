---
title: Een app-registratie maken
titleSuffix: Azure Digital Twins
description: Bekijk hoe u een Azure AD-App-registratie maakt als verificatie optie voor client-apps.
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: acb5457f10c54a741a738dd8a1008e703b0f23b0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051018"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Een app-registratie maken voor gebruik met Azure Digital Apparaatdubbels

Wanneer u werkt met een Azure Digital Apparaatdubbels-exemplaar, is het gebruikelijk om met die instantie te communiceren via client toepassingen, zoals een aangepaste client-app of een voor beeld zoals [Azure Digital Apparaatdubbels Explorer](quickstart-adt-explorer.md). Deze toepassingen moeten worden geverifieerd met Azure Digital Apparaatdubbels om ermee te kunnen communiceren, en sommige van de [verificatie mechanismen](how-to-authenticate-client.md) die apps kunnen gebruiken, zijn van toepassing op een [Azure Active Directory (Azure AD)-](../active-directory/fundamentals/active-directory-whatis.md) **app-registratie**.

Dit is niet vereist voor alle verificatie scenario's. Als u echter een verificatie strategie of code voorbeeld gebruikt waarvoor een app-registratie vereist is, met inbegrip van een **client-id** en **Tenant-id**, wordt in dit artikel beschreven hoe u er een kunt instellen.

## <a name="using-azure-ad-app-registrations"></a>Registraties van Azure AD-app gebruiken

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) is de cloud-gebaseerde service voor identiteits-en toegangs beheer van micro soft. Het instellen van een **app-registratie** in azure AD is een manier om een client-app toegang te verlenen tot Azure Digital apparaatdubbels.

Met deze app-registratie kunt u toegangs machtigingen voor de [Azure Digital apparaatdubbels-api's](how-to-use-apis-sdks.md)configureren. Later kunnen client-apps worden geverifieerd aan de hand van de app-registratie met behulp van de **client-en Tenant-ID-waarden** van de registratie. als gevolg hiervan zijn de geconfigureerde toegangs machtigingen voor de api's verleend.

>[!TIP]
> U kunt liever elke keer een nieuwe app-registratie instellen, *of* u kunt dit slechts eenmaal doen, waarbij u één app-registratie tot stand brengt die wordt gedeeld met alle scenario's die deze nodig hebben.

## <a name="create-the-registration"></a>De registratie maken

Begin met het navigeren naar [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) in het Azure Portal (u kunt deze koppeling gebruiken of zoeken naar de portal-zoek balk). Selecteer *app-registraties* in het menu service en klik vervolgens op *+ nieuwe registratie*.

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Weer gave van de Azure AD-service pagina in de Azure Portal, waarbij u de menu optie App-registraties en de knop + nieuwe registratie markeert":::

Vul op de pagina *een toepassing registreren* de gevraagde waarden in:
* **Naam**: een weergave naam voor de Azure AD-toepassing die u aan de registratie wilt koppelen
* **Ondersteunde account typen**: Selecteer *accounts in deze organisatie-Directory alleen (alleen standaard mappen-één Tenant)*
* **Omleidings-URI**: een antwoord-URL voor de *Azure AD-toepassing* voor de Azure AD-toepassing. Voeg een URI voor de *open bare client/systeem eigen (mobile & Desktop)* toe voor `http://localhost` .

Wanneer u klaar bent, klikt u op de knop *registreren* .

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Weer gave van de pagina een toepassing registreren met de beschreven waarden":::

Wanneer de registratie is voltooid, wordt u doorgestuurd naar de pagina met details van de portal.

## <a name="collect-client-id-and-tenant-id"></a>Client-ID en Tenant-ID verzamelen

Verzamel vervolgens enkele belang rijke waarden over de app-registratie van de pagina met Details:

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Portal weergave van de belang rijke waarden voor de app-registratie":::

Noteer de ID van de _**toepassings**_ -id en de _**Directory (Tenant)**_ die op **de** pagina wordt weer gegeven. Dit zijn de waarden die een client-app nodig heeft om deze registratie te gebruiken voor verificatie met Azure Digital Apparaatdubbels.

## <a name="provide-azure-digital-twins-api-permission"></a>Azure Digital Apparaatdubbels API-machtiging bieden

Vervolgens configureert u de app-registratie die u hebt gemaakt met basislijn machtigingen voor de Azure Digital Apparaatdubbels-Api's.

Selecteer vanuit de portal pagina voor de registratie van uw app *API-machtigingen* in het menu. Op de volgende machtigingen pagina, klikt u op de knop *+ een machtiging toevoegen* .

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Weer gave van de app-registratie in de Azure Portal, waarbij u de menu optie API-machtigingen en de knop + een machtiging toevoegen selecteert":::

Schakel op de pagina *API-machtigingen voor aanvragen* de volgende opdracht uit naar de api's die *Mijn organisatie gebruikt* en zoek naar *Azure Digital apparaatdubbels*. Selecteer _**Azure Digital apparaatdubbels**_ in de zoek resultaten om machtigingen toe te wijzen voor de Azure Digital Apparaatdubbels-api's.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Weer gave van het Zoek resultaat van de pagina API-machtigingen aanvragen met Azure Digital Apparaatdubbels, met een toepassings-ID (client) van 0b07f429-9f4b-4714-9392-cc5e8e80c8b0.":::

>[!NOTE]
> Als uw abonnement nog steeds een bestaand exemplaar van Azure Digital Apparaatdubbels uit de vorige open bare preview van de service heeft (vóór 2020 juli), moet u in plaats daarvan de _**Azure Smart Spaces-service**_ zoeken en selecteren. Dit is een oudere naam voor dezelfde set Api's (Let op: de *client-id* is hetzelfde als in de bovenstaande scherm afbeelding) en uw ervaring wordt niet meer gewijzigd dan in deze stap.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Weer gave van het Zoek resultaat van de pagina API-machtigingen aanvragen met de Azure Smart Spaces-service":::

Vervolgens selecteert u de machtigingen die u voor deze Api's wilt verlenen. Vouw de machtiging **Read (1)** uit en schakel het selectie vakje *lezen. schrijven* in om deze app-registratie lezer en schrijf machtigingen te verlenen.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Weer gave van de pagina ' API-machtigingen aanvragen ' om ' Lees. schrijf machtigingen ' te selecteren voor de Azure Digital Apparaatdubbels-Api's":::

Klik op *machtigingen toevoegen* wanneer u klaar bent.

### <a name="verify-success"></a>Controleren geslaagd

Controleer op de pagina *API-machtigingen* of er nu een vermelding voor de machtigingen voor lezen/schrijven wordt weer gegeven voor Azure Digital apparaatdubbels:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Portal weergave van de API-machtigingen voor de Azure AD-App-registratie, met lees-en schrijf toegang voor Azure Digital Apparaatdubbels":::

U kunt ook de verbinding met Azure Digital Apparaatdubbels in de *manifest.jsvan* de app-registratie controleren, die automatisch is bijgewerkt met de gegevens van de Azure Digital apparaatdubbels wanneer u de API-machtigingen hebt toegevoegd.

Als u dit wilt doen, selecteert u *manifest* in het menu om de manifest code van de app-registratie weer te geven. Ga naar de onderkant van het code venster en zoek deze velden onder `requiredResourceAccess` . De waarden moeten overeenkomen met die in de onderstaande scherm afbeelding:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Portal weergave van het manifest voor de registratie van de Azure AD-app. Genest onder ' requiredResourceAccess ', is de waarde ' resourceAppId ' van 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 en een ' resourceAccess > id-waarde van 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

Als deze waarden ontbreken, voert u de stappen in de [sectie voor het toevoegen van de API-machtiging](#provide-azure-digital-twins-api-permission)opnieuw uit.

## <a name="other-possible-steps-for-your-organization"></a>Andere mogelijke stappen voor uw organisatie

Het is mogelijk dat uw organisatie extra acties vereist van abonnements eigenaren/beheerders om een app-registratie te kunnen instellen. De vereiste stappen kunnen variëren, afhankelijk van de specifieke instellingen van uw organisatie.

Hier volgen enkele veelvoorkomende mogelijke activiteiten die een eigenaar/beheerder van het abonnement mogelijk moet uitvoeren. Deze en andere bewerkingen kunnen worden uitgevoerd vanaf de pagina [*Azure AD-App registraties*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) in de Azure Portal.
* Beheerderstoestemming geven voor de app-registratie. Uw organisatie heeft mogelijk *toestemming van de beheerder nodig* die wereld wijd is ingeschakeld in azure AD voor alle app-registraties in uw abonnement. Als dit het geval is, moet de eigenaar/beheerder deze knop voor uw bedrijf selecteren op de pagina *API-machtigingen* van de app-registratie om de app-registratie geldig te maken:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Portal weergave van de knop ' toestemming verlenen aan beheerder ' onder API-machtigingen":::
  - Als toestemming is verleend, moet de vermelding voor Azure Digital Apparaatdubbels de *status* waarde van _verleende voor **(uw bedrijf)**_ weer geven
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Portal weergave van de beheerder toestemming verleend voor het bedrijf onder API-machtigingen":::
* Open bare client toegang activeren
* Specifieke antwoord-Url's instellen voor web-en bureaublad toegang
* Impliciete OAuth2-verificatie stromen toestaan

Zie [*een toepassing registreren bij het micro soft-identiteits platform*](/graph/auth-register-app-v2)voor meer informatie over app-registratie en de verschillende installatie opties.

## <a name="next-steps"></a>Volgende stappen

In dit artikel stelt u een Azure AD-App-registratie in die kan worden gebruikt om client toepassingen te verifiëren met de Azure Digital Apparaatdubbels-Api's.

Lees vervolgens de informatie over verificatie mechanismen, met inbegrip van app-registraties en andere die dat niet doen:
* [*Instructies: app-verificatie code schrijven*](how-to-authenticate-client.md)