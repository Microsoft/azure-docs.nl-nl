---
title: Een aanbieding bekijken en publiceren naar micro soft Commercial Marketplace
description: Gebruik het partner centrum om uw aanbieding te verzenden naar een preview-versie, een voor beeld van uw aanbieding te bekijken en deze vervolgens te publiceren naar micro soft Commercial Marketplace.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/27/2020
ms.openlocfilehash: 8b63dfb015c7337e36dac3812e92ddc53914206d
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247162"
---
# <a name="how-to-review-and-publish-an-offer-to-the-commercial-marketplace"></a>Een aanbieding bekijken en publiceren naar de commerciële Marketplace

Dit artikel laat u zien hoe u met partner Center uw aanbieding voor publicatie kunt verzenden, een voor beeld van uw aanbieding kunt bekijken en deze vervolgens kunt publiceren naar de commerciële Marketplace. We laten ook zien hoe u uw publicatie status kunt controleren zoals deze wordt door de publicatie stappen. U moet al een aanbieding hebben gemaakt die u wilt publiceren.

## <a name="offer-status"></a>Status van aanbieding

U kunt de status van uw aanbieding bekijken op het tabblad **overzicht** van het dash board commerciële Marketplace in [partner centrum](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Een van de volgende status indicatoren wordt weer gegeven in de kolom **status** voor elke aanbieding.

| Status | Beschrijving |
| ------------ | ------------- |
| Concept | Het aanbod is gemaakt maar wordt niet gepubliceerd. |
| Publicatie wordt uitgevoerd | De aanbieding werkt via het publicatie proces. |
| Aandacht vereist | Er is een kritiek probleem gedetecteerd tijdens de certificering of tijdens een andere publicatie fase. |
| Preview | We hebben de aanbieding gecertificeerd, die nu een definitieve verificatie van de uitgever afwacht. Selecteer **Live gaan** om de aanbieding Live te publiceren. |
| Live | De aanbieding is live op Marketplace en kan worden gezien en verworven door klanten. |
| Verkopen in behandeling voor stoppen | Publisher heeft ' stoppen met verkopen ' op een aanbieding of plan geselecteerd, maar de actie is nog niet voltooid. |
| Niet beschikbaar in Marketplace | Een eerder gepubliceerde aanbieding in Marketplace is verwijderd. |
|||

## <a name="validation-and-publishing-steps"></a>Validatie-en publicatie stappen

Wanneer u klaar bent om een aanbieding voor publicatie in te dienen, selecteert u in de rechter bovenhoek van de Portal de optie **controleren en publiceren** . Op de pagina **controleren en publiceren** ziet u de status van elke pagina voor uw aanbieding. Dit kan een van de volgende zijn:

   - **Niet gestart** : de pagina is niet volledig.
   - **Onvolledig** : in de pagina ontbreekt de vereiste informatie of fouten die moeten worden opgelost. U moet terug naar de pagina gaan en deze bijwerken.
   - **Voltooid** : de pagina is voltooid. Alle vereiste gegevens zijn opgegeven en er zijn geen fouten.

Als een van de pagina's een andere status heeft dan **voltooid**, moet u het probleem op die pagina corrigeren en vervolgens terugkeren naar de pagina **controleren en publiceren** om te bevestigen dat de status nu als **voltooid** wordt weer gegeven. Voor sommige aanbiedings typen moet worden getest. Als dit het geval is, ziet u een **Opmerking voor het certificerings** veld waarin u test instructies moet geven aan het certificerings team en eventuele aanvullende notities die nuttig zijn voor de uitleg van uw app.

Nadat alle pagina's zijn voltooid en u toepasselijke test notities hebt ingevoerd, selecteert u **publiceren** om de validatie-en publicatie processen te starten. De fasen en de algehele volg orde kunnen variëren, afhankelijk van het type aanbieding dat u publiceert. De volgende tabel bevat een mogelijke publicatie stroom. Elke fase wordt gedetailleerd beschreven in de volgende secties.

| Fase | Wat gebeurt er? |
| ------------ | ------------- | ------------- |
| [Automatische validatie](#automated-validation-phase) | We verwerken een reeks geautomatiseerde validaties. |
| [Certificering](#certification-phase) | We voeren hand matige validaties uit. |
| [Voor beeld maken](#preview-creation-phase) | De aanbiedings pagina voor de preview-versie van uw aanbieding is beschikbaar voor iedereen met de preview-koppeling. Als uw aanbieding wordt verkocht via micro soft (transactable), kan alleen de doel groep die u hebt opgegeven op de pagina **voor beeld van de doel groep** van uw aanbieding kopen en toegang krijgen tot de aanbieding voor testen. |
| [Uitgever afmelden](#publisher-sign-off-phase) | We sturen u een e-mail met een aanvraag voor u om uw aanbieding te bekijken en goed te keuren. |
| [Publiceren](#publish-phase) | We voeren een reeks stappen uit om te controleren of de preview-aanbieding Live is gepubliceerd naar de commerciële Marketplace. |
|||

## <a name="automated-validation-phase"></a>Automatische validatie fase

De eerste stap in het publicatie proces is een set automatische validaties. Elke validatie stap komt overeen met een functie die u hebt gekozen tijdens het maken van de aanbieding. Elke validatie controle moet zijn voltooid voordat de aanbieding kan door gaan naar de volgende stap in het publicatie proces.

- **Setup van inkoop stroom van aanbieding** (<10 min.)

   Wij zorgen ervoor dat uw aanbieding kan worden afgehandeld wanneer klanten via de Azure Portal worden gekocht. Deze stap is alleen van toepassing op aanbiedingen die via micro soft worden verkocht.

- **Gegevens validatie test station** (~ 5 min)

   We valideren de gegevens die u hebt ingevoerd op de pagina technische configuratie van de aanbieding. U kunt test drive functionaliteit testen en goed keuren. Deze stap is alleen van toepassing op aanbiedingen waarvoor een test drive is ingeschakeld.

- Het inrichten van het **station testen** (~ 30 minuten)

    Nadat de gegevens en functionaliteit van uw test drive in de vorige stap zijn gevalideerd, implementeren en repliceren we exemplaren van uw test drive zodat ze klaar zijn voor gebruik door de klant. Deze stap is alleen van toepassing op aanbiedingen waarvoor een test drive is ingeschakeld.

- **Validatie en registratie van lead beheer** (<15 min)

    We bevestigen dat uw systeem voor het beheer van potentiële klanten leads kan ontvangen op basis van de details die u hebt ingevoerd op de pagina **aanbieding instellen** . Deze stap is alleen van toepassing op aanbiedingen waarvoor Lead beheer is ingeschakeld.

## <a name="certification-phase"></a>Certificerings fase

Aanbiedingen die worden verzonden naar de commerciële Marketplace moeten gecertificeerd zijn voordat ze kunnen worden gepubliceerd. Aanbiedingen worden uitvoerig getest, sommige geautomatiseerd en anderen hand matig. Zie voor meer informatie het [beleid voor commerciële Marketplace-certificering](/legal/marketplace/certification-policies).

### <a name="types-of-validation-that-take-place-during-certification"></a>Typen validatie die worden uitgevoerd tijdens het certificerings proces

Er zijn drie validatie niveaus opgenomen in het certificerings proces voor elk ingediende aanbod.

- Zakelijke geschiktheid voor Publisher
- Validatie van inhoud
- Technische validatie

#### <a name="publisher-business-eligibility"></a>Zakelijke geschiktheid voor Publisher

Elk aanbiedings type controleert een reeks vereiste criteria voor basis geschiktheid. Dit criterium kan de status van de uitgever, de bewaarde vaardig heden, het competentie niveau, enzovoort bevatten.

#### <a name="content-validation"></a>Validatie van inhoud

De gegevens die u hebt ingevoerd tijdens het maken van uw aanbieding, worden gecontroleerd op kwaliteit en relevantie. Met deze controles worden uw vermeldingen voor de details van Marketplace-vermelding, prijzen, Beschik baarheid, bijbehorende plannen, enzovoort gecontroleerd. Om te voldoen aan de vermeldings criteria van Microsoft AppSource en Azure Marketplace, zullen we valideren dat uw aanbieding de volgende bevat:

- Een titel die de aanbieding nauw keurig beschrijft
- Goed geschreven beschrijvingen die een uitgebreid overzicht en toegevoegde waarde bieden
- Scherm afbeeldingen en Video's van kwaliteit
- Een uitleg van de manier waarop de aanbieding gebruikmaakt van micro soft-platforms en-hulpprogram ma's.

Meer informatie over de criteria voor de validatie van de inhoud vindt u in de [algemene aanbiedings beleidsregels](/legal/marketplace/certification-policies#100-general).

#### <a name="technical-validation"></a>Technische validatie

Tijdens de technische validatie onderneemt de aanbieding (pakket of binair) de volgende controles.

- Gescand op malware
- Bewaakte netwerk aanroepen
- Pakket geanalyseerd
- Grondige scan van de functionaliteit van het aanbod

De aanbieding wordt getest op verschillende platforms en versies om ervoor te zorgen dat deze krachtig is.

### <a name="certification-failure-report"></a>Rapport van certificerings fout

Als uw aanbieding een van de aanbiedingen, technische of beleids controles mislukt of als u niet in aanmerking komt voor een aanbieding van dit type, sturen we een certificaat voor een certificerings fout naar u.

Dit rapport bevat beschrijvingen van alle beleids regels die zijn mislukt, samen met controle notities. Bekijk dit e-mail rapport, Los eventuele problemen op, breng updates voor uw aanbieding waar nodig aan en verzend de aanbieding opnieuw via de [Portal voor commerciële Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) in het partner centrum. U kunt de aanbieding zo vaak als nodig opnieuw verzenden totdat de certificering is door gegeven.

## <a name="preview-creation-phase"></a>Fase voor preview-versie maken

Tijdens het maken van de preview-fase maken we een versie van uw aanbieding die alleen toegankelijk is voor de doel groep die u hebt opgegeven op de pagina **voor beeld van de doel groep** van uw aanbieding, indien van toepassing. De preview-versie van uw aanbieding is niet beschikbaar voor iedereen buiten het publiek van de preview-periode tot u de Live-aanbieding publiceert.

> [!NOTE]
> Gebruik de preview-doel groep niet om personen buiten uw organisatie inzicht te geven in een aanbieding. Gebruik in plaats daarvan de optie voor de privé-aanbieding. Uw aanbieding is op dit moment niet volledig getest en gevalideerd en is niet gereed voor externe distributie.

## <a name="cancel-publishing"></a>Publicatie annuleren

Een aanbieding annuleren met de status **publiceren in voortgang** :

1. Selecteer de naam van de aanbieding om de **overzichts** pagina van de aanbieding te openen.
1. Selecteer **Publiceren annuleren** in de rechter bovenhoek van de pagina.
1. Bevestig dat u wilt stoppen met het publiceren van de aanbieding.

Als u de aanbieding op een later tijdstip wilt publiceren, moet u eerst het publicatie proces starten.

> [!NOTE]
> U kunt voor komen dat een aanbieding alleen wordt gepubliceerd als de aanbieding nog niet is verzonden naar de stap van de uitgever. Nadat u **live-verbinding** maken hebt geselecteerd, hebt u geen optie om de publicatie langer te annuleren.

## <a name="publisher-sign-off-phase"></a>Aanmeldings fase van de uitgever

Wanneer de aanbieding klaar is om te worden gecontroleerd en u bent afgemeld, sturen we u een e-mail om u te vragen uw aanbiedings voorbeeld te controleren en goed te keuren. U kunt ook de pagina **overzicht van aanbieding** in uw browser vernieuwen om te zien of uw aanbieding de afmeldings fase van de uitgever heeft bereikt. Als dat het geval is, is de knop **Live** en voor beeld-koppelingen beschikbaar.

De volgende scherm afbeelding toont de pagina **overzicht van aanbieding** voor een SaaS-aanbieding. De validatie stappen die u op deze pagina ziet, variëren afhankelijk van het type aanbieding en de selecties die u hebt gemaakt tijdens het maken van de aanbieding.

![Illustreert de pagina overzicht van aanbieding voor een aanbieding in partner centrum. De knop Go Live en de preview-koppelingen worden weer gegeven.](./media/publish-status-publisher-signoff.png)

### <a name="previewing-and-approving-your-offer"></a>Uw aanbieding bekijken en goed keuren

> [!IMPORTANT]
> Als u de end-to-end aankoop-en configuratie stroom wilt valideren, koopt u uw aanbieding terwijl deze in preview is. Informeer micro soft eerst met een [ondersteunings ticket](https://aka.ms/marketplacesupport) om ervoor te zorgen dat er geen kosten worden verwerkt. Als uw aanbieding een contact persoon voor u is, test u of een lead is gemaakt zoals verwacht door de contact gegevens voor deze persoon op te geven tijdens de preview-versie.

Op de **overzichts** pagina van de aanbieding ziet u een voor beeld van koppelingen onder de knop **Go Live** . Er is een koppeling naar de preview-versie van AppSource, de preview van Azure Marketplace, of beide, afhankelijk van de opties die u hebt gekozen bij het maken van uw aanbieding. Als u ervoor kiest om uw aanbieding via micro soft te verkopen, kan iedereen die is toegevoegd aan de preview-doel groep de aanschaf en implementatie van uw aanbieding testen om ervoor te zorgen dat deze aan uw vereisten voldoet tijdens deze fase.

Nadat u uw Preview hebt goedgekeurd, selecteert u **Live** om uw aanbieding Live naar de commerciële Marketplace te publiceren. 

Als u wijzigingen wilt aanbrengen nadat u een voor beeld van de aanbieding hebt bekeken, kunt u uw publicatie aanvraag bewerken en opnieuw verzenden. Als uw aanbieding al Live is en beschikbaar is voor het publiek in de Marketplace, worden de updates die u aanbrengt pas Live wanneer u **Go* selecteert. Zie [een bestaande aanbieding bijwerken in de commerciële Marketplace](partner-center-portal/update-existing-offer.md) voor meer informatie.

## <a name="publish-phase"></a>Publicatie fase

Nu u ervoor hebt gekozen om live te gaan met uw aanbieding, waardoor deze beschikbaar is in de commerciële Marketplace, voeren we een reeks definitieve validatie controles uit om ervoor te zorgen dat de Live-aanbieding is geconfigureerd, net als de preview-versie van de aanbieding.

- **Setup van inkoop stroom van aanbieding** (>10 min.)

    Wij zorgen ervoor dat uw aanbieding kan worden afgehandeld wanneer klanten via de Azure Portal worden gekocht. Deze stap is alleen van toepassing op aanbiedingen die via micro soft worden verkocht.

- **Gegevens validatie test station** (~ 5 min)

    We valideren de gegevens die u hebt ingevoerd op de pagina technische configuratie van de aanbieding. U kunt test drive functionaliteit testen en goed keuren. Deze stap is alleen van toepassing op aanbiedingen waarvoor een test drive is ingeschakeld.

- Het inrichten van het **station testen** (~ 30 minuten)

    We implementeren en repliceren exemplaren van uw test drive zodat ze klaar zijn voor gebruik door de klant. Deze stap is alleen van toepassing op aanbiedingen waarvoor een test drive is ingeschakeld.

- **Validatie en registratie van lead beheer** (>15 min)

    We bevestigen dat uw lead beheersysteem leads van klanten kan ontvangen op basis van de gegevens die op de pagina met de installatie van uw **aanbieding** staan. Deze stap is alleen van toepassing op aanbiedingen waarvoor Lead beheer is ingeschakeld.

- **Definitieve publicatie (>30 minuten)**

    We zorgen ervoor dat uw aanbieding openbaar beschikbaar wordt op Marketplace.

Nadat deze validatie controles zijn voltooid, is uw aanbieding Live in de Marketplace.

## <a name="publishing-history"></a>Publicatie geschiedenis

De **geschiedenis** pagina in partner centrum toont de publicatie gebeurtenissen voor uw commerciële Marketplace-aanbiedingen. Voor elke gebeurtenis wordt op de pagina de gebruiker weer gegeven die de actie heeft gestart, het type gebeurtenis en de datum en tijd van de gebeurtenis. De [validatie-en publicatie stappen](#validation-and-publishing-steps) worden weer gegeven met de datum en tijd van voltooiing.

De geschiedenis van uw aanbieding bekijken:

1.    Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2.    Selecteer in het navigatie menu aan de linkerkant de optie **commerciële Marketplace**-  >  **overzicht**.
3.    Selecteer uw aanbieding.
4.    Selecteer het tabblad **geschiedenis** in het navigatie menu aan de linkerkant. Op de pagina vindt u informatie over de volgende gebeurtenissen, zoals van toepassing op uw aanbieding:

|Gebeurtenis    |Beschrijving    |
|---------|---------------|
|Aanbieding gemaakt    |De aanbieding is gemaakt in het partner centrum. Een gebruiker heeft het aanbiedings type, aanbiedings-id en aanbiedings alias geselecteerd in **commercieel Marketplace**-  >  **overzicht**.    |
|Gemaakt plan: *naam van abonnement*    |Een gebruiker heeft een nieuw plan gemaakt door de plan-ID en plan naam op te geven op het tabblad Overzicht van het **plan** .</br>*Deze gebeurtenis geldt alleen voor aanbiedings typen die ondersteuning bieden voor abonnementen*.    |
|Verwijderd plan    |Een gebruiker heeft een concept abonnement verwijderd dat niet is gepubliceerd door **concept verwijderen** te selecteren op de **overzichts** pagina van het abonnement.</br>*Deze gebeurtenis geldt alleen voor aanbiedings typen die ondersteuning bieden voor abonnementen*.    |
|Gestart plan stoppen verkoop: *plan naam*    |Een gebruiker heeft een plan voor stoppen en verkopen gestart door **stoppen** te selecteren op de **overzichts** pagina van het abonnement.</br>*Deze gebeurtenis geldt alleen voor aanbiedings typen die ondersteuning bieden voor abonnementen*.    |
|Plan voor ongedaan maken stoppen verkoop: *plan naam*    |Een gebruiker heeft een plan voor stoppen en verkopen geannuleerd door de optie **ongedaan maken stoppen** te selecteren op de **overzichts** pagina van het abonnement.</br>*Deze gebeurtenis geldt alleen voor aanbiedings typen die ondersteuning bieden voor abonnementen*.    |
|Aangeboden aanbieding voor preview    |Een gebruiker heeft de aanbieding aangeboden als preview door **publiceren** te selecteren op de pagina **controleren en publiceren** .    |
|Verzenden geïnitieerd om de preview-versie te annuleren    |Een gebruiker heeft gevraagd de aanbiedings publicatie te annuleren als preview door te klikken op **Publiceren annuleren** op de **overzichts** pagina van de aanbieding na de verzen ding naar de preview-versie.</br>*Deze gebeurtenis wordt weer gegeven als de annulerings aanvraag wordt verwerkt*.    |
|De verzen ding is geannuleerd voor de preview-versie    |Een gebruiker heeft de aanbiedings publicatie geannuleerd als voor beeld door de selectie van de pagina **aanbieding** **Annuleren** te selecteren na de verzen ding naar de preview-versie.</br>*Deze gebeurtenis wordt weer gegeven nadat de annulerings aanvraag is verwerkt*.    |
|Meld u aan om live te gaan    |Een gebruiker heeft de aanbieding op commerciële Marketplace gepubliceerd door **Live go** te selecteren op de **overzichts** pagina van de aanbieding.    |
|Publicatie van publiceren op Marketplace is geïnitieerd    |Een gebruiker heeft de aanbiedings publicatie geannuleerd door na de afmelding **Annuleren** te selecteren op de pagina **overzicht van aanbieding** .</br>*Deze gebeurtenis wordt weer gegeven als de annulerings aanvraag wordt verwerkt*.    |
|Het publiceren naar de commerciële Marketplace is geannuleerd    |Een gebruiker heeft de aanbiedings publicatie geannuleerd door na de afmelding **Annuleren** te selecteren op de pagina **overzicht van aanbieding** .</br>*Deze gebeurtenis wordt weer gegeven nadat de annulerings aanvraag is verwerkt*.    |
|Persoonlijke doel groep synchroniseren    |Een gebruiker heeft de persoonlijke doel groep bijgewerkt en gesynchroniseerd door **persoonlijke doel groep** te selecteren op de pagina overzicht van het **abonnement** of de pagina **prijs & Beschik baarheid plannen** .</br>*Deze gebeurtenis geldt alleen voor aanbiedings typen die persoonlijke plannen ondersteunen*.    |
|Verkoop aanbieding stoppen    |Een gebruiker heeft de aanbieding niet meer verkocht door te klikken op **verkopen stoppen** op de **overzichts** pagina van de aanbieding.    |

> [!NOTE]
> De pagina geschiedenis geeft geen melding wanneer een concept van een aanbieding is opgeslagen.

### <a name="filter-options"></a>Filteropties

U kunt filters gebruiken om de volledige geschiedenis van uw aanbieding te verfijnen tot specifieke publicatie gebeurtenissen:

1.    Selecteer de filter knop in de rechter bovenhoek van de pagina.
2.    Kies een filter en selecteer vervolgens **Toep assen** om te zien welke geschiedenis gebeurtenissen overeenkomen met de criteria die u hebt geselecteerd.
3.    Selecteer **filters wissen** om terug te keren naar de volledige geschiedenis van uw aanbieding.

Er zijn vier filters:
* Gebeurtenissen
* Gebruikers
* Date
* Pagina's

Wanneer u het filter **Pages** kiest, kunt u een van de partner centrum-pagina's selecteren die van toepassing zijn op uw aanbiedings type. Wanneer u **dit toepast,** worden in het filter alle **ingediende aanbiedingen** weer gegeven met wijzigingen aan de pagina die u hebt geselecteerd.

* Voor alle aanbiedingen is de pagina **aanbieding instellen** standaard opgenomen voor elke indienings gebeurtenis.
* Voor aanbiedingen die ondersteuning bieden voor abonnementen, wordt de **overzichts** pagina van het abonnement opgenomen voor elke indienings gebeurtenis.
* Voor aanbiedingen die test drive ondersteunen, wordt de pagina **test drive** opgenomen voor elke indienings gebeurtenis.

### <a name="users"></a>Gebruikers

Als een gebeurtenis is geïnitieerd door een gebruiker, toont de pagina geschiedenis de gebruiker aan de hand van de volgende scenario's:

#### <a name="the-event-was-initiated-by-the-publisher"></a>De gebeurtenis is geïnitieerd door de uitgever

Voor gebruikers met publicatie machtigingen voor een aanbieding wordt de naam weer gegeven voor de publicatie gebeurtenissen die ze initiëren.

[![Op de pagina geschiedenis wordt de naam weer gegeven van gebruikers met publicatie machtigingen.](./media/review-publish-offer/event-initiated-by-publisher.png)](./media/review-publish-offer/event-initiated-by-publisher.png#lightbox)

#### <a name="the-event-was-initiated-by-microsoft"></a>De gebeurtenis is geïnitieerd door micro soft

U kunt micro soft-beheerders machtigingen geven om namens u acties te initiëren of om corrigerende maat regelen te nemen na een onverwachte systeem fout. De micro soft-naam en het logo worden weer gegeven voor het publiceren van gebeurtenissen die door micro soft zijn geïnitieerd namens uw account.

[![Voor beeld van hoe de geschiedenis pagina gebeurtenissen weergeeft die door micro soft zijn geïnitieerd.](./media/review-publish-offer/event-initiated-by-microsoft.png)](./media/review-publish-offer/event-initiated-by-microsoft.png#lightbox)

#### <a name="the-event-was-initiated-by-an-unidentified-user"></a>De gebeurtenis is geïnitieerd door een niet-geïdentificeerde gebruiker

Gebruikers die niet meer aan een account zijn gekoppeld, krijgen hun naam uit de kolom **gestart door te** verwijderen nadat hun publicatie machtigingen zijn ingetrokken.

[![Voor beeld van hoe de geschiedenis pagina gebeurtenissen weergeeft die worden geïnitieerd door onbekende gebruikers.](./media/review-publish-offer/event-initiated-by-unidentified-user.png)](./media/review-publish-offer/event-initiated-by-unidentified-user.png#lightbox)

#### <a name="validation-and-publishing-steps"></a>Validatie-en publicatie stappen

Er wordt geen gebruiker weer gegeven voor systeem processen die overeenkomen met de [stappen voor validatie en publiceren](#validation-and-publishing-steps). Deze gebeurtenissen worden gekleurd op basis van de voltooiings status van de gebeurtenis.

[![Voor beeld van hoe de geschiedenis pagina validatie-en publicatie stappen weergeeft.](./media/review-publish-offer/validation-and-publishing-event.png)](./media/review-publish-offer/validation-and-publishing-event.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

[Toegang krijgen tot analytische rapporten voor de commerciële Marketplace in het partner centrum](partner-center-portal/analytics.md)
