---
title: Overzicht van Azure Confidential Computing
description: Overzicht van ACC (Azure Confidential Computing)
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: c3ef6a764123f52583f081a3b152651b9bb8b8b7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554187"
---
# <a name="confidential-computing-on-azure"></a>Confidential Computing op Azure

Met Azure Confidential Computing kunt u uw gevoelige gegevens isoleren terwijl deze worden verwerkt in de cloud. Veel branches gebruiken confidential computing om hun gegevens te beveiligen. Confidential computing wordt gebruikt voor:

- Beveiliging van financiële gegevens
- Bescherming van patiëntgegevens
- Het uitvoeren van machine learning processen met gevoelige informatie
- Het uitvoeren van algoritmen op versleutelde gegevenssets uit meerdere bronnen


## <a name="overview"></a>Overzicht
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

We weten dat het beveiligen van uw cloudgegevens belangrijk is. We kennen uw zorgen. Dit volgen slechts enkele vragen die onze klanten kunnen hebben wanneer ze gevoelige workloads naar de cloud verplaatsen: 

- Hoe kan ik ervoor zorgen dat Microsoft geen toegang heeft tot gegevens die niet zijn versleuteld?
- Hoe kan ik beveiligingsrisico's voorkomen, afkomstig van bevoegde beheerders in mijn bedrijf?
- Zijn er nog meer manieren waarop ik kan voorkomen dat derden toegang krijgen tot gevoelige klantgegevens?

Microsoft Azure helpt u om de kwetsbaarheid voor aanvallen te beperken voor betere gegevensbescherming. Azure biedt al veel hulpprogramma's om [**data-at-rest-**](../security/fundamentals/encryption-atrest.md) te beschermen via modellen zoals versleuteling aan clientzijde en versleuteling aan serverzijde. Daarnaast biedt Azure mechanismen voor het versleutelen van [**gegevens in overdracht**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) via beveiligde protocollen zoals TLS en HTTPS. Op deze pagina maakt u kennis met een derde element van gegevensversleuteling: de versleuteling van **gegevens in gebruik**.

## <a name="introduction-to-confidential-computing"></a>Inleiding tot confidential computing 

Confidential computing is een industriële term die is gedefinieerd door het [Confidential Computing Consortium](https://confidentialcomputing.io/) (CCC), een stichting die zich toelegt op het definiëren en versnellen van de acceptatie van confidential computing. Het CCC definieert confidential computing als: De beveiliging van gegevens in gebruik door berekeningen uit te voeren in een hardware-gebaseerde TEE (Trust Execution Environment).

Een TEE is een omgeving die de uitvoering afdwingt van alleen geautoriseerde code. Gegevens in de TEE kunnen niet worden gelezen of gemanipuleerd door code buiten die omgeving. 

### <a name="lessen-the-need-for-trust"></a>De behoefte aan vertrouwen beperken
Voor het uitvoeren van werkbelastingen in de cloud is vertrouwen vereist. U geeft deze vertrouwensrelatie aan verschillende providers die verschillende onderdelen van uw toepassing inschakelen.


**Leveranciers van app-software**: Vertrouw software door on-premises te implementeren met behulp van open-source of door interne toepassingssoftware te bouwen.

**Hardware-leveranciers**: Vertrouw hardware door gebruik te maken van on-premises hardware of interne hardware. 

**Infrastructuur-providers**: Vertrouw cloudproviders of beheer uw eigen on-premises datacenters.


Met Azure confidential computing is het gemakkelijker om de cloudprovider te vertrouwen door de noodzaak van vertrouwen in verschillende aspecten van de cloudinfrastructuur te verminderen. Azure confidential computing beperkt de vertrouwensrelatie voor de host OS-kernel, de hypervisor, de VM-beheerder en de host-beheerder.

### <a name="reducing-the-attack-surface"></a>De kwetsbaarheid voor aanvallen verminderen
De Trusted Computing Base (TCB) verwijst naar alle hardware-, firmware- en softwareonderdelen van het systeem die een beveiligde omgeving bieden. De onderdelen in de TCB worden als kritiek beschouwd. Als er een fout is opgetreden in het ene onderdeel in de TCB, kan de beveiliging van het hele systeem worden gekraakt. 

Een lagere TCB betekent een betere beveiliging. Er is minder risico voor blootstelling aan verschillende beveiligingsproblemen, malware, aanvallen en kwaadaardige personen. Azure Confidential Computing is erop gericht de TCB voor uw werkbelastingen in de cloud te verlagen door TEE’s te bieden. TEE’s verminderen uw TCB tot binaire bestanden voor vertrouwde runtime, code en bibliotheken. Wanneer u Azure-infrastructuur en -services gebruikt voor confidential computing, kunt u alle Microsoft-elementen uit uw TCB verwijderen.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Azure gebruiken voor confidential computing in de cloud<a id="cc-on-azure"></a>

Met Azure Confidential Computing kunt u de mogelijkheden van confidential computing in een gevirtualiseerde omgeving gebruiken. U kunt nu hulpprogramma's, software en cloudinfrastructuur gebruiken om te bouwen op veilige hardware.  

**Toegang door onbevoegden voorkomen**: Voer gevoelige gegevens uit in de Cloud. Vertrouw erop dat Azure de best mogelijke gegevensbescherming biedt, met weinig tot geen wijzigingen van wat er vandaag wordt gedaan.

**Naleving van regelgeving**: Migreer naar de cloud en behoud het volledige beheer van de gegevens om te voldoen aan de wettelijke voorschriften voor het beschermen van persoonlijke gegevens en het beveiligen van organisatie-IP.

**Beveiligde en niet-vertrouwde samenwerking**: Bewaak problemen die in de hele organisatie worden geschaald door gegevens samen te zetten in organisaties, zelfs in concurrenten, om brede gegevensanalyses en meer inzichten te ontgrendelen.

**Geïsoleerde verwerking**: Bied een nieuwe golf aan producten die de aansprakelijkheid van privégegevens met blinde verwerking elimineren. Gebruikersgegevens kunnen zelfs niet worden opgehaald door de serviceprovider. 

## <a name="get-started"></a>Aan de slag
### <a name="azure-compute"></a>Azure Compute
Bouw toepassingen boven op de IaaS-aanbiedingen voor confidential computing in Azure.
- Virtuele machines (VM's): [DCsv2-serie](confidential-computing-enclaves.md)
- Azure Kubernetes (AKS): [Vertrouwelijke containers organiseren](confidential-nodes-aks-overview.md)

### <a name="azure-security"></a>Azure-beveiliging 
Zorg ervoor dat uw werkbelastingen zijn beveiligd via verificatiemethoden en door aan hardware gebonden sleutelbeheer. 
- Attestation: [Microsoft Azure Attestation (preview)](../attestation/overview.md)
- Sleutelbeheer: Beheerde HSM (preview)

### <a name="develop"></a>Ontwikkelen
Begin met het gebruik van het ontwikkelen van enclave toepassingen en het implementeren van vertrouwelijke algoritmen met behulp van het raamwerk voor vertrouwelijke deductie.
- Toepassingen schrijven om op DCsv2-VM's te worden uitgevoerd: [Open enclave-SDK](https://github.com/openenclave/openenclave)
- Vertrouwelijke ML-modellen in ONNX runtime: [Vertrouwelijke deductie (beta)](https://aka.ms/confidentialinference)

## <a name="next-steps"></a>Volgende stappen

Implementeer een virtuele machine uit de DCsv2-serie en installeer daarop de OE SDK.

> [!div class="nextstepaction"]
> [Een VM met Azure Confidential Computing implementeren in Azure Marketplace](quick-create-marketplace.md)