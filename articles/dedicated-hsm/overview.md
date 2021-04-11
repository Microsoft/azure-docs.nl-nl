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
ms.openlocfilehash: 0e07839c3c5ce542335eeadc92e6a3c98fe87856
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606977"
---
# <a name="what-is-azure-dedicated-hsm"></a>Wat is Azure Toegewezen HSM?

Azure Toegewezen HSM is een Azure-service die opslag van cryptografische sleutels biedt in Azure. Toegewezen HSM voldoet aan de strengste beveiligingseisen. Dit is de ideale oplossing voor klanten die apparaten willen gebruiken die zijn gevalideerd met FIPS 140-2 Niveau 3 en die volledige en exclusieve controle over het HSM-apparaat willen. 

 HSM-apparaten zijn wereldwijd geïmplementeerd in verschillende Azure-regio's. Ze kunnen eenvoudig worden ingericht als een paar apparaten en worden geconfigureerd voor hoge beschikbaarheid. HSM-apparaten kunnen ook worden ingericht in verschillende regio's als oplossing voor een eventuele failover op regionaal niveau. Micro soft levert de toegewezen HSM-service met behulp van het [Thales Luna 7 HSM model A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) -apparaten. Dit apparaat biedt de hoogste prestatieniveaus en de beste opties voor cryptografische integratie. 

Nadat HSM-apparaten zijn ingericht, zijn ze rechtstreeks verbonden met het virtuele netwerk van een klant. Ze zijn dan ook toegankelijk voor on-premises hulpprogramma's voor toepassingen en beheer door een punt-naar-site-verbinding of site-naar-site-verbinding te configureren voor het VPN. Klanten krijgen de software en documentatie voor het configureren en beheren van HSM-apparaten vanuit de [Thales-portal voor klanten ondersteuning](https://supportportal.thalesgroup.com/csm).

## <a name="why-use-azure-dedicated-hsm"></a>Waarom Azure Dedicated HSM gebruiken?

### <a name="fips-140-2-level-3-compliance"></a>Naleving van FIPS 140-2 Niveau 3

Veel organisaties hebben strenge industriële voor Schriften die bepalen dat cryptografische sleutels moeten worden opgeslagen in het [FIPS 140-2-](https://csrc.nist.gov/publications/detail/fips/140/2/final) gevalideerde hsm's niveau 3. Met Azure dedicated HSM en een nieuwe aanbieding voor eenmalige tenants, [Azure Key Vault beheerde HSM (preview)](https://docs.microsoft.com/azure/key-vault/managed-hsm), helpen klanten van verschillende branche segmenten, zoals financiële dienst verleners, overheids instanties en anderen voldoen aan de vereisten van het niveau 3 van FIPS 140-2. Hoewel de multi tenant-service voor [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) van micro soft momenteel gebruikmaakt van FIPS 140-2-hsm's niveau 2 gevalideerd. 

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

Of Azure Dedicated HSM geschikt is voor u, is afhankelijk van een complexe mengeling van vereisten en van de compromissen die wel of niet kunnen worden gesloten. Een voorbeeld is de vereiste voor FIPS 140-2 Niveau 3. Deze vereiste is gebruikelijk, en Azure dedicated HSM en een nieuwe aanbieding voor eenmalige tenants, [Azure Key Vault Managed HSM (preview)](https://docs.microsoft.com/azure/key-vault/managed-hsm) is momenteel de enige opties voor het beantwoorden van deze. Als deze verplichte vereisten niet relevant zijn, is het vaak een keuze tussen Azure Key Vault en een specifieke HSM van Azure. Bekijk wat uw vereisten zijn voordat u een keuze maakt.

Situaties waarin u uw opties moet afwegen, zijn onder andere: 

- Nieuwe code die wordt uitgevoerd op de virtuele Azure-machine van een klant
- SQL Server TDE in een virtuele Azure-machine
- Azure Storage-versleuteling aan de clientzijde
- SQL Server en Azure SQL DB Always Encrypted

## <a name="next-steps"></a>Volgende stappen

Dit is een zeer gespecialiseerde service. Het is daarom belangrijk dat u de belangrijkste concepten in deze documentatieset volledig begrijpt, inclusief de prijzen, de ondersteuning en de serviceovereenkomsten. 

Met de [Thales-integratie handleidingen](https://cpl.thalesgroup.com/partners/overview) kunt u het inrichten van hsm's naar een bestaande virtuele netwerk omgeving vereenvoudigen. Er zijn ook instructies om u te helpen bepalen hoe u uw implementatie architectuur kunt instellen.

* [Hoge beschikbaarheid](high-availability.md)
* [Fysieke beveiliging](physical-security.md)
* [Netwerken](networking.md)
* [Ondersteuning](supportability.md)
* [Controle](monitoring.md)
