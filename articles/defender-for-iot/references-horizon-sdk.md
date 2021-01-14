---
title: Horizon-SDK
titleSuffix: Azure Defender for IoT
description: Met de horizon-SDK kunnen met Azure Defender voor IoT-ontwikkel aars de modules voor het netwerk verkeer worden gedecodeerd, zodat deze kunnen worden verwerkt door automatische Defender voor IoT-netwerk analyse Programma's.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/13/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: d6105f65508eff59164246020d9a3f286b68c5a1
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210566"
---
# <a name="horizon-proprietary-protocol-dissector"></a>Horizon-eigendom protocol Dissector

Horizon is een Open Development Environment (node) die wordt gebruikt voor het beveiligen van IoT-en ICS-apparaten waarop eigen protocollen worden uitgevoerd.

Deze omgeving biedt de volgende oplossingen voor klanten en technologie partners:

- Onbeperkte, volledige ondersteuning voor algemene, bedrijfsspecifieke, aangepaste protocollen of protocollen die afwijken van de standaard. 

- Een nieuw niveau van flexibiliteit en bereik voor de ontwikkeling van DPI.

- Een hulp programma waarmee u de zicht baarheid en controle exponentieel uitbreidt, zonder dat u Defender voor IoT-platform versies hoeft bij te werken.

- De beveiliging van het toestaan van bedrijfs eigen ontwikkeling zonder divulging gevoelige informatie.

Met de horizon-SDK kunnen met Azure Defender voor IoT-ontwikkel aars de modules voor het netwerk verkeer worden gedecodeerd, zodat deze kunnen worden verwerkt door automatische Defender voor IoT-netwerk analyse Programma's.

Protocol-DIS-sectoren worden ontwikkeld als externe invoeg toepassingen en zijn geïntegreerd met een uitgebreid scala aan Defender voor IoT-Services. Bijvoorbeeld services die mogelijkheden voor bewaking, waarschuwingen en rapportage bieden.

## <a name="secure-development-environment"></a>Beveiligde ontwikkel omgeving 

De horizon node maakt het mogelijk aangepaste of eigen protocollen te ontwikkelen die buiten een organisatie niet kunnen worden gedeeld. Bijvoorbeeld vanwege wettelijke voor Schriften of bedrijfs beleid.

Ontwikkel modules voor dissectors zonder: 

- informatie over de manier waarop uw protocollen worden gedefinieerd, zichtbaar maken.

- delen van uw gevoelige PCAPs.

- nalevings regels schenden.

## <a name="customization-and-localization"></a>Aanpassing en lokalisatie  

De SDK ondersteunt verschillende aanpassings opties, waaronder:

  - Tekst voor functie codes. 

  - Volledige lokalisatie tekst voor waarschuwingen, gebeurtenissen en protocol parameters. Zie [toewijzings bestanden maken (JSON)](#create-mapping-files-json)voor meer informatie.

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="Volledig gelokaliseerde waarschuwingen weer geven.":::

## <a name="horizon-architecture"></a>Horizon-architectuur

Het architectuur model bevat drie product lagen.

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>Defender voor IoT-platform-laag

Maakt onmiddellijke integratie en real-time bewaking van aangepaste modules voor het maken van de invoeg toepassingen mogelijk in het Defender voor IoT-platform, zonder dat de versie van de Defender voor IoT-platform hoeft te worden bijgewerkt.

## <a name="defender-for-iot-services-layer"></a>Defender voor IoT-Services-laag

Elke service is ontworpen als een pijp lijn, losgekoppeld van een specifiek protocol, waardoor efficiëntere, onafhankelijke ontwikkeling mogelijk is.

Elke service is ontworpen als een pijp lijn, losgekoppeld van een specifiek protocol. Services luistert naar verkeer op de pijp lijn. Ze communiceren met de gegevens van de invoeg toepassing en het verkeer dat door de Sens oren is vastgelegd om geïmplementeerde protocollen te indexeren en de payload van het verkeer te analyseren en een efficiëntere en onafhankelijke ontwikkeling mogelijk te maken.

## <a name="custom-dissector-layer"></a>Aangepaste laag voor lagen 

Maakt het mogelijk om invoeg toepassingen te maken met de Defender voor IoT-bedrijfs eigen SDK (inclusief C++-implementatie en JSON-configuratie) naar: 

- Definiëren hoe het protocol moet worden geïdentificeerd

- Definieer hoe u de velden die u wilt extra heren wilt toewijzen uit het verkeer en deze wilt extra heren 

- Definiëren hoe u kunt integreren met de Defender voor IoT-Services

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="De ingebouwde lagen.":::

Defender voor IoT biedt eenvoudige sectoren voor algemene protocollen. U kunt uw sectoren boven op deze protocollen bouwen.

## <a name="before-you-begin"></a>Voordat u begint

## <a name="what-this-sdk-contains"></a>Wat deze SDK bevat 

Deze kit bevat de header bestanden die nodig zijn voor de ontwikkeling. Voor het ontwikkelings proces zijn basis stappen en optionele geavanceerde stappen vereist, zoals beschreven in deze SDK.

Neem contact <support@cyberx-labs.com> op met informatie over het ontvangen van header bestanden en andere bronnen.

## <a name="about-the-environment-and-setup"></a>Over de omgeving en de installatie 

### <a name="requirements"></a>Vereisten 

- De aanbevolen ontwikkel omgeving is Linux. Als u in een Windows-omgeving ontwikkelt, kunt u een virtuele machine met een Linux-systeem gebruiken.

- Voor het compilatie proces gebruikt u GCC 7.4.0 of hoger. Gebruik een standaard klasse uit stdlib die wordt ondersteund onder C++ 17.

- Defender voor IoT versie 3,0 en hoger.

### <a name="process"></a>Proces

1. [Down load](https://www.eclipse.org/) de eclips IDE voor C/C++-ontwikkel aars. U kunt elk ander IDE-gewenst gebruik gebruiken. Dit document helpt u bij het configureren van de configuratie met behulp van eclips IDE.

1. Na het starten van eclips IDE en het configureren van de werk ruimte (waar uw projecten worden opgeslagen), drukt u op **CTRL + n** en maakt u deze als een C++-project.

1. Stel op het volgende scherm de naam in voor het protocol dat u wilt ontwikkelen en selecteer het project type als `Shared Library` en `AND Linux GCC` .

1. Bewerk de project eigenschappen, onder **C/C++ build**  >  **Settings**  >  **tool settings instellingen**  >  **gcc C++ compiler**  >  **Varia**  >  **code onafhankelijk** van de positie.

1. Plak de voorbeeld codes die u hebt ontvangen met de SDK en compileer deze.

1. Voeg de artefacten (bibliotheek, config.jsop en meta gegevens) toe aan een tar. gz-bestand en wijzig de bestands extensie in \<XXX> . HDP, waarbij \<XXX> de naam is van de invoeg toepassing.

### <a name="research"></a>Nader 

Voordat u begint, controleert u of u:

- Lees de protocol specificatie, indien beschikbaar.

- Weet welke protocol velden u wilt uitpakken.

- Uw toewijzings doelstellingen hebben gepland.

## <a name="about-plugin-files"></a>Over invoeg toepassings bestanden 

Tijdens het ontwikkelings proces worden drie bestanden gedefinieerd.

### <a name="json-configuration-file-required"></a>JSON-configuratie bestand (vereist) 

Dit bestand moet de ID en declaraties, afhankelijkheden, integratie vereisten, validatie parameters en toewijzings definities definiëren voor het omzetten van waarden naar namen, cijfers naar tekst. Zie de volgende koppelingen voor meer informatie:

- [Het configuratie bestand voorbereiden (JSON)](#prepare-the-configuration-file-json)

- [Validatie van de implementatie code voorbereiden](#prepare-implementation-code-validations)

- [Meta gegevens van apparaten extra heren](#extract-device-metadata)

- [Verbinding maken met een Indexing-service (basis lijn)](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>Implementatie code: C++ (vereist)

De implementatie code (CPP) parseert onbewerkt verkeer en wijst het toe aan waarden zoals Services, klassen en functie codes. Het extraheert de laag velden en wijst deze toe aan de index namen van de JSON-configuratie bestanden. De velden die moeten worden geëxtraheerd uit CPP, worden gedefinieerd in het configuratie bestand. Zie [de implementatie code voorbereiden (C++)](#prepare-the-implementation-code-c)voor meer informatie.

### <a name="mapping-files-optional"></a>Toewijzings bestanden (optioneel)

U kunt de uitvoer tekst van de invoeg toepassing aanpassen zodat deze voldoet aan de behoeften van uw bedrijfs omgeving.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="virtuelemachinemigratie":::

U kunt toewijzings bestanden definiëren en bijwerken om tekst bij te werken zonder de code te wijzigen. Elk bestand kan een of meer velden toewijzen:

  - Toewijzing van veld waarden aan namen, bijvoorbeeld 1: opnieuw instellen, 2: begin, 3: stoppen.

  - Toewijzing van tekst ter ondersteuning van meerdere talen.

Zie [toewijzings bestanden maken (JSON)](#create-mapping-files-json)voor meer informatie.

## <a name="create-a-dissector-plugin-overview"></a>Een module voor een onsector maken (overzicht)

1. Raadpleeg de sectie [over de omgeving en het installatie programma](#about-the-environment-and-setup) .

2.  [Bereid de implementatie code (C++)](#prepare-the-implementation-code-c)voor. Kopieer de **template.jsin** het bestand en bewerk dit om aan uw behoeften te voldoen. Wijzig de sleutels niet. 

3. [Bereid het configuratie bestand voor (JSON)](#prepare-the-configuration-file-json). Kopieer het bestand **Template. cpp** en implementeer een onderdrukkings methode. Zie voor meer informatie [horizon::p rotocol:: BaseParser](#horizonprotocolbaseparser) voor details.

4. [Bereid de validatie van de implementatie code](#prepare-implementation-code-validations)voor.

## <a name="prepare-the-implementation-code-c"></a>De implementatie code voorbereiden (C++)

Het CPP-bestand is een parser die verantwoordelijk is voor:

- Validatie van de pakket header en Payload (bijvoorbeeld header lengte of Payload-structuur).

- Gegevens uit de koptekst en payload extra heren naar gedefinieerde velden.

- Het implementeren van geconfigureerde velden extractie door het JSON-bestand.

### <a name="what-to-do"></a>Wat u moet doen

Kopieer het bestand template **. cpp** en implementeer een onderdrukkings methode. Zie voor meer informatie [horizon::p rotocol:: BaseParser](#horizonprotocolbaseparser).

### <a name="basic-c-template-sample"></a>Voor beeld van Basic C++-sjabloon 

Deze sectie bevat de basis protocol sjabloon, met standaard functies voor een voor beeld van Defender voor IoT horizon-protocol.

```C++
#include “plugin/plugin.h”
namespace {
 class CyberxHorizonSDK: public horizon::protocol::BaseParser
  public:
   std::vector<uint64_t> processDissectAs(const std::map<std::string,
                                                         std::vector<std::string>> &filters) const override {
     return std::vector<uint64_t>();
   }
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     return horizon::protocol::ParserResult();
   }
 };
}

extern "C" {
  std::shared_ptr<horizon::protocol::BaseParser> create_parser() {
    return std::make_shared<CyberxHorizonSDK>();
  }
}

```

### <a name="basic-c-template-description"></a>Beschrijving van Basic C++-sjabloon  

Deze sectie bevat de basis protocol sjabloon, met een beschrijving van de standaard functies voor een voor beeld van Defender voor IoT horizon-protocol. 

### <a name="include-pluginpluginh"></a>#include "plugin/invoeg toepassing. h"

De definitie die de invoeg toepassing gebruikt. Het header-bestand bevat alles wat u nodig hebt om de ontwikkeling te volt ooien.

### <a name="horizonprotocolbaseparser"></a>Horizon::p rotocol:: BaseParser

De communicatie-interface tussen de horizon-infra structuur en de laag van de invoeg toepassing. Zie [horizon-architectuur](#horizon-architecture) voor een overzicht van de lagen voor meer informatie.

De processLayer is de methode die wordt gebruikt voor het verwerken van gegevens.

- De eerste para meter in de functie code is het verwerkings hulpprogramma dat wordt gebruikt voor het ophalen van eerder verwerkte gegevens, het maken van nieuwe velden en lagen.

- De tweede para meter in de functie code zijn de huidige gegevens die door de vorige parser zijn door gegeven.

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

Gebruiken om het exemplaar van de parser te maken.

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>Voor beeld van protocol functie code 

In deze sectie vindt u een voor beeld van hoe het code nummer (2 bytes) en de bericht lengte (4 bytes) worden geëxtraheerd.

Dit wordt gedaan volgens de waarde die is opgegeven in het JSON-configuratie bestand. Dit betekent dat als het protocol een *kleine endian* is en de sensor wordt uitgevoerd op een computer met een geringe endian, wordt deze geconverteerd.

Er wordt ook een laag gemaakt om gegevens op te slaan. Gebruik de *fieldsManager* van de hulppr. verwerking om nieuwe velden te maken. Een veld kan slechts een van de volgende typen bevatten: *teken reeks*, *getal*, *onbewerkte gegevens*, *matrix* (van een specifiek type) of *complex*. Deze laag kan een getal, een RAW of een teken reeks met ID bevatten.

In het onderstaande voor beeld worden de volgende twee velden geëxtraheerd:

- `function_code_number`

- `headerLength`

Er wordt een nieuwe laag gemaakt en het geëxtraheerde veld wordt gekopieerd.

In het onderstaande voor beeld wordt een specifieke functie beschreven. Dit is de belangrijkste logica die is geïmplementeerd voor de verwerking van de invoeg toepassing.

```C++
namespace {
 class CyberxHorizonProtocol: public horizon::protocol::BaseParser {
  public:
   
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     uint16_t codeNumber = data.readUInt16();
     uint32_t headerLength = data.readUInt32();
 
     auto &layer = ctx.createNewLayer();
 
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("code_number"),codeNumber;    
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("header_length"),headerLength);     
     return horizon::protocol::SuccessResult();
   }
 

```

### <a name="related-json-field"></a>Gerelateerde JSON-veld 

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="Het gerelateerde JSON-veld.":::

## <a name="prepare-the-configuration-file-json"></a>Het configuratie bestand voorbereiden (JSON)

De horizon-SDK gebruikt standaard JavaScript Object Notation (JSON), een licht gewicht indeling voor het opslaan en transporteren van gegevens en vereist geen specifieke script talen.

In deze sectie worden minimale JSON-configuratie declaraties beschreven, de gerelateerde structuur en een voor beeld van een configuratie bestand dat een protocol definieert. Dit protocol wordt automatisch geïntegreerd met de Device Discovery-service.

## <a name="file-structure"></a>Bestandsstructuur

In het voor beeld hieronder wordt de bestands structuur beschreven.

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="Het voor beeld van de bestands structuur.":::

### <a name="what-to-do"></a>Wat u moet doen

Kopieer het sjabloon `config.json` bestand en bewerk dit zodat het aan uw behoeften voldoet. Wijzig de sleutel niet. Sleutels worden rood gemarkeerd in het voor [beeld-JSON-configuratie bestand](#sample-json-configuration-file). 

### <a name="file-naming-requirements"></a>Vereisten voor bestands naamgeving 

Het JSON-configuratie bestand moet worden opgeslagen als `config.json` .

### <a name="json-configuration-file-fields"></a>Velden van het JSON-configuratie bestand

In deze sectie worden de JSON-configuratie velden beschreven die u wilt definiëren. Wijzig de *labels* van de velden niet.

### <a name="basic-parameters"></a>Basis parameters

In deze sectie worden de basis parameters beschreven.

| Parameter label | Beschrijving | Type |
|--|--|--|
| **ID** | De naam van het protocol. Verwijder de standaard waarde en voeg de naam van het protocol toe zoals deze wordt weer gegeven. | Tekenreeks |
| **endianess** | Hiermee wordt gedefinieerd hoe de multi byte gegevens worden gecodeerd. Gebruik de term ' kleine ' of ' Big ' alleen. Overgenomen van de protocol specificatie of verkeers opname | Tekenreeks |
| **sanity_failure_codes** | Dit zijn de codes die worden geretourneerd door de parser wanneer er sprake is van een Sanity-conflict met betrekking tot de identiteit van de code. Zie Magic-nummer validatie in de sectie C++. | Tekenreeks |
| **malformed_codes** | Dit zijn de codes die juist zijn geïdentificeerd, maar er wordt een fout gedetecteerd. Bijvoorbeeld als de veld lengte te kort of lang is of een waarde ongeldig is. | Tekenreeks |
| **dissect_as** | Een matrix die definieert waar het specifieke protocol verkeer moet arriveren. | TCP/UDP, poort etc. |
| **bedragvelden** | De declaratie van de velden die uit het verkeer worden opgehaald. Elk veld heeft een eigen ID (naam) en het type (numeric, String, RAW, array, complex). Bijvoorbeeld de [functie](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k) Field die wordt geëxtraheerd in het implementatie parser-bestand. De velden die in het configuratie bestand zijn geschreven, zijn de enige die aan de laag kunnen worden toegevoegd. |  |

### <a name="other-advanced-fields"></a>Andere geavanceerde velden 

In deze sectie worden andere velden beschreven.

| Parameter label | Beschrijving |
|-----------------|--------|
| **lijsten toestaan** | U kunt de protocol waarden indexeren en weer geven in rapporten voor gegevens analyse. Deze rapporten zijn gebaseerd op uw netwerk basislijn. :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Een voor beeld van de weer gave voor gegevens analyse."::: <br /> Zie [verbinding maken met een Indexing-service (basis lijn)](#connect-to-an-indexing-service-baseline) voor meer informatie. |
| **firmware** | U kunt firmware gegevens extra heren, index waarden definiëren en firmware waarschuwingen activeren voor het Protocol van de invoeg toepassing. Zie [firmware gegevens extra heren](#extract-firmware-data) voor meer informatie. |
| **value_mapping** | U kunt de uitvoer tekst van de invoeg toepassing aanpassen zodat deze voldoet aan de behoeften van uw bedrijfs omgeving door toewijzings bestanden te definiëren en bij te werken. Bijvoorbeeld: toewijzen aan taal bestanden. Wijzigingen kunnen eenvoudig worden geïmplementeerd in tekst zonder de code te wijzigen of te beïnvloeden. Zie [toewijzings bestanden (JSON) maken](#create-mapping-files-json) voor meer informatie. |

## <a name="sample-json-configuration-file"></a>Voor beeld van JSON-configuratie bestand

```json
{
  "id":"CyberX Horizon Protocol",
  "endianess": "big",
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
{
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ]
}


```

## <a name="prepare-implementation-code-validations"></a>Validatie van de implementatie code voorbereiden

In deze sectie worden de implementatie functies voor code validatie beschreven en vindt u voorbeeld code. Er zijn twee validatie lagen beschikbaar:

- Sanity.

- Verkeerd ingedeelde code.

U hoeft geen validatie code te maken om een werkende invoeg toepassing te kunnen bouwen. Als u de validatie code niet voorbereidt, kunt u sensor gegevens analyse rapporten bekijken als een indicatie van een geslaagde verwerking.

Veld waarden kunnen worden toegewezen aan de tekst in toewijzings bestanden en naadloos worden bijgewerkt zonder dat dit invloed heeft op de verwerking.

## <a name="sanity-code-validations"></a>Validaties van Sanity-code 

Hiermee wordt gevalideerd of het verzonden pakket overeenkomt met de validatie parameters van het Protocol, waarmee u het protocol in het verkeer kunt identificeren.

Gebruik bijvoorbeeld de eerste acht bytes als *Magic-nummer*. Als de Sanity mislukt, wordt een reactie van een Sanity-fout geretourneerd.

Bijvoorbeeld:

```C++
  horizon::protocol::ParserResult 
processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer 
&data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }
```

Als andere relevante invoeg toepassingen zijn geïmplementeerd, wordt het pakket op basis daarvan gevalideerd.

## <a name="malformed-code-validations"></a>Ongeldige code validaties 

Ongeldige validaties worden gebruikt nadat het protocol positief is gevalideerd.

Als er een fout is opgetreden bij het verwerken van de pakketten op basis van het Protocol, wordt een fout bericht geretourneerd.

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="ongeldige code":::

## <a name="c-sample-with-validations"></a>C++-voor beeld met validaties

Volgens de functie wordt het proces uitgevoerd, zoals wordt weer gegeven in het onderstaande voor beeld.

### <a name="function-20"></a>Functie 20 

- Het wordt verwerkt als firmware.

- De velden worden op basis van de functie gelezen.

- De velden worden toegevoegd aan de laag.

### <a name="function-10"></a>Functie 10 

- De functie bevat een andere subfunctie. Dit is een meer specifieke bewerking

- De subfunctie wordt gelezen en aan de laag toegevoegd.

Wanneer dit is gebeurd, wordt de verwerking voltooid. De geretourneerde waarde geeft aan of de laag van de branche is verwerkt. Als dat het geval is, wordt de laag bruikbaar.

```C++
#include "plugin/plugin.h"

#define FUNCTION_FIRMWARE_RESPONSE 20

#define FUNCTION_SUBFUNCTION_REQUEST 10

namespace {

class CyberxHorizonSDK: public horizon::protocol::BaseParser {

 public:

  std::vector<uint64_t> processDissectAs(const std::map<std::string,

                                                        std::vector<std::string>> &filters) const override {

    return std::vector<uint64_t>();

  }

  horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,

                                               horizon::general::IDataBuffer &data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }

    uint16_t function = data.readUInt16();

    uint32_t length = data.readUInt32();

    if (length > data.getRemaningData()) {

      return horizon::protocol::MalformedResult(0);

    }

    auto &layer = ctx.createNewLayer();

    ctx.getFieldsManager().create(layer, HORIZON_FIELD("function"), function);

    switch (function) {

      case FUNCTION_FIRMWARE_RESPONSE: {

        uint8_t modelLength = data.readUInt8();

        std::string model = data.readString(modelLength);

        uint16_t firmwareVersion = data.readUInt16();

        uint8_t nameLength = data.readUInt8();

        std::string name = data.readString(nameLength);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("model"), model);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("version"), firmwareVersion);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("name"), name);

      }

      break;

      case FUNCTION_SUBFUNCTION_REQUEST: {

       uint8_t subFunction = data.readUInt8();

       ctx.getFieldsManager().create(layer, HORIZON_FIELD("sub_function"), subFunction);

      }

      break;

    }

    return horizon::protocol::SuccessResult();

  }

};

}

extern "C" {

 std::shared_ptr<horizon::protocol::BaseParser> create_parser() {

   return std::make_shared<CyberxHorizonSDK>();

 }

}
```

## <a name="extract-device-metadata"></a>Meta gegevens van apparaten extra heren

U kunt de volgende meta gegevens voor assets extra heren:

  - `Is_distributed_control_system` -Geeft aan of het protocol deel uitmaakt van het gedistribueerde besturings systeem. (voor beeld: SCADA-protocol moet onwaar zijn)

  - `Has_protocol_address` – Geeft aan of er een Protocol adres is; het specifieke adres voor het huidige protocol, bijvoorbeeld MODBUS Unit Identifier

  - `Is_scada_protocol` -Geeft aan of het protocol specifiek is voor netwerken

  - `Is_router_potential` -Geeft aan of het protocol hoofd zakelijk wordt gebruikt door routers. Bijvoorbeeld, LLDP, CDP of STP.

Om dit te kunnen doen, moet het JSON-configuratie bestand worden bijgewerkt met behulp van de meta gegevens eigenschap.

## <a name="json-sample-with-metadata"></a>JSON-voor beeld met meta gegevens 

```json

{
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 "fields": [
   {
     "id": "function",
     "type": "numeric"
},
   {
     "id": "sub_function",
     "type": "numeric"
   },
   {
     "id": "name",
     "type": "string"
   },
   {
     "id": "model",
     "type": "string"
   },
   {
     "id": "version",
     "type": "numeric"
   }
 ],
}

```

## <a name="extract-programming-code"></a>Programma code extra heren 

Wanneer de programmerings gebeurtenis zich voordoet, kunt u de code-inhoud extra heren. Met de geëxtraheerde inhoud kunt u:

- Code bestand inhoud vergelijken in verschillende programmeer gebeurtenissen.

- Een waarschuwing activeren bij niet-geautoriseerde programmering.  

- Een gebeurtenis activeren voor het ontvangen van programma code bestand.

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="Het logboek voor het wijzigen van de programmering.":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="Klik op de knop om de programmering weer te geven.":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="De niet-geautoriseerde PLC-programmeer waarschuwing.":::

Om dit te kunnen doen, moet het JSON-configuratie bestand worden bijgewerkt met behulp van de `code_extraction` eigenschap. 

### <a name="json-configuration-fields"></a>JSON-configuratie velden 

In deze sectie worden de JSON-configuratie velden beschreven. 

- **methode**

  Hiermee wordt aangegeven hoe programmeer gebeurtenis bestanden worden ontvangen. 

  ALLE (elke programmeer actie zorgt ervoor dat alle code bestanden worden ontvangen, zelfs als er bestanden zijn zonder wijzigingen).

- **file_type**  

  Hiermee wordt het type code-inhoud aangegeven.  

  TEKST (elk code bestand bevat tekstuele informatie).

- **code_data_field**
  
  Hiermee wordt het implementatie veld aangegeven dat moet worden gebruikt om de code-inhoud op te geven.

  Aan.

- **code_name_field**

  Hiermee wordt het implementatie veld aangegeven dat moet worden gebruikt om de naam van het code bestand op te geven.

  Aan.

- **size_limit**

  Hiermee wordt de grootte limiet van elke inhoud van het codeer bestand in BYTES aangegeven, als een code bestand de ingestelde limiet overschrijdt. Als dit veld niet is opgegeven, wordt de standaard waarde 15.000.000 15 MB.

  Telwoord.

- **metagegevensarchiefmethode**

  Geeft aanvullende informatie aan voor een code bestand.

  Matrix met objecten met twee eigenschappen:

    - naam (teken reeks): geeft aan dat de meta gegevens sleutel XSense momenteel alleen ondersteuning biedt voor de sleutel username.
 
    - waarde (veld): Hiermee wordt het implementatie veld aangegeven dat moet worden gebruikt om de meta gegevens op te geven.

## <a name="json-sample-with-programming-code"></a>JSON-voor beeld met programmeer code

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "script",
      "type": "string"
    },
    {
      "id": "script_name",
      "type": "string"
    }, 
      "id": "username",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon 
                                                Protocol Function",
      "alert_text": "There was an attempt by the source to 
                        invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, 
                  for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"code_extraction": {
    "method": "ALL",
    "file_type": "TEXT",
    "code_data_field": "script",
    "code_name_field": "script_name",
    "size_limit": 15000000,
    "metadata": [
      {
        "name": "username",
        "value": "username"
      }
    ]
  }
}

```
## <a name="custom-horizon-alerts"></a>Aangepaste waarschuwingen voor de horizon

Sommige protocol functie code kunnen duiden op een fout. Als het protocol bijvoorbeeld een container beheert met een specifieke chemische stof die altijd moet worden opgeslagen op een specifieke Tempe ratuur. In dit geval kan er sprake zijn van functie code die aangeeft dat er een fout is in de Thermo meter. Als de functie code bijvoorbeeld 25 is, kunt u een waarschuwing activeren in de webconsole die aangeeft dat er een probleem is met de container. In dat geval kunt u diepe pakket waarschuwingen definiëren.

Voeg de para meter **waarschuwingen** toe aan de `config.json` van de invoeg toepassing.

```json
“alerts”: [{
    “id”: 1,
    “message”: “Problem with thermometer at station {IPv4.src}”,
    “title”: “Thermometer problem”,
    “expression”: “{CyberXHorizonProtocol.function} == 25”
}]

```

## <a name="json-configuration-fields"></a>JSON-configuratie velden

In deze sectie worden de JSON-configuratie velden beschreven. 

| Veldnaam | Beschrijving | Mogelijke waarden |
|--|--|--|
| **ID** | Hiermee wordt één waarschuwings-ID aangeduid. Deze moet uniek zijn in deze context. | Numerieke waarde 0-10000 |
| **Bericht** | Informatie die wordt weer gegeven aan de gebruiker. Met dit veld kunt u verschillende velden gebruiken. | Gebruik een wille keurig veld uit uw protocol of een protocol voor een lagere laag. |
| **hoofd** | De titel van de waarschuwing |  |
| **expression** | Wanneer u wilt dat deze waarschuwing wordt pop-up. | Gebruik een numeriek veld in lagere lagen of de huidige laag.</br></br> Elk veld moet wrapper hebben met, zodat de SDK het kan `{}` detecteren als een veld, de ondersteunde logische Opera tors zijn</br> = =-Is gelijk aan</br> <=-kleiner dan of gelijk aan</br> >=-meer dan of gelijk aan</br> >-meer dan</br> <-kleiner dan</br> ~ =-Niet gelijk aan |

## <a name="more-about-expressions"></a>Meer over expressies

Telkens wanneer de horizon SDK de expressie aanroept en deze wordt ingesteld op *True*, wordt er een waarschuwing in de sensor geactiveerd.

Meerdere expressies kunnen onder dezelfde waarschuwing worden opgenomen. Bijvoorbeeld:

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

Met deze expressie wordt de functie code alleen gevalideerd wanneer het pakket-IPv4-bron-10.10.10.10 is. Dit is een onbewerkte weer gave van het IP-adres in numerieke weer gave.

U kunt gebruiken `and` , of `or` om expressies te verbinden.

## <a name="json-sample-custom-horizon-alerts"></a>Aangepaste periode waarschuwingen voor JSON-voor beeld

```json
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 …………………………………….
 “alerts”: [{
“id”: 1,
“message”: “Problem with thermometer at station {IPv4.src}”,
“title”: “Thermometer problem”,
“expression”: “{CyberXHorizonProtocol.function} == 25”

```

## <a name="connect-to-an-indexing-service-baseline"></a>Verbinding maken met een Indexing-service (basis lijn)

U kunt de protocol waarden indexeren en weer geven in rapporten voor gegevens analyse.

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Een weer gave van de optie voor gegevens analyse.":::

Deze waarden kunnen later worden toegewezen aan specifieke teksten, zoals toewijzings nummers als teksten of het toevoegen van informatie, in elke taal.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="virtuelemachinemigratie":::

Zie [toewijzings bestanden (JSON) maken](#create-mapping-files-json) voor meer informatie.

U kunt ook waarden uit protocollen die eerder zijn geparseerd, gebruiken om aanvullende informatie te extra heren.

Voor de waarde, die is gebaseerd op TCP, kunt u bijvoorbeeld de waarden van IPv4-laag gebruiken. Vanuit deze laag kunt u waarden extra heren, zoals de bron van het pakket en de bestemming.

Om dit te kunnen doen, moet het JSON-configuratie bestand worden bijgewerkt met behulp van de `whitelist` eigenschap.

## <a name="allow-list-data-mining-fields"></a>Velden voor acceptatie lijst (gegevens analyse)

De volgende acceptatie lijst velden zijn beschikbaar:

- naam: de naam die wordt gebruikt voor het indexeren.

- alert_title: een korte, unieke titel die de gebeurtenis verklaart.

- alert_text: aanvullende informatie

Er kunnen meerdere allow-lijsten worden toegevoegd, waardoor de volledige flexibiliteit voor het indexeren mogelijk is.

## <a name="json-sample-with-indexing"></a>JSON-voor beeld met indexering 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    }

```
## <a name="extract-firmware-data"></a>Firmware gegevens extra heren

U kunt firmware gegevens extra heren, index waarden definiëren en firmware waarschuwingen activeren voor het Protocol van de invoeg toepassing. Bijvoorbeeld:

- Pak het firmware model of de versie uit. Deze informatie kan verder worden gebruikt om CVEs te identificeren.

- Een waarschuwing activeren wanneer een nieuwe firmware versie wordt gedetecteerd.

Om dit te kunnen doen, moet het JSON-configuratie bestand worden bijgewerkt met behulp van de eigenschap firmware.

## <a name="firmware-fields"></a>Firmware velden

In deze sectie worden de configuratie velden voor JSON-firmware beschreven.

- **name**
  
  Hiermee wordt aangegeven hoe het veld wordt weer gegeven in de sensor console.

- **value**

 Hiermee wordt het implementatie veld aangegeven dat moet worden gebruikt om de gegevens op te geven. 

- **firmware_index:**

  Selecteer een optie:  
  - **model**: het model van het apparaat. Hiermee wordt de detectie van CVEs ingeschakeld.
  - **serieel**: het serie nummer van het apparaat. Het serie nummer is niet altijd beschikbaar voor alle protocollen. Deze waarde is uniek per apparaat.
  - **rek**: geeft de rack-id aan als het apparaat deel uitmaakt van een rek.
  - **sleuf**: de sleuf-id als het apparaat deel uitmaakt van een rek.  
  - **module_address**: gebruik dit om een hiërarchie te presen teren als de module achter een ander apparaat kan worden weer gegeven. In plaats daarvan wordt een combi natie van een rack sleuf, die een eenvoudigere presentatie is.  
  - **firmware_version**: geeft de versie van het apparaat aan. Hiermee wordt de detectie van CVEs ingeschakeld.
  - **alert_text**: geeft tekst aan met een beschrijving van de firmware afwijkingen, bijvoorbeeld versie wijzigingen.  
  - **index_by**: Hiermee worden de velden aangegeven die worden gebruikt voor het identificeren en indexeren van het apparaat. In het voor beeld onder het apparaat wordt aangeduid met het IP-adres. In bepaalde protocollen kan een complexere index worden gebruikt. Als er bijvoorbeeld een ander apparaat is verbonden en u weet hoe u het interne pad kunt extra heren. De MODBUS eenheid-ID kan bijvoorbeeld worden gebruikt als onderdeel van de index, als een andere combi natie van IP-adres en eenheid-id.
  - **firmware_fields**: geeft aan welke velden de meta gegevens velden van een apparaat zijn. In dit voor beeld worden de volgende opties gebruikt: model, revisie en naam. Elk protocol kan zijn eigen firmware gegevens definiëren.

## <a name="json-sample-with-firmware"></a>JSON-voor beeld met firmware 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset.   
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
}

```
## <a name="extract-device-attributes"></a>Attributen van het apparaat extra heren 

U kunt de beschik bare informatie op het apparaat in de inventaris, gegevens analyse en andere rapporten verbeteren.

- Naam

- Type

- Leverancier

- Besturingssysteem

Om dit te kunnen doen, moet het JSON-configuratie bestand worden bijgewerkt met behulp van de eigenschap **Properties** . 

U kunt dit doen na het schrijven van de Basic-invoeg toepassing en het extra heren van de vereiste velden.

## <a name="properties-fields"></a>Eigenschappen velden

In deze sectie worden de configuratie velden van de JSON-eigenschappen beschreven. 

**config_file** 

Bevat de naam van het bestand dat definieert hoe elke sleutel in de velden moet worden verwerkt `key` . Het configuratie bestand zelf moet de JSON-indeling hebben en moet worden opgenomen als onderdeel van de map voor het invoeg toepassing-protocol.

Elke sleutel in de JSON definieert de set actie die moet worden uitgevoerd wanneer u deze sleutel uit een pakket ophaalt.

Elke sleutel kan het volgende hebben:

- **Pakket gegevens** : geeft de eigenschappen aan die worden bijgewerkt op basis van de gegevens die zijn geëxtraheerd uit het pakket (het implementatie veld dat wordt gebruikt om die gegevens op te geven).

- **Statische gegevens** : geeft een vooraf gedefinieerde set `property-value` acties aan die moeten worden bijgewerkt.

De eigenschappen die in dit bestand kunnen worden geconfigureerd zijn: 

- **Naam** : Hiermee geeft u de naam van het apparaat.

- **Type** : geeft het apparaattype aan.

- **Leverancier** : geeft de leverancier van het apparaat aan.

- **Beschrijving** : geeft het firmware model van het apparaat (lagere prioriteit dan model) aan.

- **Operating** System: geeft het besturings systeem van het apparaat aan.

### <a name="fields"></a>Velden

| Veld | Beschrijving |
|--|--|
| sleutel | Geeft de sleutel aan. |
| waarde | Hiermee wordt het implementatie veld aangegeven dat moet worden gebruikt om de gegevens op te geven. |
| is_static_key | Hiermee wordt aangegeven of het `key` veld is afgeleid als een waarde van het pakket of een vooraf gedefinieerde waarde is. |

### <a name="working-with-static-keys-only"></a>Alleen met statische sleutels werken

Als u werkt met statische sleutels, hoeft u de niet te configureren `config.file` . U kunt het JSON-bestand alleen configureren.

## <a name="json-sample-with-properties"></a>JSON-voor beeld met eigenschappen 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
  
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "vendor",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
"properties": {
    "config_file": "config_file_example",
"fields": [
  {
    "key": "vendor",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },
{
    "key": "name",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },

]
  }
}

```

## <a name="config_file_exapmle-json"></a>CONFIG_FILE_EXAPMLE JSON 

```json
{
"someKey": {
  "staticData": {
    "model": "FlashSystem", 
    "vendor": "IBM", 
    "type": "Storage"}
  }
  "packetData": [
    "name”  
  ]
}

```

## <a name="create-mapping-files-json"></a>Toewijzings bestanden maken (JSON)

U kunt de uitvoer tekst van de invoeg toepassing aanpassen om te voldoen aan de behoeften van uw bedrijfs omgeving door bestanden te definiëren en bij te werken. Wijzigingen kunnen eenvoudig worden geïmplementeerd in tekst zonder de code te wijzigen of te beïnvloeden. Elk bestand kan een of meer velden toewijzen.

- Toewijzing van veld waarden aan namen, bijvoorbeeld 1: opnieuw instellen, 2: begin, 3: stoppen.

- Toewijzing van tekst ter ondersteuning van meerdere talen.

Er kunnen twee typen toewijzings bestanden worden gedefinieerd.

  - [Eenvoudig toewijzings bestand](#simple-mapping-file).

  - [Bestand met afhankelijkheids toewijzing](#dependency-mapping-file).

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="ether-net":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="Een weer gave van de onverwerkte waarschuwingen.":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="Een lijst met bekende beleids schendingen.":::

## <a name="file-naming-and-storage-requirements"></a>Bestands namen en opslag vereisten 

Toewijzings bestanden moeten worden opgeslagen in de map met meta gegevens.

De naam van het bestand moet overeenkomen met de ID van het JSON-configuratie bestand.

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="Een voor beeld van een JSON-configuratie bestand.":::

## <a name="simple-mapping-file"></a>Eenvoudig toewijzings bestand 

In het volgende voor beeld wordt een basis-JSON-bestand als sleutel waarde weer gegeven.

Wanneer u een acceptatie lijst maakt en deze een of meer van de toegewezen velden bevat. De waarde wordt geconverteerd van een getal, een teken reeks of een wille keurig type, in naar opgemaakte tekst die in het toewijzings bestand wordt weer gegeven.

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>Bestand met afhankelijkheids toewijzing 

Om aan te geven dat het bestand een afhankelijkheids bestand is, voegt u het tref woord toe `dependency` aan de toewijzings configuratie.

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

Het bestand bevat een toewijzing tussen het veld afhankelijkheid en het veld functie. Bijvoorbeeld tussen de functie en de sub-functie. De subfunctie wordt gewijzigd op basis van de opgegeven functie.

In de acceptatie lijst die eerder is geconfigureerd, is er geen afhankelijkheids configuratie, zoals hieronder wordt weer gegeven.

```json
"whitelists": [
{
"name": "Functions",
"alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
"alert_text": "There was an attempt by the source to invoke a new function on the destination",
"fields": [
{
"name": "Source",
"value": "IPv4.src"
},
{
"name": "Destination",
"value": "IPv4.dst"
},
{
"name": "Function",
"value": "CyberXHorizonProtocol.function"
}
]
}

```

De afhankelijkheid kan worden gebaseerd op een specifieke waarde of een veld. In het onderstaande voor beeld is het gebaseerd op een veld. Als u het op een waarde baseert, definieert u de extractie waarde die moet worden gelezen uit het toewijzings bestand.

In het onderstaande voor beeld wordt de afhankelijkheid gevolgd door dezelfde waarde van het veld.

Zo wordt in de subfunctie vijf de betekenis gewijzigd op basis van de functie.

  - Als het een lees functie is, betekent vijf gelezen geheugen.

  - Als het een schrijf functie is, wordt dezelfde waarde gebruikt voor het lezen van een bestand.

    ```json
    {
      “10”: {
         “5”:  “Memory”,
         “6”: “File”,
         “7” “Register”
       },
      “3”: {
       “5”: “File”,
       “7”: “Memory”,
       “6”, “Register”
      }
    }

    ```

### <a name="sample-file"></a>Voorbeeldbestand

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {"is_distributed_control_system": false, "has_protocol_address": false, "is_scada_protocol": true, "is_router_potenial": false},
  "sanity_failure_codes": { "wrong magic": 0 },
  "malformed_codes": { "not enough bytes": 0  },
  "exports_dissect_as": {  },
  "dissect_as": { "UDP": { "port": ["12345"] }},
  "fields": [{ "id": "function", "type": "numeric" }, { "id": "sub_function", "type": "numeric" },
     {"id": "name", "type": "string" }, { "id": "model", "type": "string" }, { "id": "version", "type": "numeric" }],
  "whitelists": [{
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
      "fields": [{ "name": "Source", "value": "IPv4.src" }, { "name": "Destination", "value": "IPv4.dst" },
        { "name": "Function", "value": "CyberXHorizonProtocol.function" },
        { "name": "Sub function", "value": "CyberXHorizonProtocol.sub_function", "dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value": "CyberXHorizonProtocol.model", "firmware_index": "model" },
       { "name": "Revision", "value": "CyberXHorizonProtocol.version", "firmware_index": "firmware_version" },
       { "name": "Name", "value": "CyberXHorizonProtocol.name" }]
  },
  "value_mapping": {
      "CyberXHorizonProtocol.function": {
        "file": "function-mapping"
      },
      "CyberXHorizonProtocol.sub_function": {
        "dependency": true,
        "file": "sub_function-mapping"
      }
  }
}

```

## <a name="json-sample-with-mapping"></a>JSON-voor beeld met toewijzing

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        },
        {
          “name”: “Sub function”,
          “value”: “CyberXHorizonProtocol.sub_function”,
          “dependency”: {
              “field”: “CyberXHorizonProtocol.function”
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"value_mapping": {
    "CyberXHorizonProtocol.function": {
      "file": "function-mapping"
    },
    "CyberXHorizonProtocol.sub_function": {
      "dependency": true,
      "file": "sub_function-mapping"
    }
}

```
## <a name="package-upload-and-monitor-the-plugin"></a>De invoeg toepassing inpakken, uploaden en bewaken 

In deze sectie wordt beschreven hoe u

  - Verpak uw invoeg toepassing.

  - Upload uw invoeg toepassing.

  - Controleer en fouten opsporen in de invoeg toepassing om te evalueren hoe goed het wordt uitgevoerd.

De invoeg toepassing inpakken:

1. Voeg het **artefact** (kan, bibliotheek, config.jsaan of meta gegevens) toe aan een `tar.gz` bestand.

1. Wijzig de bestands extensie in \<XXX.hdp> , waarbij \<XXX> de naam is van de invoeg toepassing.

Aanmelden bij de horizon-console:

1.  Meld u aan bij uw sensor-CLI als beheerder, Cyberx of ondersteunings gebruiker.

2.  In het bestand: `/var/cyberx/properties/horizon.properties` Wijzig de eigenschap **UI. enabled** in **True** ( `horizon.properties:ui.enabled=true` ).

3.  Meld u aan bij de sensor console.

4.  Selecteer de optie **horizon** in het hoofd menu.

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="Selecteer de optie horizon in het deel venster aan de linkerkant.":::

    De horizon-console wordt geopend.

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="Een weer gave van de horizon-console en alle bijbehorende invoeg toepassingen.":::

## <a name="plugins-pane"></a>Deel venster invoeg toepassingen

Het deel venster invoeg toepassingen bevat het volgende:

  - Infra structuur-invoeg toepassingen: de infra structuur-invoeg toepassingen worden standaard met Defender voor IoT geïnstalleerd.

  - Application plugins: toepassings-invoeg toepassingen worden standaard geïnstalleerd met Defender voor IoT en andere invoeg toepassingen die zijn ontwikkeld door Defender voor IoT of externe ontwikkel aars.
    
Invoeg toepassingen die zijn geüpload met behulp van de wissel knop in-en uitschakelen.

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="De wissel knop overschrijving.":::

### <a name="uploading-a-plugin"></a>Een invoeg toepassing uploaden

Nadat u uw invoeg toepassing hebt gemaakt en verpakt, kunt u deze uploaden naar de Defender voor IoT-sensor. Als u de volledige dekking van uw netwerk wilt benutten, moet u de invoeg toepassing uploaden naar elke sensor in uw organisatie.

Uploaden:

1.  Meld u aan bij uw sensor.


2. Selecteer **Uploaden**.

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="Upload uw invoeg toepassingen.":::

3. Blader naar de invoeg toepassing en sleep deze naar het dialoog venster invoeg toepassing. Controleer of het voor voegsel is `.hdp` . De invoeg toepassing wordt geladen.

## <a name="plugin-status-overview"></a>Overzicht van de status van de invoeg toepassing 

Het venster horizon console **Overview** bevat informatie over de invoeg toepassing die u hebt geüpload en waarmee u deze kunt uitschakelen en inschakelen.

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="Het overzicht van de horizon-console.":::

| Veld | Beschrijving |
|--|--|
| Toepassing | De naam van de invoeg toepassing die u hebt geüpload. |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="De schakel optie aan en uit."::: | De invoeg toepassing **in** -of **uitschakelen** . Defender voor IoT verwerkt geen protocol verkeer dat in de invoeg toepassing is gedefinieerd wanneer u de invoeg toepassing uitschakelt. |
| Tijd | De tijd dat de gegevens voor het laatst zijn geanalyseerd. Elke vijf seconden bijgewerkt. |
| PPS | Het aantal pakketten per seconde. |
| Bandbreedte | De gemiddelde band breedte die in de afgelopen vijf seconden is gedetecteerd. |
| Malforms | Ongeldige validaties worden gebruikt nadat het protocol positief is gevalideerd. Als er een fout is opgetreden bij het verwerken van de pakketten op basis van het Protocol, wordt een fout bericht geretourneerd.   <br><br>In deze kolom wordt het aantal malform-fouten in de afgelopen vijf seconden aangegeven. Zie [validatie van ongeldige code](#malformed-code-validations) voor meer informatie. |
| Waarschuwingen | Pakketten komen overeen met de structuur en specificatie, maar er is een onverwacht gedrag op basis van de configuratie van de waarschuwing voor de invoeg toepassing. |
| Fouten | Het aantal pakketten waarvoor basis protocol validaties zijn mislukt. Hiermee wordt gecontroleerd of het pakket overeenkomt met de protocol definities. Het getal dat hier wordt weer gegeven geeft aan dat het aantal fouten dat in de afgelopen vijf seconden is gedetecteerd. Zie voor meer informatie [Sanity code validions](#sanity-code-validations) voor details. |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="Het pictogram monitor."::: | Bekijk de informatie over malform en waarschuwingen die zijn gedetecteerd voor de invoeg toepassing. |

## <a name="plugin-details"></a>Details van invoeg toepassing

U kunt het gedrag van real-time-invoeg toepassingen controleren door het aantal *Malform* en *waarschuwingen* voor de invoeg toepassing te analyseren. Er is een optie beschikbaar om het scherm te blok keren en te exporteren voor verder onderzoek

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="Het scherm SNMP-monitor.":::

Controleren:

Selecteer de knop monitor voor de invoeg toepassing in het overzicht.

Volgende stappen

Uw horizon- [API](references-horizon-api.md) instellen
