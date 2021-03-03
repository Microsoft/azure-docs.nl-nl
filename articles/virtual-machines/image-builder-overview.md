---
title: Meer informatie over Azure Image Builder (preview-versie)
description: Meer informatie over Azure Image Builder voor virtuele machines in Azure.
author: danielsollondon
ms.author: danis
ms.date: 05/02/2019
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.reviewer: cynthn
ms.openlocfilehash: 1c70edfc3bad2be70d26c71736ca06fcc4a8dcdb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672511"
---
# <a name="preview-azure-image-builder-overview"></a>Voor beeld: overzicht van Azure Image Builder

Met gestandaardiseerde installatie kopieën van virtuele machines kunnen organisaties migreren naar de Cloud en zorgen voor consistentie in de implementaties. Installatie kopieën bevatten doorgaans vooraf gedefinieerde beveiligings-en configuratie-instellingen en de benodigde software. Voor het instellen van uw eigen Imaging-pijp lijn zijn tijd, infra structuur en configuratie vereist, maar met Azure VM Image Builder kunt u een eenvoudige configuratie bieden waarmee uw installatie kopie wordt beschreven, verzonden naar de service en de installatie kopie wordt gebouwd en gedistribueerd.
 
Met de opbouw functie voor installatie kopieën van Azure VM (Azure Image Builder) kunt u beginnen met een Azure Marketplace-installatie kopie op basis van Windows of Linux, bestaande aangepaste installatie kopieën of Red Hat Enterprise Linux (RHEL) ISO en begint met het toevoegen van uw eigen aanpassingen. Omdat de opbouw functie voor installatie kopieën is gebaseerd op [HashiCorp Packer](https://packer.io/), kunt u ook uw bestaande scripts voor de shell-inrichtings pakket importeren. U kunt ook opgeven waar u wilt dat uw installatie kopieën worden gehost, in de [Galerie met gedeelde installatie kopieën van Azure](shared-image-galleries.md)als een beheerde installatie kopie of een VHD.

> [!IMPORTANT]
> Azure Image Builder is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="preview-features"></a>Preview-functies

Voor de preview worden de volgende functies ondersteund:

- Het maken van gouden Baseline-installatie kopieën, die uw minimale beveiligings-en bedrijfs configuraties bevatten, en die afdelingen in staat stellen om deze verder aan hun behoeften aan te passen.
- Als u bestaande installatie kopieën wilt bijwerken, kunt u met Image Builder voortdurend bestaande aangepaste installatie kopieën bijwerken.
- Verbind de opbouw functie voor installatie kopieën met uw bestaande virtuele netwerken, zodat u verbinding kunt maken met bestaande configuratie servers (DSC, chef, puppet enz.), bestands shares of andere Routeer bare servers/Services.
- Dankzij de integratie met de galerie met gedeelde Azure-afbeeldingen kunt u installatie kopieën globaal distribueren, versies en schalen, en beschikt u over een systeem voor het beheer van installatie kopieën.
- Integratie met bestaande installatie kopieën bouw pijp lijnen, roep de opbouw functie voor installatie kopieën aan vanuit uw pijp lijn, of gebruik de Azure DevOps-taak voor de preview-versie van de voorbeeld weergave van installatie kopieën.
- Migreer een bestaande pijp lijn voor het aanpassen van een installatie kopie naar Azure. Gebruik uw bestaande scripts, opdrachten en processen om installatie kopieën aan te passen.
- Installatie kopieën maken in VHD-indeling ter ondersteuning van Azure Stack.
 

## <a name="regions"></a>Regio's
De Azure Image Builder-service is beschikbaar voor een preview in deze regio's. Installatie kopieën kunnen buiten deze regio's worden gedistribueerd.
- VS - oost
- VS - oost 2
- VS - west-centraal
- VS - west
- VS - west 2
- Europa - noord
- Europa -west

## <a name="os-support"></a>Ondersteuning voor besturings systeem
AIB biedt ondersteuning voor Azure Marketplace Base OS-basis installatie kopieën:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6, 7,7
- CentOS 7,6, 7,7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enter prise/Enter prise multi-sessie/Professional
- Windows 2016
- Windows 2019

Ondersteuning voor RHEL Iso's wordt niet meer ondersteund.

## <a name="how-it-works"></a>Uitleg

De opbouw functie voor installatie kopieën van Azure is een volledig beheerde Azure-service die toegankelijk is voor een Azure-resource provider. Het Azure Image Builder-proces bestaat uit drie hoofd onderdelen: bron, aanpassen en distribueren. deze worden weer gegeven in een sjabloon. In het onderstaande diagram ziet u de onderdelen, met een aantal eigenschappen. 
 

**Image Builder-proces** 

![Conceptuele tekening van het Azure Image Builder-proces](./media/image-builder-overview/image-builder-process.png)

1. Maak de afbeeldings sjabloon als een. JSON-bestand. Dit JSON-bestand bevat informatie over de afbeeldings bron, aanpassingen en distributie. Er zijn meerdere voor beelden in de [Azure Image Builder github-opslag plaats](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Verzend het naar de service. Hiermee wordt een afbeeldings sjabloon artefact gemaakt in de resource groep die u opgeeft. Op de achtergrond downloadt de opbouw functie voor afbeeldingen de bron installatie kopie of ISO, en scripts als dat nodig is. Deze worden opgeslagen in een afzonderlijke resource groep die automatisch wordt gemaakt in uw abonnement, in de indeling: IT_ \<DestinationResourceGroup> _ \<TemplateName> . 
1. Zodra de afbeeldings sjabloon is gemaakt, kunt u de installatie kopie maken. In de opbouw functie voor achtergrond gebruiken de sjabloon en bron bestanden voor het maken van een virtuele machine (standaard grootte: Standard_D1_v2), netwerk, openbaar IP-adres, NSG en opslag in de \<DestinationResourceGroup> \<TemplateName> resource groep IT_ _.
1. Als onderdeel van het maken van de installatie kopie distribueert Image Builder de installatie kopie op basis van de sjabloon, waarna de extra resources worden verwijderd uit de \<DestinationResourceGroup> \<TemplateName> resource groep IT_ _ die voor het proces is gemaakt.


## <a name="permissions"></a>Machtigingen
Wanneer u zich registreert voor de (AIB), verleent dit de AIB-service toestemming voor het maken, beheren en verwijderen van een staging-resource groep (IT_ *), en hebt u rechten om resources toe te voegen, die vereist zijn voor het bouwen van de installatie kopie. Dit wordt gedaan door een AIB-SPN (Service Principal Name) die tijdens een geslaagde registratie beschikbaar wordt gesteld in uw abonnement.

Als u wilt dat Azure VM Image Builder installatie kopieën naar de beheerde installatie kopieën of naar een galerie met gedeelde installatie kopieën kan distribueren, moet u een door de gebruiker toegewezen Azure-identiteit maken die machtigingen heeft voor het lezen en schrijven van installatie kopieën. Als u toegang hebt tot Azure Storage, hebt u machtigingen nodig om persoonlijke containers te lezen.

In eerste instantie moet u de [aan de gebruiker toegewezen beheerde identiteits documentatie voor Azure maken](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) volgen voor het maken van een identiteit.

Als u de identiteit hebt die u nodig hebt om deze machtigingen te verlenen, kunt u dit doen door een aangepaste functie definitie van Azure te gebruiken en vervolgens de door de gebruiker toegewezen beheerde identiteit toe te wijzen om de aangepaste roldefinitie te gebruiken.

De machtigingen worden [hier](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements)uitgebreid beschreven en de voor beelden laten zien hoe dit wordt geïmplementeerd.

> [!Note]
> Eerder met AIB zou u de SPN van AIB gebruiken en de SPN-machtigingen verlenen aan de installatie kopie resource groepen. We gaan weg van dit model om toekomstige mogelijkheden te bieden. Van 26 mei 2020, de opbouw functie voor installatie kopieën accepteert geen sjablonen die geen door de gebruiker toegewezen identiteit hebben, bestaande sjablonen moeten opnieuw worden ingediend bij de service met een [gebruikers identiteit](./linux/image-builder-json.md). In deze voor beelden ziet u al hoe u een door de gebruiker toegewezen identiteit kunt maken en toevoegen aan een sjabloon. Raadpleeg deze [documentatie](https://github.com/danielsollondon/azvmimagebuilder#service-updates-and-latest-release-information) voor meer informatie over deze wijziging en releases van updates.

## <a name="costs"></a>Kosten
U maakt een aantal reken-, netwerk-en opslag kosten bij het maken, maken en opslaan van installatie kopieën met Azure Image Builder. Deze kosten zijn vergelijkbaar met de kosten die zijn gemaakt bij het hand matig maken van aangepaste installatie kopieën. Voor de resources worden er kosten in rekening gebracht voor uw Azure-tarieven. 

Tijdens het maken van de installatie kopie worden bestanden gedownload en opgeslagen in de `IT_<DestinationResourceGroup>_<TemplateName>` resource groep, waardoor er een kleine opslag kosten in rekening worden gebracht. Als u deze niet wilt blijven gebruiken, verwijdert u de **afbeeldings sjabloon** na het maken van de installatie kopie.
 
Met de opbouw functie voor installatie kopieën wordt een VM gemaakt met behulp van een D1v2 VM-grootte en de opslag en netwerken die nodig zijn voor de virtuele machine. Deze resources duren de duur van het bouw proces en worden verwijderd wanneer de installatie kopie is gemaakt met de opbouw functie voor installatie kopieën. 
 
De installatie kopie wordt door Azure Image Builder gedistribueerd naar de gekozen regio's. Dit kan leiden tot uitstaande netwerk kosten.

## <a name="hyper-v-generation"></a>Hyper-V generatie
De opbouw functie voor installatie kopieën ondersteunt momenteel alleen systeem eigen ondersteuning voor het maken van Hyper-V-generatie (gen1) 1 installatie kopieën in de Azure Shared Image Gallery (SIG) of een beheerde installatie kopie. Als u Gen2-installatie kopieën wilt maken, moet u een Gen2-bron installatie kopie gebruiken en distribueren naar VHD. Daarna moet u een beheerde installatie kopie maken van de VHD en deze in de SIG injecteren als een Gen2-installatie kopie.
 
## <a name="next-steps"></a>Volgende stappen 
 
Zie de artikelen voor het bouwen van [Linux](./linux/image-builder.md) -of [Windows](./windows/image-builder.md) -installatie kopieën om de Azure Image Builder uit te proberen.

