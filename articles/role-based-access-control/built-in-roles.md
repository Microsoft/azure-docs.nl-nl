---
title: ingebouwde Azure-rollen - Azure RBAC
description: In dit artikel worden de ingebouwde Azure-rollen voor op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) beschreven. De lijst bevat Actions, NotActions, DataActions en NotDataActions.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 04/09/2021
ms.custom: generated
ms.openlocfilehash: c89e6ed98e0a71f530cefda4cc1f42a27996d805
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518504"
---
# <a name="azure-built-in-roles"></a>Ingebouwde Azure-rollen

[Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](overview.md) heeft verschillende ingebouwde Azure-rollen die u kunt toewijzen aan gebruikers, groepen, service-principals en beheerde identiteiten. Roltoewijzingen zijn de manier waarop u de toegang tot Azure-resources kunt beheren. Als de ingebouwde rollen niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen [aangepaste Azure-rollen](custom-roles.md) maken. Zie Stappen voor het toewijzen van een Azure-rol voor meer informatie over het [toewijzen van rollen.](role-assignments-steps.md)

In dit artikel worden de ingebouwde Azure-rollen vermeld. Als u op zoek bent naar beheerdersrollen voor Azure Active Directory (Azure AD), zie [Ingebouwde Azure AD-rollen.](../active-directory/roles/permissions-reference.md)

De volgende tabel bevat een korte beschrijving van elke ingebouwde rol. Klik op de rolnaam om de lijst `Actions` met , , en voor elke rol weer te `NotActions` `DataActions` `NotDataActions` geven. Zie Inzicht in Azure-roldefinities voor meer informatie over wat deze acties betekenen en hoe ze van toepassing zijn op de beheer- en [gegevensvlakken.](role-definitions.md)

## <a name="all"></a>Alles

> [!div class="mx-tableFixed"]
> | Ingebouwde rol | Beschrijving | Id |
> | --- | --- | --- |
> | **Algemeen** |  |  |
> | [Inzender](#contributor) | Verleent volledige toegang voor het beheren van alle resources, maar biedt u niet de mogelijkheid om rollen toe te wijzen in Azure RBAC, toewijzingen te beheren in Azure Blueprints of galerieën met afbeeldingen te delen. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Eigenaar](#owner) | Verleent volledige toegang voor het beheren van alle resources, inclusief de mogelijkheid om rollen toe te wijzen in Azure RBAC. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Lezer](#reader) | Alles weergeven resources, maar staat u niet toe om wijzigingen aan te brengen. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Beheerder van gebruikerstoegang](#user-access-administrator) | Hiermee kunt u gebruikerstoegang tot Azure-resources beheren. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Compute** |  |  |
> | [Inzender voor klassieke virtuele machines](#classic-virtual-machine-contributor) | Hiermee kunt u klassieke virtuele machines beheren, maar geen toegang krijgen tot deze machines, en niet het virtuele netwerk of het opslagaccount waarmee ze zijn verbonden. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Aanmeldgegevens van de beheerder van de virtuele machine](#virtual-machine-administrator-login) | De Virtual Machines in de portal weergeven en u aanmelden als beheerder | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Inzender voor virtuele machines](#virtual-machine-contributor) | Hiermee kunt u virtuele machines beheren, maar geen toegang tot deze machines, en niet tot het virtuele netwerk of opslagaccount waarmee ze zijn verbonden. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Gebruikersmelding voor virtuele machine](#virtual-machine-user-login) | Bekijk Virtual Machines in de portal en meld u aan als een gewone gebruiker. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Netwerken** |  |  |
> | [Inzender voor CDN-eindpunten](#cdn-endpoint-contributor) | Kan CDN-eindpunten beheren, maar kan geen toegang verlenen aan andere gebruikers. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN-eindpuntlezer](#cdn-endpoint-reader) | Kan CDN-eindpunten weergeven, maar kan geen wijzigingen aanbrengen. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Inzender voor CDN-profielen](#cdn-profile-contributor) | Kan CDN-profielen en hun eindpunten beheren, maar kan geen toegang verlenen aan andere gebruikers. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN-profiellezer](#cdn-profile-reader) | Kan CDN-profielen en hun eindpunten weergeven, maar kan geen wijzigingen aanbrengen. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Inzender voor klassieke netwerken](#classic-network-contributor) | Hiermee kunt u klassieke netwerken beheren, maar geen toegang tot deze netwerken. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Inzender voor DNS-zone](#dns-zone-contributor) | Hiermee kunt u DNS-zones en recordsets beheren in Azure DNS, maar kunt u niet beheren wie er toegang toe heeft. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Inzender voor netwerken](#network-contributor) | Hiermee kunt u netwerken beheren, maar geen toegang tot deze netwerken. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Privé-DNS zone-inzender](#private-dns-zone-contributor) | Hiermee kunt u privé-DNS-zonebronnen beheren, maar niet de virtuele netwerken waar ze aan zijn gekoppeld. | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Traffic Manager-inzender](#traffic-manager-contributor) | Hiermee kunt u Traffic Manager beheren, maar kunt u niet bepalen wie er toegang tot heeft. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [Avere-inzender](#avere-contributor) | Kan een cluster met Avere vFXT maken en beheren. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere Operator](#avere-operator) | Wordt gebruikt door het Avere vFXT cluster om het cluster te beheren | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Back-upbijdrager](#backup-contributor) | Hiermee kunt u de back-upservice beheren, maar u kunt geen kluizen maken en anderen toegang geven | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Back-upoperator](#backup-operator) | Hiermee kunt u back-upservices beheren, behalve het verwijderen van back-ups, het maken van een kluis en het verlenen van toegang aan anderen | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Back-uplezer](#backup-reader) | Kan back-upservices weergeven, maar kan geen wijzigingen aanbrengen | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Inzender voor klassieke opslagaccounts](#classic-storage-account-contributor) | Hiermee kunt u klassieke opslagaccounts beheren, maar geen toegang tot deze accounts. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Klassieke servicerol sleuteloperator voor opslagaccounts](#classic-storage-account-key-operator-service-role) | Sleuteloperators voor klassieke opslagaccounts kunnen sleutels in klassieke opslagaccounts in een lijst met en opnieuw worden weergegeven | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Data Box-inzender](#data-box-contributor) | Hiermee kunt u alles beheren onder Data Box Service, behalve toegang te verlenen aan anderen. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Data Box Lezer](#data-box-reader) | Hiermee kunt u de Data Box beheren, behalve het maken van order- of bewerkingsgegevens en het verlenen van toegang aan anderen. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Lake Analytics Developer](#data-lake-analytics-developer) | Hiermee kunt u uw eigen taken verzenden, bewaken en beheren, maar geen accounts maken Data Lake Analytics verwijderen. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Lezer en gegevenstoegang](#reader-and-data-access) | Hiermee kunt u alles bekijken, maar u kunt geen opslagaccount of ingesloten resource verwijderen of maken. Het biedt ook lees-/schrijftoegang tot alle gegevens in een opslagaccount via toegang tot opslagaccountsleutels. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Inzender voor opslagaccounts](#storage-account-contributor) | Staat beheer van opslagaccounts toe. Biedt toegang tot de accountsleutel, die kan worden gebruikt voor toegang tot gegevens via gedeelde sleutelautorisatie. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Servicerol Sleuteloperator voor opslagaccount](#storage-account-key-operator-service-role) | Maakt het mogelijk om toegangssleutels voor opslagaccounts weer te geven en opnieuw te gebruiken. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Inzender voor Storage Blob-gegevens](#storage-blob-data-contributor) | Lees, schrijf en verwijder Azure Storage containers en blobs. Zie Machtigingen voor het aanroepen van blob- en wachtrijgegevensbewerkingen voor meer informatie over welke acties vereist zijn voor een [bepaalde gegevensbewerking.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Eigenaar van opslagblobgegevens](#storage-blob-data-owner) | Biedt volledige toegang tot Azure Storage blobcontainers en -gegevens, waaronder het toewijzen van POSIX-toegangsbeheer. Zie Machtigingen voor het aanroepen van blob- en wachtrijgegevensbewerkingen voor meer informatie over welke acties vereist zijn voor een [bepaalde gegevensbewerking.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Lezer voor opslagblobgegevens](#storage-blob-data-reader) | Lees en vermeld Azure Storage containers en blobs. Zie Machtigingen voor het aanroepen van blob- en wachtrijgegevensbewerkingen voor meer informatie over welke acties vereist zijn voor een [bepaalde gegevensbewerking.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Storage Blob Delegator](#storage-blob-delegator) | Haal een sleutel voor gebruikersdelegatie op, die vervolgens kan worden gebruikt om een Shared Access Signature te maken voor een container of blob die is ondertekend met Azure AD-referenties. Zie Create [a user delegation SAS (Een SAS voor gebruikersdelegatie maken) voor meer informatie.](/rest/api/storageservices/create-user-delegation-sas) | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Inzender voor opslagbestandsgegevens via SMB-share](#storage-file-data-smb-share-contributor) | Hiermee kunt u lezen, schrijven en verwijderen van toegang tot bestanden/mappen in Azure-bestands shares. Deze rol heeft geen ingebouwd equivalent op Windows-bestandsservers. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Inzender met verhoogde bevoegdheden voor opslagbestandsgegevens via SMB-share](#storage-file-data-smb-share-elevated-contributor) | Hiermee kunnen ACL's voor bestanden/mappen in Azure-bestands shares worden gelezen, geschreven, verwijderd en gewijzigd. Deze rol is gelijk aan een wijzigings-ACL voor bestands share op Windows-bestandsservers. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Lezer voor opslagbestandgegevens via SMB-share](#storage-file-data-smb-share-reader) | Biedt leestoegang tot bestanden/mappen in Azure-bestands shares. Deze rol is gelijk aan een bestandsdeel-ACL voor lezen op Windows-bestandsservers. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Inzender voor opslagwachtrijgegevens](#storage-queue-data-contributor) | Lees, schrijf en verwijder Azure Storage wachtrijen en wachtrijberichten. Zie Machtigingen voor het aanroepen van blob- en wachtrijgegevensbewerkingen voor meer informatie over welke acties vereist zijn voor een [bepaalde gegevensbewerking.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Berichtenprocessor voor opslagwachtrijgegevens](#storage-queue-data-message-processor) | Een bericht bekijken, ophalen en verwijderen uit een Azure Storage wachtrij. Zie Machtigingen voor het aanroepen van blob- en wachtrijgegevensbewerkingen voor meer informatie over welke acties vereist zijn voor een [bepaalde gegevensbewerking.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Afzender van opslagwachtrijgegevensbericht](#storage-queue-data-message-sender) | Berichten toevoegen aan een Azure Storage wachtrij. Zie Machtigingen voor het aanroepen van blob- en wachtrijgegevensbewerkingen voor meer informatie over welke acties vereist zijn voor een [bepaalde gegevensbewerking.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Lezer opslagwachtrijgegevens](#storage-queue-data-reader) | Lees en vermeld Azure Storage wachtrijen en wachtrijberichten. Zie Machtigingen voor het aanroepen van blob- en wachtrijgegevensbewerkingen voor meer informatie over welke acties vereist zijn voor een [bepaalde gegevensbewerking.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Azure Maps Inzender voor gegevens](#azure-maps-data-contributor) | Verleent toegang tot lezen, schrijven en verwijderen van toegang tot gerelateerde gegevens uit een Azure Maps-account. | 8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204 |
> | [Azure Maps-gegevenslezer](#azure-maps-data-reader) | Verleent toegang tot het lezen van kaartgerelateerde gegevens uit een Azure Maps-account. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Azure Spring Cloud-gegevenslezer](#azure-spring-cloud-data-reader) | Leestoegang tot Azure Spring Cloud toestaan | b5537268-8956-4941-a8f0-646150406f0c |
> | [Inzender voor zoekservice](#search-service-contributor) | Hiermee kunt u Zoekservices beheren, maar geen toegang krijgen tot deze services. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [SignalR AccessKey Reader](#signalr-accesskey-reader) | Toegangssleutels SignalR Service lezen | 04165923-9d83-45d5-8227-78b77b0a687e |
> | [SignalR App Server (preview)](#signalr-app-server-preview) | Hiermee kan uw app-server toegang SignalR Service met AAD-auth-opties. | 420fcaa2-552c-430f-98ca-3264be4806c7 |
> | [SignalR-inzender](#signalr-contributor) | SignalR-serviceresources maken, lezen, bijwerken en verwijderen | 8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761 |
> | [Serverloze inzender van SignalR (preview)](#signalr-serverless-contributor-preview) | Hiermee kan uw app toegang krijgen tot de service in de serverloze modus met AAD-auth-opties. | fd53cd77-2268-407a-8f46-7e7863d0f521 |
> | [SignalR Service eigenaar (preview)](#signalr-service-owner-preview) | Volledige toegang tot Azure SignalR Service REST API's | 7e4f1700-ea5a-4f59-8f37-079cfe29dce3 |
> | [SignalR Service Lezer (preview)](#signalr-service-reader-preview) | Alleen-lezentoegang tot Azure SignalR Service REST API's | ddde6b66-c0df-4114-a159-3618637b3035 |
> | [Inzender voor webplannen](#web-plan-contributor) | Hiermee kunt u de webplannen voor websites beheren, maar geen toegang tot de websites. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Inzender voor websites](#website-contributor) | Hiermee kunt u websites (geen webplannen) beheren, maar geen toegang tot deze websites. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Containers** |  |  |
> | [AcrDelete](#acrdelete) | acr delete | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | acr image signer | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | acr pull | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | acr push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | acr quarantine data reader | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | acr quarantine data writer | c8d4ff99-41c3-41a8-9f60-21df be59608 |
> | [Azure Kubernetes Service clusterbeheerdersrol](#azure-kubernetes-service-cluster-admin-role) | De actie Clusterbeheerderreferenties opsommen. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Azure Kubernetes Service clustergebruikersrol](#azure-kubernetes-service-cluster-user-role) | Lijst met gebruikersreferenties voor cluster. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Azure Kubernetes Service Inzender-rol](#azure-kubernetes-service-contributor-role) | Verleent toegang tot lees- en Azure Kubernetes Service clusters | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [Azure Kubernetes Service RBAC-beheerder](#azure-kubernetes-service-rbac-admin) | Hiermee kunt u alle resources onder cluster/naamruimte beheren, behalve resourcequota en naamruimten bijwerken of verwijderen. | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [Azure Kubernetes Service RBAC-clusterbeheerder](#azure-kubernetes-service-rbac-cluster-admin) | Hiermee kunt u alle resources in het cluster beheren. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [Azure Kubernetes Service RBAC-lezer](#azure-kubernetes-service-rbac-reader) | Hiermee staat u alleen-lezentoegang toe om de meeste objecten in een naamruimte te zien. Het weergeven van rollen of rolbindingen is niet toegestaan. Deze rol staat het weergeven van geheimen niet toe, omdat het lezen van de inhoud van Geheimen toegang biedt tot ServiceAccount-referenties in de naamruimte, waardoor API-toegang mogelijk is als elk ServiceAccount in de naamruimte (een vorm van escalatie van bevoegdheden). Door deze rol toe te passen op clusterbereik, krijgt u toegang tot alle naamruimten. | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [Azure Kubernetes Service RBAC Writer](#azure-kubernetes-service-rbac-writer) | Hiermee staat u lees-/schrijftoegang toe tot de meeste objecten in een naamruimte. Deze rol staat het weergeven of wijzigen van rollen of rolbindingen niet toe. Met deze rol kunt u echter toegang krijgen tot Geheimen en pods uitvoeren als elk ServiceAccount in de naamruimte, zodat deze kan worden gebruikt om de API-toegangsniveaus van elk ServiceAccount in de naamruimte te verkrijgen. Als u deze rol op clusterbereik toe passen, krijgt u toegang tot alle naamruimten. | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **Databases** |  |  |
> | [Cosmos DB rol accountlezer](#cosmos-db-account-reader-role) | Kan de Azure Cosmos DB lezen. Zie [Inzender voor DocumentDB-accounts](#documentdb-account-contributor) voor het beheren Azure Cosmos DB accounts. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB Operator](#cosmos-db-operator) | Hiermee kunt u Azure Cosmos DB accounts beheren, maar geen toegang krijgen tot gegevens in deze accounts. Hiermee voorkomt u toegang tot accountsleutels en verbindingsreeksen. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Kan een herstelaanvraag indienen voor een Cosmos DB database of een container voor een account | db7b14f2-5adf-42da-9f96-f2ee17cb5cb |
> | [CosmosRestoreOperator](#cosmosrestoreoperator) | Kan herstelactie uitvoeren voor Cosmos DB databaseaccount met continue back-upmodus | 5432c526-bc82-444a-b7ba-57c5b0b5b34f |
> | [Inzender voor DocumentDB-account](#documentdb-account-contributor) | Kan uw Azure Cosmos DB beheren. Azure Cosmos DB staat voorheen bekend als DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Redis Cache-inzender](#redis-cache-contributor) | Hiermee kunt u Redis-caches beheren, maar geen toegang tot deze caches. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Inzender voor SQL DB](#sql-db-contributor) | Hiermee kunt u SQL-databases beheren, maar geen toegang krijgen tot deze databases. U kunt ook hun beveiligingsbeleid of hun bovenliggende SQL-servers niet beheren. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL Managed Instance-inzender](#sql-managed-instance-contributor) | Hiermee kunt u SQL Managed Instances en de vereiste netwerkconfiguratie beheren, maar u kunt geen toegang verlenen aan anderen. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL-beveiligingsbeheerder](#sql-security-manager) | Hiermee kunt u het beveiligingsbeleid van SQL-servers en -databases beheren, maar geen toegang tot deze servers. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server-inzender](#sql-server-contributor) | Hiermee kunt u SQL-servers en -databases beheren, maar geen toegang krijgen tot deze servers en niet het bijbehorende beveiligingsbeleid. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analyse** |  |  |
> | [Azure Event Hubs gegevenseigenaar](#azure-event-hubs-data-owner) | Biedt volledige toegang tot Azure Event Hubs resources. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure Event Hubs Ontvanger van gegevens](#azure-event-hubs-data-receiver) | Hiermee krijgt u toegang tot Azure Event Hubs resources. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Azure Event Hubs gegevens afzender](#azure-event-hubs-data-sender) | Hiermee kunt u toegang tot Azure Event Hubs verzenden. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Data Factory-inzender](#data-factory-contributor) | Maak en beheer gegevensfabrieken en onderliggende resources in deze resources. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Gegevens ops purgeren](#data-purger) | Privégegevens verwijderen uit een Log Analytics-werkruimte. | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [HDInsight-clusteroperator](#hdinsight-cluster-operator) | Hiermee kunt u HDInsight-clusterconfiguraties lezen en wijzigen. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Inzender voor HDInsight Domain Services](#hdinsight-domain-services-contributor) | Kan domain services-gerelateerde bewerkingen lezen, maken, wijzigen en verwijderen die nodig zijn voor HDInsight-Enterprise Security Package | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Inzender van Log Analytics](#log-analytics-contributor) | Log Analytics-inzender kan alle bewakingsgegevens lezen en bewakingsinstellingen bewerken. Het bewerken van bewakingsinstellingen omvat het toevoegen van de VM-extensie aan VM's; opslagaccountsleutels lezen om het verzamelen van logboeken van Azure Storage; Automation-accounts maken en configureren; oplossingen toevoegen; en azure diagnostics configureren voor alle Azure-resources. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Lezer van Log Analytics](#log-analytics-reader) | De Log Analytics-lezer kan alle bewakingsgegevens bekijken en doorzoeken, evenals bewakingsinstellingen, waaronder het weergeven van de configuratie van diagnostische Azure-gegevens op alle Azure-resources. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Purview DataAtor](#purview-data-curator) | De Microsoft.Purview-gegevenscurator kan catalogusgegevensobjecten maken, lezen, wijzigen en verwijderen en relaties tussen objecten tot stand brengen. Deze rol is in preview en kan worden gewijzigd. | 8a3c2885-9b38-4fd2-9d99-91af537c1347 |
> | [Gegevenslezer voor purviewen](#purview-data-reader) | De Microsoft.Purview-gegevenslezer kan catalogusgegevensobjecten lezen. Deze rol is in preview en kan worden gewijzigd. | ff100721-1b9d-43d8-af52-42b69c1272db |
> | [Gegevensbronbeheerder purviewen](#purview-data-source-administrator) | De microsoft.Purview-gegevensbronbeheerder kan gegevensbronnen en gegevensscans beheren. Deze rol is in preview en kan worden gewijzigd. | 200bba9e-f0c8-430f-892b-6f0794863803 |
> | [Inzender van schemaregisters (preview)](#schema-registry-contributor-preview) | Schemaregistergroepen en schema's lezen, schrijven en verwijderen. | 5dffeca3-4936-4216-b2bc-10343a5abb25 |
> | [Lezer van schemaregisters (preview)](#schema-registry-reader-preview) | Schemaregistergroepen en schema's lezen en weergeven. | 2c56ea50-c6b3-40a6-83c0-9d98858bc7d2 |
> | **Blockchain** |  |  |
> | [Toegang tot blockchain-lid-knooppunt (preview)](#blockchain-member-node-access-preview) | Hiermee kunt u toegang krijgen tot blockchainlidknooppunten | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI + machine learning** |  |  |
> | [Cognitive Services-inzender](#cognitive-services-contributor) | Hiermee kunt u sleutels van de Cognitive Services. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Cognitive Services Custom Vision-inzender](#cognitive-services-custom-vision-contributor) | Volledige toegang tot het project, inclusief de mogelijkheid om projecten weer te geven, te maken, te bewerken of te verwijderen. | c1ff6cc2-c111-46fe-8896-e0ef812ad9f3 |
> | [Cognitive Services Custom Vision implementatie](#cognitive-services-custom-vision-deployment) | Modellen publiceren, publiceren of exporteren. Implementatie kan het project weergeven, maar kan niet worden bijgewerkt. | 5c4089e1-6d96-4d2f-b296-c1bc7137275f |
> | [Cognitive Services Custom Vision Labeler](#cognitive-services-custom-vision-labeler) | Trainingsafbeeldingen weergeven, bewerken en de afbeeldingstags maken, toevoegen, verwijderen of verwijderen. Labelaars kunnen het project bekijken, maar kunnen niets anders dan trainingsafbeeldingen en tags bijwerken. | 88424f51-ebe7-446f-bc41-7fa16989e96c |
> | [Cognitive Services Custom Vision Reader](#cognitive-services-custom-vision-reader) | Alleen-lezenacties in het project. Lezers kunnen het project niet maken of bijwerken. | 93586559-c37d-4a6b-ba08-b9f0940c2d73 |
> | [Cognitive Services Custom Vision Cognitive Services Custom Vision](#cognitive-services-custom-vision-trainer) | Bekijk, bewerk projecten en train de modellen, met inbegrip van de mogelijkheid om de modellen te publiceren, uit te publiceren en te exporteren. Docenten kunnen het project niet maken of verwijderen. | 0a5ae4ab-0d65-4eeb-be61-29fc9b54394b |
> | [Cognitive Services Gegevenslezer (preview)](#cognitive-services-data-reader-preview) | Hiermee kunt u Cognitive Services lezen. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services Metrics Advisor Administrator](#cognitive-services-metrics-advisor-administrator) | Volledige toegang tot het project, inclusief de configuratie op systeemniveau. | cb43c632-a144-4ec5-977c-e80c4affc34a |
> | [Cognitive Services QnA Maker Editor](#cognitive-services-qna-maker-editor) | Hier kunt u een KB maken, bewerken, importeren en exporteren. U kunt een KB niet publiceren of verwijderen. | f4cc2bf9-21be-47a1-bdf1-5c5804381025 |
> | [Cognitive Services QnA Maker Reader](#cognitive-services-qna-maker-reader) | U kunt een KB alleen lezen en testen. | 466ccd10-b268-4a11-b098-b4849f024126 |
> | [Cognitive Services gebruiker](#cognitive-services-user) | Hiermee kunt u sleutels van de Cognitive Services. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Internet of Things** |  |  |
> | [Apparaatupdatebeheerder](#device-update-administrator) | Biedt u volledige toegang tot beheer- en inhoudsbewerkingen | 02ca0879-e8e4-47a5-a61e-5c618b76e64a |
> | [Apparaatupdate-inhoudsbeheerder](#device-update-content-administrator) | Geeft u volledige toegang tot inhoudsbewerkingen | 0378884a-3af5-44ab-8323-f5b22f9f3c98 |
> | [Inhoudslezer voor apparaatupdates](#device-update-content-reader) | Geeft u leestoegang tot inhoudsbewerkingen, maar staat het aanbrengen van wijzigingen niet toe | d1ee9a80-8b14-47f0-bdc2-f4a351625a7b |
> | [Beheerder van apparaatupdate-implementaties](#device-update-deployments-administrator) | Biedt u volledige toegang tot beheerbewerkingen | e4237640-0e3d-4a46-8fda-70bc94856432 |
> | [Lezer voor implementaties van apparaatupdates](#device-update-deployments-reader) | Geeft u leestoegang tot beheerbewerkingen, maar staat het aanbrengen van wijzigingen niet toe | 49e2f5d2-7741-4835-8efa-19e1fe35e47f |
> | [Lezer apparaatupdate](#device-update-reader) | Geeft u leestoegang tot beheer- en inhoudsbewerkingen, maar staat het aanbrengen van wijzigingen niet toe | e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f |
> | **Mixed reality** |  |  |
> | [Remote Rendering Administrator](#remote-rendering-administrator) | Biedt de gebruiker conversie-, sessie-, rendering- en diagnostische mogelijkheden voor Azure Remote Rendering | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Remote Rendering Client](#remote-rendering-client) | Biedt de gebruiker mogelijkheden voor sessie-, rendering- en diagnostische gegevens voor Azure Remote Rendering. | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Spatial Anchors-account inzender](#spatial-anchors-account-contributor) | Hiermee kunt u spatial anchors in uw account beheren, maar niet verwijderen | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Spatial Anchors accounteigenaar](#spatial-anchors-account-owner) | Hiermee kunt u ruimtelijke ankers in uw account beheren, inclusief het verwijderen ervan | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Spatial Anchors-accountlezer](#spatial-anchors-account-reader) | Hiermee kunt u eigenschappen van ruimtelijke ankers in uw account zoeken en lezen | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integratie** |  |  |
> | [API Management servicebijdrager](#api-management-service-contributor) | Kan de service en de API's beheren | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [API Management serviceoperatorrol](#api-management-service-operator-role) | Kan de service beheren, maar niet de API's | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [API Management servicelezerrol](#api-management-service-reader-role) | Alleen-lezentoegang tot service en API's | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [App Configuration eigenaar van gegevens](#app-configuration-data-owner) | Hiermee hebt u volledige toegang tot App Configuration gegevens. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [App Configuration gegevenslezer](#app-configuration-data-reader) | Hiermee staat u leestoegang tot App Configuration gegevens toe. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Azure Service Bus gegevenseigenaar](#azure-service-bus-data-owner) | Biedt volledige toegang tot Azure Service Bus resources. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure Service Bus-ontvanger](#azure-service-bus-data-receiver) | Hiermee kunt u toegang krijgen tot Azure Service Bus resources. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Azure Service Bus Afzender van gegevens](#azure-service-bus-data-sender) | Hiermee kunt u toegang tot Azure Service Bus verzenden. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Azure Stack registratie-eigenaar](#azure-stack-registration-owner) | Hiermee kunt u Azure Stack beheren. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [EventGrid-inzender](#eventgrid-contributor) | Hiermee kunt u EventGrid-bewerkingen beheren. | 1e241071-0855-49ea-94dc-649edcd759de |
> | [EventGrid EventSubscription Contributor](#eventgrid-eventsubscription-contributor) | Hiermee kunt u gebeurtenisabonnementbewerkingen voor EventGrid beheren. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription Reader](#eventgrid-eventsubscription-reader) | Hiermee kunt u EventGrid-gebeurtenisabonnementen lezen. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [FHIR-gegevensbijdrager](#fhir-data-contributor) | Rol biedt gebruiker of principal volledige toegang tot FHIR-gegevens | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [FHIR Data Export](#fhir-data-exporter) | Met rol kan de gebruiker of principal FHIR-gegevens lezen en exporteren | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [FHIR-gegevenslezer](#fhir-data-reader) | Rol staat gebruiker of principal toe om FHIR-gegevens te lezen | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [FHIR Data Writer](#fhir-data-writer) | Met rol kan de gebruiker of principal FHIR-gegevens lezen en schrijven | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [Integratieserviceomgeving-inzender](#integration-service-environment-contributor) | Hiermee kunt u integratieserviceomgevingen beheren, maar geen toegang tot deze omgevingen. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [Integratieserviceomgeving Developer](#integration-service-environment-developer) | Hiermee kunnen ontwikkelaars werkstromen, integratieaccounts en API-verbindingen maken en bijwerken in integratieserviceomgevingen. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Inzender voor Intelligent Systems-account](#intelligent-systems-account-contributor) | Hiermee kunt u Intelligent Systems-accounts beheren, maar geen toegang tot deze accounts. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Inzender voor logische apps](#logic-app-contributor) | Hiermee kunt u logische apps beheren, maar de toegang tot deze apps niet wijzigen. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Logische app-operator](#logic-app-operator) | Hiermee kunt u logische apps lezen, inschakelen en uitschakelen, maar niet bewerken of bijwerken. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identiteit** |  |  |
> | [Inzender voor beheerde identiteit](#managed-identity-contributor) | Door de gebruiker toegewezen identiteit maken, lezen, bijwerken en verwijderen | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Operator voor beheerde identiteit](#managed-identity-operator) | Door de gebruiker toegewezen identiteit lezen en toewijzen | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Beveiliging** |  |  |
> | [Attestation-inzender](#attestation-contributor) | Kan het exemplaar van de Attestation-provider lezen of verwijderen | bbf86eb8-f7b4-4cce-96e4-18cddf81d86e |
> | [Attestation-lezer](#attestation-reader) | Kan de eigenschappen van de Attestation-provider lezen | fd1bd22b-8476-40bc-a0bc-69b95687b9f3 |
> | [Azure Sentinel Automation-inzender](#azure-sentinel-automation-contributor) | Azure Sentinel Automation-inzender | f4c81013-99ee-4d62-a7ee-b3f1f648599a |
> | [Azure Sentinel Contributor](#azure-sentinel-contributor) | Azure Sentinel Contributor | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel Reader](#azure-sentinel-reader) | Azure Sentinel Reader | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel Responder](#azure-sentinel-responder) | Azure Sentinel Responder | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Key Vault Administrator](#key-vault-administrator) | Voer alle gegevensvlakbewerkingen uit op een sleutelkluis en alle objecten daarin, inclusief certificaten, sleutels en geheimen. Kan geen key vault-resources beheren of roltoewijzingen beheren. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Key Vault Certificates Officer](#key-vault-certificates-officer) | Voer een actie uit op de certificaten van een sleutelkluis, behalve machtigingen beheren. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure. | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [Key Vault-inzender](#key-vault-contributor) | U kunt sleutelkluizen beheren, maar u kunt geen rollen toewijzen in Azure RBAC en biedt u geen toegang tot geheimen, sleutels of certificaten. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Key Vault Crypto Officer](#key-vault-crypto-officer) | Voer een actie uit op de sleutels van een sleutelkluis, behalve machtigingen beheren. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Key Vault cryptoserviceversleutelingsgebruiker](#key-vault-crypto-service-encryption-user) | Metagegevens van sleutels lezen en wrap-/unwrap-bewerkingen uitvoeren. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Key Vault cryptogebruiker](#key-vault-crypto-user) | Cryptografische bewerkingen uitvoeren met behulp van sleutels. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure. | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Key Vault Lezer](#key-vault-reader) | Metagegevens van sleutelkluizen en de certificaten, sleutels en geheimen lezen. Kan geen gevoelige waarden lezen, zoals geheime inhoud of sleutelmateriaal. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure. | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [Key Vault Secrets Officer](#key-vault-secrets-officer) | Voer een actie uit op de geheimen van een sleutelkluis, behalve machtigingen beheren. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
> | [Key Vault Geheimen-gebruiker](#key-vault-secrets-user) | Geheime inhoud lezen. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure. | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [Inzender voor beheerde HSM](#managed-hsm-contributor) | Hiermee kunt u beheerde HSM-pools beheren, maar geen toegang tot deze pools. | 18500a29-7fe2-46b2-a342-b16a415e101d |
> | [Beveiligingsbeheerder](#security-admin) | Machtigingen voor de Security Center. Dezelfde machtigingen als de rol Beveiligingslezer en kunnen ook het beveiligingsbeleid bijwerken en waarschuwingen en aanbevelingen afwijzen. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Inzender voor beveiligingsevaluatie](#security-assessment-contributor) | Hiermee kunt u evaluaties naar Security Center | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Security Manager (verouderd)](#security-manager-legacy) | Dit is een verouderde rol. Gebruik in plaats daarvan Beveiligingsbeheerder. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Beveiligingslezer](#security-reader) | Machtigingen voor Security Center. Kan aanbevelingen, waarschuwingen, een beveiligingsbeleid en beveiligings states weergeven, maar kan geen wijzigingen aanbrengen. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [DevTest Labs-gebruiker](#devtest-labs-user) | Hiermee kunt u uw virtuele machines in uw omgeving verbinden, starten, opnieuw opstarten en afsluiten Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Labmaker](#lab-creator) | Hiermee kunt u nieuwe labs maken onder uw Azure Lab-accounts. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Controle** |  |  |
> | [Application Insights inzender voor onderdelen](#application-insights-component-contributor) | Kan de Application Insights beheren | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Geeft de gebruiker toestemming om momentopnamen voor foutopsporing weer te geven en te downloaden die zijn verzameld met de Application Insights Snapshot Debugger. Houd er rekening mee dat deze machtigingen niet zijn opgenomen in de [rollen Eigenaar](#owner) [of Inzender.](#contributor) Wanneer u gebruikers de Application Insights Snapshot Debugger geeft, moet u de rol rechtstreeks aan de gebruiker verlenen. De rol wordt niet herkend wanneer deze wordt toegevoegd aan een aangepaste rol. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Controlebijdrager](#monitoring-contributor) | Kan alle bewakingsgegevens lezen en bewakingsinstellingen bewerken. Zie ook [Aan de slag met rollen, machtigingen en beveiliging met Azure Monitor.](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles) | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Uitgever van metrische gegevens bewaken](#monitoring-metrics-publisher) | Hiermee kunt u metrische gegevens publiceren op Basis van Azure-resources | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Lezer voor bewaking](#monitoring-reader) | Kan alle bewakingsgegevens (metrische gegevens, logboeken, enzovoort) lezen. Zie ook [Aan de slag met rollen, machtigingen en beveiliging met Azure Monitor.](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles) | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Inzender voor werkmap](#workbook-contributor) | Kan gedeelde werkmappen opslaan. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Werkmaplezer](#workbook-reader) | Kan werkmappen lezen. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Beheer en governance** |  |  |
> | [Automation-taakoperator](#automation-job-operator) | Taken maken en beheren met Automation-runbooks. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation-operator](#automation-operator) | Automation-operators kunnen taken starten, stoppen, opschorten en hervatten | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automation-runbookoperator](#automation-runbook-operator) | Runbookeigenschappen lezen: om taken van het runbook te kunnen maken. | 5fb5aef8-1081-4b8e-bb16-9d5d03855 |
> | [Azure Arc kubernetes-clustergebruikersrol ingeschakeld](#azure-arc-enabled-kubernetes-cluster-user-role) | Lijst met gebruikersreferenties voor clusteractie. | 00493d72-78f6-4148-b6c5-d3ce8e4799dd |
> | [Azure Arc Kubernetes-beheerder](#azure-arc-kubernetes-admin) | Hiermee kunt u alle resources onder cluster/naamruimte beheren, behalve resourcequota en naamruimten bijwerken of verwijderen. | dffb1e0c-446f-4dde-a09f-99eb5cc68b96 |
> | [Azure Arc Kubernetes-clusterbeheerder](#azure-arc-kubernetes-cluster-admin) | Hiermee kunt u alle resources in het cluster beheren. | 8393591c-06b9-48a2-a542-1bd6b377f6a2 |
> | [Azure Arc Kubernetes-viewer](#azure-arc-kubernetes-viewer) | Hiermee kunt u alle resources in het cluster/de naamruimte weergeven, met uitzondering van geheimen. | 63f0a09d-1495-4db4-a681-037d84835eb4 |
> | [Azure Arc Kubernetes Writer](#azure-arc-kubernetes-writer) | Hiermee kunt u alles bijwerken in cluster/naamruimte, behalve (cluster)rollen en (cluster)rolbindingen. | 5b999177-9696-4545-85c7-50de3797e5a1 |
> | [Azure Connected Machine Onboarding](#azure-connected-machine-onboarding) | Kan onboarding uitvoeren voor Azure Connected Machines. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Azure Connected Machine resourcebeheerder](#azure-connected-machine-resource-administrator) | Kan Azure Connected Machines lezen, schrijven, verwijderen en opnieuw onboarden. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Lezer voor facturering](#billing-reader) | Hiermee staat u leestoegang tot factureringsgegevens toe | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Blauwdrukinzender](#blueprint-contributor) | Blauwdrukinzenders kunnen blauwdrukdefinities beheren, maar deze niet toewijzen. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Blauwdrukoperator](#blueprint-operator) | Kan bestaande gepubliceerde blauwdrukken toewijzen, maar kan geen nieuwe blauwdrukken maken. Houd er rekening mee dat dit alleen werkt als de toewijzing wordt uitgevoerd met een door de gebruiker toegewezen beheerde identiteit. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Cost Management-inzender](#cost-management-contributor) | Kan kosten bekijken en kostenconfiguratie beheren (bijvoorbeeld budgetten, exports) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Cost Management Lezer](#cost-management-reader) | Kan kostengegevens en -configuratie weergeven (bijvoorbeeld budgetten, exports) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Beheerder hiërarchie-instellingen](#hierarchy-settings-administrator) | Hiermee kunnen gebruikers hiërarchie-instellingen bewerken en verwijderen | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Kubernetes-cluster - Azure Arc onboarding](#kubernetes-cluster---azure-arc-onboarding) | Roldefinitie om elke gebruiker/service te autoreren om connectedClusters-resource te maken | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [Rol inzender voor beheerde toepassingen](#managed-application-contributor-role) | Hiermee kunt u beheerde toepassingsbronnen maken. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Operatorrol voor beheerde toepassingen](#managed-application-operator-role) | Hiermee kunt u acties lezen en uitvoeren op resources van beheerde toepassingen | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Lezer van beheerde toepassingen](#managed-applications-reader) | Hiermee kunt u resources in een beheerde app lezen en JIT-toegang aanvragen. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Rol registratietoewijzing beheerde services verwijderen](#managed-services-registration-assignment-delete-role) | Met de rol Registratietoewijzing beheerde services kunnen tenantgebruikers die beheren de registratietoewijzing verwijderen die is toegewezen aan hun tenant. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Inzender voor beheergroep](#management-group-contributor) | Rol inzender voor beheergroep | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Lezer van beheergroep](#management-group-reader) | Rol van lezer van beheergroep | ac63b705-f282-497d-ac71-919bf39d939d |
> | [New Relic APM-account inzender](#new-relic-apm-account-contributor) | Hiermee kunt u New Relic Application Performance Management accounts en toepassingen beheren, maar geen toegang tot deze accounts. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Policy Insights Data Writer (preview)](#policy-insights-data-writer-preview) | Hiermee staat u leestoegang toe tot resourcebeleid en schrijftoegang tot beleidsgebeurtenissen voor resourcecomponenten. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Operator voor quotumaanvraag](#quota-request-operator) | Lees en maak quotumaanvragen, haal de status van de quotumaanvraag op en maak ondersteuningstickets. | 0e5f05e5-9ab9-446b-b98d-1e2157c94125 |
> | [Reserveringsinkoper](#reservation-purchaser) | Hiermee kunt u reserveringen aanschaffen | f7b75c60-3036-4b75-91c3-6b41c27c1689 |
> | [Inzender voor resourcebeleid](#resource-policy-contributor) | Gebruikers met rechten voor het maken/wijzigen van resourcebeleid, het maken van een ondersteuningsticket en het lezen van resources/hiërarchie. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Site Recovery-inzender](#site-recovery-contributor) | Hiermee kunt u een Site Recovery beheren, behalve het maken van een kluis en het maken van een roltoewijzing | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Site Recovery-operator](#site-recovery-operator) | Hiermee kunt u failover en failback uitvoeren, maar geen andere Site Recovery uitvoeren | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery-lezer](#site-recovery-reader) | Hiermee kunt u de Site Recovery weergeven, maar geen andere beheerbewerkingen uitvoeren | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Inzender voor ondersteuningsaanvraag](#support-request-contributor) | Hiermee kunt u ondersteuningsaanvragen maken en beheren | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Tag-inzender](#tag-contributor) | Hiermee kunt u tags voor entiteiten beheren zonder toegang te verlenen tot de entiteiten zelf. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Overige** |  |  |
> | [Azure Digital Twins gegevenseigenaar](#azure-digital-twins-data-owner) | Volledige toegangsrol voor Digital Twins gegevensvlak | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
> | [Azure Digital Twins gegevenslezer](#azure-digital-twins-data-reader) | Alleen-lezenrol voor Digital Twins gegevensvlakeigenschappen | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |
> | [BizTalk Contributor](#biztalk-contributor) | Hiermee kunt u BizTalk Services beheren, maar geen toegang krijgen tot deze services. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Inzender voor bureaubladvirtualisatietoepassingsgroep](#desktop-virtualization-application-group-contributor) | Inzender van de bureaubladvirtualisatietoepassingsgroep. | 86240b0e-9422-4c43-887b-b61143f32ba8 |
> | [Lezer voor bureaubladvirtualisatietoepassingsgroep](#desktop-virtualization-application-group-reader) | Lezer van de bureaubladvirtualisatietoepassingsgroep. | aebf23d0-b568-4e86-b8f9-fe83a2c6ab55 |
> | [Inzender voor bureaubladvirtualisatie](#desktop-virtualization-contributor) | Inzender van desktopvirtualisatie. | 082f0a83-3be5-4ba1-904c-961s79b387 |
> | [Inzender voor bureaubladvirtualisatiehostgroep](#desktop-virtualization-host-pool-contributor) | Inzender van de bureaubladvirtualisatiehostgroep. | e307426c-f9b6-4e81-87de-d99efb3c32bc |
> | [Lezer van hostgroep bureaubladvirtualisatie](#desktop-virtualization-host-pool-reader) | Lezer van de bureaubladvirtualisatiehostgroep. | ceadfde2-b300-400a-ab7b-6143895aa822 |
> | [Lezer voor bureaubladvirtualisatie](#desktop-virtualization-reader) | Lezer van bureaubladvirtualisatie. | 49a72310-ab8d-41df-bbb0-79b649203868 |
> | [Hostoperator voor bureaubladvirtualisatiesessie](#desktop-virtualization-session-host-operator) | Operator van de Bureaubladvirtualisatiesessiehost. | 2ad6aaab-ead9-4eaa-8ac5-da422f562408 |
> | [DesktopVirtualisatiegebruiker](#desktop-virtualization-user) | Hiermee kan de gebruiker de toepassingen in een toepassingsgroep gebruiken. | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [Gebruikerssessieoperator voor bureaubladvirtualisatie](#desktop-virtualization-user-session-operator) | Operator van de Uesr-sessie voor bureaubladvirtualisatie. | ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6 |
> | [Inzender voor Bureaubladvirtualisatiewerkruimte](#desktop-virtualization-workspace-contributor) | Inzender van de bureaubladvirtualisatiewerkruimte. | 21efdde3-836f-432b-bf3d-3e8e734d4b2b |
> | [Werkruimtelezer voor bureaubladvirtualisatie](#desktop-virtualization-workspace-reader) | Lezer van de werkruimte Bureaubladvirtualisatie. | 0fa44ee9-7a7d-466b-9bb2-2bf446b1204d |
> | [Schijfback-uplezer](#disk-backup-reader) | Biedt machtigingen voor back-upkluis voor het uitvoeren van schijfback-ups. | 3e5e47e6-65f7-47ef-90b5-e5dd4d455f24 |
> | [Operator voor schijfherstel](#disk-restore-operator) | Biedt machtigingen voor het maken van een back-upkluis om schijfherstel uit te voeren. | b50d9833-a0cb-478e-945f-707fcc997c13 |
> | [Inzender voor schijfmomentopnamen](#disk-snapshot-contributor) | Biedt machtigingen voor back-upkluis voor het beheren van schijfmomentopnamen. | 7efff54f-a5b4-42b5-a1c5-5411624893ce |
> | [Inzender voor Scheduler-taakverzamelingen](#scheduler-job-collections-contributor) | Hiermee kunt u Scheduler-taakverzamelingen beheren, maar geen toegang tot deze verzamelingen. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [Services Hub Operator](#services-hub-operator) | Met Services Hub Operator kunt u alle lees-, schrijf- en verwijderingsbewerkingen uitvoeren die betrekking hebben op Services Hub-connectors. | 82200a5b-e217-47a5-b665-6d8765ee745b |


## <a name="general"></a>Algemeen


### <a name="contributor"></a>Inzender

Verleent volledige toegang voor het beheren van alle resources, maar biedt u niet de mogelijkheid om rollen toe te wijzen in Azure RBAC, toewijzingen te beheren in Azure Blueprints of galerieën met afbeeldingen te delen. [Meer informatie](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | * | Resources van alle typen maken en beheren |
> | **NotActions** |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Delete | Rollen, beleidstoewijzingen, beleidsdefinities en beleidssetdefinities verwijderen |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Write | Rollen, roltoewijzingen, beleidstoewijzingen, beleidsdefinities en beleidssetdefinities maken |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | Hiermee krijgt de Gebruikerstoegangbeheerder toegang op tenantniveau |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/write | Blauwdruktoewijzingen maken of bijwerken |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/delete | Blauwdruktoewijzingen verwijderen |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/galleries/share/action | Deelt een galerie met verschillende scopes |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, but does not allow you to assign roles in Azure RBAC, manage assignments in Azure Blueprints, or share image galleries.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete",
        "Microsoft.Compute/galleries/share/action"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>Eigenaar

Verleent volledige toegang voor het beheren van alle resources, inclusief de mogelijkheid om rollen toe te wijzen in Azure RBAC. [Meer informatie](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | * | Resources van alle typen maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, including the ability to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Lezer

Alles weergeven resources, maar u kunt geen wijzigingen aanbrengen. [Meer informatie](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */lezen | Lees resources van alle typen, met uitzondering van geheimen. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View all resources, but does not allow you to make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>Beheerder van gebruikerstoegang

Hiermee kunt u gebruikerstoegang tot Azure-resources beheren. [Meer informatie](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */lezen | Lees resources van alle typen, met uitzondering van geheimen. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/* | Autorisatie beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Compute


### <a name="classic-virtual-machine-contributor"></a>Inzender voor klassieke virtuele machines

Hiermee kunt u klassieke virtuele machines beheren, maar geen toegang tot deze machines, en niet tot het virtuele netwerk of opslagaccount waarmee ze zijn verbonden.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | Klassieke rekendomeinnamen maken en beheren |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | Virtuele machines maken en beheren |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/join/action |  |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/link/action | Een gereserveerd IP-adres koppelen |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/read | Haalt de gereserveerde IP-adressen op |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/join/action | Voegt het virtuele netwerk toe. |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/read | Haal het virtuele netwerk op. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/read | Retourneert de schijf van het opslagaccount. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/read | Retourneert de afbeelding van het opslagaccount. (Afgeschaft. Gebruik 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Hiermee worden de toegangssleutels voor de opslagaccounts vermeld. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | Het opslagaccount met het opgegeven account retourneren. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee worden de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik opgeslagen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Aanmelding bij de beheerder van de virtuele machine

Uw Virtual Machines weergeven in de portal en u aanmelden als [beheerder Meer informatie](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Haalt een openbare IP-adresdefinitie op. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | De definitie van het virtuele netwerk op halen |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Haalt een load balancer op |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Haalt een definitie van de netwerkinterface op.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | Aanmelden bij een virtuele machine als een gewone gebruiker |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/loginAsAdmin/action | Meld u aan bij een virtuele machine met gebruikersbevoegdheden voor Windows-beheerders of Linux-hoofdgebruikers |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Inzender voor virtuele machines

Hiermee kunt u virtuele machines beheren, maar geen toegang krijgen tot deze machines, en niet tot het virtuele netwerk of opslagaccount waarmee ze zijn verbonden. [Meer informatie](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | Beschikbaarheidssets voor rekenkracht maken en beheren |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/locations/* | Rekenlocaties maken en beheren |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | Voer alle acties voor virtuele machines uit, waaronder het maken, bijwerken, verwijderen, starten, opnieuw opstarten en uitschakelen van virtuele machines. Voer vooraf gedefinieerde scripts uit op virtuele machines. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | Schaalsets voor virtuele machines maken en beheren |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | Hiermee maakt u een nieuwe schijf of werkt u een bestaande schijf bij |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | De eigenschappen van een schijf op te halen |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/delete | Hiermee verwijdert u de schijf |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/schedules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/join/action | Voegt een back-endadresgroep van een toepassingsgateway toe. Kan niet worden gewaarschuwd. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | Voegt een back load balancer adresgroep toe. Kan niet worden gewaarschuwd. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/join/action | Voegt een load balancer inkomende NAT-pool toe. Kan niet worden gewaarschuwd. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | Voegt een load balancer inkomende nat-regel toe. Kan niet worden gewaarschuwd. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/probes/join/action | Hiermee staat u het gebruik van tests van een load balancer. Met deze machtigings-healthProbe-eigenschap van de VM-schaalset kan bijvoorbeeld naar de test worden verwezen. Kan niet worden gewaarschuwd. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Haalt een load balancer op |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/locations/* | Netwerklocaties maken en beheren |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | Netwerkinterfaces maken en beheren |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Wordt lid van een netwerkbeveiligingsgroep. Kan niet worden gewaarschuwd. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/read | Haalt de definitie van een netwerkbeveiligingsgroep op |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | Voegt een openbaar IP-adres toe. Kan niet worden gewaarschuwd. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Haalt een openbare IP-adresdefinitie op. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | De definitie van het virtuele netwerk op te halen |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnetten/join/action | Wordt lid van een virtueel netwerk. Kan niet worden gewaarschuwd. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | Een back-upbeveiligingsintentie maken |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retourneert objectdetails van het beveiligde item |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | Een beveiligd back-upitem maken |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Retourneert alle beveiligingsbeleidsregels |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/write | Beveiligingsbeleid maken |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Met de get vault-bewerking wordt een object op de azure-resource van het type 'kluis' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Retourneert gebruiksgegevens voor een Recovery Services-kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | Met een kluisbewerking maakt u een Azure-resource van het type 'kluis' |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Retourneert de toegangssleutels voor het opgegeven opslagaccount. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Retourneert de lijst met opslagaccounts of haalt de eigenschappen voor het opgegeven opslagaccount op. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Gebruikersmelding voor virtuele machine

Bekijk Virtual Machines in de portal en meld u aan als een gewone gebruiker. [Meer informatie](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Haalt een openbare IP-adresdefinitie op. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | De definitie van het virtuele netwerk op te halen |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Haalt een load balancer op |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Haalt een netwerkinterfacedefinitie op.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | Aanmelden bij een virtuele machine als een gewone gebruiker |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>Netwerken


### <a name="cdn-endpoint-contributor"></a>Inzender voor CDN-eindpunten

Kan CDN-eindpunten beheren, maar kan geen toegang verlenen aan andere gebruikers.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>CDN-eindpuntlezer

Kan CDN-eindpunten weergeven, maar kan geen wijzigingen aanbrengen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>Inzender voor CDN-profielen

Kan CDN-profielen en hun eindpunten beheren, maar kan geen toegang verlenen aan andere gebruikers. [Meer informatie](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>CDN-profiellezer

Kan CDN-profielen en hun eindpunten weergeven, maar kan geen wijzigingen aanbrengen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Inzender voor klassieke netwerken

Hiermee kunt u klassieke netwerken beheren, maar geen toegang tot deze netwerken. [Meer informatie](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | Klassieke netwerken maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee worden de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik opgeslagen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>Inzender voor DNS-zone

Hiermee kunt u DNS-zones en recordsets beheren in Azure DNS, maar kunt u niet beheren wie er toegang toe heeft. [Meer informatie](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | DNS-zones en -records maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Inzender voor netwerken

Hiermee kunt u netwerken beheren, maar geen toegang tot deze netwerken.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/* | Netwerken maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee worden de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik opgeslagen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="private-dns-zone-contributor"></a>Privé-DNS zone-inzender

Hiermee kunt u privé-DNS-zonebronnen beheren, maar niet de virtuele netwerken waar ze aan zijn gekoppeld. [Meer informatie](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | De definitie van het virtuele netwerk op halen |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/join/action | Voegt een virtueel netwerk toe. Kan niet worden gewaarschuwd. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage private DNS zone resources, but not the virtual networks they are linked to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "name": "b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/privateDnsZones/*",
        "Microsoft.Network/privateDnsOperationResults/*",
        "Microsoft.Network/privateDnsOperationStatuses/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/join/action",
        "Microsoft.Authorization/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Private DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Traffic Manager-inzender

Hiermee kunt u Traffic Manager beheren, maar kunt u niet bepalen wie er toegang toe heeft.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee worden de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik opgeslagen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>Storage


### <a name="avere-contributor"></a>Avere-inzender

Kan een cluster met Avere vFXT maken en beheren. [Meer informatie](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/*/read |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/*/read |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | De definitie van het virtuele netwerk op halen |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnetten/read | Haalt een subnetdefinitie van een virtueel netwerk op |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Voegt een virtueel netwerk toe. Kan niet worden gewaarschuwd. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Voegt resource, zoals opslagaccount of SQL database aan een subnet. Kan niet worden gewaarschuwd. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Wordt lid van een netwerkbeveiligingsgroep. Kan niet worden gewaarschuwd. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/*/read |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Opslagaccounts maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | Haalt de resources voor de resourcegroep op. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Retourneert het resultaat van het verwijderen van een blob |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Retourneert een blob of een lijst met blobs |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Retourneert het resultaat van het schrijven van een blob |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/proximityPlacementGroups/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Avere Operator

Gebruikt door het Avere vFXT cluster voor het beheren van het cluster [Meer informatie](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | De eigenschappen van een virtuele machine op te halen |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Haalt een definitie van de netwerkinterface op.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Hiermee maakt u een netwerkinterface of werkt u een bestaande netwerkinterface bij.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | De definitie van het virtuele netwerk op halen |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnetten/read | Haalt een subnetdefinitie van een virtueel netwerk op |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Voegt een virtueel netwerk toe. Kan niet worden gewaarschuwd. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Wordt lid van een netwerkbeveiligingsgroep. Kan niet worden gewaarschuwd. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | Retourneert het resultaat van het verwijderen van een container |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Retourneert een lijst met containers |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | Retourneert het resultaat van de put-blobcontainer |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Retourneert het resultaat van het verwijderen van een blob |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Retourneert een blob of een lijst met blobs |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Retourneert het resultaat van het schrijven van een blob |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Back-upbijdrager

Hiermee kunt u de back-upservice beheren, maar u kunt geen kluizen maken en toegang verlenen aan anderen [Meer informatie](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | De definitie van het virtuele netwerk op halen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | Bewerkingsresultaten beheren voor back-upbeheer |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | Back-upcontainers maken en beheren in back-up-fabrics van Recovery Services-kluis |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | Vernieuwt de lijst met containers |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Back-uptaken maken en beheren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Taken exporteren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Resultaten van back-upbeheerbewerkingen maken en beheren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | Back-upbeleid maken en beheren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Items maken en beheren waarvan u een back-up kunt maken |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | Back-upitems maken en beheren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | Containers met back-upitems maken en beheren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Retourneert samenvattingen voor beveiligde items en beveiligde servers voor een Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/* | Certificaten maken en beheren met betrekking tot back-ups in de Recovery Services-kluis |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Uitgebreide informatie met betrekking tot de kluis maken en beheren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Haalt de waarschuwingen voor de Recovery Services-kluis op. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Met de get vault-bewerking wordt een object op de azure-resource van het type 'kluis' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Geregistreerde identiteiten maken en beheren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/* | Gebruik van Recovery Services-kluis maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Retourneert de lijst met opslagaccounts of haalt de eigenschappen voor het opgegeven opslagaccount op. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | Bewerking valideren voor beveiligd item |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | Met een kluisbewerking maakt u een Azure-resource van het type 'kluis' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Retourneert back-upbewerkingsstatus voor Recovery Services-kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Retourneert alle back-upbeheerservers die zijn geregistreerd bij de kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | Alle beveiligende containers krijgen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Controleer de back-upstatus voor Recovery Services-kluizen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Functies valideren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | De waarschuwing wordt opgelost. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Bewerking retourneert de lijst met bewerkingen voor een resourceprovider |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Haalt de bewerkingsstatus op voor een bepaalde bewerking |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Alle back-upbeveiligingsintentensen |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Back-upoperator

Hiermee kunt u back-upservices beheren, behalve het verwijderen van back-ups, het maken van een kluis en het verlenen van toegang aan [anderen Meer informatie](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | De definitie van het virtuele netwerk op te halen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | Retourneert de status van de bewerking |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | Haalt het resultaat op van de bewerking die is uitgevoerd op de beveiligingscontainer. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Voert een back-up uit voor beveiligd item. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Haalt het resultaat op van de bewerking uitgevoerd op beveiligde items. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Retourneert de status van bewerking uitgevoerd op beveiligde items. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retourneert objectdetails van het beveiligde item |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Direct itemherstel inrichten voor beveiligd item |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action | AccessToken voor herstellen tussen regio's. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Herstelpunten voor beveiligde items op halen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Herstelpunten herstellen voor beveiligde items. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Direct herstel van items intrekken voor beveiligd item |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | Een beveiligd back-upitem maken |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | Retourneert alle geregistreerde containers |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | Vernieuwt de lijst met containers |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Back-uptaken maken en beheren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Taken exporteren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Resultaten van back-upbeheerbewerkingen maken en beheren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | Resultaten van beleidsbewerkingen op te halen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Retourneert alle beveiligingsbeleidsregels |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Items maken en beheren waarvan een back-up kan worden maken |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | Retourneert de lijst met alle beveiligde items. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | Retourneert alle containers die tot het abonnement behoren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Retourneert samenvattingen voor beveiligde items en beveiligde servers voor een Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | Met de bewerking Resourcecertificaat bijwerken wordt het referentiecertificaat van de resource/kluis bijgewerkt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | Met de bewerking Uitgebreide informatie op halen haalt u de uitgebreide informatie van een object op die de Azure-resource van het type ?vault vertegenwoordigt? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/write | Met de bewerking Uitgebreide informatie op halen haalt u de uitgebreide informatie van een object op die de Azure-resource van het type ?vault vertegenwoordigt? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Haalt de waarschuwingen voor de Recovery Services-kluis op. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Met de get vault-bewerking wordt een object op de azure-resource van het type 'kluis' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | De bewerking Bewerkingsresultaten op halen kan worden gebruikt om de bewerkingsstatus en het resultaat voor de asynchroon verzonden bewerking op te halen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | De bewerking Containers op halen kan worden gebruikt om de containers op te halen die zijn geregistreerd voor een resource. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/write | De bewerking Servicecontainer registreren kan worden gebruikt om een container te registreren bij Recovery Service. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Retourneert gebruiksgegevens voor een Recovery Services-kluis. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Retourneert de lijst met opslagaccounts of haalt de eigenschappen voor het opgegeven opslagaccount op. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | Bewerking valideren voor beveiligd item |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Retourneert back-upbewerkingsstatus voor Recovery Services-kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | De status van de beleidsbewerking op te halen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/write | Hiermee maakt u een geregistreerde container |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/pc/action | Een aanvraag uitvoeren voor workloads binnen een container |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Retourneert alle back-upbeheerservers die zijn geregistreerd bij de kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | Een back-upbeveiligingsintentie maken |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | Een back-upbeveiligingsintentie krijgen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | Alle beveiligende containers krijgen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | Alle items in een container op halen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Controleer de back-upstatus voor Recovery Services-kluizen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Functies valideren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupAadProperties/read | Haal AAD-eigenschappen op voor verificatie in de derde regio voor herstel in verschillende regio's. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJobs/action | Lijst met hersteltaken voor regio-overschrijdende regio's in de secundaire regio voor Recovery Services-kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJob/action | Haal details van herstel naar andere regio's op in de secundaire regio voor Recovery Services-kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrossRegionRestore/action | Herstel naar andere regio's activeren. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationResults/read | Retourneert het CRR-bewerkingsresultaat voor Recovery Services-kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationsStatus/read | Retourneert de CRR-bewerkingsstatus voor Recovery Services-kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | De waarschuwing wordt opgelost. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Bewerking retourneert de lijst met bewerkingen voor een resourceprovider |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Haalt de bewerkingsstatus op voor een bepaalde bewerking |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Alle back-upbeveiligingsintentensen |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/locations/backupAadProperties/read",
        "Microsoft.RecoveryServices/locations/backupCrrJobs/action",
        "Microsoft.RecoveryServices/locations/backupCrrJob/action",
        "Microsoft.RecoveryServices/locations/backupCrossRegionRestore/action",
        "Microsoft.RecoveryServices/locations/backupCrrOperationResults/read",
        "Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Back-uplezer

Kan back-upservices bekijken, maar kan geen wijzigingen aanbrengen [Meer informatie](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | Retourneert de status van de bewerking |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | Haalt het resultaat op van de bewerking die is uitgevoerd op de beveiligingscontainer. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Haalt het resultaat op van de bewerking uitgevoerd op beveiligde items. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Retourneert de status van bewerking uitgevoerd op beveiligde items. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retourneert objectdetails van het beveiligde item |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Herstelpunten voor beveiligde items op halen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | Retourneert alle geregistreerde containers |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/read | Retourneert het resultaat van de taakbewerking. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/read | Retourneert alle taakobjecten |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Taken exporteren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/read | Retourneert resultaat van back-upbewerking voor Recovery Services-kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | Resultaten van beleidsbewerkingen op te halen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Retourneert alle beveiligingsbeleidsregels |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | Retourneert de lijst met alle beveiligde items. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | Retourneert alle containers die tot het abonnement behoren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Retourneert samenvattingen voor beveiligde items en beveiligde servers voor een Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | Met de bewerking Uitgebreide informatie op halen haalt u de uitgebreide informatie van een object op die de Azure-resource van het type ?vault vertegenwoordigt? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Haalt de waarschuwingen voor de Recovery Services-kluis op. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Met de get vault-bewerking wordt een object op de azure-resource van het type 'kluis' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | De bewerking Bewerkingsresultaten op halen kan worden gebruikt om de bewerkingsstatus en het resultaat voor de asynchroon verzonden bewerking op te halen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | De bewerking Containers op halen kan worden gebruikt om de containers op te halen die zijn geregistreerd voor een resource. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/read | Retourneert opslagconfiguratie voor Recovery Services-kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/read | Retourneert configuratie voor Recovery Services-kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Retourneert back-upbewerkingsstatus voor Recovery Services-kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | De status van de beleidsbewerking op te halen. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Retourneert alle back-upbeheerservers die zijn geregistreerd bij de kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | Een back-upbeveiligingsintentie krijgen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | Alle items in een container op halen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Controleer de back-upstatus voor Recovery Services-kluizen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | De waarschuwing wordt opgelost. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Bewerking retourneert de lijst met bewerkingen voor een resourceprovider |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Haalt de bewerkingsstatus op voor een bepaalde bewerking |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Alle back-upbeveiligingsintentensen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Retourneert gebruiksgegevens voor een Recovery Services-kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Functies valideren |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Inzender voor klassieke opslagaccounts

Hiermee kunt u klassieke opslagaccounts beheren, maar geen toegang tot deze accounts.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | Opslagaccounts maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>Klassieke servicerol Sleuteloperator voor opslagaccounts

Sleuteloperators voor klassieke opslagaccounts mogen sleutels in klassieke opslagaccounts in een lijst met en opnieuw worden [weergegeven. Meer informatie](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/action | Hiermee worden de toegangssleutels voor de opslagaccounts vermeld. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/action | De bestaande toegangssleutels voor het opslagaccount worden opnieuw ge regenereert. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Data Box-inzender

Hiermee kunt u alles beheren onder Data Box Service, behalve toegang te verlenen aan anderen. [Meer informatie](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee worden de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik opgeslagen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Data Box Lezer

Hiermee kunt u Data Box service beheren, behalve het maken van order- of bewerkingsgegevens en het verlenen van toegang aan anderen. [Meer informatie](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/*/read |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listsecrets/action |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listcredentials/action | Hiermee worden de niet-versleutelde referenties vermeld die betrekking hebben op de bestelling. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/availableSkus/action | Deze methode retourneert de lijst met beschikbare SKU's. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateInputs/action | Met deze methode worden alle typen validaties uitgevoerd. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/regionConfiguration/action | Deze methode retourneert de configuraties voor de regio. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateAddress/action | Valideert het verzendadres en biedt alternatieve adressen, indien van u. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics Developer

Hiermee kunt u uw eigen taken verzenden, bewaken en beheren, maar geen accounts maken Data Lake Analytics verwijderen. [Meer informatie](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.BigAnalytics/accounts/* |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee worden de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik opgeslagen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | Een DataLakeAnalytics-account verwijderen. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/action | Verleen machtigingen voor het annuleren van taken die zijn ingediend door andere gebruikers. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | Een DataLakeAnalytics-account maken of bijwerken. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | Maak of werk een gekoppeld DataLakeStore-account van een DataLakeAnalytics-account bij. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | Een DataLakeStore-account loskoppelen van een DataLakeAnalytics-account. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Write | Maak of werk een gekoppeld opslagaccount van een DataLakeAnalytics-account bij. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Delete | Een opslagaccount loskoppelen van een DataLakeAnalytics-account. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Write | Een firewallregel maken of bijwerken. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Delete | Verwijder een firewallregel. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | Een rekenbeleid maken of bijwerken. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | Een rekenbeleid verwijderen. |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Lezer en gegevenstoegang

Hiermee kunt u alles weergeven, maar u kunt geen opslagaccount of ingesloten resource verwijderen of maken. Het biedt ook lees-/schrijftoegang tot alle gegevens in een opslagaccount via toegang tot opslagaccountsleutels.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Retourneert de toegangssleutels voor het opgegeven opslagaccount. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListAccountSas/action | Retourneert het SAS-token van het account voor het opgegeven opslagaccount. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Retourneert de lijst met opslagaccounts of haalt de eigenschappen voor het opgegeven opslagaccount op. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Inzender voor opslagaccounts

Staat beheer van opslagaccounts toe. Biedt toegang tot de accountsleutel, die kan worden gebruikt voor toegang tot gegevens via gedeelde sleutelautorisatie. [Meer informatie](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Hiermee maakt, werkt u de diagnostische instelling voor de Analyseserver |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Voegt resource, zoals opslagaccount of SQL database aan een subnet. Kan niet worden gewaarschuwd. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Opslagaccounts maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>Servicerol Sleuteloperator voor opslagaccount

Maakt het mogelijk om toegangssleutels voor opslagaccounts weer te geven en opnieuw te gebruiken. [Meer informatie](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | Retourneert de toegangssleutels voor het opgegeven opslagaccount. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/regeneratekey/action | Hiermee worden de toegangssleutels voor het opgegeven opslagaccount opnieuw ge regenereerd. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Inzender voor Storage Blob-gegevens

Lees, schrijf en verwijder Azure Storage containers en blobs. Zie Machtigingen voor het aanroepen van blob- en wachtrijgegevensbewerkingen voor meer informatie over welke acties vereist zijn voor een [bepaalde gegevensbewerking.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) [Meer informatie](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | Een container verwijderen. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Een container of een lijst met containers retourneren. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | Wijzig de metagegevens of eigenschappen van een container. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Retourneert een sleutel voor gebruikersdelegatie voor Blob service. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Een blob verwijderen. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Een blob of een lijst met blobs retourneren. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Schrijven naar een blob. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/move/action | Verplaatst de blob van het ene pad naar het andere |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/add/action | Retourneert het resultaat van het toevoegen van blob-inhoud |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Eigenaar van opslagblobgegevens

Biedt volledige toegang tot Azure Storage blobcontainers en -gegevens, waaronder het toewijzen van POSIX-toegangsbeheer. Zie Machtigingen voor het aanroepen van blob- en wachtrijgegevensbewerkingen voor meer informatie over welke acties vereist zijn voor een [bepaalde gegevensbewerking.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) [Meer informatie](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/* | Volledige machtigingen voor containers. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Retourneert een sleutel voor gebruikersdelegatie voor de Blob service. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/* | Volledige machtigingen voor blobs. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Lezer voor opslagblobgegevens

Lees en vermeld Azure Storage containers en blobs. Zie Machtigingen voor het aanroepen van blob- en wachtrijgegevensbewerkingen voor meer informatie over welke acties vereist zijn voor een [bepaalde gegevensbewerking.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) [Meer informatie](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Een container of een lijst met containers retourneren. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Retourneert een sleutel voor gebruikersdelegatie voor Blob service. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Een blob of een lijst met blobs retourneren. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Storage Blob Delegator

Haal een sleutel voor gebruikersdelegatie op, die vervolgens kan worden gebruikt om een Shared Access Signature te maken voor een container of blob die is ondertekend met Azure AD-referenties. Zie Create [a user delegation SAS (Een SAS voor gebruikersdelegatie maken) voor meer informatie.](/rest/api/storageservices/create-user-delegation-sas) [Meer informatie](/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Retourneert een sleutel voor gebruikersdelegatie voor Blob service. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Inzender voor opslagbestandsgegevens via SMB-share

Hiermee kunt u lezen, schrijven en verwijderen van toegang tot bestanden/mappen in Azure-bestands shares. Deze rol heeft geen ingebouwd equivalent op Windows-bestandsservers. [Meer informatie](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Retourneert een bestand/map of een lijst met bestanden/mappen. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | Retourneert het resultaat van het schrijven van een bestand of het maken van een map. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | Retourneert het resultaat van het verwijderen van een bestand/map. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Inzender met verhoogde bevoegdheden voor opslagbestandsgegevens via SMB-share

Hiermee kunnen ACL's voor bestanden/mappen in Azure-bestands shares worden gelezen, geschreven, verwijderd en gewijzigd. Deze rol is gelijk aan een wijzigings-ACL voor bestands share op Windows-bestandsservers. [Meer informatie](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Retourneert een bestand/map of een lijst met bestanden/mappen. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | Retourneert het resultaat van het schrijven van een bestand of het maken van een map. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | Retourneert het resultaat van het verwijderen van een bestand/map. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Retourneert het resultaat van het wijzigen van de machtiging voor een bestand/map. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Lezer voor opslagbestandgegevens via SMB-share

Hiermee hebt u leestoegang tot bestanden/mappen in Azure-bestands shares. Deze rol is gelijk aan een bestands share-ACL van lezen op Windows-bestandsservers. [Meer informatie](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Retourneert een bestand/map of een lijst met bestanden/mappen. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Inzender voor opslagwachtrijgegevens

Lees, schrijf en verwijder Azure Storage wachtrijen en wachtrijberichten. Zie Machtigingen voor het aanroepen van blob- en wachtrijgegevensbewerkingen voor meer informatie over welke acties vereist zijn voor een [bepaalde gegevensbewerking.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) [Meer informatie](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/delete | Een wachtrij verwijderen. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | Een wachtrij of een lijst met wachtrijen retourneren. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/write | Metagegevens of eigenschappen van wachtrij wijzigen. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/delete | Verwijder een of meer berichten uit een wachtrij. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Bekijk of haal een of meer berichten uit een wachtrij op. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/write | Voeg een bericht toe aan een wachtrij. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | Retourneert het resultaat van het verwerken van een bericht |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Berichtenprocessor voor opslagwachtrijgegevens

Een bericht bekijken, ophalen en verwijderen uit een Azure Storage wachtrij. Zie Machtigingen voor het aanroepen van blob- en wachtrijgegevensbewerkingen voor meer informatie over welke acties vereist zijn voor een [bepaalde gegevensbewerking.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) [Meer informatie](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Bekijk een bericht. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | Een bericht ophalen en verwijderen. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Afzender van opslagwachtrijgegevensbericht

Berichten toevoegen aan een Azure Storage wachtrij. Zie Machtigingen voor het aanroepen van blob- en wachtrijgegevensbewerkingen voor meer informatie over welke acties vereist zijn voor een [bepaalde gegevensbewerking.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) [Meer informatie](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/add/action | Voeg een bericht toe aan een wachtrij. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Lezer opslagwachtrijgegevens

Lees en vermeld Azure Storage wachtrijen en wachtrijberichten. Zie Machtigingen voor het aanroepen van blob- en wachtrijgegevensbewerkingen voor meer informatie over welke acties vereist zijn voor een [bepaalde gegevensbewerking.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) [Meer informatie](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | Retourneert een wachtrij of een lijst met wachtrijen. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Bekijk of haal een of meer berichten uit een wachtrij op. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Web


### <a name="azure-maps-data-contributor"></a>Azure Maps inzender voor gegevens

Verleent toegang tot lezen, schrijven en verwijderen van toegang tot gerelateerde gegevens uit een Azure Maps-account. [Meer informatie](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/write |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/delete |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read, write, and delete access to map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "name": "8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read",
        "Microsoft.Maps/accounts/*/write",
        "Microsoft.Maps/accounts/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-maps-data-reader"></a>Azure Maps gegevenslezer

Verleent toegang tot het lezen van kaartgerelateerde gegevens uit een Azure Maps-account. [Meer informatie](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-spring-cloud-data-reader"></a>Azure Spring Cloud gegevenslezer

Leestoegang tot gegevens Azure Spring Cloud meer [informatie toestaan](../spring-cloud/how-to-access-data-plane-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/*/read |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allow read access to Azure Spring Cloud Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b5537268-8956-4941-a8f0-646150406f0c",
  "name": "b5537268-8956-4941-a8f0-646150406f0c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppPlatform/Spring/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Spring Cloud Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Inzender voor zoekservice

Hiermee kunt u Zoekservices beheren, maar geen toegang krijgen tot deze services. [Meer informatie](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Search](resource-provider-operations.md#microsoftsearch)/searchServices/* | Zoekservices maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-accesskey-reader"></a>SignalR AccessKey Reader

Toegangssleutels SignalR Service lezen

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/*/read |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/listkeys/action | De waarde van SignalR-toegangssleutels weergeven in de beheerportal of via de API |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read SignalR Service Access Keys",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/04165923-9d83-45d5-8227-78b77b0a687e",
  "name": "04165923-9d83-45d5-8227-78b77b0a687e",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*/read",
        "Microsoft.SignalRService/SignalR/listkeys/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR AccessKey Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-app-server-preview"></a>SignalR App Server (preview)

Hiermee kan uw app-server toegang SignalR Service met AAD-auth-opties.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/action | Genereer een tijdelijke AccessKey voor het ondertekenen van ClientTokens. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/serverConnection/write | Start een serververbinding. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app server access SignalR Service with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/420fcaa2-552c-430f-98ca-3264be4806c7",
  "name": "420fcaa2-552c-430f-98ca-3264be4806c7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/serverConnection/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR App Server (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-contributor"></a>SignalR-inzender

SignalR-serviceresources maken, lezen, bijwerken en verwijderen

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete SignalR service resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "name": "8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-serverless-contributor-preview"></a>Serverloze inzender van SignalR (preview)

Hiermee kan uw app toegang krijgen tot de service in de serverloze modus met AAD-auth-opties.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/action | Genereer een ClientToken voor het starten van een clientverbinding. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app access service in serverless mode with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd53cd77-2268-407a-8f46-7e7863d0f521",
  "name": "fd53cd77-2268-407a-8f46-7e7863d0f521",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/clientToken/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Serverless Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-owner-preview"></a>SignalR Service eigenaar (preview)

Volledige toegang tot Azure SignalR Service REST API's

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/action | Genereer een tijdelijke AccessKey voor het ondertekenen van ClientTokens. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/action | Genereer een ClientToken voor het starten van een clientverbinding. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/hub/send/action | Berichten uitzenden naar alle clientverbindingen in de hub. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/send/action | Broadcastbericht naar groep. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | Controleer het bestaan van de groep of het bestaan van de gebruiker in de groep. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/write | Lid worden/groep verlaten. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/send/action | Berichten rechtstreeks naar een clientverbinding verzenden. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | Controleer het bestaan van de clientverbinding. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/write | Sluit de clientverbinding. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/send/action | Berichten verzenden naar de gebruiker, die uit meerdere clientverbindingen kan bestaan. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | Controleer het bestaan van de gebruiker. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/write | Een gebruiker wijzigen. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "name": "7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/auth/clientToken/action",
        "Microsoft.SignalRService/SignalR/hub/send/action",
        "Microsoft.SignalRService/SignalR/group/send/action",
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/group/write",
        "Microsoft.SignalRService/SignalR/clientConnection/send/action",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/clientConnection/write",
        "Microsoft.SignalRService/SignalR/user/send/action",
        "Microsoft.SignalRService/SignalR/user/read",
        "Microsoft.SignalRService/SignalR/user/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Owner (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-reader-preview"></a>SignalR Service Reader (preview)

Alleen-lezentoegang tot Azure SignalR Service REST API's

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | Controleer het bestaan van de groep of het bestaan van de gebruiker in de groep. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | Controleer het bestaan van de clientverbinding. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | Controleer het bestaan van de gebruiker. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ddde6b66-c0df-4114-a159-3618637b3035",
  "name": "ddde6b66-c0df-4114-a159-3618637b3035",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/user/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Inzender voor webplannen

Hiermee kunt u de webplannen voor websites beheren, maar geen toegang tot de websites.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee worden de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik opgeslagen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | Server farms maken en beheren |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/hostingEnvironments/Join/Action | Een App Service Environment |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Inzender voor websites

Hiermee kunt u websites (geen webplannen) beheren, maar geen toegang tot deze websites.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Insights-onderdelen maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/certificates/* | Websitecertificaten maken en beheren |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/read | Haal namen op van sites die zijn toegewezen aan hostnaam. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | Een abonnement App Service maken |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | De eigenschappen van een App Service krijgen |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/* | Websites maken en beheren (voor het maken van een site zijn ook schrijfmachtigingen vereist voor het gekoppelde App Service plan) |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Containers


### <a name="acrdelete"></a>AcrDelete

acr delete [Meer informatie](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/artifacts/delete | Artefact in een containerregister verwijderen. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

acr image signer [Meer informatie](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/sign/write | Metagegevens van inhoud vertrouwensrelatie pushen/pullen voor een containerregister. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

acr pull [Meer informatie](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | Haal afbeeldingen op uit een containerregister of haal ze op. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

acr push [Meer informatie](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | Haal afbeeldingen op uit een containerregister of haal ze op. |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/push/write | Push- of schrijf-afbeeldingen naar een containerregister. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

acr quarantine data reader

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | Pull- of Get quarantined images from container registry (In quarantaine geplaatste afbeeldingen uit containerregister halen) |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

acr quarantine data writer

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | Pull- of Get quarantined images from container registry (In quarantaine geplaatste afbeeldingen uit het containerregister halen) |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/write | Quarantainetoestand van in quarantaine geplaatste afbeeldingen schrijven/wijzigen |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes Service rol clusterbeheerder

De actie Clusterbeheerderreferenties opsommen. [Meer informatie](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/action | De clusterreferenties voor het beheer van een beheerd cluster opnommen |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/action | Een toegangsprofiel voor een beheerd cluster op basis van rolnaam op basis van lijstreferenties |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Een beheerd cluster krijgen |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes Service clustergebruikersrol

Lijst met gebruikersreferenties voor cluster. [Meer informatie](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | De clusterreferenties van een beheerd cluster opsommen |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Een beheerd cluster krijgen |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-contributor-role"></a>Azure Kubernetes Service inzendersrol

Verleent toegang tot lees- en Azure Kubernetes Service clusters [Meer informatie](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Een beheerd cluster krijgen |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/write | Hiermee maakt u een nieuw beheerd cluster of werkt u een bestaand cluster bij |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read and write Azure Kubernetes Service clusters",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "name": "ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/read",
        "Microsoft.ContainerService/managedClusters/write",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-admin"></a>Azure Kubernetes Service RBAC-beheerder

Hiermee kunt u alle resources onder cluster/naamruimte beheren, behalve resourcequota en naamruimten bijwerken of verwijderen. [Meer informatie](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Hiermee maakt of werkt u een implementatie bij. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Haal de resultaten van de abonnementsbewerking op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Haalt de lijst met abonnementen op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | De clusterreferenties van de gebruiker van een beheerd cluster opsommen |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/write | Schrijft resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/delete | Resourcequotas wordt verwijderd |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/write | Schrijft naamruimten |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/delete | Naamruimten verwijderen |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3498e952-d568-435e-9b2c-8d77e338d7f7",
  "name": "3498e952-d568-435e-9b2c-8d77e338d7f7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/delete",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/namespaces/delete"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>Azure Kubernetes Service RBAC-clusterbeheerder

Hiermee kunt u alle resources in het cluster beheren. [Meer informatie](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Hiermee maakt of werkt u een implementatie bij. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Haal de resultaten van de abonnementsbewerking op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Haalt de lijst met abonnementen op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | De clusterreferenties van de gebruiker van een beheerd cluster opsommen |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "name": "b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-reader"></a>Azure Kubernetes Service RBAC-lezer

Hiermee staat u alleen-lezentoegang toe om de meeste objecten in een naamruimte te zien. Het weergeven van rollen of rolbindingen is niet toegestaan. Deze rol staat het weergeven van geheimen niet toe, omdat het lezen van de inhoud van Geheimen toegang biedt tot ServiceAccount-referenties in de naamruimte, waardoor API-toegang mogelijk is als elk ServiceAccount in de naamruimte (een vorm van escalatie van bevoegdheden). Door deze rol toe te passen op clusterbereik, krijgt u toegang tot alle naamruimten. [Meer informatie](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Hiermee maakt of werkt u een implementatie bij. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Haal de resultaten van de abonnementsbewerking op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Haalt de lijst met abonnementen op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | Controllerrevisions lezen |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/read | Leest daemonsets |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/read | Leest implementaties |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/read | Replicasets lezen |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/read | Leest statefulsets |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/read | Leest horizontalpodautoscalers |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/read | Cronjobs lezen |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/read | Taken lezen |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/read | Leest configuratiekaarten |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/read | Leest eindpunten |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | Gebeurtenissen lezen |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | Gebeurtenissen lezen |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/read | Daemonsets lezen |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/read | Leest implementaties |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/read | Leest ingresses |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/read | Leest netwerkbeleid |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/read | Replicasets lezen |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | Leest limieten |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | Naamruimten lezen |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/read | Leest ingresses |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/read | Leest netwerkbeleid |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/read | Leest persistentvolumeclaims |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/read | Pods lezen |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/read | Leest poddisruptionbudgets |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | Leest replicatiecontrollers |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | Leest replicatiecontrollers |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | Leest resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/read | Leest serviceaccounts |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/read | Leest services |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read-only access to see most objects in a namespace. It does not allow viewing roles or role bindings. This role does not allow viewing Secrets, since reading the contents of Secrets enables access to ServiceAccount credentials in the namespace, which would allow API access as any ServiceAccount in the namespace (a form of privilege escalation). Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "name": "7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/apps/deployments/read",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/read",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/read",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/read",
        "Microsoft.ContainerService/managedClusters/batch/jobs/read",
        "Microsoft.ContainerService/managedClusters/configmaps/read",
        "Microsoft.ContainerService/managedClusters/endpoints/read",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/read",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/read",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/read",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/read",
        "Microsoft.ContainerService/managedClusters/pods/read",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/read",
        "Microsoft.ContainerService/managedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>Azure Kubernetes Service RBAC Writer

Hiermee staat u lees-/schrijftoegang toe tot de meeste objecten in een naamruimte. Deze rol staat het weergeven of wijzigen van rollen of rolbindingen niet toe. Met deze rol kunt u echter toegang krijgen tot Geheimen en pods uitvoeren als een serviceaccount in de naamruimte, zodat deze kan worden gebruikt om de API-toegangsniveaus van elk ServiceAccount in de naamruimte te verkrijgen. Door deze rol toe te passen op clusterbereik, krijgt u toegang tot alle naamruimten. [Meer informatie](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Hiermee maakt of werkt u een implementatie bij. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Haal de resultaten van de abonnementsbewerking op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Haalt de lijst met abonnementen op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | Controllerrevisions lezen |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | Leest gebeurtenissen |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | Leest gebeurtenissen |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | Leeslimieten |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | Naamruimten lezen |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | Leest resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/secrets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read/write access to most objects in a namespace.This role does not allow viewing or modifying roles or role bindings. However, this role allows accessing Secrets and running Pods as any ServiceAccount in the namespace, so it can be used to gain the API access levels of any ServiceAccount in the namespace. Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "name": "a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/apps/deployments/*",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/*",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/*",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/*",
        "Microsoft.ContainerService/managedClusters/batch/jobs/*",
        "Microsoft.ContainerService/managedClusters/configmaps/*",
        "Microsoft.ContainerService/managedClusters/endpoints/*",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/*",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/*",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/*",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/*",
        "Microsoft.ContainerService/managedClusters/pods/*",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/secrets/*",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/*",
        "Microsoft.ContainerService/managedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Databases


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB rol accountlezer

Kan de Azure Cosmos DB lezen. Zie [Inzender voor DocumentDB-accounts](#documentdb-account-contributor) voor het beheren Azure Cosmos DB accounts. [Meer informatie](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/read | Een verzameling lezen |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/action | Leest het databaseaccount alleen-lezen sleutels. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/read | Metrische definities lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/read | Metrische gegevens lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Cosmos DB Operator

Hiermee kunt u Azure Cosmos DB beheren, maar geen toegang krijgen tot gegevens in deze accounts. Hiermee voorkomt u toegang tot accountsleutels en verbindingsreeksen. [Meer informatie](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee worden de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik opgeslagen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Voegt resource, zoals opslagaccount of SQL database, toe aan een subnet. Kan niet worden gewaarschuwd. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/write | Een SQL-roldefinitie maken of bijwerken |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/delete | Een SQL-roldefinitie verwijderen |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/write | Een SQL-roltoewijzing maken of bijwerken |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/delete | Een SQL-roltoewijzing verwijderen |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/delete",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Kan een herstelaanvraag indienen voor een Cosmos DB database of een container voor een account [Meer informatie](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/backup/action | Een aanvraag indienen om back-up te configureren |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/restore/action | Een herstelaanvraag indienen |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosrestoreoperator"></a>CosmosRestoreOperator

Kan herstelactie uitvoeren voor Cosmos DB databaseaccount met continue back-upmodus

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/restore/action | Een herstelaanvraag indienen |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/*/read |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/read | Een restorable databaseaccount lezen of alle restorable databaseaccounts op een lijst zetten |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can perform restore action for Cosmos DB database account with continuous backup mode",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "name": "5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosRestoreOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>Inzender voor DocumentDB-account

Kan uw Azure Cosmos DB beheren. Azure Cosmos DB staat voorheen bekend als DocumentDB. [Meer informatie](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | Accounts voor Azure Cosmos DB maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Voegt resource, zoals opslagaccount of SQL database aan een subnet. Kan niet worden gewaarschuwd. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Redis Cache-inzender

Hiermee kunt u Redis-caches beheren, maar geen toegang tot deze caches.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/register/action | Registreert de resourceprovider Microsoft.Cache bij een abonnement |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/redis/* | Redis-caches maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee worden de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik opgeslagen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/register/action",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>Inzender voor SQL DB

Hiermee kunt u SQL-databases beheren, maar geen toegang krijgen tot deze databases. U kunt ook hun beveiligingsbeleid of hun bovenliggende SQL-servers niet beheren. [Meer informatie](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/* | SQL-databases maken en beheren |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | Retourneert de lijst met servers of haalt de eigenschappen voor de opgegeven server op. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Metrische gegevens lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Metrische definities lezen |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Controle-instellingen bewerken |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | De databaseblob-controlerecords ophalen |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Beleid voor gegevensmaskering bewerken |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Beveiligingswaarschuwingsbeleid bewerken |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Metrische beveiligingsgegevens bewerken |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>SQL Managed Instance-inzender

Hiermee kunt u SQL Managed Instances en de vereiste netwerkconfiguratie beheren, maar u kunt geen toegang verlenen aan anderen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/instanceFailoverGroups/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnetten/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Metrische gegevens lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Metrische definities lezen |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/delete | Hiermee verwijdert u een specifieke beheerde server Azure Active Directory alleen verificatieobject |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/write | Hiermee wordt een specifiek beheerd serverobject toegevoegd of Azure Active Directory alleen verificatieobject |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/locations/instanceFailoverGroups/*",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>SQL-beveiligingsbeheerder

Hiermee kunt u het beveiligingsbeleid van SQL-servers en -databases beheren, maar geen toegang tot deze servers. [Meer informatie](../azure-sql/database/azure-defender-for-sql.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Voegt resource, zoals opslagaccount of SQL database aan een subnet. Kan niet worden gewaarschuwd. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/administratorAzureAsyncOperation/read | Haalt het resultaat van azure async administrator-bewerkingen van het beheerde exemplaar op. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | Controle-instelling voor SQL Server maken en beheren |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/read | Details ophalen van het uitgebreide blobcontrolebeleid voor servers dat is geconfigureerd op een bepaalde server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Controle-instellingen voor SQL Server-database maken en beheren |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | De databaseblob-controlerecords ophalen |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Gegevensmaskeringsbeleid voor SQL Server-database maken en beheren |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/read | Details ophalen van het uitgebreide blobcontrolebeleid dat is geconfigureerd voor een bepaalde database |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/read | Retourneert de lijst met databases of haalt de eigenschappen voor de opgegeven database op. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/read | Haal een databaseschema op. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/read | Haal een databasekolom op. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/read | Haal een databasetabel op. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Beveiligingswaarschuwingsbeleid voor SQL Server-database maken en beheren |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Metrische beveiligingsgegevens voor SQL Server-database maken en beheren |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/devOpsAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/firewallRules/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | Retourneert de lijst met servers of haalt de eigenschappen voor de opgegeven server op. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | Beveiligingswaarschuwingsbeleid voor SQL Server maken en beheren |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/read | Retourneert de lijst met beheerde exemplaren of haalt de eigenschappen op voor het opgegeven beheerde exemplaar. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/* |  |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/sqlVulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/administrators/read | Haalt een lijst met beheerders van beheerde exemplaren op. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/administrators/read | Haalt een specifiek Azure Active Directory administratorobject op |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/administratorAzureAsyncOperation/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/devOpsAuditingSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/*",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*",
        "Microsoft.Security/sqlVulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/administrators/read",
        "Microsoft.Sql/servers/administrators/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>SQL Server-inzender

Hiermee kunt u SQL-servers en -databases beheren, maar geen toegang krijgen tot deze servers en niet het bijbehorende beveiligingsbeleid. [Meer informatie](../azure-sql/database/authentication-aad-configure.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/* | SQL-servers maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Metrische gegevens lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Metrische definities lezen |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | Controle-instellingen voor SQL Server bewerken |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Controle-instellingen voor SQL Server-database bewerken |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | De databaseblob-controlerecords ophalen |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Gegevensmaskeringsbeleid voor SQL Server-database bewerken |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Beveiligingswaarschuwingsbeleid voor SQL Server-database bewerken |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Metrische beveiligingsgegevens van SQL Server-database bewerken |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/devOpsAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | Beveiligingswaarschuwingsbeleid voor SQL Server bewerken |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/delete | Hiermee verwijdert u een specifiek serverobject Azure Active Directory alleen verificatieobject |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/write | Hiermee wordt een specifiek serverobject toegevoegd of Azure Active Directory alleen verificatieobject |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/devOpsAuditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Analyse


### <a name="azure-event-hubs-data-owner"></a>Azure Event Hubs eigenaar van gegevens

Biedt volledige toegang tot Azure Event Hubs resources. [Meer informatie](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Azure Event Hubs-ontvanger

Hiermee krijgt u toegang tot Azure Event Hubs resources. [Meer informatie](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/receive/action |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Azure Event Hubs Afzender van gegevens

Hiermee kunt u toegang tot Azure Event Hubs resources verzenden. [Meer informatie](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/send/action |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Data Factory-inzender

Maak en beheer gegevensfabrieken en onderliggende resources in deze resources. [Meer informatie](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | Maak en beheer gegevensfabrieken en onderliggende resources in deze resources. |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/factories/* | Maak en beheer gegevensfabrieken en onderliggende resources in deze resources. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | Een eventSubscription maken of bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Gegevens ops purgeren

Privégegevens verwijderen uit een Log Analytics-werkruimte. [Meer informatie](../azure-monitor/logs/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/purge/action | Gegevens verwijderen uit Application Insights |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Log Analytics-gegevens weergeven |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/purge/action | Opgegeven gegevens uit werkruimte verwijderen |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>HDInsight-clusteroperator

Hiermee kunt u HDInsight-clusterconfiguraties lezen en wijzigen. [Meer informatie](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/*/read |  |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/getGatewaySettings/action | Gateway-instellingen voor HDInsight-cluster opsommen |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/updateGatewaySettings/action | Gateway-instellingen voor HDInsight-cluster bijwerken |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/configurations/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Haalt implementatiebewerkingen op of vermeldt deze. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>Inzender voor HDInsight Domain Services

Kan Domain Services-gerelateerde bewerkingen lezen, maken, wijzigen en verwijderen die nodig zijn voor HDInsight Enterprise Security Package [meer informatie](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Inzender van Log Analytics

Log Analytics-inzender kan alle bewakingsgegevens lezen en bewakingsinstellingen bewerken. Het bewerken van bewakingsinstellingen omvat het toevoegen van de VM-extensie aan VM's; opslagaccountsleutels lezen om het verzamelen van logboeken van Azure Storage; Automation-accounts maken en configureren; oplossingen toevoegen; en azure diagnostics configureren voor alle Azure-resources. [Meer informatie](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */read | Lees alle typen resources, met uitzondering van geheimen. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/* |  |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/extensions/* |  |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Hiermee worden de toegangssleutels voor de opslagaccounts vermeld. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/extensions/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Installeert of werkt een Azure Arc-extensies |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Hiermee maakt, werkt of leest u de diagnostische instelling voor Analyseserver |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Retourneert de toegangssleutels voor het opgegeven opslagaccount. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Lezer van Log Analytics

Log Analytics-lezer kan alle bewakingsgegevens bekijken en doorzoeken, evenals bewakingsinstellingen, inclusief het bekijken van de configuratie van diagnostische gegevens van Azure voor alle Azure-resources. [Meer informatie](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */lezen | Lees resources van alle typen, met uitzondering van geheimen. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Zoeken met behulp van een nieuwe engine. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Hiermee wordt een zoekquery uitgevoerd |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/read | Hiermee haalt u de gedeelde sleutels voor de werkruimte op. Deze sleutels worden gebruikt om Microsoft-Operational Insights te verbinden met de werkruimte. |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-curator"></a>Purview DataCursator

De Microsoft.Purview-gegevenscursator kan catalogusgegevensobjecten maken, lezen, wijzigen en verwijderen en relaties tussen objecten tot stand brengen. Deze rol is in preview en kan worden gewijzigd.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Lees de accountresource voor de Microsoft Purview-provider. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/read | Gegevensobjecten lezen. |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/write | Gegevensobjecten maken, bijwerken en verwijderen. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data curator can create, read, modify and delete catalog data objects and establish relationships between objects. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "name": "8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read",
        "Microsoft.Purview/accounts/data/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Curator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-reader"></a>Gegevenslezer voor purviewen

De Microsoft.Purview-gegevenslezer kan catalogusgegevensobjecten lezen. Deze rol is in preview en kan worden gewijzigd.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Lees de accountresource voor de Microsoft Purview-provider. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/read | Gegevensobjecten lezen. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data reader can read catalog data objects. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ff100721-1b9d-43d8-af52-42b69c1272db",
  "name": "ff100721-1b9d-43d8-af52-42b69c1272db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-source-administrator"></a>Gegevensbronbeheerder purviewen

De microsoft.Purview-gegevensbronbeheerder kan gegevensbronnen en gegevensscans beheren. Deze rol is in preview en kan worden gewijzigd.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Lees de accountresource voor de Microsoft Purview-provider. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/scan/read | Gegevensbronnen en scans lezen. |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/scan/write | Gegevensbronnen maken, bijwerken en verwijderen en scans beheren. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data source administrator can manage data sources and data scans. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/200bba9e-f0c8-430f-892b-6f0794863803",
  "name": "200bba9e-f0c8-430f-892b-6f0794863803",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/scan/read",
        "Microsoft.Purview/accounts/scan/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Source Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-contributor-preview"></a>Inzender van schemaregisters (preview)

Schemaregistergroepen en schema's lezen, schrijven en verwijderen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read, write, and delete Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5dffeca3-4936-4216-b2bc-10343a5abb25",
  "name": "5dffeca3-4936-4216-b2bc-10343a5abb25",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-reader-preview"></a>Lezer van schemaregisters (preview)

Schemaregistergroepen en schema's lezen en weergeven.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/read | Lijst met resourcebeschrijvingen van SchemaGroup op halen |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/read | Schema's ophalen |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and list Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "name": "2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Blockchain


### <a name="blockchain-member-node-access-preview"></a>Toegang tot blockchain-lid-knooppunt (preview)

Hiermee kunt u toegang krijgen tot blockchain-lidknooppunten [Meer informatie](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/read | Hiermee worden bestaande blockchain-lidtransactie-knooppunt(en) opgeslagen of vermeld. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/connect/action | Maakt verbinding met een blockchain-lidtransactie-knooppunt. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>AI en machine learning


### <a name="cognitive-services-contributor"></a>Cognitive Services-inzender

Hiermee kunt u sleutels van de Cognitive Services. [Meer informatie](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/features/read | Haalt de functies van een abonnement op. |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/providers/features/read | Haalt de functie van een abonnement op in een bepaalde resourceprovider. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Hiermee maakt, werkt of leest u de diagnostische instelling voor Analyseserver |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | Logboekdefinities lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | Metrische definities lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Metrische gegevens lezen |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Haalt implementatiebewerkingen op of vermeldt deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Haal de resultaten van de abonnementsbewerking op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Haalt de lijst met abonnementen op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-contributor"></a>Cognitive Services Custom Vision-inzender

Volledige toegang tot het project, inclusief de mogelijkheid om projecten weer te geven, te maken, te bewerken of te verwijderen. [Meer informatie](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the ability to view, create, edit, or delete projects.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "name": "c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Custom Vision Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-deployment"></a>Cognitive Services Custom Vision implementatie

Modellen publiceren, publiceren of exporteren. Implementatie kan het project weergeven, maar kan niet worden bijgewerkt. [Meer informatie](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/publish/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/export/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/quicktest/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/classify/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/detect/* |  |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Hiermee exporteert u een project. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Publish, unpublish or export models. Deployment can view the project but can't update.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "name": "5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/publish/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/export/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/quicktest/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/classify/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/detect/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Deployment",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-labeler"></a>Cognitive Services Custom Vision Labeler

Trainingsafbeeldingen weergeven, bewerken en de afbeeldingstags maken, toevoegen, verwijderen of verwijderen. Labelaars kunnen het project bekijken, maar kunnen niets anders dan trainingsafbeeldingen en tags bijwerken. [Meer informatie](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/query/action | Haal afbeeldingen op die zijn verzonden naar uw voorspellings-eindpunt. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tags/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/suggested/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tagsandregions/suggestions/action | Deze API krijgt voorgestelde tags en regio's voor een matrix/batch met afbeeldingen zonder tag, samen met betrouwbaarheid voor de tags. Er wordt een lege matrix retourneert als er geen tags worden gevonden. |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Hiermee exporteert u een project. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit training images and create, add, remove, or delete the image tags. Labelers can view the project but can't update anything other than training images and tags.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/88424f51-ebe7-446f-bc41-7fa16989e96c",
  "name": "88424f51-ebe7-446f-bc41-7fa16989e96c",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/suggested/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tagsandregions/suggestions/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Labeler",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-reader"></a>Cognitive Services Custom Vision Lezer

Alleen-lezenacties in het project. Lezers kunnen het project niet maken of bijwerken. [Meer informatie](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/query/action | Haal afbeeldingen op die naar uw voorspellings-eindpunt zijn verzonden. |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Hiermee exporteert u een project. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only actions in the project. Readers can't create or update the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "name": "93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-trainer"></a>Cognitive Services Custom Vision Cognitive Services Custom Vision

Bekijk, bewerk projecten en train de modellen, met inbegrip van de mogelijkheid om de modellen te publiceren, uit te publiceren en te exporteren. Docenten kunnen het project niet maken of verwijderen. [Meer informatie](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/action | Maak een project. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/delete | Verwijder een specifiek project. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/import/action | Hiermee importeert u een project. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Hiermee exporteert u een project. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit projects and train the models, including the ability to publish, unpublish, export the models. Trainers can't create or delete the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "name": "0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/delete",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/import/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Trainer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Cognitive Services Gegevenslezer (preview)

Hiermee kunt u Cognitive Services lezen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-metrics-advisor-administrator"></a>Cognitive Services Metrics Advisor Administrator

Volledige toegang tot het project, inclusief de configuratie op systeemniveau. [Meer informatie](../cognitive-services/metrics-advisor/how-tos/alerts.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/Metrics Advisor/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the system level configuration.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cb43c632-a144-4ec5-977c-e80c4affc34a",
  "name": "cb43c632-a144-4ec5-977c-e80c4affc34a",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/MetricsAdvisor/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Metrics Advisor Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-editor"></a>Cognitive Services QnA Maker Editor

Hier kunt u een KB maken, bewerken, importeren en exporteren. U kunt een KB niet publiceren of verwijderen. [Meer informatie](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Informatie over een roltoewijzing. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/read | Informatie over een roldefinitie. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/read | Haalt een lijst op met KnowledgeBases of details van een specifieke KnowledgeBaser. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/read | Download de KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/create/write | Asynchrone bewerking voor het maken van een nieuwe KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/write | Asynchrone bewerking voor het wijzigen van een knowledgebase of het vervangen van de inhoud van knowledgebase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/action | GenerateAnswer-aanroep om een query uit te voeren op de KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/train/action | Train aanroepen om suggesties toe te voegen aan de KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/read | Download wijzigingen uit runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/write | Vervang wijzigingen in gegevens. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/read | Haalt eindpuntsleutels voor een eindpunt op |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/refreshkeys/action | Genereert opnieuw een eindpuntsleutel. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/read | Haalt eindpuntinstellingen voor een eindpunt op |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/write | Eindpunt-seettings voor een eindpunt bijwerken. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/operations/read | Haalt details op van een specifieke langdurige bewerking. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/read | Haalt een lijst op met KnowledgeBases of details van een specifieke KnowledgeBaser. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/read | Download de KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/create/write | Asynchrone bewerking voor het maken van een nieuwe KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/write | Asynchrone bewerking voor het wijzigen van een knowledgebase of het vervangen van de inhoud van knowledgebase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | GenerateAnswer-aanroep om een query uit te voeren op de KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/train/action | Train de aanroep om suggesties toe te voegen aan de KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/read | Download wijzigingen uit runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/write | Vervang wijzigingen in gegevens. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/read | Haalt eindpuntsleutels voor een eindpunt op |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action | Genereert opnieuw een eindpuntsleutel. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/read | Haalt eindpuntinstellingen voor een eindpunt op |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/write | Eindpunt-seettings voor een eindpunt bijwerken. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/operations/read | Haalt details op van een specifieke langlopende bewerking. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/read | Haalt een lijst op met KnowledgeBases of details van een specifieke KnowledgeBaser. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read | Download de KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/create/write | Asynchrone bewerking voor het maken van een nieuwe KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/write | Asynchrone bewerking voor het wijzigen van een knowledgebase of het vervangen van de inhoud van knowledgebase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action | GenerateAnswer-aanroep om een query uit te voeren op de KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/train/action | Train de aanroep om suggesties toe te voegen aan de KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/read | Download wijzigingen uit runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/write | Wijzigingsgegevens vervangen. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/read | Haalt eindpuntsleutels voor een eindpunt op |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/refreshkeys/action | Genereert een eindpuntsleutel opnieuw. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/read | Haalt eindpuntinstellingen voor een eindpunt op |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/write | Eindpunt-seettings voor een eindpunt bijwerken. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/operations/read | Haalt details op van een specifieke langdurige bewerking. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you create, edit, import and export a KB. You cannot publish or delete a KB.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "name": "f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/operations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/operations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/operations/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Editor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-reader"></a>Cognitive Services QnA Maker Reader

U kunt alleen een KB lezen en testen. [Meer informatie](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Informatie over een roltoewijzing. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/read | Informatie over een roldefinitie. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/read | Haalt een lijst op met KnowledgeBases of details van een specifieke KnowledgeBaser. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/read | Download de KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/action | GenerateAnswer-aanroep om een query uit te voeren op de KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/read | Download wijzigingen uit runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/read | Haalt eindpuntsleutels voor een eindpunt op |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/read | Haalt eindpuntinstellingen voor een eindpunt op |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/read | Haalt een lijst op met KnowledgeBases of details van een specifieke KnowledgeBaser. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/read | Download de KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | GenerateAnswer-aanroep om een query uit te voeren op de KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/read | Download wijzigingen uit runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/read | Haalt eindpuntsleutels voor een eindpunt op |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/read | Haalt eindpuntinstellingen voor een eindpunt op |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/read | Haalt een lijst op met KnowledgeBases of details van een specifieke KnowledgeBaser. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read | Download de KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action | GenerateAnswer-aanroep om een query uit te voeren op de KnowledgeBase. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/read | Download wijzigingen uit runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/read | Haalt eindpuntsleutels voor een eindpunt op |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/read | Haalt eindpuntinstellingen voor een eindpunt op |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you read and test a KB only.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/466ccd10-b268-4a11-b098-b4849f024126",
  "name": "466ccd10-b268-4a11-b098-b4849f024126",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Cognitive Services gebruiker

Hiermee kunt u sleutels van de Cognitive Services. [Meer informatie](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/listkeys/action | Sleutels opslijst |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Een klassieke waarschuwing voor metrische gegevens lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/read | Een diagnostische instelling voor resources lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | Logboekdefinities lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | Metrische definities lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Metrische gegevens lezen |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Haalt implementatiebewerkingen op of vermeldt deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Haal de resultaten van de abonnementsbewerking op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Haalt de lijst met abonnementen op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="internet-of-things"></a>Internet of things


### <a name="device-update-administrator"></a>Apparaatupdatebeheerder

Biedt u volledige toegang tot beheer- en inhoudsbewerkingen [Meer informatie](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Voert een leesbewerking uit met betrekking tot updates |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/write | Voert een schrijfbewerking uit met betrekking tot updates |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/delete | Voert een verwijderbewerking uit met betrekking tot updates |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Voert een leesbewerking uit met betrekking tot beheer |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/write | Voert een schrijfbewerking uit met betrekking tot beheer |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/delete | Voert een verwijderbewerking uit met betrekking tot beheer |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to management and content operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/02ca0879-e8e4-47a5-a61e-5c618b76e64a",
  "name": "02ca0879-e8e4-47a5-a61e-5c618b76e64a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/updates/write",
        "Microsoft.DeviceUpdate/accounts/instances/updates/delete",
        "Microsoft.DeviceUpdate/accounts/instances/management/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/write",
        "Microsoft.DeviceUpdate/accounts/instances/management/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-content-administrator"></a>Inhoudsbeheerder voor apparaatupdates

Geeft u volledige toegang tot inhoudsbewerkingen [Meer informatie](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Voert een leesbewerking uit met betrekking tot updates |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/write | Voert een schrijfbewerking uit met betrekking tot updates |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/delete | Voert een verwijderbewerking uit met betrekking tot updates |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to content operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0378884a-3af5-44ab-8323-f5b22f9f3c98",
  "name": "0378884a-3af5-44ab-8323-f5b22f9f3c98",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/updates/write",
        "Microsoft.DeviceUpdate/accounts/instances/updates/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Content Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-content-reader"></a>Inhoudslezer voor apparaatupdates

Geeft u leestoegang tot inhoudsbewerkingen, maar biedt geen mogelijkheid om wijzigingen aan te brengen [Meer informatie](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Voert een leesbewerking uit met betrekking tot updates |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to content operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d1ee9a80-8b14-47f0-bdc2-f4a351625a7b",
  "name": "d1ee9a80-8b14-47f0-bdc2-f4a351625a7b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Content Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-deployments-administrator"></a>Beheerder van apparaatupdate-implementaties

Biedt u volledige toegang tot beheerbewerkingen [Meer informatie](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Voert een leesbewerking uit met betrekking tot beheer |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/write | Voert een schrijfbewerking uit met betrekking tot beheer |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/delete | Voert een verwijderbewerking uit met betrekking tot beheer |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e4237640-0e3d-4a46-8fda-70bc94856432",
  "name": "e4237640-0e3d-4a46-8fda-70bc94856432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/management/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/write",
        "Microsoft.DeviceUpdate/accounts/instances/management/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Deployments Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-deployments-reader"></a>Lezer voor implementaties van apparaatupdates

Geeft u leestoegang tot beheerbewerkingen, maar staat geen wijzigingen toe [Meer informatie](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Voert een leesbewerking uit met betrekking tot beheer |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to management operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49e2f5d2-7741-4835-8efa-19e1fe35e47f",
  "name": "49e2f5d2-7741-4835-8efa-19e1fe35e47f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/management/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Deployments Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-reader"></a>Lezer apparaatupdate

Geeft u leestoegang tot beheer- en inhoudsbewerkingen, maar biedt geen mogelijkheid om wijzigingen aan te brengen [Meer informatie](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Voert een leesbewerking uit met betrekking tot updates |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Voert een leesbewerking uit met betrekking tot beheer |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to management and content operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f",
  "name": "e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Mixed reality


### <a name="remote-rendering-administrator"></a>Remote Rendering Administrator

Biedt de gebruiker mogelijkheden voor conversie, sessie-, rendering- en diagnostische Azure Remote Rendering [meer informatie](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/action | Conversie van asset starten |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/read | Eigenschappen van assetconversie op halen |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/delete | Conversie van asset stoppen |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | Sessie-eigenschappen op halen |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | Sessies starten |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | Sessies stoppen |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | Verbinding maken met een sessie |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Verbinding maken met de Remote Rendering inspector |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with conversion, manage session, rendering and diagnostics capabilities for Azure Remote Rendering",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "name": "3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="remote-rendering-client"></a>Remote Rendering Client

Biedt gebruikers mogelijkheden voor het beheren van sessies, rendering en diagnostische Azure Remote Rendering. [Meer informatie](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | Sessie-eigenschappen op halen |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | Sessies starten |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | Sessies stoppen |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | Verbinding maken met een sessie |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Verbinding maken met de Remote Rendering inspector |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with manage session, rendering and diagnostics capabilities for Azure Remote Rendering.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "name": "d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Client",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-contributor"></a>Spatial Anchors-account inzender

Hiermee kunt u spatial anchors in uw account beheren, maar niet verwijderen [Meer informatie](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | Ruimtelijke ankers maken |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Ruimtelijke ankers in de buurt ontdekken |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Eigenschappen van ruimtelijke ankers op halen |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Ruimtelijke ankers zoeken |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Diagnostische gegevens verzenden om de kwaliteit van de Azure Spatial Anchors-service te verbeteren |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Eigenschappen van ruimtelijke ankers bijwerken |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Spatial Anchors accounteigenaar

Hiermee kunt u ruimtelijke ankers in uw account beheren, inclusief het verwijderen ervan [Meer informatie](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | Ruimtelijke ankers maken |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/delete | Ruimtelijke ankers verwijderen |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Ruimtelijke ankers in de buurt ontdekken |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Eigenschappen van ruimtelijke ankers krijgen |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Ruimtelijke ankers zoeken |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Diagnostische gegevens verzenden om de kwaliteit van de Azure Spatial Anchors-service te verbeteren |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Eigenschappen van ruimtelijke ankers bijwerken |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Spatial Anchors-accountlezer

Hiermee kunt u eigenschappen van ruimtelijke ankers in uw account zoeken en lezen [Meer informatie](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Ruimtelijke ankers in de buurt ontdekken |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Eigenschappen van ruimtelijke ankers op halen |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Ruimtelijke ankers zoeken |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Diagnostische gegevens verzenden om de kwaliteit van de Azure Spatial Anchors-service te verbeteren |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>Integratie


### <a name="api-management-service-contributor"></a>API Management servicebijdrager

Kan de service en de API's beheren [Meer informatie](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/* | Een service voor API Management maken en beheren |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>API Management Rol serviceoperator

Kan de service beheren, maar niet de API's [Meer informatie](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | Exemplaren API Management Service lezen |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/backup/action | Backup API Management Service naar de opgegeven container in een door de gebruiker opgegeven opslagaccount |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/delete | Een API Management Service-exemplaar verwijderen |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/managedeployments/action | SKU/eenheden wijzigen, regionale implementaties van API Management Service toevoegen/verwijderen |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | Metagegevens lezen voor een API Management Service-exemplaar |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/restore/action | Restore API Management Service from the specified container in a user provided storage account (Een service herstellen vanuit de opgegeven container in een door de gebruiker opgegeven opslagaccount) |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatecertificate/action | TLS/SSL-certificaat uploaden voor een API Management Service |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatehostname/action | Aangepaste domeinnamen voor een API Management Service instellen, bijwerken API Management verwijderen |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/write | Een service-API Management maken of bijwerken |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | Sleutels ophalen die zijn gekoppeld aan de gebruiker |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>API Management rol servicelezer

Alleen-lezentoegang tot service en API's [Meer informatie](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | Exemplaren API Management Service lezen |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | Metagegevens lezen voor een API Management Service-exemplaar |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee worden de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik opgeslagen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | Sleutels ophalen die zijn gekoppeld aan de gebruiker |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>App Configuration eigenaar van gegevens

Biedt volledige toegang tot App Configuration gegevens. [Meer informatie](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/write |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/delete |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>App Configuration-gegevenslezer

Hiermee staat u leestoegang tot App Configuration gegevens toe. [Meer informatie](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Azure Service Bus eigenaar van gegevens

Biedt volledige toegang tot Azure Service Bus resources. [Meer informatie](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Azure Service Bus-ontvanger

Hiermee kunt u toegang krijgen tot Azure Service Bus resources. [Meer informatie](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/receive/action |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Azure Service Bus Afzender van gegevens

Hiermee kunt u toegang tot Azure Service Bus verzenden. [Meer informatie](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/send/action |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Azure Stack registratie-eigenaar

Hiermee kunt u Azure Stack beheren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/read | De eigenschappen van een Azure Stack Edge-abonnement |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/*/action |  |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/read | De eigenschappen van een Azure Stack Marketplace-product |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/read | Haalt de eigenschappen van een Azure Stack op |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/edgeSubscriptions/read",
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-contributor"></a>EventGrid-inzender

Hiermee kunt u EventGrid-bewerkingen beheren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/* | Resources voor Event Grid maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1e241071-0855-49ea-94dc-649edcd759de",
  "name": "1e241071-0855-49ea-94dc-649edcd759de",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription Contributor

Hiermee kunt u eventgrid-gebeurtenisabonnementbewerkingen beheren. [Meer informatie](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* | Regionale gebeurtenisabonnementen maken en beheren |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | Globale gebeurtenisabonnementen per onderwerptype |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | Regionale gebeurtenisabonnementen op een lijst |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | Regionale gebeurtenisabonnementen per onderwerptype |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription Reader

Hiermee kunt u EventGrid-gebeurtenisabonnementen lezen. [Meer informatie](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | Een eventSubscription lezen |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | Globale gebeurtenisabonnementen per onderwerptype |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | Regionale gebeurtenisabonnementen op een lijst zetten |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | Regionale gebeurtenisabonnementen per onderwerptype |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-contributor"></a>FHIR-gegevensbijdrager

Rol geeft gebruiker of principal volledige toegang tot FHIR-gegevens [Meer informatie](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal full access to FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "name": "5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-exporter"></a>FHIR Data Export

Rol staat gebruiker of principal toe om FHIR-gegevens te lezen en te exporteren [Meer informatie](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | Lees FHIR-resources (inclusief zoek- en versiegeschiedenis).  |
> | Microsoft.HealthcareApis/services/fhir/resources/export/action | Exportbewerking ($export). |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and export FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3db33094-8700-4567-8da5-1501d4e7e843",
  "name": "3db33094-8700-4567-8da5-1501d4e7e843",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/services/fhir/resources/export/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Exporter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-reader"></a>FHIR-gegevenslezer

Rol staat gebruiker of principal toe om FHIR-gegevens te lezen [Meer informatie](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | Lees FHIR-resources (inclusief zoek- en versiegeschiedenis).  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "name": "4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-writer"></a>FHIR Data Writer

Rol staat gebruiker of principal toe om FHIR-gegevens te lezen en schrijven [Meer informatie](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action | Hard Delete (inclusief versiegeschiedenis). |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and write FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3f88fce4-5892-4214-ae73-ba5294559913",
  "name": "3f88fce4-5892-4214-ae73-ba5294559913",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action"
      ]
    }
  ],
  "roleName": "FHIR Data Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-contributor"></a>Integratieserviceomgeving-inzender

Hiermee kunt u integratieserviceomgevingen beheren, maar geen toegang tot deze omgevingen. [Meer informatie](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage integration service environments, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "name": "a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-developer"></a>Integratieserviceomgeving Developer

Hiermee kunnen ontwikkelaars werkstromen, integratieaccounts en API-verbindingen maken en bijwerken in integratieserviceomgevingen. [Meer informatie](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/read | Leest de integratieserviceomgeving. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/*/join/action |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows developers to create and update workflows, integration accounts and API connections in integration service environments.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "name": "c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/read",
        "Microsoft.Logic/integrationServiceEnvironments/*/join/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Inzender voor Intelligent Systems-account

Hiermee kunt u Intelligent Systems-accounts beheren, maar geen toegang tot deze accounts.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | Microsoft.IntelligentSystems/accounts/* | Intelligente systeemaccounts maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Inzender voor logische apps

Hiermee kunt u logische apps beheren, maar de toegang tot deze apps niet wijzigen. [Meer informatie](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Hiermee worden de toegangssleutels voor de opslagaccounts vermeld. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | Retourner het opslagaccount met het opgegeven account. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Hiermee maakt, werkt of leest u de diagnostische instelling voor Analyseserver |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | Deze machtiging is nodig voor gebruikers die toegang nodig hebben tot activiteitenlogboeken via de portal. Lijst met logboekcategorieën in activiteitenlogboek. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | Metrische definities lezen (lijst met beschikbare metrische typen voor een resource). |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/* | Beheert Logic Apps resources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Haal de resultaten van de abonnementsbewerking op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | Retourneert de toegangssleutels voor het opgegeven opslagaccount. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Retourneert de lijst met opslagaccounts of haalt de eigenschappen voor het opgegeven opslagaccount op. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | Een verbindingsgateway maken en beheren. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/* | Een verbinding maken en beheren. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/* | Hiermee maakt en beheert u een aangepaste API. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | Een App Service-abonnement |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | De eigenschappen van een App Service krijgen |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/functions/listSecrets/action | Lijst met functiegeheimen. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Logische app-operator

Hiermee kunt u logische apps lezen, inschakelen en uitschakelen, maar niet bewerken of bijwerken. [Meer informatie](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/read | Waarschuwingsregels voor Inzichten lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/read | Haalt diagnostische instellingen op voor Logic Apps |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/read | Haalt de beschikbare metrische gegevens voor Logic Apps. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/*/read | Leest Logic Apps resources. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/disable/action | Hiermee schakelt u de werkstroom uit. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/enable/action | Hiermee schakelt u de werkstroom in. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/validate/action | Valideert de werkstroom. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Haalt implementatiebewerkingen op of vermeldt deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Haal de resultaten van de abonnementsbewerking op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/read | Lees Verbindingsgateways. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/*/read | Verbindingen lezen. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/*/read | Aangepaste API lezen. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | De eigenschappen van een App Service krijgen |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>Identiteit


### <a name="managed-identity-contributor"></a>Inzender voor beheerde identiteit

Een door de gebruiker toegewezen identiteit maken, lezen, bijwerken en [verwijderen Meer informatie](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/read | Haalt een bestaande door de gebruiker toegewezen identiteit op |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/write | Hiermee maakt u een nieuwe door de gebruiker toegewezen identiteit of werkt u de tags bij die zijn gekoppeld aan een bestaande door de gebruiker toegewezen identiteit |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/delete | Hiermee verwijdert u een bestaande door de gebruiker toegewezen identiteit |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Operator voor beheerde identiteit

Lees en wijs een door de gebruiker toegewezen identiteit toe [Meer informatie](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/read |  |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/assign/action |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Beveiliging


### <a name="attestation-contributor"></a>Attestation-inzender

Kan het exemplaar van de Attestation-provider lezen of verwijderen [Meer informatie](../attestation/quickstart-powershell.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
> | Microsoft.Attestation/attestationProviders/attestation/write |  |
> | Microsoft.Attestation/attestationProviders/attestation/delete |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read write or delete the attestation provider instance",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "name": "bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read",
        "Microsoft.Attestation/attestationProviders/attestation/write",
        "Microsoft.Attestation/attestationProviders/attestation/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="attestation-reader"></a>Attestation-lezer

De eigenschappen van de Attestation-provider lezen [Meer informatie](../attestation/troubleshoot-guide.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read the attestation provider properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "name": "fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-automation-contributor"></a>Azure Sentinel Automation-inzender

Azure Sentinel Automation-inzender [Meer informatie](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/triggers/read | Leest de trigger. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/triggers/listCallbackUrl/action | Haalt de callback-URL voor de trigger op. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/runs/read | Leest de werkstroom uit te voeren. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Automation Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4c81013-99ee-4d62-a7ee-b3f1f648599a",
  "name": "f4c81013-99ee-4d62-a7ee-b3f1f648599a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Logic/workflows/triggers/read",
        "Microsoft.Logic/workflows/triggers/listCallbackUrl/action",
        "Microsoft.Logic/workflows/runs/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Automation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-contributor"></a>Azure Sentinel Contributor

Azure Sentinel Inzender [Meer informatie](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Zoeken met behulp van een nieuwe engine. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Log Analytics-gegevens weergeven |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Afsluitende OMS-oplossing krijgen |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Query's uitvoeren op de gegevens in de werkruimte |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Haal gegevensbron op onder een werkruimte. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Een privé-werkmap lezen |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Azure Sentinel Reader

Azure Sentinel Reader [Meer informatie](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | Gebruikersautorisatie en -licentie controleren |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | Indicatoren voor bedreigingsinformatie voor query's |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | Indicatoren voor bedreigingsinformatie voor query's |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Zoeken met behulp van een nieuwe engine. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Log Analytics-gegevens weergeven |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/LinkedServices/read | Haal gekoppelde services op onder de opgegeven werkruimte. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | Haalt een opgeslagen zoekquery op |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Afsluitende OMS-oplossing krijgen |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Query's uitvoeren op de gegevens in de werkruimte |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Haal gegevensbron op onder een werkruimte. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Een werkmap lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Een privé-werkmap lezen |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Azure Sentinel Responder

Azure Sentinel Responder [Meer informatie](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | Gebruikersautorisatie en -licentie controleren |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/automationRules/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | Tags toevoegen aan Indicator voor bedreigingsinformatie |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | Indicatoren voor bedreigingsinformatie voor query's |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/bulkTag/action | Bedreigingsinformatie bulksgewijs taggen |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | Tags toevoegen aan Indicator voor bedreigingsinformatie |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/replaceTags/action | Tags van bedreigingsinformatie-indicator vervangen |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | Indicatoren voor bedreigingsinformatie voor query's |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Zoeken met behulp van een nieuwe engine. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Log Analytics-gegevens weergeven |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Haal gegevensbron op onder een werkruimte. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | Haalt een opgeslagen zoekquery op |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Afsluitende OMS-oplossing krijgen |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Query's uitvoeren op de gegevens in de werkruimte |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Haal gegevensbron op onder een werkruimte. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Een werkmap lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Een privé-werkmap lezen |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/*/Delete |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/*/Delete |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/automationRules/*",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/bulkTag/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/replaceTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.SecurityInsights/cases/*/Delete",
        "Microsoft.SecurityInsights/incidents/*/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-administrator"></a>Key Vault Administrator

Alle gegevensvlakbewerkingen uitvoeren op een sleutelkluis en alle objecten daarin, inclusief certificaten, sleutels en geheimen. Kan geen key vault-resources beheren of roltoewijzingen beheren. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Controleert of de naam van een sleutelkluis geldig is en niet wordt gebruikt |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | De eigenschappen van zacht verwijderde sleutelkluizen weergeven |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Een lijst met bewerkingen die beschikbaar zijn op de resourceprovider Microsoft.KeyVault |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform all data plane operations on a key vault and all objects in it, including certificates, keys, and secrets. Cannot manage key vault resources or manage role assignments. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "name": "00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer"></a>Key Vault Certificates Officer

Voer een actie uit op de certificaten van een sleutelkluis, behalve machtigingen beheren. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Controleert of de naam van een sleutelkluis geldig is en niet wordt gebruikt |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | De eigenschappen van zacht verwijderde sleutelkluizen weergeven |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Een lijst met bewerkingen die beschikbaar zijn op de resourceprovider Microsoft.KeyVault |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificatecas/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificates/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the certificates of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4417e6f-fecd-4de8-b567-7b0420556985",
  "name": "a4417e6f-fecd-4de8-b567-7b0420556985",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/certificatecas/*",
        "Microsoft.KeyVault/vaults/certificates/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Certificates Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Key Vault-inzender

U kunt sleutelkluizen beheren, maar u kunt geen rollen toewijzen in Azure RBAC en biedt u geen toegang tot geheimen, sleutels of certificaten. [Meer informatie](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/deletedVaults/purge/action | Een zacht verwijderde sleutelkluis leeg maken |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHsms/* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*",
        "Microsoft.KeyVault/managedHsms/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-officer"></a>Key Vault Crypto Officer

Voer een actie uit op de sleutels van een sleutelkluis, behalve machtigingen beheren. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Controleert of de naam van een sleutelkluis geldig is en niet wordt gebruikt |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | De eigenschappen van zacht verwijderde sleutelkluizen weergeven |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Een lijst met bewerkingen die beschikbaar zijn op de resourceprovider Microsoft.KeyVault |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the keys of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "name": "14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-user"></a>Key Vault cryptoserviceversleutelingsgebruiker

Metagegevens van sleutels lezen en wrap-/unwrap-bewerkingen uitvoeren. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | Een eventSubscription maken of bijwerken |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | Een eventSubscription lezen |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/delete | Een eventSubscription verwijderen |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | Hiermee geeft u sleutels weer in de opgegeven kluis of leest u eigenschappen en openbaar materiaal van een sleutel. Voor asymmetrische sleutels maakt deze bewerking een openbare sleutel weer en biedt deze de mogelijkheid om algoritmen met een openbare sleutel uit te voeren, zoals het versleutelen en verifiëren van handtekeningen. Persoonlijke sleutels en symmetrische sleutels worden nooit blootgesteld. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Verpakt een symmetrische sleutel met een Key Vault sleutel. Als de sleutel Key Vault is, kan deze bewerking worden uitgevoerd door principals met leestoegang. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Pakt een symmetrische sleutel uit met een Key Vault sleutel. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of keys and perform wrap/unwrap operations. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "name": "e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventGrid/eventSubscriptions/write",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/eventSubscriptions/delete"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Service Encryption User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user"></a>Key Vault cryptogebruiker

Cryptografische bewerkingen uitvoeren met behulp van sleutels. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | Lijst met sleutels in de opgegeven kluis of leeseigenschappen en openbaar materiaal van een sleutel. Voor asymmetrische sleutels maakt deze bewerking de openbare sleutel openbaar en biedt deze de mogelijkheid om algoritmen voor openbare sleutels uit te voeren, zoals het versleutelen en verifiëren van handtekeningen. Persoonlijke sleutels en symmetrische sleutels worden nooit blootgesteld. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/update/action | Hiermee worden de opgegeven kenmerken bijgewerkt die zijn gekoppeld aan de opgegeven sleutel. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/backup/action | Hiermee maakt u het back-upbestand van een sleutel. Het bestand kan worden gebruikt om de sleutel te herstellen in Key Vault van hetzelfde abonnement. Er kunnen beperkingen van toepassing zijn. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/encrypt/action | Versleutelt niet-versleutelde tekst met een sleutel. Houd er rekening mee dat als de sleutel asymmetrisch is, deze bewerking kan worden uitgevoerd door principals met leestoegang. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/decrypt/action | Ontsleutelt coderingstekst met een sleutel. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Verpakt een symmetrische sleutel met een Key Vault sleutel. Als de sleutel Key Vault is, kan deze bewerking worden uitgevoerd door principals met leestoegang. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Pakt een symmetrische sleutel uit met een Key Vault sleutel. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/sign/action | Ondertekent een berichten digest (hash) met een sleutel. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/verify/action | Verifieert de handtekening van een berichten digest (hash) met een sleutel. Houd er rekening mee dat als de sleutel asymmetrisch is, deze bewerking kan worden uitgevoerd door principals met leestoegang. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform cryptographic operations using keys. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/12338af0-0e69-4776-bea7-57ae8d297424",
  "name": "12338af0-0e69-4776-bea7-57ae8d297424",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/update/action",
        "Microsoft.KeyVault/vaults/keys/backup/action",
        "Microsoft.KeyVault/vaults/keys/encrypt/action",
        "Microsoft.KeyVault/vaults/keys/decrypt/action",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action",
        "Microsoft.KeyVault/vaults/keys/sign/action",
        "Microsoft.KeyVault/vaults/keys/verify/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader"></a>Key Vault Lezer

Metagegevens van sleutelkluizen en de certificaten, sleutels en geheimen lezen. Kan geen gevoelige waarden lezen, zoals geheime inhoud of sleutelmateriaal. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Controleert of de naam van een sleutelkluis geldig is en niet wordt gebruikt |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | De eigenschappen van zacht verwijderde sleutelkluizen weergeven |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Een lijst met bewerkingen die beschikbaar zijn op de resourceprovider Microsoft.KeyVault |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | De eigenschappen van een geheim weergeven of weergeven, maar niet de waarde ervan. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of key vaults and its certificates, keys, and secrets. Cannot read sensitive values such as secret contents or key material. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21090545-7ca7-4776-b22c-e363652d74d2",
  "name": "21090545-7ca7-4776-b22c-e363652d74d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer"></a>Key Vault Secrets Officer

Voer een actie uit op de geheimen van een sleutelkluis, behalve machtigingen beheren. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Controleert of de naam van een sleutelkluis geldig is en niet wordt gebruikt |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | De eigenschappen van zacht verwijderde sleutelkluizen weergeven |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Een lijst met bewerkingen die beschikbaar zijn op de resourceprovider Microsoft.KeyVault |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the secrets of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "name": "b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user"></a>Key Vault Geheimen-gebruiker

Geheime inhoud lezen. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/getSecret/action | Haalt de waarde van een geheim op. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | De eigenschappen van een geheim weergeven of weergeven, maar niet de waarde ervan. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read secret contents. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4633458b-17de-408a-b874-0445c86b69e6",
  "name": "4633458b-17de-408a-b874-0445c86b69e6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/getSecret/action",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-hsm-contributor"></a>Inzender voor beheerde HSM

Hiermee kunt u beheerde HSM-pools beheren, maar geen toegang tot deze pools. [Meer informatie](../key-vault/managed-hsm/secure-your-managed-hsm.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHSMs/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage managed HSM pools, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18500a29-7fe2-46b2-a342-b16a415e101d",
  "name": "18500a29-7fe2-46b2-a342-b16a415e101d",
  "permissions": [
    {
      "actions": [
        "Microsoft.KeyVault/managedHSMs/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed HSM contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Beveiligingsbeheerder

Machtigingen voor de Security Center. Dezelfde machtigingen als de rol Beveiligingslezer en kunnen ook het beveiligingsbeleid bijwerken en waarschuwingen en aanbevelingen afwijzen. [Meer informatie](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | Beleidstoewijzingen maken en beheren |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | Beleidsdefinities maken en beheren |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyExemptions/* | Beleidsvrijstellingen maken en beheren |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | Beleidssets maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lijst met beheergroepen voor de geverifieerde gebruiker. |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Log Analytics-gegevens weergeven |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | Beveiligingsonderdelen en -beleid maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policyExemptions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Inzender voor beveiligingsevaluatie

Hiermee kunt u evaluaties naar Security Center

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/assessments/write | Beveiligingsevaluaties voor uw abonnement maken of bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>Security Manager (verouderd)

Dit is een verouderde rol. Gebruik in plaats daarvan Beveiligingsbeheerder.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/read | Lees de configuratiegegevens van klassieke virtuele machines |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/write | Schrijfconfiguratie voor klassieke virtuele machines |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/read | Configuratie-informatie over het klassieke netwerk lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | Beveiligingsonderdelen en -beleid maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>Beveiligingslezer

Machtigingen voor Security Center. Kan aanbevelingen, waarschuwingen, een beveiligingsbeleid en beveiligings states weergeven, maar kan geen wijzigingen aanbrengen. [Meer informatie](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Een klassieke waarschuwing voor metrische gegevens lezen |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Log Analytics-gegevens weergeven |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/*/read | Beveiligingsonderdelen en -beleid lezen |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/*/read |  |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/packageDownloads/action | Downloadbare informatie over IoT Defender-pakketten |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/downloadManagerActivation/action | Activeringsbestand van manager downloaden met quotumgegevens voor abonnementen |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotSensors/downloadResetPassword/action | Downloadt het wachtwoordbestand voor IoT-sensoren opnieuw instellen |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lijst met beheergroepen voor de geverifieerde gebruiker. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*/read",
        "Microsoft.Security/iotDefenderSettings/packageDownloads/action",
        "Microsoft.Security/iotDefenderSettings/downloadManagerActivation/action",
        "Microsoft.Security/iotSensors/downloadResetPassword/action",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>DevTest Labs-gebruiker

Hiermee kunt u uw virtuele machines in uw virtuele machine verbinden, starten, opnieuw opstarten en afsluiten Azure DevTest Labs. [Meer informatie](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/read | De eigenschappen van een beschikbaarheidsset op halen |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read | De eigenschappen van een virtuele machine lezen (VM-grootten, runtimestatus, VM-extensies, enzovoort) |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/deallocate/action | De virtuele machine uitschakelen en de rekenresources vrij geven |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | De eigenschappen van een virtuele machine op te halen |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/action | Start de virtuele machine opnieuw op |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/start/action | Start de virtuele machine |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/read | De eigenschappen van een lab lezen |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/claimAnyVm/action | Claim een willekeurige claimbare virtuele machine in het lab. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/createEnvironment/action | Maak virtuele machines in een lab. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/ensureCurrentUserProfile/action | Zorg ervoor dat de huidige gebruiker een geldig profiel heeft in het lab. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/delete | Formules verwijderen. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/read | Formules lezen. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/write | Formules toevoegen of wijzigen. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/policySets/evaluatePolicies/action | Evalueert labbeleid. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/claim/action | Eigenaar worden van een bestaande virtuele machine |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualmachines/listApplicableSchedules/action | Hiermee worden de toepasselijke planningen voor starten/stoppen vermeld, indien van toepassing. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/getRdpFileContents/action | Haalt een tekenreeks op die de inhoud van het RDP-bestand voor de virtuele machine vertegenwoordigt |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | Voegt een back load balancer adresgroep toe. Kan niet worden gewaarschuwd. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | Voegt een load balancer inkomende nat-regel toe. Kan niet worden gewaarschuwd. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/read | De eigenschappen van een netwerkinterface lezen (bijvoorbeeld alle load balancers waar de netwerkinterface deel van uitmaakt) |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/join/action | Voegt een virtuele machine toe aan een netwerkinterface. Kan niet worden gewaarschuwd. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Haalt een definitie van de netwerkinterface op.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Hiermee maakt u een netwerkinterface of werkt u een bestaande netwerkinterface bij.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/read | De eigenschappen van een openbaar IP-adres lezen |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | Voegt een openbaar IP-adres toe. Kan niet worden gewaarschuwd. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Haalt een openbare IP-adresdefinitie op. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Wordt lid van een virtueel netwerk. Kan niet worden gewaarschuwd. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Haalt implementatiebewerkingen op of vermeldt deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Haalt implementaties op of vermeldt deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Retourneert de toegangssleutels voor het opgegeven opslagaccount. |
> | **NotActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/read | Een lijst met beschikbare grootten waar de virtuele machine naar kan worden bijgewerkt |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Labmaker

Hiermee kunt u nieuwe labs maken onder uw Azure Lab-accounts. [Meer informatie](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/read |  |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/action | Maak een lab in een labaccount. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/action | Krijg de prijzen en beschikbaarheid van combinaties van grootten, geografische gebieden en besturingssystemen voor het lab-account. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/action | Kernbeperkingen en gebruik voor dit abonnement krijgen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create new labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>Monitor


### <a name="application-insights-component-contributor"></a>Application Insights inzender voor onderdelen

Kan de Application Insights beheren [Meer informatie](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klassieke waarschuwingsregels maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/generateLiveToken/read | Get-token voor live metrische gegevens |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | Nieuwe waarschuwingsregels maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Insights-onderdelen maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/topology/read | Topologie lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/transactions/read | Transacties lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Insights-webtests maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee worden de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik opgeslagen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/generateLiveToken/read",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/topology/read",
        "Microsoft.Insights/transactions/read",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger

Geeft de gebruiker toestemming om momentopnamen voor foutopsporing weer te geven en te downloaden die zijn verzameld met de Application Insights Snapshot Debugger. Houd er rekening mee dat deze machtigingen niet zijn opgenomen in de [rollen Eigenaar](#owner) [of Inzender.](#contributor) Wanneer u gebruikers de Application Insights Snapshot Debugger geeft, moet u de rol rechtstreeks aan de gebruiker verlenen. De rol wordt niet herkend wanneer deze wordt toegevoegd aan een aangepaste rol. [Meer informatie](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Controlebijdrager

Kan alle bewakingsgegevens lezen en bewakingsinstellingen bewerken. Zie ook [Aan de slag met rollen, machtigingen en beveiliging met Azure Monitor.](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles) [Meer informatie](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */lezen | Lees resources van alle typen, met uitzondering van geheimen. |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alerts/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Insights-onderdelen maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Hiermee maakt, werkt of leest u de diagnostische instelling voor Analyseserver |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/eventtypes/* | Activiteitenlogboekgebeurtenissen (beheergebeurtenissen) in een abonnement op een lijst zetten. Deze machtiging is van toepassing op zowel programmatische als portaltoegang tot het activiteitenlogboek. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | Deze machtiging is nodig voor gebruikers die toegang nodig hebben tot activiteitenlogboeken via de portal. Lijst met logboekcategorieën in activiteitenlogboek. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | Metrische definities lezen (lijst met beschikbare metrische typen voor een resource). |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | Metrische gegevens voor een resource lezen. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | De Microsoft Insights-provider registreren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Insights-webtests maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/write | Hiermee maakt u een nieuwe werkruimte of koppelingen naar een bestaande werkruimte door de klant-id op te geven uit de bestaande werkruimte. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/intelligencepacks/* | Lees-/schrijf-/verwijder-oplossingspakketten voor log analytics. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* | Opgeslagen zoekopdrachten in Log Analytics lezen/schrijven/verwijderen. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Hiermee wordt een zoekquery uitgevoerd |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/action | Hiermee haalt u de gedeelde sleutels voor de werkruimte op. Deze sleutels worden gebruikt om Microsoft-agents Operational Insights te verbinden met de werkruimte. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/storageinsightconfigs/* | Log Analytics Storage Insight-configuraties lezen/schrijven/verwijderen. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/monitors/* | Informatie over statusmonitors voor gast-VM's. |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/dataCollectionRules/*",
        "Microsoft.Insights/dataCollectionRuleAssociations/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>Uitgever van metrische gegevens bewaken

Metrische gegevens publiceren in Azure-resources [Meer informatie](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | De Microsoft Insights-provider registreren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | Metrische gegevens schrijven |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Lezer voor bewaking

Kan alle bewakingsgegevens (metrische gegevens, logboeken, enzovoort) lezen. Zie ook [Aan de slag met rollen, machtigingen en beveiliging met Azure Monitor.](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles) [Meer informatie](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */read | Lees alle typen resources, met uitzondering van geheimen. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Hiermee wordt een zoekquery uitgevoerd |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Inzender voor werkmap

Kan gedeelde werkmappen opslaan. [Meer informatie](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/write | Een werkmap maken of bijwerken |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/delete | Een werkmap verwijderen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Een werkmap lezen |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Werkmaplezer

Kan werkmappen lezen. [Meer informatie](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [microsoft.insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Een werkmap lezen |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>Beheer en governance


### <a name="automation-job-operator"></a>Automation-taakoperator

Taken maken en beheren met Automation-runbooks. [Meer informatie](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Leest Hybrid Runbook Worker resources |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Haalt een Azure Automation op |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Hervat een Azure Automation taak |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Stopt een Azure Automation taak |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Haalt een Azure Automation taakstroom op |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Ondersort een Azure Automation taak |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Hiermee maakt u Azure Automation taak |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | Haalt de uitvoer van een taak op |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Automation-operator

Automation-operators kunnen taken starten, stoppen, opschorten en hervatten [Meer informatie](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Leest Hybrid Runbook Worker resources |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Haalt een Azure Automation op |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Hervat een Azure Automation taak |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Stopt een Azure Automation taak |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Haalt een Azure Automation taakstroom op |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Een Azure Automation opschorten |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Hiermee maakt u Azure Automation taak |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/read | Haalt een Azure Automation taakplanning op |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/write | Hiermee maakt u Azure Automation taakplanning |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/linkedWorkspace/read | Haalt de werkruimte op die is gekoppeld aan het Automation-account |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/read | Haalt een Azure Automation-account op |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Haalt een Azure Automation runbook op |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/read | Haalt een Azure Automation-asset op |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/write | Hiermee maakt of werkt u een Azure Automation asset |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | Haalt de uitvoer van een taak op |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Automation-runbookoperator

Runbookeigenschappen lezen: om taken van het runbook te kunnen maken. [Meer informatie](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Haalt een Azure Automation runbook op |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-enabled-kubernetes-cluster-user-role"></a>Azure Arc kubernetes-clustergebruikersrol ingeschakeld

Lijst met gebruikersreferenties voor cluster.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Hiermee maakt of werkt u een implementatie bij. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Haal de resultaten van de abonnementsbewerking op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Haalt de lijst met abonnementen op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/listClusterUserCredentials/action | Clusterreferenties van gebruiker opsommen |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credentials action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00493d72-78f6-4148-b6c5-d3ce8e4799dd",
  "name": "00493d72-78f6-4148-b6c5-d3ce8e4799dd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/listClusterUserCredentials/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Enabled Kubernetes Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-admin"></a>Azure Arc Kubernetes-beheerder

Hiermee kunt u alle resources onder cluster/naamruimte beheren, behalve resourcequota en naamruimten bijwerken of verwijderen. [Meer informatie](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Hiermee maakt of werkt u een implementatie bij. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Haal de resultaten van de abonnementsbewerking op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Haalt de lijst met abonnementen op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Controllerrevisions lezen |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/authorization.k8s.io/reviewubjectaccessreviews/write | Schrijft writesubjectaccessreviews |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Leest gebeurtenissen |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Leest gebeurtenissen |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Leeslimieten |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Naamruimten lezen |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/rbac.authorization.k8s.io/rolebindings/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/rbac.authorization.k8s.io/roles/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Leest resourcequotas |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/secrets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dffb1e0c-446f-4dde-a09f-99eb5cc68b96",
  "name": "dffb1e0c-446f-4dde-a09f-99eb5cc68b96",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/*",
        "Microsoft.Kubernetes/connectedClusters/authorization.k8s.io/localsubjectaccessreviews/write",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/*",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/*",
        "Microsoft.Kubernetes/connectedClusters/configmaps/*",
        "Microsoft.Kubernetes/connectedClusters/endpoints/*",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/*",
        "Microsoft.Kubernetes/connectedClusters/pods/*",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/rolebindings/*",
        "Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/secrets/*",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/*",
        "Microsoft.Kubernetes/connectedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-cluster-admin"></a>Azure Arc Kubernetes-clusterbeheerder

Hiermee kunt u alle resources in het cluster beheren. [Meer informatie](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Hiermee maakt of werkt u een implementatie bij. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Haal de resultaten van de abonnementsbewerking op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Haalt de lijst met abonnementen op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8393591c-06b9-48a2-a542-1bd6b377f6a2",
  "name": "8393591c-06b9-48a2-a542-1bd6b377f6a2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-viewer"></a>Azure Arc Kubernetes-viewer

Hiermee kunt u alle resources in het cluster/de naamruimte weergeven, met uitzondering van geheimen. [Meer informatie](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Hiermee maakt of werkt u een implementatie bij. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Haal de resultaten van de abonnementsbewerking op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Haalt de lijst met abonnementen op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Controllerrevisions lezen |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/read | Leest daemonsets |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/read | Leest implementaties |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/read | Replicasets lezen |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/read | Leest statefulsets |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/read | Leest horizontalpodautoscalers |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/read | Cronjobs lezen |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/read | Leest taken |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/read | Leest configmaps |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/read | Leest eindpunten |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Gebeurtenissen lezen |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Gebeurtenissen lezen |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/read | Daemonsets lezen |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/read | Leest implementaties |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/read | Leest ingresses |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/read | Leest netwerkbeleid |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/read | Replicasets lezen |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Leeslimieten |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Naamruimten lezen |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/read | Leest ingresses |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/read | Leest netwerkbeleid |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/read | Leest persistentvolumeclaims |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/read | Pods lezen |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/read | Leest poddisruptionbudgets |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/read | Leest replicatiecontrollers |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/read | Leest replicatiecontrollers |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Leest resourcequotas |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/read | Leest serviceaccounts |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/read | Leest services |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view all resources in cluster/namespace, except secrets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/63f0a09d-1495-4db4-a681-037d84835eb4",
  "name": "63f0a09d-1495-4db4-a681-037d84835eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/read",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/read",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/read",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/read",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/read",
        "Microsoft.Kubernetes/connectedClusters/configmaps/read",
        "Microsoft.Kubernetes/connectedClusters/endpoints/read",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/read",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/read",
        "Microsoft.Kubernetes/connectedClusters/pods/read",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/read",
        "Microsoft.Kubernetes/connectedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Viewer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-writer"></a>Azure Arc Kubernetes Writer

Hiermee kunt u alles bijwerken in cluster/naamruimte, behalve (cluster)rollen en (cluster)rolbindingen. [Meer informatie](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Hiermee maakt of werkt u een implementatie bij. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Haal de resultaten van de abonnementsbewerking op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Haalt de lijst met abonnementen op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Controllerrevisions lezen |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Leest gebeurtenissen |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Leest gebeurtenissen |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Leest limieten |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Naamruimten lezen |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Leest resourcequotas |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/secrets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you update everything in cluster/namespace, except (cluster)roles and (cluster)role bindings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5b999177-9696-4545-85c7-50de3797e5a1",
  "name": "5b999177-9696-4545-85c7-50de3797e5a1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/*",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/*",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/*",
        "Microsoft.Kubernetes/connectedClusters/configmaps/*",
        "Microsoft.Kubernetes/connectedClusters/endpoints/*",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/*",
        "Microsoft.Kubernetes/connectedClusters/pods/*",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/secrets/*",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/*",
        "Microsoft.Kubernetes/connectedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Azure Connected Machine Onboarding

Kan onboarding uitvoeren voor Azure Connected Machines. [Meer informatie](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Lees alle Azure Arc machines |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Schrijft een Azure Arc machines |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/privateLinkScopes/read | Lees alle Azure Arc privateLinkScopes |
> | [Microsoft.GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/read | Gastconfiguratietoewijzing krijgen. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/privateLinkScopes/read",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Azure Connected Machine resourcebeheerder

Kan Azure Connected Machines lezen, schrijven, verwijderen en opnieuw onboarden.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Lees alle Azure Arc machines |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Schrijft een Azure Arc machines |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/delete | Hiermee verwijdert u een Azure Arc machines |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Installeert of werkt een Azure Arc-extensies |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/privateLinkScopes/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/privateLinkScopes/*",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Lezer voor facturering

Leestoegang tot factureringsgegevens verlenen [Meer informatie](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/*/read | Factureringsgegevens lezen |
> | [Microsoft.Commerce](resource-provider-operations.md#microsoftcommerce)/*/read |  |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lijst met beheergroepen voor de geverifieerde gebruiker. |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>Blauwdrukinzender

Blauwdrukinzenders kunnen blauwdrukdefinities beheren, maar deze niet toewijzen. [Meer informatie](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprints/* | Blauwdrukdefinities of blauwdrukartefacten maken en beheren. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>Blauwdrukoperator

Kan bestaande gepubliceerde blauwdrukken toewijzen, maar kan geen nieuwe blauwdrukken maken. Houd er rekening mee dat dit alleen werkt als de toewijzing wordt uitgevoerd met een door de gebruiker toegewezen beheerde identiteit. [Meer informatie](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | Blauwdruktoewijzingen maken en beheren. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Cost Management-inzender

Kan kosten bekijken en kostenconfiguratie beheren (bijvoorbeeld budgetten, exports) [Meer informatie](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Haalt de lijst met abonnementen op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | Configuraties krijgen |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | Aanbevelingen lezen |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lijst met beheergroepen voor de geverifieerde gebruiker. |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingProperty/read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Cost Management Reader

Kan kostengegevens en -configuratie weergeven (bijvoorbeeld budgetten, exports) [Meer informatie](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Haalt de lijst met abonnementen op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | Configuraties krijgen |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | Leest aanbevelingen |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lijst met beheergroepen voor de geverifieerde gebruiker. |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingProperty/read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hierarchy-settings-administrator"></a>Beheerder hiërarchie-instellingen

Hiermee kunnen gebruikers hiërarchie-instellingen bewerken en verwijderen

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/write | Hiermee worden hiërarchie-instellingen voor beheergroep gemaakt of bijgewerkt. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/delete | Hiermee verwijdert u instellingen voor de hiërarchie van beheergroep. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Kubernetes-cluster - Azure Arc onboarding

Roldefinitie voor het autoreren van elke gebruiker/service om connectedClusters-resource te maken [Meer informatie](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Hiermee maakt of werkt u een implementatie bij. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Haal de resultaten van de abonnementsbewerking op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Haalt de lijst met abonnementen op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | Schrijft verbondenClusters |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/read | ConnectedClusters lezen |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role definition to authorize any user/service to create connectedClusters resource",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "name": "34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/Write",
        "Microsoft.Kubernetes/connectedClusters/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Cluster - Azure Arc Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Rol inzender voor beheerde toepassingen

Hiermee kunt u beheerde toepassingsbronnen maken.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */read | Lees alle typen resources, met uitzondering van geheimen. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/* |  |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/register/action | Registreer u bij Solutions. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Operatorrol voor beheerde toepassingen

Hiermee kunt u acties lezen en uitvoeren op resources van beheerde toepassingen

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */lezen | Lees resources van alle typen, met uitzondering van geheimen. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/read | Hiermee haalt u een lijst met toepassingen op. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/*/action |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Lezer van beheerde toepassingen

Hiermee kunt u resources in een beheerde app lezen en JIT-toegang aanvragen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */lezen | Lees resources van alle typen, met uitzondering van geheimen. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>Rol registratietoewijzing beheerde services verwijderen

Met de rol Registratietoewijzing beheerde services kunnen tenantgebruikers die beheren de registratietoewijzing verwijderen die is toegewezen aan hun tenant. [Meer informatie](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/read | Hiermee haalt u een lijst met registratietoewijzingen van Managed Services op. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/delete | Hiermee verwijdert u registratietoewijzing voor Beheerde services. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/read | Leest de bewerkingsstatus voor de resource. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>Inzender voor beheergroep

Rol Inzender [beheergroep Meer informatie](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/delete | Beheergroep verwijderen. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lijst met beheergroepen voor de geverifieerde gebruiker. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/delete | Koppel het abonnement uit de beheergroep. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/write | Koppelt een bestaand abonnement aan de beheergroep. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/write | Maak of werk een beheergroep bij. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | Geeft een abonnement weer onder de opgegeven beheergroep. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>Lezer van beheergroep

Rol van lezer van beheergroep

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lijst met beheergroepen voor de geverifieerde gebruiker. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | Geeft een abonnement weer onder de opgegeven beheergroep. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>New Relic APM-account inzender

Hiermee kunt u New Relic Application Performance Management accounts en toepassingen beheren, maar geen toegang tot deze accounts.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee worden de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik opgeslagen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Policy Insights Data Writer (preview)

Hiermee staat u leestoegang toe tot resourcebeleid en schrijftoegang tot beleidsgebeurtenissen voor resourcecomponenten. [Meer informatie](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/read | Informatie over een beleidstoewijzing. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/read | Informatie over een beleidsdefinitie op te halen. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/read | Informatie over een beleidsvrijstelling. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/read | Informatie over een beleidssetdefinitie. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/action | Controleer de nalevingsstatus van een bepaald onderdeel op gegevensbeleid. |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/action | Registreer de beleidsgebeurtenissen voor resourcecomponenten. |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policyexemptions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="quota-request-operator"></a>Operator voor quotumaanvraag

Lees en maak quotumaanvragen, haal de status van de quotumaanvraag op en maak ondersteuningstickets. [Meer informatie](/rest/api/reserved-vm-instances/quotaapi)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimits/read | De huidige servicelimiet of het huidige quotum van de opgegeven resource en locatie op te halen |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimits/write | Servicelimiet of quotum maken voor de opgegeven resource en locatie |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimitsRequests/read | Een aanvraag voor servicelimieten voor de opgegeven resource en locatie op te halen |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/register/action | Registreert de capaciteitsresourceprovider en maakt het maken van capaciteitsresources mogelijk. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and create quota requests, get quota request status, and create support tickets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0e5f05e5-9ab9-446b-b98d-1e2157c94125",
  "name": "0e5f05e5-9ab9-446b-b98d-1e2157c94125",
  "permissions": [
    {
      "actions": [
        "Microsoft.Capacity/resourceProviders/locations/serviceLimits/read",
        "Microsoft.Capacity/resourceProviders/locations/serviceLimits/write",
        "Microsoft.Capacity/resourceProviders/locations/serviceLimitsRequests/read",
        "Microsoft.Capacity/register/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Quota Request Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reservation-purchaser"></a>Reserveringsinkoper

Hiermee kunt u reserveringen aanschaffen [Meer informatie](../cost-management-billing/reservations/prepare-buy-reservation.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Haalt de lijst met abonnementen op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/register/action | Registreert de capaciteitsresourceprovider en maakt het maken van capaciteitsresources mogelijk. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/register/action | Registreert abonnement bij Microsoft.Compute-resourceprovider |
> | [Microsoft.SQL](resource-provider-operations.md#microsoftsql)/register/action | Registreert het abonnement voor de Microsoft SQL Database resourceprovider en maakt het maken van Microsoft SQL Databases mogelijk. |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/register/action | Registreren bij Verbruiks-RP |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/catalogs/read | Catalogus van reservering lezen |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Informatie over een roltoewijzing. |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/reservationRecommendations/read | Een lijst met enkele of gedeelde aanbevelingen voor gereserveerde instanties voor een abonnement. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/supporttickets/write | Hiermee kunt u een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you purchase reservations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "name": "f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Capacity/register/action",
        "Microsoft.Compute/register/action",
        "Microsoft.SQL/register/action",
        "Microsoft.Consumption/register/action",
        "Microsoft.Capacity/catalogs/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Consumption/reservationRecommendations/read",
        "Microsoft.Support/supporttickets/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reservation Purchaser",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Inzender voor resourcebeleid

Gebruikers met rechten voor het maken/wijzigen van resourcebeleid, het maken van een ondersteuningsticket en het lezen van resources/hiërarchie. [Meer informatie](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */lezen | Lees resources van alle typen, met uitzondering van geheimen. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | Beleidstoewijzingen maken en beheren |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/* | Beleidsdefinities maken en beheren |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/* | Beleidsvrijstellingen maken en beheren |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | Beleidssets maken en beheren |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policyexemptions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Site Recovery-inzender

Hiermee kunt u een Site Recovery beheren, behalve het maken van een kluis en roltoewijzing [Meer informatie](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | De definitie van het virtuele netwerk op halen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp is een interne bewerking die wordt gebruikt door de service |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | Met de bewerking Resourcecertificaat bijwerken wordt het referentiecertificaat van de resource/kluis bijgewerkt. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Uitgebreide informatie met betrekking tot de kluis maken en beheren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Met de get vault-bewerking wordt een object op de azure-resource van het type 'kluis' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Geregistreerde identiteiten maken en beheren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/* | Waarschuwingsinstellingen voor replicatie maken of bijwerken |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Alle gebeurtenissen lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/* | Replicatie-fabrics maken en beheren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Replicatietaken maken en beheren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/* | Replicatiebeleid maken en beheren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/* | Herstelplannen maken en beheren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | Opslagconfiguratie van Recovery Services-kluis maken en beheren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Retourneert gebruiksgegevens voor een Recovery Services-kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | De kluis-tokenbewerking kan worden gebruikt om kluis-token op te halen voor back-endbewerkingen op kluisniveau. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Waarschuwingen voor de Recovery Services-kluis lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Retourneert de lijst met opslagaccounts of haalt de eigenschappen voor het opgegeven opslagaccount op. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationOperationStatus/read | Alle kluisreplicatiebewerkingsstatussen lezen |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Site Recovery-operator

Hiermee kunt u failover en failback uitvoeren, maar geen andere beheerbewerkingen Site Recovery [meer informatie](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | De definitie van het virtuele netwerk op te halen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp is een interne bewerking die wordt gebruikt door de service |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | Met de bewerking Uitgebreide informatie op halen haalt u de uitgebreide informatie van een object op die de Azure-resource van het type ?vault vertegenwoordigt? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Met de get vault-bewerking wordt een object op de azure-resource van het type 'kluis' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | De bewerking Bewerkingsresultaten op halen kan worden gebruikt om de bewerkingsstatus en het resultaat voor de asynchroon verzonden bewerking op te halen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | De bewerking Containers op halen kan worden gebruikt om de containers op te halen die zijn geregistreerd voor een resource. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | Eventuele waarschuwingsinstellingen lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Alle gebeurtenissen lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/checkConsistency/action | Controleert de consistentie van de fabric |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | Alle fabrics lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/reassociateGateway/action | Gateway opnieuw koppelen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/renewcertificate/action | Certificaat vernieuwen voor Fabric |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | Alle netwerken lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Alle netwerktoewijzingen lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | Alle beveiligingscontainers lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Lees alle beveiligende items |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Herstelpunt toepassen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Failover-door commit |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Geplande failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Lees alle beveiligde items |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Lees alle replicatieherstelpunten |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Replicatie herstellen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Beveiligd item opnieuw veiligen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Beveiliging van container wisselen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Failover testen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Failover opschonen testen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Mobility-service bijwerken |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Lees eventuele beveiligingscontainertoewijzingen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Alle Recovery Services-providers lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Provider vernieuwen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | Opslagclassificaties lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Opslagclassificatietoewijzingen lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | Lees alle vCenters |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Replicatietaken maken en beheren |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | Alle beleidsregels lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/failoverCommit/action | Herstelplan voor failover commit |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/plannedFailover/action | Herstelplan voor geplande failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | Herstelplannen lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/reProtect/action | Herstelplan opnieuw protect |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailover/action | Failoverherstelplan testen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Herstelplan voor het opschonen van failover testen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/unplannedFailover/action | Failoverherstelplan |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Waarschuwingen voor de Recovery Services-kluis lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Retourneert gebruiksgegevens voor een Recovery Services-kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | De kluis-tokenbewerking kan worden gebruikt om kluis-token op te halen voor back-endbewerkingen op kluisniveau. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee haalt u de beschikbaarheidsstatussen op voor alle resources in het opgegeven bereik |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Retourneert de lijst met opslagaccounts of haalt de eigenschappen voor het opgegeven opslagaccount op. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Site Recovery-lezer

Hiermee kunt u de Site Recovery weergeven, maar geen andere beheerbewerkingen uitvoeren [Meer informatie](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | Met de bewerking Uitgebreide informatie op halen haalt u de uitgebreide informatie van een object op die de Azure-resource van het type ?vault vertegenwoordigt? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Haalt de waarschuwingen voor de Recovery Services-kluis op. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Met de get vault-bewerking wordt een object op de azure-resource van het type 'kluis' |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | De bewerking Bewerkingsresultaten op halen kan worden gebruikt om de bewerkingsstatus en het resultaat voor de asynchroon verzonden bewerking op te halen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | De bewerking Containers get kan worden gebruikt om de containers op te halen die zijn geregistreerd voor een resource. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | Alle instellingen voor waarschuwingen lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Alle gebeurtenissen lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | Alle fabrics lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | Alle netwerken lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Alle netwerktoewijzingen lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | Alle beveiligingscontainers lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Lees alle beveiligende items |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Lees alle beveiligde items |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Alle replicatieherstelpunten lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Lees eventuele beveiligingscontainertoewijzingen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Alle Recovery Services-providers lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | Opslagclassificaties lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Opslagclassificatietoewijzingen lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | Lees alle vCenters |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/read | Alle taken lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | Alle beleidsregels lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | Herstelplannen lezen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Retourneert gebruiksgegevens voor een Recovery Services-kluis. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | De kluis-tokenbewerking kan worden gebruikt om een kluis-token op te halen voor back-endbewerkingen op kluisniveau. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Inzender voor ondersteuningsaanvraag

Hiermee kunt u ondersteuningsaanvragen maken en beheren [Meer informatie](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>Tag-inzender

Hiermee kunt u tags voor entiteiten beheren zonder toegang te verlenen tot de entiteiten zelf. [Meer informatie](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | Haalt de resources voor de resourcegroep op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resources/read | Haalt resources van een abonnement op. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/tags/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Anders


### <a name="azure-digital-twins-data-owner"></a>Azure Digital Twins gegevenseigenaar

Volledige toegangsrol voor Digital Twins gegevensvlak [Meer informatie](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/* | Een gebeurtenisroute lezen, verwijderen, maken of bijwerken |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/* | Digital Twin lezen, maken, bijwerken of verwijderen |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/commands/* | Een opdracht aanroepen op een Digital Twin |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/* | Een Digital Twin-relatie lezen, maken, bijwerken of verwijderen |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/* | Een model lezen, maken, bijwerken of verwijderen |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/* | Een query uitvoeren op Digital Twins Graph |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access role for Digital Twins data-plane",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "name": "bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/eventroutes/*",
        "Microsoft.DigitalTwins/digitaltwins/*",
        "Microsoft.DigitalTwins/digitaltwins/commands/*",
        "Microsoft.DigitalTwins/digitaltwins/relationships/*",
        "Microsoft.DigitalTwins/models/*",
        "Microsoft.DigitalTwins/query/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-digital-twins-data-reader"></a>Azure Digital Twins-gegevenslezer

Alleen-lezenrol voor Digital Twins eigenschappen van het gegevensvlak [Meer informatie](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/read | Een Digital Twin lezen |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/read | Een Digital Twin-relatie lezen |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/read | Een gebeurtenisroute lezen |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/read | Elk model lezen |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/action | Een query uitvoeren op Digital Twins Graph |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only role for Digital Twins data-plane properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "name": "d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/digitaltwins/read",
        "Microsoft.DigitalTwins/digitaltwins/relationships/read",
        "Microsoft.DigitalTwins/eventroutes/read",
        "Microsoft.DigitalTwins/models/read",
        "Microsoft.DigitalTwins/query/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="biztalk-contributor"></a>BizTalk Contributor

Hiermee kunt u BizTalk Services beheren, maar geen toegang krijgen tot deze services.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | Microsoft.BizTalkServices/BizTalk/* | BizTalk Services maken en beheren |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee worden de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik opgeslagen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-application-group-contributor"></a>Inzender voor bureaubladvirtualisatietoepassingsgroep

Inzender van de bureaubladvirtualisatietoepassingsgroep. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/* |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Hostpools lezen |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Hostpools/sessionhosts lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86240b0e-9422-4c43-887b-b61143f32ba8",
  "name": "86240b0e-9422-4c43-887b-b61143f32ba8",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-application-group-reader"></a>Lezer voor bureaubladvirtualisatietoepassingsgroep

Lezer van de bureaubladvirtualisatietoepassingsgroep. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/*/read |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Toepassingsgroepen lezen |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Hostpools lezen |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Hostpools/sessionhosts lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Haalt implementaties op of vermeldt deze. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Een klassieke waarschuwing voor metrische gegevens lezen |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "name": "aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-contributor"></a>Inzender voor bureaubladvirtualisatie

Inzender van desktopvirtualisatie. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/082f0a83-3be5-4ba1-904c-961cca79b387",
  "name": "082f0a83-3be5-4ba1-904c-961cca79b387",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-contributor"></a>Inzender voor bureaubladvirtualisatiehostgroep

Inzender van de bureaubladvirtualisatiehostgroep. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "name": "e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-reader"></a>Lezer hostgroep bureaubladvirtualisatie

Lezer van de hostgroep voor bureaubladvirtualisatie. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/*/read |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Hostpools lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Haalt implementaties op of vermeldt deze. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Een klassieke waarschuwing voor metrische gegevens lezen |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ceadfde2-b300-400a-ab7b-6143895aa822",
  "name": "ceadfde2-b300-400a-ab7b-6143895aa822",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-reader"></a>Lezer voor bureaubladvirtualisatie

Lezer van bureaubladvirtualisatie. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Haalt implementaties op of vermeldt deze. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Een klassieke waarschuwing voor metrische gegevens lezen |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49a72310-ab8d-41df-bbb0-79b649203868",
  "name": "49a72310-ab8d-41df-bbb0-79b649203868",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-session-host-operator"></a>Hostoperator voor bureaubladvirtualisatiesessie

Operator van de Bureaubladvirtualisatiesessiehost. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Hostpools lezen |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Operator of the Desktop Virtualization Session Host.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "name": "2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Session Host Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user"></a>Bureaubladvirtualisatiegebruiker

Hiermee staat u de gebruiker toe de toepassingen in een toepassingsgroep te gebruiken. [Meer informatie](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationGroups/useApplications/action | ApplicationGroup gebruiken |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows user to use the applications in an application group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "name": "1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DesktopVirtualization/applicationGroups/useApplications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user-session-operator"></a>Gebruikerssessieoperator voor bureaubladvirtualisatie

Operator van de Uesr-sessie voor bureaubladvirtualisatie. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Hostpools lezen |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Hostpools/sessionhosts lezen |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/usersessions/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Operator of the Desktop Virtualization Uesr Session.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "name": "ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User Session Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-contributor"></a>Inzender voor bureaubladvirtualisatiewerkruimte

Inzender van de werkruimte Bureaubladvirtualisatie. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/workspaces/* |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Toepassingsgroepen lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "name": "21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/*",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-reader"></a>Werkruimtelezer voor bureaubladvirtualisatie

Lezer van de werkruimte Bureaubladvirtualisatie. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/workspaces/read | Werkruimten lezen |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Toepassingsgroepen lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Haalt implementaties op of vermeldt deze. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Een klassieke waarschuwing voor metrische gegevens lezen |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "name": "0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-backup-reader"></a>Schijfback-uplezer

Geeft toestemming om een back-up van de back-up van de schijf te maken. [Meer informatie](../backup/disk-backup-faq.yml)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | De eigenschappen van een schijf op te halen |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/action | De SAS-URI van de schijf voor blobtoegang op halen |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to perform disk backup.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "name": "3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/beginGetAccess/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-restore-operator"></a>Operator voor schijfherstel

Biedt machtigingen voor het maken van een back-upkluis om schijfherstel uit te voeren. [Meer informatie](../backup/restore-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | Hiermee maakt u een nieuwe schijf of werkt u een bestaande schijf bij |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | De eigenschappen van een schijf op te halen |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to perform disk restore.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b50d9833-a0cb-478e-945f-707fcc997c13",
  "name": "b50d9833-a0cb-478e-945f-707fcc997c13",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Restore Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-snapshot-contributor"></a>Inzender voor schijfmomentopnamen

Biedt machtigingen voor back-upkluis voor het beheren van schijfmomentopnamen. [Meer informatie](../backup/backup-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/delete | Een momentopname verwijderen |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/write | Een nieuwe momentopname maken of een bestaande momentopname bijwerken |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/read | De eigenschappen van een momentopname op halen |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/beginGetAccess/action | De SAS-URI van de momentopname voor blob-toegang op halen |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/endGetAccess/action | De SAS-URI van de momentopname intrekken |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/action | De SAS-URI van de schijf voor blobtoegang op halen |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | Retourneert de toegangssleutels voor het opgegeven opslagaccount. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/write | Hiermee maakt u een opslagaccount met de opgegeven parameters of werk u de eigenschappen of tags bij of voegt u een aangepast domein toe voor het opgegeven opslagaccount. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Retourneert de lijst met opslagaccounts of haalt de eigenschappen voor het opgegeven opslagaccount op. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/delete | Hiermee verwijdert u een bestaand opslagaccount. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to manage disk snapshots.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "name": "7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/snapshots/delete",
        "Microsoft.Compute/snapshots/write",
        "Microsoft.Compute/snapshots/read",
        "Microsoft.Compute/snapshots/beginGetAccess/action",
        "Microsoft.Compute/snapshots/endGetAccess/action",
        "Microsoft.Compute/disks/beginGetAccess/action",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/write",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Snapshot Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Inzender voor Scheduler-taakverzamelingen

Hiermee kunt u Scheduler-taakverzamelingen beheren, maar geen toegang tot deze verzamelingen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Hiermee worden de beschikbaarheidsstatussen voor alle resources in het opgegeven bereik opgeslagen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | Taakverzamelingen maken en beheren |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Een ondersteuningsticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="services-hub-operator"></a>Services Hub Operator

Met Services Hub Operator kunt u alle lees-, schrijf- en verwijderingsbewerkingen uitvoeren die betrekking hebben op Services Hub-connectors. [Meer informatie](/services-hub/health/sh-connector-roles)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Rollen en roltoewijzingen lezen |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Hiermee haalt u resourcegroepen op of vermeldt u deze. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Een implementatie maken en beheren |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/write | Een Services Hub-connector maken of bijwerken |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/read | Services Hub-connectors weergeven of weergeven |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/delete | Services Hub-connectors verwijderen |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/checkAssessmentEntitlement/action | Hiermee worden de evaluatierechten voor een bepaalde Services Hub-werkruimte vermeld |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/supportOfferingEntitlement/read | Bekijk de rechten voor ondersteuningsaanbiedingen voor een bepaalde Services Hub-werkruimte |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/workspaces/read | De Services Hub-werkruimten voor een bepaalde gebruiker in een lijst zetten |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | *geen* |  |
> | **NotDataActions** |  |
> | *geen* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Services Hub Operator allows you to perform all read, write, and deletion operations related to Services Hub Connectors.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/82200a5b-e217-47a5-b665-6d8765ee745b",
  "name": "82200a5b-e217-47a5-b665-6d8765ee745b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.ServicesHub/connectors/write",
        "Microsoft.ServicesHub/connectors/read",
        "Microsoft.ServicesHub/connectors/delete",
        "Microsoft.ServicesHub/connectors/checkAssessmentEntitlement/action",
        "Microsoft.ServicesHub/supportOfferingEntitlement/read",
        "Microsoft.ServicesHub/workspaces/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Services Hub Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Volgende stappen

- [Azure-rollen toewijzen met behulp van de Azure Portal](role-assignments-portal.md)
- [Aangepaste Azure-rollen](custom-roles.md)
- [Machtigingen in Azure Security Center](../security-center/security-center-permissions.md)
