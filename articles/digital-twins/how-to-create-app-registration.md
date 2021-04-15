---
title: Een app-registratie maken
titleSuffix: Azure Digital Twins
description: Zie Een Azure AD-app-registratie maken als verificatieoptie voor client-apps.
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bd45bb264f8e29a2aad870a7daff45fdd44e0d3c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478808"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Een app-registratie maken voor gebruik met Azure Digital Twins

Wanneer u met een Azure Digital Twins-instantie werkt, is het gebruikelijk om met dat exemplaar te communiceren via clienttoepassingen, zoals een aangepaste client-app of een voorbeeld zoals [Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md). Deze toepassingen moeten worden geverifieerd met Azure Digital Twins om erop te [](how-to-authenticate-client.md) kunnen communiceren. Sommige verificatiemechanismen die apps kunnen gebruiken, hebben betrekking op de registratie van een [Azure Active Directory-app (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md) 

Dit is niet vereist voor alle verificatiescenario's. Als u echter een verificatiestrategie of codevoorbeeld gebruikt waarvoor wel een app-registratie is vereist, met inbegrip van een **client-id** en **tenant-id,** wordt in dit artikel beschreven hoe u er een in kunt stellen.

## <a name="using-azure-ad-app-registrations"></a>Azure AD-app-registraties gebruiken

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) is de identiteits- en toegangsbeheerservice van Microsoft in de cloud. Het instellen van **een app-registratie** in Azure AD is één manier om een client-app toegang te verlenen tot Azure Digital Twins.

In deze app-registratie configureert u toegangsmachtigingen voor de [Azure Digital Twins API's](how-to-use-apis-sdks.md). Later kunnen client-apps worden geverifieerd aan de hand van de app-registratie met behulp van de **client-** en tenant-id-waarden van de registratie. Als gevolg hiervan worden de geconfigureerde toegangsmachtigingen voor de API's verleend.

>[!TIP]
> U kunt de voorkeur geven aan het instellen van een nieuwe app-registratie telkens wanneer u er een nodig *hebt,* of om dit slechts één keer te doen, door één app-registratie tot stand te laten komen die wordt gedeeld tussen alle scenario's waarvoor dit nodig is.

## <a name="create-the-registration"></a>De registratie maken

Navigeer eerst naar [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) in de Azure Portal (u kunt deze koppeling gebruiken of zoeken in de zoekbalk van de portal). Selecteer *App-registraties* in het servicemenu en vervolgens *+ Nieuwe registratie.*

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Weergave van de Azure AD-servicepagina in de Azure Portal, met de menuoptie 'App-registraties' en de knop '+ Nieuwe registratie'":::

Vul op *de pagina* Een toepassing registreren de aangevraagde waarden in:
* **Naam:** een weergavenaam van een Azure AD-toepassing die u aan de registratie wilt koppelen
* **Ondersteunde accounttypen:** selecteer *Alleen accounts in deze organisatiemap (alleen standaarddirectory - één tenant)*
* **Omleidings-URI:** een *antwoord-URL van een Azure AD-toepassing* voor de Azure AD-toepassing. Voeg een *openbare client-/native URI (& desktop)* toe voor `http://localhost` .

Wanneer u klaar bent, klikt u op de *knop* Registreren.

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Weergave van de pagina 'Een toepassing registreren' met de beschreven waarden ingevuld":::

Wanneer de registratie is voltooid, wordt u door de portal omgeleid naar de detailpagina.

## <a name="collect-client-id-and-tenant-id"></a>Client-id en tenant-id verzamelen

Verzamel vervolgens enkele belangrijke waarden over de app-registratie van de pagina met details:

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Portalweergave van de belangrijke waarden voor de app-registratie":::

Noteer de id van _**de toepassing (client)**_ en _**de map-id (tenant)**_ die op uw **pagina worden** weergegeven. Dit zijn de waarden die een client-app nodig heeft om deze registratie te gebruiken voor verificatie met Azure Digital Twins.

## <a name="provide-azure-digital-twins-api-permission"></a>Geef Azure Digital Twins API op

Configureer vervolgens de app-registratie die u hebt gemaakt met basislijnmachtigingen voor de Azure Digital Twins API's.

Selecteer op de portalpagina voor uw app-registratie *API-machtigingen* in het menu. Klik op de pagina met de volgende machtigingen op *de knop + Een machtiging* toevoegen.

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Weergave van de app-registratie in de Azure Portal, met de menuoptie 'API-machtigingen' en de knop '+ Een machtiging toevoegen'":::

Schakel op *de volgende pagina API-machtigingen* aanvragen over naar de API's die *mijn* organisatie gebruikt en zoek naar azure digital *twins.* Selecteer _**Azure Digital Twins**_ in de zoekresultaten om door te gaan met het toewijzen van machtigingen voor de Azure Digital Twins API's.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Weergave van het zoekresultaat 'API-machtigingen aanvragen' met Azure Digital Twins, met een toepassings-id (client) van 0b07f429-9f4b-4714-9392-cc5e8e80c8b0.":::

>[!NOTE]
> Als uw abonnement nog steeds een bestaand Azure Digital Twins-exemplaar van de vorige openbare preview van de service (vóór juli 2020) heeft, moet u in plaats daarvan _**Azure Smart Spaces Service**_ zoeken en selecteren. Dit is een oudere naam voor dezelfde set API's (u ziet dat de toepassings-id *(client)-id* hetzelfde is als in de bovenstaande schermopname. Uw ervaring wordt na deze stap niet gewijzigd.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Weergave van het zoekresultaat 'API-machtigingen aanvragen' met Azure Smart Spaces Service":::

Vervolgens selecteert u welke machtigingen u wilt verlenen voor deze API's. Vouw de **machtiging Lezen (1)** uit en vink het selectievakje *Read.Write* aan om deze app-registratielezer en schrijfmachtigingen te verlenen.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Weergave van de pagina 'API-machtigingen aanvragen' en machtigingen voor lezen.schrijven selecteren voor de Azure Digital Twins API's":::

Druk *op Machtigingen toevoegen wanneer* u klaar bent.

### <a name="verify-success"></a>Controleren geslaagd

Controleer op *de pagina API-machtigingen* of er nu een vermelding is voor Azure Digital Twins machtigingen voor lezen/schrijven:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Portalweergave van de API-machtigingen voor de registratie van de Azure AD-app, met 'Lees-/schrijftoegang' voor Azure Digital Twins":::

U kunt ook de verbinding met Azure Digital Twins controleren in demanifest.jsvan de app-registratie *op*, die automatisch werd bijgewerkt met de Azure Digital Twins-informatie toen u de API-machtigingen hebt toegevoegd.

Hiervoor selecteert u *Manifest in het* menu om de manifestcode van de app-registratie weer te geven. Schuif naar de onderkant van het codevenster en zoek naar deze velden onder `requiredResourceAccess` . De waarden moeten overeenkomen met die in de onderstaande schermopname:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Portalweergave van het manifest voor de registratie van de Azure AD-app. Genest onder 'requiredResourceAccess', er is een 'resourceAppId'-waarde van 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 en een 'resourceAccess > id'-waarde van 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

Als deze waarden ontbreken, kunt u de stappen in de sectie voor het toevoegen van de [API-machtiging opnieuw proberen.](#provide-azure-digital-twins-api-permission)

## <a name="other-possible-steps-for-your-organization"></a>Andere mogelijke stappen voor uw organisatie

Het is mogelijk dat uw organisatie aanvullende acties van abonnementseigenaren/-beheerders nodig heeft om een app-registratie in te stellen. De vereiste stappen kunnen variëren, afhankelijk van de specifieke instellingen van uw organisatie.

Hier volgen enkele veelvoorkomende mogelijke activiteiten die een eigenaar/beheerder van het abonnement mogelijk moet uitvoeren. Deze en andere bewerkingen kunnen worden uitgevoerd vanaf de [*pagina Azure AD-app registraties*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) in de Azure Portal.
* Beheerderstoestemming geven voor de app-registratie. Voor alle app-registraties binnen uw abonnement *is* in uw organisatie mogelijk beheerders toestemming vereist globaal ingeschakeld in Azure AD. Zo ja, dan moet de eigenaar/beheerder deze knop voor uw bedrijf selecteren op de *pagina API-machtigingen* voor de app-registratie, zodat de app-registratie geldig is:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Portalweergave van de knop 'Beheerders toestemming verlenen' onder API-machtigingen":::
  - Als toestemming is verleend, moet de vermelding Azure Digital Twins de *statuswaarde* _Verleend voor **(uw bedrijf) weergeven**_
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Portalweergave van de beheerders toestemming die voor het bedrijf is verleend onder API-machtigingen":::
* Openbare clienttoegang activeren
* Specifieke antwoord-URL's instellen voor web- en desktoptoegang
* Impliciete OAuth2-verificatiestromen toestaan

Zie Een toepassing registreren bij het Microsoft Identity Platform voor meer informatie over app-registratie en de verschillende [*installatieopties.*](/graph/auth-register-app-v2)

## <a name="next-steps"></a>Volgende stappen

In dit artikel stelt u een Azure AD-app-registratie in die kan worden gebruikt voor het verifiëren van clienttoepassingen met de Azure Digital Twins API's.

Lees vervolgens meer over verificatiemechanismen, waaronder een mechanisme dat gebruikmaakt van app-registraties en andere die dat niet doen:
* [*How-to: Write app authentication code (App-verificatiecode schrijven)*](how-to-authenticate-client.md)