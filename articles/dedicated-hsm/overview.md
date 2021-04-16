---
title: Wat is Toegewezen HSM? - Azure Toegewezen HSM | Microsoft Docs
description: Meer informatie over Azure Dedicated HSM, een Azure-service die opslag biedt van cryptografische sleutels in Azure.
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 418c8f0844bf2336ce0d4a681071f237d81877ca
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505714"
---
# <a name="what-is-azure-dedicated-hsm"></a>Wat is Azure Toegewezen HSM?

Azure Toegewezen HSM is een Azure-service die opslag van cryptografische sleutels biedt in Azure. Toegewezen HSM voldoet aan de strengste beveiligingseisen. Dit is de ideale oplossing voor klanten die apparaten willen gebruiken die zijn gevalideerd met FIPS 140-2 Niveau 3 en die volledige en exclusieve controle over het HSM-apparaat willen. 

 HSM-apparaten zijn wereldwijd geïmplementeerd in verschillende Azure-regio's. Ze kunnen eenvoudig worden ingericht als een paar apparaten en worden geconfigureerd voor hoge beschikbaarheid. HSM-apparaten kunnen ook worden ingericht in verschillende regio's als oplossing voor een eventuele failover op regionaal niveau. Microsoft levert de Dedicated HSM service met behulp van de [Thales Luna 7 HSM-model A790-apparaten.](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) Dit apparaat biedt de hoogste prestatieniveaus en de beste opties voor cryptografische integratie. 

Nadat HSM-apparaten zijn ingericht, zijn ze rechtstreeks verbonden met het virtuele netwerk van een klant. Ze zijn dan ook toegankelijk voor on-premises hulpprogramma's voor toepassingen en beheer door een punt-naar-site-verbinding of site-naar-site-verbinding te configureren voor het VPN. Klanten krijgen de software en documentatie voor het configureren en beheren van HSM-apparaten via [de Thales-portal voor klantondersteuning.](https://supportportal.thalesgroup.com/csm)

## <a name="why-use-azure-dedicated-hsm"></a>Waarom Azure Dedicated HSM gebruiken?

### <a name="fips-140-2-level-3-compliance"></a>Naleving van FIPS 140-2 Niveau 3

Veel organisaties hebben strenge brancheregels die voorschrijven dat cryptografische sleutels moeten worden opgeslagen in [met FIPS 140-2 Level-3](https://csrc.nist.gov/publications/detail/fips/140/2/final) gevalideerde HMS's. Azure Dedicated HSM en een nieuwe aanbieding met één [tenant, Azure Key Vault Managed HSM,](https://docs.microsoft.com/azure/key-vault/managed-hsm)helpen klanten uit verschillende bedrijfstakken, zoals de financiële dienstverlening, overheidsinstanties en anderen, te voldoen aan fips 140-2 Niveau 3-vereisten. Hoewel de multiten tenant-service van Microsoft [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) momenteel gebruikmaakt van met FIPS 140-2 Level-2 gevalideerde HMS's. 

### <a name="single-tenant-devices"></a>Apparaten met één tenant

Veel van onze klanten hebben behoefte aan een cryptografisch opslagapparaat met een enkele tenant. Met de Azure Dedicated HSM-service kunnen ze een fysiek apparaat inrichten vanuit een van de wereldwijd gedistribueerde datacenters van Microsoft. Zodra dit apparaat is ingericht voor een klant, heeft alleen deze klant nog toegang tot het apparaat.

### <a name="full-administrative-control"></a>Volledig beheer

Veel klanten hebben behoefte aan volledig beheer en toegang tot hun apparaat die exclusief is bedoeld voor beheer. Nadat een apparaat is ingericht, heeft alleen de klant op beheer- of toepassingsniveau toegang tot het apparaat.

 Microsoft heeft geen administratieve controle meer nadat de klant de eerste keer toegang krijgt tot het apparaat en het wachtwoord wijzigt. Vanaf dit moment beschikt de klant over een enkele tenant met mogelijkheden voor volledig beheer en toepassingsbeheer. Microsoft behoudt wel toegang op bewakingsniveau (dit is geen beheerdersrol) voor telemetrie via een seriële poortverbinding. Deze toegang geldt voor hardwaremonitors zoals temperatuur, en de status van de voeding en van de ventilator. 
 
 Het staat de klant vrij om dit uit te schakelen. De klant ontvangt dan echter geen proactieve statusmeldingen meer van Microsoft.

### <a name="high-performance"></a>Hoge prestaties

Het Thales-apparaat is om verschillende redenen geselecteerd voor deze service. Het biedt brede ondersteuning voor cryptografische algoritmen, een verscheidenheid aan ondersteunde besturingssystemen en brede API-ondersteuning. Het specifieke model dat is geïmplementeerd, biedt uitstekende prestaties met 10.000 bewerkingen per seconde voor RSA-2048. Het biedt ondersteuning voor 10 partities die kunnen worden gebruikt voor unieke toepassingsexemplaren. Het is een apparaat met een lage latentie, hoge capaciteit en hoge doorvoer.

### <a name="unique-cloud-based-offering"></a>Unieke cloudaanbieding

Microsoft heeft een specifieke behoefte herkend bij een unieke groep klanten. Het is de enige cloudprovider die nieuwe klanten een toegewezen HSM-service biedt die is gevalideerd met FIPS 140-2 Niveau 3, en de enige die een dergelijke uitgebreide toepassingsintegratie biedt, zowel in de cloud als on-premises.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Is Azure Toegewezen HSM geschikt voor u?

Azure Dedicated HSM is een gespecialiseerde service die zich richt op de unieke vereisten van een specifiek type grootschalige organisaties. Het gevolg hiervan is dat de meeste Azure-klanten daarom waarschijnlijk niet beantwoorden aan het profiel voor het gebruik van deze service. Velen zullen de Azure Key Vault-service geschikter en kosteneffectiever vinden. We hebben de volgende criteria geïdentificeerd om u te helpen te bepalen of Azure Dedicated HSM geschikt is voor u.

### <a name="best-fit"></a>Geschikt voor

Azure Dedicated HSM is het meest geschikt voor lift-and-shift-scenario's waarvoor directe en exclusieve toegang tot HSM-apparaten is vereist. Enkele voorbeelden:

- Toepassingen migreren van on-premises naar Azure Virtual Machines
- Toepassingen migreren vanuit Amazon AWS EC2 naar virtuele machines die gebruikmaken van de service AWS Cloud HSM Classic (Amazon biedt deze aanbieding niet aan nieuwe klanten)
- Shrink-wrapped software uitvoeren, zoals Apache/Ngnix SSL Offload, Oracle TDE en ADCS in Azure Virtual Machines 

### <a name="not-a-fit"></a>Niet geschikt voor

Azure Dedicated HSM is niet geschikt voor de volgende typen scenario's: Microsoft-cloudservices die ondersteuning bieden voor versleuteling met door de klant beheerde sleutels (zoals Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL Database en Customer Key voor Office 365) en niet zijn geïntegreerd met Azure Dedicated HSM.

### <a name="it-depends"></a>Dat hangt ervan af

Of Azure Dedicated HSM geschikt is voor u, is afhankelijk van een complexe mengeling van vereisten en van de compromissen die wel of niet kunnen worden gesloten. Een voorbeeld is de vereiste voor FIPS 140-2 Niveau 3. Deze vereiste is gebruikelijk en Azure Dedicated HSM en een nieuwe aanbieding met één tenant, [zijn Azure Key Vault beheerde HSM](https://docs.microsoft.com/azure/key-vault/managed-hsm) momenteel de enige opties om er aan te voldoen. Als deze verplichte vereisten niet relevant zijn, is het vaak een keuze tussen Azure Key Vault en Azure Dedicated HSM. Bekijk wat uw vereisten zijn voordat u een keuze maakt.

Situaties waarin u uw opties moet afwegen, zijn onder andere: 

- Nieuwe code die wordt uitgevoerd op de virtuele Azure-machine van een klant
- SQL Server TDE in een virtuele Azure-machine
- Azure Storage-versleuteling aan de clientzijde
- SQL Server en Azure SQL DB Always Encrypted

## <a name="next-steps"></a>Volgende stappen

Dit is een zeer gespecialiseerde service. Het is daarom belangrijk dat u de belangrijkste concepten in deze documentatieset volledig begrijpt, inclusief de prijzen, de ondersteuning en de serviceovereenkomsten. 

De [Thales-integratiehandleidingen](https://cpl.thalesgroup.com/partners/overview) helpen u bij het inrichten van HMS's in een bestaande virtuele netwerkomgeving. Er zijn ook handleidingen om u te helpen bepalen hoe u uw implementatiearchitectuur moet instellen.

* [Hoge beschikbaarheid](high-availability.md)
* [Fysieke beveiliging](physical-security.md)
* [Netwerken](networking.md)
* [Ondersteuning](supportability.md)
* [Controle](monitoring.md)
