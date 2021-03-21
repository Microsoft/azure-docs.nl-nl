---
title: Aangepaste opdrachten concepten en definities-spraak service
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u informatie over de concepten en definities voor toepassingen voor aangepaste opdrachten.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 98510132b2341736664dfafa52e9567df95652be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94561094"
---
# <a name="custom-commands-concepts-and-definitions"></a>Concepten en definities voor aangepaste opdrachten

In dit artikel vindt u informatie over concepten en definities voor toepassingen voor aangepaste opdrachten.

## <a name="commands-configuration"></a>Configuratie van opdrachten
Opdrachten zijn de basis bouwstenen van een toepassing met aangepaste opdrachten. Een opdracht is een set configuraties die vereist is voor het volt ooien van een specifieke taak gedefinieerd door een gebruiker.

### <a name="example-sentences"></a>Voorbeeld zinnen
Voor beeld uitingen zijn de set-voor beelden die de gebruiker kan zeggen dat er een bepaalde opdracht wordt geactiveerd. U hoeft alleen een voor beeld van uitingen op te geven en geen uitputtende lijst. 

### <a name="parameters"></a>Parameters
Para meters zijn de vereiste gegevens voor de opdrachten voor het volt ooien van een taak. In complexe scenario's kunnen para meters ook worden gebruikt voor het definiëren van voor waarden die aangepaste acties activeren.

### <a name="completion-rules"></a>Voltooiings regels
Voltooiings regels zijn een reeks regels die moeten worden uitgevoerd nadat de opdracht is voltooid, bijvoorbeeld wanneer aan alle voor waarden van de regels is voldaan.

### <a name="interaction-rules"></a>Interactie regels
Interactie regels zijn aanvullende regels voor het verwerken van specifieke of complexe situaties. U kunt aanvullende validaties toevoegen of geavanceerde functies configureren, zoals bevestigingen of een correctie met één stap. U kunt ook uw eigen aangepaste interactie regels maken.

## <a name="parameters-configuration"></a>Configuratie van para meters

Para meters zijn gegevens die worden vereist door opdrachten voor het volt ooien van een taak. In complexe scenario's kunnen para meters ook worden gebruikt voor het definiëren van voor waarden die aangepaste acties activeren.

### <a name="name"></a>Name
Een para meter wordt geïdentificeerd door de eigenschap name. U moet altijd een beschrijvende naam opgeven voor een para meter. Een para meter kan worden verwezen in verschillende secties, bijvoorbeeld wanneer u voor waarden, spraak reacties of andere acties bouwt.
 
### <a name="isglobal"></a>IsGlobal
Dit selectie vakje geeft aan of het bereik van deze para meter wordt gedeeld door alle opdrachten in de toepassing. Als een para meter globaal is, kan de waarde ervan worden verschaft vanuit elk opdracht bereik. Nadat een waarde is toegewezen, kan deze worden aangeduid vanuit een van de opdrachten. 

### <a name="required"></a>Vereist
Dit selectie vakje geeft aan of een waarde voor deze para meter is vereist voor het uitvoeren van opdrachten of het volt ooien van de opdracht. U moet antwoorden configureren om de gebruiker te vragen om een waarde op te geven als een para meter is gemarkeerd als vereist.

Houd er rekening mee dat als u een **vereiste para meter** hebt geconfigureerd voor een **standaard waarde**, het systeem nog steeds expliciet om de waarde van de para meter wordt gevraagd.

### <a name="type"></a>Type
Aangepaste opdrachten bieden ondersteuning voor de volgende parameter typen:

* DateTime
* Geografie
* Aantal
* Tekenreeks

Al deze parameter typen, met uitzonde ring van geografie, ondersteunen de configuratie van de standaard waarde, die u vanuit de portal kunt configureren.

### <a name="configuration"></a>Configuratie
Configuratie is een para meter-eigenschap die alleen voor de type teken reeks is gedefinieerd. De volgende waarden worden ondersteund:

* **Geen**.
* **Volledige invoer accepteren**: wanneer deze functie is ingeschakeld, accepteert een para meter alle invoer-utterance. Deze optie is handig als de gebruiker een para meter met de volledige utterance nodig heeft. Een voor beeld hiervan is post adressen.
* **Vooraf gedefinieerde invoer waarden accepteren uit een externe catalogus**: deze waarde wordt gebruikt voor het configureren van een para meter die kan uitgaan van een groot aantal verschillende waarden. Een voor beeld is een verkoop catalogus. In dit geval wordt de catalogus gehost op een extern webeindpunt en kan deze onafhankelijk worden geconfigureerd.
* **Vooraf gedefinieerde invoer waarden accepteren uit interne catalogus**: deze waarde wordt gebruikt voor het configureren van een para meter die een aantal waarden kan aannemen. In dit geval moeten waarden worden geconfigureerd in de speech Studio.


### <a name="validation"></a>Validatie
Validaties zijn constructies die van toepassing zijn op bepaalde parameter typen waarmee u beperkingen kunt configureren voor de waarde van een para meter. Op dit moment worden door aangepaste opdrachten validaties ondersteund voor de volgende parameter typen:

* DateTime
* Aantal

## <a name="rules-configuration"></a>Configuratie van regels
Een regel in aangepaste opdrachten wordt gedefinieerd door een set *voor waarden* die, wanneer wordt voldaan, een reeks *acties* uitvoeren. Met regels kunt u ook de status van de *post-uitvoering* en de *verwachtingen* configureren voor de volgende beurt.

### <a name="types"></a>Typen
Aangepaste opdrachten ondersteunen de volgende regel Categorieën:

* **Voltooiings regels**: deze regels moeten worden uitgevoerd bij het volt ooien van de opdracht. Alle regels die in deze sectie zijn geconfigureerd waarvoor de voor waarden waar zijn, worden uitgevoerd. 
* **Interactie regels**: deze regels kunnen worden gebruikt voor het configureren van aanvullende aangepaste validaties, bevestigingen en een correctie met één stap, of voor het uitvoeren van andere aangepaste logica voor het dialoog venster. Interactie regels worden op elke beurt geëvalueerd in de verwerking en kunnen worden gebruikt voor het activeren van de voltooiings regels.

De verschillende acties die zijn geconfigureerd als onderdeel van een regel, worden uitgevoerd in de volg orde waarin ze worden weer gegeven in de portal voor ontwerpen.

### <a name="conditions"></a>Voorwaarden
Voor waarden zijn de vereisten waaraan moet worden voldaan om een regel uit te voeren. Voor waarden voor regels kunnen van de volgende typen zijn:

* **Parameter waarde is gelijk aan**: de waarde van de geconfigureerde para meter is gelijk aan een specifieke waarde.
* **Geen parameter waarde**: de geconfigureerde para meters mogen geen waarde hebben.
* **Vereiste para meters**: de geconfigureerde para meter heeft een waarde.
* **Alle vereiste para meters**: alle para meters die zijn gemarkeerd als vereist, hebben een waarde.
* **Bijgewerkte para meters**: een of meer parameter waarden zijn bijgewerkt als gevolg van het verwerken van de huidige invoer (utterance of activiteit).
* De **bevestiging is voltooid**: de invoer-utterance of-activiteit is geslaagd voor de bevestiging (Ja).
* **Bevestiging is geweigerd**: de invoer-utterance of-activiteit is geen geslaagde bevestiging (Nee).
* De **vorige opdracht moet worden bijgewerkt**: dit probleem wordt gebruikt in gevallen waarin u een negatie bevestiging wilt opvangen en een update. Achter de schermen wordt deze voor waarde geconfigureerd voor wanneer de dialoog engine een negatieve bevestiging detecteert waarbij de intentie hetzelfde is als de vorige beurt en de gebruiker heeft gereageerd op een update.

### <a name="actions"></a>Acties
* **Spraak antwoord verzenden**: een antwoord verzenden naar de client.
* **Waarde van para meter bijwerken**: werk de waarde van een opdracht parameter bij naar een opgegeven waarde.
* **Parameter waarde wissen**: wis de waarde van de opdracht parameter.
* **Web-eind punt aanroepen**: een aanroep naar een webeindpunt maken.
* **Activiteit verzenden naar client**: een aangepaste activiteit verzenden naar de client.

### <a name="expectations"></a>Uiteen
Verwachtingen worden gebruikt voor het configureren van hints voor de verwerking van de volgende gebruikers invoer. De volgende typen worden ondersteund:

* **Verwachte bevestiging van de gebruiker**: deze verwachting geeft aan dat de toepassing een bevestiging verwacht (Ja/Nee) voor de volgende invoer van de gebruiker.
* De **verwachte para meter (s) van de gebruiker worden verwacht**: met deze verwachting worden een of meer opdracht parameters opgegeven die de toepassing van de gebruikers invoer verwacht.

### <a name="post-execution-state"></a>Status na uitvoering
De status na uitvoering is de status van het dialoog venster na het verwerken van de huidige invoer (utterance of activiteit). Het gaat om de volgende typen:

* **Huidige status blijven**: alleen huidige status blijven.
* **Voltooi de opdracht**: Voltooi de opdracht en er worden geen aanvullende regels van de opdracht verwerkt.
* **Voltooiings regels uitvoeren**: Voer alle geldige voltooiings regels uit.
* **Wachten op invoer** van de gebruiker: wacht op de volgende invoer van de gebruiker.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bekijk de voor beelden op GitHub](https://aka.ms/speech/cc-samples)
