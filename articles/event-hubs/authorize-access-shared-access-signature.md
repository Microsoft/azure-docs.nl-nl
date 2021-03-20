---
title: Toegang verlenen met een hand tekening voor gedeelde toegang in azure Event Hubs
description: In dit artikel vindt u informatie over het verlenen van toegang tot Azure Event Hubs-resources met behulp van Shared Access signatures (SAS).
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 6a2d7385f82864e8d378055333377fb9c3f73c19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85323123"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Toegang tot Event Hubs resources met hand tekeningen voor gedeelde toegang autoriseren
Een Shared Access Signature (SAS) biedt een manier om beperkte toegang tot resources in uw Event Hubs-naam ruimte toe te kennen. SAS-beveiligingen hebben toegang tot Event Hubs-resources op basis van autorisatie regels. Deze regels worden geconfigureerd op een naam ruimte of een entiteit (Event Hub of onderwerp). Dit artikel bevat een overzicht van het SAS-model en beoordeelt de aanbevolen procedures voor SAS.

## <a name="what-are-shared-access-signatures"></a>Wat zijn Shared Access signatures?
Een Shared Access Signature (SAS) biedt gedelegeerde toegang tot Event Hubs-resources op basis van autorisatie regels. Een autorisatie regel heeft een naam, is gekoppeld aan specifieke rechten en bevat een paar cryptografische sleutels. U gebruikt de naam en sleutel van de regel via de Event Hubs-clients of in uw eigen code voor het genereren van SAS-tokens. Een client kan vervolgens het token door geven aan Event Hubs om autorisatie voor de aangevraagde bewerking te bewijzen.

SAS is een op claims gebaseerd autorisatie mechanisme met behulp van eenvoudige tokens. Met SAS worden sleutels nooit door gegeven op de kabel. Sleutels worden gebruikt voor het cryptografisch ondertekenen van informatie die later door de service kan worden geverifieerd. SAS kan worden gebruikt in combi natie met een gebruikers naam-en wachtwoord schema waarbij de client onmiddellijk in bezit is van een naam van een autorisatie regel en een overeenkomende sleutel. SAS kan worden gebruikt als een federatief beveiligings model, waarbij de client een time-Limited en ondertekend toegangs token ontvangt van een beveiligings token service, zonder dat deze ooit in bezit is van de handtekening sleutel.

> [!NOTE]
> Azure Event Hubs ondersteunt het autoriseren van Event Hubs resources met behulp van Azure Active Directory (Azure AD). Het autoriseren van gebruikers of toepassingen die gebruikmaken van het OAuth 2,0-token dat wordt geretourneerd door Azure AD, biedt een superieure beveiliging en gebruiks vriendelijk gebruik van Shared Access signatures (SAS). Met Azure AD hoeft u geen tokens op te slaan in uw code en mogelijke beveiligings problemen met Risico's.
>
> Micro soft raadt u aan Azure AD te gebruiken met uw Azure Event Hubs-toepassingen wanneer dat mogelijk is. Zie [toegang tot Azure Event hubs-resource machtigen met behulp van Azure Active Directory](authorize-access-azure-active-directory.md)voor meer informatie.

> [!IMPORTANT]
> SAS (Shared Access signatures)-tokens zijn essentieel om uw resources te beschermen. Dankzij de granulariteit verleent SAS clients toegang tot uw Event Hubs-resources. Ze mogen niet openbaar worden gedeeld. Bij het delen, als dit is vereist voor het oplossen van problemen, kunt u overwegen een gereduceerde versie van alle logboek bestanden te gebruiken of de SAS-tokens (indien aanwezig) te verwijderen uit de logboek bestanden en te controleren of de scherm opnamen geen SAS-informatie bevatten.

## <a name="shared-access-authorization-policies"></a>Beleid voor gedeelde toegang
Elke Event Hubs naam ruimte en elke Event Hubs entiteit (een Event Hub exemplaar of een Kafka-onderwerp) heeft een beleid voor gedeelde toegang dat bestaat uit regels. Het beleid op het niveau van de naam ruimte is van toepassing op alle entiteiten in de naam ruimte, ongeacht hun afzonderlijke beleids configuratie.
Voor elke autorisatie beleids regel besluit u over drie stukjes informatie: naam, bereik en rechten. De naam is een unieke naam in dat bereik. Het bereik is de URI van de betreffende resource. Voor een Event Hubs naam ruimte is de scope de Fully Qualified Domain Name (FQDN), zoals `https://<yournamespace>.servicebus.windows.net/` .

De rechten van de beleids regel kunnen bestaan uit een combi natie van:
- **Verzenden** : geeft het recht berichten te verzenden naar de entiteit
- **Luis teren** : geeft het recht om te Luis teren naar of te ontvangen van de entiteit
- **Beheren** : geeft het recht om de topologie van de naam ruimte te beheren, met inbegrip van het maken en verwijderen van entiteiten

> [!NOTE]
> Het recht **beheren** bevat de rechten **verzenden** en **Luis teren** .

Een naam ruimte-of entiteits beleid kan Maxi maal 12 Shared Access Authorization Rules bevatten, zodat er ruimte is voor de drie sets regels, elk met de basis rechten en de combi natie van verzenden en Luis teren. Deze limiet onderstreept dat het SAS-beleids archief niet bedoeld is als een gebruikers-of service account-archief. Als uw toepassing toegang moet verlenen tot Event Hubs resources op basis van gebruikers-of service-identiteiten, moet deze een beveiligings token service implementeren die SAS-tokens uitgeeft na een verificatie en toegangs controle.

Aan een autorisatie regel is een **primaire sleutel** en een **secundaire sleutel** toegewezen. Deze sleutels zijn cryptografische sterke sleutels. Zorg dat ze niet verloren gaan of lekken. Ze zijn altijd beschikbaar in de Azure Portal. U kunt een van de gegenereerde sleutels gebruiken en u kunt deze op elk gewenst moment opnieuw genereren. Als u een sleutel in het beleid opnieuw genereert of wijzigt, worden alle eerder uitgegeven tokens op basis van die sleutel onmiddellijk ongeldig. Actieve verbindingen die zijn gemaakt op basis van deze tokens blijven echter werken totdat het token verloopt.

Wanneer u een Event Hubs naam ruimte maakt, wordt automatisch een beleids regel gemaakt met de naam **RootManageSharedAccessKey** voor de naam ruimte. Dit beleid heeft **beheer** machtigingen voor de volledige naam ruimte. Het is raadzaam deze regel te behandelen als een account met beheerders rechten en niet in uw toepassing te gebruiken. U kunt aanvullende beleids regels maken op het tabblad **configureren** van de naam ruimte in de portal via Power shell of Azure cli.

## <a name="best-practices-when-using-sas"></a>Best practices bij gebruik van SAS
Wanneer u gebruikmaakt van hand tekeningen voor gedeelde toegang in uw toepassingen, moet u rekening houden met twee mogelijke Risico's:

- Als een SAS wordt gelekt, kan deze worden gebruikt door iedereen die deze verkrijgt, waardoor uw Event Hubs-resources mogelijk kunnen worden aangetast.
- Als een SAS die aan een client toepassing is gegeven, verloopt en de toepassing geen nieuwe SA'S kan ophalen van uw service, wordt de functionaliteit van de toepassing mogelijk belemmerd.

De volgende aanbevelingen voor het gebruik van hand tekeningen voor gedeelde toegang kunnen helpen bij het oplossen van deze Risico's:

- **Clients automatisch de SAS vernieuwen, indien nodig**: clients moeten de sa's goed vernieuwen voordat ze verlopen, zodat er tijd is om nieuwe pogingen te doen als de service die de SAS aanbiedt, niet beschikbaar is. Als uw SAS is bedoeld om te worden gebruikt voor een klein aantal directe, korte, langdurige bewerkingen die naar verwachting binnen de verloop periode zullen worden voltooid, is het mogelijk onnodig dat de SA'S niet naar verwachting worden verlengd. Als u echter een client hebt die regel matig aanvragen maakt via SAS, is het mogelijk dat de verval datum wordt afgespeeld. De belangrijkste overweging is om de nood zaak van de SA'S te verkorten (zoals eerder vermeld), met de nood zaak om ervoor te zorgen dat de client vernieuwingen vroeg genoeg aanvraagt (om onderbrekingen te voor komen als gevolg van het verlopen van SAS vóór een geslaagde vernieuwing).
- **Wees voorzichtig met de SAS-start tijd**: als u de begin tijd voor SAS **nu** instelt, wordt de klok scheefheid (verschillen in de huidige tijd op basis van verschillende computers) mogelijk voor de eerste paar minuten afgenomen. In het algemeen stelt u de start tijd in op ten minste 15 minuten in het verleden. U kunt de service ook niet instellen, waardoor deze onmiddellijk in alle gevallen geldig is. Hetzelfde geldt ook voor de verloop tijd. Houd er rekening mee dat u gedurende een wille keurige aanvraag Maxi maal 15 minuten aan de klok kunt laten hellen. 
- **Zorg ervoor dat de resource toegankelijk** is: een beveiligings best practice is om een gebruiker te voorzien van de mini maal vereiste bevoegdheden. Als een gebruiker alleen lees toegang nodig heeft tot één entiteit, dan verlenen zij Lees-en schrijf toegang tot de ene entiteit en niet lezen/schrijven/verwijderen voor alle entiteiten. Het helpt ook de schade te beperken als een SAS is aangetast omdat de SAS minder kracht in de handen van een aanvaller heeft.
- **Gebruik niet altijd SAS**: soms zijn de Risico's die zijn gekoppeld aan een bepaalde bewerking ten opzichte van uw event hubs, tegen de voor delen van SAS. Voor dergelijke bewerkingen maakt u een middelste laag service die naar uw Event Hubs schrijft na de validatie van de bedrijfs regel, verificatie en controle.
- **Altijd https gebruiken**: altijd https gebruiken om een SAS te maken of te distribueren. Als een SAS wordt door gegeven via HTTP en onderschept, kan een aanvaller die een man-in-the-Middle-koppeling uitvoert, de SA'S lezen en deze vervolgens gebruiken, net zoals de beoogde gebruiker kan hebben, mogelijk gevoelige gegevens in gevaar brengen of beschadiging van gegevens door de kwaadwillende gebruiker kan veroorzaken.

## <a name="conclusion"></a>Conclusie
Toegangs handtekeningen voor delen zijn handig voor het leveren van beperkte machtigingen voor het Event Hubs van resources aan uw clients. Ze zijn een belang rijk onderdeel van het beveiligings model voor elke toepassing die gebruikmaakt van Azure Event Hubs. Als u de best practices in dit artikel volgt, kunt u SAS gebruiken om meer flexibiliteit van toegang tot uw resources te bieden, zonder de beveiliging van uw toepassing in gevaar te brengen.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende verwante artikelen: 

- [Aanvragen voor Azure Event Hubs verifiëren vanuit een toepassing met behulp van Azure Active Directory](authenticate-application.md)
- [Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot Event Hubs bronnen](authenticate-managed-identity.md)
- [Aanvragen voor Azure Event Hubs verifiëren met behulp van hand tekeningen voor gedeelde toegang](authenticate-shared-access-signature.md)
- [Toegang tot Event Hubs resources autoriseren met behulp van Azure Active Directory](authorize-access-azure-active-directory.md)


