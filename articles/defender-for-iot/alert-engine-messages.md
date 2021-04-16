---
title: Waarschuwingstypen en beschrijvingen
description: Bekijk beschrijvingen van Defender for IoT-waarschuwingen.
ms.date: 4/8/2021
ms.topic: how-to
ms.openlocfilehash: 483563b53a5849b0354986269568bc42b9124cc2
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477992"
---
# <a name="alert-types-and-descriptions"></a>Waarschuwingstypen en beschrijvingen

In dit artikel worden alle waarschuwingstypen beschreven die kunnen worden gegenereerd op basis van de Defender for IoT-engines. Waarschuwingen worden weergegeven in het venster Waarschuwingen, waarmee u de waarschuwingsgebeurtenis kunt beheren. 

## <a name="policy-engine-alerts"></a>Waarschuwingen van beleidsent engine

Waarschuwingen van beleidsent engine beschrijven gedetecteerde afwijkingen van het gedrag van de geleerde basislijn.

| Titel  | Beschrijving | Severity |
|--|--|--|
| Abnormaal gebruik van MAC-adressen | Er is een nieuw bronapparaat gedetecteerd in het netwerk, maar dit apparaat is niet geautoriseerd. | Secundair |
| Hadoe Software is gewijzigd | Firmware is bijgewerkt op een bronapparaat. Dit kan geautoriseerde activiteit zijn, bijvoorbeeld een geplande onderhoudsprocedure. | Primair |
| Aanmelden bij database is mislukt | Er is een mislukte aanmeldingspoging gedetecteerd vanaf een bronapparaat naar een doelserver. Dit kan het gevolg zijn van een menselijke fout, maar kan ook duiden op een kwaadwillende poging om de server of gegevens erop te compromitteerd. | Primair |
| Emerson ROC-firmwareversie gewijzigd | Firmware is bijgewerkt op een bronapparaat. Dit kan geautoriseerde activiteit zijn, bijvoorbeeld een geplande onderhoudsprocedure. | Primair |
| Extern adres in het netwerk dat wordt gecommuniceerd met internet | Een bronapparaat dat is gedefinieerd als onderdeel van uw netwerk communiceert met internetadressen. De bron is niet gemachtigd om te communiceren met internetadressen. | Kritiek |
| Veldapparaat onverwacht ontdekt | Er is een nieuw bronapparaat gedetecteerd in het netwerk, maar is niet geautoriseerd. | Primair |
| Firmwarewijziging gedetecteerd | Firmware is bijgewerkt op een bronapparaat. Dit kan geautoriseerde activiteit zijn, bijvoorbeeld een geplande onderhoudsprocedure. | Primair |
| Firmwareversie gewijzigd | Firmware is bijgewerkt op een bronapparaat. Dit kan geautoriseerde activiteit zijn, bijvoorbeeld een geplande onderhoudsprocedure. | Primair |
| Foxboro I/A Niet-geautoriseerde bewerking | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| FTP-aanmelding mislukt | Er is een mislukte aanmeldingspoging gedetecteerd van een bronapparaat naar een doelserver. Dit kan het gevolg zijn van een menselijke fout, maar kan ook duiden op een kwaadwillende poging om de server of gegevens erop te compromitteerd. | Primair |
| Functiecode heeft een niet-geautoriseerde uitzondering tot leven gebracht | Een bronapparaat (slave) heeft een uitzondering geretourneerd op een doelapparaat (master). | Primair |
| INSTELLINGEN VOOR HET MESSAGE Type VAN HET MESSAGE | Berichtinstellingen (geïdentificeerd op basis van protocol-id) zijn gewijzigd op een bronapparaat. | Waarschuwing |
| Firmwareversie van firmware van firmware van Firmware is gewijzigd | Firmware is bijgewerkt op een bronapparaat. Dit kan geautoriseerde activiteit zijn, bijvoorbeeld een geplande onderhoudsprocedure. | Primair |
| Onrechtmatige HTTP-communicatie | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Internettoegang gedetecteerd | Een bronapparaat dat is gedefinieerd als onderdeel van uw netwerk communiceert met internetadressen. De bron is niet gemachtigd om te communiceren met internetadressen. | Primair |
| De firmwareversie van de firmware is gewijzigd | Firmware is bijgewerkt op een bronapparaat. Dit kan geautoriseerde activiteit zijn, bijvoorbeeld een geplande onderhoudsprocedure. | Primair |
| Schending van modbus-adresbereik | Een hoofdapparaat heeft toegang aangevraagd tot een nieuw geheugenadres van een slave. | Primair |
| Modbus Firmware Version Changed | Firmware is bijgewerkt op een bronapparaat. Dit kan geautoriseerde activiteit zijn, bijvoorbeeld een geplande onderhoudsprocedure. | Primair |
| Nieuwe activiteit gedetecteerd - CIP-klasse | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd - CIP-klasseservice | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd - CIP PCCC-opdracht | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd - CIP-symbool | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd - EtherNet/IP I/O-verbinding | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd - EtherNet/IP Protocol-opdracht | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd - SMS-berichtcode | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd - LonTalk-opdrachtcodes | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe poortdetectie | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Waarschuwing |
| Nieuwe activiteit gedetecteerd - LonTalk-netwerkvariabele | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd - Aanvraag voor ovatiegegevens | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd - opdracht lezen/schrijven (AMS-indexgroep) | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd - opdracht lezen/schrijven (AMS-index offset) | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd - Niet-geautoriseerd DeltaV-berichttype | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd - Niet-geautoriseerde DeltaV ROC-bewerking | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd - Niet-geautoriseerd RPC-berichttype | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd - aanroepen van niet-geautoriseerde RPC-procedure | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd - ams-protocolopdracht gebruiken | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd - met behulp van de opdracht Siemens SICAM | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| New Activity Detected - Using Suitelink Protocol command (Nieuwe activiteit gedetecteerd- met behulp van de suitelinkprotocolopdracht) | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd - Suitelink Protocol-sessies gebruiken | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe activiteit gedetecteerd- met de opdracht Yokogawa VNetIP | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Nieuwe asset gedetecteerd | Er is een nieuw bronapparaat gedetecteerd in het netwerk, maar is niet geautoriseerd. | Primair |
| Nieuwe LLDP-apparaatconfiguratie | Er is een nieuw bronapparaat gedetecteerd in het netwerk, maar is niet geautoriseerd. | Primair |
| Nieuwe poortdetectie | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Waarschuwing |
| Opdracht omron FINS unauthorized | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| S7 Plus FIRMWARE changed (S7 Plus FIRMWARE gewijzigd) | Firmware is bijgewerkt op een bronapparaat. Dit kan geautoriseerde activiteit zijn, bijvoorbeeld een geplande onderhoudsprocedure. | Primair |
| Voorbeeldwaarden Instellingen voor berichttype | Berichtinstellingen (geïdentificeerd op basis van protocol-id) zijn gewijzigd op een bronapparaat. | Waarschuwing |
| Vermoeden van ongeoorloofde integriteitsscan | Er is een scan gedetecteerd op een DNP3-bronapparaat (outstation). Deze scan is niet geautoriseerd als het geleerde verkeer in uw netwerk. | Primair |
| De opdracht Computer Link Unauthorized | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Secundair |
| Niet-geautoriseerde ABB Totalflow-bestandsbewerking | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde ABB Totalflow-registratiebewerking | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Onbevoegde toegang tot Het gegevensblok van Siemens S7 | Een bronapparaat heeft geprobeerd toegang te krijgen tot een resource op een ander apparaat. Een toegangspoging tot deze resource tussen deze twee apparaten is niet geautoriseerd, omdat het geleerde verkeer in uw netwerk wordt gebruikt. | Waarschuwing |
| Niet-geautoriseerde toegang tot Het Object S7 Plus | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Onbevoegde toegang tot Wonderware Tag | Een bronapparaat heeft geprobeerd toegang te krijgen tot een resource op een ander apparaat. Een toegangspoging tot deze resource tussen deze twee apparaten is niet geautoriseerd, omdat het geleerde verkeer in uw netwerk wordt gebruikt. | Primair |
| Toegang tot niet-geautoriseerd BACNet-object | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde BACNet-route | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde database-aanmelding | Er is een aanmeldingspoging tussen een bronclient en doelserver gedetecteerd. Communicatie tussen deze apparaten is niet geautoriseerd als het geleerde verkeer in uw netwerk. | Primair |
| Niet-geautoriseerde databasebewerking | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde Emerson ROC-bewerking | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde GE SRTP-bestandstoegang | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde GE SRTP Protocol-opdracht | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde GE SRTP-systeemgeheugenbewerking | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde HTTP-activiteit | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde HTTP-server | Er is een niet-geautoriseerde toepassing gedetecteerd op een bronapparaat. De toepassing is niet geautoriseerd als een geleerde toepassing in uw netwerk. | Primair |
| Niet-geautoriseerde HTTP SOAP-actie | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde HTTP-gebruikersagent | Er is een niet-geautoriseerde toepassing gedetecteerd op een bronapparaat. De toepassing is niet geautoriseerd als een geleerde toepassing in uw netwerk. | Primair |
| Niet-geautoriseerde internetverbinding gedetecteerd | Een bronapparaat dat is gedefinieerd als onderdeel van uw netwerk communiceert met internetadressen. De bron is niet gemachtigd om te communiceren met internetadressen. | Kritiek |
| Niet-geautoriseerde MelSEC-opdracht | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Toegang tot het MMS-programma niet toegestaan | Een bronapparaat heeft geprobeerd toegang te krijgen tot een resource op een ander apparaat. Een toegangspoging tot deze resource tussen deze twee apparaten is niet geautoriseerd, omdat het geleerde verkeer in uw netwerk wordt gebruikt. | Primair |
| Niet-geautoriseerde MMS-service | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde multicast-/broadcastverbinding | Er is een Multicast/Broadcast-verbinding gedetecteerd tussen een bronapparaat en andere apparaten. Multicast-/broadcastcommunicatie is niet geautoriseerd. | Kritiek |
| Query op niet-geautoriseerde naam | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde OPC UA-activiteit | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde OPC UA-aanvraag/-reactie | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde bewerking is gedetecteerd door een door de gebruiker gedefinieerde regel | Er is verkeer gedetecteerd tussen twee apparaten. Deze activiteit is niet geautoriseerd op basis van een aangepaste waarschuwingsregel die is gedefinieerd door een gebruiker. | Primair |
| Niet-geautoriseerde READ-configuratie van HETEENF | Het bronapparaat is niet gedefinieerd als een programmeerapparaat, maar heeft een lees-/schrijfbewerking uitgevoerd op een doelcontroller. Programmeerwijzigingen mogen alleen worden uitgevoerd door programmeerapparaten. Mogelijk is er een programmeertoepassing op dit apparaat geïnstalleerd. | Waarschuwing |
| Schrijven van niet-geautoriseerde TIJDENS DE-configuratie | Het bronapparaat heeft een opdracht verzonden om het programma van een doelcontroller te lezen/schrijven. Deze activiteit is niet eerder gezien. | Primair |
| Niet-geautoriseerde UPLOAD VAN HET PROGRAMMA | Het bronapparaat heeft een opdracht verzonden om het programma van een doelcontroller te lezen/schrijven. Deze activiteit is niet eerder gezien. | Primair |
| Niet-geautoriseerde PROGRAMMING-programmering | Het bronapparaat is niet gedefinieerd als een programmeerapparaat, maar heeft een lees-/schrijfbewerking uitgevoerd op een doelcontroller. Programmeerwijzigingen mogen alleen worden uitgevoerd door programmeerapparaten. Mogelijk is er een programmeertoepassing op dit apparaat geïnstalleerd. | Kritiek |
| Niet-geautoriseerd frametype Profinet | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde SAIA S-Bus-opdracht | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde Siemens S7-uitvoering van de besturingsfunctie | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde Siemens S7-uitvoering van door de gebruiker gedefinieerde functie | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde Siemens S7 Plus Toegang blokkeren | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde Siemens S7 Plus-bewerking | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde SMB-aanmelding | Er is een aanmeldingspoging tussen een bronclient en doelserver gedetecteerd. Communicatie tussen deze apparaten is niet geautoriseerd als het geleerde verkeer in uw netwerk. | Primair |
| Niet-geautoriseerde SNMP-bewerking | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerde SSH-toegang | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd zoals het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-geautoriseerd Windows-proces | Er is een niet-geautoriseerde toepassing gedetecteerd op een bronapparaat. De toepassing is niet geautoriseerd als een geleerde toepassing in uw netwerk. | Primair |
| Niet-geautoriseerde Windows-service | Er is een niet-geautoriseerde toepassing gedetecteerd op een bronapparaat. De toepassing is niet geautoriseerd als een geleerde toepassing in uw netwerk. | Primair |
| Niet-geautoriseerde bewerking is gedetecteerd door een door de gebruiker gedefinieerde regel | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters schendt een door de gebruiker gedefinieerde regel | Primair |
| Niet-doorgestuurde modbus- en elektrische extensie | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-doorgestuurd gebruik van ASDU-typen | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-doorgestuurd gebruik van DNP3-functiecode | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |
| Niet-doorgestuurd gebruik van interne indicatie (IIN) | Een DNP3-bronapparaat (outstation) heeft een interne indicatie (IIN) gerapporteerd die niet is geautoriseerd als geleerd verkeer in uw netwerk. | Primair |
| Niet-doorgestuurd gebruik van Modbus-functiecode | Er zijn nieuwe verkeersparameters gedetecteerd. Deze combinatie van parameters is niet geautoriseerd als het geleerde verkeer in uw netwerk. De volgende combinatie is niet geautoriseerd. | Primair |

## <a name="anomaly-engine-alerts"></a>Waarschuwingen voor anomalie-engine

Waarschuwingen van de anomalie-engine beschrijven gedetecteerde afwijkingen in netwerkactiviteit.

| Titel | Beschrijving | Severity |
|--|--|--|
| Abnormaal uitzonderingspatroon in Slave | Er is een overmatig aantal fouten gedetecteerd op een bronapparaat. Dit kan het gevolg zijn van een operationeel probleem. | Secundair |
| Abnormale lengte http-header | Het bronapparaat heeft een abnormaal bericht verzonden. Dit kan wijzen op een poging om het doelapparaat aan te vallen. | Kritiek |
| Abnormaal aantal parameters in HTTP-header | Het bronapparaat heeft een abnormaal bericht verzonden. Dit kan wijzen op een poging om het doelapparaat aan te vallen. | Kritiek |
| Abnormaal periodiek gedrag in communicatiekanaal | Er is een wijziging in de communicatiefrequentie tussen de bron- en doelapparaten gedetecteerd. | Secundair |
| Abnormale beëindiging van toepassingen | Er is een overmatig aantal stopopdrachten gedetecteerd op een bronapparaat. Dit kan het gevolg zijn van een operationeel probleem of een poging om het apparaat te manipuleren. | Primair |
| Abnormale bandbreedte voor verkeer | Abnormale bandbreedte is gedetecteerd op een kanaal. De bandbreedte lijkt aanzienlijk lager/hoger te zijn dan eerder gedetecteerd. Werk voor meer informatie met de widget Totale bandbreedte. | Waarschuwing |
| Abnormale bandbreedte voor verkeer tussen apparaten | Abnormale bandbreedte is gedetecteerd op een kanaal. De bandbreedte lijkt aanzienlijk lager/hoger te zijn dan eerder gedetecteerd. Werk voor meer informatie met de widget Totale bandbreedte. | Waarschuwing |
| Adresscan gedetecteerd | Er is een bronapparaat gedetecteerd dat netwerkapparaten scant. Dit apparaat is niet geautoriseerd als een netwerkscanapparaat. | Kritiek |
| ARP-adresscan gedetecteerd | Er is een bronapparaat gedetecteerd dat netwerkapparaten scant met behulp van Address Resolution Protocol (ARP). Dit apparaatadres is niet geautoriseerd als geldig ARP-scanadres. | Kritiek |
| ARP-adresscan gedetecteerd | Er is een bronapparaat gedetecteerd dat netwerkapparaten scant met behulp van Address Resolution Protocol (ARP). Dit apparaatadres is niet geautoriseerd als geldig ARP-scanadres. | Kritiek |
| ARP-adresvervalsing | Er is een abnormale hoeveelheid pakketten gedetecteerd in het netwerk. Dit kan duiden op een aanval, bijvoorbeeld een ARP-spoofing- of ICMP-overlastaanval. | Waarschuwing |
| Overmatige aanmeldingspogingen | Er is een bronapparaat gezien dat overmatige aanmeldingspogingen heeft uitgevoerd op een doelserver. Dit kan een brute force-aanval zijn. De server kan worden aangetast door een kwaadwillende actor. | Kritiek |
| Overmatig aantal sessies | Er is gezien dat een bronapparaat overmatige aanmeldingspogingen heeft uitgevoerd op een doelserver. Dit kan een brute force-aanval zijn. De server kan worden aangetast door een kwaadwillende actor. | Kritiek |
| Overmatige herstartsnelheid van een onderstation | Er is een overmatig aantal opdrachten voor opnieuw opstarten gedetecteerd op een bronapparaat. Dit kan het gevolg zijn van een operationeel probleem of een poging om het apparaat te manipuleren. | Primair |
| Overmatige SMB-aanmeldingspogingen | Er is gezien dat een bronapparaat overmatige aanmeldingspogingen heeft uitgevoerd op een doelserver. Dit kan een brute force-aanval zijn. De server kan worden aangetast door een kwaadwillende actor. | Kritiek |
| ICMP-overstromingen | Er is een abnormale hoeveelheid pakketten gedetecteerd in het netwerk. Dit kan wijzen op een aanval, bijvoorbeeld een ARP-spoofing- of ICMP-overlastaanval. | Waarschuwing |
| Inhoud van de verboden HTTP-header | Het bronapparaat heeft een ongeldige aanvraag geïnitieerd. | Kritiek |
| Inactieve communicatiekanaal | Een communicatiekanaal tussen twee apparaten was inactief gedurende een periode waarin activiteit meestal wordt gezien. Dit kan erop wijzen dat het programma dat dit verkeer genereert, is gewijzigd of dat het programma mogelijk niet beschikbaar is. Het is raadzaam om de configuratie van het geïnstalleerde programma te controleren en te controleren of het programma juist is geconfigureerd. | Waarschuwing |
| Long Duration Address Scan Detected | Er is een bronapparaat gedetecteerd dat netwerkapparaten scant. Dit apparaat is niet geautoriseerd als een netwerkscanapparaat. | Kritiek |
| Poging tot wachtwoord raden gedetecteerd | Er is een bronapparaat gezien dat overmatige aanmeldingspogingen heeft uitgevoerd op een doelserver. Dit kan een brute force-aanval zijn. De server kan worden aangetast door een kwaadwillende actor. | Kritiek |
| DETECTED Scan Detected (SCAN VAN DESE GEDETECTEERD) | Er is een bronapparaat gedetecteerd dat netwerkapparaten scant. Dit apparaat is niet geautoriseerd als een netwerkscanapparaat. | Kritiek |
| Poortscan gedetecteerd | Er is een bronapparaat gedetecteerd dat netwerkapparaten scant. Dit apparaat is niet geautoriseerd als een netwerkscanapparaat. | Kritiek |
| Onverwachte berichtlengte | Het bronapparaat heeft een abnormaal bericht verzonden. Dit kan wijzen op een poging om het doelapparaat aan te vallen. | Kritiek |
| Onverwacht verkeer voor standaardpoort | Er is verkeer gedetecteerd op een apparaat met behulp van een poort die is gereserveerd voor een ander protocol. | Primair |

## <a name="protocol-violation-engine-alerts"></a>Enginewaarschuwingen voor schendingen van protocollen

Waarschuwingen van de protocol-engine beschrijven gedetecteerde afwijkingen in de pakketstructuur of veldwaarden in vergelijking met protocolspecificaties.

| Titel | Beschrijving | Severity |
|--|--|--|
| Overmatige misvormde pakketten in één sessie | Een abnormaal aantal verkeerd vormgevormde pakketten dat van het bronapparaat naar het doelapparaat wordt verzonden. Dit kan duiden op onjuiste communicatie of een poging om het doelapparaat te manipuleren. | Primair |
| Firmware-update | Een bronapparaat heeft een opdracht verzonden om firmware bij te werken op een doelapparaat. Controleer of recente upgrades voor programmeren, configuratie en firmware op het doelapparaat geldig zijn. | Waarschuwing |
| Functiecode wordt niet ondersteund door het outstation | Het doelapparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Het bericht over een verboden BACNet | Het bronapparaat heeft een ongeldige aanvraag geïnitieerd. | Primair |
| Poging tot verboden verbinding op poort 0 | Een bronapparaat heeft geprobeerd verbinding te maken met het doelapparaat op poortnummer nul (0). Voor TCP is poort 0 gereserveerd en kan niet worden gebruikt. Voor UDP is de poort optioneel en een waarde van 0 betekent geen poort. Er is meestal geen service op een systeem dat luistert op poort 0. Deze gebeurtenis kan wijzen op een poging om het doelapparaat aan te vallen of erop wijzen dat een toepassing onjuist is geprogrammeerd. | Secundair |
| Onrechtmatige DNP3-bewerking | Het bronapparaat heeft een ongeldige aanvraag geïnitieerd. | Primair |
| Onrechtmatige MODBUS-bewerking (uitzondering die is veroorzaakt door master) | Het bronapparaat heeft een ongeldige aanvraag geïnitieerd. | Primair |
| Onrechtmatige MODBUS-bewerking (functiecode nul) | Het bronapparaat heeft een ongeldige aanvraag geïnitieerd. | Primair |
| Onrechtmatige protocolversie | Het bronapparaat heeft een ongeldige aanvraag geïnitieerd. | Primair |
| Onjuiste parameter verzonden naar outstation | Het doelapparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Initiëren van een verouderde functiecode (gegevens initialiseren) | Het bronapparaat heeft een ongeldige aanvraag geïnitieerd. | Secundair |
| Initiëren van een verouderde functiecode (Configuratie opslaan) | Het bronapparaat heeft een ongeldige aanvraag geïnitieerd. | Secundair |
| Master heeft bevestiging van een toepassingslaag aangevraagd | Het bronapparaat heeft een ongeldige aanvraag geïnitieerd. | Waarschuwing |
| Modbus-uitzondering | Een bronapparaat (slave) heeft een uitzondering geretourneerd op een doelapparaat (master). | Primair |
| Slave Device Received Illegal ASDU Type | Het doelapparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Slave Device ontvangen onrechtmatige opdrachtoorzaak van verzending | Het doelapparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Slave Device heeft een verboden veelgebruikt adres ontvangen | Het doelapparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| De parameter 'Slave Device Received Illegal Data Address' | Het doelapparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Parameter voor de waarde van de onrechtmatige gegevenswaarde van het slave-apparaat is ontvangen | Het doelapparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Slave Device heeft een onrechtmatige functiecode ontvangen | Het doelapparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Slave Device Received Illegal Information Object Address | Het doelapparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Onbekend object verzonden naar outstation | Het doelapparaat heeft een ongeldige aanvraag ontvangen. | Primair |
| Gebruik van een gereserveerde functiecode | Het bronapparaat heeft een ongeldige aanvraag geïnitieerd. | Primair |
| Gebruik van onjuiste opmaak per outstation | Het bronapparaat heeft een ongeldige aanvraag geïnitieerd. | Waarschuwing |
| Gebruik van gereserveerde statusvlaggen (IIN) | Een DNP3-bronapparaat (outstation) heeft de gereserveerde interne indicator 2.6 gebruikt. Het is raadzaam om de configuratie van het apparaat te controleren. | Waarschuwing |

## <a name="malware-engine-alerts"></a>Waarschuwingen van de malware-engine

Waarschuwingen van de malware-engine beschrijven gedetecteerde schadelijke netwerkactiviteit.

| Titel | Beschrijving| Severity |
|--|--|--|
| Verbindingspoging tot bekend schadelijk IP-adres | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Ongeldig SMB-bericht (DoublePulsar Backdoor:) | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Aanvraag voor schadelijke domeinnaam | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Testbestand voor malware gedetecteerd - EICAR AV geslaagd | Er is een EICAR AV-testbestand gedetecteerd in het verkeer tussen twee apparaten. Het bestand is geen malware. Het wordt gebruikt om te bevestigen dat de antivirussoftware correct is geïnstalleerd; laten zien wat er gebeurt wanneer er een virus wordt gevonden en interne procedures en reacties controleren wanneer er een virus wordt gevonden. Antivirussoftware moet EICAR detecteren alsof het een echt virus is. | Primair |
| Vermoeden van confickermalware | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Vermoeden van Denial of Service-aanval | Een bronapparaat heeft geprobeerd een overmatig aantal nieuwe verbindingen met een doelapparaat te initiëren. Dit kan een DoS-aanval (Denial of Service) op het doelapparaat zijn en kan de functionaliteit van het apparaat onderbreken, de prestaties en beschikbaarheid van de service beïnvloeden of onherkenbare fouten veroorzaken. | Kritiek |
| Vermoeden van schadelijke activiteit | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Vermoeden van schadelijke activiteit (BlackEnergy) | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteit (DarkComet) | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteit (Duqu) | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteit (Activity) | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteit (Havex) | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteit (Kangany) | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteit (LightsOut) | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteit (naamquery's) | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Vermoeden van schadelijke activiteit (Verwerkverwerker Ivy) | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteit (opnieuw proberen) | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteit (Stuxnet) | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van schadelijke activiteit (WannaCry) | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Vermoeden van NotPetya-malware : schadelijke SMB-parameters gedetecteerd | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van NotPetya-malware - onrechtmatige SMB-transactie gedetecteerd | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |
| Vermoeden van uitvoering van externe code met PsExec | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Vermoeden van extern Windows-servicebeheer | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Suspicious Executable File Detected on Endpoint | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Primair |
| Verdacht verkeer gedetecteerd | Er is verdachte netwerkactiviteit gedetecteerd. Deze activiteit kan worden gekoppeld aan een aanval waarbij gebruik wordt gemaakt van een methode die wordt gebruikt door bekende malware. | Kritiek |

## <a name="operational-engine-alerts"></a>Waarschuwingen voor operationele engine

Waarschuwingen van operationele engine beschrijven gedetecteerde operationele incidenten of defecte entiteiten.

| Titel | Beschrijving | Severity |
|--|--|--|
| Er is een S7 Stop COMMAND verzonden | Het bronapparaat heeft een stopopdracht verzonden naar een doelcontroller. De controller werkt niet meer totdat er een startopdracht wordt verzonden. | Waarschuwing |
| BACNet-bewerking is mislukt | Een server heeft een foutcode geretourneerd. Dit duidt op een serverfout of een ongeldige aanvraag door een client. | Primair |
| Slechte mms-apparaattoestand | Een mms Virtual Manufacturing Device (VMD) heeft een statusbericht verzonden. Het bericht geeft aan dat de server mogelijk niet correct, gedeeltelijk operationeel of helemaal niet operationeel is geconfigureerd. | Primair |
| Wijziging van apparaatconfiguratie | Er is een configuratiewijziging gedetecteerd op een bronapparaat. | Secundair |
| Continue gebeurtenisbuffer-overloop op outstation | Er is een bufferoverloopgebeurtenis gedetecteerd op een bronapparaat. De gebeurtenis kan leiden tot beschadiging van gegevens, crashes in het programma of uitvoering van schadelijke code. | Primair |
| Controller opnieuw instellen | Een bronapparaat heeft een opdracht voor opnieuw instellen verzonden naar een doelcontroller. De controller is tijdelijk gestopt en wordt automatisch opnieuw gestart. | Waarschuwing |
| Controller stoppen | Het bronapparaat heeft een stopopdracht verzonden naar een doelcontroller. De controller werkt niet meer totdat er een startopdracht wordt verzonden. | Waarschuwing |
| Apparaat kan geen dynamisch IP-adres ontvangen | Het bronapparaat is geconfigureerd voor het ontvangen van een dynamisch IP-adres van een DHCP-server, maar heeft geen adres ontvangen. Dit duidt op een configuratiefout op het apparaat of een operationele fout in de DHCP-server. Het is raadzaam om de netwerkbeheerder op de hoogte te stellen van het incident | Primair |
| Apparaat is vermoedelijk niet verbonden (reageert niet) | Een bronapparaat heeft niet gereageerd op een opdracht die naar het apparaat is verzonden. De verbinding is mogelijk verbroken toen de opdracht werd verzonden. | Primair |
| EtherNet/IP CIP-serviceaanvraag is mislukt | Een server heeft een foutcode geretourneerd. Dit duidt op een serverfout of een ongeldige aanvraag door een client. | Primair |
| EtherNet/IP Encapsulation Protocol-opdracht is mislukt | Een server heeft een foutcode geretourneerd. Dit duidt op een serverfout of een ongeldige aanvraag door een client. | Primair |
| Gebeurtenisbuffer-overloop in outstation | Er is een bufferoverloopgebeurtenis gedetecteerd op een bronapparaat. De gebeurtenis kan leiden tot beschadiging van gegevens, crashes in het programma of uitvoering van schadelijke code. | Primair |
| Verwachte back-upbewerking is niet uitgevoerd | De verwachte activiteit voor back-up/bestandsoverdracht is niet tussen twee apparaten uitgevoerd. Dit kan duiden op fouten in het back-up-/bestandsoverdrachtproces. | Primair |
| Ge SRTP opdrachtfout | Een server heeft een foutcode geretourneerd. Dit duidt op een serverfout of een ongeldige aanvraag door een client. | Primair |
| Ge SRTP stop... opdracht is verzonden | Het bronapparaat heeft een stopopdracht verzonden naar een doelcontroller. De controller werkt niet meer totdat er een startopdracht wordt verzonden. | Waarschuwing |
| VOOR HET BEHEERBLOK is verdere configuratie vereist | Een bronapparaat heeft een SMS-bericht verzonden dat aangeeft dat het apparaat in gebruik moet worden gebruikt. Dit betekent dat het CONTROLEblok verdere configuratie vereist en DAT DE BERICHTEN gedeeltelijk of volledig niet operationeel zijn. | Primair |
| DE CONFIGURATIE van DE GEGEVENSSET IS GEWIJZIGD | Een bericht -gegevensset (aangeduid met protocol-id) is gewijzigd op een bronapparaat. Dit betekent dat het apparaat een andere gegevensset voor dit bericht rapporteert. | Waarschuwing |
| Controller controller onverwachte status | Een Controller Controller heeft een onverwacht diagnostisch bericht verzonden waarin een statuswijziging wordt aangegeven. | Waarschuwing |
| HTTP-clientfout | Het bronapparaat heeft een ongeldige aanvraag geïnitieerd. | Waarschuwing |
| Verboden IP-adres | Het systeem heeft verkeer gedetecteerd tussen een bronapparaat en een IP-adres dat een ongeldig adres is. Dit kan duiden op een verkeerde configuratie of een poging om verboden verkeer te genereren. | Secundair |
| Master-Slave verificatiefout | Het verificatieproces tussen een DNP3-bronapparaat (master) en een doelapparaat (outstation) is mislukt. | Secundair |
| Mms-serviceaanvraag is mislukt | Een server heeft een foutcode geretourneerd. Dit duidt op een serverfout of een ongeldige aanvraag door een client. | Primair |
| Er is geen verkeer gedetecteerd in de sensorinterface | Een sensor is gestopt met het detecteren van netwerkverkeer op een netwerkinterface. | Kritiek |
| OPC UA-server heeft een gebeurtenis aan de orde gesteld die de aandacht van de gebruiker vereist | Een OPC UA-server heeft een gebeurtenismelding verzonden naar een client. Dit type gebeurtenis vereist aandacht van de gebruiker | Primair |
| OPC UA-serviceaanvraag is mislukt | Een server heeft een foutcode geretourneerd. Dit duidt op een serverfout of een ongeldige aanvraag door een client. | Primair |
| Outstation opnieuw opgestart | Er is een koude herstart gedetecteerd op een bronapparaat. Dit betekent dat het apparaat fysiek is uitgeschakeld en opnieuw is ingeschakeld. | Waarschuwing |
| Outstation wordt regelmatig opnieuw opgestart | Er is een overmatig aantal koude herstarts gedetecteerd op een bronapparaat. Dit betekent dat het apparaat een overmatig aantal keren fysiek is uitgeschakeld en weer ingeschakeld. | Secundair |
| Configuratie van het outstation is gewijzigd | Er is een configuratiewijziging gedetecteerd op een bronapparaat. | Primair |
| Beschadigde configuratie van het outstation gedetecteerd | Dit DNP3-bronapparaat (outstation) heeft een beschadigde configuratie gerapporteerd. | Primair |
| Profinet DCP-opdracht is mislukt | Een server heeft een foutcode geretourneerd. Dit duidt op een serverfout of een ongeldige aanvraag door een client. | Primair |
| Profinet apparaat fabrieksinstellingen terugzetten | Een bronapparaat heeft een opdracht voor het terugzetten van de fabrieksinstellingen verzonden naar een Profinet-doelapparaat. Met de opdracht voor opnieuw instellen worden de configuraties van het Profinet-apparaat geweken en wordt de werking ervan gestopt. | Waarschuwing |
| RPC-bewerking is mislukt | Een server heeft een foutcode geretourneerd. Dit duidt op een serverfout of een ongeldige aanvraag door een client. | Primair |
| Configuratie van voorbeeldwaarden berichtgegevensset is gewijzigd | Een bericht -gegevensset (aangeduid met protocol-id) is gewijzigd op een bronapparaat. Dit betekent dat het apparaat een andere gegevensset voor dit bericht rapporteert. | Waarschuwing |
| Onherkenbare fout met slave-apparaat | Er is een onherkenbare voorwaardefout gedetecteerd op een bronapparaat. Dit type fout duidt meestal op een hardwarefout of een fout bij het uitvoeren van een specifieke opdracht. | Primair |
| Vermoeden van hardwareproblemen in outstation | Er is een onherkenbare voorwaardefout gedetecteerd op een bronapparaat. Dit type fout duidt meestal op een hardwarefout of een fout bij het uitvoeren van een specifieke opdracht. | Primair |
| Vermoeden van niet-reagerend MODBUS-apparaat | Een bronapparaat heeft niet gereageerd op een opdracht die naar het apparaat is verzonden. De verbinding is mogelijk verbroken toen de opdracht werd verzonden. | Secundair |
| Verkeer gedetecteerd op sensorinterface | Een sensor heeft het detecteren van netwerkverkeer op een netwerkinterface hervat. | Waarschuwing |

## <a name="next-steps"></a>Volgende stappen

U kunt [waarschuwingsgebeurtenissen beheren.](how-to-manage-the-alert-event.md)
Meer informatie over het [doorsturen van waarschuwingsgegevens.](how-to-forward-alert-information-to-partners.md)
