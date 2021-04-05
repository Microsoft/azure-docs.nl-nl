---
title: Azure-gegevens versleuteling-at-rest-Azure-beveiliging
description: Dit artikel biedt een overzicht van Azure Data Encryption op rest, de algemene mogelijkheden en algemene overwegingen.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: mbaldwin
ms.openlocfilehash: 1318aaf50d4f05b8a999e5661318dc83997a2f02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96920784"
---
# <a name="azure-data-encryption-at-rest"></a>Azure-gegevens versleuteling in rust

Microsoft Azure bevat hulpprogram ma's voor het beveiligen van gegevens op basis van de beveiligings-en nalevings behoeften van uw bedrijf. Dit document is gericht op:

- Hoe gegevens worden beveiligd in de rest van Microsoft Azure
- Bespreekt de verschillende onderdelen die deel uitmaken van de implementatie van gegevens beveiliging,
- Beoordelingen van de voor-en nadelen van de verschillende beveiligings benaderingen voor sleutel beheer.

Versleuteling op rest is een gemeen schappelijke beveiligings vereiste. In azure kunnen organisaties gegevens in rust versleutelen zonder het risico of de kosten van een aangepaste oplossing voor sleutel beheer. Organisaties hebben de mogelijkheid om de versleuteling van Azure volledig te beheren. Daarnaast hebben organisaties verschillende opties om versleuteling of versleutelings sleutels nauw keurig te beheren.

## <a name="what-is-encryption-at-rest"></a>Wat is versleuteling in rust?

Versleuteling is de veilige code ring van gegevens die worden gebruikt om de vertrouwelijkheid van gegevens te beveiligen. De versleuteling bij rest-ontwerpen in azure gebruikt symmetrische versleuteling om grote hoeveel heden gegevens snel te versleutelen en te ontsleutelen op basis van een eenvoudig conceptueel model:

- Een symmetrische versleutelings sleutel wordt gebruikt voor het versleutelen van gegevens die naar opslag worden geschreven.
- Dezelfde versleutelings sleutel wordt gebruikt om die gegevens te ontsleutelen wanneer deze gereed is voor gebruik in het geheugen.
- Gegevens kunnen gepartitioneerd zijn en verschillende sleutels kunnen voor elke partitie worden gebruikt.
- Sleutels moeten worden opgeslagen op een veilige locatie met toegangs beheer op basis van identiteit en controle beleid. Gegevens versleutelings sleutels worden vaak versleuteld met een sleutel versleutelings sleutel in Azure Key Vault om de toegang verder te beperken.

In de praktijk moeten sleutel beheer-en controle scenario's, evenals schaal-en beschikbaarheids garanties, extra constructs vereisen. Microsoft Azure versleuteling bij rest-concepten en-onderdelen worden hieronder beschreven.

## <a name="the-purpose-of-encryption-at-rest"></a>Het doel van versleuteling bij rust

Versleuteling bij rest biedt gegevens beveiliging voor opgeslagen gegevens (op rest). Aanvallen tegen gegevens op rest zijn onder andere pogingen om fysieke toegang te verkrijgen tot de hardware waarop de gegevens worden opgeslagen en vervolgens de Inge sloten gegevens in gevaar te brengen. In een dergelijke aanval is het mogelijk dat de harde schijf van een server tijdens het onderhoud niet kan worden verwerkt, waardoor een aanvaller de harde schijf kan verwijderen. Later zou de aanvaller de harde schijf in een computer onder hun controle kunnen plaatsen om toegang te krijgen tot de gegevens.

Versleuteling op rest is ontworpen om te voor komen dat een aanvaller toegang krijgt tot de niet-versleutelde gegevens door ervoor te zorgen dat de gegevens worden versleuteld wanneer ze op schijf zijn. Als een aanvaller een harde schijf verkrijgt met versleutelde gegevens, maar niet de versleutelings sleutels, moet de aanvaller de versleuteling verslaan om de gegevens te lezen. Deze aanval is veel ingewik kelder en maakt gebruik van resources dan toegang tot niet-versleutelde gegevens op een harde schijf. Daarom wordt versleuteling in rust sterk aanbevolen en is een hoge prioriteit vereist voor veel organisaties.

Versleuteling in rust kan ook worden vereist door de nood zaak van een organisatie voor data governance en nalevings inspanningen. Industriële en wettelijke voor schriften zoals HIPAA, PCI en FedRAMP, leggen specifieke veiligheids maatregelen vast over de vereisten voor gegevens bescherming en-versleuteling. Versleuteling op rest is een verplichte maat regel die vereist is om te voldoen aan sommige van deze voor Schriften. Zie voor meer informatie over de micro soft-benadering van FIPS 140-2-validatie [Federal Information Processing Standard (FIPS)-publicatie 140-2](/microsoft-365/compliance/offering-fips-140-2).

Naast het voldoen aan de nalevings vereisten en de regelgeving, biedt versleuteling op rest een ingrijpende beveiliging. Microsoft Azure biedt een compatibel platform voor services, toepassingen en gegevens. Het biedt ook uitgebreide functionaliteit en fysieke beveiliging, gegevens toegangs beheer en controle. Het is echter belang rijk om extra "overlappende" beveiligings maatregelen te bieden voor het geval een van de andere veiligheids maatregelen mislukt en versleuteling in rust biedt een dergelijke veiligheids maatregel.

Micro soft hecht veel belang aan het versleutelen van de rest-opties in Cloud Services en geeft klanten controle over de versleutelings sleutels en logboeken van het sleutel gebruik. Daarnaast werkt micro soft standaard bij het versleutelen van alle klant gegevens op rest.

## <a name="azure-encryption-at-rest-components"></a>Azure-versleuteling bij rest-onderdelen

Zoals eerder beschreven, is het doel van het versleutelen van versleuteling bij rest de gegevens die persistent zijn op schijf versleuteld met een geheime versleutelings sleutel. U kunt het beste een beveiligde sleutel maken, opslag, Toegangs beheer en beheer van de versleutelings sleutels opgeven. Hoewel de details kunnen verschillen, kan de Azure-Services-versleuteling bij rest-implementaties worden beschreven in termen die in het volgende diagram worden geïllustreerd.

![Onderdelen](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

De opslag locatie van de versleutelings sleutels en toegangs beheer voor die sleutels is centraal voor een versleuteling in het rest-model. De sleutels moeten zeer goed worden beveiligd, maar kunnen worden beheerd door opgegeven gebruikers en beschikbaar zijn voor specifieke services. Voor Azure-Services is Azure Key Vault de aanbevolen oplossing voor sleutel opslag en biedt een gemeen schappelijke beheer ervaring tussen services. Sleutels worden opgeslagen en beheerd in sleutel kluizen, en toegang tot een sleutel kluis kan worden gegeven aan gebruikers of services. Azure Key Vault ondersteunt het maken van sleutels of het importeren van klant sleutels voor gebruik in scenario's met door de klant beheerde versleutelings sleutel.

### <a name="azure-active-directory"></a>Azure Active Directory

Machtigingen voor het gebruik van sleutels die zijn opgeslagen in Azure Key Vault, om deze te beheren of te openen voor versleuteling bij rest versleuteling en ontsleuteling, kunnen worden verleend aan Azure Active Directory-accounts.

### <a name="key-hierarchy"></a>Sleutel hiërarchie

Er wordt meer dan één versleutelings sleutel gebruikt in een versleuteling bij rest-implementatie. Het opslaan van een versleutelings sleutel in Azure Key Vault zorgt voor veilige toegang tot de sleutel en Centraal beheer van sleutels. De lokale toegang tot de versleutelings sleutels van de service is echter efficiënter voor bulk versleuteling en ontsleuteling dan met Key Vault voor elke gegevens bewerking, waardoor een sterkere versleuteling en betere prestaties mogelijk zijn. Het beperken van het gebruik van één versleutelings sleutel vermindert het risico dat de sleutel wordt aangetast en de kosten voor het opnieuw versleutelen wanneer een sleutel moet worden vervangen. Azure-versleuteling bij rest-modellen gebruiken een belang rijke hiërarchie die bestaat uit de volgende soorten sleutels om deze behoeften aan te pakken:

- **Gegevens versleutelings sleutel (dek)** : een SYMMETRISCHe AES256-sleutel die wordt gebruikt om een partitie of gegevens blok te versleutelen.  Eén resource kan veel partities en veel gegevens versleutelings sleutels hebben. Het versleutelen van elk gegevens blok met een andere sleutel maakt crypto-analyse aanvallen lastiger. Toegang tot DEKs is vereist voor de resource provider of het toepassings exemplaar dat een specifiek blok versleutelt en ontsleutelt. Wanneer een DEK wordt vervangen door een nieuwe sleutel, moeten alleen de gegevens in het bijbehorende blok opnieuw worden versleuteld met de nieuwe sleutel.
- Sleutel versleutelings **sleutel (KEK)** : een versleutelings sleutel die wordt gebruikt om de gegevens versleutelings sleutels te versleutelen. Door gebruik te maken van een versleutelings sleutel die Key Vault nooit verlaat, kunnen de gegevens versleutelings sleutels zelf worden versleuteld en beheerd. De entiteit die toegang heeft tot de KEK kan afwijken van de entiteit waarvoor de DEK is vereist. Een entiteit kan toegang tot de DEK Broker om de toegang van elke DEK te beperken tot een specifieke partitie. Omdat de KEK is vereist voor het ontsleutelen van de DEKs, is de KEK een specifiek punt waarmee DEKs effectief kan worden verwijderd door het verwijderen van de KEK.

De gegevens versleutelings sleutels, versleuteld met de sleutel versleutelings sleutels, worden afzonderlijk opgeslagen en alleen een entiteit met toegang tot de sleutel coderings sleutel kan deze versleutelings sleutels ontsleutelen. Verschillende modellen van sleutel opslag worden ondersteund. Zie [gegevens versleutelings modellen](encryption-models.md) voor meer informatie.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Versleuteling in rust in micro soft-Cloud Services

Microsoft Cloud Services worden in alle drie Cloud modellen gebruikt: IaaS, PaaS, SaaS. Hieronder ziet u voor beelden van hoe ze in elk model passen:

- Software Services, aangeduid als software als een server of SaaS, die toepassingen hebben die door de cloud worden verschaft, zoals Microsoft 365.
- Platform services die klanten gebruikmaken van de cloud in hun toepassingen, met behulp van de Cloud voor zaken als opslag, analyses en service bus-functionaliteit.
- Infrastructuur services of Infrastructure as a Service (IaaS) waarin de klant besturings systemen en toepassingen implementeert die worden gehost in de Cloud en mogelijk gebruikmaken van andere Cloud Services.

### <a name="encryption-at-rest-for-saas-customers"></a>Versleuteling op rest voor SaaS-klanten

SaaS-klanten (Software as a Service) hebben doorgaans versleuteling op rest ingeschakeld of beschikbaar in elke service. Microsoft 365 heeft verschillende opties voor klanten om versleuteling op rest te verifiëren of in te scha kelen. Zie [versleuteling in Microsoft 365](/microsoft-365/compliance/encryption)voor meer informatie over Microsoft 365 Services.

### <a name="encryption-at-rest-for-paas-customers"></a>Versleuteling bij rest voor PaaS-klanten

De gegevens van platform as a Service (PaaS) bevinden zich doorgaans in een opslag service, zoals Blob Storage, maar kunnen ook worden opgeslagen in de cache van de toepassings Execution omgeving, zoals een virtuele machine. Bekijk de onderstaande tabel voor de opslag-en toepassings platformen die u gebruikt om de versleutelings opties voor de beschik bare rest te bekijken.

### <a name="encryption-at-rest-for-iaas-customers"></a>Versleuteling bij rest voor IaaS-klanten

IaaS-klanten (Infrastructure as a Service) kunnen een groot aantal services en toepassingen gebruiken. IaaS Services kunnen versleuteling in de rest inschakelen in hun door Azure gehoste virtuele machines en Vhd's met behulp van Azure Disk Encryption.

#### <a name="encrypted-storage"></a>Versleutelde opslag

Net als PaaS kunnen IaaS-oplossingen gebruikmaken van andere Azure-Services die gegevens versleuteld bewaren op rest. In dergelijke gevallen kunt u de versleuteling inschakelen op rest-ondersteuning, zoals wordt geboden door elke verbruikte Azure-service. In de onderstaande tabel worden de belangrijkste opslag-, service-en toepassings platforms en het versleutelings model op rest-ondersteuning opgesomd.

#### <a name="encrypted-compute"></a>Versleutelde compute

Alle Managed Disks, moment opnamen en installatie kopieën worden versleuteld met behulp van Storage Service Encryption met een door een service beheerde sleutel. Een meer complete versleuteling bij rest-oplossing zorgt ervoor dat de gegevens niet in een niet-versleutelde vorm worden bewaard. Tijdens het verwerken van de gegevens op een virtuele machine kunnen gegevens worden bewaard in het Windows-pagina bestand of het Linux-wissel bestand, een crash dump of een toepassings logboek. Om ervoor te zorgen dat deze gegevens in rust worden versleuteld, kunnen IaaS-toepassingen gebruikmaken van Azure Disk Encryption op een virtuele machine met Azure IaaS (Windows of Linux) en virtuele schijf.

#### <a name="custom-encryption-at-rest"></a>Aangepaste versleuteling bij rest

Het wordt aanbevolen om waar mogelijk IaaS toepassingen Azure Disk Encryption en versleuteling te gebruiken bij rest opties die worden verschaft door alle verbruikte Azure-Services. In sommige gevallen, zoals onregelmatige versleutelings vereisten of opslag op basis van Azure, moet een ontwikkelaar van een IaaS-toepassing mogelijk versleuteling op rest zelf implementeren. Ontwikkel aars van IaaS-oplossingen kunnen beter integreren met Azure-beheer en klant verwachtingen door gebruik te maken van bepaalde Azure-onderdelen. Met name moeten ontwikkel aars de Azure Key Vault-service gebruiken voor het leveren van beveiligde sleutel opslag en hun klanten een consistente sleutel beheer opties bieden met die van de meeste services van het Azure-platform. Daarnaast moeten aangepaste oplossingen Azure-Managed service-identiteiten gebruiken om service accounts toegang te geven tot versleutelings sleutels. Voor informatie over ontwikkel aars over Azure Key Vault en beheerde service-identiteiten raadpleegt u hun respectieve Sdk's.

## <a name="azure-resource-providers-encryption-model-support"></a>Ondersteuning voor het versleutelings model van Azure-resource providers

Microsoft Azure Services bieden ondersteuning voor een of meer van de versleuteling bij rest-modellen. Voor sommige services zijn echter een of meer van de versleutelings modellen niet van toepassing. Voor services die door de klant beheerde sleutel scenario's ondersteunen, kunnen ze slechts een subset van de sleutel typen ondersteunen die Azure Key Vault ondersteunt voor sleutel versleutelings sleutels. Daarnaast kunnen services de ondersteuning voor deze scenario's en sleutel typen in verschillende schema's vrijgeven. In deze sectie wordt de versleuteling op rest-ondersteuning beschreven op het moment van schrijven voor elk van de belangrijkste Azure-Services voor gegevens opslag.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Elke klant die gebruikmaakt van Azure Infrastructure as a Service (IaaS)-functies, kan versleutelen op rest voor hun IaaS Vm's en schijven via Azure Disk Encryption. Zie de [Azure Disk Encryption-documentatie](./azure-disk-encryption-vms-vmss.md)voor meer informatie over Azure Disk Encryption.

#### <a name="azure-storage"></a>Azure Storage

Alle Azure Storage services (Blob-opslag, wachtrij opslag, tabel opslag en Azure Files) ondersteunen server versleuteling in rust. Sommige services ondersteunen daarnaast door de klant beheerde sleutels en versleuteling aan de client zijde.

- Aan de server zijde: alle Azure Storage services maken server zijde versleuteling mogelijk door gebruik te maken van door de service beheerde sleutels, die transparant zijn voor de toepassing. Zie [Azure Storage Service Encryption voor data-at-rest](../../storage/common/storage-service-encryption.md). Azure Blob-opslag en Azure Files ondersteunen ook RSA 2048-bits door de klant beheerde sleutels in Azure Key Vault. Zie [Storage service Encryption het gebruik van door de klant beheerde sleutels in azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md)voor meer informatie.
- Client zijde: Azure-blobs,-tabellen en-wacht rijen ondersteunen versleuteling aan de client zijde. Wanneer u versleuteling aan de client zijde gebruikt, versleutelt klanten de gegevens en uploadt de gegevens als een versleutelde blob. Sleutel beheer wordt uitgevoerd door de klant. Zie [versleuteling aan client zijde en Azure Key Vault voor Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md)voor meer informatie.

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database biedt momenteel ondersteuning voor versleuteling in rust voor micro soft-beheerde service-en versleutelings scenario's aan de client zijde.

Ondersteuning voor Server versleuteling wordt momenteel geboden via de SQL-functie met de naam Transparent Data Encryption. Zodra een Azure SQL Database klant wordt ingeschakeld, wordt er automatisch een TDE-sleutel gemaakt en beheerd. Versleuteling in rust kan worden ingeschakeld op Data Base-en server niveau. Vanaf 2017 juni is [transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) standaard ingeschakeld voor nieuw gemaakte data bases. Azure SQL Database ondersteunt door de klant beheerde sleutels van RSA 2048-bits in Azure Key Vault. Zie [transparent Data Encryption met Bring your own Key ondersteuning voor Azure SQL database en Data Warehouse](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql)voor meer informatie.

Versleuteling aan client zijde van Azure SQL Database gegevens wordt ondersteund via de [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) -functie. Always Encrypted gebruikt een sleutel die door de client is gemaakt en opgeslagen. Klanten kunnen de hoofd sleutel opslaan in een Windows-certificaat archief, Azure Key Vault of een lokale hardware Security module. Met behulp van SQL Server Management Studio kiezen SQL-gebruikers welke sleutel ze willen gebruiken om de kolom te versleutelen.

## <a name="conclusion"></a>Conclusie

De beveiliging van klant gegevens die zijn opgeslagen in Azure-Services is van cruciaal belang voor micro soft. Alle door Azure gehoste services zijn doorgevoerd om versleuteling te bieden met behulp van rest-opties. Azure-Services ondersteunen door service beheerde sleutels, door de klant beheerde sleutels of versleuteling aan de client zijde. Azure-Services verbeteren de versleuteling op rest Beschik baarheid en nieuwe opties zijn gepland voor preview en algemene Beschik baarheid in de komende maanden.

## <a name="next-steps"></a>Volgende stappen

- Zie [gegevens versleutelings modellen](encryption-models.md) voor meer informatie over door service beheerde sleutels en door de klant beheerde sleutels.
- Meer informatie over hoe Azure gebruikmaakt van [dubbele versleuteling](double-encryption.md) om bedreigingen te verhelpen die worden gebruikt voor het versleutelen van gegevens.
- Meer informatie over wat micro soft doet om de [platform integriteit en beveiliging](platform.md) te garanderen van hosts die de hardware-en firmware-build, integratie, uitoefening en reparatie pijplijnen door lopen.
