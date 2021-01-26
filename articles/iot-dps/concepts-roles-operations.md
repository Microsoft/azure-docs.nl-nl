---
title: IoT Hub Device Provisioning Service-rollen en bewerkingen
description: Dit artikel bevat een conceptueel overzicht van de rollen en bewerkingen die betrokken zijn bij het ontwikkelen en IoT-oplossing met behulp van de IoT Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: 322d6b590863d8065454c0439c5d899107a6abe7
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784962"
---
# <a name="roles-and-operations"></a>Rollen en bewerkingen

De fasen van het ontwikkelen van een IoT-oplossing kunnen weken of maanden duren vanwege productie vaststaande zoals productie tijd, verzen ding, douane proces, enzovoort. Daarnaast kunnen ze activiteiten omvatten over meerdere rollen, op basis van de verschillende betrokken entiteiten. In dit onderwerp worden de verschillende rollen en bewerkingen met betrekking tot elke fase uitgebreid weer gegeven. vervolgens wordt de stroom in een sequentie diagram geïllustreerd. 

Bij het inrichten worden ook vereisten op de fabrikant van het apparaat geplaatst, specifiek voor het inschakelen van het [Attestation-mechanisme](concepts-service.md#attestation-mechanism). Productie bewerkingen kunnen ook worden uitgevoerd onafhankelijk van de timing van automatische inrichtings fasen, met name in gevallen waarin nieuwe apparaten zijn aangeschaft nadat het automatisch inrichten al is ingesteld.

Er wordt een reeks Quick starts in de inhouds opgave aan de linkerkant beschreven, zodat u de automatische inrichting kunt uitleggen door middel van praktijk ervaring. Om het leer proces te vereenvoudigen/vereenvoudigen, wordt software gebruikt voor het simuleren van een fysiek apparaat voor inschrijving en registratie. Voor sommige Quick starts moet u bewerkingen uitvoeren voor meerdere rollen, waaronder bewerkingen voor niet-bestaande rollen, vanwege de gesimuleerde aard van de Quick starts.

| Rol | Bewerking | Beschrijving |
|------| --------- | ------------|
| Fabrikant | Identiteits-en registratie-URL coderen | Op basis van het Attestation-mechanisme dat wordt gebruikt, is de fabrikant verantwoordelijk voor het coderen van de apparaat-id-gegevens en de Device Provisioning Service-registratie-URL.<br><br>**Quick** starts: aangezien het apparaat is gesimuleerd, is er geen rol van de fabrikant. Raadpleeg de rol voor ontwikkel aars voor meer informatie over hoe u deze gegevens ophaalt, die wordt gebruikt voor het coderen van een voorbeeld registratie toepassing. |
| | Apparaat-id opgeven | Als afzender van de apparaat-id-gegevens, is de fabrikant verantwoordelijk voor het communiceren met de operator (of een aangewezen agent) of het rechtstreeks inschrijven bij de Device Provisioning Service via Api's.<br><br>**Quick** starts: aangezien het apparaat is gesimuleerd, is er geen rol van de fabrikant. Zie de rol van de operator voor meer informatie over hoe u de apparaat-id krijgt, die wordt gebruikt om een gesimuleerd apparaat in te schrijven in het Device Provisioning service-exemplaar. |
| Operator | Automatische inrichting configureren | Deze bewerking komt overeen met de eerste fase van automatische inrichting.<br><br>**Quick** starts: u voert de operator Role uit en configureert de Device Provisioning Service en IOT hub exemplaren in uw Azure-abonnement. |
|  | Apparaat-id inschrijven | Deze bewerking komt overeen met de tweede fase van automatische inrichting.<br><br>**Quick** starts: u voert de operator rol uit en registreert uw gesimuleerde apparaat in het Device Provisioning service-exemplaar. De apparaat-id wordt bepaald door de Attestation-methode die in de Quick Start (TPM of X. 509) wordt gesimuleerd. Zie de rol voor ontwikkel aars voor meer informatie over de Attestation. |
| Device Provisioning Service,<br>IoT Hub | \<all operations\> | Voor zowel een productie-implementatie met fysieke apparaten als Quick starts met gesimuleerde apparaten worden deze rollen vervuld via de IoT-services die u configureert in uw Azure-abonnement. De functie rollen/bewerkingen is precies hetzelfde, omdat de IoT-Services niet verschillen met het inrichten van fysieke en gesimuleerde apparaten. |
| Ontwikkelaar | Registratie software bouwen/implementeren | Deze bewerking komt overeen met de derde fase van automatische inrichting. De ontwikkelaar is verantwoordelijk voor het maken en implementeren van de registratie software op het apparaat, met behulp van de juiste SDK.<br><br>**Quick** starts: de voorbeeld registratie toepassing die u bouwt, simuleert een echt apparaat, voor uw platform of taal, die wordt uitgevoerd op uw werk station (in plaats van het te implementeren op een fysiek apparaat). De registratie toepassing voert dezelfde bewerkingen uit als één geïmplementeerd op een fysiek apparaat. U geeft de Attestation-methode (TPM of X. 509-certificaat) op, plus de registratie-URL en de ID-Scope van uw Device Provisioning service-exemplaar. De apparaat-id wordt bepaald door de SDK-Attestation Logic tijdens runtime, op basis van de methode die u opgeeft: <ul><li>**TPM-Attestation** : uw ontwikkel werkstation voert een [TPM Simulator-toepassing](quick-create-simulated-device.md)uit. Eenmaal uitgevoerd, wordt er een afzonderlijke toepassing gebruikt voor het extra heren van de ' goedkeurings sleutel ' en ' registratie-ID ' van de TPM voor gebruik bij het inschrijven van de apparaat-id. De SDK-Attestation-logica gebruikt de Simulator tijdens de registratie om een ondertekend SAS-token te presen teren voor verificatie en inschrijvings verificatie.</li><li>**X509-Attestation** : u gebruikt een hulp programma voor het [genereren van een certificaat](tutorial-custom-hsm-enrollment-group-x509.md#create-an-x509-certificate-chain). Nadat het certificaat bestand is gegenereerd, maakt u het voor gebruik in de inschrijving vereist. De SDK-Attestation-logica gebruikt het certificaat tijdens de registratie om te presen teren voor verificatie en inschrijvings verificatie.</li></ul> |
| Apparaat | Opstart chassis en registreren | Deze bewerking komt overeen met de derde fase van automatische inrichting, die is voldaan door de software voor apparaatregistratie die is gebouwd door de ontwikkelaar. Zie de rol voor ontwikkel aars voor meer informatie. Bij de eerste keer opstarten: <ol><li>De toepassing maakt verbinding met het Device Provisioning service-exemplaar, volgens de algemene URL en het ID-bereik van de service die tijdens de ontwikkeling zijn opgegeven.</li><li>Zodra de verbinding is gemaakt, wordt het apparaat geverifieerd aan de hand van de Attestation-methode en identiteit die tijdens de inschrijving is opgegeven.</li><li>Na verificatie wordt het apparaat geregistreerd met het IoT Hub exemplaar dat is opgegeven door het inrichtings service-exemplaar.</li><li>Bij een geslaagde registratie worden een unieke apparaat-ID en IoT Hub-eind punt geretourneerd aan de registratie toepassing voor communicatie met IoT Hub.</li><li> Vanaf daar kan het apparaat de eerste dubbele toestand van het [apparaat](~/articles/iot-hub/iot-hub-devguide-device-twins.md) opvangen voor configuratie en het proces van het rapporteren van telemetriegegevens.</li></ol>**Quick** starts: sinds het apparaat is gesimuleerd, wordt de registratie software uitgevoerd op uw werk station voor ontwikkel aars.|

In het volgende diagram vindt u een overzicht van de rollen en sequentiëren van bewerkingen tijdens het automatisch inrichten van apparaten:
<br><br>
[![Volg orde voor automatisch inrichten voor een apparaat](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Optioneel kan de fabrikant ook de bewerking ' apparaat-id inschrijven ' uitvoeren met Api's van Device Provisioning Service (in plaats van via de-operator). Voor een gedetailleerde bespreking van deze sequentiëren en meer, zie de [nul aanrakende apparaatregistratie met Azure IOT video](https://youtu.be/cSbDRNg72cU?t=2460) (beginnend bij markering 41:00)

## <a name="roles-and-azure-accounts"></a>Rollen en Azure-accounts

Hoe elke rol wordt toegewezen aan een Azure-account, is afhankelijk van het scenario en er zijn enkele scenario's die kunnen worden betrokken. De onderstaande algemene patronen moeten een algemene uitleg geven over hoe rollen doorgaans worden toegewezen aan een Azure-account.

#### <a name="chip-manufacturer-provides-security-services"></a>De chip fabrikant levert beveiligings Services

In dit scenario beheert de fabrikant de beveiliging voor niveau-One-klanten. Dit scenario kan de voor keur hebben op dit niveau, één klanten, omdat ze geen gedetailleerde beveiliging hoeven te beheren. 

De fabrikant introduceert beveiliging in Hardware Security modules (Hsm's). Deze beveiliging kan de fabrikant omvatten: sleutels, certificaten, enzovoort, van potentiële klanten die al instellingen voor DPS-instanties en registratie groepen hebben. De fabrikant kan deze beveiligings informatie ook genereren voor de klanten.

In dit scenario kunnen er twee Azure-accounts betrokken zijn:

- **Account #1**: waarschijnlijk gedeeld door de Opera tors en ontwikkelaars rollen. Deze partij kan de HSM-chips kopen bij de fabrikant. Deze chips worden gewijsd naar DPS-instanties die zijn gekoppeld aan het account #1. Met DPS-inschrijvingen kan deze partij apparaten aan meerdere level-twee klanten leaseen door de instellingen voor het inschrijven van apparaten in DPS opnieuw te configureren. Deze partij kan ook IoT-hubs voor eind gebruikers hebben toegewezen aan de interface om toegang te krijgen tot telemetrie van apparaten, enzovoort. In dit laatste geval is een tweede account mogelijk niet nodig.

- **Account #2**: eind gebruikers kunnen hun eigen IOT-hubs hebben. De partij die is gekoppeld aan account #1, wijst alleen geleasde apparaten toe aan de juiste hub in dit account. Voor deze configuratie moeten DPS-en IoT-hubs over Azure-accounts worden gekoppeld. Dit kan worden gedaan met Azure Resource Manager sjablonen.

#### <a name="all-in-one-oem"></a>Alles-in-één OEM

De fabrikant kan een ' all-in-One OEM ' zijn, waarbij slechts één fabrikant account nodig is. De fabrikant zorgt voor beveiliging en het inrichten van end-to-end.

De fabrikant kan een Cloud toepassing bieden aan klanten die apparaten kopen. Deze toepassing heeft de interface met de IoT Hub toegewezen door de fabrikant.

Verkoop automaten of geautomatiseerde koffie machines vertegenwoordigen voor beelden voor dit scenario.




## <a name="next-steps"></a>Volgende stappen

Het kan handig zijn om dit artikel als referentie punt te voorzien van een verwijzing naar de bijbehorende Snelstartgids voor automatische inrichting. 

Begin met het volt ooien van de Snelstartgids voor het instellen van automatische inrichting die het beste past bij de voor keuren van uw beheer programma, waarin u wordt begeleid bij de fase ' Service configuratie ':

- [Automatische inrichting instellen met behulp van Azure CLI](quick-setup-auto-provision-cli.md)
- [Automatische inrichting instellen met behulp van de Azure Portal](quick-setup-auto-provision.md)
- [Automatische inrichting instellen met behulp van een resource manager-sjabloon](quick-setup-auto-provision-rm.md)

Ga vervolgens verder met een Snelstartgids voor het inrichten van een apparaat dat aansluit bij het Attestation-mechanisme van het apparaat en de SDK/taal voorkeur voor de Device Provisioning Service. In deze Quick start gaat u de fasen ' apparaatregistratie ' en ' registratie en configuratie van apparaten ' door lopen: 

| Attestation-mechanisme voor apparaten | Quick Start SDK/taal | 
| ------------------------------- | -------------------- |
| Symmetrische sleutel | [C](quick-create-simulated-device-symm-key.md)<br>[Java](quick-create-simulated-device-symmetric-key-java.md)<br>[Python](quick-create-device-symmetric-key-python.md) |
| X. 509-certificaat | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |
| Gesimuleerde Trusted Platform Module (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |




