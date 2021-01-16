---
title: Ondersteuning voor netwerk bestandssysteem 3,0 in Azure Blob-opslag (preview) | Microsoft Docs
description: Blob-opslag ondersteunt nu het NFS-protocol (Network File System) 3,0. Met deze ondersteuning kunnen Windows-en Linux-clients een container koppelen in Blob Storage vanaf een virtuele Azure-machine (VM) of een computer die on-premises wordt uitgevoerd.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 52f7b328b013fd520787fca420a45ffdc5e9d9b1
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250805"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Ondersteuning voor het protocol Network File System (NFS) 3,0 in Azure Blob-opslag (preview-versie)

Blob-opslag ondersteunt nu het NFS-protocol (Network File System) 3,0. Met deze ondersteuning kunnen Windows-of Linux-clients een container koppelen in Blob Storage vanaf een virtuele Azure-machine (VM) of een computer on-premises. 

> [!NOTE]
> Ondersteuning voor NFS 3,0-protocol in Azure Blob-opslag is in open bare preview. Het biedt ondersteuning voor GPV2-opslag accounts met de prestaties van de Standard-laag in de volgende regio's: Australië-oost, Korea-centraal en Zuid-Centraal vs. De preview biedt ook ondersteuning voor blok-blobs met een Premium-prestatie niveau in alle open bare regio's.

## <a name="general-workflow-mounting-a-storage-account-container"></a>Algemene werk stroom: een opslag account container koppelen

Als u een opslag account container wilt koppelen, moet u deze dingen doen.

1. De NFS 3,0-protocol functie registreren bij uw abonnement.

2. Controleer of de functie is geregistreerd.

3. Maak een Azure-Virtual Network (VNet).

4. Configureer netwerk beveiliging.

5. Maak en configureer een opslag account dat alleen verkeer van het VNet accepteert.

6. Maak een container in het opslag account.

7. Koppel de container.

Zie [Blob Storage koppelen met behulp van het NFS-protocol (Network File System) 3,0 (preview)](network-file-system-protocol-support-how-to.md)voor stapsgewijze instructies.

> [!IMPORTANT]
> Het is belang rijk om deze taken uit te voeren in de juiste volg orde. U kunt geen containers koppelen die u maakt voordat u het NFS 3,0-protocol inschakelt voor uw account. Nadat u het NFS 3,0-protocol hebt ingeschakeld voor uw account, kunt u dit ook niet uitschakelen.

## <a name="network-security"></a>Netwerkbeveiliging

Uw opslag account moet zich in een VNet bevinden. Met een VNet kunnen clients veilig verbinding maken met uw opslag account. De enige manier om de gegevens in uw account te beveiligen, is door gebruik te maken van een VNet en andere netwerk beveiligings instellingen. Elk ander hulp programma dat wordt gebruikt om gegevens te beveiligen, waaronder account sleutel autorisatie, Azure Active Directory (AD)-beveiliging en toegangs beheer lijsten (Acl's), worden nog niet ondersteund in accounts waarvoor ondersteuning voor NFS 3,0-protocol is ingeschakeld. 

Zie [aanbevelingen voor netwerk beveiliging voor Blob Storage voor](security-recommendations.md#networking)meer informatie.

## <a name="supported-network-connections"></a>Ondersteunde netwerk verbindingen

Een client kan verbinding maken via een openbaar of een [persoonlijk eind punt](../common/storage-private-endpoints.md)en kan verbinding maken vanaf een van de volgende netwerk locaties:

- Het VNet dat u configureert voor uw opslag account. 

  Voor het doel van dit artikel verwijzen we naar die VNet als het *primaire vnet*. Zie [toegang verlenen vanuit een virtueel netwerk](../common/storage-network-security.md#grant-access-from-a-virtual-network)voor meer informatie.

- Een gepeerd VNet dat zich in dezelfde regio als het primaire VNet bevindt.

  U moet uw opslag account configureren om toegang tot dit gepeerde VNet toe te staan. Zie [toegang verlenen vanuit een virtueel netwerk](../common/storage-network-security.md#grant-access-from-a-virtual-network)voor meer informatie.

- Een on-premises netwerk dat is verbonden met uw primaire VNet met behulp van [VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) of een [ExpressRoute-gateway](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md). 

  Zie [toegang vanaf on-premises netwerken configureren](../common/storage-network-security.md#configuring-access-from-on-premises-networks)voor meer informatie.

- Een on-premises netwerk dat is verbonden met een peered netwerk.

  U kunt dit doen met behulp van [VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) of een [ExpressRoute-gateway](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) samen met [gateway-door Voer](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit). 

> [!IMPORTANT]
> Als u verbinding maakt vanaf een on-premises netwerk, moet u ervoor zorgen dat de client uitgaande communicatie toestaat via de poorten 111 en 2048. Het protocol NFS 3,0 maakt gebruik van deze poorten.

## <a name="azure-storage-features-not-yet-supported"></a>Azure Storage functies die nog niet worden ondersteund

De volgende Azure Storage-functies worden niet ondersteund wanneer u het NFS 3,0-protocol inschakelt voor uw account. 

- Azure Active Directory (AD)-beveiliging

- POSIX-achtige Access Control Lists (Acl's)

- De mogelijkheid om NFS 3,0-ondersteuning op bestaande opslag accounts in te scha kelen

- De mogelijkheid om NFS 3,0-ondersteuning uit te scha kelen in een opslag account (nadat u deze hebt ingeschakeld)

- Mogelijkheid om te schrijven naar blobs door REST Api's of Sdk's te gebruiken. 
  
## <a name="nfs-30-features-not-yet-supported"></a>NFS 3,0-functies die nog niet worden ondersteund

De volgende NFS 3,0-functies worden nog niet ondersteund met Azure Data Lake Storage Gen2.

- NFS 3,0 via UDP. Alleen NFS 3,0 over TCP wordt ondersteund.

- Bestanden vergren delen met Network Lock Manager (NLM). Koppelings opdrachten moeten de `-o nolock` para meter bevatten.

- Submappen koppelen. U kunt alleen de hoofdmap (container) koppelen.

- Koppelingen weer geven (bijvoorbeeld: met behulp van de opdracht `showmount -a` )

- Export vermeldingen (bijvoorbeeld door gebruik te maken van de opdracht `showmount -e` )

- Harde koppeling

- Een container exporteren als alleen-lezen

## <a name="pricing"></a>Prijzen

Tijdens de preview worden de gegevens die in uw opslag account zijn opgeslagen, gefactureerd op basis van hetzelfde capaciteits tarief als voor Blob Storage per GB per maand. 

Tijdens de preview-periode worden er geen kosten in rekening gebracht. Prijzen voor trans acties kunnen worden gewijzigd en worden vastgesteld wanneer het algemeen beschikbaar is.

## <a name="next-steps"></a>Volgende stappen

Zie [Blob Storage koppelen met behulp van het NFS-protocol (Network File System) 3,0 (preview)](network-file-system-protocol-support-how-to.md)om aan de slag te gaan.
