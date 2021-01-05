---
title: 'Azure VMware-oplossing door CloudSimple: beveiliging voor CloudSimple-Services'
description: Beschrijft de modellen van gedeelde verantwoordelijkheden voor de beveiliging van CloudSimple-Services
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d62eec72a884470ade03897a0533e62c6f1b0f71
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898790"
---
# <a name="cloudsimple-security-overview"></a>Overzicht van CloudSimple-beveiliging

Dit artikel bevat een overzicht van de manier waarop beveiliging wordt geïmplementeerd op de Azure VMware-oplossing door CloudSimple service, infra structuur en Data Center. Meer informatie over gegevens beveiliging en beveiliging, netwerk beveiliging en hoe beveiligings problemen en patches worden beheerd.

## <a name="shared-responsibility"></a>Gedeelde verantwoordelijkheid

Azure VMware-oplossing per CloudSimple gebruikt een gedeeld verantwoordelijkheids model voor beveiliging. Vertrouwde beveiliging in de Cloud wordt bereikt door de gedeelde verantwoordelijkheden van klanten en micro soft als een service provider. Deze matrix van verantwoordelijkheid biedt betere beveiliging en elimineert individuele storings punten.

## <a name="azure-infrastructure"></a>Azure-infrastructuur

Beveiligings overwegingen voor Azure-infra structuur omvatten de data centers en de locatie van het apparaat.

### <a name="datacenter-security"></a>Beveiliging van data centers

Micro soft heeft een volledige afdeling voor het ontwerpen, bouwen en gebruiken van de fysieke faciliteiten die ondersteuning bieden voor Azure. Dit team is geïnvesteerd in het onderhouden van geavanceerde fysieke beveiliging. Zie [Azure-faciliteiten,-premises en fysieke beveiliging](../security/fundamentals/physical-security.md)voor meer informatie over de fysieke beveiliging.

### <a name="equipment-location"></a>Locatie van apparatuur

De bare-metal hardware-apparatuur die uw Privécloud uitvoert, wordt gehost op Azure Data Center-locaties.  De kooien waar dat apparaat is, vereist biometrische twee ledige verificatie om toegang te krijgen.

## <a name="dedicated-hardware"></a>Toegewezen hardware

Als onderdeel van de CloudSimple-service krijgen alle CloudSimple-klanten toegewezen bare metal-hosts met lokale gekoppelde schijven die fysiek zijn geïsoleerd van andere Tenant-hardware. Een ESXi-Hyper Visor met vSAN wordt uitgevoerd op elk knoop punt. De knoop punten worden beheerd via een door de klant toegewezen VMware vCenter-en NSX. Het delen van hardware tussen tenants biedt geen extra laag voor isolatie en beveiliging.

## <a name="data-security"></a>Gegevensbeveiliging

Klanten houden toezicht en eigendom van hun gegevens. Gegevens opslag van klant gegevens is de verantwoordelijkheid van de klant.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Gegevens beveiliging voor gegevens in rust tijd en gegevens in beweging in interne netwerken

Voor Data-at-rest in de Privécloud-omgeving kunt u vSAN-versleuteling gebruiken. vSAN-versleuteling werkt met VMware Certified External Key Management servers (KMS) in uw eigen virtuele netwerk of on-premises.  U beheert de gegevens versleutelings sleutels zelf. Voor gegevens in beweging in de Privécloud ondersteunt vSphere versleuteling van gegevens via de kabel voor al het vmkernel-verkeer (inclusief vMotion-verkeer).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Gegevens beveiliging voor gegevens die nodig zijn om te scha kelen via open bare netwerken

Voor het beveiligen van gegevens die via open bare netwerken worden verplaatst, kunt u IPsec-en TLS VPN-tunnels maken voor uw Privécloud. Algemene versleutelings methoden worden ondersteund, met inbegrip van 128-byte en 256-bytes AES. Gegevens die onderweg zijn (inclusief verificatie, beheerders toegang en klant gegevens) worden versleuteld met standaard versleutelings mechanismen (SSH, TLS 1,2 en Secure RDP). Communicatie waarbij gevoelige informatie wordt getransporteerd, maakt gebruik van de standaard versleutelings mechanismen.

### <a name="secure-disposal"></a>Beveiligde verwijdering

Als uw CloudSimple-service verloopt of wordt beëindigd, bent u verantwoordelijk voor het verwijderen of verwijderen van uw gegevens. CloudSimple werkt samen met u bij het verwijderen of retour neren van alle klant gegevens zoals die zijn opgegeven in de klant overeenkomst, met uitzonde ring van de mate waarin CloudSimple is vereist door toepasselijk recht om enkele of alle persoons gegevens te bewaren. Als dit nodig is om persoonlijke gegevens te behouden, worden de gegevens door CloudSimple gearchiveerd en worden er redelijke maat regelen genomen om te voor komen dat de klant gegevens worden verwerkt.

### <a name="data-location"></a>Locatie van gegevens

Bij het instellen van uw persoonlijke Clouds kiest u de Azure-regio waar ze worden geïmplementeerd. Virtuele VMware-machine gegevens worden niet verplaatst van dat fysieke Data Center tenzij u gegevens migratie of externe gegevens back-up uitvoert. U kunt werk belastingen ook hosten en gegevens opslaan in meerdere Azure-regio's, indien van toepassing op uw behoeften.

De klant gegevens die zich in de Privécloud bevinden, Hyper-geconvergeerde knoop punten passeren geen locaties zonder de expliciete actie van de Tenant beheerder. Het is uw verantwoordelijkheid om uw workloads op een Maxi maal beschik bare manier te implementeren.

### <a name="data-backups"></a>Gegevensback-ups

CloudSimple maakt geen back-up van en archiveert geen klant gegevens. CloudSimple voert periodieke back-ups van vCenter-en NSX-gegevens uit om hoge Beschik baarheid van beheerser vers te bieden. Voordat de back-up wordt gemaakt, worden alle gegevens versleuteld op de vCenter-bron met behulp van VMware-Api's. De versleutelde gegevens worden getransporteerd en opgeslagen in Azure Blob. Versleutelings sleutels voor back-ups worden opgeslagen in een zeer veilige CloudSimple beheerde kluis die wordt uitgevoerd in het virtuele CloudSimple-netwerk in Azure.

## <a name="network-security"></a>Netwerkbeveiliging

De CloudSimple-oplossing is afhankelijk van lagen van netwerk beveiliging.

### <a name="azure-edge-security"></a>Beveiliging van Azure Edge

De CloudSimple-Services zijn gebaseerd op de basis netwerk beveiliging die wordt geboden door Azure. Azure past ingrijpende technieken toe voor detectie en tijdig antwoord op aanvallen op basis van het netwerk die zijn gekoppeld aan afwijkende ingangen of uitvoerige verkeers patronen en gedistribueerde Denial-of-service-aanvallen (DDoS). Dit beveiligings beheer is van toepassing op de omgevingen van de Privécloud en de software voor het beheer vlak die is ontwikkeld door CloudSimple.

### <a name="segmentation"></a>Segmentatie

De CloudSimple-service heeft logische afzonderlijke laag 2-netwerken die de toegang tot uw eigen particuliere netwerken in uw privécloud-omgeving beperken. U kunt uw particuliere Cloud netwerken verder beveiligen met behulp van een firewall. Met de CloudSimple-Portal kunt u de regels voor het gebruik van ieuwe en NS-netwerk verkeer definiëren voor alle netwerk verkeer, met inbegrip van intra particuliere cloud verkeer, verkeer tussen particuliere Clouds, algemeen verkeer naar het internet en netwerk verkeer naar on-premises via IPsec VPN of ExpressRoute-verbinding.

## <a name="vulnerability-and-patch-management"></a>Beveiligings problemen en patch beheer

CloudSimple is verantwoordelijk voor periodieke beveiligings patches van beheerde VMware-software (ESXi, vCenter en NSX).

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer

Klanten kunnen zich verifiëren bij hun Azure-account (in azure AD) met behulp van multi-factor Authentication of SSO als voor keur. Vanuit het Azure Portal kunt u de CloudSimple-Portal starten zonder referenties opnieuw in te voeren.

CloudSimple ondersteunt een optionele configuratie van een identiteits bron voor de Privécloud. U kunt een [on-premises identiteits bron](set-vcenter-identity.md), een nieuwe id-bron voor de Privécloud of [Azure AD](azure-ad.md)gebruiken.

Klanten krijgen standaard de bevoegdheden die nodig zijn voor de dagelijkse bewerkingen van vCenter in de Privécloud. Dit machtigings niveau bevat geen beheerders toegang tot vCenter. Als beheerders toegang tijdelijk vereist is, kunt u [uw bevoegdheden](escalate-private-cloud-privileges.md) voor een beperkte periode escaleren tijdens het volt ooien van de beheer taken.
