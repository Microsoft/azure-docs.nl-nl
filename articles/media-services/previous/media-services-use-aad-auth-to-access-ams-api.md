---
title: Toegang tot Azure Media Services-API met Azure Active Directory-verificatie | Microsoft Docs
description: Meer informatie over concepten en stappen voor het gebruik van Azure Active Directory (Azure AD) voor het verifiëren van toegang tot de Azure Media Services-API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: a26359c46caa8085384313f1c385c5740abc0b32
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98694532"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Toegang tot de API van Azure Media Services met Azure Active Directory-verificatie 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

De Azure Media Services-API is een REST API. U kunt deze gebruiken om bewerkingen uit te voeren op media bronnen met behulp van een REST API of door beschik bare client-Sdk's te gebruiken. Azure Media Services biedt een Media Services client-SDK voor Microsoft .NET. Om te worden geautoriseerd voor toegang tot Media Services-resources en de API van Media Services, moet u eerst worden geverifieerd. 

Media Services ondersteunt [Azure Active Directory (op Azure AD) gebaseerde verificatie](../../active-directory/fundamentals/active-directory-whatis.md). De Azure media REST-service vereist dat de gebruiker of toepassing die de REST API aanvragen maakt, de rol **Inzender** of **eigenaar** heeft om toegang te krijgen tot de resources. Zie [Wat is Azure Role-based Access Control (Azure RBAC)](../../role-based-access-control/overview.md)voor meer informatie.  

Dit document bevat een overzicht van de manier waarop u toegang hebt tot de Media Services-API met behulp van REST-of .NET-Api's.

> [!NOTE]
> Access Control autorisatie is afgeschaft op 1 juni 2018.

## <a name="access-control"></a>Toegangsbeheer

Voor een succes volle Azure media-aanvraag moet de aanroepende gebruiker een rol Inzender of eigenaar hebben voor het Media Services account dat toegang probeert te krijgen.  
Alleen een gebruiker met de rol eigenaar kan media resource (account) toegang geven tot nieuwe gebruikers of apps. De rol Inzender heeft alleen toegang tot de media bron.
Niet-geautoriseerde aanvragen zijn mislukt, met de status code 401. Als u deze fout code ziet, controleert u of uw gebruiker de rol Inzender of eigenaar heeft toegewezen voor het Media Services account van de gebruiker. U kunt dit controleren in de Azure Portal. Zoek naar uw media account en klik vervolgens op het tabblad **toegangs beheer** . 

![Tabblad toegangs beheer](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Verificatie typen 
 
Wanneer u Azure AD-verificatie met Azure Media Services gebruikt, hebt u twee verificatie opties:

- **Gebruikers verificatie**. Verifieer een persoon die de app gebruikt om te communiceren met Media Services-resources. De interactieve toepassing moet eerst de gebruiker vragen naar de referenties van de gebruiker. Een voorbeeld is een beheerconsole-app die door geautoriseerde gebruikers wordt gebruikt om coderingstaken of livestreams te bewaken. 
- **Service-Principal-verificatie**. Verifieer een service. Toepassingen die meestal gebruikmaken van deze verificatiemethode, zijn apps waarmee daemonservices, services uit de middelste laag, of geplande taken worden uitgevoerd. Voor beelden zijn web-apps, functie-apps, Logic apps, API en micro Services.

### <a name="user-authentication"></a>Gebruikersverificatie 

Toepassingen die gebruikmaken van de verificatie methode van de gebruiker zijn onder andere beheer of bewaking van systeem eigen apps: mobiele apps, Windows-apps en console toepassingen. Dit type oplossing is handig als u wilt dat mensen interactie met de service in een van de volgende scenario's:

- Bewakings dashboard voor uw coderings taken.
- Bewakings dashboard voor uw live streams.
- Beheer toepassing voor desktop-of mobiele gebruikers om resources te beheren in een Media Services-account.

> [!NOTE]
> Deze verificatie methode mag niet worden gebruikt voor consument gerichte toepassingen. 

Een systeem eigen toepassing moet eerst een toegangs token van Azure AD verkrijgen en dit vervolgens gebruiken wanneer u HTTP-aanvragen maakt voor de Media Services REST API. Voeg het toegangs token toe aan de aanvraag header. 

In het volgende diagram ziet u een typische verificatie stroom voor interactieve toepassingen: 

![Diagram van systeem eigen apps](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

In het voor gaande diagram vertegenwoordigt de getallen de stroom van de aanvragen in chronologische volg orde.

> [!NOTE]
> Wanneer u de verificatie methode van de gebruiker gebruikt, delen alle apps dezelfde standaard client-ID voor de toepassing en de omleidings-URI van de systeem eigen toepassing. 

1. Een gebruiker vragen om referenties.
2. Een Azure AD-toegangs token aanvragen met de volgende para meters:  

   * Azure AD-Tenant eindpunt.

       De gegevens van de Tenant kunnen worden opgehaald uit de Azure Portal. Plaats de cursor op de naam van de aangemelde gebruiker in de rechter bovenhoek.
   * Media Services resource-URI. 

       Deze URI is hetzelfde voor Media Services accounts die zich in dezelfde Azure-omgeving (bijvoorbeeld https: \/ /rest.media.Azure.net) bevinden.

   * Media Services (systeem eigen) client-ID van toepassing.
   * Media Services (systeem eigen) URI voor omleiding van toepassing.
   * Resource-URI voor REST-Media Services.
        
       De URI vertegenwoordigt het REST API-eind punt (bijvoorbeeld https://test03.restv2.westus.media.azure.net/api/) .

     Zie [de Azure Portal gebruiken om toegang te krijgen tot Azure AD-verificatie-instellingen](media-services-portal-get-started-with-aad.md) met behulp van de optie gebruikers verificatie om waarden voor deze para meters op te halen.

3. Het Azure AD-toegangs token wordt verzonden naar de client.
4. De client verzendt een aanvraag naar de Azure media REST API met het Azure AD-toegangs token.
5. De client haalt de gegevens terug van Media Services.

Zie [Azure AD-verificatie gebruiken om toegang te krijgen tot de Media Services-API met .net](media-services-dotnet-get-started-with-aad.md)voor informatie over het gebruik van Azure AD-verificatie om te communiceren met rest-aanvragen met behulp van de Media Services .net-client-SDK. 

Als u de Media Services .NET-client-SDK niet gebruikt, moet u hand matig een Azure AD-toegangs token aanvraag maken met behulp van de para meters die zijn beschreven in stap 2. Zie [de Azure AD-verificatie bibliotheek gebruiken om het Azure AD-token op te halen](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)voor meer informatie.

### <a name="service-principal-authentication"></a>Verificatie van service-principal

Toepassingen die meestal gebruikmaken van deze verificatie methode zijn apps die services voor de middelste laag en geplande taken uitvoeren: Web apps, functie-apps, Logic apps, Api's en micro Services. Deze verificatie methode is ook geschikt voor interactieve toepassingen waarin u mogelijk een service account wilt gebruiken om resources te beheren.

Wanneer u de Service-Principal-verificatie methode gebruikt om consumenten scenario's samen te stellen, wordt de verificatie doorgaans verwerkt in de middelste laag (via sommige API'S) en niet rechtstreeks in een mobiele of bureaublad toepassing. 

Als u deze methode wilt gebruiken, maakt u een Azure AD-toepassing en Service-Principal in een eigen Tenant. Nadat u de toepassing hebt gemaakt, geeft u de rol app Inzender of eigenaar toegang tot het Media Services-account. U kunt dit doen in de Azure Portal, met behulp van de Azure CLI of met een Power shell-script. U kunt ook een bestaande Azure AD-toepassing gebruiken. U kunt uw Azure AD-app en Service-Principal registreren en beheren [in de Azure Portal](media-services-portal-get-started-with-aad.md). U kunt dit ook doen met behulp van [Azure cli](media-services-use-aad-auth-to-access-ams-api.md) of [Power shell](media-services-powershell-create-and-configure-aad-app.md). 

![Toepassingen op middelste laag](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Nadat u uw Azure AD-toepassing hebt gemaakt, krijgt u waarden voor de volgende instellingen. U hebt deze waarden nodig voor verificatie:

- Client-id 
- Clientgeheim 

In de voor gaande afbeelding vertegenwoordigen de getallen de stroom van de aanvragen in chronologische volg orde:
    
1. Een middelste app (Web API of Web Application) vraagt een Azure AD-toegangs token met de volgende para meters:  

   * Azure AD-Tenant eindpunt.

       De gegevens van de Tenant kunnen worden opgehaald uit de Azure Portal. Plaats de cursor op de naam van de aangemelde gebruiker in de rechter bovenhoek.
   * Media Services resource-URI. 

       Deze URI is hetzelfde voor Media Services accounts die zich in dezelfde Azure-omgeving bevinden (bijvoorbeeld https: \/ /rest.media.Azure.net).

   * Resource-URI voor REST-Media Services.

       De URI vertegenwoordigt het REST API-eind punt (bijvoorbeeld https://test03.restv2.westus.media.azure.net/api/) .

   * Azure AD-toepassings waarden: de client-ID en het client geheim.
    
     Als u waarden voor deze para meters wilt ophalen, raadpleegt u [de Azure Portal gebruiken om toegang te krijgen tot Azure AD-verificatie-instellingen](media-services-portal-get-started-with-aad.md) met behulp van de optie Service-Principal-verificatie

2. Het Azure AD-toegangs token wordt verzonden naar de middelste laag.
4. De middelste laag verzendt een aanvraag naar de Azure media-REST API met het Azure AD-token.
5. De middelste laag krijgt een back-up van de gegevens van Media Services.

Zie [Azure AD-verificatie gebruiken om toegang te krijgen tot Azure Media Services API met .net](media-services-dotnet-get-started-with-aad.md)voor meer informatie over het gebruik van Azure AD-verificatie om te communiceren met rest-aanvragen met behulp van de Media Services .net-client-SDK. 

Als u de Media Services .NET-client-SDK niet gebruikt, moet u hand matig een Azure AD-token aanvraag maken met behulp van para meters die worden beschreven in stap 1. Zie [de Azure AD-verificatie bibliotheek gebruiken om het Azure AD-token op te halen](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)voor meer informatie.

## <a name="troubleshooting"></a>Problemen oplossen

Uitzonde ring: de externe server heeft een fout geretourneerd: (401) niet toegestaan.

Oplossing: voor de Media Services REST-aanvraag om te slagen, moet de aanroepende gebruiker een rol Inzender of eigenaar zijn in het Media Services-account dat toegang probeert te krijgen. Zie de sectie [toegangs beheer](media-services-use-aad-auth-to-access-ams-api.md#access-control) voor meer informatie.

## <a name="resources"></a>Resources

De volgende artikelen zijn overzichten van de concepten van Azure AD-verificatie: 

- [Verificatie scenario's die worden behandeld door Azure AD](../../active-directory/develop/authentication-vs-authorization.md)
- [Een toepassing toevoegen, bijwerken of verwijderen in azure AD](../../active-directory/develop/quickstart-register-app.md)
- [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Volgende stappen

* Gebruik de Azure Portal om [toegang te krijgen tot Azure AD-verificatie om Azure Media Services API te](media-services-portal-get-started-with-aad.md)gebruiken.
* Gebruik Azure AD-verificatie om [toegang te krijgen tot Azure Media Services-API met .net](media-services-dotnet-get-started-with-aad.md).
