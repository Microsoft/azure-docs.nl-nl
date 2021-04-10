---
title: Zelf studie voor het configureren van Saviynt met Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Zelf studie voor het configureren van Azure Active Directory B2C met Saviynt voor integratie van meerdere toepassingen om IT-modernisatie te stroom lijnen en betere beveiliging, governance en naleving te bevorderen. 
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b1889a693c08ce78a8bd06b6fe348c0c67bf3812
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103465671"
---
# <a name="tutorial-for-configuring-saviynt-with-azure-active-directory-b2c"></a>Zelf studie voor het configureren van Saviynt met Azure Active Directory B2C

In deze voorbeeld zelfstudie bieden we richt lijnen voor het integreren van Azure Active Directory (AD) B2C met [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/). Het beveiligings beheer platform van Saviynt biedt de zicht baarheid, de beveiliging en het beheer van de huidige bedrijven, in één uniform platform. Saviynt maakt gebruik van toepassings Risico's en governance, infrastructuur beheer, Privileged account management en risico analyse van klanten.

In deze voorbeeld zelf studie stelt u Saviynt in om het gedelegeerde beheer op basis van toegangs beheer voor Azure AD B2C gebruikers nauw keurig te maken. Saviynt controleert het volgende om te bepalen of een gebruiker gemachtigd is om Azure AD B2C gebruikers te beheren.

- Beveiliging op onderdeel niveau om te bepalen of een gebruiker een bepaalde bewerking kan uitvoeren. U kunt bijvoorbeeld gebruiker maken, gebruiker bijwerken, gebruikers wachtwoord opnieuw instellen, enzovoort.

- Beveiliging op veld niveau om te bepalen of een gebruiker een specifiek kenmerk van een andere gebruiker kan lezen/schrijven tijdens gebruikers beheer bewerkingen. Helpdesk medewerker kan bijvoorbeeld alleen het telefoon nummer bijwerken en alle andere kenmerken hebben het kenmerk alleen-lezen.

- Beveiliging op gegevens niveau om te bepalen of een gebruiker een bepaalde bewerking voor een specifieke gebruiker kan uitvoeren. De Help Desk Administrator voor de UK-regio kan bijvoorbeeld alleen UK-gebruikers beheren.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

- Een [Azure AD B2C-Tenant](./tutorial-create-tenant.md). De Tenant is gekoppeld aan uw Azure-abonnement.

- Een Saviynt- [abonnement](https://saviynt.com/contact-us/)

## <a name="scenario-description"></a>Scenariobeschrijving

De Saviynt-integratie bevat de volgende onderdelen:

- [Azure AD B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) : de identiteit van de Business-to-Customer als een service waarmee uw klanten zich kunnen aanmelden, aanmelden en hun profielen beheren.

- [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/) : het platform voor identiteits beheer dat verzorgt voor het beheer van de levens cyclus van gebruikers en toegangs governance van Azure AD B2C gebruikers.  

- [Microsoft Graph-API](/graph/use-the-api) : deze API biedt de interfaces voor Saviynt om de Azure AD B2C gebruikers en hun toegang in azure AD B2C te beheren.

In het volgende architectuur diagram wordt de implementatie weer gegeven.

![Afbeelding met saviynt-architectuur diagram](./media/partner-saviynt/saviynt-architecture-diagram.png)

|Stap | Beschrijving |
|:-----| :-----------|
| 1. | Een gedelegeerde beheerder start een Azure AD B2C gebruikers bewerking beheren via Saviynt.
| 2. | Saviynt controleert de autorisatie-Engine of de gedelegeerde beheerder de specifieke bewerking kan uitvoeren.
| 3. | Met de autorisatie-engine van Saviynt wordt een autorisatie verzonden die is geslaagd/mislukt.
| 4. | Met Saviynt kan de gedelegeerde beheerder de vereiste bewerking uitvoeren.
| 5. | Saviynt roept Microsoft Graph-API aan met gebruikers kenmerken voor het beheren van de gebruiker in Azure AD B2C
| 6. | Met Microsoft Graph-API wordt de gebruiker in Azure AD B2C gemaakt/bijwerken/verwijderen.
| 7. | Azure AD B2C stuurt een reactie op geslaagd/mislukt.
| 8. | Microsoft Graph-API retourneert vervolgens de reactie op Saviynt.

## <a name="onboard-with-saviynt"></a>Onboard met Saviynt

1. Als u een Saviynt-account wilt maken, neemt u contact op met [Saviynt](https://saviynt.com/contact-us/)

2. Maak gedelegeerd beheer beleid en wijs gebruikers toe als gedelegeerd beheerder met verschillende rollen.

## <a name="configure-azure-ad-b2c-with-saviynt"></a>Azure AD B2C configureren met Saviynt

### <a name="create-an-azure-ad-application-for-saviynt"></a>Een Azure AD-toepassing maken voor Saviynt

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/#home).

2. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.

3. Zoek in het Azure Portal naar en selecteer **Azure AD B2C**.

4. Selecteer **app-registraties**  >  **nieuwe registratie**.

5. Voer een naam in voor de toepassing. Bijvoorbeeld Saviynt en selecteer **maken**.

6. Ga naar **API-machtigingen** en selecteer **+ een machtiging toevoegen.**

7. De pagina API-machtigingen voor aanvragen wordt weer gegeven. Selecteer het tabblad **micro soft-api's** en selecteer **Microsoft Graph** als veelgebruikte micro soft-api's.

8. Ga naar de volgende pagina en selecteer **toepassings machtigingen**.

9. Selecteer **Directory** en selecteer **Directory. Read. all** en **map. readwrite. all** in het selectie vakje.

10. Selecteer **machtigingen toevoegen**. Controleer de toegevoegde machtigingen.

11. Selecteer **toestemming geven voor de beheerder voor standaard mappen**  >  **Opslaan**.

12. Ga naar **certificaten en geheimen** en selecteer **+ client geheim toevoegen**. Voer de beschrijving van het client geheim in, selecteer de optie verloop datum en selecteer **toevoegen**.

13. De geheime sleutel wordt gegenereerd en weer gegeven in de sectie client Secret.

    >[!NOTE]
    > U hebt het client geheim later nodig.

14. Ga naar **overzicht** en haal de **client-ID** en **Tenant-id** op.

15. De Tenant-ID, client-ID en client geheim zijn vereist voor het volt ooien van de installatie in Saviynt.

### <a name="enable-saviynt-to-delete-users"></a>Saviynt inschakelen om gebruikers te verwijderen

In de onderstaande stappen wordt uitgelegd hoe u Saviynt kunt gebruiken om bewerkingen voor het verwijderen van gebruikers uit te voeren in Azure AD B2C.

>[!NOTE]
>[Evalueer het risico voordat beheerders rollen toegang verlenen tot een service-principal.](../active-directory/develop/app-objects-and-service-principals.md)

1. Installeer de meest recente versie van de MSOnline Power shell-module op een Windows-werk station/-server.

2. Maak verbinding met de AzureAD Power shell-module en voer de volgende opdrachten uit:

```powershell
Connect-msolservice #Enter Admin credentials of the Azure portal
$webApp = Get-MsolServicePrincipal –AppPrincipalId “<ClientId of Azure AD Application>”
Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType ServicePrincipal -RoleMemberObjectId $webApp.ObjectId
```

## <a name="test-the-solution"></a>De oplossing testen

Blader naar de Tenant van uw Saviynt-toepassing en test het beheer van de levens cyclus van gebruikers en gebruik de toegangs aanvraag.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Aangepast beleid in Azure AD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepast beleid in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)

- [Een web-API-toepassing maken](./add-web-api-application.md)