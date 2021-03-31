---
title: Azure Attestation-overzicht
description: Een overzicht van Microsoft Azure Attestation, een oplossing voor het afleiden van Trusted Execution Environments (TEE’s)
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: 020ba74948a062d23d61272ee912eb3364180f1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102617995"
---
# <a name="microsoft-azure-attestation"></a>Microsoft Azure Attestation 

Microsoft Azure Attestation is een geïntegreerde oplossing voor het extern controleren van de betrouw baarheid van een platform en integriteit van de binaire bestanden die erin worden uitgevoerd. De service ondersteunt bekrachtiging van de platforms die worden ondersteund door Trusted Platform Modules (TPM's) en biedt de mogelijkheid om de status van TEE's (Trusted Execution Environment), zoals [Intel® software Guard Extensions](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) (SGX)-enclaves en enclaves met [Beveiliging op basis van virtualisatie](/windows-hardware/design/device-experiences/oem-vbs) (VBS), te bekrachtigen. 

Attestation is een proces voor het demonstreren dat de software-binaire bestanden op de juiste manier op een vertrouwd platform zijn geïnstantieerd. Externe relying party's kunnen dan betrouwbaarheid krijgen dat alleen dergelijke bedoelde software wordt uitgevoerd op vertrouwde hardware. Azure Attestation is een uniforme klantgerichte service en kader voor Attestation.

Met Azure Attestation kunnen geavanceerde beveiligingsmodellen, zoals [Azure Confidential Computing](../confidential-computing/overview.md) en Intelligent Edge-beveiliging. Klanten hebben de mogelijkheid gevraagd om de locatie van een machine onafhankelijk te controleren, het postuur van een virtuele machine (VM) op die computer en de omgeving waarin enclaves op die VM worden uitgevoerd. Met Azure Attestation kunnen deze en vele bijkomende aanvragen van klanten worden gestimuleerd.

Azure Attestation ontvangt bewijs van berekeningsentiteiten, zet ze om in een set claims, valideert ze op basis van configureerbare beleidsregels en produceert cryptografische bewijzen voor op claims gebaseerde toepassingen (bijvoorbeeld relying party's en controle-instanties).

## <a name="use-cases"></a>Gebruiksvoorbeelden

Azure Attestation biedt uitgebreide Attestation-Services voor meerdere omgevingen en aparte use-cases.

### <a name="sgx-attestation"></a>SGX-Attestation

SGX verwijst naar hardwarematige isolatie, die wordt ondersteund op bepaalde Intel CPU-modellen. SGX maakt het mogelijk code uit te voeren in gezuiverde compartimenten, ook wel SGX-enclaves genoemd. De machtigingen voor toegang en geheugen worden vervolgens beheerd door hardware om een minimaal aanvalsoppervlak met de juiste isolatie te garanderen.

Clienttoepassingen kunnen worden ontworpen om gebruik te maken van SGX-enclaves door beveiligingsgevoelige taken te delegeren in deze enclaves. Dergelijke toepassingen kunnen vervolgens gebruik maken van Azure Attestation om regelmatig een vertrouwensrelatie tot stand te brengen in de enclave en de mogelijkheid om toegang te krijgen tot gevoelige gegevens.

### <a name="open-enclave"></a>Open Enclave
[Open Enclave](https://openenclave.io/sdk/) (OE) is een verzameling van bibliotheken die gericht zijn op het maken van een enkelvoudige enclaving-abstractie voor ontwikkelaars voor het bouwen van op TEE gebaseerde toepassingen. Het biedt een universeel veilig app-model dat platformbijzonderheden minimaliseert. Microsoft beschouwt dit als een essentiële opstap voor democratisering van op hardware gebaseerde enclave-technologieën zoals SGX en het verhogen van hun opname op Azure.

OE standaardiseert specifieke vereisten voor de verificatie van een enclave-bewijs. Hiermee wordt OE gekwalificeerd als een uiterst aanpasbare Attestation-consument van Azure Attestation.

### <a name="tpm-attestation"></a>TPM-attestation 

Attestation op basis van TPM (Trusted Platform Module) is van cruciaal belang om de status van een platform te kunnen bewijzen. TPM fungeert als de basis voor het vertrouwen en van de beveiligingscoprocessor om cryptografische geldigheid aan de metingen (bewijs) te verlenen. Apparaten met een TPM kunnen vertrouwen op attestation om te bewijzen dat tijdens het opstarten de opstartintegriteit niet wordt aangetast en dat de claims worden gebruikt om activeringsmogelijkheden van functiestatussen te detecteren. 

Clienttoepassingen kunnen worden ontworpen om gebruik te maken van TPM-attestation door beveiligingsgevoelige taken te delegeren, zodat deze alleen kunnen worden uitgevoerd nadat een platform op veiligheid is gevalideerd. Dergelijke toepassingen kunnen vervolgens gebruik maken van Azure Attestation om regelmatig een vertrouwensrelatie met het platform tot stand te brengen en van de mogelijkheid toegang te krijgen tot gevoelige gegevens.

## <a name="azure-attestation-can-run-in-a-tee"></a>Azure Attestation kan worden uitgevoerd in een TEE

Azure Attestation is essentieel voor vertrouwelijke computerscenario's, omdat het de volgende acties uitvoert:

- Controleert of de enclave-bewijzen geldig zijn.
- Evalueert het enclave-bewijs op basis van een door de klant gedefinieerd beleid.
- Beheert en tenant-specifieke beleidsregels op.
- Genereert en ondertekent een token dat wordt gebruikt door relying party's om te communiceren met de enclave.

Azure Attestation is gebouwd om te worden uitgevoerd in twee soorten omgevingen:
- Azure Attestation wordt uitgevoerd in een SGX-ingeschakelde TEE.
- Azure Attestation wordt uitgevoerd in een niet-TEE.

Klanten van Azure Attestation hebben een vereiste voor Microsoft uitgesproken om operationeel vanuit de Trusted Computing Base (TCB) te worden uitgevoerd. Dit is om te voorkomen dat Microsoft-entiteiten, zoals VM-beheerders, hostbeheerders en Microsoft-ontwikkelaars, de Attestation-aanvragen, beleidsregels en door Azure Attestation uitgegeven tokens wijzigen. Azure Attestation is ook ontworpen om te worden uitgevoerd in TEE, waarbij functies van Azure Attestation, zoals offertevalidatie, het genereren van tokens en token-ondertekening, worden verplaatst naar een SGX-enclave.

## <a name="why-use-azure-attestation"></a>Waarom Azure Attestation gebruiken

Azure Attestation is de voorkeurskeuze voor het afleveren van TEEs omdat het de volgende voordelen biedt: 

- Unified Framework voor het bekrachtigen van meerdere omgevingen, zoals TPM's, SGX-enclaves en VBS-enclaves 
- Multi-tenant-service waarmee aangepaste Attestation-providers en -beleid kunnen worden geconfigureerd om het genereren van tokens te beperken
- Biedt regionale gedeelde providers die kunnen worden verklaard zonder configuratie van gebruikers
- Beveiligt de gegevens die worden gebruikt bij de implementatie in een SGX-enclave
- Service met hoge beschikbaarheid 

## <a name="business-continuity-and-disaster-recovery-bcdr-support"></a>Ondersteuning voor bedrijfscontinuïteit en herstel na noodgevallen (BCDR)

[Bedrijfscontinuïteit en herstel na noodgevallen](../best-practices-availability-paired-regions.md) (BCDR) voor Azure Attestation maakt het mogelijk om service-onderbrekingen te beperken die voortkomen uit aanzienlijke beschikbaarheidsproblemen of noodgebeurtenissen in een regio.

Clusters die in twee regio's zijn geïmplementeerd, werken onder normale omstandigheden onafhankelijk. In het geval van een storing of uitval van één regio vindt het volgende plaats:

- Azure Attestation BCDR zorgt voor een naadloze failover waarbij klanten geen extra stap hoeven uit te voeren om te herstellen
- De [Azure-Traffic Manager](../traffic-manager/index.yml) voor de regio detecteert dat de status test is gedegradeerd en het eind punt overschakelt naar de gekoppelde regio
- Bestaande verbindingen werken niet en ontvangen een interne serverfout of time-out voor problemen
- Alle besturingsvlak-bewerkingen worden geblokkeerd. Klanten kunnen geen Attestation-providers in de primaire regio maken
- Alle gegevenslaag bewerkingen, inclusief attest gesprekken en beleids configuratie, worden geleverd door de secundaire regio. Klanten kunnen blijven werken aan gegevenslaag bewerkingen met de oorspronkelijke URI die overeenkomt met de primaire regio

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure Attestation-basisconcepten](basic-concepts.md)
- [Een Attestation-beleid ontwerpen en ondertekenen](author-sign-policy.md)
- [Azure Attestation instellen met behulp van PowerShell](quickstart-powershell.md)
