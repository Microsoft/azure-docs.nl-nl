---
title: Meer informatie over Azure Image Builder (preview-versie)
description: Meer informatie over Azure Image Builder voor virtuele machines in Azure.
author: danielsollondon
ms.author: danis
ms.date: 03/05/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 20bb6925f859d497046eb42bbafb5264826b77b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604063"
---
# <a name="preview-azure-image-builder-overview"></a>Voor beeld: overzicht van Azure Image Builder

Met gestandaardiseerde installatie kopieën van virtuele machines kunnen organisaties migreren naar de Cloud en zorgen voor consistentie in de implementaties. Installatie kopieën bevatten doorgaans vooraf gedefinieerde beveiligings-en configuratie-instellingen en de benodigde software. Voor het instellen van uw eigen Imaging-pijp lijn zijn tijd, infra structuur en configuratie vereist, maar met de opbouw functie voor Azure VM-installatie kopieën kunt u een configuratie met een beschrijving van de installatie kopie opgeven, deze verzenden naar de service en de installatie kopie bouwen en distribueren.
 
Met de opbouw functie voor installatie kopieën van Azure VM (Azure Image Builder) kunt u beginnen met een Azure Marketplace-installatie kopie op basis van Windows of Linux, bestaande aangepaste installatie kopieën en beginnen met het toevoegen van uw eigen aanpassingen. Omdat de opbouw functie voor installatie kopieën is gebaseerd op [HashiCorp Packer](https://packer.io/) , ziet u enkele overeenkomsten, maar hebt u het voor deel van een beheerde service. U kunt ook opgeven waar u wilt dat uw installatie kopieën worden gehost, in de [Galerie met gedeelde installatie kopieën van Azure](shared-image-galleries.md)als een beheerde installatie kopie of een VHD.

> [!IMPORTANT]
> Azure Image Builder is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="preview-features"></a>Preview-functies

Voor de preview worden de volgende functies ondersteund:

- Het maken van basislijn installatie kopieën, die uw minimale beveiligings-en bedrijfs configuraties bevatten, en die afdelingen toestaan om deze te wijzigen.
- De integratie van de kern toepassingen, zodat de VM na het maken van de werk belastingen kan uitvoeren of configuraties kan toevoegen ter ondersteuning van installatie kopieën van virtuele Windows-Bureau bladen.
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

## <a name="how-it-works"></a>Uitleg

De opbouw functie voor installatie kopieën van Azure VM is een volledig beheerde Azure-service die toegankelijk is voor een Azure-resource provider. Geef een configuratie op voor de service waarmee de bron installatie kopie, de aanpassing van de uitvoering en de locatie van de nieuwe afbeelding worden gedistribueerd. in het onderstaande diagram ziet u een werk stroom op hoog niveau:

![Conceptuele tekening van het Azure Image Builder-proces met de bronnen (Windows/Linux), aanpassingen (shell, Power shell, Windows opnieuw starten & update, bestanden toevoegen) en wereld wijde distributie met de galerie met gedeelde Azure-afbeeldingen](./media/image-builder-overview/image-builder-flow.png)

Sjabloon configuraties kunnen worden door gegeven met behulp van Power shell, AZ CLI, ARM-sjablonen en met behulp van de Azure VM Image Builder DevOps-taak, wanneer u deze indient bij de service, wordt er een bron voor de afbeeldings sjabloon gemaakt. Wanneer de bron van de afbeeldings sjabloon wordt gemaakt, ziet u een faserings resource groep die is gemaakt in uw abonnement, in de indeling: IT_ \<DestinationResourceGroup> _\<TemplateName>_ \( GUID). De resource groep staging bevat bestanden en scripts waarnaar wordt verwezen in het bestand, Shell, Power shell-aanpassing in de eigenschap ScriptURI.

Voor het uitvoeren van de build die u aanroept `Run` op de bron van de afbeeldings sjabloon, implementeert de service vervolgens aanvullende bronnen voor de build, zoals een VM, netwerk, schijf, netwerk adapter, enzovoort. Als u een installatie kopie bouwt zonder gebruik te maken van een bestaande VNET Image Builder, worden er ook een openbaar IP-en NSG geïmplementeerd, de service maakt verbinding met de build-VM met SSH of WinRM. Als u een bestaand VNET selecteert, wordt de service geïmplementeerd met behulp van een persoonlijke Azure-koppeling en is er geen openbaar IP-adres vereist. Raadpleeg de [Details](./linux/image-builder-networking.md)voor meer informatie over het uitvoeren van een image builder-netwerk.

Wanneer de build alle resources heeft verwijderd, met uitzonde ring van de faserings resource groep en het opslag account, verwijdert u deze om deze te verwijderen. u kunt ook de bron van de afbeeldings sjabloon verwijderen, of deze laten staan om de build opnieuw uit te voeren.

Er zijn meerdere voor beelden en stapsgewijze hand leidingen in deze documentatie, die verwijzen naar configuratie sjablonen en oplossingen in de [Azure Image Builder github-opslag plaats](https://github.com/azure/azvmimagebuilder).

### <a name="move-support"></a>Ondersteuning voor verplaatsen
De bron van de afbeeldings sjabloon kan onveranderbaar zijn en bevat koppelingen naar resources en de resource groep staging. het bron type biedt daarom geen ondersteuning voor het verplaatsen van de bron. Als u de bron van de afbeeldings sjabloon wilt verplaatsen, moet u ervoor zorgen dat u beschikt over een kopie van de configuratie sjabloon (als u de bestaande configuratie van de resource hebt opgehaald als u deze niet hebt), een nieuwe bron voor een afbeeldings sjabloon maken in de nieuwe resource groep met een nieuwe naam en de vorige afbeeldings sjabloon resource verwijderen. 

## <a name="permissions"></a>Machtigingen
Wanneer u zich registreert voor de (AIB), verleent dit de AIB-service toestemming voor het maken, beheren en verwijderen van een staging-resource groep (IT_ *), en hebt u rechten om resources toe te voegen, die vereist zijn voor het bouwen van de installatie kopie. Dit wordt gedaan door een AIB-SPN (Service Principal Name) die tijdens een geslaagde registratie beschikbaar wordt gesteld in uw abonnement.

Als u wilt dat Azure VM Image Builder installatie kopieën naar de beheerde installatie kopieën of naar een galerie met gedeelde installatie kopieën kan distribueren, moet u een door de gebruiker toegewezen Azure-identiteit maken die machtigingen heeft voor het lezen en schrijven van installatie kopieën. Als u toegang hebt tot Azure Storage, hebt u machtigingen nodig om persoonlijke en open bare containers te lezen.

Machtigingen worden uitgebreid beschreven in [Power shell](./linux/image-builder-permissions-powershell.md)en [AZ cli](./linux/image-builder-permissions-cli.md).

## <a name="costs"></a>Kosten
U maakt een aantal reken-, netwerk-en opslag kosten bij het maken, maken en opslaan van installatie kopieën met Azure Image Builder. Deze kosten zijn vergelijkbaar met de kosten die zijn gemaakt bij het hand matig maken van aangepaste installatie kopieën. Voor de resources worden er kosten in rekening gebracht voor uw Azure-tarieven. 

Tijdens het maken van de installatie kopie worden bestanden gedownload en opgeslagen in de `IT_<DestinationResourceGroup>_<TemplateName>` resource groep, waardoor er een kleine opslag kosten in rekening worden gebracht. Als u deze niet wilt blijven gebruiken, verwijdert u de **afbeeldings sjabloon** na het maken van de installatie kopie.
 
Met de opbouw functie voor installatie kopieën wordt een VM gemaakt met behulp van een D1v2 VM-grootte en de opslag en netwerken die nodig zijn voor de virtuele machine. Deze resources duren de duur van het bouw proces en worden verwijderd wanneer de installatie kopie is gemaakt met de opbouw functie voor installatie kopieën. 
 
De installatie kopie wordt door Azure Image Builder gedistribueerd naar de gekozen regio's. Dit kan leiden tot uitstaande netwerk kosten.

## <a name="hyper-v-generation"></a>Hyper-V generatie
De opbouw functie voor installatie kopieën ondersteunt momenteel alleen systeem eigen ondersteuning voor het maken van Hyper-V-generatie (gen1) 1 installatie kopieën in de Azure Shared Image Gallery (SIG) of een beheerde installatie kopie. 
 
## <a name="next-steps"></a>Volgende stappen 
 
Zie de artikelen voor het bouwen van [Linux](./linux/image-builder.md) -of [Windows](./windows/image-builder.md) -installatie kopieën om de Azure Image Builder uit te proberen.