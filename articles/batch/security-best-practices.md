---
title: Best practices voor batch beveiliging en naleving
description: Leer de aanbevolen procedures en handige tips voor het verbeteren van de beveiliging met uw Azure Batch oplossingen.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: 6ec4a1d89ebaa9318986fc0d51e832652ba51683
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98723809"
---
# <a name="batch-security-and-compliance-best-practices"></a>Best practices voor batch beveiliging en naleving

Dit artikel bevat richt lijnen en aanbevolen procedures voor het verbeteren van de beveiliging bij het gebruik van Azure Batch.

Azure Batch accounts hebben standaard een openbaar eind punt en zijn toegankelijk voor iedereen. Wanneer een Azure Batch groep wordt gemaakt, wordt de groep ingericht in een opgegeven subnet van een virtueel Azure-netwerk. Virtuele machines in de batch-pool zijn toegankelijk via open bare IP-adressen die door batch worden gemaakt. Reken knooppunten in een pool kunnen met elkaar communiceren wanneer dat nodig is, zoals voor het uitvoeren van taken voor meerdere instanties, maar knoop punten in een pool kunnen niet communiceren met virtuele machines buiten de groep.

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="Diagram waarin een typische batch-omgeving wordt weer gegeven.":::

Er zijn veel functies beschikbaar om u te helpen bij het maken van een veiligere Azure Batch-implementatie. U kunt de toegang tot knoop punten beperken en de detectie van de knoop punten op het Internet verminderen door [de groep in te richten zonder open bare IP-adressen](batch-pool-no-public-ip-address.md). De reken knooppunten kunnen veilig communiceren met andere virtuele machines of met een on-premises netwerk door [de groep in te richten in een subnet van een virtueel Azure-netwerk](batch-virtual-network.md). En u kunt [persoonlijke toegang inschakelen vanuit virtuele netwerken](private-connectivity.md) vanuit een service van een persoonlijke Azure-koppeling.

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="Diagram met een veiligere batch omgeving.":::

## <a name="general-security-related-best-practices"></a>Algemene aanbevolen procedures voor beveiliging

### <a name="pool-configuration"></a>Groeps configuratie

Veel beveiligings functies zijn alleen beschikbaar voor groepen die zijn geconfigureerd met [virtuele-machine configuratie](nodes-and-pools.md#configurations)en niet voor pools met Cloud Services configuratie. We raden u aan virtuele-machine configuratie Pools te gebruiken, waarbij u waar mogelijk [virtuele-machine schaal sets](../virtual-machine-scale-sets/overview.md)gebruikt.

### <a name="batch-account-authentication"></a>Verificatie van batch-account

De toegang tot het batch-account ondersteunt twee verificatie methoden: gedeelde sleutel en [Azure Active Directory (Azure AD)](batch-aad-auth.md).

U wordt aangeraden Azure AD te gebruiken voor de verificatie van batch-accounts. Voor sommige batch-mogelijkheden is deze verificatie methode vereist, inclusief veel van de beveiligings functies die hier worden besproken.

### <a name="batch-account-pool-allocation-mode"></a>Groeps toewijzings modus Batch-account

Wanneer u een batch-account maakt, kunt u kiezen uit twee [pool toewijzings modi](accounts.md#batch-accounts):

- **Batch-service**: de standaard optie, waarbij de onderliggende resources van de Cloud service of de schaalset voor virtuele machines die worden gebruikt voor het toewijzen en beheren van groeps knooppunten, worden gemaakt in interne abonnementen en niet direct zichtbaar zijn in de Azure Portal. Alleen de batch-Pools en knoop punten zijn zichtbaar. 
- **Gebruikers abonnement**: de onderliggende resources van de Cloud service of virtuele-machine schaal sets worden gemaakt in hetzelfde abonnement als het batch-account. Deze resources zijn daarom naast de bijbehorende batch-resources zichtbaar in het abonnement.

In de modus gebruikers abonnement worden batch-Vm's en andere resources rechtstreeks in uw abonnement gemaakt wanneer er een groep wordt gemaakt. De modus gebruikers abonnement is vereist als u batch-Pools wilt maken met behulp van Azure Reserved VM Instances, Azure Policy wilt gebruiken voor resources met schaal sets voor virtuele machines en/of het kern quotum voor het abonnement wilt beheren (gedeeld met alle batch-accounts in het abonnement). Voor het maken van een Batch-account in de gebruikersabonnementmodus moet u het account ook bij Azure Batch registreren en aan een Azure Key Vault koppelen.

## <a name="restrict-network-endpoint-access"></a>Toegang tot netwerk eindpunt beperken

### <a name="batch-network-endpoints"></a>Batch netwerk eindpunten

Houd er rekening mee dat eind punten met open bare IP-adressen standaard worden gebruikt om te communiceren met batch-accounts, batch-Pools en pool knooppunten.

### <a name="batch-account-api"></a>API voor batch-account

 Wanneer een batch-account wordt gemaakt, wordt er een openbaar eind punt gemaakt dat wordt gebruikt voor het aanroepen van de meeste bewerkingen voor het account met behulp van een [rest API](/rest/api/batchservice/). Het account eindpunt heeft een basis-URL met de notatie `https://{account-name}.{region-id}.batch.azure.com` . De toegang tot het batch-account is beveiligd, met communicatie over het account eindpunt dat wordt versleuteld met behulp van HTTPS en elke aanvraag die wordt geverifieerd met behulp van een gedeelde sleutel of Azure Active Directory (Azure AD)-verificatie.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Naast bewerkingen die specifiek zijn voor een batch-account, zijn [beheer bewerkingen](/rest/api/batchmanagement/) alleen van toepassing op enkelvoudige en meervoudige batch-accounts. Deze beheer bewerkingen zijn toegankelijk via Azure Resource Manager.

Batch beheer bewerkingen via Azure Resource Manager worden versleuteld met behulp van HTTPS en elke aanvraag wordt geverifieerd met behulp van Azure AD-verificatie.

### <a name="batch-pool-nodes"></a>Knoop punten van batch-pool

De batch-service communiceert met een batch-knooppunt agent die wordt uitgevoerd op elk knoop punt in de pool. De service geeft de knooppunt agent bijvoorbeeld de opdracht een taak uit te voeren, een taak te stoppen of de bestanden voor een taak op te halen. Communicatie met de knooppunt agent wordt ingeschakeld door een of meer load balancers, het aantal dat afhankelijk is van het aantal knoop punten in een pool. De load balancer stuurt de communicatie door naar het gewenste knoop punt, waarbij elk knoop punt wordt geadresseerd door een uniek poort nummer. Standaard hebben load balancers een openbaar IP-adres dat eraan is gekoppeld. U kunt ook op afstand toegang krijgen tot pool knooppunten via RDP of SSH (deze toegang is standaard ingeschakeld, met communicatie via load balancers).

### <a name="restricting-access-to-batch-endpoints"></a>De toegang tot batch-eind punten beperken 

Er zijn verschillende mogelijkheden beschikbaar om de toegang tot de verschillende batch-eind punten te beperken, met name wanneer de oplossing gebruikmaakt van een virtueel netwerk. 

#### <a name="use-private-endpoints"></a>Privé-eindpunten gebruiken

Met de [persoonlijke Azure-koppeling](../private-link/private-link-overview.md) kunt u toegang krijgen tot Azure PaaS Services en Azure-partner services die zijn gehost door de klant, via een persoonlijk eind punt in uw virtuele netwerk. U kunt een persoonlijke koppeling gebruiken om de toegang tot een batch-account te beperken vanuit het virtuele netwerk of van een peered virtueel netwerk. Resources die zijn toegewezen aan een privé koppeling, zijn ook on-premises toegankelijk via privé-peering via VPN of Azure ExpressRoute.

Als u persoonlijke eind punten wilt gebruiken, moet een batch-account op de juiste wijze worden geconfigureerd wanneer het wordt gemaakt. configuratie van open bare netwerk toegang moet zijn uitgeschakeld. Na het maken kunnen persoonlijke eind punten worden gemaakt en gekoppeld aan het batch-account. Zie voor meer informatie [privé-eind punten gebruiken met Azure batch accounts](private-connectivity.md).

#### <a name="create-pools-in-virtual-networks"></a>Groepen maken in virtuele netwerken

Reken knooppunten in een batch-pool kunnen met elkaar communiceren, zoals het uitvoeren van taken voor meerdere instanties zonder een virtueel netwerk (VNET). Knoop punten in een pool kunnen echter standaard niet communiceren met virtuele machines die zich buiten de pool van een virtueel netwerk bevinden en persoonlijke IP-adressen hebben, zoals licentie servers of bestands servers.

Als u wilt toestaan dat reken knooppunten veilig communiceren met andere virtuele machines of met een on-premises netwerk, kunt u een pool configureren zodat deze zich in een subnet van een Azure VNET bevindt.

Wanneer de Pools een openbaar IP-eind punt hebben, moet het subnet binnenkomende communicatie van de batch-service toestaan om taken te kunnen plannen en andere bewerkingen uit te voeren op de reken knooppunten, en uitgaande communicatie om te communiceren met Azure Storage of andere resources die nodig zijn voor uw werk belasting. Voor Pools in de virtuele-machine configuratie voegt batch netwerk beveiligings groepen (Nsg's) toe op het netwerk interface niveau dat is gekoppeld aan reken knooppunten. Deze Nsg's hebben regels om in te scha kelen:

- Binnenkomend TCP-verkeer van IP-adressen van batch-service
- Binnenkomend TCP-verkeer voor externe toegang
- Uitgaand verkeer op een wille keurige poort voor het virtuele netwerk (kan worden gewijzigd per NSG-regels op subnetniveau)
- Uitgaand verkeer op een wille keurige poort naar Internet (kan worden gewijzigd per NSG-regels op subnetniveau)

U hoeft geen Nsg's op te geven op het subnet van het virtuele netwerk, omdat batch een eigen Nsg's configureert. Als er een NSG is gekoppeld aan het subnet waarin batch Compute-knoop punten zijn geïmplementeerd, of als u aangepaste NSG-regels wilt Toep assen om de standaard instellingen te overschrijven, moet u deze NSG configureren met ten minste de regels voor binnenkomende en uitgaande verbindingen om batch service communicatie toe te staan voor de groeps knooppunten en de communicatie tussen het groeps knooppunt en Azure Storage.

Zie [een Azure batch groep maken in een virtueel netwerk](batch-virtual-network.md)voor meer informatie.

#### <a name="create-pools-with-static-public-ip-addresses"></a>Groepen maken met statische open bare IP-adressen

De open bare IP-adressen die zijn gekoppeld aan Pools zijn standaard dynamisch; ze worden gemaakt wanneer er een groep wordt gemaakt en IP-adressen kunnen worden toegevoegd of verwijderd wanneer de grootte van een groep wordt gewijzigd. Wanneer de taak toepassingen die worden uitgevoerd op groeps knooppunten toegang moeten hebben tot externe services, moet de toegang tot die services mogelijk worden beperkt tot specifieke IP-adressen.  In dit geval kunnen dynamische IP-adressen niet worden beheerd.

U kunt statische open bare IP-adres bronnen maken in hetzelfde abonnement als het batch-account voordat de groep wordt gemaakt. U kunt deze adressen vervolgens opgeven bij het maken van de pool.

Zie [een Azure batch groep met opgegeven open bare IP-adressen maken](create-pool-public-ip.md)voor meer informatie.

#### <a name="create-pools-without-public-ip-addresses"></a>Groepen maken zonder open bare IP-adressen

Standaard worden alle reken knooppunten in een Azure Batch virtuele-machine configuratie groep toegewezen aan een of meer open bare IP-adressen. Deze eind punten worden door de batch-service gebruikt voor het plannen van taken en voor communicatie met reken knooppunten, waaronder uitgaande toegang tot internet.

Als u de toegang tot deze knoop punten wilt beperken en de detectie van deze knoop punten van het Internet wilt verkorten, kunt u de groep inrichten zonder open bare IP-adressen.

Zie [een pool maken zonder open bare IP-adressen](batch-pool-no-public-ip-address.md)voor meer informatie.

#### <a name="limit-remote-access-to-pool-nodes"></a>Externe toegang tot pool knooppunten beperken

Met batch kan een knooppunt gebruiker met een netwerk verbinding standaard verbinding maken met een reken knooppunt in een batch-pool met behulp van RDP of SSH.

Gebruik een van de volgende methoden om externe toegang tot knoop punten te beperken:

- Configureer de [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) om de toegang te weigeren. De juiste netwerk beveiligings groep (NSG) wordt gekoppeld aan de groep.
- Maak uw pool [zonder open bare IP-adressen](batch-pool-no-public-ip-address.md). Deze groepen kunnen standaard niet worden geopend buiten het VNet.
- Koppel een NSG aan het VNet om toegang tot de RDP-of SSH-poorten te weigeren.
- Maak geen gebruikers op het knoop punt. Zonder knooppunt gebruikers is externe toegang niet mogelijk.

## <a name="encrypt-data"></a>Gegevens versleutelen

### <a name="encrypt-data-in-transit"></a>Actieve gegevens versleutelen

Alle communicatie met het eind punt van een batch-account (of via Azure Resource Manager) moet HTTPS gebruiken. U moet `https://` in de url's van het batch-account dat is opgegeven in api's, gebruiken wanneer u verbinding maakt met de batch-service.

Clients die communiceren met de batch-service moeten worden geconfigureerd voor het gebruik van Transport Layer Security (TLS) 1,2.

### <a name="encrypt-batch-data-at-rest"></a>Batch gegevens op rest versleutelen

Sommige van de gegevens die zijn opgegeven in batch-Api's, zoals account certificaten, taak-en taak-meta gegevens en opdracht regels voor taken, worden automatisch versleuteld wanneer ze worden opgeslagen door de batch-service. Deze gegevens worden standaard versleuteld met Azure Batch door het platform beheerde sleutels die uniek zijn voor elk batch-account.

U kunt deze gegevens ook versleutelen met door de [klant beheerde sleutels](batch-customer-managed-key.md). [Azure Key Vault](../key-vault/general/overview.md) wordt gebruikt om de sleutel te genereren en op te slaan, waarbij de sleutel-id is geregistreerd bij uw batch-account.

### <a name="encrypt-compute-node-disks"></a>Schijven van reken knooppunten versleutelen

Batch Compute-knoop punten hebben standaard twee schijven: een besturingssysteem schijf en de lokale tijdelijke SSD. [Bestanden en mappen](files-and-directories.md) die worden beheerd door batch, bevinden zich op de tijdelijke SSD. Dit is de standaard locatie voor bestanden, zoals uitvoer bestanden van taken. Batch-taak toepassingen kunnen de standaard locatie op de SSD of de besturingssysteem schijf gebruiken.

Voor extra beveiliging kunt u deze schijven versleutelen met behulp van een van deze Azure Disk Encryption-functies:

- [Beheerde schijf versleuteling op rest met door het platform beheerde sleutels](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Versleuteling op de host met een door een platform beheerde sleutel](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>Veilige toegang tot services van reken knooppunten

Batch knooppunten hebben [veilig toegang tot referenties en geheimen](credential-access-key-vault.md) die zijn opgeslagen in [Azure Key Vault](../key-vault/general/overview.md), die door taak toepassingen kunnen worden gebruikt voor toegang tot andere services. Een certificaat wordt gebruikt om de groeps knooppunten toegang tot Key Vault te verlenen.

## <a name="governance-and-compliance"></a>Governance en naleving

### <a name="compliance"></a>Naleving

Om klanten te helpen hun eigen nalevings verplichtingen voor gereguleerde industrieën en markten wereld wijd te voldoen, onderhoudt Azure een [groot assortiment aan nalevings aanbod](https://azure.microsoft.com/overview/trusted-cloud/compliance). 

Deze aanbiedingen zijn gebaseerd op verschillende soorten garanties, waaronder formele certificeringen, verklaringen, validering, autorisaties en beoordelingen die worden geproduceerd door onafhankelijke controle bedrijven van derden, evenals contractuele wijzigingen, zelf beoordeling en klant begeleiding documenten die door micro soft worden geproduceerd. Bekijk het [uitgebreide overzicht van de nalevings aanbiedingen](https://aka.ms/AzureCompliance) om te bepalen welke voor waarden mogelijk relevant zijn voor uw batch-oplossingen.

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../governance/policy/overview.md) helpt organisatie standaarden af te dwingen en de naleving op schaal te beoordelen. Veelvoorkomende use-cases voor Azure Policy zijn onder andere het implementeren van governance voor consistentie van resources, naleving van de regelgeving, beveiliging, kosten en beheer.

Afhankelijk van de pool toewijzings modus en de resources waarop het beleid van toepassing moet zijn, gebruikt u Azure Policy met batch op een van de volgende manieren:

- Rechtstreeks, met behulp van de Microsoft.BatCH/batchAccounts resource. Een subset van de eigenschappen voor een batch-account kan worden gebruikt. Uw beleid kan bijvoorbeeld bestaan uit geldige batch-account regio's, toegestane pool toewijzings modus en of een openbaar netwerk is ingeschakeld voor accounts.
- Indirect, met behulp van de resource micro soft. Compute/virtualMachineScaleSets. Batch-accounts met de pool toewijzings modus voor gebruikers abonnementen kunnen beleid instellen op de virtuele-machine Scale set-resources die zijn gemaakt in het batch-account abonnement. Bijvoorbeeld toegestane VM-grootten en zorg ervoor dat bepaalde uitbrei dingen worden uitgevoerd op elk groeps knooppunt.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [Azure Security-basis lijn voor batch](security-baseline.md).
- Lees meer [Aanbevolen procedures voor Azure batch](best-practices.md).