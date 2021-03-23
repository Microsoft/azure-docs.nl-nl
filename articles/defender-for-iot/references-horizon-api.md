---
title: Horizon API
description: In deze hand leiding worden veelgebruikte horizon-methoden beschreven.
ms.date: 1/5/2021
ms.topic: article
ms.openlocfilehash: b65f7663df29e2c82faa5d1aeec3b820d5fbaf70
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786784"
---
# <a name="horizon-api"></a>Horizon API 

In deze hand leiding worden veelgebruikte horizon-methoden beschreven.

### <a name="getting-more-information"></a>Meer informatie ophalen

Raadpleeg de volgende informatie voor meer informatie over het werken met horizon en het Defender voor IoT-platform:

- Neem voor de node-SDK (Open Development Environment) contact op met uw Defender voor IoT-vertegenwoordiger.
- Neem contact op met voor ondersteuning en informatie over probleem oplossing <support@cyberx-labs.com> .

- Als u toegang wilt krijgen tot de gebruikers handleiding voor Defender voor IoT in de Defender voor IoT-console, selecteert u de :::image type="icon" source="media/references-horizon-api/profile.png"::: **Gebruikers handleiding downloaden** en selecteert u deze.


## `horizon::protocol::BaseParser`

Samen vatting voor alle invoeg toepassingen. Dit bestaat uit twee methoden:

- Voor de verwerkings module filters die boven u zijn gedefinieerd. Op deze manier weet u ook hoe u met de parser kunt communiceren.
- Voor het verwerken van de werkelijke gegevens.

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

Met de eerste functie die wordt aangeroepen voor de invoeg toepassing, wordt een exemplaar van de parser gemaakt zodat deze wordt herkend en geregistreerd.

### <a name="parameters"></a>Parameters 

Geen.

### <a name="return-value"></a>Retourwaarde

shared_ptr naar het parser-exemplaar.

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

Deze functie wordt aangeroepen voor elke hiervoor geregistreerde invoeg toepassing. 

In de meeste gevallen is dit leeg. Verwerk een uitzonde ring voor Horizon om te weten dat er iets fout is opgetreden.

### <a name="parameters"></a>Parameters 

- Een kaart met de structuur van dissect_as, zoals gedefinieerd in de config.jsop een andere invoeg toepassing die u wenst te registreren.

### <a name="return-value"></a>Retourwaarde 

Een matrix van uint64_t, de registratie die wordt verwerkt in een soort uint64_t. Dit betekent dat u in de kaart een lijst met poorten hebt waarvan de waarden de uin64_t zijn.

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

De hoofd functie. Met name de logica van de invoeg toepassing, elke keer dat een nieuw pakket uw parser bereikt. Deze functie wordt aangeroepen. alle gerelateerde voor pakket verwerking moet hier worden gedaan.

### <a name="considerations"></a>Overwegingen

De invoeg toepassing moet thread-safe zijn, omdat deze functie kan worden aangeroepen vanuit verschillende threads. Een goede benadering is het definiëren van alles op de stack.

### <a name="parameters"></a>Parameters

- De SDK-controle-eenheid die verantwoordelijk is voor het opslaan van de gegevens en het maken van aan SDK gerelateerde objecten, zoals ILayer en velden.
- Een helper voor het lezen van de gegevens van het onbewerkte pakket. Deze is al ingesteld met de byte volgorde die u hebt gedefinieerd in de config.jsop.

### <a name="return-value"></a>Retourwaarde 

Het resultaat van de verwerking. Dit kan *slagen*, *misvormd* of *Sanity* zijn.

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

Markeert de verwerking als een uitval fout, wat inhoudt dat het pakket niet wordt herkend door het huidige protocol en dat de horizon wordt door gegeven aan een andere parser, indien van toepassing op dezelfde filters.

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

Constructor

### <a name="parameters"></a>Parameters 

- Hiermee definieert u de fout code die wordt gebruikt door de horizon voor logboek registratie, zoals gedefinieerd in de config.jsop.

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

Onjuist gevormd resultaat, wat aangeeft dat het pakket al is herkend als ons protocol, maar er is een ongeldige validatie opgetreden (gereserveerde bits zijn ingeschakeld of een veld ontbreekt).

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

Constructor

### <a name="parameters"></a>Parameters  

- Fout code, zoals gedefinieerd in config.jsop.

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

Hiermee wordt een bericht verzonden over de voltooiing van de verwerking. Wanneer dit is gelukt, is het pakket geaccepteerd, de gegevens horen bij ons en alle gegevens zijn geëxtraheerd.

## `horizon::protocol::SuccessResult()`

Constructor. Er is een basis resultaat gemaakt. Dit betekent dat we de richting of andere meta gegevens met betrekking tot het pakket niet kennen.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

Constructor.

### <a name="parameters"></a>Parameters 

- De richting van het pakket, indien geïdentificeerd. Waarden kunnen een *aanvraag* of *antwoord* zijn.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

Constructor.

### <a name="parameters"></a>Parameters

- De richting van het pakket, als dit is geïdentificeerd, kan *Request* en *Response* zijn.
- Berichten. Deze gebeurtenissen zijn niet mogelijk, maar er wordt wel een melding ontvangen.

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

Constructor.

### <a name="parameters"></a>Parameters 

-  Berichten. Deze gebeurtenissen zijn niet mogelijk, maar er wordt wel een melding ontvangen.

## `HorizonID HORIZON_FIELD(const std::string_view &)`

Converteert een op teken reeks gebaseerde verwijzing naar een veld naam (bijvoorbeeld function_code) naar HorizonID.

### <a name="parameters"></a>Parameters 

- De teken reeks die moet worden geconverteerd.

### <a name="return-value"></a>Retourwaarde

- HorizonID is gemaakt op basis van de teken reeks.

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

Hiermee maakt u een nieuwe laag. zo niet, weet u dat de invoeg toepassing sommige gegevens wil opslaan. Dit is de basis eenheid voor opslag die u moet gebruiken.

### <a name="return-value"></a>Retourwaarde

Een verwijzing naar een gemaakte laag, zodat u er gegevens aan kunt toevoegen.

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

Hiermee wordt het object voor veld beheer opgehaald, dat verantwoordelijk is voor het maken van velden in verschillende objecten, bijvoorbeeld op ILayer.

### <a name="return-value"></a>Retourwaarde

Een verwijzing naar de Manager.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

Hiermee maakt u een nieuw numeriek veld van 64 bits op de laag met de aangevraagde ID.

### <a name="parameters"></a>Parameters 

- De laag die u eerder hebt gemaakt.
- HorizonID gemaakt door de **HORIZON_FIELD** macro.
- De onbewerkte waarde die u wilt opslaan.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

Hiermee maakt u een nieuw teken reeks veld van op de laag met de aangevraagde ID. Het geheugen wordt verplaatst, dus wees voorzichtig. U kunt deze waarde niet opnieuw gebruiken.

### <a name="parameters"></a>Parameters  

- De laag die u eerder hebt gemaakt.
- HorizonID gemaakt door de **HORIZON_FIELD** macro.
- De onbewerkte waarde die u wilt opslaan.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

Hiermee maakt u een nieuw veld voor onbewerkte waarde (byte matrix) van op de laag, met de aangevraagde ID. Het geheugen wordt verplaatst, dus u kunt deze waarde niet opnieuw gebruiken.

### <a name="parameters"></a>Parameters

- De laag die u eerder hebt gemaakt.
- HorizonID gemaakt door de **HORIZON_FIELD** macro.
- De onbewerkte waarde die u wilt opslaan.

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

Hiermee maakt u een matrix waarde-veld (matrix) op de laag van het opgegeven type met de aangevraagde ID.

### <a name="parameters"></a>Parameters

- De laag die u eerder hebt gemaakt.
- HorizonID gemaakt door de **HORIZON_FIELD** macro.
- Het type waarden dat in de matrix wordt opgeslagen.

### <a name="return-value"></a>Retourwaarde

Verwijzing naar een matrix waaraan u waarden moet toevoegen.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

Voegt een nieuwe integerwaarde toe aan de matrix die u eerder hebt gemaakt.

### <a name="parameters"></a>Parameters

- De matrix die u eerder hebt gemaakt.
- De onbewerkte waarde die in de matrix moet worden opgeslagen.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

Voegt een nieuwe teken reeks waarde toe aan de matrix die u eerder hebt gemaakt. Het geheugen wordt verplaatst, dus u kunt deze waarde niet opnieuw gebruiken.

### <a name="parameters"></a>Parameters

- De matrix die u eerder hebt gemaakt.
- De onbewerkte waarde die in de matrix moet worden opgeslagen.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

Hiermee wordt een nieuwe onbewerkte waarde toegevoegd aan de matrix die u eerder hebt gemaakt. Het geheugen wordt verplaatst, dus u kunt deze waarde niet opnieuw gebruiken.

### <a name="parameters"></a>Parameters

- De matrix die u eerder hebt gemaakt.
- De onbewerkte waarde die in de matrix moet worden opgeslagen.

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

Controleert of de buffer ten minste X bytes bevat.

### <a name="parameters"></a>Parameters

Het aantal bytes dat moet bestaan.

### <a name="return-value"></a>Retourwaarde

Waar als de buffer ten minste X bytes bevat. Anders is dit `False` .

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

Hiermee wordt de uint8-waarde (1 byte) gelezen uit de buffer, op basis van de byte volgorde.

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

- De geheugen regio waarnaar de gegevens moeten worden gekopieerd.
- Grootte van de geheugen regio, deze para meter is ook gedefinieerd hoeveel bytes er worden gekopieerd.

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

Hiermee wordt een teken reeks uit de buffer gelezen.

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
