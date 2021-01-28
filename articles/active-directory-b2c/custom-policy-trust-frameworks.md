---
title: Overzicht van Azure AD B2C aangepaste beleid | Microsoft Docs
description: Een onderwerp over Azure Active Directory B2C aangepast beleid en het Framework voor identiteits ervaring.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 514ce0a43904048952f38edd6a9d38713f6ef8f3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936657"
---
# <a name="azure-ad-b2c-custom-policy-overview"></a>Overzicht van Azure AD B2C aangepaste beleids regels

Aangepaste beleids regels zijn configuratie bestanden waarmee het gedrag van uw Azure Active Directory B2C (Azure AD B2C)-Tenant wordt gedefinieerd. Terwijl [gebruikers stromen](user-flow-overview.md) vooraf zijn gedefinieerd in de Azure AD B2C portal voor de meest voorkomende identiteits taken, kunnen aangepaste beleids regels volledig worden bewerkt door een identiteits ontwikkelaar om veel verschillende taken uit te voeren.

Een aangepast beleid is volledig configureerbaar en op basis van beleid. Een aangepast beleid is een vertrouwens relatie tussen entiteiten in standaard-protocol indelingen, zoals OpenID Connect Connect, OAuth, SAML en een aantal niet-standaard-, bijvoorbeeld REST API-systeem claim uitwisselingen. Het Framework maakt gebruikers vriendelijke, met wit gelabeld ervaring.

Een aangepast beleid wordt weer gegeven als een of meer XML-indelings bestanden die naar elkaar in een hiërarchische keten verwijzen. De XML-elementen definiëren de bouw stenen, de interactie met de gebruiker en andere partijen en de bedrijfs logica. 

## <a name="custom-policy-starter-pack"></a>Aangepast beleids Starter Pack

Azure AD B2C aangepast beleids [Starter Pack](custom-policy-get-started.md#get-the-starter-pack) wordt geleverd met verschillende vooraf gemaakte beleids regels om snel aan de slag te gaan. Elk van deze Starter Packs bevat het kleinste aantal technische profielen en gebruikers ritten dat nodig is voor het uitvoeren van de beschreven scenario's:

- **LocalAccounts** : Hiermee schakelt u het gebruik van alleen lokale accounts in.
- **SocialAccounts** : Hiermee schakelt u het gebruik van sociale (of federatieve) accounts in.
- **SocialAndLocalAccounts** : Hiermee schakelt u het gebruik van zowel lokale als sociale accounts in. De meeste van onze voor beelden verwijzen naar dit beleid.
- **SocialAndLocalAccountsWithMFA** : Hiermee schakelt u de opties sociale, lokale en multi-factor Authentication in.

## <a name="understanding-the-basics"></a>Informatie over de basis beginselen 

### <a name="claims"></a>Claims

Een claim biedt tijdelijke opslag van gegevens tijdens het uitvoeren van een Azure AD B2C beleid. Hiermee kan informatie over de gebruiker worden opgeslagen, zoals de voor naam, achternaam of een andere claim die is verkregen van de gebruiker of andere systemen (claim uitwisselingen). Het [claim schema](claimsschema.md) is de plaats waar u uw claims declareert. 

Wanneer het beleid wordt uitgevoerd, wordt door Azure AD B2C claims verzonden naar en ontvangen van interne en externe partijen en wordt vervolgens een subset van deze claims naar uw Relying Party-toepassing verzonden als onderdeel van het token. Claims worden op de volgende manieren gebruikt: 

- Een claim wordt opgeslagen, gelezen of bijgewerkt op basis van het gebruikers object van de Directory.
- Er wordt een claim ontvangen van een externe ID-provider.
- Claims worden verzonden of ontvangen met behulp van een aangepaste REST API service.
- Gegevens worden verzameld als claims van de gebruiker tijdens de registratie-of Bewerk profiel stromen.

### <a name="manipulating-your-claims"></a>Uw claims bewerken

De [claim transformaties](claimstransformations.md) zijn vooraf gedefinieerde functies die kunnen worden gebruikt om een gegeven claim te converteren naar een andere aanvraag, een claim te evalueren of een claim waarde in te stellen. U kunt bijvoorbeeld een item toevoegen aan een teken reeks verzameling, het hoofdletter gebruik van een teken reeks wijzigen of een datum-en tijd claim evalueren. Een claim transformatie geeft een transformatie methode aan. 

### <a name="customize-and-localize-your-ui"></a>Uw gebruikers interface aanpassen en lokaliseren

Als u informatie van uw gebruikers wilt verzamelen door een pagina in hun webbrowser te presen teren, gebruikt u het [zelfondertekende technische profiel](self-asserted-technical-profile.md). U kunt uw door uzelf bevestigde technische profiel bewerken om [claims toe te voegen en gebruikers invoer](./configure-user-input.md)aan te passen.

Als u [de gebruikers interface](customize-ui-with-html.md) voor uw zelfondertekende technische profiel wilt aanpassen, geeft u een URL op in het [inhouds definitie](contentdefinitions.md) -element met aangepaste HTML-inhoud. In het zelf-beweringen technische profiel wijst u deze inhouds definitie-ID aan.

Gebruik het [lokalisatie](localization.md) -element om taalspecifieke teken reeksen aan te passen. Een inhouds definitie kan een [lokalisatie](localization.md) verwijzing bevatten die een lijst met gelokaliseerde resources opgeeft die moeten worden geladen. Azure AD B2C combineert elementen van de gebruikersinterface met de HTML-inhoud die vanaf de URL wordt geladen en presenteert vervolgens de pagina aan de gebruiker. 

## <a name="relying-party-policy-overview"></a>Overzicht van het beleid voor Relying Party

Een Relying Party toepassing, die in het SAML-protocol bekend staat als een service provider, roept het [Relying Party-beleid](relyingparty.md) aan om een specifieke gebruikers traject uit te voeren. Het Relying Party beleid specificeert de gebruikers traject die moet worden uitgevoerd en de lijst met claims die het token bevat. 

![Diagram waarin de stroom voor het uitvoeren van beleid wordt weer gegeven](./media/custom-policy-trust-frameworks/custom-policy-execution.png)

Alle Relying Party-toepassingen die hetzelfde beleid gebruiken, ontvangen dezelfde token claims en de gebruiker verloopt via dezelfde gebruikers traject.

### <a name="user-journeys"></a>Gebruikersbelevingen

Met [gebruikers ritten](userjourneys.md) kunt u de bedrijfs logica definiëren met het pad waarmee de gebruiker toegang krijgt tot uw toepassing. De gebruiker wordt door de gebruikers reis geleid om de claims op te halen die aan uw toepassing moeten worden gepresenteerd. Een gebruikers traject is gebaseerd op een reeks [Orchestration-stappen](userjourneys.md#orchestrationsteps). Een gebruiker moet de laatste stap bereiken om een token op te halen. 

In de volgende instructies wordt beschreven hoe u Orchestration-stappen kunt toevoegen aan het Starter Pack-beleid voor [sociale en lokale accounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts) . Hier volgt een voor beeld van een REST API-aanroep die is toegevoegd.

![aangepaste gebruikers traject](media/custom-policy-trust-frameworks/user-journey-flow.png)


### <a name="orchestration-steps"></a>Orchestration-stappen

De Orchestration-stap verwijst naar een methode die het beoogde doel of de functionaliteit implementeert. Deze methode wordt een [technisch profiel](technicalprofiles.md)genoemd. Wanneer uw gebruikers traject vertakkingen nodig heeft om de bedrijfs logica beter weer te geven, verwijst de Orchestration-stap naar [subtraject](subjourneys.md). Een subtraject bevat een eigen set indelings stappen.

Een gebruiker moet de laatste Orchestration-stap in de gebruikers tocht bereiken om een token te verkrijgen. Het is echter mogelijk dat gebruikers niet alle Orchestration-stappen hoeven uit te voeren. Orchestration-stappen kunnen voorwaardelijk worden uitgevoerd op basis van voor [waarden](userjourneys.md#preconditions) die in de Orchestration-stap zijn gedefinieerd. 

Nadat een indelings stap is voltooid, worden in Azure AD B2C de verwerkte claims opgeslagen in de **verzameling claims**. De claims in de claim Bag kunnen worden gebruikt door alle verdere Orchestration-stappen in de gebruikers reis.

In het volgende diagram ziet u hoe de Orchestration-stappen van de gebruiker toegang hebben tot de claim Bag.

![Gebruikers traject Azure AD B2C](media/custom-policy-trust-frameworks/user-journey-diagram.png)

### <a name="technical-profile"></a>Technisch profiel

Een technisch profiel biedt een interface voor communicatie met verschillende soorten partijen. Een gebruikers traject combineert technische profielen aan de hand van Orchestration-stappen om uw bedrijfs logica te definiëren.

Alle typen technische profielen delen hetzelfde concept. U verzendt invoer claims, voert claims-trans formatie uit en communiceert met de geconfigureerde partij. Nadat het proces is voltooid, retourneert het technische profiel de uitvoer claims om een Bag-claim in te dienen. Zie [Technical Profiles Overview](technicalprofiles.md)(Engelstalig) voor meer informatie.

### <a name="validation-technical-profile"></a>Validatie technische profiel

Wanneer een gebruiker met de gebruikers interface communiceert, wilt u mogelijk de verzamelde gegevens valideren. Als u wilt communiceren met de gebruiker, moet u een [zelf-bevestigd technisch profiel](self-asserted-technical-profile.md) gebruiken.

Voor het valideren van de gebruikers invoer wordt een [technische profiel voor validatie](validation-technical-profile.md) aangeroepen vanuit het zelfondertekende technische profiel. Een technische validatie profiel is een methode om een niet-interactief technisch profiel aan te roepen. In dit geval kan het technische profiel uitvoer claims of een fout bericht retour neren. Het fout bericht wordt weer gegeven aan de gebruiker op het scherm, zodat de gebruiker het opnieuw kan proberen.

In het volgende diagram ziet u hoe Azure AD B2C een validatie technische profiel gebruikt om de gebruikers referenties te valideren.

![Diagram voor validatie van technische profielen](media/custom-policy-trust-frameworks/validation-technical-profile.png)

## <a name="inheritance-model"></a>Overname model

Elk Starter Pack bevat de volgende bestanden:

- Een **basis** bestand dat de meeste definities bevat. Voor hulp bij het oplossen van problemen en onderhoud op lange termijn van uw beleid, probeert u het aantal wijzigingen dat u in dit bestand aanbrengt, te minimaliseren.
- Een **extensie** bestand dat de unieke configuratie wijzigingen voor uw Tenant bevat. Dit beleids bestand is afgeleid van het basis bestand. Gebruik dit bestand om nieuwe functionaliteit toe te voegen of bestaande functionaliteit te overschrijven. Gebruik dit bestand bijvoorbeeld om met nieuwe id-providers te communiceren.
- Een **RP-bestand (Relying Party)** dat het bestand met de taak focus is dat rechtstreeks wordt aangeroepen door de Relying Party toepassing, zoals uw web-, mobiele of bureaublad toepassingen. Voor elke unieke taak, zoals aanmelden, aanmelden, wacht woord opnieuw instellen of profiel bewerken, is een eigen Relying Party-beleids bestand vereist. Dit beleids bestand is afgeleid van het extensies-bestand.

Het overname model is als volgt:

- Het onderliggende beleid op elk niveau kan overnemen van het bovenliggende beleid en het uitbreiden door nieuwe elementen toe te voegen.
- Voor complexere scenario's kunt u meer overname niveaus toevoegen (Maxi maal 5 in totaal).
- U kunt meer Relying Party-beleid toevoegen. U kunt bijvoorbeeld mijn account verwijderen, een telefoon nummer wijzigen, het SAML-Relying Party beleid en nog veel meer.

In het volgende diagram ziet u de relatie tussen de beleids bestanden en de Relying Party toepassingen.

![Diagram van het overname model voor het vertrouwens raamwerk beleid](media/custom-policy-trust-frameworks/policies.png)


## <a name="guidance-and-best-practices"></a>Richtlijnen en aanbevolen procedures

### <a name="best-practices"></a>Aanbevolen procedures

Binnen een Azure AD B2C aangepast beleid kunt u uw eigen bedrijfs logica integreren om de gebruikers ervaringen te bouwen die u nodig hebt en de functionaliteit van de service te verbeteren. We hebben een aantal aanbevolen procedures en aanbevelingen om aan de slag te gaan.

- Maak uw logica in het **uitbrei ding beleid** of **Relying Party beleid**. U kunt nieuwe elementen toevoegen, waardoor het basis beleid wordt overschreven door naar dezelfde ID te verwijzen. Op deze manier kunt u uw project uitschalen terwijl u het basis beleid later gemakkelijker kunt upgraden als micro soft nieuwe Starter Packs vrijgeeft.
- Binnen het **basis beleid** raden wij u ten zeerste aan om te voor komen dat u wijzigingen aanbrengt. Indien nodig kunt u opmerkingen maken waarin de wijzigingen zijn aangebracht.
- Wanneer u een element overschrijft, zoals technische profiel meta gegevens, moet u voor komen dat het hele technische profiel wordt gekopieerd uit het basis beleid. Kopieer in plaats daarvan alleen de vereiste sectie van het element. Zie [e-mail verificatie uitschakelen](./disable-email-verification.md) voor een voor beeld van hoe u de wijziging aanbrengt.
- Als u het dupliceren van technische profielen wilt reduceren, waarbij de kern functionaliteit wordt gedeeld, gebruikt u [technisch profiel opnemen](technicalprofiles.md#include-technical-profile).
- Vermijd het schrijven naar de Azure AD-Directory tijdens het aanmelden, wat kan leiden tot het beperken van problemen.
- Als uw beleid externe afhankelijkheden heeft, zoals REST Api's, zorgt u ervoor dat ze Maxi maal beschikbaar zijn.
- Voor een betere gebruikers ervaring zorgt u ervoor dat uw aangepaste HTML-sjablonen wereld wijd worden geïmplementeerd met behulp van [online content delivery](../cdn/index.yml). Met Azure Content Delivery Network (CDN) kunt u de laad tijden verminderen, band breedte besparen en de reactie snelheid verbeteren.
- Als u een wijziging wilt aanbrengen in de gebruikers traject, kopieert u de volledige gebruikers reis van het basis beleid naar het extensie beleid. Geef een unieke reis-ID voor de gebruiker op die u hebt gekopieerd. Wijzig vervolgens in het [Relying Party beleid](relyingparty.md)het [standaard gebruikers traject](relyingparty.md#defaultuserjourney) -element dat naar de nieuwe gebruikers traject wijst.

## <a name="troubleshooting"></a>Problemen oplossen

Bij het ontwikkelen met Azure AD B2C-beleid, kunt u fouten of uitzonde ringen uitvoeren tijdens het uitvoeren van uw gebruikers traject. De kan worden onderzocht met behulp van Application Insights.

- Integreer Application Insights met Azure AD B2C om [uitzonde ringen](troubleshoot-with-application-insights.md)op te sporen.
- Met de [extensie Azure AD B2C voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) kunt u [de logboeken openen en visualiseren](https://github.com/azure-ad-b2c/vscode-extension/blob/master/src/help/app-insights.md) op basis van een beleids naam en-tijd.
- De meest voorkomende fout bij het instellen van aangepast beleid is XML met een onjuiste indeling. Gebruik [XML-schema validatie](troubleshoot-custom-policies.md) om fouten te identificeren voordat u het XML-bestand uploadt.

## <a name="continuous-integration"></a>Continue integratie

Door gebruik te maken van een continue integratie-en leverings pijplijn (CI/CD) die u in azure-pijp lijnen hebt ingesteld, kunt u [uw Azure AD B2C aangepaste beleids regels in uw software-levering](deploy-custom-policies-devops.md) en code beheer automatisering toevoegen. Wanneer u implementeert in verschillende Azure AD B2C omgevingen, bijvoorbeeld dev, test en productie, wordt u aangeraden hand matige processen te verwijderen en geautomatiseerd testen uit te voeren met behulp van Azure-pijp lijnen.

## <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

Aan de slag met Azure AD B2C aangepast beleid:

1. [Een Azure AD B2C-tenant maken](tutorial-create-tenant.md)
1. [Registreer een webtoepassing](tutorial-register-applications.md) met behulp van de Azure portal zodat u uw beleid kunt testen.
1. Voeg de benodigde [beleids sleutels](custom-policy-get-started.md#add-signing-and-encryption-keys) toe en [Registreer de Framework-toepassingen voor identiteits ervaring](custom-policy-get-started.md#register-identity-experience-framework-applications).
1. [Down load het Azure AD B2C Policy Starter Pack](custom-policy-get-started.md#get-the-starter-pack) en upload het naar uw Tenant. 
1. Nadat u het Starter Pack hebt geüpload, moet u [uw registratie-of aanmeldings beleid testen](custom-policy-get-started.md#test-the-custom-policy).
1. U wordt aangeraden [Visual Studio code](https://code.visualstudio.com/) (VS code) te downloaden en te installeren. Visual Studio code is een licht gewicht, krachtige bron code-editor, die op uw bureau blad wordt uitgevoerd en beschikbaar is voor Windows, macOS en Linux. Met VS code kunt u snel navigeren door uw Azure AD B2C aangepaste beleids-XML-bestanden te openen door de [Azure AD B2C-extensie voor VS code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) te installeren.
 
## <a name="next-steps"></a>Volgende stappen

Nadat u uw Azure AD B2C-beleid hebt ingesteld en getest, kunt u beginnen met het aanpassen van uw beleid. Ga door de volgende artikelen voor meer informatie over:

- [Claims toevoegen en gebruikers invoer aanpassen](./configure-user-input.md) met aangepast beleid. Meer informatie over het definiëren van een claim en het toevoegen van een claim aan de gebruikers interface door een aantal van de technische profielen van het Start pakket aan te passen.
- [Pas de gebruikers interface](customize-ui-with-html.md) van uw toepassing aan met behulp van een aangepast beleid. Meer informatie over het maken van uw eigen HTML-inhoud en het aanpassen van de inhouds definitie.
- [Lokalisatie van de gebruikers interface](./language-customization.md) van uw toepassing met behulp van een aangepast beleid. Meer informatie over het instellen van de lijst met ondersteunde talen en het bieden van taalspecifieke labels door het element gelokaliseerde resources toe te voegen.
- Tijdens het ontwikkelen en testen van uw beleid kunt u [e-mail verificatie uitschakelen](./disable-email-verification.md). Meer informatie over het overschrijven van een technische profiel meta gegevens.
- [Stel aanmelden met een Google-account](./identity-provider-google.md) in met behulp van aangepast beleid. Meer informatie over het maken van een nieuwe claim provider met het technische profiel OAuth2. Pas vervolgens de reis van de gebruiker aan, zodat deze de Google-aanmeldings optie bevat.
- Als u problemen met uw aangepaste beleid wilt vaststellen, kunt u [Azure Active Directory B2C-logboeken verzamelen met Application Insights](troubleshoot-with-application-insights.md). Meer informatie over het toevoegen van nieuwe technische profielen en het configureren van uw Relying Party-beleid.
