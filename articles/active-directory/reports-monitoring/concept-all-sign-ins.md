---
title: Rapporten voor aanmeldings activiteiten Azure Active Directory-preview | Microsoft Docs
description: Inleiding tot de rapporten voor aanmeldings activiteiten in de Azure Active Directory Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/16/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: b17e7f34da1428eec219a393417b0a2a7b461078
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552730"
---
# <a name="azure-active-directory-sign-in-activity-reports---preview"></a>Rapporten over Azure Active Directory-aanmeld activiteiten-preview

De rapportage architectuur in Azure Active Directory (Azure AD) bestaat uit de volgende onderdelen:

- **Activiteit** 
    - **Aanmeldingen** : informatie over wanneer gebruikers, toepassingen en beheerde bronnen zich aanmelden bij Azure AD en toegang krijgen tot resources.
    - **Audit logboeken**  -  [Audit logboeken](concept-audit-logs.md) bevatten informatie over de systeem activiteit van gebruikers en groeps beheer, beheerde toepassingen en Directory-activiteiten.
- **Beveiliging** 
    - **Risk ante aanmeldingen** : een [Risk ante aanmelding](../identity-protection/overview-identity-protection.md) is een indicator voor een aanmeldings poging door iemand die niet de rechtmatige eigenaar van een gebruikers account is.
    - **Gebruikers die zijn gemarkeerd voor risico** : een [Risk ante gebruiker](../identity-protection/overview-identity-protection.md) is een indicator voor een gebruikers account dat mogelijk is aangetast.

Het klassieke aanmeld rapport in Azure Active Directory biedt een overzicht van interactieve gebruikers aanmeldingen. Daarnaast hebt u nu toegang tot drie extra aanmeldings rapporten die nu als preview-versie beschikbaar zijn:

- Niet-interactieve gebruikers aanmeldingen

- Service-Principal-aanmeldingen

- Beheerde identiteiten voor Azure-resource aanmeldingen

In dit artikel vindt u een overzicht van het rapport voor de aanmeldings activiteit met de preview-versie van niet-interactieve, toepassings-en beheerde identiteiten voor aanmeldingen bij Azure-resources. Zie voor meer informatie over het aanmeldings rapport zonder de preview-functies  [aanmeldings activiteiten rapporten in de Azure Active Directory Portal](concept-sign-ins.md).



## <a name="prerequisites"></a>Vereisten

Voordat u deze functie kunt gebruiken, moet u de antwoorden op het volgende weten:

- Wie hebben er toegang tot de gegevens?

- Welke Azure AD-licentie heb ik nodig voor toegang tot aanmeldingsactiviteiten?

### <a name="who-can-access-the-data"></a>Wie hebben er toegang tot de gegevens?

- Gebruikers in de beveiligings beheerder, beveiligings lezer en rapport lezers rollen

- Globale beheerders

- Alle gebruiker (niet-beheerders) hebben toegang tot hun eigen aanmeldingen 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Welke Azure AD-licentie heb ik nodig voor toegang tot aanmeldingsactiviteiten?

Aan uw Tenant moet een Azure AD Premium-licentie zijn gekoppeld om aanmeldings activiteiten te kunnen zien. Zie [Aan de slag met Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) om uw versie van Azure Active Directory te upgraden. Het duurt enkele dagen voordat de gegevens in de rapporten worden weer gegeven nadat u een upgrade hebt uitgevoerd naar een Premium-licentie zonder gegevens activiteiten vóór de upgrade.



## <a name="sign-ins-report"></a>Aanmeldingenrapport

Het rapport met aanmeldingen bevat antwoorden op de volgende vragen:

- Wat is het aanmeldings patroon van een gebruiker, toepassing of service?
- Hoeveel gebruikers, apps of services hebben zich gedurende een week aangemeld?
- Wat is de status van deze aanmeldingen?


Op de Blade rapport voor aanmeldingen kunt u scha kelen tussen:

- **Interactieve aanmeldingen van gebruikers** -aanmeldingen waarbij een gebruiker een verificatie factor heeft, zoals een wacht woord, een reactie via een MFA-app, een biometrische factor of een QR-code.

- **Niet-interactieve aanmeldingen van gebruikers** -aanmeldingen die door een client namens een gebruiker worden uitgevoerd. Voor deze aanmeldingen is geen interactie of verificatie factor van de gebruiker vereist. Bijvoorbeeld: authenticatie en autorisatie met behulp van vernieuwen en toegangs tokens waarvoor geen gebruiker referenties hoeft in te voeren.

- **Service-Principal-aanmeldingen** : aanmeldingen per apps en service-principals die geen gebruik maken van een gebruiker. In deze aanmeldingen biedt de app of service een referentie voor het verifiëren of openen van bronnen.

- **Beheerde identiteiten voor Azure-bronnen aanmeldingen** : aanmeldingen door Azure-resources met geheimen die worden beheerd door Azure. Zie [Wat zijn beheerde identiteiten voor Azure-resources?](../managed-identities-azure-resources/overview.md) voor meer informatie. 


![Typen aanmeld rapporten](./media/concept-all-sign-ins/sign-ins-report-types.png)












## <a name="user-sign-ins&quot;></a>Gebruikers aanmeldingen

Op elk tabblad van de Blade aanmeldingen worden de onderstaande standaard kolommen weer gegeven. Sommige tabbladen hebben extra kolommen:

- Aanmeldingsdatum

- Aanvraag-id

- Gebruikers naam of gebruikers-ID

- Toepassings naam of toepassings-ID

- Status van de aanmelding

- IP-adres van het apparaat dat wordt gebruikt voor de aanmelding



### <a name=&quot;interactive-user-sign-ins&quot;></a>Interactieve gebruikers aanmeldingen


Interactieve gebruikers aanmeldingen zijn aanmeldingen wanneer een gebruiker een verificatie factor aan Azure AD biedt of rechtstreeks met Azure AD of een helper-app werkt, zoals de app Microsoft Authenticator. De factoren die gebruikers bevatten, zijn wacht woorden, reacties op MFA-uitdagingen, biometrische factoren of QR-codes die een gebruiker aan Azure AD of een helper-app biedt.

> [!NOTE]
> Dit rapport bevat ook federatieve aanmeldingen van id-providers die federatieve zijn voor Azure AD.  



Opmerking: het rapport interactieve gebruikers aanmelding wordt gebruikt om niet-interactieve aanmeldingen van micro soft Exchange-clients te bevatten. Hoewel deze aanmeldingen niet interactief waren, zijn ze opgenomen in het rapport interactieve gebruikers aanmeldingen voor meer zicht baarheid. Zodra het rapport voor niet-interactieve gebruikers aanmeldingen in november 2020 een open bare preview heeft ingevoerd, zijn die niet-interactieve gebeurtenis logboeken naar het niet-interactieve gebruikers aanmeld rapport verplaatst voor een grotere nauw keurigheid. 


**Rapport grootte:** klein <br> 
**Vindt**

- Een gebruiker geeft gebruikers naam en wacht woord op in het aanmeldings scherm van Azure AD.

- Een gebruiker geeft een SMS MFA-uitdaging door.

- Een gebruiker beschikt over biometrische bewegingen om hun Windows-PC te ontgrendelen met Windows hello voor bedrijven.

- Een gebruiker is federatieve naar Azure AD met een AD FS SAML-verklaring.


Naast de standaard velden wordt in het rapport interactieve aanmeldingen ook het volgende weer gegeven: 

- De aanmeldings locatie

- Of voorwaardelijke toegang is toegepast



U kunt de lijstweergave aanpassen door te klikken op **Kolommen** op de werkbalk.

![Interactieve aanmeldings kolommen van gebruikers](./media/concept-all-sign-ins/columns-interactive.png &quot;Interactieve aanmeldings kolommen van gebruikers")





Door de weer gave aan te passen kunt u extra velden weer geven of velden verwijderen die al worden weer gegeven.

![Alle interactieve kolommen](./media/concept-all-sign-ins/all-interactive-columns.png)


Selecteer een item in de lijst weergave voor meer gedetailleerde informatie over de gerelateerde aanmelding.

![Aanmeldings activiteit](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "Interactieve gebruikers aanmeldingen")



### <a name="non-interactive-user-sign-ins"></a>Niet-interactieve gebruikers aanmeldingen

Niet-interactieve gebruikers aanmeldingen zijn aanmeldingen die zijn uitgevoerd door een client-app of besturingssysteem onderdelen namens een gebruiker. Net als bij interactieve aanmeldingen voor gebruikers worden deze aanmeldingen uitgevoerd namens een gebruiker. In tegens telling tot interactieve gebruikers aanmeldingen, is de gebruiker niet verplicht om een verificatie factor op te geven. In plaats daarvan gebruikt het apparaat of de client-app een token of code voor het verifiëren of openen van een resource namens een gebruiker. Over het algemeen worden deze aanmeldingen op de achtergrond van de activiteit van de gebruiker door de gebruiker waargenomen.


**Rapport grootte:** Lange <br>
**Voorbeelden:** 

- Een client-app gebruikt een OAuth 2,0-vernieuwings token om een toegangs token op te halen.

- Een client gebruikt een OAuth 2,0-autorisatie code voor het ophalen van een toegangs token en een vernieuwings token.

- Een gebruiker voert eenmalige aanmelding (SSO) uit voor een web-of Windows-app op een aan Azure AD gekoppelde PC.

- Een gebruiker meldt zich aan bij een tweede Microsoft Office-app terwijl ze een sessie op een mobiel apparaat hebben met FOCI (familie of client-Id's).




Naast de standaard velden wordt in het rapport niet-interactieve aanmeldingen ook het volgende weer gegeven: 

- Resource-id

- Aantal gegroepeerde aanmeldingen




U kunt de velden die in dit rapport worden weer gegeven, niet aanpassen.


![Uitgeschakelde kolommen](./media/concept-all-sign-ins/disabled-columns.png "Uitgeschakelde kolommen")

Om de samen vatting van de gegevens gemakkelijker te maken, worden niet-interactieve aanmeldings gebeurtenissen gegroepeerd. Clients maken vaak veel niet-interactieve aanmeldingen namens dezelfde gebruiker binnen een korte periode, die dezelfde kenmerken delen, behalve het tijdstip waarop de aanmelding werd geprobeerd. Een client kan bijvoorbeeld één keer per uur een toegangs token ontvangen namens een gebruiker. Als de gebruiker of client de status niet wijzigt, zijn het IP-adres, de bron en alle andere gegevens hetzelfde voor elke toegangs token aanvraag. Wanneer Azure AD meerdere aanmeldingen registreert die identiek zijn, zijn de aanmeldingen van dezelfde entiteit en worden ze samengevoegd tot één rij. Een rij met meerdere identieke aanmeldingen (met uitzonde ring van datum en tijd) heeft een waarde die groter is dan 1 in de kolom # aanmeldingen. U kunt de rij uitbreiden om alle verschillende aanmeldingen en hun verschillende tijds tempels weer te geven. Aanmeldingen worden in de niet-interactieve gebruikers geaggregeerd wanneer de volgende gegevens overeenkomen:


- Toepassing

- Gebruiker

- IP-adres

- Status

- Resource-id


U kunt:

- Vouw een knoop punt uit om de afzonderlijke items van een groep weer te geven.  

- Klik op een afzonderlijk item om alle details te bekijken 


![Details van niet-interactieve gebruikers aanmelding](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>Service-Principal-aanmeldingen

In tegens telling tot interactieve en niet-interactieve gebruikers aanmeldingen, is voor Service-Principal-aanmeldingen geen gebruiker vereist. In plaats daarvan zijn ze aanmeldingen via een niet-gebruikers account, zoals apps of service-principals (met uitzonde ring van beheerde identiteits aanmelding, die alleen zijn opgenomen in het rapport beheerde identiteits registraties). In deze aanmeldingen biedt de app of service een eigen referentie, zoals een certificaat of app-geheim voor het verifiëren of openen van bronnen.


**Rapport grootte:** Lange <br>
**Voorbeelden:**

- Een Service-Principal gebruikt een certificaat om de Microsoft Graph te verifiëren en te openen. 

- Een toepassing maakt gebruik van een client geheim om te verifiëren in de gegevens stroom van de OAuth-client. 


Dit rapport bevat een standaard lijst weergave waarin het volgende wordt weer gegeven:

- Aanmeldingsdatum

- Aanvraag-id

- Principal-naam of-ID van service

- Status

- IP-adres

- Resourcenaam

- Resource-id

- Aantal aanmeldingen

U kunt de velden die in dit rapport worden weer gegeven, niet aanpassen.

![Uitgeschakelde kolommen](./media/concept-all-sign-ins/disabled-columns.png "Uitgeschakelde kolommen")

Voor het gemakkelijker maken van de samen vatting van de gegevens in de logboeken van de Service-Principal, worden aanmeldings gebeurtenissen voor de Service-Principal gegroepeerd. Aanmeldingen van dezelfde entiteit onder dezelfde voor waarden worden samengevoegd tot één rij. U kunt de rij uitbreiden om alle verschillende aanmeldingen en hun verschillende tijds tempels weer te geven. Aanmeldingen worden in het Service-Principal-rapport geaggregeerd wanneer de volgende gegevens overeenkomen:

- Principal-naam of-ID van service

- Status

- IP-adres

- Resource naam of-ID

U kunt:

- Vouw een knoop punt uit om de afzonderlijke items van een groep weer te geven.  

- Klik op een afzonderlijk item om alle details te bekijken 


![Kolom Details](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "Kolom Details")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Beheerde identiteit voor Azure-bronnen aanmeldingen 

Beheerde identiteit voor Azure-bronnen aanmeldingen zijn aanmeldingen die zijn uitgevoerd door resources die hun geheimen hebben beheerd door Azure om het beheer van referenties te vereenvoudigen.

**Rapport grootte:** Geringe <br> 
**Vindt**

Een virtuele machine met beheerde referenties maakt gebruik van Azure AD om een toegangs token op te halen.   


Dit rapport bevat een standaard lijst weergave waarin het volgende wordt weer gegeven:


- ID beheerde identiteit

- Naam beheerde identiteit

- Resource

- Resource-id

- Aantal gegroepeerde aanmeldingen

U kunt de velden die in dit rapport worden weer gegeven, niet aanpassen.

Voor het gemakkelijker maken van de samen vatting van de gegevens, beheerde identiteiten voor Azure-bronnen aanmeld logboeken, worden niet-interactieve aanmeld gebeurtenissen gegroepeerd. Aanmeldingen van dezelfde entiteit worden samengevoegd tot één rij. U kunt de rij uitbreiden om alle verschillende aanmeldingen en hun verschillende tijds tempels weer te geven. Aanmeldingen worden in het rapport beheerde identiteiten geaggregeerd wanneer alle volgende gegevens overeenkomen:

- Naam of ID van beheerde identiteit

- Status

- IP-adres

- Resource naam of-ID

Selecteer een item in de lijst weergave om alle aanmeldingen weer te geven die zijn gegroepeerd onder een knoop punt.

Selecteer een gegroepeerd item om alle details van de aanmelding weer te geven. 


## <a name="filter-sign-in-activities"></a>Aanmeldactiviteiten filteren

Door een filter in te stellen, kunt u het bereik van de geretourneerde aanmeldings gegevens beperken. Azure AD biedt u een breed scala aan extra filters die u kunt instellen. Wanneer u het filter instelt, moet u altijd speciale aandacht schenken aan uw geconfigureerde **datum** bereik filter. Een correct datum bereik filter zorgt ervoor dat Azure AD alleen de gegevens retourneert die u echt bevalt.     

Met het filter **datum** bereik kunt u een tijds bestek voor de geretourneerde gegevens definiëren.
Mogelijke waarden zijn:

- Eén maand

- Zeven dagen

- Eenentwintig uur

- Aangepast

![Filter datum bereik](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>Gebruikers aanmeldingen filteren

Het filter voor interactieve en niet-interactieve aanmeldingen is hetzelfde. Als gevolg hiervan wordt het filter dat u voor interactieve aanmeldingen hebt geconfigureerd, persistent gemaakt voor niet-interactieve aanmeldingen en omgekeerd. 






## <a name="access-the-new-sign-in-activity-reports"></a>De rapporten van de nieuwe aanmeldings activiteiten openen 

Het activiteiten rapport voor aanmeldingen in de Azure Portal biedt u een eenvoudige methode om het preview-rapport in of uit te scha kelen. Als u de preview-rapporten hebt ingeschakeld, krijgt u een nieuw menu dat u toegang geeft tot alle typen registratie-activiteiten rapporten.     


Om toegang te krijgen tot de nieuwe aanmeldings rapporten met niet-interactieve en toepassings aanmeldingen: 

1. Selecteer **Azure Active Directory** in de [Azure-portal](https://portal.azure.com).

    ![Selecteer Azure AD](./media/concept-all-sign-ins/azure-services.png)

2. Klik in de sectie **bewaking** op **aanmeldingen**.

    ![Aanmeldingen selecteren](./media/concept-all-sign-ins/sign-ins.png)

3. Klik op de **Preview** -balk.

    ![Nieuwe weer gave inschakelen](./media/concept-all-sign-ins/enable-new-preview.png)

4. Als u wilt terugkeren naar de standaard weergave, klikt u nogmaals op de **Preview** -balk. 

    ![Klassieke weer gave herstellen](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-reports"></a>Rapporten over aanmeld activiteiten downloaden

Wanneer u een rapport met aanmeld activiteiten downloadt, is het volgende waar:

- U kunt het aanmeldings rapport downloaden als CSV-of JSON-bestand.

- U kunt Maxi maal 100-K-records downloaden. Als u meer gegevens wilt downloaden, gebruikt u de rapportage-API.

- Uw down load is gebaseerd op de filters electie die u hebt gemaakt.

- Het aantal records dat u kunt downloaden, is beperkt door het [Bewaar beleid](reference-reports-data-retention.md)voor de Azure Active Directory-rapport. 


![Rapporten downloaden](./media/concept-all-sign-ins/download-reports.png "Rapporten downloaden")


Elke CSV-down load bestaat uit zes verschillende bestanden:

- Interactieve aanmeldingen

- Verificatie gegevens van de interactieve aanmeldingen

- Niet-interactieve aanmeldingen

- Verificatie gegevens van de niet-interactieve aanmeldingen

- Service-Principal-aanmeldingen

- Beheerde identiteit voor Azure-bronnen aanmeldingen

Elke JSON-down load bestaat uit vier verschillende bestanden:

- Interactieve aanmeldingen (inclusief verificatie gegevens)

- Niet-interactieve aanmeldingen (inclusief verificatie gegevens)

- Service-Principal-aanmeldingen

- Beheerde identiteit voor Azure-bronnen aanmeldingen

![Bestanden downloaden](./media/concept-all-sign-ins/download-files.png "Bestanden downloaden")




## <a name="next-steps"></a>Volgende stappen

* [Fout codes voor aanmeldings activiteiten rapport](reference-sign-ins-error-codes.md)
* [Beleid voor het bewaren van Azure AD-gegevens](reference-reports-data-retention.md)
* [Vertragingen van Azure AD-rapporten](reference-reports-latencies.md)
