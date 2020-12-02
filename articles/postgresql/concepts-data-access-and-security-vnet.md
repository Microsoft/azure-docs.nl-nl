---
title: Regels voor het virtuele netwerk-Azure Database for PostgreSQL-één server
description: Meer informatie over het gebruik van de service-eind punten van Virtual Network (vnet) om verbinding te maken met Azure Database for PostgreSQL-één server.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: d45ab771f90c0174f24d5f0d39921f93f72be850
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451068"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql---single-server"></a>Virtual Network Service-eind punten en-regels voor Azure Database for PostgreSQL-één server gebruiken

*Regels voor virtuele netwerken* zijn één firewall beveiligings functie die bepaalt of uw Azure database for postgresql server communicatie accepteert die wordt verzonden vanuit bepaalde subnetten in virtuele netwerken. In dit artikel wordt uitgelegd waarom de regel functie van het virtuele netwerk soms de beste optie is om communicatie met uw Azure Database for PostgreSQL-server veilig toe te staan.

Als u een regel voor een virtueel netwerk wilt maken, moet u eerst een [virtueel netwerk][vm-virtual-network-overview] (VNet) en een [virtueel netwerk service-eind punt][vm-virtual-network-service-endpoints-overview-649d] voor de regel waarnaar moet worden verwezen. In de volgende afbeelding ziet u hoe een Virtual Network Service-eind punt samen met Azure Database for PostgreSQL werkt:

:::image type="content" source="media/concepts-data-access-and-security-vnet/vnet-concept.png" alt-text="Voor beeld van hoe een VNet-service-eind punt werkt":::

> [!NOTE]
> Deze functie is beschikbaar in alle regio's van de open bare Azure-Cloud, waar Azure Database for PostgreSQL wordt geïmplementeerd voor Algemeen en servers die zijn geoptimaliseerd voor geheugen.
> In het geval van VNet-peering, als verkeer via een gemeen schappelijke VNet-gateway met Service-eind punten stroomt en naar de peer moet stromen, moet u een ACL/VNet-regel maken om Azure Virtual Machines in de gateway-VNet toegang te geven tot de Azure Database for PostgreSQL-server.

U kunt ook een [persoonlijke koppeling](concepts-data-access-and-security-private-link.md) gebruiken voor verbindingen. Persoonlijke koppeling biedt een privé-IP-adres in uw VNet voor de Azure Database for PostgreSQL-server.

<a name="anch-terminology-and-description-82f"></a>
## <a name="terminology-and-description"></a>Terminologie en beschrijving

**Virtueel netwerk:** U kunt virtuele netwerken koppelen aan uw Azure-abonnement.

**Subnet:** Een virtueel netwerk bevat **subnetten**. Alle Azure virtual machines (Vm's) die u hebt toegewezen aan subnetten. Eén subnet kan meerdere Vm's of andere reken knooppunten bevatten. Reken knooppunten die zich buiten uw virtuele netwerk bevinden, hebben geen toegang tot het virtuele netwerk tenzij u de beveiliging zo configureert dat toegang wordt toegestaan.

**Service-eind punt Virtual Network:** Een [Virtual Network Service-eind punt][vm-virtual-network-service-endpoints-overview-649d] is een subnet waarvan de eigenschaps waarden een of meer formele namen van Azure-service typen bevatten. In dit artikel bent u geïnteresseerd in de type naam van **micro soft. SQL**, die verwijst naar de Azure-service met de naam SQL database. Deze servicetag is ook van toepassing op de Azure Database for PostgreSQL-en MySQL-Services. Het is belang rijk te weten wanneer u de code van de **micro soft. SQL** -service toepast op een VNet-service-eind punt Hiermee wordt het verkeer van service-eind punten geconfigureerd voor alle Azure SQL Database, Azure Database for PostgreSQL en Azure database for MySQL servers in het subnet. 

**Regel voor virtueel netwerk:** Een regel voor het virtuele netwerk voor uw Azure Database for PostgreSQL-server is een subnet dat wordt vermeld in de toegangs beheer lijst (ACL) van uw Azure Database for PostgreSQL-server. Het subnet moet de naam van het **micro soft. SQL** -type bevatten in de ACL voor uw Azure database for postgresql-server.

Met een regel voor het virtuele netwerk krijgt uw Azure Database for PostgreSQL-server de communicatie van elk knoop punt dat zich in het subnet bevindt, te accepteren.

<a name="anch-details-about-vnet-rules-38q"></a>

## <a name="benefits-of-a-virtual-network-rule"></a>Voor delen van een regel voor een virtueel netwerk

Totdat u actie onderneemt, kunnen de Vm's op uw subnetten niet communiceren met uw Azure Database for PostgreSQL-server. Een actie die de communicatie tot stand brengt, is het maken van een regel voor een virtueel netwerk. De motivering van het kiezen van de methode voor de VNet-regel vereist een vergelijking en contrast met betrekking tot de concurrerende beveiligings opties die door de firewall worden geboden.

### <a name="a-allow-access-to-azure-services"></a>A. Toegang tot Azure-services toestaan

Het deel venster verbindings beveiliging heeft een **aan/uit-** knop met de naam **toegang tot Azure-Services toestaan**. Met de instelling **bij** kunt u communicatie van alle Azure IP-adressen en alle Azure-subnetten toestaan. Deze IP-adressen of subnetten van Azure zijn mogelijk niet het eigendom van u. Deze **bij** instelling is waarschijnlijk meer open dan u wilt dat uw Azure database for PostgreSQL-data base. De functie regel voor virtueel netwerk biedt veel nauw keurigere controle.

### <a name="b-ip-rules"></a>B. IP-regels

Met de Azure Database for PostgreSQL firewall kunt u IP-adresbereiken opgeven waarvan de communicatie wordt geaccepteerd in de Azure Database for PostgreSQL-data base. Deze aanpak is nauw keurig voor stabiele IP-adressen die zich buiten het particuliere Azure-netwerk bevinden. Maar veel knoop punten in het particuliere netwerk van Azure zijn geconfigureerd met *dynamische* IP-adressen. Dynamische IP-adressen kunnen veranderen, bijvoorbeeld wanneer de virtuele machine opnieuw is opgestart. Het is Folly om een dynamisch IP-adres op te geven in een firewall regel, in een productie omgeving.

U kunt de IP-optie inwaarderen door een *statisch* IP-adres voor uw virtuele machine op te halen. Zie voor meer informatie [privé IP-adressen configureren voor een virtuele machine met behulp van de Azure Portal][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

De aanpak van een statisch IP-adres kan echter moeilijk te beheren zijn, en het kost goed wanneer deze op schaal wordt uitgevoerd. Regels voor virtuele netwerken zijn eenvoudiger te maken en te beheren.


<a name="anch-details-about-vnet-rules-38q"></a>

## <a name="details-about-virtual-network-rules"></a>Details over regels voor virtuele netwerken

In deze sectie worden verschillende details over regels voor het virtuele netwerk beschreven.

### <a name="only-one-geographic-region"></a>Slechts één geografische regio

Elk Virtual Network Service-eind punt is alleen van toepassing op één Azure-regio. Met het eind punt kunnen andere regio's geen communicatie van het subnet accepteren.

Een regel voor het virtuele netwerk is beperkt tot de regio waarin het onderliggende eind punt van toepassing is.

### <a name="server-level-not-database-level"></a>Server niveau, niet database niveau

Elke regel voor het virtuele netwerk is van toepassing op uw hele Azure Database for PostgreSQL server, niet alleen op een bepaalde data base op de server. Met andere woorden, de regel voor het virtuele netwerk geldt op server niveau, niet op database niveau.

#### <a name="security-administration-roles"></a>Beveiligings beheer rollen

Er is een schei ding van beveiligings rollen in het beheer van Virtual Network Service-eind punten. Actie is vereist voor elk van de volgende rollen:

- **Netwerk beheerder:** &nbsp; Schakel het eind punt in.
- **Database beheerder:** &nbsp; Werk de toegangs beheer lijst (ACL) bij om het opgegeven subnet toe te voegen aan de Azure Database for PostgreSQL-server.

*Alternatief voor Azure RBAC:*

De rollen van de netwerk beheerder en de database beheerder hebben meer mogelijkheden dan nodig zijn voor het beheren van regels voor het virtuele netwerk. Er is slechts een subset van de mogelijkheden nodig.

U hebt de mogelijkheid om op [rollen gebaseerd toegangs beheer (Azure RBAC)][rbac-what-is-813s] in azure te gebruiken om één aangepaste rol te maken die alleen de benodigde subset van mogelijkheden heeft. De aangepaste rol kan worden gebruikt in plaats van de netwerk beheerder of de database beheerder. De surface area van uw beveiligings risico is lager als u een gebruiker toevoegt aan een aangepaste rol, en de gebruiker toevoegt aan de andere twee belang rijke beheerders rollen.

> [!NOTE]
> In sommige gevallen bevinden de Azure Database for PostgreSQL en het VNet-subnet zich in verschillende abonnementen. In deze gevallen moet u ervoor zorgen dat u de volgende configuraties hebt:
> - Beide abonnementen moeten zich in dezelfde Azure Active Directory Tenant bezitten.
> - De gebruiker beschikt over de vereiste machtigingen voor het initiëren van bewerkingen, zoals het inschakelen van service-eind punten en het toevoegen van een VNet-subnet aan de opgegeven server.
> - Zorg ervoor dat voor beide abonnementen de resource provider **micro soft. SQL** en **micro soft. DBforPostgreSQL** is geregistreerd. Raadpleeg [Resource-Manager-registratie][resource-manager-portal] voor meer informatie

## <a name="limitations"></a>Beperkingen

Voor Azure Database for PostgreSQL heeft de functie regels voor virtuele netwerken de volgende beperkingen:

- Een web-app kan worden toegewezen aan een persoonlijk IP-adres in een VNet/subnet. Zelfs als service-eind punten zijn ingeschakeld vanuit het opgegeven VNet/subnet, hebben verbindingen van de web-app naar de server een open bare IP-bron van Azure, niet een VNet/subnet-bron. Als u de verbinding van een web-app naar een server met VNet-firewall regels wilt inschakelen, moet u Azure-Services toegang geven tot de server op de server.

- In de firewall voor uw Azure Database for PostgreSQL verwijst elke virtuele netwerk regel naar een subnet. Al deze subnetten waarnaar wordt verwezen, moeten worden gehost in dezelfde geografische regio die als host fungeert voor de Azure Database for PostgreSQL.

- Elke Azure Database for PostgreSQL-server kan Maxi maal 128 ACL-vermeldingen hebben voor elk gegeven virtueel netwerk.

- De regels voor virtuele netwerken zijn alleen van toepassing op Azure Resource Manager virtuele netwerken. en niet op [klassieke implementatie model][arm-deployment-model-568f] netwerken.

- Als u de service-eind punten van het virtuele netwerk inschakelt voor Azure Database for PostgreSQL met het tag **micro soft. SQL** -service, worden ook de eind punten ingeschakeld voor alle Azure data base-services: Azure Database for MySQL, Azure Database for PostgreSQL, Azure SQL database en Azure Synapse Analytics.

- Ondersteuning voor VNet-service-eind punten is alleen voor servers met Algemeen en geoptimaliseerd voor geheugen.

- Als **micro soft. SQL** is ingeschakeld in een subnet, betekent dit dat u alleen VNet-regels wilt gebruiken om verbinding te maken. [Niet-VNet-firewall regels](concepts-firewall-rules.md) van bronnen in dat subnet werken niet.

- IP-adresbereiken op de firewall zijn van toepassing op de volgende netwerk items, maar de regels voor het virtuele netwerk doen dit niet:
    - [Virtueel particulier netwerk (VPN) van site-naar-site (S2S)][vpn-gateway-indexmd-608y]
    - On-premises via [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Als uw netwerk is verbonden met het Azure-netwerk met behulp van [ExpressRoute][expressroute-indexmd-744v], wordt elk circuit geconfigureerd met twee open bare IP-adressen op de micro soft Edge. De twee IP-adressen worden gebruikt om verbinding te maken met micro soft-Services, zoals Azure Storage, met behulp van open bare Azure-peering.

Als u communicatie van uw circuit naar Azure Database for PostgreSQL wilt toestaan, moet u IP-netwerk regels maken voor de open bare IP-adressen van uw circuits. Als u de open bare IP-adressen van uw ExpressRoute-circuit wilt vinden, opent u een ondersteunings ticket met ExpressRoute met behulp van de Azure Portal.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Een VNET-firewall regel toevoegen aan uw server zonder de VNET-service-eind punten in te scha kelen

Als u alleen een VNet-firewall regel instelt, kunt u de server niet beveiligen met het VNet. U moet ook VNet-service-eind punten **inschakelen om de beveiliging van kracht** te laten worden. Wanneer u service-eind punten inschakelt, wordt de **downtime van uw** VNet- **subnet in stand** gezet totdat de overgang van **uit** naar wordt voltooid. Dit geldt met name in de context van grote VNets. U kunt de vlag **IgnoreMissingServiceEndpoint** gebruiken om de downtime te verminderen of te elimineren tijdens de overgang.

U kunt de vlag **IgnoreMissingServiceEndpoint** instellen met behulp van de Azure CLI of portal.

## <a name="related-articles"></a>Verwante artikelen:
- [Virtuele netwerken van Azure][vm-virtual-network-overview]
- [Service-eindpunten voor een virtueel Azure-netwerk][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Volgende stappen
Zie voor artikelen over het maken van VNet-regels:
- [Azure Database for PostgreSQL VNet-regels maken en beheren met behulp van de Azure Portal](howto-manage-vnet-using-portal.md)
- [Azure Database for PostgreSQL VNet-regels maken en beheren met Azure CLI](howto-manage-vnet-using-cli.md)


<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../role-based-access-control/overview.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
