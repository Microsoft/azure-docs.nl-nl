---
title: Oplossingen voor veelvoorkomende fouten en problemen in B2B-scenario's
description: Oplossingen voor veelvoorkomende fouten en problemen vinden bij het oplossen van problemen met B2B-scenario's in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 6400cfe7e524dcc16e08c2bba7dfba4a62d00b2e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86232556"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>B2B-fouten en oplossingen voor Azure Logic Apps

Dit artikel helpt u bij het oplossen van fouten die zich in Logische apps B2B scenario's kunnen voordoen en stelt passende acties voor om deze fouten te corrigeren.

## <a name="agreement-resolution"></a>Overeenkomst oplossing

### <a name="no-agreement-found"></a>Geen overeenkomst gevonden 

**Fout beschrijving**: er is geen overeenkomst gevonden met de para meters voor de overeenkomst oplossing.

**Gebruikers actie**: de overeenkomst moet worden toegevoegd aan het integratie account met overeengekomen bedrijfs identiteiten. De bedrijfs identiteiten moeten overeenkomen met de invoer bericht-Id's.

### <a name="no-agreement-found-with-identities"></a>Geen overeenkomst gevonden met identiteiten

**Fout beschrijving**: er is geen overeenkomst gevonden met identiteiten: ' AS2Identity ':: ' Partner1 ' and'AS2Identity ':: ' Partner3 '

**Gebruikers actie**: ongeldige AS2-From of AS2-To geconfigureerd voor de overeenkomst. Corrigeer de AS2-bericht ' AS2-from ' of ' AS2-to '-headers of de overeenkomst om te voldoen aan de AS2-Id's in de AS2-bericht koppen met overeenkomst configuraties.

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Ontbrekende AS2-bericht headers  

**Fout beschrijving**: ongeldige AS2-headers. Een van de headers "AS2-to" of "AS2-from" is leeg.

**Gebruikers actie**: er is een AS2-bericht ontvangen dat niet de AS2-From of AS2-To of beide headers bevat. Controleer het AS2-bericht AS2-From en AS2-To kopteksten en corrigeer ze op basis van de overeenkomst configuratie.

### <a name="missing-as2-message-body-and-headers"></a>Ontbrekende AS2 bericht tekst en kopteksten    

**Fout beschrijving**: de inhoud van de aanvraag is null of leeg.

**Gebruikers actie**: er is een AS2-bericht ontvangen dat de bericht tekst niet bevat.

### <a name="as2-message-decryption-failure"></a>Fout bij het ontsleutelen van AS2-berichten

**Fout beschrijving**: [verwerkte/fout: ontsleuteling-mislukt]

**Gebruikers actie**: Voeg toe @base64ToBinary aan AS2Message voordat u deze naar de partner verzendt.

Bijvoorbeeld:

```json
"HTTP": {
   "inputs": {
   "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
   "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
   "method": "POST",
   "uri": "xxxxx.xxx"
},
``` 

### <a name="mdn-decryption-failure"></a>Fout bij het decoderen van MDN

**Fout beschrijving**: [verwerkte/fout: ontsleuteling-mislukt]

**Gebruikers actie**: Voeg toe @base64ToBinary aan MDN voordat u deze naar de partner verzendt.

Bijvoorbeeld:

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Ontbrekend handtekening certificaat

**Fout beschrijving**: het handtekening certificaat is niet geconfigureerd voor AS2 Party. AS2-from: partner1 AS2-to: partner2

**Gebruikers actie**: Configureer instellingen voor de AS2-overeenkomst met het juiste certificaat voor hand tekening.

## <a name="x12-and-edifact"></a>X12 en EDIFACT

### <a name="leading-or-trailing-space-found"></a>Voor loop-of volg spatie gevonden    

**Fout beschrijving**: er is een fout opgetreden tijdens het parseren. De EDIFACT-transactieset met de ID ' 123456 ' die is opgenomen in Interchange (zonder groep) met ID ' 987654 ', met afzender-ID ' Partner1 ', ontvanger-ID ' Partner2 ' wordt onderbroken met de volgende fouten: ' voorloop scheidings teken gevonden '

**Gebruikers actie**: de instellingen van de overeenkomst die moeten worden geconfigureerd voor het toestaan van voor loop-en volg spaties. Overeenkomst instellingen bewerken om voorloop-en volg spaties toe te staan.

![ruimte toestaan](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Controle op dubbele items is ingeschakeld in de overeenkomst

**Fout beschrijving**: dubbel controle nummer

**Gebruikers actie**: deze fout geeft aan dat het ontvangen bericht dubbele controle nummers heeft. Corrigeer het controle nummer en verzend het bericht opnieuw.

### <a name="missing-schema-in-the-agreement"></a>Ontbrekend schema in de overeenkomst

**Fout beschrijving**: er is een fout opgetreden tijdens het parseren. De X12-transactieset met ID ' 564220001 ' die is opgenomen in de functionele groep met ID ' 56422 ' in de uitwisseling met ID 000056422, met afzender-ID ' 12345678 ' 87654321, wordt geblokkeerd met de volgende fouten: ' het bericht heeft een onbekend document type en is niet omgezet naar een van de bestaande schema's die in de overeenkomst zijn geconfigureerd '

**Gebruikers actie**: schema configureren in de instellingen van de overeenkomst.

### <a name="incorrect-schema-in-the-agreement"></a>Onjuist schema in de overeenkomst

**Fout beschrijving**: het bericht bevat een onbekend document type en is niet omgezet in een van de bestaande schema's die in de overeenkomst zijn geconfigureerd.

**Gebruikers actie**: Configureer het juiste schema in de instellingen van de overeenkomst.

## <a name="flat-file"></a>Plat bestand

### <a name="input-message-with-no-body"></a>Invoer bericht zonder hoofd tekst

**Fout beschrijving**: InvalidTemplate. Kan de sjabloon taal expressies niet verwerken in de invoer van de actie ' Flat_File_Decoding ' op regel ' 1 ' en in kolom ' 1902 ': ' de vereiste eigenschap ' content ' verwacht een waarde, maar is null. Pad.

**Gebruikers actie**: deze fout geeft aan dat het invoer bericht geen hoofd tekst bevat.
