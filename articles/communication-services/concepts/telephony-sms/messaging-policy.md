---
title: Bericht beleid
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over SMS Messa ging-beleids regels.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/19/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: bb9765c2620f45d67bf888f8bfe8a4dee450cfd6
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646694"
---
# <a name="azure-communication-services-messaging-policy"></a>Berichten beleid voor Azure Communication Services

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Azure Communication Services is bezig met het transformeren van de manier waarop onze klanten werken met hun clients door geavanceerde, aangepaste communicatie ervaringen te bouwen die gebruikmaken van dezelfde hoogwaardige services die micro soft teams en Skype gebruiken. Integreer SMS-berichten functionaliteit in uw communicatie oplossingen om uw klanten overal en overal toegang te bieden. U hoeft alleen maar enkele bericht vereisten te onthouden om aan de slag te gaan.

We weten dat de Messa ging-vereisten duidelijk lijken te zijn, maar ze zijn net zo eenvoudig als ' COMS ':

- C-toestemming
- O-Opt-Out
- Inhoud van M-bericht
- S-spoofing

Dit berichten beleid is ontwikkeld om u te helpen bij het voldoen aan de wettelijke vereisten en het uitlijnen met aanbevolen procedures. 

[!INCLUDE [Notice](../../includes/messaging-policy-include.md)]

## <a name="consent"></a>Toestemming 

### <a name="what-is-consent"></a>Wat is toestemming?

Toestemming is een overeenkomst tussen u en de ontvanger van het bericht, waarmee u geautomatiseerd berichten kunt verzenden. U moet toestemming krijgen voordat u het eerste bericht verzendt. Zorg er daarom voor dat u de ontvanger duidelijk maakt dat ze akkoord gaan met het ontvangen van berichten van u. Deze procedure wordt ' voorafgaande uitdrukkelijke toestemming ' ontvangen van de persoon die u wilt berichten.

De berichten die u verzendt, moeten overeenkomen met het type berichten dat de ontvanger heeft geaccepteerd en mag alleen worden verzonden naar het nummer dat de ontvanger aan u heeft gegeven. Als u van plan bent om informatieve berichten te verzenden, zoals herinneringen of waarschuwingen van afspraken, kan de toestemming worden geschreven of oraal. Als u promotie berichten wilt verzenden, zoals verkoop-of marketing berichten die een product of service promoten, moet toestemming worden geschreven.

### <a name="how-do-you-obtain-consent"></a>Hoe krijg ik toestemming?

Toestemming kan op verschillende manieren worden verkregen, zoals:

- Wanneer een gebruiker het telefoon nummer in een website invoert, 
- Wanneer een gebruiker een SMS-bericht uitwisseling initieert of
- Wanneer een gebruiker een tref woord voor aanmelding naar uw telefoon nummer stuurt. 
 
Ongeacht hoe toestemming wordt verkregen, moeten u en uw klanten ervoor zorgen dat de toestemming ondubbelzinnig is. Het bereik van de toestemming moet worden gewist aan de ontvanger.


### <a name="consent-requirements"></a>Vereisten voor toestemming:

- Geef een ' aanroep aan actie ' op alvorens toestemming te verkrijgen. U en uw klanten moeten potentiële bericht ontvangers voorzien van een ' aanroepen naar actie ' die ze nodigt om u aan te melden bij uw berichten programma. De aanroep van de actie moet mini maal: (1) de identiteit van de afzender van het bericht, (2), opt-in instructies, (3) opt-out-instructies, en (4) alle bijbehorende berichten kosten omvatten.
- Toestemming is niet overdraagbaar of kan niet worden toegewezen. Elke toestemming die een persoon biedt, kan niet worden overgedragen of verkocht aan een niet-gelieerde derde partij. Als u de toestemming van een persoon voor een derde partij verzamelt, moet u de persoon van de derde partij duidelijk identificeren. U moet ook aangeven dat de door u verkregen toestemming alleen geldt voor communicatie van de derde partij.
- De toestemming is beperkt in het doel. Een persoon die het nummer voor een bepaald doel heeft verstrekt om alleen communicatie te ontvangen voor dat specifieke doel en van die specifieke afzender van het bericht. Voordat u toestemming krijgt, moet u de ontvanger van het beoogde bericht duidelijk informeren als u terugkerende berichten of berichten van een partner verzendt.

### <a name="consent-best-practices"></a>Aanbevolen procedures voor de toestemming:

Naast de hierboven beschreven bericht vereisten, wilt u mogelijk verschillende algemene aanbevolen procedures implementeren, waaronder: 

- Gedetailleerde informatie over het aanroepen van actie. Om ervoor te zorgen dat u de juiste toestemming krijgt, geeft u
  - de naam of beschrijving van uw e-mail programma of product
  - het aantal (en) van waaruit ontvangers berichten ontvangen en 
  - alle toepasselijke voor waarden voor een afzonderlijke kiest om berichten van u te ontvangen.
- Nauw keurige administratie van toestemming. U moet de records bewaren van elke toestemming die een individu voor ten minste vier jaar biedt. De bestemmings records kunnen het volgende omvatten:
  - tijds tempels
  - het medium op basis waarvan toestemming is verkregen
  - de specifieke campagne waarvoor toestemming is verkregen
  - scherm opnamen
  - de sessie-ID of het IP-adres van de persoon die de e-mail toestuurt.
- Privacy-en beveiligings beleid. Ontwikkel aars worden aangemoedigd om een eenvoudig privacybeleid te bieden dat ontvangers van berichten kunnen beoordelen voordat hun toestemming wordt verkregen. We raden u ook aan proactieve beveiligings controles te onderhouden om persoonlijke gegevens van personen te beveiligen.


## <a name="double-opt-in-consent"></a>Instemming met dubbele opt-in:

Azure Communication Services raadt u aan om toestemming voor dubbel opt-in te gebruiken voor alle Messa ging-campagnes. Toestemming voor dubbel opt-in is een proces dat uit twee stappen bestaat, waarbij een individu eerst toestemming geeft om bepaalde typen berichten van u te ontvangen. Vervolgens verzendt u een opvolgings bericht om de toestemming te bevestigen. U moet meer berichten verzenden zodra de ontvanger van het bericht de toestemming bevestigt.

Het eerste bevestigings bericht dat u verzendt, moet uw identiteit bevatten, de optie voor het afmelden van toekomstige berichten (zoals het gebruik van een STOP opdracht), een gratis nummer of HELP-opdracht voor aanvullende informatie, de melding dat de persoon is inge schreven in een terugkerend bericht programma, een korte beschrijving van het programma, de frequentie waarmee u terugkerende berichten wilt verzenden en eventuele bijbehorende kosten. 

### <a name="does-azure-communication-services-ever-require-double-opt-in-consent"></a>Is voor Azure Communication Services ooit toestemming voor dubbel opt-in vereist?
Ja, hoewel toestemming voor dubbel opt-in wordt altijd aanbevolen, is voor Azure Communication Services vereist dat u toestemming voor het gebruik van dubbele opt-in gebruikt voor sommige typen Messa ging-campagnes, omdat ze veelvuldig worden gebruikt in phishing-schema's of in hun tendens om klachten over consumenten te kunnen leveren. Deze campagnes zijn onder andere:
- Berichten over automatische garantie
- Langlopende verzekerings plannen voor de gezondheid van de korte termijn
- Berichten over schulden herfinanciering of rente percentages die niet worden door een financiële instelling
- Berichten over het genereren van leads
- Wedstrijd, wedstrijden en giveaways
- Werk van-Home-aanbiedingen

De campagnes waarvoor de toestemming voor dubbel opt-in zijn vereist, zijn onderhevig aan wijzigingen van Azure Communication Services.

### <a name="exceptions-to-traditional-consent-rules"></a>Uitzonde ringen op regels voor traditionele toestemming:
Hoewel voorafgaande uitdrukkelijke toestemming normaal gesp roken vereist is voordat een bericht wordt verzonden, zijn er twee situaties waarin toestemming wordt gegeven aan een bericht dat een individu wordt geïmpliceerd.

- Ontvanger initieert een communicatie. Als een persoon een communicatie initieert door een bericht naar u te verzenden, kunt u relevante informatie verstrekken in antwoord op een specifieke query of aanvraag die in het bericht is opgenomen. De impliciete toestemming die de persoon heeft gegeven, is echter beperkt tot de conversatie die de persoon heeft geïnitieerd, tenzij u toestemming krijgt voor verdere communicatie.
- Uitzonde ringen voor specifieke services. Er zijn verschillende specifieke services waarvoor u mogelijk impliciete toestemming hebt om een bericht te initiëren. De meest voorkomende hiervan zijn: 
- pakket leverings berichten
- berichten van financiële instelling die betrekking hebben op tijdgebonden onderwerpen (zoals potentieel frauduleuze trans acties of gegevens schendingen)
- berichten van de gezondheids zorg die tijdgebonden informatie bevatten en een behandelings doel (zoals een afspraak of een examen herinnering, test resultaten en recept meldingen). 
 
Geen van deze berichten kan verzoeken of advertenties bevatten.


## <a name="opt-out"></a>Opt-out

Bericht ontvangers kunnen toestemming intrekken en zich afmelden voor het ontvangen van toekomstige berichten via een redelijk middel. U mag geen exclusieve manier aanwijzen voor ontvangers van berichten om toestemming in te trekken. 

### <a name="opt-out-requirements"></a>Opt-out vereisten:

Zorg ervoor dat bericht ontvangers op elk gewenst moment zich kunnen afmelden voor toekomstige berichten. U moet ook meerdere opt-outopties bieden. Nadat de ontvanger van een bericht heeft gestemd, moet u geen extra berichten verzenden, tenzij de persoon een vernieuwde toestemming verleent.

Een van de meest voorkomende opt-out-mechanismen is het sleutel woord ' STOP ' in het eerste bericht van elke nieuwe conversatie op te nemen. Bereid u voor om klanten te verwijderen die beantwoorden met een kleine "Stop" of andere veelvoorkomende tref woorden, zoals "Afmelden" of "Annuleren". Nadat een individuele toestemming intrekt, moet u deze verwijderen van alle terugkerende Messa ging-campagnes, tenzij ze uitdrukkelijk moeten blijven ontvangen van berichten van een bepaald programma.

### <a name="opt-out-best-practices"></a>Aanbevolen procedures voor opt-out:

Naast tref woorden zijn ook andere algemene opt-out-mechanismen beschikbaar voor klanten met een aangewezen opt-out-e-mail adres, het telefoon nummer van de mede werkers van de klant ondersteuning of een koppeling naar het afmelden van uw webpagina. 


### <a name="how-we-handle-opt-out-requests"></a>Hoe wij opt-out-aanvragen verwerken:

Als een afzonderlijke aanvraag voor het afmelden van toekomstige berichten op een gratis nummer van Azure Communication Services wordt uitgevoerd, wordt al het verdere verkeer van dat aantal automatisch gestopt. U moet echter nog steeds ervoor zorgen dat u geen extra berichten verzendt voor deze berichten campagne vanuit nieuwe of andere nummers. Als u expliciete toestemming hebt verkregen voor een andere berichten campagne, kunt u berichten van een ander nummer voor die campagne blijven verzenden. Bekijk onze sectie met veelgestelde vragen voor meer informatie over [het afhandelen van opt-out](https://github.com/Prakulka/azure-docs-pr/blob/master/articles/communication-services/concepts/telephony-sms/sms-faq.md#how-can-i-receive-messages-using-azure-communication-services)

## <a name="message-content"></a>Bericht inhoud

### <a name="adult-content"></a>Inhoud voor volwassenen:

Bericht inhoud die elementen van geslacht, Hate, alcohol, vuur wapens, tabak, gokken of wedstrijd en contesters bevat, kan aanvullende vereisten activeren. Deze inhoud is uitdrukkelijk verboden in sommige jurisdicties. Als u een bericht verzendt dat deze inhoud bevat, is het de bevoegdheid om aan alle toepasselijke wetgevingen te deel te gaan van de jurisdicties waarin de communicatie wordt ontvangen. Bij de aanvraag van de politie of de Azure Communication Services moet u worden voor bereid om toestemming te geven aan de lokale wetgeving waarbij inhoud voor volwassenen wordt gecontroleerd.

Zelfs als dergelijke inhoud niet onrecht matig is, moet u bij opt-in een ouderdoms verificatie mechanisme toevoegen om de ontvanger van het beoogde bericht van inhoud voor volwassenen te betrekken. In de Verenigde Staten zijn aanvullende wettelijke vereisten van toepassing op marketing communicaties die zijn gericht op kinderen onder de leeftijd van 13 jaar. 

### <a name="prohibited-content"></a>Verboden inhoud:

Azure Communication Services verbiedt bepaalde bericht inhoud, ongeacht toestemming. Verboden inhoud bevat:
- Inhoud waarmee ongeoorloofde activiteiten (bijvoorbeeld belasting fraude of Cruelty in de Verenigde Staten) worden gepromoot
- Hate spraak, lasterlijke speech, intimidatie of een andere spraak bepaald om Patent aanstootgevend te maken
- Porno grafie inhoud
- Obscene of vulgaire inhoud
- Intimidation en bedreigingen
- Inhoud die voornemens is om fraude te voor komen, te misleiden, schade te veroorzaken of op ongeoorloofde wijze een waarde te verkrijgen 
- Inhoud die schadelijk, discriminatie of geweld inbrengt
- Inhoud die schadelijke software verspreidt
- Inhoud die omzeilen de leeftijd van beperking vereisten

Wij behouden ons het recht voor om de lijst met inhoud van verboden berichten te allen tijde te wijzigen.

## <a name="spoofing"></a>Adresvervalsing (spoofing)

Spoofing is het gevolg van een misleidend of onjuist nummer dat wordt weer gegeven op het apparaat van een bericht ontvanger. Wij raden u ten zeerste aan om en elke service provider die u gebruikt voor het verzenden van vervalste berichten. Met spoofing Shields u de identiteit van de afzender van het bericht en voor komt u dat ontvangers ongewenste berichten eenvoudig kunnen afmelden. U moet er ook voor zorgen dat u alle toepasselijke wetgeving voor spoofing gebruikt.

## <a name="final-thoughts"></a>Laatste ideeën

### <a name="legal-responsibility"></a>Juridische verantwoordelijkheid:

Dit berichten beleid vormt geen juridisch advies en wij behouden ons het recht voor om het beleid op elk gewenst moment te wijzigen. Azure Communication Services is niet verantwoordelijk om ervoor te zorgen dat de inhoud, het tijdstip of de ontvangers van de berichten van onze klanten voldoen aan alle toepasselijke wettelijke vereisten. 

Onze klanten zijn verantwoordelijk voor alle bericht vereisten. Als u een platform of software provider bent die Azure Communication Services gebruikt voor berichten doeleinden, moet u uw klanten ook verplichten om te voldoen aan alle vereisten die in dit berichten beleid worden besproken. Voor verdere instructies biedt de CTIA handige [berichten principes en aanbevolen procedures](https://api.ctia.org/wp-content/uploads/2019/07/190719-CTIA-Messaging-Principles-and-Best-Practices-FINAL.pdf).

### <a name="penalties"></a>Tegen

We raden onze klanten aan om beleid en procedures te ontwikkelen en te implementeren die zijn ontworpen om te voldoen aan alle bericht vereisten. Schendingen van bericht vereisten kunnen leiden tot aanzienlijke boeten die snel kunnen worden geballond. Het is in uw beste belang dat u alle toepasselijke bericht vereisten leert en houdt en de beveiliging van efficiënte beperkende beveiligings maatregelen ontwikkelt om schendingen te verhelpen voordat ze zich verspreiden. Als u ons berichten beleid of andere wettelijke vereisten schendt, gaan we met u samen werken om te zorgen voor toekomstige naleving. Wij behouden ons echter het recht voor om een klant uit het Azure Communication Services-platform te verwijderen die een patroon van niet-naleving met ons bericht beleid of wettelijke vereisten laat zien.
