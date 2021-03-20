---
title: Overzicht van Azure-versleuteling | Microsoft Docs
description: Meer informatie over versleutelings opties in Azure. Zie informatie voor versleuteling op rest, versleuteling in vlucht en sleutel beheer met Azure Key Vault.
services: security
author: msmbaldwin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: d7d438b369c863660a032f101e466b6fadf639fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879711"
---
# <a name="azure-encryption-overview"></a>Overzicht van Azure-versleuteling

Dit artikel bevat een overzicht van de manier waarop versleuteling wordt gebruikt in Microsoft Azure. Hierin worden de belangrijkste gebieden van versleuteling beschreven, inclusief versleuteling op rest, versleuteling in vlucht en sleutel beheer met Azure Key Vault. Elke sectie bevat koppelingen naar meer gedetailleerde informatie.

## <a name="encryption-of-data-at-rest"></a>Versleuteling van data-at-rest

Data-at-rest bevat informatie die zich in een permanente opslag op fysieke media bevindt, in een digitale indeling. De media kunnen bestanden bevatten op magnetische of optische media, gearchiveerde gegevens en gegevens back-ups. Microsoft Azure biedt diverse oplossingen voor gegevens opslag om te voldoen aan verschillende vereisten, waaronder bestands-, schijf-, Blob-en tabel opslag. Micro soft biedt ook versleuteling om [Azure SQL database](../../azure-sql/database/sql-database-paas-overview.md), [Azure Cosmos DB](../../data-factory/introduction.md)en Azure data Lake te beveiligen.

Gegevens versleuteling op rest is beschikbaar voor services in de Cloud modellen software as a Service (SaaS), platform as a Service (PaaS) en Infrastructure as a Service (IaaS). In dit artikel vindt u een overzicht van en informatie bronnen die u helpen bij het gebruik van de opties voor Azure-versleuteling.

Zie [Azure Data Encryption-at-rest](encryption-atrest.md)voor meer informatie over hoe gegevens in rust worden versleuteld in Azure.

## <a name="azure-encryption-models"></a>Azure-versleutelings modellen

Azure biedt ondersteuning voor verschillende versleutelings modellen, waaronder versleuteling aan de server zijde die gebruikmaakt van door de klant beheerde sleutels, door de gebruiker door de beheerder in Key Vault of door de klant beheerde sleutels. Met versleuteling aan clientzijde kunt u sleutels on-premises of op een andere veilige locatie beheren en opslaan.

### <a name="client-side-encryption"></a>Clientversleuteling

Versleuteling aan de client zijde wordt buiten Azure uitgevoerd. Het bevat:

- Gegevens die worden versleuteld door een toepassing die wordt uitgevoerd in het Data Center van de klant of door een service toepassing.
- Gegevens die al zijn versleuteld wanneer deze worden ontvangen door Azure.

Met versleuteling aan de client zijde hebben Cloud serviceproviders geen toegang tot de versleutelings sleutels en kunnen deze gegevens niet worden ontsleuteld. U behoudt de volledige controle over de sleutels.

### <a name="server-side-encryption"></a>Versleuteling aan de serverzijde

De drie versleutelings modellen aan de server zijde bieden verschillende kenmerken voor sleutel beheer, die u op basis van uw vereisten kunt kiezen:

- Door **service beheerde sleutels**: biedt een combi natie van beheer en gebruiks gemak met lage overhead.

- Door de **klant beheerde sleutels**: geeft u de controle over de sleutels, waaronder het meebrengen van uw eigen sleutels (BYOK), of u kunt er nieuwe toevoegen.

- Door **service beheerde sleutels in door de klant beheerde hardware**: Hiermee kunt u sleutels in uw eigen opslag plaats beheren, buiten micro soft Control. Dit kenmerk wordt een host uw eigen sleutel (HYOK) genoemd. Configuratie is echter complex en de meeste Azure-Services bieden geen ondersteuning voor dit model.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

U kunt virtuele Windows-en Linux-machines beveiligen met behulp van [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md), die gebruikmaakt van [Windows BitLocker](/previous-versions/windows/it-pro/windows-vista/cc766295(v=ws.10)) -technologie en Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) om zowel de besturingssysteem schijven als gegevens schijven te beveiligen met volledige versleuteling van het volume.

Versleutelings sleutels en geheimen worden beveiligd in uw [Azure Key Vault-abonnement](../../key-vault/general/overview.md). Met de Azure Backup-Service kunt u back-ups maken en terugzetten van versleutelde virtuele machines (Vm's) die gebruikmaken van de KEK-configuratie (Key Encryption Key).

### <a name="azure-storage-service-encryption"></a>Versleuteling van Azure Storage-service

Data-at-rest in Azure Blob-opslag en Azure-bestands shares kunnen worden versleuteld in zowel server-als client scenario's.

Met [Azure Storage service versleuteling (SSE)](../../storage/common/storage-service-encryption.md) kunnen gegevens automatisch worden versleuteld voordat ze worden opgeslagen en worden de gegevens automatisch ontsleuteld wanneer u deze ophaalt. Het proces is volledig transparant voor gebruikers. Storage Service Encryption maakt gebruik van 256-bits [Advanced Encryption Standard (AES)-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), een van de krach tigste blok cijfers die beschikbaar zijn. AES verwerkt versleuteling, ontsleuteling en sleutel beheer transparant.

### <a name="client-side-encryption-of-azure-blobs"></a>Versleuteling aan client zijde van Azure-blobs

U kunt op verschillende manieren versleuteling van Azure-blobs aan de client zijde uitvoeren.

U kunt het Azure Storage-client bibliotheek voor .NET NuGet-pakket gebruiken voor het versleutelen van gegevens binnen uw client toepassingen voordat u deze uploadt naar uw Azure-opslag.

Zie [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage)voor meer informatie over en het downloaden van de Azure Storage-client bibliotheek voor .net NuGet-pakketten.

Wanneer u versleuteling aan de client zijde gebruikt met Key Vault, worden uw gegevens versleuteld met een eenmalige symmetrische inhouds versleutelings sleutel (CEK) die wordt gegenereerd door de Azure Storage client-SDK. De CEK wordt versleuteld met een sleutel versleutelings sleutel (KEK), die een symmetrische sleutel of een asymmetrisch sleutel paar kan zijn. U kunt het lokaal beheren of opslaan in Key Vault. De versleutelde gegevens worden vervolgens geüpload naar Azure Storage.

Zie [zelf studie: Azure Storage blobs versleutelen en ontsleutelen met behulp van Key Vault](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md)voor meer informatie over versleuteling aan de client zijde met Key Vault en aan de slag met instructies.

Ten slotte kunt u ook de Azure Storage-client bibliotheek voor Java gebruiken om versleuteling aan de client zijde uit te voeren voordat u gegevens uploadt naar Azure Storage en om de gegevens te ontsleutelen wanneer u deze naar de client downloadt. Deze bibliotheek ondersteunt ook integratie met [Key Vault](https://azure.microsoft.com/services/key-vault/) voor sleutel beheer van het opslag account.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Versleuteling van gegevens in rust met Azure SQL Database

[Azure SQL database](../../azure-sql/database/sql-database-paas-overview.md) is een relationele database service voor algemeen gebruik in azure die ondersteuning biedt voor structuren zoals relationele gegevens, JSON, ruimtelijke en XML. SQL Database ondersteunt zowel versleuteling aan de server zijde via de functie Transparent Data Encryption (TDE) als versleuteling aan client zijde via de Always Encrypted-functie.

#### <a name="transparent-data-encryption"></a>Transparante gegevensversleuteling

[TDe](/sql/relational-databases/security/encryption/transparent-data-encryption-tde) wordt gebruikt voor het versleutelen van [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL database](../../azure-sql/database/sql-database-paas-overview.md)en [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) -gegevens bestanden in realtime, met behulp van een database versleutelings sleutel (dek), die wordt opgeslagen in de data base boot record voor Beschik baarheid tijdens het herstel.

TDE beschermt gegevens en logboek bestanden met behulp van AES-en Triple Data Encryption Standard (3DES)-versleutelings algoritmen. Versleuteling van het database bestand wordt uitgevoerd op pagina niveau. De pagina's in een versleutelde data base worden versleuteld voordat ze naar de schijf worden geschreven en worden ontsleuteld wanneer ze in het geheugen worden gelezen. TDE is nu standaard ingeschakeld voor nieuw gemaakte Azure SQL-data bases.

#### <a name="always-encrypted-feature"></a>Always Encrypted functie

Met de functie [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) in Azure SQL kunt u gegevens in client toepassingen versleutelen voordat u deze opslaat in Azure SQL database. U kunt ook delegering van on-premises database beheer aan derden inschakelen en de schei ding tussen degenen die eigenaar zijn, behouden en de gegevens weer geven die deze beheren, maar geen toegang hebben.

#### <a name="cell-level-or-column-level-encryption"></a>Versleuteling op cellen of op kolom niveau

Met Azure SQL Database kunt u symmetrische versleuteling Toep assen op een kolom met gegevens met behulp van Transact-SQL. Deze benadering wordt versleuteling op naam [of op kolom niveau (CLE)](/sql/relational-databases/security/encryption/encrypt-a-column-of-data)genoemd, omdat u deze kunt gebruiken voor het versleutelen van specifieke kolommen of zelfs specifieke gegevens cellen met verschillende versleutelings sleutels. Dit biedt u meer gedetailleerde coderings mogelijkheden dan TDE, waarmee gegevens op pagina's worden versleuteld.

CLE heeft ingebouwde functies die u kunt gebruiken voor het versleutelen van gegevens met behulp van symmetrische of asymmetrische sleutels, de open bare sleutel van een certificaat of een wachtwoordzin met 3DES.

### <a name="cosmos-db-database-encryption"></a>Database versleuteling Cosmos DB

[Azure Cosmos DB](../../cosmos-db/database-encryption-at-rest.md) is de wereld wijd gedistribueerde, multi-model data base van micro soft. Gebruikers gegevens die zijn opgeslagen in Cosmos DB in niet-vluchtige opslag (Solid-state drives) worden standaard versleuteld. Er zijn geen besturings elementen om deze in of uit te scha kelen. Versleuteling op rest wordt geïmplementeerd met behulp van een aantal beveiligings technologieën, waaronder systemen voor beveiligde sleutel opslag, versleutelde netwerken en cryptografische Api's. Versleutelings sleutels worden beheerd door micro soft en worden geroteerd volgens de interne richt lijnen van micro soft.

### <a name="at-rest-encryption-in-data-lake"></a>At-rest-versleuteling in Data Lake

[Azure data Lake](../../data-lake-store/data-lake-store-encryption.md) is een opslag plaats in de hele onderneming van elk type gegevens dat op één plaats wordt verzameld vóór een formele definitie van vereisten of schema. Data Lake Store ondersteunt standaard transparante versleuteling van data-at-rest, die tijdens het maken van uw account is ingesteld. Standaard beheert Azure Data Lake Store de sleutels voor u, maar u hebt de mogelijkheid om deze zelf te beheren.

Er worden drie soorten sleutels gebruikt bij het versleutelen en ontsleutelen van gegevens: de hoofd versleutelings sleutel (MEK), de gegevens versleutelings sleutel (DEK) en de blok versleutelings sleutel (BEK). De MEK wordt gebruikt voor het versleutelen van de DEK, die wordt opgeslagen op permanente media, en de BEK is afgeleid van de DEK en het gegevens blok. Als u uw eigen sleutels beheert, kunt u de MEK draaien.

## <a name="encryption-of-data-in-transit"></a>Versleuteling van gegevens die onderweg zijn

Azure biedt veel mechanismen voor het privé houden van gegevens wanneer deze van de ene locatie naar de andere worden verplaatst.

### <a name="data-link-layer-encryption-in-azure"></a>Data Link-laag versleuteling in azure

Wanneer het verkeer van Azure-klanten tussen data centers wordt verplaatst: buiten de fysieke grenzen die niet worden beheerd door micro soft (of namens micro soft)--een gegevenskoppeling slaag versleutelings methode met behulp van de [IEEE 802.1 AE Mac Security Standards](https://1.ieee802.org/security/802-1ae/) (ook wel bekend als MACsec) wordt toegepast vanaf Point-to-Point over de onderliggende netwerkhardware. De pakketten worden versleuteld en ontsleuteld op de apparaten voordat ze worden verzonden, waardoor fysieke ' man-in-the-middle ' of bewaak-en wiretapping-aanvallen worden voor komen. Omdat deze technologie is geïntegreerd op de netwerkhardware zelf, biedt deze een regel frequentie versleuteling op de netwerkhardware zonder meet bare koppelings latentie verhoging. Deze MACsec-versleuteling is standaard ingeschakeld voor alle Azure-verkeer dat binnen een regio of tussen regio's reist, en er is geen actie vereist voor het onderdeel van de klanten om in te scha kelen. 

### <a name="tls-encryption-in-azure"></a>TLS-versleuteling in azure

Micro soft biedt klanten de mogelijkheid om [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) -Protocol (TLS) te gebruiken voor het beveiligen van gegevens wanneer er wordt gereisd tussen de Cloud Services en klanten. Micro soft-data centers onderhandelen over een TLS-verbinding met client systemen die verbinding maken met Azure-Services. TLS biedt sterke verificatie, privacy van berichten en integriteit (inschakelen van detectie van bericht manipulatie, onderscheping en vervalsing), interoperabiliteit, algoritme flexibiliteit en gemakkelijke implementatie en gebruik.

[Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) beschermt verbindingen tussen client systemen van klanten en micro soft-Cloud Services door unieke sleutels. Verbindingen maken ook gebruik van op RSA gebaseerde 2.048-bits versleutelings sleutel lengtes. Deze combi natie maakt het moeilijk voor iemand om gegevens te onderscheppen en te benaderen die onderweg zijn.

### <a name="azure-storage-transactions"></a>Azure Storage-transacties

Wanneer u met Azure Storage communiceert via de Azure Portal, worden alle trans acties uitgevoerd via HTTPS. U kunt ook de opslag REST API via HTTPS gebruiken om te communiceren met Azure Storage. U kunt het gebruik van HTTPS afdwingen wanneer u de REST-Api's aanroept om toegang te krijgen tot objecten in opslag accounts door de beveiligde overdracht in te scha kelen die vereist is voor het opslag account.

Shared Access signatures ([SAS](../../storage/common/storage-sas-overview.md)), die kunnen worden gebruikt voor het delegeren van toegang tot Azure Storage-objecten, bevatten een optie om op te geven dat alleen het HTTPS-protocol kan worden gebruikt wanneer u gedeelde toegangs handtekeningen gebruikt. Deze aanpak zorgt ervoor dat iedereen die koppelingen met SAS-tokens verzendt, het juiste protocol gebruikt.

[SMB 3,0](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn551363(v=ws.11)#BKMK_SMBEncryption), die wordt gebruikt voor toegang tot Azure files shares, ondersteunt versleuteling en is beschikbaar in windows server 2012 R2, Windows 8, Windows 8,1 en Windows 10. Hiermee hebt u toegang tot meerdere regio's en zelfs toegang op het bureau blad.

Versleuteling aan client zijde versleutelt de gegevens voordat deze naar uw Azure Storage-exemplaar worden verzonden, zodat deze wordt versleuteld wanneer deze via het netwerk worden overgebracht.

### <a name="smb-encryption-over-azure-virtual-networks"></a>SMB-versleuteling via Azure Virtual Networks 

Door [SMB 3,0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) te gebruiken op virtuele machines waarop Windows Server 2012 of hoger wordt uitgevoerd, kunt u gegevens overdracht beveiligen door gegevens in door voer te versleutelen via Azure Virtual Networks. Door gegevens te versleutelen, kunt u zich beschermen tegen onrecht matige wijzigingen en inbreuken. Beheerders kunnen SMB-versleuteling inschakelen voor de volledige server of alleen specifieke shares.

Wanneer SMB-versleuteling standaard is ingeschakeld voor een share of server, hebben alleen SMB 3,0-clients toegang tot de versleutelde shares.

## <a name="in-transit-encryption-in-vms"></a>In-transit versleuteling in Vm's

Gegevens in transit naar, van en tussen virtuele machines met Windows kunnen op verschillende manieren worden versleuteld, afhankelijk van de aard van de verbinding.

### <a name="rdp-sessions"></a>RDP-sessies

U kunt verbinding maken met en aanmelden bij een virtuele machine met behulp van de [Remote Desktop Protocol (RDP)](/windows/win32/termserv/remote-desktop-protocol) van een Windows-client computer of van een Mac waarop een RDP-client is geïnstalleerd. Gegevens in de overdracht via het netwerk in RDP-sessies kunnen worden beveiligd door TLS.

U kunt Extern bureaublad ook gebruiken om verbinding te maken met een virtuele Linux-machine in Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Veilige toegang tot virtuele Linux-machines met SSH

Voor extern beheer kunt u [Secure Shell](../../virtual-machines/linux/ssh-from-windows.md) (SSH) gebruiken om verbinding te maken met virtuele Linux-machines die in Azure worden uitgevoerd. SSH is een versleuteld verbindings protocol waarmee beveiligde aanmeldingen via niet-beveiligde verbindingen worden toegestaan. Het is het standaard verbindings protocol voor Linux Vm's die worden gehost in Azure. Als u SSH-sleutels voor verificatie gebruikt, hoeft u zich niet meer aan te melden. SSH gebruikt een openbaar/persoonlijk sleutel paar (asymmetrische versleuteling) voor verificatie.

## <a name="azure-vpn-encryption"></a>Azure VPN-versleuteling

U kunt verbinding maken met Azure via een virtueel particulier netwerk dat een beveiligde tunnel maakt om de privacy te beschermen van de gegevens die via het netwerk worden verzonden.

### <a name="azure-vpn-gateways"></a>Azure VPN-gateways

U kunt een [Azure VPN-gateway](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) gebruiken om versleuteld verkeer tussen uw virtuele netwerk en uw on-premises locatie via een open bare verbinding te verzenden, of om verkeer tussen virtuele netwerken te verzenden.

Site-naar-site-Vpn's gebruiken [IPSec](https://en.wikipedia.org/wiki/IPsec) voor transport versleuteling. Azure VPN-gateways gebruiken een set standaard voorstellen. U kunt Azure VPN-gateways configureren voor het gebruik van een aangepast IPsec/IKE-beleid met specifieke cryptografische algoritmen en sleutel sterkten, in plaats van de standaard beleids sets van Azure.

### <a name="point-to-site-vpns"></a>Punt-naar-site-VPN's

Met punt-naar-site-Vpn's kunnen afzonderlijke client computers toegang krijgen tot een virtueel Azure-netwerk. [SSTP (Secure Socket Tunneling Protocol)](/previous-versions/technet-magazine/cc162322(v=msdn.10)) wordt gebruikt om de VPN-tunnel te maken. Het kan firewalls passeren (de tunnel wordt weer gegeven als een HTTPS-verbinding). U kunt uw eigen interne PKI (Public Key Infrastructure) basis certificerings instantie (CA) gebruiken voor punt-naar-site-connectiviteit.

U kunt een punt-naar-site-VPN-verbinding met een virtueel netwerk configureren met behulp van de Azure Portal met certificaat verificatie of Power shell.

Zie voor meer informatie over punt-naar-site-VPN-verbindingen met virtuele Azure-netwerken:

[Een punt-naar-site-verbinding met een virtueel netwerk configureren met behulp van certificerings verificatie: Azure Portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Een punt-naar-site-verbinding met een virtueel netwerk configureren met behulp van certificaat verificatie: Power shell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Site-naar-site-Vpn's 

U kunt een site-naar-site-VPN-gateway verbinding gebruiken om uw on-premises netwerk te verbinden met een virtueel Azure-netwerk via een IPsec/IKE (IKEv1 of IKEv2) VPN-tunnel. Voor dit type verbinding is een on-premises VPN-apparaat vereist waaraan een extern gericht openbaar IP-adres is toegewezen.

U kunt een site-naar-site-VPN-verbinding met een virtueel netwerk configureren met behulp van de Azure Portal, Power shell of Azure CLI.

Zie voor meer informatie:

[Een site-naar-site-verbinding maken in de Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md)

[Een site-naar-site-verbinding maken in Power shell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Een virtueel netwerk met een site-naar-site-VPN-verbinding maken met behulp van CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>In-transit versleuteling in Data Lake

Gegevens tijdens overdracht (ook wel gegevens in beweging genoemd) worden ook altijd versleuteld in Data Lake Store. Naast het versleutelen van gegevens voorafgaand aan het opslaan op permanente media, worden de gegevens ook altijd tijdens het door Voer beveiligd met behulp van HTTPS. HTTPS is het enige protocol dat wordt ondersteund voor de Data Lake Store REST-interfaces.

Zie [versleuteling van gegevens in data Lake Store](../../data-lake-store/data-lake-store-encryption.md)voor meer informatie over het versleutelen van gegevens in door voer in data Lake.

## <a name="key-management-with-key-vault"></a>Sleutel beheer met Key Vault

Zonder de juiste bescherming en het beheer van de sleutels, wordt de versleuteling onbruikbaar. Key Vault is de door micro soft aanbevolen oplossing voor het beheren en beheren van de toegang tot de versleutelings sleutels die worden gebruikt door Cloud Services. Machtigingen voor toegangs sleutels kunnen worden toegewezen aan services of aan gebruikers via Azure Active Directory accounts.

Met Key Vault hoeven organisaties hardwarebeveiligingsmodules (Hardware Security Modules of HSM's) en sleutelbeheersoftware niet zelf te configureren, te onderhouden en hiervoor patches toe te passen. Wanneer u Key Vault gebruikt, behoudt u de controle. Micro soft bewaart nooit uw sleutels en toepassingen hebben geen directe toegang. U kunt ook sleutels importeren of genereren in Hsm's.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure-beveiliging](./overview.md)
- [Overzicht van Azure-netwerkbeveiliging](network-overview.md)
- [Overzicht van Azure data base-beveiliging](../../azure-sql/database/security-overview.md)
- [Overzicht van de beveiliging van virtuele Azure-machines](virtual-machines-overview.md)
- [Versleuteling van inactieve gegevens](encryption-atrest.md)
- [Best practices voor gegevensbeveiliging en -versleuteling](data-encryption-best-practices.md)