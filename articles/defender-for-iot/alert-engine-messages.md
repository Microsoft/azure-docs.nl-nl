---
title: Waarschuwings typen en-beschrijvingen
description: Bekijk de beschrijvingen van Defender voor IoT-waarschuwingen.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 4/8/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9ef7aa388d0f25adcafec1cb4a5b38dcfb8597a1
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210462"
---
# <a name="alert-types-and-descriptions"></a>Waarschuwings typen en-beschrijvingen

In dit artikel worden alle waarschuwings typen beschreven die kunnen worden gegenereerd van de Defender voor IoT-engines. Waarschuwingen worden weer gegeven in het venster waarschuwingen, waarmee u de waarschuwings gebeurtenis kunt beheren. 

## <a name="policy-engine-alerts"></a>Waarschuwingen van beleid-engine

Met waarschuwingen over de beleids engine worden afwijkingen van het geleerde basislijn netwerk gedrag beschreven.

| Titel  | Beschrijving | Severity |
|--|--|--|
| Abnormaal gebruik van MAC-adressen | Er is een nieuw bron apparaat gedetecteerd op het netwerk, maar dit is niet geautoriseerd. | Secundair |
| Beckhoff-software gewijzigd | De firmware is bijgewerkt op een bron apparaat. Dit kan geautoriseerde activiteit zijn, bijvoorbeeld een geplande onderhouds procedure. | Primair |
| Kan de data base niet aanmelden | Er is een mislukte aanmeldings poging van een bron apparaat naar een doel server gedetecteerd. Dit kan het resultaat zijn van een menselijke fout, maar kan ook duiden op een kwaad aardige poging om de server of gegevens erop te manipuleren. | Primair |
| Versie van Emerson ROC firmware gewijzigd | De firmware is bijgewerkt op een bron apparaat. Dit kan geautoriseerde activiteit zijn, bijvoorbeeld een geplande onderhouds procedure. | Primair |
| Extern adres binnen het netwerk communiceert met Internet | Een bron apparaat dat is gedefinieerd als onderdeel van uw netwerk, communiceert met Internet adressen. De bron is niet gemachtigd om te communiceren met Internet adressen. | Kritiek |
| Het veld apparaat is onverwachts gedetecteerd | Er is een nieuw bron apparaat gedetecteerd op het netwerk, maar dit is niet geautoriseerd. | Primair |
| Wijziging in firmware gedetecteerd | De firmware is bijgewerkt op een bron apparaat. Dit kan geautoriseerde activiteit zijn, bijvoorbeeld een geplande onderhouds procedure. | Primair |
| Firmware versie gewijzigd | De firmware is bijgewerkt op een bron apparaat. Dit kan geautoriseerde activiteit zijn, bijvoorbeeld een geplande onderhouds procedure. | Primair |
| Een niet-geautoriseerde bewerking Foxboro | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| FTP-aanmelding mislukt | Er is een mislukte aanmeldings poging van een bron apparaat naar een doel server gedetecteerd. Dit kan het resultaat zijn van een menselijke fout, maar kan ook duiden op een kwaad aardige poging om de server of gegevens erop te manipuleren. | Primair |
| Functie code verhoogde ongeautoriseerde uitzonde ring | Een bron apparaat (slave) heeft een uitzonde ring geretourneerd naar een doel apparaat (Master). | Primair |
| Instellingen voor GOOSE-bericht type | Het bericht (geïdentificeerd door de protocol-ID) instellingen is gewijzigd op een bron apparaat. | Waarschuwing |
| Versie van Honeywell-firmware gewijzigd | De firmware is bijgewerkt op een bron apparaat. Dit kan geautoriseerde activiteit zijn, bijvoorbeeld een geplande onderhouds procedure. | Primair |
| Ongeldige HTTP-communicatie | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Internet toegang gedetecteerd | Een bron apparaat dat is gedefinieerd als onderdeel van uw netwerk, communiceert met Internet adressen. De bron is niet gemachtigd om te communiceren met Internet adressen. | Primair |
| Mitsubishi firmware-versie gewijzigd | De firmware is bijgewerkt op een bron apparaat. Dit kan geautoriseerde activiteit zijn, bijvoorbeeld een geplande onderhouds procedure. | Primair |
| Schending van Modbus-adres bereik | Een Master-apparaat heeft toegang aangevraagd tot een nieuw slave-geheugen adres. | Primair |
| Versie van Modbus-firmware gewijzigd | De firmware is bijgewerkt op een bron apparaat. Dit kan geautoriseerde activiteit zijn, bijvoorbeeld een geplande onderhouds procedure. | Primair |
| Nieuwe activiteit gedetecteerd-overschrijvings klasse | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-overschrijvings klasse-service | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-overschrijving PCCC opdracht | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-overschrijvings symbool | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-EtherNet/IP-I/O-verbinding | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-EtherNet/IP-protocol opdracht | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-GSM-bericht code | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-LonTalk-opdracht codes | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe poort detectie | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Waarschuwing |
| Nieuwe activiteit gedetecteerd-LonTalk-netwerk variabele | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-Ovation-gegevens aanvraag | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-lezen/schrijven opdracht (AMS index groep) | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-lezen/schrijven opdracht (offset van AMS-index) | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-niet-geautoriseerd DeltaV-bericht type | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-niet-geautoriseerde DeltaV ROC-bewerking | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-niet-geautoriseerd RPC-bericht type | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-niet-geautoriseerde RPC-procedure aanroep | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-met behulp van AMS-protocol opdracht | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-met Siemens SICAM-opdracht | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-met behulp van SuiteLink-protocol opdracht | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-SuiteLink-protocol sessies gebruiken | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd-met behulp van Yokogawa VNetIP opdracht | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Nieuwe Asset gedetecteerd | Er is een nieuw bron apparaat gedetecteerd op het netwerk, maar dit is niet geautoriseerd. | Primair |
| Nieuwe configuratie voor LLDP-apparaten | Er is een nieuw bron apparaat gedetecteerd op het netwerk, maar dit is niet geautoriseerd. | Primair |
| Nieuwe poort detectie | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Waarschuwing |
| Omron VINNEN niet-gemachtigde opdracht | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| S7 plus PLC firmware gewijzigd | De firmware is bijgewerkt op een bron apparaat. Dit kan geautoriseerde activiteit zijn, bijvoorbeeld een geplande onderhouds procedure. | Primair |
| Instellingen voor voorbeeld bericht typen van waarden | Het bericht (geïdentificeerd door de protocol-ID) instellingen is gewijzigd op een bron apparaat. | Waarschuwing |
| Vermoeden van ongeldige integriteits scan | Er is een scan gevonden op een DNP3-bron apparaat (outstation). Deze scan is niet geautoriseerd als geleerd verkeer op uw netwerk. | Primair |
| Opdracht Toshiba computer link Unauthorized | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Secundair |
| Niet-geautoriseerde ABB Totalflow-bestands bewerking | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Registratie bewerking ABB Totalflow niet toegestaan | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Onbevoegde toegang tot het S7-gegevens blok van Siemens | Een bron apparaat heeft geprobeerd toegang te krijgen tot een bron op een ander apparaat. Een poging tot het verkrijgen van toegang tot deze bron tussen deze twee apparaten is niet geautoriseerd als geleerd verkeer op uw netwerk. | Waarschuwing |
| Onbevoegde toegang tot Siemens S7 plus object | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Onbevoegde toegang tot de Wonderware-tag | Een bron apparaat heeft geprobeerd toegang te krijgen tot een bron op een ander apparaat. Een poging tot het verkrijgen van toegang tot deze bron tussen deze twee apparaten is niet geautoriseerd als geleerd verkeer op uw netwerk. | Primair |
| Niet-geautoriseerde BACNet-object toegang | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde BACNet-route | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde database aanmelding | Er is een aanmeldings poging tussen een bron-client en de doel server gedetecteerd. De communicatie tussen deze apparaten is niet geautoriseerd als geleerd verkeer op uw netwerk. | Primair |
| Niet-geautoriseerde database bewerking | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde Emerson ROC-bewerking | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde bestands toegang tot GE SRTP | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerd GE SRTP protocol opdracht | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde GE SRTP systeem geheugen bewerking | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde HTTP-activiteit | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde HTTP-server | Er is een niet-geautoriseerde toepassing gedetecteerd op een bron apparaat. De toepassing is niet geautoriseerd als een geleerde toepassing in uw netwerk. | Primair |
| Niet-geautoriseerde HTTP SOAP-actie | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde HTTP-gebruikers agent | Er is een niet-geautoriseerde toepassing gedetecteerd op een bron apparaat. De toepassing is niet geautoriseerd als een geleerde toepassing in uw netwerk. | Primair |
| Niet-geautoriseerde Internet connectiviteit gedetecteerd | Een bron apparaat dat is gedefinieerd als onderdeel van uw netwerk, communiceert met Internet adressen. De bron is niet gemachtigd om te communiceren met Internet adressen. | Kritiek |
| Niet-geautoriseerde Mitsubishi MELSEC-opdracht | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde MMS-programma toegang | Een bron apparaat heeft geprobeerd toegang te krijgen tot een bron op een ander apparaat. Een poging tot het verkrijgen van toegang tot deze bron tussen deze twee apparaten is niet geautoriseerd als geleerd verkeer op uw netwerk. | Primair |
| Niet-geautoriseerde MMS-service | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde multi cast/broadcast-verbinding | Er is een multi cast/broadcast-verbinding gedetecteerd tussen een bron apparaat en andere apparaten. Multi cast/broadcast-communicatie is niet toegestaan. | Kritiek |
| Niet-gemachtigde naam query | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde OPC UA-activiteit | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde OPC UA-aanvraag/antwoord | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Er is een niet-geautoriseerde bewerking gedetecteerd door een door de gebruiker gedefinieerde regel | Verkeer is gedetecteerd tussen twee apparaten. Deze activiteit is niet-geautoriseerd op basis van een aangepaste waarschuwings regel die door een gebruiker is gedefinieerd. | Primair |
| Niet-geautoriseerde PLC-configuratie lezen | Het bron apparaat is niet gedefinieerd als een programmeer apparaat, maar er is een lees-en schrijf bewerking uitgevoerd op een doel controller. Wijzigingen in het Program meren moeten alleen worden uitgevoerd door apparaten. Er is mogelijk een programmeer toepassing op dit apparaat geïnstalleerd. | Waarschuwing |
| Niet-geautoriseerde PLC-configuratie schrijven | Het bron apparaat heeft een opdracht verzonden om het programma van een doel controller te lezen/schrijven. Deze activiteit is niet eerder gezien. | Primair |
| Niet-geautoriseerde PLC-Program Ma's uploaden | Het bron apparaat heeft een opdracht verzonden om het programma van een doel controller te lezen/schrijven. Deze activiteit is niet eerder gezien. | Primair |
| Niet-geautoriseerde PLC-programmering | Het bron apparaat is niet gedefinieerd als een programmeer apparaat, maar er is een lees-en schrijf bewerking uitgevoerd op een doel controller. Wijzigingen in het Program meren moeten alleen worden uitgevoerd door apparaten. Er is mogelijk een programmeer toepassing op dit apparaat geïnstalleerd. | Kritiek |
| Niet-geautoriseerd bijzonderings frame type | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde SAIA S-Bus opdracht | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-gemachtigde Siemens S7-uitvoering van de beheer functie | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-gemachtigde Siemens S7 uitvoering van door de gebruiker gedefinieerde functie | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde Siemens S7 plus-blok toegang | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-gemachtigde Siemens S7 plus-bewerking | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde SMB-aanmelding | Er is een aanmeldings poging tussen een bron-client en de doel server gedetecteerd. De communicatie tussen deze apparaten is niet geautoriseerd als geleerd verkeer op uw netwerk. | Primair |
| Niet-geautoriseerde SNMP-bewerking | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde SSH-toegang | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-geautoriseerd Windows-proces | Er is een niet-geautoriseerde toepassing gedetecteerd op een bron apparaat. De toepassing is niet geautoriseerd als een geleerde toepassing in uw netwerk. | Primair |
| Niet-geautoriseerde Windows-service | Er is een niet-geautoriseerde toepassing gedetecteerd op een bron apparaat. De toepassing is niet geautoriseerd als een geleerde toepassing in uw netwerk. | Primair |
| Er is een niet-geautoriseerde bewerking gedetecteerd door een door de gebruiker gedefinieerde regel | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meter schendt een door de gebruiker gedefinieerde regel | Primair |
| Niet-toegestane Modbus Schneider elektrische extensie | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-toegestaan gebruik van ASDU-typen | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-toegestaan gebruik van DNP3-functie code | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |
| Niet-toegestaan gebruik van interne indicatie (IIN) | Een DNP3-bron apparaat (outstation) heeft een interne indicatie (IIN) gerapporteerd die geen geleerd verkeer op uw netwerk heeft geautoriseerd. | Primair |
| Niet-toegestaan gebruik van Modbus-functie code | Er zijn nieuwe verkeers parameters gedetecteerd. Deze combi natie van para meters is niet geautoriseerd als geleerd verkeer op uw netwerk. De volgende combi natie is niet geautoriseerd. | Primair |

## <a name="anomaly-engine-alerts"></a>Anomalie-engine waarschuwingen

| Titel | Beschrijving | Severity |
|--|--|--|
| Abnormaal uitzonderings patroon in slave | Er is een uitzonderlijk aantal fouten gedetecteerd op een bron apparaat. Dit kan het gevolg zijn van een operationeel probleem. | Secundair |
| Abnormale HTTP-header lengte | Het bron apparaat heeft een abnormaal bericht verzonden. Dit kan duiden op een poging om het doel apparaat aan te vallen. | Kritiek |
| Abnormaal aantal para meters in HTTP-header | Het bron apparaat heeft een abnormaal bericht verzonden. Dit kan duiden op een poging om het doel apparaat aan te vallen. | Kritiek |
| Abnormaal periodiek gedrag in communicatie kanaal | Er is een wijziging in de frequentie van de communicatie tussen de bron-en doel apparaten gedetecteerd. | Secundair |
| Abnormale beëindiging van toepassingen | Er zijn een buitensporig aantal Stop opdrachten op een bron apparaat gedetecteerd. Dit kan het gevolg zijn van een operationeel probleem of een poging om het apparaat te manipuleren. | Primair |
| Afwijking van abnormaal verkeer | Er is een abnormale band breedte gedetecteerd op een kanaal. De band breedte lijkt aanzienlijk lager/hoger te zijn dan eerder gedetecteerd. Werk met de widget totale band breedte voor meer informatie. | Waarschuwing |
| Abnormale verkeers bandbreedte tussen apparaten | Er is een abnormale band breedte gedetecteerd op een kanaal. De band breedte lijkt aanzienlijk lager/hoger te zijn dan eerder gedetecteerd. Werk met de widget totale band breedte voor meer informatie. | Waarschuwing |
| Adres scan gedetecteerd | Er is een bron apparaat aangetroffen bij het scannen van netwerk apparaten. Dit apparaat is niet geautoriseerd als een netwerk scanapparaat. | Kritiek |
| ARP-adres scan gedetecteerd | Er is met een bron apparaat gedetecteerd dat netwerk apparaten worden gescand met ARP (Address Resolution Protocol). Het adres van dit apparaat is niet als geldig ARP-scan adres toegestaan. | Kritiek |
| ARP-adres scan gedetecteerd | Er is met een bron apparaat gedetecteerd dat netwerk apparaten worden gescand met ARP (Address Resolution Protocol). Het adres van dit apparaat is niet als geldig ARP-scan adres toegestaan. | Kritiek |
| ARP-Spoofing | Er is een abnormaal aantal pakketten gedetecteerd in het netwerk. Dit kan duiden op een aanval, bijvoorbeeld een ARP-vervalsings aanval of een ICMP-Flooding. | Waarschuwing |
| Buitensporige aanmeldings pogingen | Er is een bron apparaat waargenomen tijdens het uitvoeren van buitensporige aanmeldings pogingen naar een doel server. Dit kan een beveiligings aanval zijn. De server kan worden aangetast door een schadelijke actor. | Kritiek |
| Uitzonderlijk aantal sessies | Er is een bron apparaat waargenomen tijdens het uitvoeren van buitensporige aanmeldings pogingen naar een doel server. Dit kan een beveiligings aanval zijn. De server kan worden aangetast door een schadelijke actor. | Kritiek |
| Buitensporige start frequentie van een outstation | Er zijn een buitensporig aantal opdrachten voor opnieuw opstarten gedetecteerd op een bron apparaat. Dit kan het gevolg zijn van een operationeel probleem of een poging om het apparaat te manipuleren. | Primair |
| Buitensporige SMB-aanmeldings pogingen | Er is een bron apparaat waargenomen tijdens het uitvoeren van buitensporige aanmeldings pogingen naar een doel server. Dit kan een beveiligings aanval zijn. De server kan worden aangetast door een schadelijke actor. | Kritiek |
| ICMP-Flooding | Er is een abnormaal aantal pakketten gedetecteerd in het netwerk. Dit kan duiden op een aanval, bijvoorbeeld een ARP-vervalsings aanval of een ICMP-Flooding. | Waarschuwing |
| Ongeldige inhoud voor HTTP-header | Het bron apparaat heeft een ongeldige aanvraag gestart. | Kritiek |
| Inactief communicatie kanaal | Een communicatie kanaal tussen twee apparaten was inactief tijdens een periode waarin de activiteit meestal wordt gezien. Dit kan erop wijzen dat het programma waarmee dit verkeer wordt gegenereerd, is gewijzigd of dat het programma niet beschikbaar is. U wordt aangeraden de configuratie van het geïnstalleerde programma te controleren en te controleren of het correct is geconfigureerd. | Waarschuwing |
| Adres scan voor lange duur gedetecteerd | Er is een bron apparaat aangetroffen bij het scannen van netwerk apparaten. Dit apparaat is niet geautoriseerd als een netwerk scanapparaat. | Kritiek |
| Poging tot wacht woord raden gedetecteerd | Er is een bron apparaat waargenomen tijdens het uitvoeren van buitensporige aanmeldings pogingen naar een doel server. Dit kan een beveiligings aanval zijn. De server kan worden aangetast door een schadelijke actor. | Kritiek |
| PLC-scan gedetecteerd | Er is een bron apparaat aangetroffen bij het scannen van netwerk apparaten. Dit apparaat is niet geautoriseerd als een netwerk scanapparaat. | Kritiek |
| Poort controle gedetecteerd | Er is een bron apparaat aangetroffen bij het scannen van netwerk apparaten. Dit apparaat is niet geautoriseerd als een netwerk scanapparaat. | Kritiek |
| Onverwachte bericht lengte | Het bron apparaat heeft een abnormaal bericht verzonden. Dit kan duiden op een poging om het doel apparaat aan te vallen. | Kritiek |
| Onverwacht verkeer voor de standaard poort | Verkeer is op een apparaat gedetecteerd met een poort die is gereserveerd voor een ander protocol. | Primair |

## <a name="protocol-violation-engine-alerts"></a>Engine-waarschuwingen voor protocol overtreding

| Titel | Beschrijving | Severity |
|--|--|--|
| Overmatige ongeldige pakketten tijdens één sessie | Een abnormaal aantal ongeldige pakketten dat is verzonden van het bron apparaat naar het doel apparaat. Dit kan duiden op foutieve communicatie of een poging om het doel apparaat te manipuleren. | Primair |
| Firmware-update | Een bron apparaat heeft een opdracht verzonden om firmware op een doel apparaat bij te werken. Controleer of recente programmeer-, configuratie-en firmware-upgrades op het doel apparaat geldig zijn. | Waarschuwing |
| Functie code wordt niet ondersteund door outstation | Het doel apparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Ongeldig BACNet-bericht | Het bron apparaat heeft een ongeldige aanvraag gestart. | Primair |
| Ongeldige verbindings poging op poort 0 | Een bron apparaat heeft geprobeerd verbinding te maken met het doel apparaat op poort nummer nul (0). Voor TCP is poort 0 gereserveerd en kan niet worden gebruikt. Voor UDP is de poort optioneel en de waarde 0 betekent geen poort. Er is meestal geen service op een systeem die luistert op poort 0. Deze gebeurtenis kan duiden op een poging om het doel apparaat aan te vallen of om aan te geven dat een toepassing onjuist is geprogrammeerd. | Secundair |
| Ongeldige DNP3-bewerking | Het bron apparaat heeft een ongeldige aanvraag gestart. | Primair |
| Ongeldige MODBUS-bewerking (uitzonde ring gemeld door hoofd) | Het bron apparaat heeft een ongeldige aanvraag gestart. | Primair |
| Ongeldige MODBUS-bewerking (functie code is nul) | Het bron apparaat heeft een ongeldige aanvraag gestart. | Primair |
| Ongeldige Protocol versie | Het bron apparaat heeft een ongeldige aanvraag gestart. | Primair |
| Onjuiste para meter verzonden naar outstation | Het doel apparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Initiëren van een verouderde functie code (gegevens initialiseren) | Het bron apparaat heeft een ongeldige aanvraag gestart. | Secundair |
| Initiëren van een verouderde functie code (configuratie opslaan) | Het bron apparaat heeft een ongeldige aanvraag gestart. | Secundair |
| Master heeft een Application Layer-bevestiging aangevraagd | Het bron apparaat heeft een ongeldige aanvraag gestart. | Waarschuwing |
| Modbus-uitzonde ring | Een bron apparaat (slave) heeft een uitzonde ring geretourneerd naar een doel apparaat (Master). | Primair |
| Het slave-apparaat heeft een ongeldig ASDU-type ontvangen | Het doel apparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Het slave-apparaat heeft een ongeldige opdracht voor verzen ding ontvangen | Het doel apparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Apparaat voor slave heeft illegaal algemeen adres ontvangen | Het doel apparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Het apparaat dat een slave heeft gekregen, heeft een ongeldige gegevens adres parameter ontvangen | Het doel apparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Het slave-apparaat heeft een ongeldige gegevens waarde-para meter ontvangen | Het doel apparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Het slave-apparaat heeft een ongeldige functie code ontvangen | Het doel apparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Slave-apparaat heeft ongeldig informatie object adres ontvangen | Het doel apparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Onbekend object verzonden naar outstation | Het doel apparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Gebruik van een gereserveerde functie code | Het bron apparaat heeft een ongeldige aanvraag gestart. | Primair |
| Gebruik van onjuiste indeling op outstation | Het bron apparaat heeft een ongeldige aanvraag gestart. | Waarschuwing |
| Gebruik van gereserveerde status vlaggen (IIN) | Een DNP3-bron apparaat (outstation) heeft de gereserveerde interne indicator 2,6 gebruikt. Het is raadzaam om de configuratie van het apparaat te controleren. | Waarschuwing |

## <a name="malware-engine-alerts"></a>Malware-engine waarschuwingen

| Titel | Beschrijving| Severity |
|--|--|--|
| Verbindings poging met bekend schadelijk IP-adres | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Ongeldig SMB-bericht (DoublePulsar-back-implantatie) | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Aanvraag voor schadelijke domein naam | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Test bestand malware gedetecteerd-EICAR AV geslaagd | Er is een EICAR AV-test bestand aangetroffen in het verkeer tussen twee apparaten. Het bestand is geen malware. Het wordt gebruikt om te controleren of de antivirus software correct is geïnstalleerd. laat zien wat er gebeurt als er een virus wordt gevonden en controleer interne procedures en reacties wanneer een virus wordt gevonden. Antivirus software moet EICAR detecteren alsof het een echt virus betreft. | Primair |
| Vermoeden van Conficker-malware | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Vermoeden van een denial of service-aanval | Een bron apparaat heeft geprobeerd een uitzonderlijk groot aantal nieuwe verbindingen met een doel apparaat te initiëren. Dit kan een DOS-aanval (Denial of service) zijn op het doel apparaat en kan de functionaliteit van het apparaat, de prestaties en de beschik baarheid van de service onderbreken of onherstelbare fouten veroorzaken. | Kritiek |
| Vermoeden van schadelijke activiteiten | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Vermoeden van schadelijke activiteiten (BlackEnergy) | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteiten (DarkComet) | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteiten (Duqu) | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteiten (vlam) | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteiten (metX) | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteiten (Karagany) | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteiten (LightsOut) | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteiten (naam Query's) | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Vermoeden van schadelijke activiteiten (Poison-Ivy) | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteiten (Regin) | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteiten (Stuxnet) | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteiten (WannaCry) | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Vermoeden van NotPetya malware-ongeldige SMB-para meters gedetecteerd | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van NotPetya malware-ongeldige SMB-trans actie gedetecteerd | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden dat externe code wordt uitgevoerd met PsExec | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Vermoeden van extern Windows-Service beheer | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Verdacht uitvoerbaar bestand gedetecteerd op eind punt | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Verdacht verkeer gedetecteerd | Er is een verdachte netwerk activiteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval die gebruikmaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |

## <a name="operational-engine-alerts"></a>Waarschuwingen van operationele engine

| Titel | Beschrijving | Severity |
|--|--|--|
| Er is een S7 stop PLC-opdracht verzonden | Het bron apparaat heeft een stop opdracht naar een doel controller verzonden. De controller stopt met werken totdat een start opdracht wordt verzonden. | Waarschuwing |
| De BACNet-bewerking is mislukt | Een server heeft een fout code geretourneerd. Dit duidt op een server fout of een ongeldige aanvraag van een client. | Primair |
| Ongeldige status van MMS-apparaat | Een VMD-apparaat (MMS Virtual Manufacturing) heeft een status bericht verzonden. Het bericht geeft aan dat de server mogelijk niet juist is geconfigureerd, gedeeltelijk operationeel of niet operationeel is. | Primair |
| De configuratie van het apparaat wijzigen | Er is een configuratie wijziging gedetecteerd op een bron apparaat. | Secundair |
| Buffer overloop bij doorlopende gebeurtenissen op outstation | Er is een buffer overloop gebeurtenis gedetecteerd op een bron apparaat. De gebeurtenis kan leiden tot beschadiging van gegevens, het programma loopt vast of de uitvoering van schadelijke code. | Primair |
| Controller opnieuw instellen | Een bron apparaat heeft een reset-opdracht naar een doel controller verzonden. De controller is tijdelijk gestopt en automatisch opnieuw gestart. | Waarschuwing |
| Controller stoppen | Het bron apparaat heeft een stop opdracht naar een doel controller verzonden. De controller stopt met werken totdat een start opdracht wordt verzonden. | Waarschuwing |
| Apparaat kan geen dynamisch IP-adres ontvangen | Het bron apparaat is geconfigureerd voor het ontvangen van een dynamisch IP-adres van een DHCP-server, maar er is geen adres ontvangen. Dit duidt op een configuratie fout op het apparaat of op een operationele fout in de DHCP-server. Het is raadzaam om de netwerk beheerder van het incident op de hoogte te stellen | Primair |
| Het apparaat wordt mogelijk niet meer verbonden (reageert niet) | Een bron apparaat reageert niet op een opdracht die ernaar is verzonden. De verbinding is mogelijk verbroken toen de opdracht werd verzonden. | Primair |
| Aanvraag voor EtherNet/IP-overschrijvings service is mislukt | Een server heeft een fout code geretourneerd. Dit duidt op een server fout of een ongeldige aanvraag van een client. | Primair |
| Opdracht voor EtherNet/IP-Encapsulation-protocol is mislukt | Een server heeft een fout code geretourneerd. Dit duidt op een server fout of een ongeldige aanvraag van een client. | Primair |
| Overloop gebeurtenis buffer in outstation | Er is een buffer overloop gebeurtenis gedetecteerd op een bron apparaat. De gebeurtenis kan leiden tot beschadiging van gegevens, het programma loopt vast of de uitvoering van schadelijke code. | Primair |
| De verwachte back-upbewerking is niet uitgevoerd | De verwachte activiteit voor back-up/bestands overdracht is niet uitgevoerd tussen twee apparaten. Dit kan duiden op fouten in het proces voor back-up/bestands overdracht. | Primair |
| GE SRTP-opdracht fout | Een server heeft een fout code geretourneerd. Dit duidt op een server fout of een ongeldige aanvraag van een client. | Primair |
| De opdracht GE SRTP stop PLC is verzonden | Het bron apparaat heeft een stop opdracht naar een doel controller verzonden. De controller stopt met werken totdat een start opdracht wordt verzonden. | Waarschuwing |
| GOOSE-besturings blok vereist verdere configuratie | Een bron apparaat heeft een GOOSE-bericht verzonden dat aangeeft dat het apparaat moet worden geprovisied. Dit betekent dat het GOOSE-besturings blok verdere configuratie-en GOOSE-berichten vereist die gedeeltelijk of volledig niet-operationeel zijn. | Primair |
| De configuratie van de GOOSE-gegevensset is gewijzigd | Er is een bericht (geïdentificeerd door de protocol-ID) gegevensset gewijzigd op een bron apparaat. Dit betekent dat het apparaat een andere gegevensset voor dit bericht zal rapporteren. | Waarschuwing |
| Onverwachte status van Honeywell-controller | Een Honeywell-controller heeft een onverwacht diagnostisch bericht verzonden dat aangeeft dat de status is gewijzigd. | Waarschuwing |
| HTTP-client fout | Het bron apparaat heeft een ongeldige aanvraag gestart. | Waarschuwing |
| Ongeldig IP-adres | Het systeem heeft verkeer gedetecteerd tussen een bron apparaat en een IP-adres dat een ongeldig adres is. Dit kan duiden op een onjuiste configuratie of een poging om illegaal verkeer te genereren. | Secundair |
| Verificatie fout Master-Slave | Het verificatie proces tussen een DNP3-bron apparaat (Master) en een bestemmings apparaat (outstation) is mislukt. | Secundair |
| Aanvraag van MMS-service mislukt | Een server heeft een fout code geretourneerd. Dit duidt op een server fout of een ongeldige aanvraag van een client. | Primair |
| Geen verkeer gedetecteerd op sensor interface | Een sensor heeft het detecteren van netwerk verkeer op een netwerk interface gestopt. | Kritiek |
| OPC UA-server heeft een gebeurtenis gegenereerd waarvoor aandacht van de gebruiker is vereist | Een OPC UA-server heeft een gebeurtenis melding naar een client verzonden. Voor dit type gebeurtenis is de aandacht van de gebruiker vereist | Primair |
| OPC UA-service aanvraag mislukt | Een server heeft een fout code geretourneerd. Dit duidt op een server fout of een ongeldige aanvraag van een client. | Primair |
| Het station is opnieuw opgestart | Er is een koud opnieuw opstarten gedetecteerd op een bron apparaat. Dit betekent dat het apparaat fysiek is uitgeschakeld en opnieuw weer actief is. | Waarschuwing |
| Outstation wordt regel matig opnieuw opgestart | Er is een buitensporig aantal koud opnieuw opstarten gedetecteerd op een bron apparaat. Dit betekent dat het apparaat fysiek is uitgeschakeld en opnieuw een buitensporig aantal keer opnieuw wordt weer gegeven. | Secundair |
| De configuratie van het outstation is gewijzigd | Er is een configuratie wijziging gedetecteerd op een bron apparaat. | Primair |
| De beschadigde configuratie van het outstation is gedetecteerd | Dit DNP3-bron apparaat (outstation) heeft een beschadigde configuratie gerapporteerd. | Primair |
| De opdracht DCP kan niet worden uitgevoerd | Een server heeft een fout code geretourneerd. Dit duidt op een server fout of een ongeldige aanvraag van een client. | Primair |
| Fabrieks instellingen van apparaat verfijnen | Een bron apparaat heeft een fabrieks herstel opdracht verzonden naar een verfijnd doel apparaat. Met de opdracht reset worden de configuraties voor het verfijnen van apparaten gewist en wordt de bewerking gestopt. | Waarschuwing |
| RPC-bewerking mislukt | Een server heeft een fout code geretourneerd. Dit duidt op een server fout of een ongeldige aanvraag van een client. | Primair |
| De gegevensset configuratie van de voorbeeld waarden is gewijzigd | Er is een bericht (geïdentificeerd door de protocol-ID) gegevensset gewijzigd op een bron apparaat. Dit betekent dat het apparaat een andere gegevensset voor dit bericht zal rapporteren. | Waarschuwing |
| Onherstelbare fout van slave-apparaat | Er is een onherstelbare voorwaarde fout gedetecteerd op een bron apparaat. Dit soort fout geeft meestal aan dat er een hardwarestoring of fout is opgetreden bij het uitvoeren van een specifieke opdracht. | Primair |
| Vermoeden van hardwareproblemen in het outstation | Er is een onherstelbare voorwaarde fout gedetecteerd op een bron apparaat. Dit soort fout geeft meestal aan dat er een hardwarestoring of fout is opgetreden bij het uitvoeren van een specifieke opdracht. | Primair |
| Vermoeden van niet-reagerend MODBUS-apparaat | Een bron apparaat reageert niet op een opdracht die ernaar is verzonden. De verbinding is mogelijk verbroken toen de opdracht werd verzonden. | Secundair |
| Verkeer gedetecteerd op sensor interface | Een sensor heeft het detecteren van netwerk verkeer op een netwerk interface hervat. | Waarschuwing |

## <a name="next-steps"></a>Volgende stappen

U kunt [waarschuwings gebeurtenissen beheren](how-to-manage-the-alert-event.md).
Meer informatie over het [door sturen van waarschuwings gegevens](how-to-forward-alert-information-to-partners.md).
