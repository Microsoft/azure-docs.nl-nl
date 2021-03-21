---
title: Integratie patronen voor Smart-contracten-Azure Block Chain workbench
description: Overzicht van de integratie patronen voor Smart-contracten in azure Block Chain Workbench preview.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: mmercuri
ms.openlocfilehash: dae63e16356e825d3be31380df1648749e59d8bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015499"
---
# <a name="smart-contract-integration-patterns"></a>Integratiepatronen voor slimme contracten

Slimme contracten vertegenwoordigen vaak een zakelijke werk stroom die moet worden geïntegreerd met externe systemen en apparaten.

De vereisten van deze werk stromen zijn een nood zaak om trans acties te initiëren op een gedistribueerd groot boek dat gegevens van een extern systeem, een service of een apparaat bevat. Daarnaast moeten externe systemen reageren op gebeurtenissen die afkomstig zijn van slimme contracten op een gedistribueerd groot boek.

De integratie van REST API en berichten verzendt trans acties van externe systemen naar slimme contracten die zijn opgenomen in een Azure Block Chain Workbench-toepassing. Ook worden gebeurtenis meldingen naar externe systemen verzonden op basis van wijzigingen die in een toepassing plaatsvinden.

Voor scenario's voor gegevens integratie bevat Azure Block Chain Workbench een set database weergaven waarmee u een combi natie van transactionele gegevens uit de Block chain en meta gegevens over toepassingen en slimme contracten kunt samen voegen.

Daarnaast is het mogelijk dat er voor sommige scenario's, zoals die met betrekking tot de toeleverings keten of media, ook de integratie van documenten vereist is. Hoewel Azure Block Chain Workbench geen API-aanroepen biedt voor het rechtstreeks verwerken van documenten, kunnen documenten worden opgenomen in een Block Chain-toepassing. Deze sectie bevat ook dat patroon.

Deze sectie bevat de patronen die zijn geïdentificeerd voor het implementeren van elk van deze typen integraties in uw end-to-end-oplossingen.

## <a name="rest-api-based-integration"></a>Integratie op basis van REST API

De mogelijkheden van Azure Block Chain Workbench gegenereerde webtoepassingen worden weer gegeven via de REST API. De mogelijkheden zijn onder andere Azure Block Chain Workbench upload, configuratie en beheer van toepassingen, het verzenden van trans acties naar een gedistribueerd groot boek en het uitvoeren van query's op meta gegevens van toepassingen en de grootboek informatie.

De REST API wordt voornamelijk gebruikt voor interactieve clients, zoals web-, mobiele en bot-toepassingen.

In deze sectie wordt aandacht besteed aan patronen die gericht zijn op de aspecten van de REST API die trans acties verzenden naar een gedistribueerd groot boek en patronen waarmee gegevens over trans acties vanuit Azure Block Chain Workbench uit de *keten* database worden opgevraagd.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Trans acties vanuit een extern systeem naar een gedistribueerd groot boek verzenden

Met Azure Block Chain Workbench REST API worden geverifieerde aanvragen verzonden voor het uitvoeren van trans acties in een gedistribueerd groot boek.

![Trans acties naar een gedistribueerd groot boek verzenden](./media/integration-patterns/send-transactions-ledger.png)

Trans acties worden uitgevoerd met behulp van het proces dat eerder is weer gegeven, waarbij:

-   De externe toepassing wordt geverifieerd bij de Azure Active Directory ingericht als onderdeel van de Azure Block Chain Workbench-implementatie.
-   Geautoriseerde gebruikers ontvangen een Bearer-token dat met aanvragen naar de API kan worden verzonden.
-   Externe toepassingen voeren de REST API met behulp van het Bearer-token.
-   De aanvraag wordt door de REST API als een bericht verpakt en verzonden naar de Service Bus. Hier wordt het opgehaald, ondertekend en verzonden naar het juiste gedistribueerde groot boek.
-   De REST API maakt een aanvraag voor de Azure Block Chain Workbench SQL-Data Base om de aanvraag vast te leggen en de huidige inrichtings status in te stellen.
-   De SQL-data base retourneert de inrichtings status en de API-aanroep retourneert de ID naar de externe toepassing die de naam heeft aangeroepen.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Query's uitvoeren op meta gegevens van Block Chain Workbench en gedistribueerde grootboek transacties

Met Azure Block Chain Workbench REST API worden geverifieerde aanvragen verzonden naar query Details met betrekking tot het uitvoeren van een slim contract in een gedistribueerd groot boek.

![Meta gegevens opvragen](./media/integration-patterns/querying-metadata.png)

Query's worden uitgevoerd op basis van het proces dat eerder is getoond, waarbij:

1. De externe toepassing wordt geverifieerd bij de Azure Active Directory ingericht als onderdeel van de Azure Block Chain Workbench-implementatie.
2. Geautoriseerde gebruikers ontvangen een Bearer-token dat met aanvragen naar de API kan worden verzonden.
3. Externe toepassingen voeren de REST API met behulp van het Bearer-token.
4. De REST API query's uitvoeren op de gegevens voor de aanvraag van de SQL-data base en retourneert deze naar de client.

## <a name="messaging-integration"></a>Integratie van berichten

Integratie van berichten vereenvoudigt de interactie met systemen, services en apparaten waarbij een interactieve aanmelding niet mogelijk of wenselijk is. Integratie van berichten gericht op twee typen berichten: berichten die trans acties aanvragen, worden uitgevoerd op een gedistribueerd groot boek, en gebeurtenissen die door het groot boek worden weer gegeven wanneer er trans acties zijn uitgevoerd.

Integratie van berichten richt zich op de uitvoering en controle van trans acties met betrekking tot het maken van een gebruiker, het maken van contracten en het uitvoeren van trans acties voor contracten en wordt voornamelijk gebruikt door *headless* back-endsystemen.

In deze sectie ziet u patronen die gericht zijn op de aspecten van de API op basis van berichten die trans acties verzendt naar een gedistribueerd groot boek en patronen die gebeurtenis berichten vertegenwoordigen die worden weer gegeven door het onderliggende gedistribueerde groot boek.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Eenrichtings gebeurtenis levering van een slim contract naar een event consumer 

In dit scenario treedt er een gebeurtenis op in een slim contract, bijvoorbeeld een status wijziging of de uitvoering van een specifiek type trans actie. Deze gebeurtenis wordt uitgezonden via een Event Grid naar downstream-consumenten en deze consumenten nemen vervolgens de nodige maat regelen.

Een voor beeld van dit scenario is dat wanneer een trans actie wordt uitgevoerd, een consument wordt gewaarschuwd en actie kan ondernemen, zoals het vastleggen van de gegevens in een SQL-data base of de Common Data Service. Dit scenario is hetzelfde patroon dat Workbench volgt om de SQL-Data Base *uit de buiten keten* te vullen.

Een ander zou zijn als een slimme contract overgang naar een bepaalde status, bijvoorbeeld wanneer een contract in een *OutOfCompliance* gaat. Wanneer deze status wijziging optreedt, kan een waarschuwing worden gegenereerd om te worden verzonden naar de mobiele telefoon van de beheerder.

![Eenrichtings gebeurtenis levering](./media/integration-patterns/one-way-event-delivery.png)

Dit scenario treedt op met behulp van het proces dat eerder is weer gegeven, waarbij:

-   Het slimme contract verandert in een nieuwe status en verzendt een gebeurtenis naar het groot boek.
-   Het groot boek ontvangt en levert de gebeurtenis aan Azure Block Chain Workbench.
-   Azure Block Chain Workbench is geabonneerd op gebeurtenissen van het groot boek en ontvangt de gebeurtenis.
-   Azure Block Chain Workbench publiceert de gebeurtenis naar abonnees op het Event Grid.
-   Externe systemen zijn geabonneerd op de Event Grid, gebruiken het bericht en nemen de nodige maat regelen.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Eenrichtings gebeurtenis aflevering van een bericht van een extern systeem naar een slim contract

Er is ook een scenario dat van de tegenovergestelde richting loopt. In dit geval wordt een gebeurtenis gegenereerd door een sensor of een extern systeem en de gegevens van die gebeurtenis moeten naar een slim contract worden verzonden.

Een voor beeld hiervan is het leveren van gegevens van financiële markten, bijvoorbeeld prijzen van grond stoffen, aandelen of obligaties, aan een slim contract.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Directe levering van een Azure Block Chain workbench in de verwachte indeling

Sommige toepassingen zijn gebouwd om te integreren met Azure Block Chain Workbench en kunnen berichten rechtstreeks genereren en verzenden in de verwachte indelingen.

![Directe levering](./media/integration-patterns/direct-delivery.png)

Deze levering vindt plaats met behulp van het proces dat eerder is weer gegeven, waarbij:

-   Er treedt een gebeurtenis op in een extern systeem dat het maken van een bericht voor Azure Block Chain Workbench activeert.
-   Het externe systeem heeft code geschreven om dit bericht in een bekende indeling te maken en het rechtstreeks naar de Service Bus te verzenden.
-   Azure Block Chain Workbench is geabonneerd op gebeurtenissen van de Service Bus en haalt het bericht op.
-   Azure Block Chain Workbench initieert een aanroep van het groot boek, waarbij gegevens van het externe systeem naar een specifiek contract worden verzonden.
-   Na ontvangst van het bericht wordt het contract overgezet naar een nieuwe status.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Levering van een bericht in een indeling die onbekend is bij Azure Block Chain workbench

Sommige systemen kunnen niet worden gewijzigd voor het leveren van berichten in de standaard indelingen die worden gebruikt door Azure Block Chain Workbench. In dergelijke gevallen kunnen bestaande mechanismen en bericht indelingen van deze systemen vaak worden gebruikt. Met name de systeem eigen bericht typen van deze systemen kunnen worden getransformeerd met behulp van Logic Apps, Azure Functions of andere aangepaste code, zodat deze kan worden toegewezen aan een van de standaard indelingen voor berichten die worden verwacht.

![Onbekende bericht indeling](./media/integration-patterns/unknown-message-format.png)

Dit gebeurt op basis van het proces dat eerder is weer gegeven, waarbij:

-   Er treedt een gebeurtenis op in een extern systeem dat het maken van een bericht activeert.
-   Een logische app of aangepaste code wordt gebruikt om het bericht te ontvangen en te transformeren naar een standaard bericht voor Azure Block Chain Workbench-indeling.
-   De logische app verzendt het getransformeerde bericht rechtstreeks naar de Service Bus.
-   Azure Block Chain Workbench is geabonneerd op gebeurtenissen van de Service Bus en haalt het bericht op.
-   Azure Block Chain Workbench initieert een aanroep van het groot boek, waarbij gegevens van het externe systeem naar een specifieke functie in het contract worden verzonden.
-   De functie wordt uitgevoerd en wijzigt doorgaans de status. De wijziging van de status verplaatst de bedrijfs werk stroom in het slimme contract, zodat andere functies nu kunnen worden uitgevoerd, indien van toepassing.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Besturings element overstappen op een extern proces en wacht op voltooiing

Er zijn scenario's waarin een slim contract de interne uitvoering moet stoppen en aan een extern proces kan worden overgedragen. Het externe proces wordt vervolgens voltooid, een bericht verzonden naar het slimme contract en de uitvoering wordt vervolgens voortgezet in het slimme contract.

#### <a name="transition-to-the-external-process"></a>Overgang naar het externe proces

Dit patroon wordt gewoonlijk geïmplementeerd met de volgende methode:

-   De slimme contract overgangen naar een specifieke status. In deze status kunt u geen of een beperkt aantal functies uitvoeren totdat een extern systeem een gewenste actie onderneemt.
-   De wijziging van de status wordt geoppereerd als een gebeurtenis voor een downstream-Consumer.
-   De downstream-consument ontvangt de gebeurtenis en activeert het uitvoeren van externe code.

![Het diagram toont een status wijziging binnen het contract waardoor een gebeurtenis wordt uitgevoerd naar gedistribueerde grootboek. Block Chain Workbench kiest vervolgens de gebeurtenis en publiceert deze.](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Het besturings element van het slimme contract retour neren

Afhankelijk van de mogelijkheid om het externe systeem aan te passen, kunnen er mogelijk geen berichten worden bezorgd in een van de standaard indelingen die Azure Block Chain Workbench verwacht. Bepaal op basis van de mogelijkheid externe systemen een van deze berichten te genereren welke van de volgende twee retour paden worden genomen.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Directe levering van een Azure Block Chain workbench in de verwachte indeling

![Het diagram toont een P I-bericht van het externe systeem dat wordt opgehaald door Block Chain Workbench via de Service Bus. Block Chain Workbench verzendt vervolgens namens de agent een bericht als een trans actie naar gedistribueerd groot boek. Het wordt door gegeven aan contract, waarbij een status wijziging wordt veroorzaakt.](./media/integration-patterns/direct-delivery.png)

In dit model wordt de communicatie met het contract en de volgende status wijziging uitgevoerd na het vorige proces waarbij-

-   Na het bereiken van de voltooiing of een specifieke mijl paal tijdens het uitvoeren van externe code, wordt een gebeurtenis verzonden naar de Service Bus die is verbonden met Azure Block Chain Workbench.

-   Voor systemen die niet rechtstreeks kunnen worden aangepast om een bericht te schrijven dat voldoet aan de verwachtingen van de API, wordt het getransformeerd.

-   De inhoud van het bericht wordt verpakt en verzonden naar een specifieke functie van het slimme contract. Deze levering geschiedt namens de gebruiker die is gekoppeld aan het externe systeem.

-   De functie wordt uitgevoerd en wijzigt doorgaans de status. De wijziging van de status verplaatst de bedrijfs werk stroom in het slimme contract, zodat andere functies nu kunnen worden uitgevoerd, indien van toepassing.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Levering van een bericht in een indeling die onbekend is bij Azure Block Chain workbench

![Onbekende bericht indeling](./media/integration-patterns/unknown-message-format.png)

In dit model waarbij een bericht in een standaard indeling niet rechtstreeks kan worden verzonden, wordt de communicatie met het contract en de volgende status wijziging uitgevoerd na het vorige proces, waarbij:

1.  Na het bereiken van de voltooiing of een specifieke mijl paal tijdens het uitvoeren van externe code, wordt een gebeurtenis verzonden naar de Service Bus die is verbonden met Azure Block Chain Workbench.
2.  Een logische app of aangepaste code wordt gebruikt om het bericht te ontvangen en te transformeren naar een standaard bericht voor Azure Block Chain Workbench-indeling.
3.  De logische app verzendt het getransformeerde bericht rechtstreeks naar de Service Bus.
4.  Azure Block Chain Workbench is geabonneerd op gebeurtenissen van de Service Bus en haalt het bericht op.
5.  Azure Block Chain Workbench initieert een aanroep van het groot boek, waarbij gegevens van het externe systeem naar een specifiek contract worden verzonden.
6. De inhoud van het bericht wordt verpakt en verzonden naar een specifieke functie van het slimme contract. Deze levering geschiedt namens de gebruiker die is gekoppeld aan het externe systeem.
7.  De functie wordt uitgevoerd en wijzigt doorgaans de status. De wijziging van de status verplaatst de bedrijfs werk stroom in het slimme contract, zodat andere functies nu kunnen worden uitgevoerd, indien van toepassing.

## <a name="iot-integration"></a>IoT-integratie

Een veelvoorkomend integratie scenario is het opnemen van telemetriegegevens die zijn opgehaald uit Sens oren in een slim contract. Op basis van gegevens die door Sens oren worden geleverd, kunnen slimme contracten gefundeerde acties uitvoeren en de status van het contract wijzigen.

Als een vracht wagen die medicijnen levert, bijvoorbeeld is via, kan dit van invloed zijn 110 op de effectiviteit van de medicijnen en kan dit een openbaar veiligheids probleem veroorzaken als dit niet wordt gedetecteerd en verwijderd uit de toeleverings keten. Als een stuur programma zijn auto versnelt naar 100 mijl per uur, zou de resulterende sensor informatie een annulering van de verzekering door hun verzekerings provider kunnen activeren. Als de auto een huur auto was, kunnen GPS-gegevens erop wijzen dat het stuur programma buiten een geografische regio wordt gedekt door hun huur overeenkomst en een boete in rekening wordt gebracht.

De uitdaging is dat deze Sens oren gegevens op een constante basis kunnen leveren en niet geschikt zijn om al deze gegevens naar een slim contract te verzenden. Een typische aanpak is het beperken van het aantal berichten dat naar de Block Chain wordt verzonden terwijl alle berichten aan een secundair archief worden geleverd. Zo kunt u alleen berichten afleveren die zijn ontvangen met een vast interval, bijvoorbeeld één keer per uur, en wanneer een Inge sloten waarde buiten een overeengekomen bereik valt voor een slim contract. Het controleren van waarden die buiten de tolerantie vallen, zorgt ervoor dat de gegevens die relevant zijn voor de bedrijfs logica van de contracten, worden ontvangen en uitgevoerd. Bij het controleren van de waarde bij het interval wordt bevestigd dat de sensor nog steeds rapporteert. Alle gegevens worden verzonden naar een secundair rapportage archief om uitgebreidere rapporten, analyses en machine learning mogelijk te maken. Het ophalen van sensor-aflezingen voor GPS is bijvoorbeeld niet elke minuut vereist voor een slim contract, maar ze kunnen interessante gegevens leveren die worden gebruikt in rapporten of toewijzings routes.

Op het Azure-platform wordt de integratie met apparaten doorgaans uitgevoerd met IoT Hub. IoT Hub voorziet in route ring van berichten op basis van inhoud en schakelt het type functionaliteit in dat eerder is beschreven.

![IoT-berichten](./media/integration-patterns/iot.png)

In het proces wordt een patroon weer gegeven:

-   Een apparaat communiceert rechtstreeks of via een veld Gateway naar IoT Hub.
-   IoT Hub ontvangt de berichten en evalueert de berichten op basis van routes die zijn ingesteld voor het controleren van de inhoud van het bericht. *Rapporteert de sensor een Tempe ratuur groter dan 50 graden?*
-   De IoT Hub stuurt berichten die voldoen aan de criteria aan een gedefinieerd Service Bus voor de route.
-   Een logische app of andere code luistert naar de Service Bus die IoT Hub voor de route heeft ingesteld.
-   Met de logische app of andere code wordt het bericht opgehaald en getransformeerd naar een bekende indeling.
-   Het getransformeerde bericht, nu in een standaard indeling, wordt verzonden naar de Service Bus voor Azure Block Chain Workbench.
-   Azure Block Chain Workbench is geabonneerd op gebeurtenissen van de Service Bus en haalt het bericht op.
-   Azure Block Chain Workbench initieert een aanroep van het groot boek, waarbij gegevens van het externe systeem naar een specifiek contract worden verzonden.
-   Na ontvangst van het bericht evalueert het contract de gegevens en kan de status worden gewijzigd op basis van het resultaat van deze evaluatie, bijvoorbeeld voor een hoge Tempe ratuur, wijzigt u de status in *niet-naleving*.

## <a name="data-integration"></a>Gegevensintegratie

Azure Block Chain Workbench biedt naast REST en op berichten gebaseerde API ook toegang tot een SQL-data base die is gevuld met de meta gegevens van de toepassing en het contract, evenals transactionele gegevens uit gedistribueerde groot boeken.

![Gegevensintegratie](./media/integration-patterns/data-integration.png)

De gegevens integratie is bekend:

-   Azure Block Chain Workbench slaat meta gegevens over toepassingen, werk stromen, contracten en trans acties op als onderdeel van het normale gedrag van het besturings systeem.
-   Externe systemen of hulpprogram ma's bieden een of meer dialoog vensters om het verzamelen van informatie over de data base te vergemakkelijken, zoals de naam van de database server, de database naam, het type verificatie, aanmeldings referenties en de database weergaven die moeten worden gebruikt.
-   Query's worden geschreven op basis van database weergaven om het gebruik door externe systemen, services, rapportage, hulpprogram ma's voor ontwikkel aars en hulpprogram ma's voor bedrijfs productiviteit te vergemakkelijken.

## <a name="storage-integration"></a>Opslag integratie

Voor veel scenario's is het nood zakelijk dat Attestation-bestanden worden opgenomen. Om meerdere redenen is het niet ongepaste bestanden op een Block chain te plaatsen. In plaats daarvan is het een gemeen schappelijke benadering om een cryptografische hash (bijvoorbeeld SHA-256) uit te voeren voor een bestand en die hash te delen in een gedistribueerd groot boek. Als u de hash op een later tijdstip opnieuw uitvoert, zou hetzelfde resultaat moeten worden geretourneerd. Als het bestand wordt gewijzigd, zelfs als er slechts één pixel is gewijzigd in een afbeelding, retourneert de hash een andere waarde.

![Opslag integratie](./media/integration-patterns/storage-integration.png)

Het patroon kan worden geïmplementeerd, waarbij:

-   Een extern systeem persistent een bestand in een opslag mechanisme, zoals Azure Storage.
-   Er wordt een hash gegenereerd met het bestand of het bestand en de bijbehorende meta gegevens, zoals een id voor de eigenaar, de URL waar het bestand zich bevindt, enzovoort.
-   De hash en meta gegevens worden verzonden naar een functie in een slim contract, zoals *FileAdded*
-   In de toekomst kunnen het bestand en de meta gegevens opnieuw worden gehasht en vergeleken met de waarden die zijn opgeslagen in het groot boek.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Vereisten voor het implementeren van integratie patronen met behulp van de REST-en bericht-Api's

Om te zorgen dat een extern systeem of apparaat kan communiceren met het slimme contract met behulp van de REST-of Message-API, moet het volgende optreden:

1. In de Azure Active Directory voor het consortium wordt een account gemaakt dat het externe systeem of apparaat vertegenwoordigt.
2. Een of meer geschikte slimme contracten voor uw Azure Block Chain Workbench-toepassing hebben functies die zijn gedefinieerd om de gebeurtenissen van uw externe systeem of apparaat te accepteren.
3. Het configuratie bestand van de toepassing voor uw slimme contract bevat de rol, waaraan het systeem of apparaat is toegewezen.
4. Het configuratie bestand van de toepassing voor uw slimme contract identificeert in welke status deze functie wordt aangeroepen door de gedefinieerde rol.
5. Het configuratie bestand van de toepassing en de bijbehorende slimme contracten worden geüpload naar Azure Block Chain Workbench.

Zodra de toepassing is geüpload, wordt het Azure Active Directory account voor het externe systeem toegewezen aan het contract en de bijbehorende rol.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Externe systeem integratie stromen testen voorafgaand aan schrijven van integratie code 

Integratie met externe systemen is een belang rijke vereiste voor veel scenario's. Het is wenselijk om het ontwerp van het slimme contract te valideren vóór of parallel aan de ontwikkeling van code om te integreren met externe systemen.

Het gebruik van Azure Active Directory (Azure AD) kan de productiviteit van ontwikkel aars en de tijd aanzienlijk versnellen. Met name de code integratie met een extern systeem kan een niet-trivial tijd duren. Met behulp van Azure AD en de automatische generatie van UX door Azure Block Chain Workbench kunt u ontwikkel aars zich aanmelden bij Block Chain Workbench als het externe systeem en waarden van het externe systeem via de UX vullen. U kunt snel ideeën ontwikkelen en valideren in een test omgeving voordat integratie code wordt geschreven voor de externe systemen.
