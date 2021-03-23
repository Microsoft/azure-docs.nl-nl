---
title: Gebeurtenis aggregatie (preview-versie)
description: Met Defender voor IoT-beveiligings agenten worden gegevens-en systeem gebeurtenissen van uw lokale apparaat verzameld en worden de gegevens naar de Azure-Cloud verzonden voor verwerking en analyse.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: c0280e97549009a1e4911c072a7a8ec052684b4e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779321"
---
# <a name="event-aggregation-preview"></a>Gebeurtenis aggregatie (preview-versie)

Met Defender voor IoT-beveiligings agenten worden gegevens-en systeem gebeurtenissen van uw lokale apparaat verzameld en worden de gegevens naar de Azure-Cloud verzonden voor verwerking en analyse. De Defender voor IoT micro-agent verzamelt veel soorten apparaatfuncties, waaronder nieuwe processen, en alle nieuwe verbindings gebeurtenissen. Het nieuwe proces en de nieuwe verbindings gebeurtenissen kunnen regel matig worden uitgevoerd op een apparaat binnen een seconde. Deze mogelijkheid is belang rijk voor een uitgebreide beveiliging, maar het aantal berichten dat door beveiligings agenten kan worden verzonden, kan snel aan de slag gaan of uw IoT Hub quotum en kosten limieten overschrijden. Deze gebeurtenissen bevatten niettemin zeer waardevolle beveiligings informatie die essentieel is voor het beveiligen van uw apparaat. 

Om het extra quotum en de kosten te verminderen en uw apparaten te beveiligen, worden met Defender voor IoT-agents deze typen gebeurtenissen geaggregeerd: 

- ProcessCreate (alleen Linux) 

- ConnectionCreate (alleen Azure RTO'S) 

## <a name="how-does-event-aggregation-work"></a>Hoe werkt gebeurtenis aggregatie? 

Defender voor IoT-agents statistische gebeurtenissen voor de interval periode of het tijd venster. Zodra de interval periode is verstreken, verzendt de agent de geaggregeerde gebeurtenissen naar de Azure-Cloud voor verdere analyse. De geaggregeerde gebeurtenissen worden in het geheugen opgeslagen totdat ze naar de Azure-Cloud worden verzonden. 

Wanneer de agent een identieke gebeurtenis in het geheugen verzamelt, wordt het aantal treffers van deze specifieke gebeurtenis door de agent verhoogd, om het geheugen gebruik van de agent te verminderen. Wanneer het aggregatie tijd venster wordt door gegeven, verzendt de agent het aantal treffers van elk type gebeurtenis dat heeft plaatsgevonden. Gebeurtenis aggregatie is simpelweg de aggregatie van de treffer aantallen van elk verzameld type gebeurtenis. 

## <a name="process-events"></a>Evenementen verwerken 

Proces gebeurtenissen worden momenteel alleen ondersteund in Linux-besturings systemen. 

Proces gebeurtenissen worden beschouwd als identiek als de *opdracht regel* en de  *gebruikers-id*   identiek zijn. 

De standaard buffer voor proces gebeurtenissen is 32 processen, waarna de buffer wordt gerecycled en de oudste proces gebeurtenissen worden verwijderd om ruimte te maken voor nieuwe proces gebeurtenissen.  

## <a name="network-connection-events"></a>Netwerk verbindings gebeurtenissen 

Netwerk verbindings gebeurtenissen worden momenteel alleen ondersteund in azure RTO'S. 

Netwerk verbindings gebeurtenissen worden beschouwd als identiek wanneer de *lokale poort*, de  *externe poort*, het  *Transport Protocol*, het  *lokale adres* en het  *externe adres* identiek zijn. 

De standaard buffer voor netwerk verbindings gebeurtenissen is 64. Er worden geen nieuwe netwerk gebeurtenissen in de cache opgeslagen tot de volgende verzamelings cyclus. Er wordt een waarschuwing weer gegeven om de cache grootte te verg Roten.

## <a name="next-steps"></a>Volgende stappen

Controleer uw [Defender voor IOT-beveiligings waarschuwingen](concept-security-alerts.md).
