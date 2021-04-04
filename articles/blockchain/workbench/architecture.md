---
title: Architectuur van Azure Blockchain Workbench
description: Overzicht van de architectuur van Azure Block Chain Workbench preview en de bijbehorende onderdelen.
ms.date: 09/05/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 1fff70ef2eeb1dc27d33769fd48fe5923f56717b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021602"
---
# <a name="azure-blockchain-workbench-architecture"></a>Architectuur van Azure Blockchain Workbench

Azure Block Chain Workbench preview vereenvoudigt de ontwikkeling van Block Chain toepassingen door een oplossing te bieden met behulp van verschillende Azure-onderdelen. Block Chain Workbench kan worden geïmplementeerd met behulp van een oplossings sjabloon in azure Marketplace. Met de sjabloon kunt u modules en onderdelen kiezen om te implementeren, waaronder Block Chain stack, type client toepassing en ondersteuning voor IoT-integratie. Na de implementatie biedt Block Chain Workbench toegang tot een web-app, iOS-app en Android-app.

![Architectuur van Block Chain workbench](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identiteit en verificatie

Met behulp van Block Chain Workbench kan een consortium hun ondernemings identiteiten met behulp van Azure Active Directory (Azure AD). Workbench genereert nieuwe gebruikers accounts voor identiteiten op de keten met de ondernemings identiteiten die zijn opgeslagen in azure AD. De identiteits toewijzing vereenvoudigt geverifieerde aanmelding bij client-Api's en toepassingen en maakt gebruik van het verificatie beleid van organisaties. Workbench biedt ook de mogelijkheid om ondernemings identiteiten aan specifieke rollen binnen een bepaald slim contract te koppelen. Daarnaast biedt Workbench ook een mechanisme voor het identificeren van de acties die rollen kunnen ondernemen en op welk moment.

Nadat Block Chain Workbench is geïmplementeerd, communiceren gebruikers met block Chain Workbench via de client toepassingen, de REST-based client-API of de Messa ging-API. In alle gevallen moeten interacties worden geverifieerd via Azure Active Directory (Azure AD) of apparaatspecifieke referenties.

Gebruikers behandelen hun identiteiten aan een consortium Azure AD door een e-mail bericht te verzenden naar deel nemers op hun e-mail adres. Wanneer u zich aanmeldt, worden deze gebruikers geverifieerd aan de hand van de naam, het wacht woord en het beleid. Bijvoorbeeld twee ledige verificatie van de organisatie.

Azure AD wordt gebruikt voor het beheren van alle gebruikers die toegang hebben tot Block Chain Workbench. Elk apparaat dat verbinding maakt met een slim contract is ook gekoppeld aan Azure AD.

Azure AD wordt ook gebruikt om gebruikers toe te wijzen aan een speciale beheerders groep. Gebruikers die aan de groep Administrators zijn gekoppeld, krijgen toegang tot rechten en acties binnen Block Chain Workbench, waaronder het implementeren van contracten en het verlenen van machtigingen aan een gebruiker om toegang te krijgen tot een contract. Gebruikers buiten deze groep hebben geen toegang tot beheerders acties.

## <a name="client-applications"></a>Clienttoepassingen

Workbench biedt automatisch gegenereerde client toepassingen voor web en mobiel (iOS, Android), die kunnen worden gebruikt voor het valideren, testen en weer geven van Block Chain-toepassingen. De toepassings interface wordt dynamisch gegenereerd op basis van slimme contract metagegevens en kan alle gebruiks cases bevatten. De client toepassingen leveren een aan de gebruiker gerichte front-end voor de volledige Block Chain-toepassingen die door Block Chain Workbench worden gegenereerd. Client toepassingen verifiëren gebruikers via Azure Active Directory (Azure AD) en presen teren een gebruikers ervaring die is afgestemd op de zakelijke context van het slimme contract. De gebruikers ervaring maakt het mogelijk om nieuwe slimme contract instanties te maken op basis van geautoriseerde personen en geeft vervolgens de mogelijkheid om bepaalde typen trans acties uit te voeren op de juiste punten in het slimme contract.

In de webtoepassing hebben gemachtigde gebruikers toegang tot de Administrator-console. De-console is beschikbaar voor gebruikers in de groep Administrators in azure AD en biedt toegang tot de volgende functionaliteit:

* Implementeer micro soft meegeleverde slimme contracten voor populaire scenario's. Bijvoorbeeld een scenario voor de overdracht van activa.
* Upload en implementeer hun eigen slimme contracten.
* Een gebruiker toegang tot het slimme contract toewijzen in de context van een specifieke rol.

Zie de voor [beelden van Azure Block Chain Workbench-client toepassingen op github](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile)voor meer informatie.

## <a name="gateway-service-api"></a>Gateway Service-API

Block Chain Workbench bevat een API voor op REST gebaseerde Gateway Service. Wanneer er naar een Block Chain wordt geschreven, genereert en levert de API berichten aan een event Broker. Wanneer gegevens worden aangevraagd door de API, worden query's verzonden naar de data base uit de buiten keten. De Data Base bevat een replica van gegevens en meta gegevens op de keten met informatie over de context en configuratie voor ondersteunde slimme contracten. Query's retour neren de vereiste gegevens van de off-keten replica in een indeling die wordt geïnformeerd over de meta gegevens voor het contract.

Ontwikkel aars hebben toegang tot de Gateway Service-API om Block chain-oplossingen te bouwen of te integreren zonder dat ze gebruikmaken van Block Chain Workbench-client-apps.

> [!NOTE]
> Om geverifieerde toegang tot de API in te scha kelen, worden twee client toepassingen geregistreerd in Azure Active Directory. Azure Active Directory vereist afzonderlijke toepassings registraties elk toepassings type (systeem eigen en web).

## <a name="message-broker-for-incoming-messages"></a>Bericht Broker voor inkomende berichten

Ontwikkel aars die berichten rechtstreeks naar Block Chain Workbench willen verzenden, kunnen berichten rechtstreeks naar Service Bus verzenden. Berichten-API kan bijvoorbeeld worden gebruikt voor systeem-naar-systeem-integratie of IoT-apparaten.

## <a name="message-broker-for-downstream-consumers"></a>Message Broker voor downstream-consumenten

Tijdens de levens cyclus van de toepassing vinden gebeurtenissen plaats. Gebeurtenissen kunnen worden geactiveerd door de Gateway-API of het groot boek. Gebeurtenis meldingen kunnen downstream-code initiëren op basis van de gebeurtenis.

Block Chain Workbench implementeert automatisch twee typen gebeurtenis verbruikers. Er wordt één consumer geactiveerd door Block Chain-gebeurtenissen om de buiten-keten SQL-opslag te vullen. De andere gebruiker is het vastleggen van meta gegevens voor gebeurtenissen die worden gegenereerd door de API met betrekking tot het uploaden en opslaan van documenten.

## <a name="message-consumers"></a>Bericht gebruikers

 Bericht consumers nemen berichten van Service Bus. Het onderliggende gebeurtenis model voor bericht consumenten biedt uitbrei dingen van extra services en systemen. U kunt bijvoorbeeld ondersteuning toevoegen om CosmosDB te vullen of berichten te evalueren met behulp van Azure streaming Analytics. In de volgende secties worden de bericht gebruikers beschreven die zijn opgenomen in Block Chain Workbench.

### <a name="distributed-ledger-consumer"></a>Gedistribueerde grootboek verbruiker

Distributed Ledger-berichten (DLT) bevatten de meta gegevens voor trans acties die naar de Block Chain moeten worden geschreven. De Consumer haalt de berichten op en duwt de gegevens naar een trans actie Builder, ondertekenaar en router.

### <a name="database-consumer"></a>Database gebruiker

De database gebruiker neemt berichten van Service Bus en duwt de gegevens naar een bijgevoegde data base, zoals een data base in Azure SQL Database.

### <a name="storage-consumer"></a>Opslag verbruiker

De opslag verbruiker neemt berichten van Service Bus en pusht gegevens naar een gekoppelde opslag. U kunt bijvoorbeeld gehashte documenten opslaan in Azure Storage.

## <a name="transaction-builder-and-signer"></a>Trans actie Builder en ondertekenaar

Als een bericht in de binnenkomende Message Broker moet worden geschreven naar de Block Chain, wordt dit verwerkt door de DLT-Consumer. De DLT-Consumer is een service waarmee het bericht wordt opgehaald dat meta gegevens bevat voor een gewenste trans actie om uit te voeren en vervolgens de gegevens naar de *opbouw functie en ondertekenaar van de trans actie* te verzenden. De *trans actie-opbouw functie en ondertekenaar* assembleert een Block Chain-trans actie op basis van de gegevens en de gewenste Block Chain-bestemming. Na de assembly is de trans actie ondertekend. Persoonlijke sleutels worden opgeslagen in Azure Key Vault.

 Block Chain Workbench haalt de juiste persoonlijke sleutel op uit Key Vault en ondertekent de trans actie buiten Key Vault. Eenmaal ondertekend, wordt de trans actie verzonden naar transactie routers en groot boeken.

## <a name="transaction-routers-and-ledgers"></a>Transactie routers en groot boeken

Trans actie routers en groot boeken nemen ondertekende trans acties en sturen ze naar de juiste Block chain. Op dit moment ondersteunt Block Chain Workbench Ethereum als doel Block chain.

## <a name="dlt-watcher"></a>DLT-Watcher

Een Distributed Ledger-Watcher bewaakt gebeurtenissen die plaatsvinden op blok ketens die zijn gekoppeld aan block Chain Workbench.
Gebeurtenissen geven informatie weer die relevant is voor personen en systemen. Bijvoorbeeld het maken van nieuwe contract instanties, het uitvoeren van trans acties en de status wijzigingen. De gebeurtenissen worden vastgelegd en verzonden naar de uitgaande Message Broker, zodat deze kunnen worden gebruikt door downstream-gebruikers.

De SQL-gebruiker bewaakt bijvoorbeeld gebeurtenissen, verbruikt deze en vult de data base met de opgenomen waarden. Met de kopie wordt het maken van een replica van on-keten gegevens in een buiten keten archief mogelijk.

## <a name="azure-sql-database"></a>Azure SQL Database

De data base die is gekoppeld aan block Chain Workbench slaat contract definities, meta gegevens over de configuratie en een SQL-toegankelijke replica op van gegevens die zijn opgeslagen in de Block chain. Deze gegevens kunnen eenvoudig worden opgevraagd, gevisualiseerd of geanalyseerd door rechtstreeks toegang te krijgen tot de data base. Ontwikkel aars en andere gebruikers kunnen de-Data Base gebruiken voor rapportages, analyses of andere gegevens gerichte integraties. Gebruikers kunnen bijvoorbeeld transactie gegevens visualiseren met behulp van Power BI.

Deze buiten keten opslag biedt bedrijven de mogelijkheid om gegevens in SQL te doorzoeken in plaats van in een Block Chain-groot boek. Daarnaast kunt u met behulp van een standaard schema dat neutraal van Block Chain-technologie stacks wordt genormaliseerd, de niet-keten opslag gebruiken om rapporten en andere artefacten te hergebruiken in projecten, scenario's en organisaties.

## <a name="azure-storage"></a>Azure Storage

Azure Storage wordt gebruikt om contracten en meta gegevens op te slaan die aan contracten zijn gekoppeld.

Van inkoop orders en vracht brieven, op afbeeldingen die worden gebruikt in de nieuws-en medische afbeelding, naar Video's die afkomstig zijn van een continuüm, met inbegrip van de politie-en hoogwaardige Foto's, spelen documenten een rol in veel Block Chain-scenario's. Documenten zijn niet geschikt om rechtstreeks op de Block chain te plaatsen.

Block Chain Workbench ondersteunt de mogelijkheid om documenten of andere media-inhoud toe te voegen met block Chain-bedrijfs logica. Een hash van de inhoud van het document of medium wordt opgeslagen in de Block chain en het actuele document of media-inhoud wordt opgeslagen in Azure Storage. De bijbehorende transactie gegevens worden geleverd aan de binnenkomende bericht Broker, verpakt, ondertekend en doorgestuurd naar de Block chain. Met dit proces worden gebeurtenissen geactiveerd die worden gedeeld via de uitgaande Message Broker. De SQL-data base gebruikt deze informatie en verzendt deze naar de Data Base voor latere query's. Downstream-systemen kunnen deze gebeurtenissen ook gebruiken om te fungeren als dat nodig is.

## <a name="monitoring"></a>Bewaking

Workbench biedt toepassings logboeken met behulp van Application Insights en Azure Monitor. Application Insights wordt gebruikt voor het opslaan van alle geregistreerde gegevens van Block Chain Workbench en bevat fouten, waarschuwingen en geslaagde bewerkingen. Application Insights kunnen door ontwikkel aars worden gebruikt om problemen met block Chain Workbench op te lossen. 

Azure Monitor biedt informatie over de status van het block chain-netwerk. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench implementeren](./deploy.md)
