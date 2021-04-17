---
title: Aanmeldactiviteitenrapporten in Azure Active Directory Portal | Microsoft Docs
description: Ontdek de aanmeldactiviteitenrapporten in de Azure Active Directory Portal
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
ms.date: 04/16/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99f1f27cb087dc83295dddade4c0fca551a0d9c9
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589683"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Aanmeldactiviteitenrapporten in Azure Active Directory Portal

De Azure Active Directory portal biedt u toegang tot drie activiteitenlogboeken:

- **Aanmeldingen: informatie** over aanmeldingen en hoe uw resources worden gebruikt door uw gebruikers.
- **[Controle:](concept-audit-logs.md)** informatie over wijzigingen die worden toegepast op uw tenant, zoals gebruikers en groepsbeheer of updates die worden toegepast op de resources van uw tenant.
- **[Inrichting:](concept-provisioning-logs.md)** activiteiten die worden uitgevoerd door de inrichtingsservice, zoals het maken van een groep in ServiceNow of het importeren van een gebruiker uit Workday.

In dit artikel vindt u een overzicht van het aanmeldingsrapport.

## <a name="prerequisites"></a>Vereisten

### <a name="who-can-access-the-data"></a>Wie hebben er toegang tot de gegevens?

* Gebruikers in de rollen Beveiligingsbeheerder, Beveiligingslezer, Globale lezer en Rapportlezer
* Globale beheerders
* Alle gebruiker (niet-beheerders) hebben toegang tot hun eigen aanmeldingen 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Welke Azure AD-licentie heb ik nodig voor toegang tot aanmeldingsactiviteiten?

Het rapport aanmeldingsactiviteiten is beschikbaar in alle [edities](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data) van Azure AD en is ook toegankelijk via de Microsoft Graph API.

## <a name="sign-ins-report"></a>Aanmeldingenrapport

Het rapport Aanmeldingen van gebruikers biedt antwoorden op de volgende vragen:

* Wat is het aanmeldingspatroon van een gebruiker?
* Hoeveel gebruikers hebben zich gedurende een week aangemeld?
* Wat is de status van deze aanmeldingen?

Selecteer in [Azure Portal](https://portal.azure.com) menu de **optie Azure Active Directory** of zoek en selecteer **Azure Active Directory** op een pagina.

![Selecteer Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

Selecteer **onder** Bewaking **de optie Aanmeldingen om** het rapport [Aanmeldingen te openen.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)

![Schermopname met aanmeldingen die zijn geselecteerd in het menu Bewaking.](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Aanmeldingsactiviteit")

Het kan tot twee uur duren voordat sommige aanmeldingsrecords worden weer geven in de portal.

> [!IMPORTANT]
> In het aanmeldingsrapport worden  alleen de interactieve aanmeldingen weergegeven, dat wil zeggen aanmeldingen waarbij gebruikers zich handmatig aanmelden met hun gebruikersnaam en wachtwoord. Niet-interactieve aanmeldingen, zoals service-naar-serviceverificatie, worden niet weergegeven in het aanmeldingsrapport. 

Een aanmeldingslogboek heeft een standaardlijstweergave die het volgende laat zien:

- De aanmeldingsdatum
- De gerelateerde gebruiker
- De toepassing waar de gebruiker zich heeft aangemeld
- De aanmeldingsstatus
- De status van de risicodetectie
- De status van de vereiste voor meervoudige verificatie (MFA)

![Schermopname van de aanmeldingen voor Office 365 SharePoint Online.](./media/concept-sign-ins/sign-in-activity.png "Aanmeldingsactiviteit")

U kunt de lijstweergave aanpassen door te klikken op **Kolommen** op de werkbalk.

![Schermopname van de optie Kolommen op de pagina Aanmeldingen.](./media/concept-sign-ins/19.png "Aanmeldingsactiviteit")

Het **dialoogvenster** Kolommen geeft u toegang tot de selecteerbare kenmerken. In een aanmeldingsrapport kunt u geen velden hebben met meer dan één waarde voor een bepaalde aanmeldingsaanvraag als kolom. Dit geldt bijvoorbeeld voor verificatiegegevens, voorwaardelijke toegangsgegevens en netwerklocatie.   

![Schermopname van het dialoogvenster Kolommen waarin u kenmerken kunt selecteren.](./media/concept-sign-ins/columns.png "Aanmeldingsactiviteit")

Selecteer een item in de lijstweergave voor meer gedetailleerde informatie.

![Schermopname van een gedetailleerde informatieweergave.](./media/concept-sign-ins/basic-sign-in.png "Aanmeldingsactiviteit")

> [!NOTE]
> Klanten kunnen nu problemen met beleid voor voorwaardelijke toegang oplossen via alle aanmeldingsrapporten. Door te klikken  op het tabblad Voorwaardelijke toegang voor een aanmeldingsrecord, kunnen klanten de status van voorwaardelijke toegang bekijken en de details bekijken van de beleidsregels die van toepassing zijn op de aanmelding en het resultaat voor elk beleid.
> Zie veelgestelde vragen over [CA-informatie in](reports-faq.md#conditional-access)alle aanmeldingen voor meer informatie.


## <a name="sign-in-error-code"></a>Foutcode voor aanmelden

Als een aanmelding is mislukt, kunt u meer informatie krijgen over de reden in de sectie **Basisgegevens** van het gerelateerde logboekitem. 

![foutcode voor aanmelden](./media/concept-all-sign-ins/error-code.png)
 
Hoewel het logboekitem u een foutreden geeft, zijn er gevallen waarin u mogelijk meer informatie krijgt met behulp van het hulpprogramma voor het opschonen van [aanmeldingsfouten.](https://login.microsoftonline.com/error) Indien beschikbaar biedt dit hulpprogramma u bijvoorbeeld herstelstappen.  

![Hulpprogramma voor het opschonen van foutcode](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities&quot;></a>Aanmeldactiviteiten filteren

Eerst moet u de gerapporteerde gegevens beperken tot een niveau dat voor u werkt. Filter ten tweede aanmeldingsgegevens met behulp van het datumveld als standaardfilter. Azure AD biedt u een breed scala aan extra filters die u kunt instellen:

![Schermopname met de optie Filters toevoegen.](./media/concept-sign-ins/04.png &quot;Aanmeldingsactiviteit")

**Aanvraag-id:** de id van de aanvraag die u belangrijk vindt.

**Gebruiker:** de naam of het user principal name (UPN) van de gebruiker die u belangrijk vindt.

**Toepassing:** de naam van de doeltoepassing.
 
**Status:** de aanmeldingsstatus die u belangrijk vindt:

- Geslaagd

- Fout

- Onderbroken


**IP-adres:** het IP-adres van het apparaat dat wordt gebruikt om verbinding te maken met uw tenant.

De **locatie:** de locatie van waar de verbinding is gestart:

- Plaats

- Staat/provincie

- Land/regio


**Resource:** de naam van de service die wordt gebruikt voor de aanmelding.


**Resource-id:** de id van de service die wordt gebruikt voor de aanmelding.


**Client-app:** het type client-app dat wordt gebruikt om verbinding te maken met uw tenant:

![Client-app-filter](./media/concept-sign-ins/client-app-filter.png)


|Name|Moderne verificatie|Description|
|---|:-:|---|
|Geverifieerde SMTP| |Wordt gebruikt door pop- en IMAP-client om e-mailberichten te verzenden.|
|Autodiscover| |Wordt gebruikt door Outlook- en EAS-clients om postvakken in Exchange Online te zoeken en er verbinding mee te maken.|
|Exchange ActiveSync:| |Dit filter toont alle aanmeldingspogingen waarbij het EAS-protocol is geprobeerd.|
|Browser|![Blauw vinkje.](./media/concept-sign-ins/check.png)|Geeft alle aanmeldingspogingen weer van gebruikers die webbrowsers gebruiken|
|Exchange ActiveSync:| | Geeft alle aanmeldingspogingen weer van gebruikers met client-apps die Exchange ActiveSync gebruiken om verbinding te maken met Exchange Online|
|Exchange Online PowerShell| |Wordt gebruikt om verbinding te maken met Exchange Online met externe PowerShell. Als u basisverificatie voor Exchange Online PowerShell blokkeert, moet u de Exchange Online PowerShell-module gebruiken om verbinding te maken. Zie Verbinding maken met [Exchange Online PowerShell met behulp van meervoudige verificatie voor instructies.](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)|
|Exchange-webservices| |Een programmeerinterface die wordt gebruikt door Outlook, Outlook voor Mac en apps van derden.|
|IMAP4| |Een verouderde e-mailclient die IMAP gebruikt om e-mail op te halen.|
|MAPI via HTTP| |Wordt gebruikt door Outlook 2010 en hoger.|
|Mobiele apps en bureaubladcl clients|![Blauw vinkje.](./media/concept-sign-ins/check.png)|Geeft alle aanmeldingspogingen weer van gebruikers die gebruikmaken van mobiele apps en desktopcl clients.|
|Offline Adresboek| |Een kopie van adreslijstverzamelingen die worden gedownload en gebruikt door Outlook.|
|Outlook Anywhere (RPC via HTTP)| |Wordt gebruikt door Outlook 2016 en eerder.|
|Outlook-service| |Wordt gebruikt door de app Mail en Calendar voor Windows 10.|
|POP3| |Een verouderde e-mailclient die POP3 gebruikt om e-mail op te halen.|
|Reporting Web Services| |Wordt gebruikt om rapportgegevens op te halen in Exchange Online.|
|Andere clients| |Geeft alle aanmeldingspogingen weer van gebruikers waarbij de client-app niet is opgenomen of onbekend is.|



**Besturingssysteem:** het besturingssysteem dat op het apparaat wordt uitgevoerd, heeft aanmelding bij uw tenant gebruikt. 


**Apparaatbrowser:** als de verbinding vanuit een browser is gestart, kunt u in dit veld filteren op browsernaam.


**Correlatie-id:** de correlatie-id van de activiteit.




**Voorwaardelijke toegang:** de status van de toegepaste regels voor voorwaardelijke toegang

- **Niet toegepast:** er wordt geen beleid toegepast op de gebruiker en toepassing tijdens het aanmelden.

- **Geslaagd:** een of meer beleidsregels voor voorwaardelijke toegang die worden toegepast op de gebruiker en toepassing (maar niet noodzakelijkerwijs de andere voorwaarden) tijdens het aanmelden. 

- **Fout:** De aanmelding voldoet aan de gebruikers- en toepassingsvoorwaarde van ten minste één beleid voor voorwaardelijke toegang en het verlenen van besturingselementen is niet voldaan of is ingesteld op het blokkeren van toegang.









## <a name="download-sign-in-activities"></a>Aanmeldactiviteiten downloaden

Klik op **de optie** Downloaden om een CSV- of JSON-bestand met de meest recente 250.000 records te maken. Begin met [het downloaden van de aanmeldingsgegevens](quickstart-download-sign-in-report.md) als u er buiten de Azure Portal.  

![Downloaden](./media/concept-sign-ins/71.png "Downloaden")

> [!IMPORTANT]
> Het aantal records dat u kunt downloaden, wordt beperkt door het [Azure Active Directory van het rapport.](reference-reports-data-retention.md)  


## <a name="sign-ins-data-shortcuts"></a>Snelkoppelingen voor aanmeldingsgegevens

Azure AD en de Azure Portal beide bieden u extra toegangspunten voor aanmeldingsgegevens:

- Overzicht van identiteitsbeveiliging
- Gebruikers
- Groepen
- Bedrijfstoepassingen

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Aanmeldingsgegevens van gebruikers in Identity Security Protection

In de aanmeldingsgrafiek van de gebruiker **op de overzichtspagina identiteitsbeveiliging** worden wekelijkse aggregaties van aanmeldingen weergegeven. De standaardwaarde voor de periode is 30 dagen.

![Schermopname van een grafiek van aanmeldingen gedurende een maand.](./media/concept-sign-ins/06.png "Aanmeldingsactiviteit")

Als u in de aanmeldingsgrafiek op een dag klikt, ziet u een overzicht van de aanmeldingsactiviteiten voor die dag.

Elke rij in de lijst met aanmeldingsactiviteiten geeft het volgende weer:

* Wie heeft zich aangemeld?
* Welke toepassing was het aanmeldingsdoel?
* Wat is de status van de aanmelding?
* Wat is de MFA-status van de aanmelding?

Door op een item te klikken, krijgt u meer informatie over de aanmelding:

- Gebruikers-id
- Gebruiker
- Gebruikersnaam
- Toepassings-id
- Toepassing
- Client
- Locatie
- IP-adres
- Datum
- MFA vereist
- Aanmeldingsstatus

> [!NOTE]
> IP-adressen worden zodanig uitgegeven dat er geen definitieve verbinding is tussen een IP-adres en waar de computer met dat adres zich fysiek bevindt. Het toewijzen van IP-adressen is gecompliceerd door het feit dat mobiele providers en VPN's IP-adressen uitgeven van centrale pools die vaak heel ver van waar het clientapparaat daadwerkelijk wordt gebruikt. Momenteel is het in Azure AD-rapporten een best effort om het IP-adres te converteren naar een fysieke locatie op basis van traceringen, registergegevens, reverse look ups en andere informatie.

Op de pagina **Gebruikers** krijgt u een volledig overzicht van alle aanmeldingen van gebruikers door in de sectie **Activiteit** op **Aanmelden** te klikken.

![Schermopname van de sectie Activiteit waarin u Aanmeldingen kunt selecteren.](./media/concept-sign-ins/08.png "Aanmeldingsactiviteit")

## <a name="usage-of-managed-applications"></a>Het gebruik van beheerde toepassingen

Met een toepassingsgerichte weergave van uw aanmeldingsgegevens kunt u antwoord vinden op vragen zoals:

* Wie gebruikt mijn toepassingen?
* Wat zijn de drie beste toepassingen in uw organisatie?
* Hoe gaat het met mijn nieuwste toepassing?

Het toegangspunt voor deze gegevens is de drie beste toepassingen in uw organisatie. De gegevens zijn opgenomen in het rapport van de afgelopen 30 dagen in de **sectie Overzicht** onder **Bedrijfstoepassingen.**

![Schermopname die laat zien waar u Overzicht kunt selecteren.](./media/concept-sign-ins/10.png "Aanmeldingsactiviteit")

In de grafieken voor app-gebruik worden wekelijkse aggregaties van aanmeldingen weergegeven voor uw drie belangrijkste toepassingen in een bepaalde periode. De standaard ingestelde periode is 30 dagen.

![Schermopname van het app-gebruik voor een periode van één maand.](./media/concept-sign-ins/graph-chart.png "Aanmeldingsactiviteit")

Als u wilt, kunt u de focus instellen op een specifieke toepassing.

![Rapportage](./media/concept-sign-ins/single-app-usage-graph.png "Rapportages")

Als u op een dag in de appgebruikgrafiek klikt, ziet u een gedetailleerd overzicht van de aanmeldactiviteiten.

Met de optie **Aanmeldingen** krijgt u een volledig overzicht van alle aanmeldingsgebeurtenissen voor uw toepassingen.

## <a name="microsoft-365-activity-logs"></a>Microsoft 365 activiteitenlogboeken

U kunt de Microsoft 365 bekijken vanuit de [Microsoft 365-beheercentrum.](/office365/admin/admin-overview/about-the-admin-center) Houd er rekening mee dat Microsoft 365 activiteitenlogboeken en Azure AD-activiteitenlogboeken een groot aantal directory-resources delen. Alleen de Microsoft 365-beheercentrum biedt een volledig overzicht van de Microsoft 365 activiteitenlogboeken. 

U kunt de activiteitenlogboeken van Microsoft 365 ook programmatisch openen met behulp van de [Office 365 Management-API's.](/office/office-365-management-api/office-365-management-apis-overview)

## <a name="next-steps"></a>Volgende stappen

* [Foutcodes voor aanmeldactiviteitenrapport](reference-sign-ins-error-codes.md)
* [Azure AD-beleid voor gegevensretentie](reference-reports-data-retention.md)
* [Latentie van Azure AD-rapport](reference-reports-latencies.md)