---
title: Azure Active Directory aanmeldactiviteitenrapporten - preview van | Microsoft Docs
description: Inleiding tot de aanmeldactiviteitenrapporten in de Azure Active Directory portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc9aa77b3fdc3cda94670545f847bb9de31e1160
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718949"
---
# <a name="azure-active-directory-sign-in-activity-reports---preview"></a>Azure Active Directory voor aanmeldingsactiviteiten - preview

Als IT-beheerder wilt u weten hoe het gaat met uw IT-omgeving. Aan de hand van de informatie over de status van uw systeem kunt u beoordelen of en hoe u op potentiële problemen moet reageren. 

Ter ondersteuning van dit doel biedt Azure Active Directory portal toegang tot drie activiteitenlogboeken:

- **Aanmeldingen: informatie** over aanmeldingen en hoe uw resources worden gebruikt door uw gebruikers.
- **[Controle:](concept-audit-logs.md)** informatie over wijzigingen die worden toegepast op uw tenant, zoals gebruikers en groepsbeheer of updates die worden toegepast op de resources van uw tenant.
- **[Inrichting:](concept-provisioning-logs.md)** activiteiten die worden uitgevoerd door de inrichtingsservice, zoals het maken van een groep in ServiceNow of een gebruiker die is geïmporteerd uit Workday.


Het klassieke aanmeldingsrapport in Azure Active Directory biedt een overzicht van interactieve aanmeldingen van gebruikers. Bovendien hebt u nu toegang tot drie aanvullende aanmeldingsrapporten die nu als preview-versie beschikbaar zijn:

- Niet-interactieve aanmeldingen van gebruikers

- Aanmeldingen voor service-principals

- Beheerde identiteiten voor azure-resource-aanmeldingen

In dit artikel vindt u een overzicht van het rapport met aanmeldingsactiviteiten met een preview van niet-interactieve, toepassings- en beheerde identiteiten voor aanmeldingen bij Azure-resources. Zie Aanmeldactiviteitenrapporten in de Azure Active Directory portal voor meer informatie over [het aanmeldingsrapport zonder de preview-functies.](concept-sign-ins.md)



## <a name="prerequisites"></a>Vereisten

Voordat u deze functie kunt gaan gebruiken, moet u de volgende antwoorden weten:

- Wie hebben er toegang tot de gegevens?

- Welke Azure AD-licentie heb ik nodig voor toegang tot aanmeldingsactiviteiten?

### <a name="who-can-access-the-data"></a>Wie hebben er toegang tot de gegevens?

- Gebruikers met de rollen Beveiligingsbeheerder, Beveiligingslezer en Rapportlezer

- Globale beheerders

- Alle gebruiker (niet-beheerders) hebben toegang tot hun eigen aanmeldingen 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Welke Azure AD-licentie heb ik nodig voor toegang tot aanmeldingsactiviteiten?

Aan uw tenant moet een Azure AD Premium zijn gekoppeld om aanmeldingsactiviteiten te kunnen zien. Zie [Aan de slag met Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) om uw versie van Azure Active Directory te upgraden. Het duurt enkele dagen voordat de gegevens in de rapporten worden weer geven nadat u vóór de upgrade hebt geupgraded naar een Premium-licentie zonder gegevensactiviteiten.



## <a name="sign-ins-report"></a>Aanmeldingenrapport

Het aanmeldingsrapport bevat antwoorden op de volgende vragen:

- Wat is het aanmeldingspatroon van een gebruiker, toepassing of service?
- Hoeveel gebruikers, apps of services hebben zich gedurende een week aangemeld?
- Wat is de status van deze aanmeldingen?


Op de rapportblade aanmeldingen kunt u schakelen tussen:

- **Interactieve aanmeldingen van** gebruikers: aanmeldingen waarbij een gebruiker een verificatiefactor biedt, zoals een wachtwoord, een antwoord via een MFA-app, een biometrische factor of een QR-code.

- **Niet-interactieve aanmeldingen van gebruikers:** aanmeldingen die worden uitgevoerd door een client namens een gebruiker. Voor deze aanmeldingen is geen interactie- of verificatiefactor van de gebruiker vereist. Bijvoorbeeld verificatie en autorisatie met behulp van vernieuwings- en toegangstokens waarvoor een gebruiker geen referenties hoeft in te voeren.

- **Aanmeldingen voor service-principals:** aanmeldingen door apps en service-principals waarbij geen gebruiker betrokken is. Bij deze aanmeldingen verstrekt de app of service namens zichzelf een referentie om resources te verifiëren of te openen.

- **Aanmeldingen voor beheerde identiteiten voor Azure-resources:** aanmeldingen door Azure-resources met geheimen die worden beheerd door Azure. Zie Wat zijn beheerde identiteiten [voor Azure-resources? voor meer informatie.](../managed-identities-azure-resources/overview.md) 


![Rapporttypen voor aanmeldingen](./media/concept-all-sign-ins/sign-ins-report-types.png)












## <a name="user-sign-ins&quot;></a>Aanmeldingen van gebruikers

Op elk tabblad op de blade Aanmeldingen worden de onderstaande standaardkolommen weergegeven. Sommige tabbladen hebben extra kolommen:

- Aanmeldingsdatum

- Aanvraag-id

- Gebruikersnaam of gebruikers-id

- Toepassingsnaam of toepassings-id

- Status van de aanmelding

- IP-adres van het apparaat dat wordt gebruikt voor de aanmelding



### <a name=&quot;interactive-user-sign-ins&quot;></a>Interactieve gebruikers sign-ins


Interactieve aanmeldingen van gebruikers zijn aanmeldingen waarbij een gebruiker een verificatiefactor biedt aan Azure AD of rechtstreeks communiceert met Azure AD of een helper-app, zoals de Microsoft Authenticator app. De factoren die gebruikers bieden, zijn onder andere wachtwoorden, antwoorden op MFA-uitdagingen, biometrische factoren of QR-codes die een gebruiker aan Azure AD of een helper-app verstrekt.

> [!NOTE]
> Dit rapport bevat ook federatief aanmeldingen van id-providers die federatief zijn voor Azure AD.  



> [!NOTE] 
> Het rapport Interactieve aanmeldingen van gebruikers dat wordt gebruikt om een aantal niet-interactieve aanmeldingen van Microsoft Exchange-clients te bevatten. Hoewel deze aanmeldingen niet-interactief waren, zijn ze opgenomen in het rapport Interactieve aanmeldingen van gebruikers voor meer zichtbaarheid. Zodra het rapport voor niet-interactieve aanmeldingen van gebruikers in november 2020 de openbare preview heeft ingevoerd, zijn deze niet-interactieve aanmeldingsgebeurtenislogboeken voor een grotere nauwkeurigheid verplaatst naar het niet-interactieve gebruikersrapport. 


**Rapportgrootte:** klein <br> 
**Voorbeelden:**

- Een gebruiker geeft een gebruikersnaam en wachtwoord op in het aanmeldingsscherm van Azure AD.

- Een gebruiker doorstaat een SMS MFA-uitdaging.

- Een gebruiker geeft een biometrisch gebaar om zijn Windows-pc te ontgrendelen met Windows Hello voor Bedrijven.

- Een gebruiker wordt ge federeerd naar Azure AD met een AD FS SAML-asserty.


Naast de standaardvelden bevat het rapport interactieve aanmeldingen ook het volgende: 

- De aanmeldingslocatie

- Of voorwaardelijke toegang is toegepast



U kunt de lijstweergave aanpassen door te klikken op **Kolommen** op de werkbalk.

![Interactieve aanmeldingskolommen voor gebruikers](./media/concept-all-sign-ins/columns-interactive.png &quot;Interactieve aanmeldingskolommen voor gebruikers")





Door de weergave aan te passen, kunt u extra velden weergeven of velden verwijderen die al worden weergegeven.

![Alle interactieve kolommen](./media/concept-all-sign-ins/all-interactive-columns.png)


Selecteer een item in de lijstweergave voor meer gedetailleerde informatie over de gerelateerde aanmelding.

![Aanmeldingsactiviteit](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "Interactieve gebruikers sign-ins")



### <a name="non-interactive-user-sign-ins"></a>Niet-interactieve aanmeldingen van gebruikers

Niet-interactieve aanmeldingen van gebruikers zijn aanmeldingen die namens een gebruiker zijn uitgevoerd door een client-app of onderdelen van het besturingssysteem. Net als interactieve aanmeldingen van gebruikers worden deze aanmeldingen namens een gebruiker uitgevoerd. In tegenstelling tot interactieve aanmeldingen van gebruikers hoeft de gebruiker voor deze aanmeldingen geen verificatiefactor op te geven. In plaats daarvan gebruikt het apparaat of de client-app een token of code om namens een gebruiker een resource te verifiëren of te openen. Over het algemeen zal de gebruiker deze aanmeldingen zien als plaatsvinden op de achtergrond van de activiteit van de gebruiker.


**Rapportgrootte:** Grote <br>
**Voorbeelden:** 

- Een client-app gebruikt een OAuth 2.0-vernieuwings token om een toegangsteken op te halen.

- Een client gebruikt een OAuth 2.0-autorisatiecode om een toegangsteken op te halen en het token te vernieuwen.

- Een gebruiker voert eenmalige aanmelding (SSO) uit bij een web- of Windows-app op een pc die is verbonden met Azure AD.

- Een gebruiker meldt zich aan bij een tweede Microsoft Office app terwijl deze een sessie op een mobiel apparaat heeft met behulp van FOCI (Family of Client-IDs).




Naast de standaardvelden bevat het rapport niet-interactieve aanmeldingen ook het volgende: 

- Resource-id

- Aantal gegroepeerde aanmeldingen




U kunt de velden die in dit rapport worden weergegeven niet aanpassen.


![Uitgeschakelde kolommen](./media/concept-all-sign-ins/disabled-columns.png "Uitgeschakelde kolommen")

Om het gemakkelijker te maken om de gegevens te verwerken, worden niet-interactieve aanmeldingsgebeurtenissen gegroepeerd. Clients maken vaak veel niet-interactieve aanmeldingen namens dezelfde gebruiker in een korte periode, die dezelfde kenmerken delen, met uitzondering van de tijd dat de aanmelding is geprobeerd. Een client kan bijvoorbeeld één keer per uur een toegangs token namens een gebruiker krijgen. Als de status van de gebruiker of client niet wordt gewijzigd, zijn het IP-adres, de resource en alle andere informatie hetzelfde voor elke aanvraag voor toegangs token. Wanneer Azure AD meerdere aanmeldingen registreert die identiek zijn dan tijd en datum, worden deze aanmeldingen vanuit dezelfde entiteit samengevoegd in één rij. Een rij met meerdere identieke aanmeldingen (met uitzondering van de datum en tijd die is uitgegeven) heeft een waarde die groter is dan 1 in de kolom # aanmeldingen. U kunt de rij uitv vouwen om alle verschillende aanmeldingen en de verschillende tijdstempels te zien. Aanmeldingen worden geaggregeerd in de niet-interactieve gebruikers wanneer de volgende gegevens overeenkomt:


- Toepassing

- Gebruiker

- IP-adres

- Status

- Resource-id


U kunt:

- Vouw een knooppunt uit om de afzonderlijke items van een groep te bekijken.  

- Klik op een afzonderlijk item om alle details weer te geven 


![Niet-interactieve aanmeldingsgegevens van gebruikers](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>Aanmeldingen voor service-principals

In tegenstelling tot interactieve en niet-interactieve aanmeldingen van gebruikers hebben aanmeldingen van service-principals geen betrekking op een gebruiker. In plaats daarvan zijn ze aanmeldingen van niet-gebruikersaccounts, zoals apps of service-principals (behalve aanmelding bij beheerde identiteiten, die alleen zijn opgenomen in het rapport aanmeldingen voor beheerde identiteiten). Bij deze aanmeldingen biedt de app of service zijn eigen referenties, zoals een certificaat of app-geheim voor verificatie of toegang tot resources.


**Rapportgrootte:** Grote <br>
**Voorbeelden:**

- Een service-principal gebruikt een certificaat voor verificatie en toegang tot de Microsoft Graph. 

- Een toepassing gebruikt een clientgeheim om te verifiëren in de OAuth-clientreferentiestroom. 


Dit rapport heeft een standaardlijstweergave met het volgende:

- Aanmeldingsdatum

- Aanvraag-id

- Naam of id van service-principal

- Status

- IP-adres

- Resourcenaam

- Resource-id

- Aantal aanmeldingen

U kunt de velden die in dit rapport worden weergegeven niet aanpassen.

![Uitgeschakelde kolommen](./media/concept-all-sign-ins/disabled-columns.png "Uitgeschakelde kolommen")

Om het eenvoudiger te maken om de gegevens in de aanmeldingslogboeken van de service-principal te verwerken, worden aanmeldingsgebeurtenissen van de service-principal gegroepeerd. Aanmeldingen van dezelfde entiteit onder dezelfde voorwaarden worden geaggregeerd in één rij. U kunt de rij uitv vouwen om alle verschillende aanmeldingen en de verschillende tijdstempels te zien. Aanmeldingen worden geaggregeerd in het service-principalrapport wanneer de volgende gegevens overeenkomt:

- Naam of id van service-principal

- Status

- IP-adres

- Resourcenaam of -id

U kunt:

- Vouw een knooppunt uit om de afzonderlijke items van een groep te bekijken.  

- Klik op een afzonderlijk item om alle details te bekijken 


![Kolomdetails](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "Kolomdetails")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Aanmeldingen voor beheerde identiteit voor Azure-resources 

Aanmeldingen voor beheerde identiteit voor Azure-resources zijn aanmeldingen die zijn uitgevoerd door resources met hun geheimen die worden beheerd door Azure om referentiebeheer te vereenvoudigen.

**Rapportgrootte:** Kleine <br> 
**Voorbeelden:**

Een VM met beheerde referenties gebruikt Azure AD om een toegangs token op te halen.   


Dit rapport heeft een standaardlijstweergave die het volgende laat zien:


- Id van beheerde identiteit

- Naam van beheerde identiteit

- Resource

- Resource-id

- Aantal gegroepeerde aanmeldingen

U kunt de velden die in dit rapport worden weergegeven niet aanpassen.

Om het gemakkelijker te maken om de gegevens te verwerken, worden de aanmeldingslogboeken van beheerde identiteiten voor Azure-resources gegroepeerd, en worden niet-interactieve aanmeldingsgebeurtenissen gegroepeerd. Aanmeldingen van dezelfde entiteit worden geaggregeerd in één rij. U kunt de rij uitv vouwen om alle verschillende aanmeldingen en de verschillende tijdstempels te zien. Aanmeldingen worden geaggregeerd in het rapport met beheerde identiteiten wanneer alle volgende gegevens overeenkomt:

- Naam of id van beheerde identiteit

- Status

- IP-adres

- Resourcenaam of -id

Selecteer een item in de lijstweergave om alle aanmeldingen weer te geven die onder een knooppunt zijn gegroepeerd.

Selecteer een gegroepeerd item om alle details van de aanmelding weer te geven. 


## <a name="sign-in-error-code"></a>Foutcode voor aanmelden

Als een aanmelding is mislukt, kunt u meer informatie krijgen over de reden in de sectie **Basisgegevens** van het gerelateerde logboekitem. 

![Schermopname van een gedetailleerde informatieweergave.](./media/concept-all-sign-ins/error-code.png)
 
Hoewel het logboekitem u een foutreden geeft, zijn er gevallen waarin u mogelijk meer informatie krijgt met behulp van het hulpprogramma voor het opschonen van [aanmeldingsfouten.](https://login.microsoftonline.com/error) Indien beschikbaar biedt dit hulpprogramma u bijvoorbeeld herstelstappen.  

![Hulpprogramma voor het opschonen van foutcode](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities"></a>Aanmeldactiviteiten filteren

Door een filter in te stellen, kunt u het bereik van de geretourneerde aanmeldingsgegevens beperken. Azure AD biedt u een breed scala aan extra filters die u kunt instellen. Bij het instellen van uw filter moet u altijd speciale aandacht besteden aan het geconfigureerde **filter Datumbereik.** Een juist datumbereikfilter zorgt ervoor dat Azure AD alleen de gegevens retourneert die u echt belangrijk vindt.     

Met **het** filter Datumbereik kunt u een tijdsbestek definiëren voor de geretourneerde gegevens.
Mogelijke waarden zijn:

- Eén maand

- Zeven dagen

- 24 uur

- Aangepast

![Datumbereikfilter](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>Aanmeldingen van gebruikers filteren

Het filter voor interactieve en niet-interactieve aanmeldingen is hetzelfde. Daarom wordt het filter dat u hebt geconfigureerd voor interactieve aanmeldingen, persistent voor niet-interactieve aanmeldingen en vice versa. 






## <a name="access-the-new-sign-in-activity-reports"></a>Toegang tot de nieuwe rapporten voor aanmeldingsactiviteiten 

Het activiteitenrapport voor aanmeldingen in de Azure Portal biedt u een eenvoudige methode om het voorbeeldrapport in of uit te schakelen. Als u de preview-rapporten hebt ingeschakeld, krijgt u een nieuw menu dat u toegang biedt tot alle rapporttypen voor aanmeldactiviteiten.     


Voor toegang tot de nieuwe aanmeldingsrapporten met niet-interactieve aanmeldingen en toepassingsmeldingen: 

1. Selecteer **Azure Active Directory** in de [Azure-portal](https://portal.azure.com).

    ![Selecteer Azure AD](./media/concept-all-sign-ins/azure-services.png)

2. Klik in **de** sectie Bewaking **op Aanmeldingen.**

    ![Aanmeldingen selecteren](./media/concept-all-sign-ins/sign-ins.png)

3. Klik op de **balk Preview.**

    ![Nieuwe weergave inschakelen](./media/concept-all-sign-ins/enable-new-preview.png)

4. Als u wilt teruggaan naar de standaardweergave, klikt u nogmaals op **de balk Preview.** 

    ![Klassieke weergave herstellen](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-reports"></a>Aanmeldactiviteitenrapporten downloaden

Wanneer u een rapport voor aanmeldingsactiviteiten downloadt, is het volgende waar:

- U kunt het aanmeldingsrapport downloaden als CSV- of JSON-bestand.

- U kunt maximaal 100.000 records downloaden. Als u meer gegevens wilt downloaden, gebruikt u de rapportage-API.

- Uw download is gebaseerd op de filterselectie die u hebt gemaakt.

- Het aantal records dat u kunt downloaden, wordt beperkt door het [Azure Active Directory beleid voor rapportretentie.](reference-reports-data-retention.md) 


![Rapporten downloaden](./media/concept-all-sign-ins/download-reports.png "Rapporten downloaden")


Elke CSV-download bestaat uit zes verschillende bestanden:

- Interactieve aanmeldingen

- Auth details of the interactive sign-ins (Details van de interactieve aanmeldingen controleren)

- Niet-interactieve aanmeldingen

- Auth details of the non-interactive sign-ins (Details van de niet-interactieve aanmeldingen controleren)

- Aanmeldingen voor service-principals

- Aanmeldingen voor beheerde identiteit voor Azure-resources

Elke JSON-download bestaat uit vier verschillende bestanden:

- Interactieve aanmeldingen (inclusief auth-gegevens)

- Niet-interactieve aanmeldingen (inclusief auth-gegevens)

- Aanmeldingen voor service-principals

- Aanmeldingen voor beheerde identiteit voor Azure-resources

![Bestanden downloaden](./media/concept-all-sign-ins/download-files.png "Bestanden downloaden")




## <a name="next-steps"></a>Volgende stappen

* [Foutcodes voor aanmeldactiviteitenrapport](reference-sign-ins-error-codes.md)
* [Azure AD-beleid voor gegevensretentie](reference-reports-data-retention.md)
* [Latentie van Azure AD-rapport](reference-reports-latencies.md)
