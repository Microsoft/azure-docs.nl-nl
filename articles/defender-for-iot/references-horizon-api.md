---
title: Horizon-API
description: In deze hand leiding worden veelgebruikte horizon-methoden beschreven.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/7/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 6d2e3fccd6a61fe129050faa29cb7bb77674ccfe
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97976896"
---
# <a name="horizon-api"></a>Horizon-API 

In deze hand leiding worden veelgebruikte horizon-methoden beschreven.

### <a name="getting-more-information"></a>Meer informatie ophalen

Raadpleeg het volgende voor meer informatie over het werken met horizon en het Cyber-x-platform:

- Neem contact op met uw Cyber-vertegenwoordiger voor de node-SDK (Open Development Environment).
- Neem contact op met voor ondersteuning en informatie over probleem oplossing <support@cyberx-labs.com> .
- Als u toegang wilt krijgen tot de gebruikers handleiding voor Cyberx vanuit Cyberx-console, selecteert u de :::image type="icon" source="media/references-horizon-api/profile-icon.png"::: **Gebruikers handleiding downloaden** en selecteert u deze.

## `horizon::protocol::BaseParser`

Samen vatting voor alle invoeg toepassingen. Dit bestaat uit twee methoden:

- Voor de verwerkings module filters die boven u zijn gedefinieerd. Op deze manier weet u ook hoe u met de parser communiceert
- Voor het verwerken van de werkelijke gegevens.

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

Met de eerste functie die wordt aangeroepen voor de invoeg toepassing, wordt een exemplaar van de parser gemaakt zodat deze wordt herkend en geregistreerd.

### <a name="parameters"></a>Parameters 

Geen

### <a name="return-value"></a>Retourwaarde

shared_ptr naar het parser-exemplaar.

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

Deze functie wordt aangeroepen voor elke hiervoor geregistreerde invoeg toepassing. 

In de meeste gevallen is dit leeg. Verwerk een uitzonde ring voor Horizon om te weten dat er iets fout is opgetreden.

### <a name="parameters"></a>Parameters 

- Een kaart met de structuur van dissect_as, zoals gedefinieerd in de config.jsop een andere invoeg toepassing die u wenst te registreren.

### <a name="return-value"></a>Retourwaarde 

Een matrix van uint64_t die de registratie verwerkt in een soort uint64_t. Dit betekent dat u in de kaart een lijst met poorten hebt waarvan de waarden de uin64_t zijn.

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

De hoofd functie. Met name de logica van de invoeg toepassing, elke keer dat een nieuw pakket uw parser bereikt. Deze functie wordt aangeroepen. alle gerelateerde voor pakket verwerking moet hier worden gedaan.

### <a name="considerations"></a>Overwegingen

De invoeg toepassing moet thread-safe zijn, omdat deze functie kan worden aangeroepen vanuit verschillende threads. Een goede benadering is het definiëren van alles op de stack.

### <a name="parameters"></a>Parameters

- De SDK-controle-eenheid die verantwoordelijk is voor het opslaan van de gegevens en het maken van aan SDK gerelateerde objecten, zoals ILayer, Fields enzovoort.
- Een helper voor het lezen van de gegevens van het onbewerkte pakket. Deze is al ingesteld met de byte volgorde die u hebt gedefinieerd in de config.jsop.

### <a name="return-value"></a>Retourwaarde 

Het resultaat van de verwerking. Dit kan slagen/misvormd/Sanity zijn.

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

Markeert de verwerking als een uitval fout, wat inhoudt dat het pakket niet wordt herkend door het huidige protocol en dat de horizon wordt door gegeven aan een andere parser, indien van toepassing op dezelfde filters.

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

Constructor

### <a name="parameters"></a>Parameters 

- Hiermee definieert u de fout code die wordt gebruikt door de horizon voor logboek registratie, zoals gedefinieerd in de config.jsop.

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

Onjuist gevormd resultaat, wat aangeeft dat het pakket al is herkend als ons protocol, maar er is een ongeldige validatie opgetreden (gereserveerde bits zijn ingeschakeld, een veld ontbreekt, enzovoort)

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

Constructor

### <a name="parameters"></a>Parameters  

- Fout code, zoals gedefinieerd in config.jsop.

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

Hiermee wordt een bericht verzonden over de voltooiing van de verwerking. Wanneer dit is gelukt, is het pakket geaccepteerd; de gegevens horen bij ons en alle gegevens zijn geëxtraheerd.

## `horizon::protocol::SuccessResult()`

Constructor. Er is een basis resultaat gemaakt. Dit betekent dat we de richting of andere meta gegevens met betrekking tot het pakket niet kennen.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

Constructor

### <a name="parameters"></a>Parameters 

- De richting van het pakket, indien geïdentificeerd. Waarden kunnen aanvraag, antwoord

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

Constructor

### <a name="parameters"></a>Parameters

- De richting van het pakket, als dit is geïdentificeerd, kan aanvraag, antwoord zijn
- Berichten. Deze gebeurtenissen zijn niet mogelijk, maar er wordt wel een melding ontvangen.

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

Constructor

### <a name="parameters"></a>Parameters 

-  Berichten. Deze gebeurtenissen zijn niet mogelijk, maar er wordt wel een melding ontvangen.

## `HorizonID HORIZON_FIELD(const std::string_view &)`

Converteert een op teken reeks gebaseerde verwijzing naar een veld naam (bijvoorbeeld function_code) naar HorizonID

### <a name="parameters"></a>Parameters 

- Teken reeks die moet worden geconverteerd

### <a name="return-value"></a>Retourwaarde

- HorizonID is gemaakt op basis van de teken reeks.

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

Hiermee maakt u een nieuwe laag. zo niet, weet u dat de invoeg toepassing sommige gegevens wil opslaan. Dit is de basis eenheid voor opslag die u moet gebruiken.

### <a name="return-value"></a>Retourwaarde

Een verwijzing naar een gemaakte laag, zodat u er gegevens aan kunt toevoegen.

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

Hiermee wordt het object voor veld beheer opgehaald, dat verantwoordelijk is voor het maken van velden in verschillende objecten, bijvoorbeeld op ILayer

### <a name="return-value"></a>Retourwaarde

Een verwijzing naar de Manager

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

Hiermee maakt u een nieuw numeriek veld van 64 bits op de laag met de aangevraagde ID.

### <a name="parameters"></a>Parameters 

- De laag die u eerder hebt gemaakt
- HorizonID gemaakt door de HORIZON_FIELD-macro
- De onbewerkte waarde die u wilt opslaan

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

Hiermee maakt u een nieuw teken reeks veld van op de laag met de aangevraagde ID. Het geheugen wordt verplaatst, dus wees voorzichtig. U kunt deze waarde niet opnieuw gebruiken.

### <a name="parameters"></a>Parameters  

- De laag die u eerder hebt gemaakt
- HorizonID gemaakt door de HORIZON_FIELD-macro
- De onbewerkte waarde die u wilt opslaan

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

Hiermee maakt u een nieuw veld voor onbewerkte waarde (byte matrix) van op de laag, met de aangevraagde ID. Het geheugen wordt verplaatst, dus u kunt deze waarde niet opnieuw gebruiken

### <a name="parameters"></a>Parameters

- De laag die u eerder hebt gemaakt
- HorizonID gemaakt door de HORIZON_FIELD-macro
- De onbewerkte waarde die u wilt opslaan

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

Hiermee maakt u een matrix waarde-veld (matrix) op de laag van het opgegeven type met de aangevraagde ID.

### <a name="parameters"></a>Parameters

- De laag die u eerder hebt gemaakt
- HorizonID gemaakt door de HORIZON_FIELD-macro
- Het type waarden dat in de matrix wordt opgeslagen

### <a name="return-value"></a>Retourwaarde

Verwijzing naar een matrix waaraan u waarden moet toevoegen

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

Hiermee wordt een nieuwe integerwaarde toegevoegd aan de matrix die u eerder hebt gemaakt

### <a name="parameters"></a>Parameters

- De matrix die u eerder hebt gemaakt
- De onbewerkte waarde die in de matrix moet worden opgeslagen

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

Voegt een nieuwe teken reeks waarde toe aan de matrix die u eerder hebt gemaakt. Het geheugen wordt verplaatst, dus u kunt deze waarde niet opnieuw gebruiken

### <a name="parameters"></a>Parameters

- De matrix die u eerder hebt gemaakt
- De onbewerkte waarde die in de matrix moet worden opgeslagen

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

Hiermee wordt een nieuwe onbewerkte waarde toegevoegd aan de matrix die u eerder hebt gemaakt. Het geheugen wordt verplaatst, dus u kunt deze waarde niet opnieuw gebruiken

### <a name="parameters"></a>Parameters

- De matrix die u eerder hebt gemaakt
- De onbewerkte waarde die in de matrix moet worden opgeslagen

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

Controleert of de buffer ten minste X bytes bevat.

### <a name="parameters"></a>Parameters

Aantal bytes moet bestaan 

### <a name="return-value"></a>Retourwaarde

Waar als de buffer ten minste X bytes bevat. Anders false.

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

Leest de uint8-waarde (1 bytes), van de buffer, op basis van de byte volgorde.

### <a name="return-value"></a>Retourwaarde

De waarde die uit de buffer is gelezen.

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

Leest uint16 waarde (2 bytes), van de buffer, op basis van de byte volgorde.

### <a name="return-value"></a>Retourwaarde

De waarde die uit de buffer is gelezen.

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

Hiermee wordt de uint32-waarde (4 bytes) van de buffer op basis van de byte volgorde gelezen.

### <a name="return-value"></a>Retourwaarde

De waarde die uit de buffer is gelezen.

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

Leest de uint64-waarde (8 bytes) van de buffer, op basis van de byte volgorde.

### <a name="return-value"></a>Retourwaarde

De waarde die uit de buffer is gelezen.

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

De gegevens worden in de regio van het geheugen gekopieerd naar het vooraf toegewezen geheugen, van een opgegeven grootte.

### <a name="parameters"></a>Parameters 

- De geheugen regio waarnaar de gegevens moeten worden gekopieerd
- Grootte van de geheugen regio, deze para meter is ook gedefinieerd hoeveel bytes worden gekopieerd

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

Leest naar een teken reeks uit de buffer

### <a name="parameters"></a>Parameters 

- Het aantal bytes dat moet worden gelezen.

### <a name="return-value"></a>Retourwaarde

De verwijzing naar het geheugen gebied van de teken reeks.

## `size_t horizon::general::IDataBuffer::getRemainingData()`

Geeft aan hoeveel bytes er in de buffer resteren.

### <a name="return-value"></a>Retourwaarde

Resterende grootte van de buffer.

## `void horizon::general::IDataBuffer::skip(size_t)`

Hiermee worden X bytes in de buffer overs Laan.

### <a name="parameters"></a>Parameters

- Het aantal bytes dat moet worden overgeslagen.
