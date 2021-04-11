---
title: Microsoft Azure integreren met Oracle-Cloud infrastructuur | Microsoft Docs
description: Meer informatie over oplossingen voor het integreren van Oracle-apps die worden uitgevoerd op Microsoft Azure met data bases in Oracle Cloud Infrastructure (OCI).
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 06/01/2020
ms.author: kegorman
ms.openlocfilehash: 04a3fb9e4e7dd1d498714cd3b2ebd4c5f6b55bec
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210343"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>Oracle-toepassings oplossingen die Microsoft Azure en Oracle-Cloud infrastructuur integreren

Micro soft en Oracle hebben een partnerschap voor de communicatie tussen verschillende Clouds met een lage latentie en een hoge door Voer, zodat u optimaal gebruik kunt maken van beide Clouds. 

Met deze cross-Cloud-verbinding kunt u een toepassing met meerdere lagen partitioneren om uw databaserol uit te voeren op een Oracle Cloud Infrastructure (OCI) en de toepassing en andere lagen op Microsoft Azure. De ervaring is vergelijkbaar met het uitvoeren van de volledige oplossings stack in één Cloud. 

Als u geïnteresseerd bent in het uitvoeren van uw middleware, inclusief WebLogic-Server, op de Azure-infra structuur, maar de Oracle-data base moet worden uitgevoerd binnen OCI, raadpleegt u [WebLogic Server Azure Applications](oracle-weblogic.md)(Engelstalig).

Als u geïnteresseerd bent in het volledig implementeren van Oracle-oplossingen in de Azure-infra structuur, raadpleegt u [Oracle VM-installatie kopieën en de implementatie ervan op Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Overzicht van scenario's

Connectiviteit tussen de Cloud biedt een oplossing voor het uitvoeren van toonaangevende toepassingen van Oracle en uw eigen aangepaste toepassingen, op virtuele machines van Azure, terwijl u profiteert van de voor delen van gehoste database services in OCI. 

Vanaf mei 2020 worden de volgende toepassingen gecertificeerd in een configuratie voor meerdere Clouds:

* E-Business Suite
* JD Edwards EnterpriseOne
* People
* Retail toepassingen van Oracle
* Financieel beheer van Oracle Hyperion

Het volgende diagram is een overzicht op hoog niveau van de verbonden oplossing. Voor het gemak toont het diagram alleen een gegevenslaagtoepassing en een gegevenslaag. Afhankelijk van de toepassings architectuur kan uw oplossing extra lagen bevatten, zoals een WebLogic-Server cluster of een weblaag in Azure. Zie de volgende secties voor meer informatie.

![Overzicht van de Azure OCI-oplossing](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Beschik baarheid van regio 

Connectiviteit tussen de Cloud is beperkt tot de volgende regio's:
* Azure-Oost (Oost) & OCI Ashburn, VA (VS Oost)
* Azure UK-zuid (UKSouth) & OCI Londen (UK-zuid)
* Azure Canada-centraal (CanadaCentral) & OCI Toronto (Canada-Zuidoost)
* Azure Europa-west (Europa West) & OCI Amsterdam (Nederland Noordwest)
* Azure Japan-Oost (JapanEast) & OCI Tokyo (Japan-Oost)
* Azure-West VS (Westus) & OCI San Jose (VS West)
* Duitsland-west-centraal (Frankfurt) & OCI Duitsland-centraal (Frankfurt)


## <a name="networking"></a>Netwerken

Zakelijke klanten kiezen er vaak voor om werk belastingen te spreideneren en te implementeren voor verschillende Clouds en operationele redenen. Klanten verbinden Cloud netwerken via internet, IPSec VPN of via de directe connectiviteits oplossing van de Cloud provider via uw on-premises netwerk naar spreiden. Het interconnectie van Cloud netwerken kan aanzienlijke investeringen in tijd, geld, ontwerp, aankoop, installatie, testen en bewerkingen vergen. 

Voor deze klant uitdagingen heeft Oracle en micro soft een geïntegreerde multi-Cloud ervaring ingeschakeld. Er wordt verbinding tussen Cloud netwerken gemaakt door een [ExpressRoute](../../../expressroute/expressroute-introduction.md) -circuit in Microsoft Azure aan te sluiten met een [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) -circuit in OCI. Deze connectiviteit is mogelijk wanneer een Azure ExpressRoute-peering locatie zich in de buurt van of op dezelfde peering-locatie bevindt als de OCI FastConnect. Met deze installatie kunt u een veilige, snelle connectiviteit tussen de twee Clouds, zonder dat u hiervoor een tussenliggende service provider nodig hebt.

Met ExpressRoute en FastConnect kunnen klanten een virtueel netwerk in azure koppelen aan een virtueel Cloud netwerk in OCI, mits de privé-IP-adres ruimte elkaar niet overlapt. Door de twee netwerken te koppelen, kan een bron in het virtuele netwerk communiceren met een resource in het OCI virtuele Cloud netwerk alsof ze zich in hetzelfde netwerk bevinden.

## <a name="network-security"></a>Netwerkbeveiliging

Netwerk beveiliging is een cruciaal onderdeel van een bedrijfs toepassing en is centraal voor deze oplossing met meerdere clouds. Elk verkeer dat ExpressRoute en FastConnect doorstuurt, wordt via een particulier netwerk verzonden. Met deze configuratie kunt u beveiligde communicatie tussen een virtueel Azure-netwerk en een virtuele Oracle-Cloud netwerk. U hoeft geen openbaar IP-adres op te geven voor virtuele machines in Azure. Op dezelfde manier hebt u geen Internet gateway nodig in OCI. Alle communicatie gebeurt via het privé-IP-adres van de computers.

Daarnaast kunt u [beveiligings lijsten](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) instellen voor uw OCI virtuele Cloud netwerk en beveiligings regels (gekoppeld aan Azure- [netwerk beveiligings groepen](../../../virtual-network/network-security-groups-overview.md)). Gebruik deze regels voor het beheren van het verkeer tussen computers in de virtuele netwerken. Netwerk beveiligings regels kunnen worden toegevoegd op computer niveau, op subnetniveau, en op het niveau van het virtuele netwerk.

De [WebLogic Server Azure-toepassingen](oracle-weblogic.md) maken elk een netwerk beveiligings groep vooraf geconfigureerd voor gebruik met de poort configuraties van de WebLogic-Server.
 
## <a name="identity"></a>Identiteit

De identiteit is een van de belangrijkste pijlers van de samen werking tussen micro soft en Oracle. Er is veel werk gedaan om [Oracle Identity Cloud service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) te integreren met [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). Azure AD is de cloud-gebaseerde service voor identiteits-en toegangs beheer van micro soft. Uw gebruikers kunnen zich aanmelden en hebben toegang tot verschillende bronnen met behulp van Azure AD. Met Azure AD kunt u ook uw gebruikers en hun machtigingen beheren.

Op dit moment kunt u met deze integratie beheren op één centrale locatie, die Azure Active Directory is. Azure AD synchroniseert alle wijzigingen in de directory met de bijbehorende Oracle-map en wordt gebruikt voor eenmalige aanmelding bij Oracle-oplossingen in meerdere clouds.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met een [Cross-Cloud netwerk](configure-azure-oci-networking.md) tussen Azure en OCI. 

Zie de [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) Documentation (Engelstalig) voor meer informatie en witboeken over OCI.
