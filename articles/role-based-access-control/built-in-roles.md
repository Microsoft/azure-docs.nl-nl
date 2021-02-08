---
title: Ingebouwde rollen van Azure-Azure RBAC
description: In dit artikel worden de ingebouwde rollen van Azure beschreven voor op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure. De lijst bevat de acties, intact, DataActions en NotDataActions.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 02/01/2021
ms.custom: generated
ms.openlocfilehash: 384d00ee41f2b6bfc2e91815bfcf54819c7d9ab2
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809370"
---
# <a name="azure-built-in-roles"></a>Ingebouwde Azure-rollen

[Toegangs beheer op basis van rollen (Azure RBAC) van](overview.md) Azure heeft verschillende ingebouwde rollen van Azure die u kunt toewijzen aan gebruikers, groepen, service-principals en beheerde identiteiten. Roltoewijzingen zijn de manier waarop u de toegang tot Azure-resources beheert. Als de ingebouwde rollen niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen [aangepaste Azure-rollen](custom-roles.md) maken.

Dit artikel bevat een overzicht van de ingebouwde rollen van Azure, die altijd in ontwikkeling zijn. Gebruik de lijst met [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) of [AZ Role definition](/cli/azure/role/definition#az-role-definition-list)om de nieuwste rollen op te halen. Als u op zoek bent naar beheerders rollen voor Azure Active Directory (Azure AD), raadpleegt u [machtigingen voor beheerdersrol in azure Active Directory](../active-directory/roles/permissions-reference.md).

De volgende tabel bevat een korte beschrijving en de unieke ID van elke ingebouwde rol. Klik op de naam van de rol om de lijst met `Actions` ,, `NotActions` `DataActions` en `NotDataActions` voor elke rol weer te geven. Zie [begrippen van Azure-functies begrijpen](role-definitions.md)voor informatie over wat deze acties betekenen en hoe deze van toepassing zijn op het beheer-en gegevens abonnement.

## <a name="all"></a>Alles

> [!div class="mx-tableFixed"]
> | Ingebouwde rol | Beschrijving | Id |
> | --- | --- | --- |
> | **Algemeen** |  |  |
> | [Inzender](#contributor) | Verleent volledige toegang om alle resources te beheren, maar biedt geen ondersteuning voor het toewijzen van rollen in azure RBAC, het beheren van toewijzingen in azure-blauw drukken of het delen van afbeeldings galerieën. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Eigenaar](#owner) | Hiermee wordt volledige toegang verleend voor het beheren van alle resources, inclusief de mogelijkheid om rollen toe te wijzen in azure RBAC. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Lezer](#reader) | Bekijk alle resources, maar u kunt geen wijzigingen aanbrengen. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Beheerder van gebruikerstoegang](#user-access-administrator) | Hiermee beheert u de gebruikers toegang tot Azure-resources. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Compute** |  |  |
> | [Inzender voor klassieke virtuele machines](#classic-virtual-machine-contributor) | Hiermee beheert u klassieke virtuele machines, maar kunt u niet de toegang tot ze en niet het virtuele netwerk of opslag account waarmee ze zijn verbonden. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Aanmelding voor de beheerder van de virtuele machine](#virtual-machine-administrator-login) | Virtual Machines in de portal weer geven en u aanmelden als beheerder | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Inzender voor virtuele machines](#virtual-machine-contributor) | Met kunt u virtuele machines beheren, maar niet de toegang tot ze en niet het virtuele netwerk of opslag account waarmee ze zijn verbonden. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Gebruikers aanmelding voor de virtuele machine](#virtual-machine-user-login) | Bekijk Virtual Machines in de portal en meld u aan als een gewone gebruiker. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Netwerken** |  |  |
> | [Inzender voor CDN-eind punten](#cdn-endpoint-contributor) | Kan CDN-eind punten beheren, maar kan geen toegang verlenen aan andere gebruikers. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN-eindpunt lezer](#cdn-endpoint-reader) | Kan CDN-eind punten weer geven, maar kan geen wijzigingen aanbrengen. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Inzender voor CDN-profiel](#cdn-profile-contributor) | Kan CDN-profielen en de bijbehorende eind punten beheren, maar kan geen toegang verlenen aan andere gebruikers. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN-profiel lezer](#cdn-profile-reader) | Kan CDN-profielen en de bijbehorende eind punten weer geven, maar kan geen wijzigingen aanbrengen. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Inzender voor klassieke netwerken](#classic-network-contributor) | Hiermee beheert u klassieke netwerken, maar hebt u geen toegang tot de netwerk bestanden. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Inzender DNS-zone](#dns-zone-contributor) | Met kunt u DNS-zones en-record sets beheren in Azure DNS, maar kunt u niet bepalen wie toegang heeft tot de groepen. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Inzender voor netwerken](#network-contributor) | Hiermee kunt u netwerken beheren, maar niet de toegang tot de netwerk bestanden. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Inzender voor Privé-DNS-zone](#private-dns-zone-contributor) | Hiermee kunt u privé-DNS-zone bronnen beheren, maar niet de virtuele netwerken waaraan ze zijn gekoppeld. | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Inzender Traffic Manager](#traffic-manager-contributor) | Hiermee beheert u Traffic Manager profielen, maar kunt u niet bepalen wie er toegang tot heeft. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [AVERE-bijdrager](#avere-contributor) | Kan een avere vFXT-cluster maken en beheren. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [AVERE-operator](#avere-operator) | Wordt gebruikt door het avere vFXT-cluster voor het beheren van het cluster | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Back-upinzender](#backup-contributor) | Hiermee kunt u de back-upservice beheren, maar geen kluizen maken en anderen toegang verlenen | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Back-upoperator](#backup-operator) | Hiermee kunt u back-upservices beheren, behalve het verwijderen van back-ups, het maken van een kluis en het verlenen van toegang | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Back-uplezer](#backup-reader) | Kan back-upservices weer geven, maar kan geen wijzigingen aanbrengen | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Inzender voor klassieke opslag accounts](#classic-storage-account-contributor) | Hiermee kunt u klassieke opslag accounts beheren, maar niet de toegang tot de account. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Service functie voor de sleutel operator voor klassieke opslag accounts](#classic-storage-account-key-operator-service-role) | De sleutel operators voor klassieke opslag accounts zijn toegestaan om sleutels weer te geven en opnieuw te genereren voor klassieke opslag. | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Inzender Data Box](#data-box-contributor) | Hiermee kunt u alles beheren onder Data Box Service, behalve dat anderen toegang verlenen. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Data Box lezer](#data-box-reader) | Met kunt u de Data Box-Service beheren, met uitzonde ring van order gegevens maken of bewerken en anderen toegang verlenen. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Lake Analytics-ontwikkelaar](#data-lake-analytics-developer) | Hiermee kunt u uw eigen taken verzenden, bewaken en beheren, maar geen Data Lake Analytics accounts maken of verwijderen. | 47b7735b-770E-4598-A7DA-8b91488b4c88 |
> | [Lezer en gegevens toegang](#reader-and-data-access) | Hiermee kunt u alles weer geven, maar kunt u geen opslag account of opgenomen resource verwijderen of maken. Ook wordt lees-/schrijftoegang tot alle gegevens in een opslag account toegestaan via toegang tot de sleutel van het opslag account. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Inzender voor opslagaccounts](#storage-account-contributor) | Hiermee staat u het beheer van opslag accounts toe. Biedt toegang tot de account sleutel, die kan worden gebruikt om toegang te krijgen tot gegevens via een gedeelde sleutel autorisatie. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Functie Service-sleutel operator van opslag account](#storage-account-key-operator-service-role) | Kan de toegangs sleutels voor het opslag account weer geven en opnieuw genereren. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Inzender voor Storage Blob-gegevens](#storage-blob-data-contributor) | Azure Storage containers en blobs lezen, schrijven en verwijderen. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Eigenaar van opslagblobgegevens](#storage-blob-data-owner) | Biedt volledige toegang tot Azure Storage BLOB-containers en-gegevens, met inbegrip van het toewijzen van POSIX-toegangs beheer. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Lezer voor opslagblobgegevens](#storage-blob-data-reader) | Azure Storage containers en blobs lezen en weer geven. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Delegering van opslag-BLOB](#storage-blob-delegator) | Een sleutel voor gebruikers overdracht ophalen, die vervolgens kan worden gebruikt om een gedeelde toegangs handtekening te maken voor een container of BLOB die is ondertekend met Azure AD-referenties. Zie [een gebruiker delegering Sa's maken](/rest/api/storageservices/create-user-delegation-sas)voor meer informatie. | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Inzender voor opslagbestandsgegevens via SMB-share](#storage-file-data-smb-share-contributor) | Hiermee wordt lees-, schrijf-en verwijder toegang voor bestanden/mappen in azure-bestands shares toegestaan. Deze rol heeft geen ingebouwd equivalent op Windows-bestands servers. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Inzender met verhoogde bevoegdheden voor opslagbestandsgegevens via SMB-share](#storage-file-data-smb-share-elevated-contributor) | Hiermee kunt u Acl's voor lezen, schrijven, verwijderen en wijzigen van bestanden/mappen in azure-bestands shares. Deze rol is gelijk aan een ACL van de bestands share die moet worden gewijzigd op Windows-bestands servers. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Lezer voor opslagbestandgegevens via SMB-share](#storage-file-data-smb-share-reader) | Hiermee staat u lees toegang toe voor bestanden/mappen in azure-bestands shares. Deze rol is gelijk aan een ACL voor bestands shares van lezen op Windows-bestands servers. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Inzender voor opslag wachtrij gegevens](#storage-queue-data-contributor) | Lees-, schrijf-en verwijder Azure Storage-wacht rijen en-wachtrij berichten. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Processor voor gegevens berichten van de opslag wachtrij](#storage-queue-data-message-processor) | Een bericht uit een Azure Storage wachtrij bekijken, ophalen en verwijderen. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Afzender gegevens bericht van opslag wachtrij](#storage-queue-data-message-sender) | Berichten toevoegen aan een Azure Storage wachtrij. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Gegevens lezer van de opslag wachtrij](#storage-queue-data-reader) | Azure Storage-wacht rijen en-wachtrij berichten lezen en weer geven. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Inzender voor Azure Maps gegevens](#azure-maps-data-contributor) | Hiermee wordt toegang verleend tot lees-, schrijf-en verwijder toegang om gerelateerde gegevens vanuit een Azure Maps-account toe te wijzen. | 8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204 |
> | [Gegevens lezer Azure Maps](#azure-maps-data-reader) | Hiermee wordt toegang verleend om gerelateerde gegevens te lezen vanuit een Azure Maps-account. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Inzender Search Service](#search-service-contributor) | Hiermee kunt u zoek services beheren, maar niet de toegang tot ze. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Signaal/AccessKey-lezer](#signalr-accesskey-reader) | Toegangs sleutels voor de seingevings service lezen | 04165923-9d83-45d5-8227-78b77b0a687e |
> | [Seingevings app-server (preview-versie)](#signalr-app-server-preview) | Hiermee wordt uw app server-toegangs signaal service met AAD-verificatie opties. | 420fcaa2-552c-430f-98ca-3264be4806c7 |
> | [Inzender bijdrager](#signalr-contributor) | Signalerings service resources maken, lezen, bijwerken en verwijderen | 8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761 |
> | [Inzender server zonder signaal (preview-versie)](#signalr-serverless-contributor-preview) | Hiermee kan uw app Access-service in de serverloze modus met AAD-verificatie opties. | fd53cd77-2268-407a-8f46-7e7863d0f521 |
> | [Eigenaar van de seingevings service (preview-versie)](#signalr-service-owner-preview) | Volledige toegang tot REST Api's van de Azure signalerings service | 7e4f1700-ea5a-4f59-8f37-079cfe29dce3 |
> | [Signaal-service lezer (preview-versie)](#signalr-service-reader-preview) | Alleen-lezen toegang tot REST Api's van de Azure signalerings service | ddde6b66-c0df-4114-a159-3618637b3035 |
> | [Inzender voor webabonnementen](#web-plan-contributor) | Hiermee kunt u de Webabonnementen voor websites beheren, maar niet de toegang tot de abonnementen. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Website bijdrager](#website-contributor) | Hiermee kunt u websites beheren (niet Webabonnementen), maar niet de toegang tot de sites. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Containers** |  |  |
> | [AcrDelete](#acrdelete) | ACR verwijderen | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | ACR-installatie kopie ondertekenaar | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | ACR pull | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | ACR-push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | ACR quarantaine gegevens lezer | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | ACR quarantaine gegevens schrijver | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Rol van Cluster beheerder voor Azure Kubernetes-service](#azure-kubernetes-service-cluster-admin-role) | Lijst met actie voor cluster beheer referenties. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Gebruikersrol Azure Kubernetes service-cluster](#azure-kubernetes-service-cluster-user-role) | Geef een lijst actie voor de gebruikers referenties van het cluster op. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Rol van de Azure Kubernetes service-bijdrager](#azure-kubernetes-service-contributor-role) | Hiermee wordt toegang verleend voor het lezen en schrijven van Azure Kubernetes Service-clusters | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [RBAC-beheerder voor Azure Kubernetes service](#azure-kubernetes-service-rbac-admin) | Hiermee kunt u alle resources beheren onder cluster/naam ruimte, behalve voor het bijwerken of verwijderen van resource quota's en naam ruimten. | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [De Azure Kubernetes service RBAC-cluster beheerder](#azure-kubernetes-service-rbac-cluster-admin) | Hiermee kunt u alle resources in het cluster beheren. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [RBAC-lezer van de Azure Kubernetes service](#azure-kubernetes-service-rbac-reader) | Hiermee staat u alleen-lezen toegang toe om de meeste objecten in een naam ruimte weer te geven. Het is niet toegestaan om weergave rollen of functie bindingen te bekijken. Deze rol staat geen weergave geheimen toe, omdat het lezen van de inhoud van geheimen toegang biedt tot ServiceAccount-referenties in de naam ruimte, waardoor API-toegang zou worden toegestaan als ServiceAccount in de naam ruimte (een vorm van bevoegdheden escalation). Als deze rol op het cluster bereik wordt toegepast, krijgt u toegang tot alle naam ruimten. | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [RBAC-schrijver van Azure Kubernetes service](#azure-kubernetes-service-rbac-writer) | Hiermee wordt lees-/schrijftoegang tot de meeste objecten in een naam ruimte toegestaan. Deze rol staat het weer geven of wijzigen van rollen of rollen bindingen niet toe. Met deze rol kunt u echter wel toegang krijgen tot geheimen en een ServiceAccount uitvoeren in de naam ruimte, zodat het kan worden gebruikt om de API-toegangs niveaus van een wille keurige ServiceAccount in de naam ruimte te verkrijgen. Als deze rol op het cluster bereik wordt toegepast, krijgt u toegang tot alle naam ruimten. | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **Databases** |  |  |
> | [Rol van Cosmos DB-account lezer](#cosmos-db-account-reader-role) | Kan gegevens van Azure Cosmos DB-account lezen. Zie [DocumentDB account Inzender](#documentdb-account-contributor) voor het beheren van Azure Cosmos DB accounts. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB-operator](#cosmos-db-operator) | Hiermee kunt u Azure Cosmos DB accounts beheren, maar geen toegang tot gegevens. Hiermee voor komt u toegang tot account sleutels en verbindings reeksen. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Kan een terugzet aanvraag indienen voor een Cosmos DB-Data Base of een container voor een account | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [CosmosRestoreOperator](#cosmosrestoreoperator) | Kan de actie voor het herstellen van Cosmos DB database account met doorlopende back-upmodus uitvoeren | 5432c526-bc82-444a-b7ba-57c5b0b5b34f |
> | [Inzender voor DocumentDB-accounts](#documentdb-account-contributor) | Kan Azure Cosmos DB accounts beheren. Azure Cosmos DB is voorheen bekend als DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Inzender Redis Cache](#redis-cache-contributor) | Hiermee kunt u redis-caches beheren, maar niet de toegang tot deze bestanden. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Inzender voor SQL-data base](#sql-db-contributor) | Hiermee kunt u SQL-data bases beheren, maar niet de toegang tot ze. U kunt ook hun beveiligings beleid of de bovenliggende SQL-servers niet beheren. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [Inzender voor SQL Managed instance](#sql-managed-instance-contributor) | Hiermee beheert u beheerde SQL-instanties en de vereiste netwerk configuratie, maar kunt u geen toegang verlenen aan anderen. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL-beveiligingsbeheerder](#sql-security-manager) | Hiermee kunt u het beveiligings beleid van SQL-servers en-data bases beheren, maar niet de toegang tot de services. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Inzender SQL Server](#sql-server-contributor) | Hiermee kunt u SQL-servers en-data bases beheren, maar niet de toegang tot ze en niet het beveiligings beleid. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analyse** |  |  |
> | [Eigenaar van Azure Event Hubs-gegevens](#azure-event-hubs-data-owner) | Hiermee krijgt u volledige toegang tot Azure Event Hubs-resources. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Gegevens ontvanger van Azure Event Hubs](#azure-event-hubs-data-receiver) | Hiermee krijgt u toegang tot Azure Event Hubs-resources. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Afzender van Azure Event Hubs gegevens](#azure-event-hubs-data-sender) | Hiermee wordt toegang tot Azure Event Hubs-resources verzonden. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Inzender Data Factory](#data-factory-contributor) | Het maken en beheren van gegevens fabrieken, evenals onderliggende resources. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Gegevens opschoner](#data-purger) | Kan Analytics-gegevens verwijderen | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [HDInsight-cluster operator](#hdinsight-cluster-operator) | Hiermee kunt u HDInsight-cluster configuraties lezen en wijzigen. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Inzender voor HDInsight Domain Services](#hdinsight-domain-services-contributor) | Kan gerelateerde bewerkingen die betrekking hebben op domein services lezen, maken, wijzigen en verwijderen die nodig zijn voor HDInsight-Enterprise Security Package | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Inzender van Log Analytics](#log-analytics-contributor) | Log Analytics Inzender kan alle bewakings gegevens lezen en controle-instellingen bewerken. Het bewerken van bewakings instellingen omvat het toevoegen van de VM-extensie aan Vm's; lezen van opslag account sleutels om het verzamelen van logboeken van Azure Storage te kunnen configureren. Automation-accounts maken en configureren; oplossingen toevoegen; en het configureren van Azure Diagnostics voor alle Azure-resources. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Lezer van Log Analytics](#log-analytics-reader) | Log Analytics Reader kan alle bewakings gegevens weer geven en doorzoeken en controle-instellingen weer geven, inclusief het weer geven van de configuratie van Azure Diagnostics op alle Azure-resources. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Controle sfeer liggen data curator](#purview-data-curator) | Met de micro soft. controle sfeer liggen data curator kunnen catalogus gegevensobjecten worden gemaakt, gelezen, gewijzigd en verwijderd en worden relaties tussen objecten tot stand gebracht. Deze functie is beschikbaar als preview-versie en kan worden gewijzigd. | 8a3c2885-9b38-4fd2-9d99-91af537c1347 |
> | [Gegevens lezer controle sfeer liggen](#purview-data-reader) | De gegevens lezer van micro soft. controle sfeer liggen kan catalogus gegevensobjecten lezen. Deze functie is beschikbaar als preview-versie en kan worden gewijzigd. | ff100721-1b9d-43d8-af52-42b69c1272db |
> | [Controle sfeer liggen-gegevens bron beheerder](#purview-data-source-administrator) | De gegevens bron beheerder van micro soft. controle sfeer liggen kan gegevens bronnen en gegevens scans beheren. Deze functie is beschikbaar als preview-versie en kan worden gewijzigd. | 200bba9e-f0c8-430f-892b-6f0794863803 |
> | [Inzender van schemaregisters (preview)](#schema-registry-contributor-preview) | Schemaregistergroepen en schema's lezen, schrijven en verwijderen. | 5dffeca3-4936-4216-b2bc-10343a5abb25 |
> | [Lezer van schemaregisters (preview)](#schema-registry-reader-preview) | Schemaregistergroepen en schema's lezen en weergeven. | 2c56ea50-c6b3-40a6-83c0-9d98858bc7d2 |
> | **Blockchain** |  |  |
> | [Toegang tot Block Chain-leden knooppunt (preview-versie)](#blockchain-member-node-access-preview) | Hiermee wordt toegang tot Block Chain-leden knooppunten toegestaan | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI en machine learning** |  |  |
> | [Inzender Cognitive Services](#cognitive-services-contributor) | Hiermee kunt u sleutels van Cognitive Services maken, lezen, bijwerken, verwijderen en beheren. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Cognitive Services Custom Vision Inzender](#cognitive-services-custom-vision-contributor) | Volledige toegang tot het project, inclusief de mogelijkheid om projecten weer te geven, te maken, te bewerken of te verwijderen. | c1ff6cc2-C111-46fe-8896-e0ef812ad9f3 |
> | [Implementatie van Cognitive Services Custom Vision](#cognitive-services-custom-vision-deployment) | Modellen publiceren, publicatie ongedaan maken of exporteren. Implementatie kan het project weer geven, maar kan het niet bijwerken. | 5c4089e1-6d96-4d2f-b296-c1bc7137275f |
> | [Cognitive Services Custom Vision Labeler](#cognitive-services-custom-vision-labeler) | Bekijk, trainings afbeeldingen bewerken en de afbeeldings Tags maken, toevoegen, verwijderen of verwijderen. Labelers kunnen het project weer geven, maar kunnen andere niet bijwerken dan trainingen en tags. | 88424f51-ebe7-446f-bc41-7fa16989e96c |
> | [Custom Vision lezer Cognitive Services](#cognitive-services-custom-vision-reader) | Alleen-lezen acties in het project. Lezers kunnen het project niet maken of bijwerken. | 93586559-c37d-4a6b-ba08-b9f0940c2d73 |
> | [Cognitive Services Custom Vision trainer](#cognitive-services-custom-vision-trainer) | Bekijk, bewerk projecten en train de modellen, inclusief de mogelijkheid om de modellen te publiceren, de publicatie ervan ongedaan te maken en te exporteren. Trainers kunnen het project niet maken of verwijderen. | 0a5ae4ab-0d65-4eeb-be61-29fc9b54394b |
> | [Cognitive Services gegevens lezer (preview-versie)](#cognitive-services-data-reader-preview) | Hiermee kunt u Cognitive Services gegevens lezen. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services metrics Advisor-beheerder](#cognitive-services-metrics-advisor-administrator) | Volledige toegang tot het project, met inbegrip van de configuratie van het systeem niveau. | cb43c632-a144-4ec5-977c-e80c4affc34a |
> | [Cognitive Services QnA Maker editor](#cognitive-services-qna-maker-editor) | U kunt een KB maken, bewerken, importeren en exporteren. U kunt geen KB publiceren of verwijderen. | f4cc2bf9-21be-47a1-bdf1-5c5804381025 |
> | [QnA Maker lezer Cognitive Services](#cognitive-services-qna-maker-reader) | Laten we alleen een KB lezen en testen. | 466ccd10-b268-4a11-b098-b4849f024126 |
> | [Cognitive Services gebruiker](#cognitive-services-user) | Hiermee kunt u de sleutels van Cognitive Services lezen en weer geven. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Gemengde realiteit** |  |  |
> | [Externe rendering-beheerder](#remote-rendering-administrator) | Biedt gebruikers de mogelijkheid om de mogelijkheden voor het omzetten van sessies, rendering en diagnose te beheren voor externe rendering in azure | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Client voor externe Rendering](#remote-rendering-client) | Biedt gebruikers de mogelijkheid om sessie, rendering en diagnose te beheren voor de externe rendering van Azure. | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Inzender voor ruimtelijke ankers](#spatial-anchors-account-contributor) | Hiermee kunt u ruimtelijke ankers in uw account beheren, maar niet verwijderen | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Eigenaar van ruimtelijk ankers](#spatial-anchors-account-owner) | Hiermee kunt u ruimtelijke ankers in uw account beheren, met inbegrip van het verwijderen ervan | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Lezer van ruimtelijk ankers-account](#spatial-anchors-account-reader) | Hiermee kunt u eigenschappen van ruimtelijke ankers in uw account zoeken en lezen | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integratie** |  |  |
> | [Inzender van API Management-service](#api-management-service-contributor) | Kan de service en de Api's beheren | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Functie API Management service operator](#api-management-service-operator-role) | Kan de service beheren, maar niet de Api's | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [API Management functie Service lezer](#api-management-service-reader-role) | Alleen-lezen toegang tot de service en Api's | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Eigenaar van app-configuratie gegevens](#app-configuration-data-owner) | Hiermee krijgt u volledige toegang tot de app-configuratie gegevens. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Gegevens lezer app-configuratie](#app-configuration-data-reader) | Hiermee staat u lees toegang toe voor app-configuratie gegevens. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Gegevens eigenaar Azure Service Bus](#azure-service-bus-data-owner) | Hiermee krijgt u volledige toegang tot Azure Service Bus-resources. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Gegevens ontvanger Azure Service Bus](#azure-service-bus-data-receiver) | Hiermee krijgt u toegang tot Azure Service Bus-resources. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Afzender van Azure Service Bus gegevens](#azure-service-bus-data-sender) | Hiermee kunt u toegang tot Azure Service Bus resources verzenden. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Registratie-eigenaar Azure Stack](#azure-stack-registration-owner) | Hiermee kunt u Azure Stack registraties beheren. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [EventGrid EventSubscription-bijdrager](#eventgrid-eventsubscription-contributor) | Hiermee kunt u bewerkingen voor EventGrid-gebeurtenis abonnementen beheren. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription-lezer](#eventgrid-eventsubscription-reader) | Hiermee kunt u EventGrid-gebeurtenis abonnementen lezen. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Inzender voor FHIR-gegevens](#fhir-data-contributor) | Rol maakt gebruiker of Principal volledige toegang tot FHIR-gegevens | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [FHIR-gegevens exporteur](#fhir-data-exporter) | Rol staat gebruiker of Principal toe om FHIR-gegevens te lezen en exporteren | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [Gegevens lezer FHIR](#fhir-data-reader) | De rol staat gebruikers of Principal toe om FHIR gegevens te lezen | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [FHIR-gegevens schrijver](#fhir-data-writer) | Rol staat gebruiker of Principal toe om FHIR-gegevens te lezen en te schrijven | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [Inzender Integratieserviceomgeving](#integration-service-environment-contributor) | Hiermee kunt u de integratie service omgevingen beheren, maar niet de toegang tot ze. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [Integratieserviceomgeving-ontwikkelaar](#integration-service-environment-developer) | Hiermee kunnen ontwikkel aars werk stromen, integratie accounts en API-verbindingen maken en bijwerken in integratie service omgevingen. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Inzender voor Intelligent Systems-account](#intelligent-systems-account-contributor) | Hiermee kunt u intelligente Systems-accounts beheren, maar niet de toegang tot ze. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Inzender voor logische apps](#logic-app-contributor) | Hiermee kunt u logische apps beheren, maar niet wijzigen. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Logische app-operator](#logic-app-operator) | Hiermee kunt u logische apps lezen, inschakelen en uitschakelen, maar niet bewerken of bijwerken. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identiteit** |  |  |
> | [Inzender beheerde identiteit](#managed-identity-contributor) | Aan de gebruiker toegewezen identiteit maken, lezen, bijwerken en verwijderen | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Beheerde identiteits operator](#managed-identity-operator) | Door gebruiker toegewezen identiteit lezen en toewijzen | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Beveiliging** |  |  |
> | [Bijdrager aan verklaring](#attestation-contributor) | Kan de Attestation-provider instantie lezen of verwijderen | bbf86eb8-f7b4-4cce-96e4-18cddf81d86e |
> | [Attestation-lezer](#attestation-reader) | Kan de eigenschappen van de Attestation-provider lezen | fd1bd22b-8476-40bc-a0bc-69b95687b9f3 |
> | [Azure Sentinel Contributor](#azure-sentinel-contributor) | Azure Sentinel Contributor | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel Reader](#azure-sentinel-reader) | Azure Sentinel Reader | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel Responder](#azure-sentinel-responder) | Azure Sentinel Responder | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Key Vault beheerder (preview-versie)](#key-vault-administrator-preview) | Alle gegevenslaag bewerkingen uitvoeren op een sleutel kluis en alle objecten hierin, met inbegrip van certificaten, sleutels en geheimen. Kan geen sleutel kluis resources beheren of roltoewijzingen beheren. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Key Vaulte-certificerings instanties (preview-versie)](#key-vault-certificates-officer-preview) | Acties uitvoeren op de certificaten van een sleutel kluis, met uitzonde ring van machtigingen beheren. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '. | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [Inzender Key Vault](#key-vault-contributor) | Sleutel kluizen beheren, maar u kunt geen rollen toewijzen in azure RBAC en biedt geen toegang tot geheimen, sleutels of certificaten. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Key Vault crypto-functionaris (preview-versie)](#key-vault-crypto-officer-preview) | Acties uitvoeren op de sleutels van een sleutel kluis, met uitzonde ring van machtigingen beheren. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Versleuteling van gebruiker van crypto-service Key Vault (preview-versie)](#key-vault-crypto-service-encryption-user-preview) | Lees de meta gegevens van sleutels en voer terugloop/uitlopende bewerkingen uit. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Key Vault crypto grafie-gebruiker (preview-versie)](#key-vault-crypto-user-preview) | Voer cryptografische bewerkingen uit met behulp van sleutels. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '. | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Key Vault lezer (preview-versie)](#key-vault-reader-preview) | Lees de meta gegevens van sleutel kluizen en de bijbehorende certificaten, sleutels en geheimen. Kan geen gevoelige waarden lezen, zoals geheime inhoud of sleutel materiaal. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '. | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [Key Vaulte geheimen-auditeur (preview-versie)](#key-vault-secrets-officer-preview) | Acties uitvoeren op de geheimen van een sleutel kluis, met uitzonde ring van machtigingen beheren. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
> | [Key Vault geheimen gebruiker (preview-versie)](#key-vault-secrets-user-preview) | Geheime inhoud lezen. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '. | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [Beheerde HSM-Inzender](#managed-hsm-contributor) | Hiermee kunt u beheerde HSM-groepen beheren, maar niet de toegang tot de Pools. | 18500a29-7fe2-46b2-a342-b16a415e101d |
> | [Beveiligingsbeheerder](#security-admin) | Machtigingen voor Security Center weer geven en bijwerken. Dezelfde machtigingen als de rol van beveiligings lezer en kunnen ook het beveiligings beleid bijwerken en waarschuwingen en aanbevelingen negeren. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Inzender voor beveiligings beoordeling](#security-assessment-contributor) | Hiermee kunt u evaluaties naar Security Center pushen | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Beveiligings beheer (verouderd)](#security-manager-legacy) | Dit is een verouderde rol. Gebruik in plaats daarvan beveiligings beheerder. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Beveiligingslezer](#security-reader) | Machtigingen voor Security Center weer geven. Kan aanbevelingen, waarschuwingen, een beveiligings beleid en beveiligings status weer geven, maar kan geen wijzigingen aanbrengen. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [DevTest Labs-gebruiker](#devtest-labs-user) | Met kunt u verbinding maken met uw virtuele machines in uw Azure DevTest Labs, deze starten, opnieuw opstarten en afsluiten. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Lab-Maker](#lab-creator) | Met kunt u nieuwe Labs maken onder uw Azure Lab-accounts. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Controle** |  |  |
> | [Inzender voor Application Insights onderdelen](#application-insights-component-contributor) | Kan Application Insights onderdelen beheren | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Geeft gebruikers machtigingen voor het weer geven en downloaden van moment opnamen van fout opsporing die zijn verzameld met de Application Insights Snapshot Debugger. Houd er rekening mee dat deze machtigingen niet zijn opgenomen in de rollen [eigenaar](#owner) of [Inzender](#contributor) . Wanneer gebruikers de Application Insights Snapshot Debugger rol geven, moet u de rol rechtstreeks aan de gebruiker toekennen. De rol wordt niet herkend wanneer deze wordt toegevoegd aan een aangepaste rol. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Inzender bewaken](#monitoring-contributor) | Kan alle bewakings gegevens lezen en controle-instellingen bewerken. Zie ook aan de [slag met rollen, machtigingen en beveiliging met Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [De uitgever van metrische gegevens controleren](#monitoring-metrics-publisher) | Hiermee schakelt u de metrische gegevens voor publicatie in op Azure-resources | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Bewakings lezer](#monitoring-reader) | Kan alle bewakings gegevens (metrieken, logboeken, enzovoort) lezen. Zie ook aan de [slag met rollen, machtigingen en beveiliging met Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Inzender voor werkmappen](#workbook-contributor) | Kan gedeelde werkmappen opslaan. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Werkmap lezer](#workbook-reader) | Kan werkmappen lezen. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Beheer + governance** |  |  |
> | [Automation-taak operator](#automation-job-operator) | Maak en beheer taken met behulp van Automation-Runbooks. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation-operator](#automation-operator) | Automatiserings operatoren kunnen taken starten, stoppen, onderbreken en hervatten | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automation-Runbook-operator](#automation-runbook-operator) | Runbook-eigenschappen lezen: om taken van het Runbook te kunnen maken. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Onboarding van met Azure verbonden computer](#azure-connected-machine-onboarding) | Kan onboarding van met Azure verbonden computers uitvoeren. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Resource beheerder van Azure Connected machine](#azure-connected-machine-resource-administrator) | Kan met Azure verbonden computers lezen, schrijven, verwijderen en onboarden. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Facturerings lezer](#billing-reader) | Hiermee staat u lees toegang tot facturerings gegevens toe | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Blauwdrukinzender](#blueprint-contributor) | Blauwdrukinzenders kunnen blauwdrukdefinities beheren, maar deze niet toewijzen. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Blauwdrukoperator](#blueprint-operator) | Kan bestaande gepubliceerde blauw drukken toewijzen, maar kan geen nieuwe blauw drukken maken. Houd er rekening mee dat dit alleen werkt als de toewijzing wordt uitgevoerd met een door de gebruiker toegewezen beheerde identiteit. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Inzender Cost Management](#cost-management-contributor) | Kan kosten bekijken en kosten configuratie beheren (bijvoorbeeld budgetten, exports) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Cost Management lezer](#cost-management-reader) | Kan kosten gegevens en-configuratie weer geven (bijvoorbeeld budgetten, exports) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Beheerder hiërarchie-instellingen](#hierarchy-settings-administrator) | Hiermee kunnen gebruikers hiërarchie-instellingen bewerken en verwijderen | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Kubernetes-cluster-Azure Arc-onboarding](#kubernetes-cluster---azure-arc-onboarding) | Roldefinitie voor het autoriseren van elke gebruiker/service voor het maken van een connectedClusters-resource | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [Rol Inzender beheerde toepassing](#managed-application-contributor-role) | Maakt het mogelijk om beheerde toepassings resources te maken. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Rol beheerde toepassings operator](#managed-application-operator-role) | Hiermee kunt u acties voor beheerde toepassings bronnen lezen en uitvoeren | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Lezer voor beheerde toepassingen](#managed-applications-reader) | Hiermee kunt u bronnen in een beheerde app lezen en JIT-toegang aanvragen. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Rol voor registratie toewijzing beheerde services verwijderen](#managed-services-registration-assignment-delete-role) | Met de functie voor registratie toewijzing van beheerde services verwijderen kan de Tenant gebruikers beheren de registratie toewijzing verwijderen die aan de Tenant is toegewezen. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Inzender beheer groep](#management-group-contributor) | Rol Inzender beheer groep | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Lezer beheer groep](#management-group-reader) | Rol van lezer van beheer groep | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Nieuwe Inzender voor Relic APM-account](#new-relic-apm-account-contributor) | Hiermee kunt u New Relic Application Performance Management accounts en-toepassingen beheren, maar niet de toegang tot ze. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Policy Insights Data Writer (preview-versie)](#policy-insights-data-writer-preview) | Hiermee wordt lees toegang tot bron beleid en schrijf toegang tot bron onderdeel beleids gebeurtenissen toegestaan. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Reserve ring-verkoper](#reservation-purchaser) | Hiermee kunt u reserve ringen kopen | f7b75c60-3036-4b75-91c3-6b41c27c1689 |
> | [Inzender voor resourcebeleid](#resource-policy-contributor) | Gebruikers met rechten voor het maken/wijzigen van het resource beleid, het maken van een ondersteunings ticket en het lezen van resources/hiërarchie. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Site Recovery-inzender](#site-recovery-contributor) | Hiermee kunt u Site Recovery-service beheren, behalve het maken van een kluis en roltoewijzing | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Site Recovery-operator](#site-recovery-operator) | Een failover en failback, maar geen andere Site Recovery beheer bewerkingen uitvoeren | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery-lezer](#site-recovery-reader) | Hiermee kunt u de Site Recovery status weer geven, maar geen andere beheer bewerkingen uitvoeren | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Inzender voor ondersteunings aanvragen](#support-request-contributor) | Hiermee kunt u ondersteunings aanvragen maken en beheren | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Inzender labelen](#tag-contributor) | Hiermee kunt u tags op entiteiten beheren zonder dat u toegang hebt tot de entiteiten zelf. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Overige** |  |  |
> | [Azure Digital Apparaatdubbels-gegevens eigenaar](#azure-digital-twins-data-owner) | Volledige Access-rol voor Digital Apparaatdubbels data-vlak | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
> | [Azure Digital Apparaatdubbels-gegevens lezer](#azure-digital-twins-data-reader) | Alleen-lezen rol voor Digital Apparaatdubbels data-vlak-eigenschappen | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |
> | [BizTalk-bijdrager](#biztalk-contributor) | Hiermee kunt u BizTalk Services beheren, maar niet de toegang tot de service. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Inzender voor bureau blad-Virtualisatiehost](#desktop-virtualization-application-group-contributor) | Bijdrager van de toepassings groep bureau blad-virtualisatie. | 86240b0e-9422-4c43-887b-b61143f32ba8 |
> | [Lezer van toepassings groep voor desktop-virtualisatie](#desktop-virtualization-application-group-reader) | Lezer van de toepassings groep bureau blad-virtualisatie. | aebf23d0-b568-4e86-b8f9-fe83a2c6ab55 |
> | [Inzender voor bureau blad-virtualisatie](#desktop-virtualization-contributor) | Inzender van bureau blad-virtualisatie. | 082f0a83-3be5-4ba1-904c-961cca79b387 |
> | [Inzender voor RD Virtualization hostgroep](#desktop-virtualization-host-pool-contributor) | Inzender van de hostgroep voor bureau blad-virtualisatie. | e307426c-f9b6-4e81-87de-d99efb3c32bc |
> | [Lezer van hostgroep voor desktop-virtualisatie](#desktop-virtualization-host-pool-reader) | Lezer van de hostgroep voor bureau blad-virtualisatie. | ceadfde2-b300-400a-ab7b-6143895aa822 |
> | [Desktop Virtualization-lezer](#desktop-virtualization-reader) | Lezer van bureau blad-virtualisatie. | 49a72310-ab8d-41df-bbb0-79b649203868 |
> | [Host-operator voor bureau blad-Virtualisatiehost](#desktop-virtualization-session-host-operator) | Operator van de host van de bureau blad-Virtualisatiehost. | 2ad6aaab-ead9-4eaa-8ac5-da422f562408 |
> | [Gebruiker van bureau blad-virtualisatie](#desktop-virtualization-user) | Hiermee kunnen gebruikers de toepassingen in een toepassings groep gebruiken. | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [Operator voor de gebruikers sessie van de bureau blad-Virtualisatiehost](#desktop-virtualization-user-session-operator) | Operator van de Uesr-sessie voor bureau blad-virtualisatie. | ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6 |
> | [Mede werker van de werk ruimte bureau blad-virtualisatie](#desktop-virtualization-workspace-contributor) | Inzender van de werk ruimte bureau blad-virtualisatie. | 21efdde3-836f-432b-bf3d-3e8e734d4b2b |
> | [Werkruimte lezer voor desktop-virtualisatie](#desktop-virtualization-workspace-reader) | Lezer van de werk ruimte bureau blad-virtualisatie. | 0fa44ee9-7a7d-466b-9bb2-2bf446b1204d |
> | [Back-uplezer van schijf](#disk-backup-reader) | Biedt machtiging voor back-upkluis voor het uitvoeren van schijf back-ups. | 3e5e47e6-65f7-47ef-90b5-e5dd4d455f24 |
> | [Schijf herstel operator](#disk-restore-operator) | Biedt machtigingen voor het maken van een back-upkluis om schijf herstel uit te voeren. | b50d9833-a0cb-478e-945f-707fcc997c13 |
> | [Inzender voor schijf momentopnamen](#disk-snapshot-contributor) | Biedt machtiging voor back-upkluis voor het beheren van moment opnamen van schijven. | 7efff54f-a5b4-42b5-a1c5-5411624893ce |
> | [Inzender voor scheduler-taak verzamelingen](#scheduler-job-collections-contributor) | Hiermee kunt u scheduler-taak verzamelingen beheren, maar niet de toegang tot deze taken. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [Operator Services hub](#services-hub-operator) | Met de operator Services hub kunt u alle Lees-, schrijf-en verwijder bewerkingen uitvoeren met betrekking tot services hub-connectors. | 82200a5b-e217-47a5-b665-6d8765ee745b |


## <a name="general"></a>Algemeen


### <a name="contributor"></a>Inzender

Verleent volledige toegang om alle resources te beheren, maar biedt geen ondersteuning voor het toewijzen van rollen in azure RBAC, het beheren van toewijzingen in azure-blauw drukken of het delen van afbeeldings galerieën. [Meer informatie](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | * | Resources van alle typen maken en beheren |
> | **NotActions** |  |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Delete | Rollen, beleids toewijzingen, beleids definities en definities van beleids sets verwijderen |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/write | Rollen, roltoewijzingen, beleids toewijzingen, beleids definities en definities van beleids sets maken |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | Hiermee krijgt de Gebruikerstoegangbeheerder toegang op tenantniveau |
> | [Micro soft. blauw druk](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/write | Een blauw druk-toewijzing maken of bijwerken |
> | [Micro soft. blauw druk](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/Delete | Eventuele blauw drukken-toewijzingen verwijderen |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/Galleries/share/Action | Een galerie deelt met verschillende bereiken |
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

Hiermee wordt volledige toegang verleend voor het beheren van alle resources, inclusief de mogelijkheid om rollen toe te wijzen in azure RBAC. [Meer informatie](rbac-and-directory-admin-roles.md)

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

Bekijk alle resources, maar u kunt geen wijzigingen aanbrengen. [Meer informatie](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
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

Hiermee beheert u de gebruikers toegang tot Azure-resources. [Meer informatie](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | [Micro soft. autorisatie](resource-provider-operations.md#microsoftauthorization)/* | Autorisatie beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

Hiermee beheert u klassieke virtuele machines, maar kunt u niet de toegang tot ze en niet het virtuele netwerk of opslag account waarmee ze zijn verbonden.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | Klassieke Compute-domein namen maken en beheren |
> | [Micro soft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | Virtuele machines maken en beheren |
> | [Micro soft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/join/Action |  |
> | [Micro soft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/link/Action | Een gereserveerd IP-adres koppelen |
> | [Micro soft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/Read | Hiermee worden de gereserveerde Ip's opgehaald |
> | [Micro soft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/join/Action | Voegt het virtuele netwerk samen. |
> | [Micro soft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/Read | Het virtuele netwerk ophalen. |
> | [Micro soft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/Read | Retourneert de schijf van het opslag account. |
> | [Micro soft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/Read | Hiermee wordt de installatie kopie van het opslag account geretourneerd. Keur. Micro soft. ClassicStorage/Storage accounts/vmImages gebruiken |
> | [Micro soft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Hier worden de toegangs sleutels voor de opslag accounts weer gegeven. |
> | [Micro soft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/Read | Retour neer het opslag account met het opgegeven account. |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="virtual-machine-administrator-login"></a>Aanmelding voor de beheerder van de virtuele machine

Bekijk Virtual Machines in de portal en meld u aan als beheerder [meer informatie](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Read | Hiermee wordt een definitie van een openbaar IP-adres opgehaald. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | De virtuele-netwerk definitie ophalen |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/Read | Hiermee wordt een load balancer definitie opgehaald |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Read | Hiermee haalt u een definitie van een netwerk interface.  |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/Action | Als gewone gebruiker aanmelden bij een virtuele machine |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/loginAsAdmin/Action | Aanmelden bij een virtuele machine met Windows-beheerders-of Linux-root gebruikers bevoegdheden |
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

Met kunt u virtuele machines beheren, maar niet de toegang tot ze en niet het virtuele netwerk of opslag account waarmee ze zijn verbonden. [Meer informatie](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | Berekenings beschikbaarheids sets maken en beheren |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/locations/* | Reken locaties maken en beheren |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | Voer alle acties van de virtuele machine uit, waaronder het maken, bijwerken, verwijderen, starten, opnieuw opstarten en uitschakelen van virtuele machines. Voer vooraf gedefinieerde scripts uit op virtuele machines. |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | Schaalsets voor virtuele machines maken en beheren |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/disks/write | Hiermee wordt een nieuwe schijf gemaakt of een bestaande bijgewerkt |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/disks/Read | De eigenschappen van een schijf ophalen |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/disks/Delete | Hiermee wordt de schijf verwijderd |
> | [Micro soft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Schedules/* |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/join/Action | Voegt een back-end-adres groep van een toepassings gateway samen. Niet Alertable. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/Action | Voegt een load balancer back-end-adres groep samen. Niet Alertable. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/join/Action | Voegt een load balancer binnenkomende NAT-pool samen. Niet alertable. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/Action | Voegt een load balancer binnenkomende NAT-regel. Niet Alertable. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/probes/join/Action | Staat het gebruik van tests van een load balancer toe. Met deze machtiging healthProbe eigenschap van de VM-schaalset kan bijvoorbeeld naar de test worden verwezen. Niet alertable. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/Read | Hiermee wordt een load balancer definitie opgehaald |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/locations/* | Netwerk locaties maken en beheren |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | Netwerk interfaces maken en beheren |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/Action | Er wordt lid van een netwerk beveiligings groep. Niet Alertable. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/Read | Hiermee wordt een definitie van een netwerk beveiligings groep opgehaald |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/Action | Er wordt verbinding gemaakt met een openbaar IP-adres. Niet Alertable. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Read | Hiermee wordt een definitie van een openbaar IP-adres opgehaald. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | De virtuele-netwerk definitie ophalen |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/Action | Voegt een virtueel netwerk samen. Niet Alertable. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/backupProtectionIntent/write | Een doel voor back-upbeveiliging maken |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/*/Read |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/Read | Hiermee worden object gegevens van het beveiligde item geretourneerd |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/write | Een beveiligd back-upitem maken |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupPolicies/Read | Hiermee worden alle beveiligings beleidsregels geretourneerd |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupPolicies/write | Hiermee wordt een beveiligings beleid gemaakt |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/Read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/usages/Read | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/write | Met een kluis bewerking maken wordt een Azure-resource van het type ' kluis ' gemaakt. |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="virtual-machine-user-login"></a>Gebruikers aanmelding voor de virtuele machine

Bekijk Virtual Machines in de portal en meld u aan als een gewone gebruiker. [Meer informatie](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Read | Hiermee wordt een definitie van een openbaar IP-adres opgehaald. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | De virtuele-netwerk definitie ophalen |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/Read | Hiermee wordt een load balancer definitie opgehaald |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Read | Hiermee haalt u een definitie van een netwerk interface.  |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/Action | Als gewone gebruiker aanmelden bij een virtuele machine |
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


### <a name="cdn-endpoint-contributor"></a>Inzender voor CDN-eind punten

Kan CDN-eind punten beheren, maar kan geen toegang verlenen aan andere gebruikers.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Micro soft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Micro soft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/endpoints/* |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="cdn-endpoint-reader"></a>CDN-eindpunt lezer

Kan CDN-eind punten weer geven, maar kan geen wijzigingen aanbrengen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Micro soft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Micro soft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/endpoints/*/Read |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="cdn-profile-contributor"></a>Inzender voor CDN-profiel

Kan CDN-profielen en de bijbehorende eind punten beheren, maar kan geen toegang verlenen aan andere gebruikers. [Meer informatie](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Micro soft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Micro soft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/* |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="cdn-profile-reader"></a>CDN-profiel lezer

Kan CDN-profielen en de bijbehorende eind punten weer geven, maar kan geen wijzigingen aanbrengen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Micro soft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Micro soft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/*/Read |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

Hiermee beheert u klassieke netwerken, maar hebt u geen toegang tot de netwerk bestanden. [Meer informatie](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | Klassieke netwerken maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="dns-zone-contributor"></a>Inzender DNS-zone

Met kunt u DNS-zones en-record sets beheren in Azure DNS, maar kunt u niet bepalen wie toegang heeft tot de groepen. [Meer informatie](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | DNS-zones en-records maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

Hiermee kunt u netwerken beheren, maar niet de toegang tot de netwerk bestanden.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/* | Netwerken maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="private-dns-zone-contributor"></a>Inzender voor Privé-DNS-zone

Hiermee kunt u privé-DNS-zone bronnen beheren, maar niet de virtuele netwerken waaraan ze zijn gekoppeld. [Meer informatie](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | De virtuele-netwerk definitie ophalen |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/join/Action | Voegt een virtueel netwerk samen. Niet Alertable. |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
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

### <a name="traffic-manager-contributor"></a>Inzender Traffic Manager

Hiermee beheert u Traffic Manager profielen, maar kunt u niet bepalen wie er toegang tot heeft.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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


### <a name="avere-contributor"></a>AVERE-bijdrager

Kan een avere vFXT-cluster maken en beheren. [Meer informatie](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/*/Read |  |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/disks/* |  |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/*/Read |  |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | De virtuele-netwerk definitie ophalen |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/Read | Hiermee wordt een subnet definitie van een virtueel netwerk opgehaald |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/Action | Voegt een virtueel netwerk samen. Niet Alertable. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/Action | Voegt een resource, zoals een opslag account of SQL database, toe aan een subnet. Niet alertable. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/Action | Er wordt lid van een netwerk beveiligings groep. Niet Alertable. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/*/Read |  |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Opslagaccounts maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/resources/Read | Hiermee haalt u de resources voor de resource groep op. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/Delete | Hiermee wordt het resultaat van het verwijderen van een BLOB geretourneerd |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/Read | Retourneert een BLOB of een lijst met blobs |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Hiermee wordt het resultaat van het schrijven van een BLOB geretourneerd |
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

### <a name="avere-operator"></a>AVERE-operator

Wordt gebruikt door het avere vFXT-cluster voor het beheren van het cluster [meer informatie](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/Read | De eigenschappen van een virtuele machine ophalen |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Read | Hiermee haalt u een definitie van een netwerk interface.  |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Hiermee maakt u een netwerk interface of werkt u een bestaande netwerk interface bij.  |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | De virtuele-netwerk definitie ophalen |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/Read | Hiermee wordt een subnet definitie van een virtueel netwerk opgehaald |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/Action | Voegt een virtueel netwerk samen. Niet Alertable. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/Action | Er wordt lid van een netwerk beveiligings groep. Niet Alertable. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Delete | Retourneert het resultaat van het verwijderen van een container |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Read | Hiermee wordt een lijst met containers opgehaald |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | Retourneert het resultaat van de put-BLOB-container |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/Delete | Hiermee wordt het resultaat van het verwijderen van een BLOB geretourneerd |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/Read | Retourneert een BLOB of een lijst met blobs |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Hiermee wordt het resultaat van het schrijven van een BLOB geretourneerd |
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

### <a name="backup-contributor"></a>Back-upinzender

Hiermee kunt u de back-upservice beheren, maar geen kluizen maken [en anderen toegang](../backup/backup-rbac-rs-vault.md) geven

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | De virtuele-netwerk definitie ophalen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/operationResults/* | Resultaten van de werking van het back-upbeheer beheren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/* | Back-upcontainers maken en beheren in back-upinfrastructuur van Recovery Services kluis |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/refreshContainers/Action | Hiermee vernieuwt u de container lijst |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupJobs/* | Back-uptaken maken en beheren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupJobsExport/Action | Taken exporteren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupOperationResults/* | Resultaten van back-upbeheer bewerkingen maken en beheren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupPolicies/* | Back-upbeleid maken en beheren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupProtectableItems/* | Items maken en beheren waarvan een back-up kan worden gemaakt |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupProtectedItems/* | Back-upitems maken en beheren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupProtectionContainers/* | Containers maken en beheren met back-upitems |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupSecurityPIN/* |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupUsageSummaries/Read | Retourneert samen vattingen voor beveiligde items en beveiligde servers voor een Recovery Services. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/certificates/* | Certificaten maken en beheren die zijn gerelateerd aan back-ups in Recovery Services kluis |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/extendedInformation/* | Uitgebreide informatie met betrekking tot de kluis maken en beheren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/monitoringAlerts/Read | Hiermee worden de waarschuwingen voor de Recovery Services-kluis opgehaald. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/monitoringConfigurations/* |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/Read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/registeredIdentities/* | Geregistreerde identiteiten maken en beheren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/usages/* | Gebruik van Recovery Services kluis maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupstorageconfig/* |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupconfig/* |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupValidateOperation/Action | Bewerking voor beveiligd item valideren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/write | Met een kluis bewerking maken wordt een Azure-resource van het type ' kluis ' gemaakt. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupOperations/Read | Hiermee wordt de status van de back-upbewerking voor Recovery Services kluis geretourneerd. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupEngines/Read | Retourneert alle back-upbeheerser vers die zijn geregistreerd bij de kluis. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectableContainers/Read | Alle Beveilig bare containers ophalen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/Action | De back-upstatus voor Recovery Services kluizen controleren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/Action |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/Action | Functies valideren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/monitoringAlerts/write | Hiermee wordt de waarschuwing opgelost. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | Met deze bewerking wordt de lijst met bewerkingen voor een resource provider geretourneerd |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/Read | Hiermee wordt de bewerkings status voor een bepaalde bewerking opgehaald |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupProtectionIntents/Read | Alle intenties van de back-upbeveiliging weer geven |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

Hiermee kunt u back-upservices beheren, behalve het verwijderen van back-ups, het maken van een kluis en het verlenen van toegang aan anderen [meer informatie](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | De virtuele-netwerk definitie ophalen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/operationResults/Read | Hiermee wordt de status van de bewerking geretourneerd |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/operationResults/Read | Hiermee wordt het resultaat van de bewerking die is uitgevoerd op de beveiligings container opgehaald. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/backup/Action | Maakt een back-up voor het beveiligde item. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/operationResults/Read | Hiermee wordt het resultaat van de bewerking die is uitgevoerd op beveiligde items opgehaald. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/Read | Hiermee wordt de status geretourneerd van de bewerking die is uitgevoerd op beveiligde items. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/Read | Hiermee worden object gegevens van het beveiligde item geretourneerd |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/Action | Direct-item herstel inrichten voor beveiligd item |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/Action | AccessToken ophalen voor het terugzetten van meerdere regio's. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Read | Herstel punten voor beveiligde items ophalen. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Restore/Action | Herstel punten voor beveiligde items herstellen. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/Action | Herstel van onmiddellijke items intrekken voor beveiligd item |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/write | Een beveiligd back-upitem maken |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/Read | Hiermee worden alle geregistreerde containers geretourneerd |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/refreshContainers/Action | Hiermee vernieuwt u de container lijst |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupJobs/* | Back-uptaken maken en beheren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupJobsExport/Action | Taken exporteren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupOperationResults/* | Resultaten van back-upbeheer bewerkingen maken en beheren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupPolicies/operationResults/Read | Resultaten van beleids bewerking ophalen. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupPolicies/Read | Hiermee worden alle beveiligings beleidsregels geretourneerd |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupProtectableItems/* | Items maken en beheren waarvan een back-up kan worden gemaakt |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupProtectedItems/Read | Hiermee wordt de lijst met alle beveiligde items geretourneerd. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupProtectionContainers/Read | Hiermee worden alle containers van het abonnement geretourneerd |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupUsageSummaries/Read | Retourneert samen vattingen voor beveiligde items en beveiligde servers voor een Recovery Services. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/certificates/write | Met de bewerking resource certificaat bijwerken wordt het resource/kluis-referentie certificaat bijgewerkt. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/extendedInformation/Read | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/extendedInformation/write | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/monitoringAlerts/Read | Hiermee worden de waarschuwingen voor de Recovery Services-kluis opgehaald. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/monitoringConfigurations/* |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/Read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/registeredIdentities/operationResults/Read | De bewerking resultaten van de bewerking ophalen kan worden gebruikt om de bewerkings status en het resultaat van de asynchroon ingediende bewerking op te halen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/registeredIdentities/Read | De bewerking containers ophalen kan worden gebruikt om de containers op te halen die voor een resource zijn geregistreerd. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/registeredIdentities/write | De bewerking service container registreren kan worden gebruikt om een container te registreren bij de Recovery-service. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/usages/Read | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupstorageconfig/* |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupValidateOperation/Action | Bewerking voor beveiligd item valideren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupOperations/Read | Hiermee wordt de status van de back-upbewerking voor Recovery Services kluis geretourneerd. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupPolicies/Operations/Read | Status van beleids bewerking ophalen. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/write | Hiermee maakt u een geregistreerde container |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/inquire/Action | Een query uitvoeren voor werk belastingen binnen een container |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupEngines/Read | Retourneert alle back-upbeheerser vers die zijn geregistreerd bij de kluis. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/backupProtectionIntent/write | Een doel voor back-upbeveiliging maken |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/backupProtectionIntent/Read | Een doel voor back-upbeveiliging verkrijgen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectableContainers/Read | Alle Beveilig bare containers ophalen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/items/Read | Alle items in een container ophalen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/Action | De back-upstatus voor Recovery Services kluizen controleren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/Action |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/Action | Functies valideren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupAadProperties/Read | Eigenschappen van AAD ophalen voor verificatie in de derde regio voor het terugzetten van meerdere regio's. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJobs/Action | Lijst met herstel taken voor meerdere regio's in de secundaire regio voor Recovery Services kluis. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJob/Action | Taak Details voor het herstellen van meerdere regio's in de secundaire regio voor Recovery Services kluis ophalen. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrossRegionRestore/Action | Herstel van meerdere regio's activeren. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationResults/Read | Retourneert CRR-bewerkings resultaat voor Recovery Services kluis. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationsStatus/Read | Retourneert CRR-bewerkings status voor Recovery Services kluis. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/monitoringAlerts/write | Hiermee wordt de waarschuwing opgelost. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | Met deze bewerking wordt de lijst met bewerkingen voor een resource provider geretourneerd |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/Read | Hiermee wordt de bewerkings status voor een bepaalde bewerking opgehaald |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupProtectionIntents/Read | Alle intenties van de back-upbeveiliging weer geven |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

Kan back-upservices weer geven, maar kan geen wijzigingen [meer](../backup/backup-rbac-rs-vault.md) aanbrengen

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/Read | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/operationResults/Read | Hiermee wordt de status van de bewerking geretourneerd |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/operationResults/Read | Hiermee wordt het resultaat van de bewerking die is uitgevoerd op de beveiligings container opgehaald. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/operationResults/Read | Hiermee wordt het resultaat van de bewerking die is uitgevoerd op beveiligde items opgehaald. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/Read | Hiermee wordt de status geretourneerd van de bewerking die is uitgevoerd op beveiligde items. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/Read | Hiermee worden object gegevens van het beveiligde item geretourneerd |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Read | Herstel punten voor beveiligde items ophalen. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/Read | Hiermee worden alle geregistreerde containers geretourneerd |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupJobs/operationResults/Read | Hiermee wordt het resultaat van de taak bewerking geretourneerd. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupJobs/Read | Hiermee worden alle taak objecten geretourneerd |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupJobsExport/Action | Taken exporteren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupOperationResults/Read | Retourneert een back-upbewerkings resultaat voor Recovery Services kluis. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupPolicies/operationResults/Read | Resultaten van beleids bewerking ophalen. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupPolicies/Read | Hiermee worden alle beveiligings beleidsregels geretourneerd |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupProtectedItems/Read | Hiermee wordt de lijst met alle beveiligde items geretourneerd. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupProtectionContainers/Read | Hiermee worden alle containers van het abonnement geretourneerd |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupUsageSummaries/Read | Retourneert samen vattingen voor beveiligde items en beveiligde servers voor een Recovery Services. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/extendedInformation/Read | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/monitoringAlerts/Read | Hiermee worden de waarschuwingen voor de Recovery Services-kluis opgehaald. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/Read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/registeredIdentities/operationResults/Read | De bewerking resultaten van de bewerking ophalen kan worden gebruikt om de bewerkings status en het resultaat van de asynchroon ingediende bewerking op te halen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/registeredIdentities/Read | De bewerking containers ophalen kan worden gebruikt om de containers op te halen die voor een resource zijn geregistreerd. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupstorageconfig/Read | Hiermee wordt de opslag configuratie voor Recovery Services kluis geretourneerd. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupconfig/Read | Hiermee wordt de configuratie voor Recovery Services kluis geretourneerd. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupOperations/Read | Hiermee wordt de status van de back-upbewerking voor Recovery Services kluis geretourneerd. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupPolicies/Operations/Read | Status van beleids bewerking ophalen. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupEngines/Read | Retourneert alle back-upbeheerser vers die zijn geregistreerd bij de kluis. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/backupProtectionIntent/Read | Een doel voor back-upbeveiliging verkrijgen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/items/Read | Alle items in een container ophalen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/Action | De back-upstatus voor Recovery Services kluizen controleren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/monitoringConfigurations/* |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/monitoringAlerts/write | Hiermee wordt de waarschuwing opgelost. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | Met deze bewerking wordt de lijst met bewerkingen voor een resource provider geretourneerd |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/Read | Hiermee wordt de bewerkings status voor een bepaalde bewerking opgehaald |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupProtectionIntents/Read | Alle intenties van de back-upbeveiliging weer geven |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/usages/Read | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/Action | Functies valideren |
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

### <a name="classic-storage-account-contributor"></a>Inzender voor klassieke opslag accounts

Hiermee kunt u klassieke opslag accounts beheren, maar niet de toegang tot de account.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | Opslagaccounts maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="classic-storage-account-key-operator-service-role"></a>Service functie voor de sleutel operator voor klassieke opslag accounts

De sleutel operators voor klassieke opslag accounts mogen sleutels vermelden en opnieuw genereren voor klassieke opslag accounts [meer informatie](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/Action | Hier worden de toegangs sleutels voor de opslag accounts weer gegeven. |
> | [Micro soft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/Action | Hiermee worden de bestaande toegangs sleutels voor het opslag account opnieuw gegenereerd. |
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

### <a name="data-box-contributor"></a>Inzender Data Box

Hiermee kunt u alles beheren onder Data Box Service, behalve dat anderen toegang verlenen. [Meer informatie](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. Databox](resource-provider-operations.md#microsoftdatabox)/* |  |
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

### <a name="data-box-reader"></a>Data Box lezer

Met kunt u de Data Box-Service beheren, met uitzonde ring van order gegevens maken of bewerken en anderen toegang verlenen. [Meer informatie](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Databox](resource-provider-operations.md#microsoftdatabox)/*/Read |  |
> | [Micro soft. Databox](resource-provider-operations.md#microsoftdatabox)/Jobs/listsecrets/Action |  |
> | [Micro soft. Databox](resource-provider-operations.md#microsoftdatabox)/Jobs/listcredentials/Action | Geeft een lijst van de niet-versleutelde referenties die zijn gerelateerd aan de order. |
> | [Micro soft. Databox](resource-provider-operations.md#microsoftdatabox)/locations/availableSkus/Action | Met deze methode wordt de lijst met beschik bare sku's geretourneerd. |
> | [Micro soft. Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateInputs/Action | Met deze methode worden alle typen validaties ondersteund. |
> | [Micro soft. Databox](resource-provider-operations.md#microsoftdatabox)/locations/regionConfiguration/Action | Deze methode retourneert de configuraties voor de regio. |
> | [Micro soft. Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateAddress/Action | Valideert het verzend adres en levert eventueel alternatieve adressen. |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics-ontwikkelaar

Hiermee kunt u uw eigen taken verzenden, bewaken en beheren, maar geen Data Lake Analytics accounts maken of verwijderen. [Meer informatie](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. BigAnalytics/accounts/* |  |
> | [Micro soft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | **NotActions** |  |
> | Micro soft. BigAnalytics/accounts/verwijderen |  |
> | Micro soft. BigAnalytics/accounts/TakeOwnership/actie |  |
> | Micro soft. BigAnalytics/accounts/schrijven |  |
> | [Micro soft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | Een DataLakeAnalytics-account verwijderen. |
> | [Micro soft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/Action | Machtigingen verlenen om taken te annuleren die door andere gebruikers zijn verzonden. |
> | [Micro soft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/write | Een DataLakeAnalytics-account maken of bijwerken. |
> | [Micro soft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/write | Een gekoppeld data Lake Store-account maken of bijwerken van een DataLakeAnalytics-account. |
> | [Micro soft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | Een Data Lake Store-account ontkoppelen van een DataLakeAnalytics-account. |
> | [Micro soft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/write | Een gekoppeld opslag account maken of bijwerken van een DataLakeAnalytics-account. |
> | [Micro soft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Delete | Een opslag account ontkoppelen van een DataLakeAnalytics-account. |
> | [Micro soft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/write | Een firewall regel maken of bijwerken. |
> | [Micro soft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Delete | Een firewall regel verwijderen. |
> | [Micro soft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/write | Een reken beleid maken of bijwerken. |
> | [Micro soft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | Een reken beleid verwijderen. |
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

### <a name="reader-and-data-access"></a>Lezer en gegevens toegang

Hiermee kunt u alles weer geven, maar kunt u geen opslag account of opgenomen resource verwijderen of maken. Ook wordt lees-/schrijftoegang tot alle gegevens in een opslag account toegestaan via toegang tot de sleutel van het opslag account.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListAccountSas/Action | Hiermee wordt het SAS-token van het account voor het opgegeven opslag account geretourneerd. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
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

Hiermee staat u het beheer van opslag accounts toe. Biedt toegang tot de account sleutel, die kan worden gebruikt om toegang te krijgen tot gegevens via een gedeelde sleutel autorisatie. [Meer informatie](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Hiermee wordt de diagnostische instelling voor Analyseserver gemaakt, bijgewerkt of gelezen |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/Action | Voegt een resource, zoals een opslag account of SQL database, toe aan een subnet. Niet alertable. |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Opslagaccounts maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="storage-account-key-operator-service-role"></a>Functie Service-sleutel operator van opslag account

Kan de toegangs sleutels voor het opslag account weer geven en opnieuw genereren. [Meer informatie](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/Action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/regeneratekey/Action | Hiermee worden de toegangs sleutels voor het opgegeven opslag account opnieuw gegenereerd. |
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

Azure Storage containers en blobs lezen, schrijven en verwijderen. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. [Meer informatie](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Delete | Een container verwijderen. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Read | Een container of een lijst met containers retour neren. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | Meta gegevens of eigenschappen van een container wijzigen. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | Retourneert een gebruikers delegerings sleutel voor de Blob service. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/Delete | Een BLOB verwijderen. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/Read | Een BLOB of een lijst met blobs retour neren. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/Move/Action | Verplaatst de blob van het ene pad naar het andere |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Schrijven naar een blob. |
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
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
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

Biedt volledige toegang tot Azure Storage BLOB-containers en-gegevens, met inbegrip van het toewijzen van POSIX-toegangs beheer. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. [Meer informatie](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/* | Volledige machtigingen voor containers. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | Retourneert een gebruikers delegerings sleutel voor de Blob service. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/* | Volledige machtigingen op blobs. |
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

Azure Storage containers en blobs lezen en weer geven. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. [Meer informatie](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Read | Een container of een lijst met containers retour neren. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | Retourneert een gebruikers delegerings sleutel voor de Blob service. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/Read | Een BLOB of een lijst met blobs retour neren. |
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

### <a name="storage-blob-delegator"></a>Delegering van opslag-BLOB

Een sleutel voor gebruikers overdracht ophalen, die vervolgens kan worden gebruikt om een gedeelde toegangs handtekening te maken voor een container of BLOB die is ondertekend met Azure AD-referenties. Zie [een gebruiker delegering Sa's maken](/rest/api/storageservices/create-user-delegation-sas)voor meer informatie. [Meer informatie](/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | Retourneert een gebruikers delegerings sleutel voor de Blob service. |
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

Hiermee wordt lees-, schrijf-en verwijder toegang voor bestanden/mappen in azure-bestands shares toegestaan. Deze rol heeft geen ingebouwd equivalent op Windows-bestands servers. [Meer informatie](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/Read | Retourneert een bestand/map of een lijst met bestanden/mappen. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Write | Retourneert het resultaat van het schrijven van een bestand of het maken van een map. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/Delete | Retourneert het resultaat van het verwijderen van een bestand/map. |
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

Hiermee kunt u Acl's voor lezen, schrijven, verwijderen en wijzigen van bestanden/mappen in azure-bestands shares. Deze rol is gelijk aan een ACL van de bestands share die moet worden gewijzigd op Windows-bestands servers. [Meer informatie](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/Read | Retourneert een bestand/map of een lijst met bestanden/mappen. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Write | Retourneert het resultaat van het schrijven van een bestand of het maken van een map. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/Delete | Retourneert het resultaat van het verwijderen van een bestand/map. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/modifypermissions/Action | Retourneert het resultaat van het wijzigen van de machtiging voor een bestand/map. |
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

Hiermee staat u lees toegang toe voor bestanden/mappen in azure-bestands shares. Deze rol is gelijk aan een ACL voor bestands shares van lezen op Windows-bestands servers. [Meer informatie](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/Read | Retourneert een bestand/map of een lijst met bestanden/mappen. |
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

### <a name="storage-queue-data-contributor"></a>Inzender voor opslag wachtrij gegevens

Lees-, schrijf-en verwijder Azure Storage-wacht rijen en-wachtrij berichten. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. [Meer informatie](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/Delete | Een wachtrij verwijderen. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/Read | Een wachtrij of een lijst met wacht rijen retour neren. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/write | Meta gegevens of eigenschappen van de wachtrij wijzigen. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/Delete | Een of meer berichten verwijderen uit een wachtrij. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/Read | Een of meer berichten uit een wachtrij bekijken of ophalen. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/write | Een bericht toevoegen aan een wachtrij. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/Action | Retourneert het resultaat van de verwerking van een bericht |
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

### <a name="storage-queue-data-message-processor"></a>Processor voor gegevens berichten van de opslag wachtrij

Een bericht uit een Azure Storage wachtrij bekijken, ophalen en verwijderen. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. [Meer informatie](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/Read | Een bericht bekijken. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/Action | Een bericht ophalen en verwijderen. |
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

### <a name="storage-queue-data-message-sender"></a>Afzender gegevens bericht van opslag wachtrij

Berichten toevoegen aan een Azure Storage wachtrij. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. [Meer informatie](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/add/Action | Een bericht toevoegen aan een wachtrij. |
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

### <a name="storage-queue-data-reader"></a>Gegevens lezer van de opslag wachtrij

Azure Storage-wacht rijen en-wachtrij berichten lezen en weer geven. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie over welke acties vereist zijn voor een bepaalde gegevens bewerking. [Meer informatie](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/Read | Hiermee wordt een wachtrij of een lijst met wacht rijen geretourneerd. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/Read | Een of meer berichten uit een wachtrij bekijken of ophalen. |
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


### <a name="azure-maps-data-contributor"></a>Inzender voor Azure Maps gegevens

Hiermee wordt toegang verleend tot lees-, schrijf-en verwijder toegang om gerelateerde gegevens vanuit een Azure Maps-account toe te wijzen. [Meer informatie](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/Read |  |
> | [Micro soft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/write |  |
> | [Micro soft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/Delete |  |
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

### <a name="azure-maps-data-reader"></a>Gegevens lezer Azure Maps

Hiermee wordt toegang verleend om gerelateerde gegevens te lezen vanuit een Azure Maps-account. [Meer informatie](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/Read |  |
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

### <a name="search-service-contributor"></a>Inzender Search Service

Hiermee kunt u zoek services beheren, maar niet de toegang tot ze. [Meer informatie](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. Search](resource-provider-operations.md#microsoftsearch)/searchServices/* | Zoek Services maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="signalr-accesskey-reader"></a>Signaal/AccessKey-lezer

Toegangs sleutels voor de seingevings service lezen

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/*/Read |  |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/listkeys/Action | De waarde van de toegangs sleutels voor de Signa lering in de beheer portal of via API weer geven |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="signalr-app-server-preview"></a>Seingevings app-server (preview-versie)

Hiermee wordt uw app server-toegangs signaal service met AAD-verificatie opties.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/Action | Een tijdelijke AccessKey voor het ondertekenen van ClientTokens genereren. |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/serverConnection/write | Start een server verbinding. |
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

### <a name="signalr-contributor"></a>Inzender bijdrager

Signalerings service resources maken, lezen, bijwerken en verwijderen

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/* |  |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="signalr-serverless-contributor-preview"></a>Inzender server zonder signaal (preview-versie)

Hiermee kan uw app Access-service in de serverloze modus met AAD-verificatie opties.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/Action | Genereer een ClientToken voor het starten van een client verbinding. |
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

### <a name="signalr-service-owner-preview"></a>Eigenaar van de seingevings service (preview-versie)

Volledige toegang tot REST Api's van de Azure signalerings service

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/hub/Send/Action | Berichten broadcasten naar alle client verbindingen in hub. |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Send/Action | Broadcast bericht naar groep. |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Read | Controleer of de groep bestaat of de gebruiker in de groep bestaat. |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/write | Groep toevoegen/verlaten. |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Send/Action | Verzend berichten rechtstreeks naar een client verbinding. |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Read | Controleer de aanwezigheid van de client verbinding. |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/write | Sluit de client verbinding. |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Send/Action | Berichten naar de gebruiker verzenden die kunnen bestaan uit meerdere client verbindingen. |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Read | Controleer de aanwezigheid van de gebruiker. |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/write |  |
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

### <a name="signalr-service-reader-preview"></a>Signaal-service lezer (preview-versie)

Alleen-lezen toegang tot REST Api's van de Azure signalerings service

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Read | Controleer of de groep bestaat of de gebruiker in de groep bestaat. |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Read | Controleer de aanwezigheid van de client verbinding. |
> | [Micro soft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Read | Controleer de aanwezigheid van de gebruiker. |
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

### <a name="web-plan-contributor"></a>Inzender voor webabonnementen

Hiermee kunt u de Webabonnementen voor websites beheren, maar niet de toegang tot de abonnementen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | Server farms maken en beheren |
> | [Micro soft. Web](resource-provider-operations.md#microsoftweb)-/hostingEnvironments/join/Action | Voegt een App Service Environment |
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

### <a name="website-contributor"></a>Website bijdrager

Hiermee kunt u websites beheren (niet Webabonnementen), maar niet de toegang tot de sites.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | Insights-onderdelen maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. Web](resource-provider-operations.md#microsoftweb)/certificates/* | Website certificaten maken en beheren |
> | [Micro soft. Web](resource-provider-operations.md#microsoftweb)-/listSitesAssignedToHostName/Read | Namen ophalen van sites die zijn toegewezen aan de hostnaam. |
> | [Micro soft. Web](resource-provider-operations.md#microsoftweb)-/serverFarms/join/Action | Een App Service-abonnement koppelen |
> | [Micro soft. Web](resource-provider-operations.md#microsoftweb)-/serverFarms/Read | De eigenschappen van een App Service plan ophalen |
> | [Micro soft. Web](resource-provider-operations.md#microsoftweb)/sites/* | Websites maken en beheren (voor het maken van sites is ook schrijf machtigingen vereist voor het bijbehorende App Service plan) |
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

[meer informatie over](../container-registry/container-registry-roles.md) ACR verwijderen

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Artifacts/Delete | Artefact verwijderen in container register. |
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

ACR-installatie kopie-ondertekenaar [meer informatie](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Sign/write | Meta gegevens van de vertrouwens relatie push/pull voor een container register. |
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

ACR-pull meer [informatie](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/Read | Pull of ophalen van installatie kopieën uit een container register. |
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

ACR-push [meer informatie](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/Read | Pull of ophalen van installatie kopieën uit een container register. |
> | [Micro soft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/push/write | Push of schrijf installatie kopieën naar een container register. |
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

ACR quarantaine gegevens lezer

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Quarantine/Read | In quarantaine geplaatste installatie kopieën verzamelen of ophalen uit het container register |
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

ACR quarantaine gegevens schrijver

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Quarantine/Read | In quarantaine geplaatste installatie kopieën verzamelen of ophalen uit het container register |
> | [Micro soft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Quarantine/write | De quarantaine status van in quarantaine geplaatste installatie kopieën schrijven/wijzigen |
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

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Rol van Cluster beheerder voor Azure Kubernetes-service

Lijst met actie voor cluster beheer referenties. [Meer informatie](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/Action | De clusterAdmin-referentie van een beheerd cluster weer geven |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/Action | Een beheerd cluster toegangs profiel verkrijgen met een rolnaam met behulp van de lijst referentie |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Een beheerd cluster ophalen |
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

### <a name="azure-kubernetes-service-cluster-user-role"></a>Gebruikersrol Azure Kubernetes service-cluster

Geef een lijst actie voor de gebruikers referenties van het cluster op. [Meer informatie](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | De clusterUser-referentie van een beheerd cluster weer geven |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Een beheerd cluster ophalen |
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

### <a name="azure-kubernetes-service-contributor-role"></a>Rol van de Azure Kubernetes service-bijdrager

Hiermee wordt toegang verleend tot lees-en schrijf Service clusters van Azure Kubernetes [meer informatie](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Een beheerd cluster ophalen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/write | Hiermee maakt u een nieuw beheerd cluster of wordt er een bestaande bijgewerkt |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
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

### <a name="azure-kubernetes-service-rbac-admin"></a>RBAC-beheerder voor Azure Kubernetes service

Hiermee kunt u alle resources beheren onder cluster/naam ruimte, behalve voor het bijwerken of verwijderen van resource quota's en naam ruimten. [Meer informatie](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/write | Hiermee wordt een implementatie gemaakt of bijgewerkt. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | De resultaten van de abonnements bewerking ophalen. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Hiermee wordt de lijst met abonnementen opgehaald. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | De clusterUser-referentie van een beheerd cluster weer geven |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/write | Schrijft resourcequotas |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Delete | Hiermee worden resourcequotas verwijderd |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/write | Schrijft naam ruimten |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/Delete | Verwijdert naam ruimten |

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

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>De Azure Kubernetes service RBAC-cluster beheerder

Hiermee kunt u alle resources in het cluster beheren. [Meer informatie](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/write | Hiermee wordt een implementatie gemaakt of bijgewerkt. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | De resultaten van de abonnements bewerking ophalen. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Hiermee wordt de lijst met abonnementen opgehaald. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | De clusterUser-referentie van een beheerd cluster weer geven |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
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

### <a name="azure-kubernetes-service-rbac-reader"></a>RBAC-lezer van de Azure Kubernetes service

Hiermee staat u alleen-lezen toegang toe om de meeste objecten in een naam ruimte weer te geven. Het is niet toegestaan om weergave rollen of functie bindingen te bekijken. Deze rol staat geen weergave geheimen toe, omdat het lezen van de inhoud van geheimen toegang biedt tot ServiceAccount-referenties in de naam ruimte, waardoor API-toegang zou worden toegestaan als ServiceAccount in de naam ruimte (een vorm van bevoegdheden escalation). Als deze rol op het cluster bereik wordt toegepast, krijgt u toegang tot alle naam ruimten. [Meer informatie](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/write | Hiermee wordt een implementatie gemaakt of bijgewerkt. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | De resultaten van de abonnements bewerking ophalen. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Hiermee wordt de lijst met abonnementen opgehaald. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/Read | Controllerrevisions lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/Read | Daemonsets lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/Deployments/Read | Leest implementaties |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/Read | Replicasets lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/Read | Statefulsets lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/AutoScaling/horizontalpodautoscalers/Read | Horizontalpodautoscalers lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/Read | Cronjobs lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/Read | Hiermee worden taken gelezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/Read | Configmaps lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/Read | Hiermee worden eind punten gelezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events.K8S.io/Events/Read | Gebeurtenissen lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events/Read | Gebeurtenissen lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/daemonsets/Read | Daemonsets lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/Deployments/Read | Leest implementaties |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/ingresses/Read | Ingresses lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/networkpolicies/Read | Networkpolicies lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/replicasets/Read | Replicasets lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/Read | Limitranges lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/Read | Naam ruimten lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.K8S.io/ingresses/Read | Ingresses lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.K8S.io/networkpolicies/Read | Networkpolicies lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/Read | Persistentvolumeclaims lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/Read | Hiermee worden de peulen gelezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Policy/poddisruptionbudgets/Read | Poddisruptionbudgets lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/Read | Replicationcontrollers lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/Read | Replicationcontrollers lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Read | Resourcequotas lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/Read | Serviceaccounts lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Services/Read | Services lezen |
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

### <a name="azure-kubernetes-service-rbac-writer"></a>RBAC-schrijver van Azure Kubernetes service

Hiermee wordt lees-/schrijftoegang tot de meeste objecten in een naam ruimte toegestaan. Deze rol staat het weer geven of wijzigen van rollen of rollen bindingen niet toe. Met deze rol kunt u echter wel toegang krijgen tot geheimen en een ServiceAccount uitvoeren in de naam ruimte, zodat het kan worden gebruikt om de API-toegangs niveaus van een wille keurige ServiceAccount in de naam ruimte te verkrijgen. Als deze rol op het cluster bereik wordt toegepast, krijgt u toegang tot alle naam ruimten. [Meer informatie](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/write | Hiermee wordt een implementatie gemaakt of bijgewerkt. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | De resultaten van de abonnements bewerking ophalen. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Hiermee wordt de lijst met abonnementen opgehaald. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/Read | Controllerrevisions lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/Deployments/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/AutoScaling/horizontalpodautoscalers/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events.K8S.io/Events/Read | Gebeurtenissen lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events/Read | Gebeurtenissen lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/daemonsets/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/Deployments/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/ingresses/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/networkpolicies/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/replicasets/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/Read | Limitranges lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/Read | Naam ruimten lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.K8S.io/ingresses/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.K8S.io/networkpolicies/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Policy/poddisruptionbudgets/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Read | Resourcequotas lezen |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Secrets/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/* |  |
> | [Micro soft. container service](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Services/* |  |
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


### <a name="cosmos-db-account-reader-role"></a>Rol van Cosmos DB-account lezer

Kan gegevens van Azure Cosmos DB-account lezen. Zie [DocumentDB account Inzender](#documentdb-account-contributor) voor het beheren van Azure Cosmos DB accounts. [Meer informatie](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/Read | Een verzameling lezen |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/Action | Hiermee wordt de alleen-lezen sleutels van het database account gelezen. |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/Read | Metrische definities lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Metrische gegevens lezen |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="cosmos-db-operator"></a>Cosmos DB-operator

Hiermee kunt u Azure Cosmos DB accounts beheren, maar geen toegang tot gegevens. Hiermee voor komt u toegang tot account sleutels en verbindings reeksen. [Meer informatie](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/Action | Voegt een resource, zoals een opslag account of SQL database, toe aan een subnet. Niet alertable. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
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
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*"
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

Kan een terugzet aanvraag indienen voor een Cosmos DB-Data Base of een container voor een account [meer informatie](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/backup/Action | Een aanvraag indienen voor het configureren van de back-up |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/Restore/Action | Een herstel aanvraag verzenden |
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

Kan de actie voor het herstellen van Cosmos DB database account met doorlopende back-upmodus uitvoeren

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/Restore/Action | Een herstel aanvraag verzenden |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/*/Read |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/Read | Een restorable data base-account lezen of alle restorable database accounts weer geven |
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

### <a name="documentdb-account-contributor"></a>Inzender voor DocumentDB-accounts

Kan Azure Cosmos DB accounts beheren. Azure Cosmos DB is voorheen bekend als DocumentDB. [Meer informatie](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | Azure Cosmos DB accounts maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/Action | Voegt een resource, zoals een opslag account of SQL database, toe aan een subnet. Niet alertable. |
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

### <a name="redis-cache-contributor"></a>Inzender Redis Cache

Hiermee kunt u redis-caches beheren, maar niet de toegang tot deze bestanden.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. cache](resource-provider-operations.md#microsoftcache)/register/Action | Hiermee wordt de resource provider micro soft. cache geregistreerd bij een abonnement |
> | [Micro soft. cache](resource-provider-operations.md#microsoftcache)/redis/* | Redis-caches maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="sql-db-contributor"></a>Inzender voor SQL-data base

Hiermee kunt u SQL-data bases beheren, maar niet de toegang tot ze. U kunt ook hun beveiligings beleid of de bovenliggende SQL-servers niet beheren. [Meer informatie](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/locations/*/Read |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/* | SQL-databases maken en beheren |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/Read | De lijst met servers retour neren of de eigenschappen voor de opgegeven server ophalen. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Metrische gegevens lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/Read | Metrische definities lezen |
> | **NotActions** |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/Tables/columns/sensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Controle-instellingen bewerken |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/Read | De data base-BLOB-controle records ophalen |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Beleids regels voor gegevens maskering bewerken |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/Tables/columns/sensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Beveiligings waarschuwingen beleid bewerken |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Metrische beveiligings gegevens bewerken |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
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

### <a name="sql-managed-instance-contributor"></a>Inzender voor SQL Managed instance

Hiermee beheert u beheerde SQL-instanties en de vereiste netwerk configuratie, maar kunt u geen toegang verlenen aan anderen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/locations/*/Read |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/locations/instanceFailoverGroups/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/* |  |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Metrische gegevens lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/Read | Metrische definities lezen |
> | **NotActions** |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/Delete | Hiermee verwijdert u een specifieke beheerde server Azure Active Directory alleen een verificatie object |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/write | Hiermee wordt een specifieke beheerde server toegevoegd of bijgewerkt Azure Active Directory alleen een verificatie object |
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

Hiermee kunt u het beveiligings beleid van SQL-servers en-data bases beheren, maar niet de toegang tot de services. [Meer informatie](../azure-sql/database/azure-defender-for-sql.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/Action | Voegt een resource, zoals een opslag account of SQL database, toe aan een subnet. Niet alertable. |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/locations/administratorAzureAsyncOperation/Read | Hiermee wordt het beheerde exemplaar van Azure async Administrator-bewerkingen opgehaald. |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/Tables/columns/sensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/transparentDataEncryption/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | SQL Server-controle-instelling maken en beheren |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/Read | Details ophalen van het uitgebreide-server-BLOB-controle beleid dat op een bepaalde server is geconfigureerd |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Controle-instellingen voor SQL Server-Data Base maken en beheren |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/Read | De data base-BLOB-controle records ophalen |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Gegevens maskerings beleid voor SQL server-data bases maken en beheren |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/Read | Details ophalen van het uitgebreide BLOB-controle beleid dat is geconfigureerd voor een bepaalde data base |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/Read | De lijst met data bases retour neren of de eigenschappen voor de opgegeven Data Base ophalen. |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/Read | Een database schema ophalen. |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/Tables/columns/Read | Een database kolom ophalen. |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/Tables/columns/sensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/Tables/Read | Een database tabel ophalen. |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Beveiligings waarschuwingen beleid voor SQL Server-Data Base maken en beheren |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Metrische gegevens over beveiliging voor SQL Server-Data Base maken en beheren |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/transparentDataEncryption/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/firewallRules/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/Read | De lijst met servers retour neren of de eigenschappen voor de opgegeven server ophalen. |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | Beveiligings waarschuwingen voor SQL Server maken en beheren |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Read | De lijst met beheerde exemplaren retour neren of de eigenschappen van het opgegeven beheerde exemplaar ophalen. |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/* |  |
> | [Micro soft. Security](resource-provider-operations.md#microsoftsecurity)/sqlVulnerabilityAssessments/* |  |
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
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/*",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*",
        "Microsoft.Security/sqlVulnerabilityAssessments/*"
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

### <a name="sql-server-contributor"></a>Inzender SQL Server

Hiermee kunt u SQL-servers en-data bases beheren, maar niet de toegang tot ze en niet het beveiligings beleid. [Meer informatie](../azure-sql/database/authentication-aad-configure.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/locations/*/Read |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/* | SQL-servers maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Metrische gegevens lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/Read | Metrische definities lezen |
> | **NotActions** |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/Tables/columns/sensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | SQL Server-controle-instellingen bewerken |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Controle-instellingen voor SQL Server-Data Base bewerken |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/Read | De data base-BLOB-controle records ophalen |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Beleids regels voor gegevens maskering van SQL Server-Data Base bewerken |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/Tables/columns/sensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Beleid voor beveiligings waarschuwingen van SQL Server-Data Base bewerken |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Metrische gegevens van beveiliging voor SQL Server-Data Base bewerken |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | Beveiligings waarschuwingen voor SQL server bewerken |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/Delete | Hiermee verwijdert u een specifieke server Azure Active Directory alleen een verificatie object |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/write | Hiermee wordt een specifieke server toegevoegd of bijgewerkt Azure Active Directory alleen een verificatie object |
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


### <a name="azure-event-hubs-data-owner"></a>Eigenaar van Azure Event Hubs-gegevens

Hiermee krijgt u volledige toegang tot Azure Event Hubs-resources. [Meer informatie](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
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

### <a name="azure-event-hubs-data-receiver"></a>Gegevens ontvanger van Azure Event Hubs

Hiermee krijgt u toegang tot Azure Event Hubs-resources. [Meer informatie](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/Read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. EventHub](resource-provider-operations.md#microsofteventhub)/*/receive/Action |  |
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

### <a name="azure-event-hubs-data-sender"></a>Afzender van Azure Event Hubs gegevens

Hiermee wordt toegang tot Azure Event Hubs-resources verzonden. [Meer informatie](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/Read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Send/Action |  |
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

### <a name="data-factory-contributor"></a>Inzender Data Factory

Het maken en beheren van gegevens fabrieken, evenals onderliggende resources. [Meer informatie](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | Maak en beheer gegevens fabrieken en onderliggende resources hierin. |
> | [Micro soft. DataFactory](resource-provider-operations.md#microsoftdatafactory)/factories/* | Maak en beheer gegevens fabrieken en onderliggende resources hierin. |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | Een eventSubscription maken of bijwerken |
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

### <a name="data-purger"></a>Gegevens opschoner

Kan analyse gegevens opschonen [meer informatie](../azure-monitor/platform/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Components/*/Read |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Components/purge/Action | Gegevens uit Application Insights verwijderen |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Log Analytics-gegevens weer geven |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/purge/Action | Opgegeven gegevens uit de werk ruimte verwijderen |
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

### <a name="hdinsight-cluster-operator"></a>HDInsight-cluster operator

Hiermee kunt u HDInsight-cluster configuraties lezen en wijzigen. [Meer informatie](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. HDInsight](resource-provider-operations.md#microsofthdinsight)/*/Read |  |
> | [Micro soft. HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/getGatewaySettings/Action | Gateway-instellingen voor HDInsight-cluster ophalen |
> | [Micro soft. HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/updateGatewaySettings/Action | Gateway-instellingen voor HDInsight-cluster bijwerken |
> | [Micro soft. HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/configurations/* |  |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

Kan benodigde bewerkingen voor domein services lezen, maken, wijzigen en verwijderen die nodig zijn voor HDInsight Enterprise Security Package [meer informatie](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Aad](resource-provider-operations.md#microsoftaad)/*/Read |  |
> | [Micro soft. Aad](resource-provider-operations.md#microsoftaad)/domainServices/*/Read |  |
> | [Micro soft. Aad](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
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

Log Analytics Inzender kan alle bewakings gegevens lezen en controle-instellingen bewerken. Het bewerken van bewakings instellingen omvat het toevoegen van de VM-extensie aan Vm's; lezen van opslag account sleutels om het verzamelen van logboeken van Azure Storage te kunnen configureren. Automation-accounts maken en configureren; oplossingen toevoegen; en het configureren van Azure Diagnostics voor alle Azure-resources. [Meer informatie](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/* |  |
> | [Micro soft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/Extensions/* |  |
> | [Micro soft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Hier worden de toegangs sleutels voor de opslag accounts weer gegeven. |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/Extensions/* |  |
> | [Micro soft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/Extensions/write | Hiermee wordt een Azure-Arc-extensie geïnstalleerd of bijgewerkt |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Hiermee wordt de diagnostische instelling voor Analyseserver gemaakt, bijgewerkt of gelezen |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Micro soft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Deployments/* |  |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

Log Analytics Reader kan alle bewakings gegevens weer geven en doorzoeken en controle-instellingen weer geven, inclusief het weer geven van de configuratie van Azure Diagnostics op alle Azure-resources. [Meer informatie](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/query/Action | Zoek met een nieuwe engine. |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Hiermee wordt een zoek query uitgevoerd |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | **NotActions** |  |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/sharedKeys/Read | Hiermee worden de gedeelde sleutels voor de werk ruimte opgehaald. Deze sleutels worden gebruikt om micro soft Operational Insights-agents te verbinden met de werk ruimte. |
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

### <a name="purview-data-curator"></a>Controle sfeer liggen data curator

Met de micro soft. controle sfeer liggen data curator kunnen catalogus gegevensobjecten worden gemaakt, gelezen, gewijzigd en verwijderd en worden relaties tussen objecten tot stand gebracht. Deze functie is beschikbaar als preview-versie en kan worden gewijzigd.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. controle sfeer liggen](resource-provider-operations.md#microsoftpurview)/accounts/Read | Lees de account resource voor de micro soft controle sfeer liggen-provider. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. controle sfeer liggen](resource-provider-operations.md#microsoftpurview)/accounts/data/read | Gegevens objecten lezen. |
> | [Micro soft. controle sfeer liggen](resource-provider-operations.md#microsoftpurview)/accounts/data/write | Gegevens objecten maken, bijwerken en verwijderen. |
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

### <a name="purview-data-reader"></a>Gegevens lezer controle sfeer liggen

De gegevens lezer van micro soft. controle sfeer liggen kan catalogus gegevensobjecten lezen. Deze functie is beschikbaar als preview-versie en kan worden gewijzigd.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. controle sfeer liggen](resource-provider-operations.md#microsoftpurview)/accounts/Read | Lees de account resource voor de micro soft controle sfeer liggen-provider. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. controle sfeer liggen](resource-provider-operations.md#microsoftpurview)/accounts/data/read | Gegevens objecten lezen. |
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

### <a name="purview-data-source-administrator"></a>Controle sfeer liggen-gegevens bron beheerder

De gegevens bron beheerder van micro soft. controle sfeer liggen kan gegevens bronnen en gegevens scans beheren. Deze functie is beschikbaar als preview-versie en kan worden gewijzigd.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. controle sfeer liggen](resource-provider-operations.md#microsoftpurview)/accounts/Read | Lees de account resource voor de micro soft controle sfeer liggen-provider. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. controle sfeer liggen](resource-provider-operations.md#microsoftpurview)/accounts/scan/Read | Lees gegevens bronnen en scans. |
> | [Micro soft. controle sfeer liggen](resource-provider-operations.md#microsoftpurview)/accounts/scan/write | Gegevens bronnen maken, bijwerken en verwijderen en scans beheren. |
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
> | [Micro soft. EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/* |  |
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
> | [Micro soft. EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/Read | Lijst met SchemaGroup-resource beschrijvingen ophalen |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/Read | Schema's ophalen |
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


### <a name="blockchain-member-node-access-preview"></a>Toegang tot Block Chain-leden knooppunt (preview-versie)

Hiermee krijgt u toegang tot de Block Chain-leden knooppunten [meer informatie](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Block Chain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/Read | Hiermee wordt een lijst met bestaande Block Chain-leden transactie knooppunten opgehaald of weer gegeven. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Block Chain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/Connect/Action | Maakt verbinding met een trans actie-knoop punt van het block Chain-lid. |
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


### <a name="cognitive-services-contributor"></a>Inzender Cognitive Services

Hiermee kunt u sleutels van Cognitive Services maken, lezen, bijwerken, verwijderen en beheren. [Meer informatie](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Micro soft. features](resource-provider-operations.md#microsoftfeatures)/features/Read | Hiermee haalt u de functies van een abonnement op. |
> | [Micro soft. features](resource-provider-operations.md#microsoftfeatures)/providers/features/Read | Hiermee wordt de functie van een abonnement in een bepaalde resource provider opgehaald. |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Hiermee wordt de diagnostische instelling voor Analyseserver gemaakt, bijgewerkt of gelezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/Read | Logboek definities lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/Read | Metrische definities lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Metrische gegevens lezen |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | De resultaten van de abonnements bewerking ophalen. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Hiermee wordt de lijst met abonnementen opgehaald. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/ResourceGroups/Deployments/* |  |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="cognitive-services-custom-vision-contributor"></a>Cognitive Services Custom Vision Inzender

Volledige toegang tot het project, inclusief de mogelijkheid om projecten weer te geven, te maken, te bewerken of te verwijderen. [Meer informatie](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
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

### <a name="cognitive-services-custom-vision-deployment"></a>Implementatie van Cognitive Services Custom Vision

Modellen publiceren, publicatie ongedaan maken of exporteren. Implementatie kan het project weer geven, maar kan het niet bijwerken. [Meer informatie](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/Read |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/Predictions/* |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/Publish/* |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/export/* |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/QuickTest/* |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/classify/* |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/detect/* |  |
> | **NotDataActions** |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/Read | Hiermee exporteert u een project. |

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

Bekijk, trainings afbeeldingen bewerken en de afbeeldings Tags maken, toevoegen, verwijderen of verwijderen. Labelers kunnen het project weer geven, maar kunnen andere niet bijwerken dan trainingen en tags. [Meer informatie](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/Read |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/Predictions/query/Action | Down load installatie kopieën die naar uw Voorspellings eindpunt zijn verzonden. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/* |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/Tags/* |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/suggested/* |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tagsandregions/Suggestions/Action | Met deze API worden voorgestelde Tags en regio's voor een matrix/batch met niet-gecodeerde installatie kopieën en vertrouwens elementen voor de Tags opgehaald. Er wordt een lege matrix geretourneerd als er geen tags worden gevonden. |
> | **NotDataActions** |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/Read | Hiermee exporteert u een project. |

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

### <a name="cognitive-services-custom-vision-reader"></a>Custom Vision lezer Cognitive Services

Alleen-lezen acties in het project. Lezers kunnen het project niet maken of bijwerken. [Meer informatie](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/Read |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/Predictions/query/Action | Down load installatie kopieën die naar uw Voorspellings eindpunt zijn verzonden. |
> | **NotDataActions** |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/Read | Hiermee exporteert u een project. |

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

### <a name="cognitive-services-custom-vision-trainer"></a>Cognitive Services Custom Vision trainer

Bekijk, bewerk projecten en train de modellen, inclusief de mogelijkheid om de modellen te publiceren, de publicatie ervan ongedaan te maken en te exporteren. Trainers kunnen het project niet maken of verwijderen. [Meer informatie](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/Action | Een project maken. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/Delete | Een specifiek project verwijderen. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/import/Action | Hiermee wordt een project geïmporteerd. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/Read | Hiermee exporteert u een project. |

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

### <a name="cognitive-services-data-reader-preview"></a>Cognitive Services gegevens lezer (preview-versie)

Hiermee kunt u Cognitive Services gegevens lezen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
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

### <a name="cognitive-services-metrics-advisor-administrator"></a>Cognitive Services metrics Advisor-beheerder

Volledige toegang tot het project, met inbegrip van de configuratie van het systeem niveau. [Meer informatie](../cognitive-services/metrics-advisor/how-tos/alerts.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/MetricsAdvisor/* |  |
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

### <a name="cognitive-services-qna-maker-editor"></a>Cognitive Services QnA Maker editor

U kunt een KB maken, bewerken, importeren en exporteren. U kunt geen KB publiceren of verwijderen. [Meer informatie](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/Read | Informatie over een roltoewijzing ophalen. |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/Read | Informatie over een roldefinitie ophalen. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Read | Hiermee wordt een lijst opgehaald met de Knowledge bases of Details van een specifieke Knowledge Base. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Download/Read | Down load de Knowledge Base. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Create/write | Asynchrone bewerking voor het maken van een nieuwe Knowledge Base. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/write | Asynchrone bewerking om een Knowledge Base te wijzigen of de inhoud van de Knowledge Base te vervangen. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/Action | GenerateAnswer-aanroep voor het uitvoeren van een query op de Knowledge Base. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Train/Action | Train gesprek om suggesties toe te voegen aan de Knowledge Base. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/Alterations/Read | Down loads van runtime. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/Alterations/write | Vervangings gegevens vervangen. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/Read | Hiermee worden eindpunt sleutels voor een eind punt opgehaald |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/refreshkeys/Action | Hiermee wordt een eindpunt sleutel opnieuw gegenereerd. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/Read | Hiermee worden de eindpunt instellingen voor een eind punt opgehaald |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/write | Seettings voor een eind punt bijwerken. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/Operations/Read | Hiermee worden details van een specifieke langlopende bewerking opgehaald. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Read | Hiermee wordt een lijst opgehaald met de Knowledge bases of Details van een specifieke Knowledge Base. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Download/Read | Down load de Knowledge Base. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Create/write | Asynchrone bewerking voor het maken van een nieuwe Knowledge Base. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/write | Asynchrone bewerking om een Knowledge Base te wijzigen of de inhoud van de Knowledge Base te vervangen. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/Action | GenerateAnswer-aanroep voor het uitvoeren van een query op de Knowledge Base. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Train/Action | Train gesprek om suggesties toe te voegen aan de Knowledge Base. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/Alterations/Read | Down loads van runtime. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/Alterations/write | Vervangings gegevens vervangen. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/Read | Hiermee worden eindpunt sleutels voor een eind punt opgehaald |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/refreshkeys/Action | Hiermee wordt een eindpunt sleutel opnieuw gegenereerd. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/Read | Hiermee worden de eindpunt instellingen voor een eind punt opgehaald |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/write | Seettings voor een eind punt bijwerken. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/Operations/Read | Hiermee worden details van een specifieke langlopende bewerking opgehaald. |
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
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/operations/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Editor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-reader"></a>QnA Maker lezer Cognitive Services

Laten we alleen een KB lezen en testen. [Meer informatie](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/Read | Informatie over een roltoewijzing ophalen. |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/Read | Informatie over een roldefinitie ophalen. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Read | Hiermee wordt een lijst opgehaald met de Knowledge bases of Details van een specifieke Knowledge Base. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Download/Read | Down load de Knowledge Base. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/Action | GenerateAnswer-aanroep voor het uitvoeren van een query op de Knowledge Base. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/Alterations/Read | Down loads van runtime. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/Read | Hiermee worden eindpunt sleutels voor een eind punt opgehaald |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/Read | Hiermee worden de eindpunt instellingen voor een eind punt opgehaald |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Read | Hiermee wordt een lijst opgehaald met de Knowledge bases of Details van een specifieke Knowledge Base. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Download/Read | Down load de Knowledge Base. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/Action | GenerateAnswer-aanroep voor het uitvoeren van een query op de Knowledge Base. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/Alterations/Read | Down loads van runtime. |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/Read | Hiermee worden eindpunt sleutels voor een eind punt opgehaald |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/Read | Hiermee worden de eindpunt instellingen voor een eind punt opgehaald |
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
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read"
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

Hiermee kunt u de sleutels van Cognitive Services lezen en weer geven. [Meer informatie](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/listkeys/Action | Lijst met sleutels |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Een klassieke waarschuwing voor metrische gegevens lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/Read | Een diagnostische instelling voor bronnen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/Read | Logboek definities lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/Read | Metrische definities lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Metrische gegevens lezen |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | De resultaten van de abonnements bewerking ophalen. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Hiermee wordt de lijst met abonnementen opgehaald. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
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

## <a name="mixed-reality"></a>Mixed reality


### <a name="remote-rendering-administrator"></a>Externe rendering-beheerder

Biedt gebruikers de mogelijkheid om de mogelijkheden voor het converteren van sessies, rendering en diagnose te beheren voor Azure remote rendering [meer informatie](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Action | Activum conversie starten |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Read | Eigenschappen van activa conversie ophalen |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Delete | Activa conversie stoppen |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Read | Sessie-eigenschappen ophalen |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Action | Sessies starten |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Delete | Sessies stoppen |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/Read | Verbinding maken met een sessie |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Diagnostic/Read | Verbinding maken met de externe rendering-controle |
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

### <a name="remote-rendering-client"></a>Client voor externe Rendering

Biedt gebruikers de mogelijkheid om sessie, rendering en diagnose te beheren voor de externe rendering van Azure. [Meer informatie](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Read | Sessie-eigenschappen ophalen |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Action | Sessies starten |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Delete | Sessies stoppen |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/Read | Verbinding maken met een sessie |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Diagnostic/Read | Verbinding maken met de externe rendering-controle |
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

### <a name="spatial-anchors-account-contributor"></a>Inzender voor ruimtelijke ankers

Hiermee kunt u ruimtelijke ankers in uw account beheren, maar niet verwijderen. [meer informatie](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Create/Action | Ruimtelijke ankers maken |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Ruimtelijke beankeringen in de buurt detecteren |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Eigenschappen van ruimtelijke ankers ophalen |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/Read | Ruimtelijke ankers zoeken |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Diagnostische gegevens verzenden om de kwaliteit van de Azure spatiale ankers-service te verbeteren |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Eigenschappen van ruimtelijke ankers bijwerken |
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

### <a name="spatial-anchors-account-owner"></a>Eigenaar van ruimtelijk ankers

Hiermee kunt u ruimtelijke ankers in uw account beheren, waaronder het verwijderen van [meer informatie](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Create/Action | Ruimtelijke ankers maken |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Delete | Ruimtelijke ankers verwijderen |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Ruimtelijke beankeringen in de buurt detecteren |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Eigenschappen van ruimtelijke ankers ophalen |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/Read | Ruimtelijke ankers zoeken |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Diagnostische gegevens verzenden om de kwaliteit van de Azure spatiale ankers-service te verbeteren |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Eigenschappen van ruimtelijke ankers bijwerken |
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

### <a name="spatial-anchors-account-reader"></a>Lezer van ruimtelijk ankers-account

Hiermee kunt u eigenschappen van ruimtelijke ankers in uw account zoeken en lezen. [meer informatie](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Ruimtelijke beankeringen in de buurt detecteren |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Eigenschappen van ruimtelijke ankers ophalen |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/Read | Ruimtelijke ankers zoeken |
> | [Micro soft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Diagnostische gegevens verzenden om de kwaliteit van de Azure spatiale ankers-service te verbeteren |
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


### <a name="api-management-service-contributor"></a>Inzender van API Management-service

Kan de service en de Api's beheren [meer informatie](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/* | API Management-service maken en beheren |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="api-management-service-operator-role"></a>Functie API Management service operator

Kan de service beheren, maar niet de Api's [meer informatie](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/Read | API Management service-exemplaren lezen |
> | [Micro soft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/Backup/Action | Backup API Management-service naar de opgegeven container in een door de gebruiker opgegeven opslag account |
> | [Micro soft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/Delete | API Management service-exemplaar verwijderen |
> | [Micro soft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/managedeployments/Action | SKU/eenheden wijzigen, regionale implementaties van API Management service toevoegen/verwijderen |
> | [Micro soft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/Read | Meta gegevens voor een API Management service-exemplaar lezen |
> | [Micro soft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/Restore/Action | De API Management-service herstellen vanuit de opgegeven container in een opslag account dat door de gebruiker is opgegeven |
> | [Micro soft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatecertificate/Action | TLS/SSL-certificaat voor een API Management-service uploaden |
> | [Micro soft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatehostname/Action | Aangepaste domein namen instellen, bijwerken of verwijderen voor een API Management-service |
> | [Micro soft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/write | API Management service-exemplaar maken of bijwerken |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | **NotActions** |  |
> | [Micro soft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/Keys/Read | Sleutels ophalen die aan de gebruiker zijn gekoppeld |
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

### <a name="api-management-service-reader-role"></a>API Management functie Service lezer

Alleen-lezen toegang tot de service en Api's [meer informatie](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/Read | API Management service-exemplaren lezen |
> | [Micro soft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/Read | Meta gegevens voor een API Management service-exemplaar lezen |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | **NotActions** |  |
> | [Micro soft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/Keys/Read | Sleutels ophalen die aan de gebruiker zijn gekoppeld |
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

### <a name="app-configuration-data-owner"></a>Eigenaar van app-configuratie gegevens

Hiermee krijgt u volledige toegang tot de app-configuratie gegevens. [Meer informatie](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Read |  |
> | [Micro soft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/write |  |
> | [Micro soft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Delete |  |
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

### <a name="app-configuration-data-reader"></a>Gegevens lezer app-configuratie

Hiermee staat u lees toegang toe voor app-configuratie gegevens. [Meer informatie](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Read |  |
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

### <a name="azure-service-bus-data-owner"></a>Gegevens eigenaar Azure Service Bus

Hiermee krijgt u volledige toegang tot Azure Service Bus-resources. [Meer informatie](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
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

### <a name="azure-service-bus-data-receiver"></a>Gegevens ontvanger Azure Service Bus

Hiermee krijgt u toegang tot Azure Service Bus-resources. [Meer informatie](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/Read |  |
> | [Micro soft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Read |  |
> | [Micro soft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Subscriptions/Read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/receive/Action |  |
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

### <a name="azure-service-bus-data-sender"></a>Afzender van Azure Service Bus gegevens

Hiermee kunt u toegang tot Azure Service Bus resources verzenden. [Meer informatie](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/Read |  |
> | [Micro soft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Read |  |
> | [Micro soft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Subscriptions/Read |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Send/Action |  |
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

### <a name="azure-stack-registration-owner"></a>Registratie-eigenaar Azure Stack

Hiermee kunt u Azure Stack registraties beheren.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/Read | De eigenschappen van een Azure Stack Edge-abonnement ophalen |
> | [Micro soft. AzureStack](resource-provider-operations.md#microsoftazurestack)/Registrations/Products/*/Action |  |
> | [Micro soft. AzureStack](resource-provider-operations.md#microsoftazurestack)/Registrations/Products/Read | Hiermee worden de eigenschappen van een Azure Stack Marketplace-product opgehaald |
> | [Micro soft. AzureStack](resource-provider-operations.md#microsoftazurestack)/Registrations/Read | Hiermee worden de eigenschappen van een Azure Stack registratie opgehaald |
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

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription-bijdrager

Hiermee kunt u bewerkingen voor EventGrid-gebeurtenis abonnementen beheren. [Meer informatie](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* |  |
> | [Micro soft. EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/Read | Globale gebeurtenis abonnementen weer geven op onderwerps type |
> | [Micro soft. EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/Read | Regionale gebeurtenis abonnementen weer geven |
> | [Micro soft. EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/Read | Regionale gebeurtenis abonnementen weer geven op topictype |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription-lezer

Hiermee kunt u EventGrid-gebeurtenis abonnementen lezen. [Meer informatie](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Read | Een eventSubscription lezen |
> | [Micro soft. EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/Read | Globale gebeurtenis abonnementen weer geven op onderwerps type |
> | [Micro soft. EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/Read | Regionale gebeurtenis abonnementen weer geven |
> | [Micro soft. EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/Read | Regionale gebeurtenis abonnementen weer geven op topictype |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
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

### <a name="fhir-data-contributor"></a>Inzender voor FHIR-gegevens

De rol maakt gebruiker of Principal volledige toegang tot FHIR-gegevens [meer informatie](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. HealthcareApis/Services/fhir/resources/* |  |
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

### <a name="fhir-data-exporter"></a>FHIR-gegevens exporteur

De rol staat gebruikers of Principal toe om FHIR gegevens te lezen en exporteren [meer informatie](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. HealthcareApis/Services/fhir/resources/lezen | Lees FHIR-bronnen (inclusief Zoek opdrachten en geschiedenis van versies).  |
> | Micro soft. HealthcareApis/Services/fhir/resources/exporteren/actie | Export bewerking ($export). |
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

### <a name="fhir-data-reader"></a>Gegevens lezer FHIR

De rol staat gebruikers of Principal toe om FHIR gegevens te lezen [meer informatie](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. HealthcareApis/Services/fhir/resources/lezen | Lees FHIR-bronnen (inclusief Zoek opdrachten en geschiedenis van versies).  |
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

### <a name="fhir-data-writer"></a>FHIR-gegevens schrijver

De rol staat gebruikers of Principal toe om FHIR gegevens te lezen en te schrijven. meer [informatie](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | Micro soft. HealthcareApis/Services/fhir/resources/* |  |
> | **NotDataActions** |  |
> | Micro soft. HealthcareApis/Services/fhir/resources/hardDelete/actie | Harde verwijdering (inclusief versie geschiedenis). |

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

### <a name="integration-service-environment-contributor"></a>Inzender Integratieserviceomgeving

Hiermee kunt u de integratie service omgevingen beheren, maar niet de toegang tot ze. [Meer informatie](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
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

### <a name="integration-service-environment-developer"></a>Integratieserviceomgeving-ontwikkelaar

Hiermee kunnen ontwikkel aars werk stromen, integratie accounts en API-verbindingen maken en bijwerken in integratie service omgevingen. [Meer informatie](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/Read | Hiermee wordt de integratie service omgeving gelezen. |
> | [Micro soft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/*/join/Action |  |
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

Hiermee kunt u intelligente Systems-accounts beheren, maar niet de toegang tot ze.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | Micro soft. IntelligentSystems/accounts/* | Intelligente systeem accounts maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

Hiermee kunt u logische apps beheren, maar niet wijzigen. [Meer informatie](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Hier worden de toegangs sleutels voor de opslag accounts weer gegeven. |
> | [Micro soft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/Read | Retour neer het opslag account met het opgegeven account. |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Hiermee wordt de diagnostische instelling voor Analyseserver gemaakt, bijgewerkt of gelezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | Deze machtiging is nodig voor gebruikers die toegang moeten hebben tot activiteiten logboeken via de portal. Lijst met logboek categorieën in het activiteiten logboek. |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | Metrische definities lezen (lijst met beschik bare meet typen voor een resource). |
> | [Micro soft. Logic](resource-provider-operations.md#microsoftlogic)/* | Beheert Logic Apps-resources. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | De resultaten van de abonnements bewerking ophalen. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/Action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | Een verbindings gateway maken en beheren. |
> | [Micro soft. Web](resource-provider-operations.md#microsoftweb)/Connections/* | Een verbinding maken en beheren. |
> | [Micro soft. Web](resource-provider-operations.md#microsoftweb)/customApis/* | Maakt en beheert een aangepaste API. |
> | [Micro soft. Web](resource-provider-operations.md#microsoftweb)-/serverFarms/join/Action | Een App Service-abonnement koppelen |
> | [Micro soft. Web](resource-provider-operations.md#microsoftweb)-/serverFarms/Read | De eigenschappen van een App Service plan ophalen |
> | [Micro soft. Web](resource-provider-operations.md#microsoftweb)-/sites/functions/listSecrets/Action | Lijst met functie geheimen. |
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
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/Read | Inzichten-waarschuwings regels lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/Read |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/Read | Hiermee worden Diagnostische instellingen voor Logic Apps opgehaald |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/Read | Hiermee worden de beschik bare metrische gegevens opgehaald voor Logic Apps. |
> | [Micro soft. Logic](resource-provider-operations.md#microsoftlogic)/*/Read | Hiermee worden Logic Apps resources gelezen. |
> | [Micro soft. Logic](resource-provider-operations.md#microsoftlogic)/workflows/Disable/Action | Hiermee schakelt u de werk stroom uit. |
> | [Micro soft. Logic](resource-provider-operations.md#microsoftlogic)/workflows/Enable/Action | Hiermee wordt de werk stroom ingeschakeld. |
> | [Micro soft. Logic](resource-provider-operations.md#microsoftlogic)/workflows/validate/Action | Valideert de werk stroom. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | De resultaten van de abonnements bewerking ophalen. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/Read | Lees de verbindings gateways. |
> | [Micro soft. Web](resource-provider-operations.md#microsoftweb)/Connections/*/Read | Lees verbindingen. |
> | [Micro soft. Web](resource-provider-operations.md#microsoftweb)/customApis/*/Read | Lees de aangepaste API. |
> | [Micro soft. Web](resource-provider-operations.md#microsoftweb)-/serverFarms/Read | De eigenschappen van een App Service plan ophalen |
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


### <a name="managed-identity-contributor"></a>Inzender beheerde identiteit

Aan de gebruiker toegewezen identiteit maken, lezen, bijwerken en verwijderen [meer informatie](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/Read | Hiermee wordt een bestaande door de gebruiker toegewezen identiteit opgehaald |
> | [Micro soft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/write | Hiermee wordt een nieuwe door de gebruiker toegewezen identiteit gemaakt of worden de labels bijgewerkt die zijn gekoppeld aan een bestaande door de gebruiker toegewezen identiteit |
> | [Micro soft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/Delete | Hiermee wordt een bestaande door de gebruiker toegewezen identiteit verwijderd |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="managed-identity-operator"></a>Beheerde identiteits operator

Aan de gebruiker toegewezen identiteit lezen en toewijzen [meer informatie](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/Read |  |
> | [Micro soft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/Assign/Action |  |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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


### <a name="attestation-contributor"></a>Bijdrager aan verklaring

Kan het exemplaar van de Attestation-provider lezen of verwijderen. meer [informatie](../attestation/quickstart-powershell.md)

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

Kan de eigenschappen van de Attestation-provider lezen [meer informatie](../attestation/troubleshoot-guide.md)

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

### <a name="azure-sentinel-contributor"></a>Azure Sentinel Contributor

Azure Sentinel contributor [meer informatie](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/query/Action | Zoek met een nieuwe engine. |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Log Analytics-gegevens weer geven |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/* |  |
> | [Micro soft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | De OMS-oplossing ophalen |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/query/Read | Query's uitvoeren voor de gegevens in de werk ruimte |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/query/*/Read |  |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/dataSources/Read | Gegevens bronnen onder een werk ruimte ophalen. |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/* |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/Read | Een persoonlijke werkmap lezen |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

Azure Sentinel Reader [meer informatie](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/Read |  |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/Action | Gebruikers autorisatie en licentie controleren |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/Action | Query-Indica tors van Threat Intelligence |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/Action | Query-Indica tors van Threat Intelligence |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/query/Action | Zoek met een nieuwe engine. |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Log Analytics-gegevens weer geven |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/LinkedServices/Read | Gekoppelde services onder de opgegeven werk ruimte ophalen. |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/Read | Hiermee wordt een opgeslagen Zoek query opgehaald |
> | [Micro soft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | De OMS-oplossing ophalen |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/query/Read | Query's uitvoeren voor de gegevens in de werk ruimte |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/query/*/Read |  |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/dataSources/Read | Gegevens bronnen onder een werk ruimte ophalen. |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Een werkmap lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/Read | Een persoonlijke werkmap lezen |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

Azure Sentinel responder [meer informatie](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/Read |  |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/Action | Gebruikers autorisatie en licentie controleren |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/automationRules/* |  |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/* |  |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/Action | Tags toevoegen aan de bedreigings informatie-indicator |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/Action | Query-Indica tors van Threat Intelligence |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/bulkTag/Action | Bedreigings informatie in bulk Tags |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/Action | Tags toevoegen aan de bedreigings informatie-indicator |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/replaceTags/Action | Tags van de bedreigings informatie-indicator vervangen |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/Action | Query-Indica tors van Threat Intelligence |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/query/Action | Zoek met een nieuwe engine. |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Log Analytics-gegevens weer geven |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/dataSources/Read | Gegevens bronnen onder een werk ruimte ophalen. |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/Read | Hiermee wordt een opgeslagen Zoek query opgehaald |
> | [Micro soft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | De OMS-oplossing ophalen |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/query/Read | Query's uitvoeren voor de gegevens in de werk ruimte |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/query/*/Read |  |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/dataSources/Read | Gegevens bronnen onder een werk ruimte ophalen. |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Een werkmap lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/Read | Een persoonlijke werkmap lezen |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | **NotActions** |  |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/*/Delete |  |
> | [Micro soft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/*/Delete |  |
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

### <a name="key-vault-administrator-preview"></a>Key Vault beheerder (preview-versie)

Alle gegevenslaag bewerkingen uitvoeren op een sleutel kluis en alle objecten hierin, met inbegrip van certificaten, sleutels en geheimen. Kan geen sleutel kluis resources beheren of roltoewijzingen beheren. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/CheckNameAvailability/Read-kluis | Hiermee wordt gecontroleerd of de naam van een sleutel kluis geldig is en niet wordt gebruikt |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/DeletedVaults/Read-kluis | De eigenschappen van voorlopig verwijderde sleutel kluizen weer geven |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/locations/*/Read |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/*/Read |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/Operations/Read-kluis | Hiermee worden bewerkingen weer gegeven die beschikbaar zijn op micro soft. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/* |  |
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
  "roleName": "Key Vault Administrator (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer-preview"></a>Key Vaulte-certificerings instanties (preview-versie)

Acties uitvoeren op de certificaten van een sleutel kluis, met uitzonde ring van machtigingen beheren. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/CheckNameAvailability/Read-kluis | Hiermee wordt gecontroleerd of de naam van een sleutel kluis geldig is en niet wordt gebruikt |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/DeletedVaults/Read-kluis | De eigenschappen van voorlopig verwijderde sleutel kluizen weer geven |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/locations/*/Read |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/*/Read |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/Operations/Read-kluis | Hiermee worden bewerkingen weer gegeven die beschikbaar zijn op micro soft. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/certificatecas/* |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/certificates/* |  |
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
  "roleName": "Key Vault Certificates Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Inzender Key Vault

Sleutel kluizen beheren, maar u kunt geen rollen toewijzen in azure RBAC en biedt geen toegang tot geheimen, sleutels of certificaten. [Meer informatie](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft.-sleutel kluis](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | **NotActions** |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/locations/deletedVaults/purge/Action-kluis | Een voorlopig verwijderde sleutel kluis leegmaken |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/managedHsms/* |  |
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

### <a name="key-vault-crypto-officer-preview"></a>Key Vault crypto-functionaris (preview-versie)

Acties uitvoeren op de sleutels van een sleutel kluis, met uitzonde ring van machtigingen beheren. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/CheckNameAvailability/Read-kluis | Hiermee wordt gecontroleerd of de naam van een sleutel kluis geldig is en niet wordt gebruikt |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/DeletedVaults/Read-kluis | De eigenschappen van voorlopig verwijderde sleutel kluizen weer geven |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/locations/*/Read |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/*/Read |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/Operations/Read-kluis | Hiermee worden bewerkingen weer gegeven die beschikbaar zijn op micro soft. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/Keys/* |  |
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
  "roleName": "Key Vault Crypto Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-user-preview"></a>Versleuteling van gebruiker van crypto-service Key Vault (preview-versie)

Lees de meta gegevens van sleutels en voer terugloop/uitlopende bewerkingen uit. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | Een eventSubscription maken of bijwerken |
> | [Micro soft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Read | Een eventSubscription lezen |
> | [Micro soft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Delete | Een eventSubscription verwijderen |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/Keys/Read-kluis | Lijst met sleutels in de opgegeven kluis of lees de eigenschappen en het open bare materiaal van een sleutel. Voor asymmetrische sleutels wordt met deze bewerking de open bare sleutel beschikbaar gemaakt en is de mogelijkheid om open bare sleutel algoritmen uit te voeren, zoals versleutelen en hand tekeningen controleren. Persoonlijke sleutels en symmetrische sleutels worden nooit weer gegeven. |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/Keys/wrap/Action-kluis | Pakt een symmetrische sleutel met een Key Vault sleutel. Houd er rekening mee dat als de Key Vault sleutel asymmetrisch is, deze bewerking kan worden uitgevoerd door principals met lees toegang. |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/Keys/Unwrap/Action-kluis | Pakt een symmetrische sleutel met een Key Vault sleutel. |
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
  "roleName": "Key Vault Crypto Service Encryption User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user-preview"></a>Key Vault crypto grafie-gebruiker (preview-versie)

Voer cryptografische bewerkingen uit met behulp van sleutels. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/Keys/Read-kluis | Lijst met sleutels in de opgegeven kluis of lees de eigenschappen en het open bare materiaal van een sleutel. Voor asymmetrische sleutels wordt met deze bewerking de open bare sleutel beschikbaar gemaakt en is de mogelijkheid om open bare sleutel algoritmen uit te voeren, zoals versleutelen en hand tekeningen controleren. Persoonlijke sleutels en symmetrische sleutels worden nooit weer gegeven. |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/Keys/update/Action-kluis | Hiermee worden de opgegeven kenmerken bijgewerkt die zijn gekoppeld aan de opgegeven sleutel. |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/Keys/backup/Action-kluis | Hiermee maakt u het back-upbestand van een sleutel. Het bestand kan worden gebruikt om de sleutel te herstellen in een Key Vault van hetzelfde abonnement. Er kunnen beperkingen van toepassing zijn. |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/Keys/Encrypt/Action-kluis | Versleutelt de Lees bare tekst met een sleutel. Houd er rekening mee dat als de sleutel asymmetrisch is, deze bewerking kan worden uitgevoerd door principals met lees toegang. |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/Keys/Decrypt/Action-kluis | Ontsleutelen van gecodeerde tekst met een sleutel. |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/Keys/wrap/Action-kluis | Pakt een symmetrische sleutel met een Key Vault sleutel. Houd er rekening mee dat als de Key Vault sleutel asymmetrisch is, deze bewerking kan worden uitgevoerd door principals met lees toegang. |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/Keys/Unwrap/Action-kluis | Pakt een symmetrische sleutel met een Key Vault sleutel. |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/Keys/Sign/Action-kluis | Een Message Digest (hash) ondertekent met een sleutel. |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/Keys/verify/Action-kluis | Verifieert de hand tekening van een Message Digest (hash) met een sleutel. Houd er rekening mee dat als de sleutel asymmetrisch is, deze bewerking kan worden uitgevoerd door principals met lees toegang. |
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
  "roleName": "Key Vault Crypto User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader-preview"></a>Key Vault lezer (preview-versie)

Lees de meta gegevens van sleutel kluizen en de bijbehorende certificaten, sleutels en geheimen. Kan geen gevoelige waarden lezen, zoals geheime inhoud of sleutel materiaal. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/CheckNameAvailability/Read-kluis | Hiermee wordt gecontroleerd of de naam van een sleutel kluis geldig is en niet wordt gebruikt |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/DeletedVaults/Read-kluis | De eigenschappen van voorlopig verwijderde sleutel kluizen weer geven |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/locations/*/Read |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/*/Read |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/Operations/Read-kluis | Hiermee worden bewerkingen weer gegeven die beschikbaar zijn op micro soft. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/*/Read |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/Secrets/readMetadata/Action-kluis | De eigenschappen van een geheim weer geven of bekijken, maar niet de bijbehorende waarde. |
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
  "roleName": "Key Vault Reader (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer-preview"></a>Key Vaulte geheimen-auditeur (preview-versie)

Acties uitvoeren op de geheimen van een sleutel kluis, met uitzonde ring van machtigingen beheren. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/CheckNameAvailability/Read-kluis | Hiermee wordt gecontroleerd of de naam van een sleutel kluis geldig is en niet wordt gebruikt |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/DeletedVaults/Read-kluis | De eigenschappen van voorlopig verwijderde sleutel kluizen weer geven |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/locations/*/Read |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/*/Read |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/Operations/Read-kluis | Hiermee worden bewerkingen weer gegeven die beschikbaar zijn op micro soft. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/Secrets/* |  |
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
  "roleName": "Key Vault Secrets Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user-preview"></a>Key Vault geheimen gebruiker (preview-versie)

Geheime inhoud lezen. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/Secrets/getSecret/Action-kluis | Hiermee wordt de waarde van een geheim opgehaald. |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/vaults/Secrets/readMetadata/Action-kluis | De eigenschappen van een geheim weer geven of bekijken, maar niet de bijbehorende waarde. |
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
  "roleName": "Key Vault Secrets User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-hsm-contributor"></a>Beheerde HSM-Inzender

Hiermee kunt u beheerde HSM-groepen beheren, maar niet de toegang tot de Pools. [Meer informatie](../key-vault/managed-hsm/secure-your-managed-hsm.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft.](resource-provider-operations.md#microsoftkeyvault)/managedHSMs/* |  |
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

Machtigingen voor Security Center weer geven en bijwerken. Dezelfde machtigingen als de rol van beveiligings lezer en kunnen ook het beveiligings beleid bijwerken en waarschuwingen en aanbevelingen negeren. [Meer informatie](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | Beleids toewijzingen maken en beheren |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | Beleids definities maken en beheren |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyExemptions/* | Beleids uitzonderingen maken en beheren |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | Beleids sets maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | [Micro soft. operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Log Analytics-gegevens weer geven |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. Security](resource-provider-operations.md#microsoftsecurity)/* | Beveiligings onderdelen en-beleid maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="security-assessment-contributor"></a>Inzender voor beveiligings beoordeling

Hiermee kunt u evaluaties naar Security Center pushen

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Security](resource-provider-operations.md#microsoftsecurity)/assessments/write | Beveiligings beoordelingen maken of bijwerken voor uw abonnement |
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

### <a name="security-manager-legacy"></a>Beveiligings beheer (verouderd)

Dit is een verouderde rol. Gebruik in plaats daarvan beveiligings beheerder.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/Read | Configuratie-informatie voor klassieke virtuele machines lezen |
> | [Micro soft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/write | Configuratie voor klassieke virtuele machines schrijven |
> | [Micro soft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/Read | Configuratie-informatie over klassiek netwerk lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. Security](resource-provider-operations.md#microsoftsecurity)/* | Beveiligings onderdelen en-beleid maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

Machtigingen voor Security Center weer geven. Kan aanbevelingen, waarschuwingen, een beveiligings beleid en beveiligings status weer geven, maar kan geen wijzigingen aanbrengen. [Meer informatie](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Een klassieke waarschuwing voor metrische gegevens lezen |
> | [Micro soft. operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Log Analytics-gegevens weer geven |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/*/Read |  |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. Security](resource-provider-operations.md#microsoftsecurity)/*/Read | Beveiligings onderdelen en-beleid lezen |
> | [Micro soft. support](resource-provider-operations.md#microsoftsupport)/*/Read |  |
> | [Micro soft. Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/packageDownloads/Action | Hiermee worden de gegevens van het downloaden van IoT Defender-pakketten opgehaald |
> | [Micro soft. Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/downloadManagerActivation/Action | Down load Manager-activerings bestand met abonnements quotum gegevens |
> | [Micro soft. Security](resource-provider-operations.md#microsoftsecurity)/iotSensors/downloadResetPassword/Action | Down load wachtwoord bestand opnieuw instellen voor IoT Sens oren |
> | [Micro soft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Beheer groepen voor de geverifieerde gebruiker weer geven. |
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

Met kunt u verbinding maken met uw virtuele machines in uw Azure DevTest Labs, deze starten, opnieuw opstarten en afsluiten. [Meer informatie](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/Read | De eigenschappen van een beschikbaarheidsset ophalen |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read | De eigenschappen van een virtuele machine lezen (VM-grootte, runtime status, VM-extensies, enzovoort) |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/deallocate/Action | Hiermee wordt de virtuele machine uitgeschakeld en worden de reken resources vrijgegeven |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/Read | De eigenschappen van een virtuele machine ophalen |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/Action | Hiermee wordt de virtuele machine opnieuw opgestart |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/start/Action | De virtuele machine wordt gestart |
> | [Micro soft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/Read | De eigenschappen van een Lab lezen |
> | [Micro soft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/claimAnyVm/Action | Claim een wille keurig claim bare virtuele machine in het lab. |
> | [Micro soft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/createEnvironment/Action | Maak virtuele machines in een lab. |
> | [Micro soft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/ensureCurrentUserProfile/Action | Zorg ervoor dat de huidige gebruiker een geldig profiel in het lab heeft. |
> | [Micro soft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/formulas/Delete | Formules verwijderen. |
> | [Micro soft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/formulas/Read | Formules lezen. |
> | [Micro soft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/formulas/write | Formules toevoegen of wijzigen. |
> | [Micro soft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/policySets/evaluatePolicies/Action | Evalueert het lab-beleid. |
> | [Micro soft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualMachines/claim/Action | Eigenaar worden van een bestaande virtuele machine |
> | [Micro soft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualmachines/listApplicableSchedules/Action | Hier worden de toepasselijke start-en stop schema's vermeld, indien van toepassing. |
> | [Micro soft. DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualMachines/getRdpFileContents/Action | Hiermee wordt een teken reeks opgehaald waarmee de inhoud van het RDP-bestand voor de virtuele machine wordt aangeduid |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/Action | Voegt een load balancer back-end-adres groep samen. Niet Alertable. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/Action | Voegt een load balancer binnenkomende NAT-regel. Niet Alertable. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/Read | De eigenschappen van een netwerk interface lezen (bijvoorbeeld alle load balancers waarvan de netwerk interface deel uitmaakt) |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/join/Action | Voegt een virtuele machine toe aan een netwerk interface. Niet Alertable. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Read | Hiermee haalt u een definitie van een netwerk interface.  |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Hiermee maakt u een netwerk interface of werkt u een bestaande netwerk interface bij.  |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/Read | De eigenschappen van een openbaar IP-adres lezen |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/Action | Er wordt verbinding gemaakt met een openbaar IP-adres. Niet Alertable. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Read | Hiermee wordt een definitie van een openbaar IP-adres opgehaald. |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/Action | Voegt een virtueel netwerk samen. Niet Alertable. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Read | Hiermee wordt een lijst met implementaties opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | **NotActions** |  |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/Read | Een lijst met beschik bare grootten waarmee de virtuele machine kan worden bijgewerkt |
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

### <a name="lab-creator"></a>Lab-Maker

Met kunt u nieuwe Labs maken onder uw Azure Lab-accounts. [Meer informatie](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/Read |  |
> | [Micro soft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/Action | Een lab maken in een Lab-account. |
> | [Micro soft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/Action | Profiteer van de prijzen en beschik baarheid van combi Naties van groottes, geografi en besturings systemen voor het lab-account. |
> | [Micro soft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/Action | Kern beperkingen en gebruik voor dit abonnement ophalen |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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


### <a name="application-insights-component-contributor"></a>Inzender voor Application Insights onderdelen

Kan Application Insights-onderdelen beheren [meer informatie](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klassieke waarschuwings regels maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/generateLiveToken/Read | Get-token van Live Metrics |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | Nieuwe waarschuwings regels maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | Insights-onderdelen maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Topology/Read | Topologie lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Transactions/Read | Trans acties lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Inzichten-webtests maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

Geeft gebruikers machtigingen voor het weer geven en downloaden van moment opnamen van fout opsporing die zijn verzameld met de Application Insights Snapshot Debugger. Houd er rekening mee dat deze machtigingen niet zijn opgenomen in de rollen [eigenaar](#owner) of [Inzender](#contributor) . Wanneer gebruikers de Application Insights Snapshot Debugger rol geven, moet u de rol rechtstreeks aan de gebruiker toekennen. De rol wordt niet herkend wanneer deze wordt toegevoegd aan een aangepaste rol. [Meer informatie](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Components/*/Read |  |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="monitoring-contributor"></a>Inzender bewaken

Kan alle bewakings gegevens lezen en controle-instellingen bewerken. Zie ook aan de [slag met rollen, machtigingen en beveiliging met Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). [Meer informatie](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | [Micro soft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/Alerts/* |  |
> | [Micro soft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | Insights-onderdelen maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Hiermee wordt de diagnostische instelling voor Analyseserver gemaakt, bijgewerkt of gelezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/eventtypes/* | Lijst activiteiten logboek gebeurtenissen (beheer gebeurtenissen) in een abonnement. Deze machtiging is van toepassing op zowel toegang via het programma als de portal tot het activiteiten logboek. |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | Deze machtiging is nodig voor gebruikers die toegang moeten hebben tot activiteiten logboeken via de portal. Lijst met logboek categorieën in het activiteiten logboek. |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | Metrische definities lezen (lijst met beschik bare meet typen voor een resource). |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | Meet gegevens voor een resource lezen. |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/register/Action | De micro soft Insights-provider registreren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Inzichten-webtests maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/* |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/write | Hiermee maakt u een nieuwe werk ruimte of koppelingen naar een bestaande werk ruimte door de klant-id van de bestaande werk ruimte op te geven. |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/intelligencepacks/* | Log Analytics-oplossings pakketten lezen/schrijven/verwijderen. |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/* | In log Analytics opgeslagen Zoek opdrachten lezen/schrijven/verwijderen. |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Hiermee wordt een zoek query uitgevoerd |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/sharedKeys/Action | Hiermee worden de gedeelde sleutels voor de werk ruimte opgehaald. Deze sleutels worden gebruikt om micro soft Operational Insights-agents te verbinden met de werk ruimte. |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/storageinsightconfigs/* | Opslag inzicht configuraties voor log Analytics lezen/schrijven/verwijderen. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/monitors/* | Informatie over status monitors van de gast-VM ophalen. |
> | [Micro soft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Micro soft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Micro soft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
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

### <a name="monitoring-metrics-publisher"></a>De uitgever van metrische gegevens controleren

Hiermee schakelt u de metrische gegevens voor publicatie in op Azure-resources [meer informatie](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/register/Action | De micro soft Insights-provider registreren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/write | Metrische gegevens schrijven |
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

### <a name="monitoring-reader"></a>Bewakings lezer

Kan alle bewakings gegevens (metrieken, logboeken, enzovoort) lezen. Zie ook aan de [slag met rollen, machtigingen en beveiliging met Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). [Meer informatie](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | [Micro soft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Hiermee wordt een zoek query uitgevoerd |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="workbook-contributor"></a>Inzender voor werkmappen

Kan gedeelde werkmappen opslaan. [Meer informatie](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/write | Een werkmap maken of bijwerken |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Delete | Een werkmap verwijderen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Een werkmap lezen |
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

### <a name="workbook-reader"></a>Werkmap lezer

Kan werkmappen lezen. [Meer informatie](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [micro soft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Een werkmap lezen |
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

## <a name="management--governance"></a>Beheer + governance


### <a name="automation-job-operator"></a>Automation-taak operator

Maak en beheer taken met behulp van Automation-Runbooks. [Meer informatie](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/Read | Hybrid Runbook Worker resources lezen |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Read | Hiermee wordt een Azure Automation-taak opgehaald |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/resume/Action | Hiermee wordt een Azure Automation taak hervat |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/stop/Action | Hiermee wordt een Azure Automation taak gestopt |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/streams/Read | Hiermee wordt een Azure Automation taak stroom opgehaald |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Suspend/Action | Hiermee wordt een Azure Automation taak onderbroken |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/write | Hiermee maakt u een Azure Automation taak |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/output/Read | Hiermee wordt de uitvoer van een taak opgehaald |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

Automatiserings operatoren kunnen taken starten, stoppen, onderbreken en hervatten [meer informatie](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/Read | Hybrid Runbook Worker resources lezen |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Read | Hiermee wordt een Azure Automation-taak opgehaald |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/resume/Action | Hiermee wordt een Azure Automation taak hervat |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/stop/Action | Hiermee wordt een Azure Automation taak gestopt |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/streams/Read | Hiermee wordt een Azure Automation taak stroom opgehaald |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Suspend/Action | Hiermee wordt een Azure Automation taak onderbroken |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/write | Hiermee maakt u een Azure Automation taak |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/Read | Hiermee wordt een Azure Automation taak planning opgehaald |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/write | Hiermee maakt u een Azure Automation taak schema |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/linkedWorkspace/Read | Hiermee wordt de werk ruimte opgehaald die is gekoppeld aan het Automation-account |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Read | Hiermee wordt een Azure Automation-account opgehaald |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/Read | Hiermee wordt een Azure Automation runbook opgehaald |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/Read | Hiermee wordt een Azure Automation schema-element opgehaald |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/write | Hiermee wordt een Azure Automation Schedule-Asset gemaakt of bijgewerkt |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/output/Read | Hiermee wordt de uitvoer van een taak opgehaald |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="automation-runbook-operator"></a>Automation-Runbook-operator

Runbook-eigenschappen lezen: om taken van het Runbook te kunnen maken. [Meer informatie](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/Read | Hiermee wordt een Azure Automation runbook opgehaald |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="azure-connected-machine-onboarding"></a>Onboarding van met Azure verbonden computer

Kan onboarding van met Azure verbonden computers uitvoeren. [Meer informatie](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/Read | Alle Azure-Arc-machines lezen |
> | [Micro soft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Schrijft een Azure-Arc-machine |
> | [Micro soft. GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/Read | Toewijzing van gast configuratie ophalen. |
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

### <a name="azure-connected-machine-resource-administrator"></a>Resource beheerder van Azure Connected machine

Kan met Azure verbonden computers lezen, schrijven, verwijderen en onboarden.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/Read | Alle Azure-Arc-machines lezen |
> | [Micro soft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Schrijft een Azure-Arc-machine |
> | [Micro soft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/Delete | Hiermee wordt een Azure-Arc-machine verwijderd |
> | [Micro soft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/reconnect/Action |  |
> | [Micro soft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/Extensions/write | Hiermee wordt een Azure-Arc-extensie geïnstalleerd of bijgewerkt |
> | [Micro soft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/Read |  |
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
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/machines/extensions/write",
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

Hiermee staat u lees toegang tot facturerings gegevens toe [meer informatie](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. billing](resource-provider-operations.md#microsoftbilling)/*/Read | Facturerings gegevens lezen |
> | [Micro soft. commerce](resource-provider-operations.md#microsoftcommerce)/*/Read |  |
> | [Micro soft. verbruik](resource-provider-operations.md#microsoftconsumption)/*/Read |  |
> | [Micro soft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | [Micro soft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/Read |  |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. blauw druk](resource-provider-operations.md#microsoftblueprint)/Blueprints/* | Maak en beheer blauw drukken-definities of blauw drukken-artefacten. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

Kan bestaande gepubliceerde blauw drukken toewijzen, maar kan geen nieuwe blauw drukken maken. Houd er rekening mee dat dit alleen werkt als de toewijzing wordt uitgevoerd met een door de gebruiker toegewezen beheerde identiteit. [Meer informatie](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. blauw druk](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | Maak maken en beheren van blauw druk-toewijzingen. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="cost-management-contributor"></a>Inzender Cost Management

Kan kosten bekijken en kosten configuratie beheren (bijvoorbeeld budgetten, exports) [meer informatie](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. verbruik](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Micro soft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Micro soft. Bill](resource-provider-operations.md#microsoftbilling)/billingPeriods/Read |  |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Hiermee wordt de lijst met abonnementen opgehaald. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/Read | Configuraties ophalen |
> | [Micro soft. Advisor](resource-provider-operations.md#microsoftadvisor)/Recommendations/Read | Aanbevelingen voor lezen |
> | [Micro soft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | [Micro soft. Bill](resource-provider-operations.md#microsoftbilling)/billingProperty/Read |  |
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

### <a name="cost-management-reader"></a>Cost Management lezer

Kan kosten gegevens en-configuratie weer geven (bijvoorbeeld budgetten, exporten) [meer informatie](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. verbruik](resource-provider-operations.md#microsoftconsumption)/*/Read |  |
> | [Micro soft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/Read |  |
> | [Micro soft. Bill](resource-provider-operations.md#microsoftbilling)/billingPeriods/Read |  |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Hiermee wordt de lijst met abonnementen opgehaald. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/Read | Configuraties ophalen |
> | [Micro soft. Advisor](resource-provider-operations.md#microsoftadvisor)/Recommendations/Read | Aanbevelingen voor lezen |
> | [Micro soft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | [Micro soft. Bill](resource-provider-operations.md#microsoftbilling)/billingProperty/Read |  |
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
> | [Micro soft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Settings/write | Hiermee worden instellingen voor de beheer groeps hiërarchie gemaakt of bijgewerkt. |
> | [Micro soft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Settings/Delete | Hiermee verwijdert u de instellingen van de beheer groeps hiërarchie. |
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

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Kubernetes-cluster-Azure Arc-onboarding

Roldefinitie voor het autoriseren van elke gebruiker/service voor het maken van connectedClusters-resource [meer informatie](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/write | Hiermee wordt een implementatie gemaakt of bijgewerkt. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/Read | De resultaten van de abonnements bewerking ophalen. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Hiermee wordt de lijst met abonnementen opgehaald. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/write | Schrijft connectedClusters |
> | [Micro soft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Read | ConnectedClusters lezen |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="managed-application-contributor-role"></a>Rol Inzender beheerde toepassing

Maakt het mogelijk om beheerde toepassings resources te maken.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | [Micro soft. Solutions](resource-provider-operations.md#microsoftsolutions)/Applications/* |  |
> | [Micro soft. Solutions](resource-provider-operations.md#microsoftsolutions)/register/Action | Registreren bij oplossingen. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/* |  |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
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

### <a name="managed-application-operator-role"></a>Rol beheerde toepassings operator

Hiermee kunt u acties voor beheerde toepassings bronnen lezen en uitvoeren

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | [Micro soft. Solutions](resource-provider-operations.md#microsoftsolutions)/Applications/Read | Hiermee haalt u een lijst met toepassingen op. |
> | [Micro soft. Solutions](resource-provider-operations.md#microsoftsolutions)/*/Action |  |
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

### <a name="managed-applications-reader"></a>Lezer voor beheerde toepassingen

Hiermee kunt u bronnen in een beheerde app lezen en JIT-toegang aanvragen.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
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

### <a name="managed-services-registration-assignment-delete-role"></a>Rol voor registratie toewijzing beheerde services verwijderen

Met de functie voor registratie toewijzing van beheerde services verwijderen kan de Tenant gebruikers beheren de registratie toewijzing verwijderen die aan de Tenant is toegewezen. [Meer informatie](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/Read | Hiermee wordt een lijst met beheerde services registratie toewijzingen opgehaald. |
> | [Micro soft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/Delete | Hiermee wordt de registratie toewijzing van beheerde services verwijderd. |
> | [Micro soft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/Read | Hiermee wordt de bewerkings status van de resource gelezen. |
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

### <a name="management-group-contributor"></a>Inzender beheer groep

Rol Inzender beheer groep [meer informatie](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Delete | Beheer groep verwijderen. |
> | [Micro soft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | [Micro soft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Subscriptions/Delete | Het abonnement van de beheer groep wordt ontkoppeld. |
> | [Micro soft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Subscriptions/write | Het bestaande abonnement wordt gekoppeld aan de beheer groep. |
> | [Micro soft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/write | Een beheer groep maken of bijwerken. |
> | [Micro soft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Subscriptions/Read | Een lijst met abonnementen onder de opgegeven beheer groep. |
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

### <a name="management-group-reader"></a>Lezer beheer groep

Rol van lezer van beheer groep

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | [Micro soft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Subscriptions/Read | Een lijst met abonnementen onder de opgegeven beheer groep. |
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

### <a name="new-relic-apm-account-contributor"></a>Nieuwe Inzender voor Relic APM-account

Hiermee kunt u New Relic Application Performance Management accounts en-toepassingen beheren, maar niet de toegang tot ze.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | NewRelic. APM/accounts/* |  |
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

### <a name="policy-insights-data-writer-preview"></a>Policy Insights Data Writer (preview-versie)

Hiermee wordt lees toegang tot bron beleid en schrijf toegang tot bron onderdeel beleids gebeurtenissen toegestaan. [Meer informatie](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/Read | Informatie over een beleids toewijzing ophalen. |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/Read | Informatie over een beleids definitie ophalen. |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/Read | Informatie over een beleids uitsluiting ophalen. |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/Read | Informatie over een definitie van een beleidsset ophalen. |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/Action | Controleer de nalevings status van een bepaald onderdeel met gegevens beleid. |
> | [Micro soft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/Action | De gebeurtenissen van het bron onderdeel beleid registreren. |
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

### <a name="reservation-purchaser"></a>Reserve ring-verkoper

Hiermee kunt u reserve ringen kopen voor [meer informatie](../cost-management-billing/reservations/prepare-buy-reservation.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Read | Hiermee wordt de lijst met abonnementen opgehaald. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. capacity](resource-provider-operations.md#microsoftcapacity)/register/Action | Hiermee wordt de resource provider voor capaciteit geregistreerd en wordt het maken van capaciteits resources ingeschakeld. |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/register/Action | Hiermee wordt een abonnement geregistreerd bij de resource provider micro soft. compute |
> | [Micro soft. SQL](resource-provider-operations.md#microsoftsql)/register/Action | Hiermee wordt het abonnement voor de resource provider van micro soft SQL Database geregistreerd en wordt het maken van micro soft SQL-data bases mogelijk. |
> | [Micro soft. verbruik](resource-provider-operations.md#microsoftconsumption)/register/Action | Registreren voor gebruik van RP |
> | [Micro soft. capacity](resource-provider-operations.md#microsoftcapacity)/Catalogs/Read | Catalogus van reserve ring lezen |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/Read | Informatie over een roltoewijzing ophalen. |
> | [Micro soft. verbruik](resource-provider-operations.md#microsoftconsumption)/reservationRecommendations/Read | Een lijst met enkele of gedeelde aanbevelingen voor gereserveerde instanties voor een abonnement. |
> | [Micro soft. support](resource-provider-operations.md#microsoftsupport)/Supporttickets/write | Staat het maken en bijwerken van een ondersteunings ticket toe |
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

Gebruikers met rechten voor het maken/wijzigen van het resource beleid, het maken van een ondersteunings ticket en het lezen van resources/hiërarchie. [Meer informatie](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | */read | Lees resources van alle typen, met uitzonde ring van geheimen. |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | Beleids toewijzingen maken en beheren |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/* | Beleids definities maken en beheren |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/* | Beleids uitzonderingen maken en beheren |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | Beleids sets maken en beheren |
> | [Micro soft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

Hiermee kunt u Site Recovery-service beheren, behalve het maken van de kluis en roltoewijzing [meer informatie](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | De virtuele-netwerk definitie ophalen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/Read | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/Action | Allo Cate Stamp is een interne bewerking die wordt gebruikt door de service |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/certificates/write | Met de bewerking resource certificaat bijwerken wordt het resource/kluis-referentie certificaat bijgewerkt. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/extendedInformation/* | Uitgebreide informatie met betrekking tot de kluis maken en beheren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/Read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/refreshContainers/Read |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/registeredIdentities/* | Geregistreerde identiteiten maken en beheren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/* | Waarschuwings instellingen voor replicatie maken of bijwerken |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/Read | Gebeurtenissen lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/* | Replicatie-fabrics maken en beheren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Replicatie taken maken en beheren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/* | Replicatie beleid maken en beheren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/* | Herstel plannen maken en beheren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/storageConfig/* | Opslag configuratie van Recovery Services kluis maken en beheren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/tokenInfo/Read |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/usages/Read | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/vaultTokens/Read | De bewerking kluis token kan worden gebruikt om het kluis token voor back-upbewerkingen op kluis niveau op te halen. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/monitoringAlerts/* | Waarschuwingen voor de Recovery Services-kluis lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationOperationStatus/Read | De status van de kluis replicatie bewerking lezen |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

Met kunt u failover en failback uitvoeren, maar geen andere Site Recovery beheer bewerkingen [meer informatie](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | De virtuele-netwerk definitie ophalen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/Read | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/Action | Allo Cate Stamp is een interne bewerking die wordt gebruikt door de service |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/extendedInformation/Read | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/Read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/refreshContainers/Read |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/registeredIdentities/operationResults/Read | De bewerking resultaten van de bewerking ophalen kan worden gebruikt om de bewerkings status en het resultaat van de asynchroon ingediende bewerking op te halen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/registeredIdentities/Read | De bewerking containers ophalen kan worden gebruikt om de containers op te halen die voor een resource zijn geregistreerd. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/Read | Waarschuwings instellingen lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/Read | Gebeurtenissen lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/checkConsistency/Action | Hiermee wordt de consistentie van de infra structuur gecontroleerd |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/Read | Alle infra structuren lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/reassociateGateway/Action | Gateway opnieuw koppelen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/renewcertificate/Action | Certificaat voor Fabric vernieuwen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/Read | Alle netwerken lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Netwerk toewijzingen lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/Read | Beveiligings containers lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Beveilig bare items lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/Action | Herstel punt Toep assen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/Action | Failover door voeren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/Action | Geplande failover |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Alle beveiligde items lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/Read | Alle replicatie herstel punten lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/Action | Replicatie herstellen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/Action | Beveiligd item opnieuw beveiligen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/Action | Beveiligings container overschakelen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/Action | Failover testen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/Action | Failovertest opschonen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/Action | Failover |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/Action | Mobility-service bijwerken |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Alle beveiligings container toewijzingen lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/Read | Recovery Services Providers lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/Action | Provider vernieuwen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/Read | Alle opslag classificaties lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Alle opslag classificatie toewijzingen lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/Read | Alle vCenter lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Replicatie taken maken en beheren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/Read | Alle beleids regels lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/failoverCommit/Action | Herstel plan voor failover door voeren |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/plannedFailover/Action | Herstel plan voor geplande failover |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/Read | Herstel plannen lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/reProtect/Action | Herstel plan opnieuw beveiligen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailover/Action | Herstel plan voor failover testen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailoverCleanup/Action | Herstel plan voor het opschonen van de Failovertest |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/unplannedFailover/Action | Herstel plan voor failover |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/monitoringAlerts/* | Waarschuwingen voor de Recovery Services-kluis lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/storageConfig/Read |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/tokenInfo/Read |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/usages/Read | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/vaultTokens/Read | De bewerking kluis token kan worden gebruikt om het kluis token voor back-upbewerkingen op kluis niveau op te halen. |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

Hiermee kunt u de Site Recovery status weer geven, maar geen andere [beheer bewerkingen uitvoeren](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/Read | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/extendedInformation/Read | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/monitoringAlerts/Read | Hiermee worden de waarschuwingen voor de Recovery Services-kluis opgehaald. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/Read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/refreshContainers/Read |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/registeredIdentities/operationResults/Read | De bewerking resultaten van de bewerking ophalen kan worden gebruikt om de bewerkings status en het resultaat van de asynchroon ingediende bewerking op te halen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/registeredIdentities/Read | De bewerking containers ophalen kan worden gebruikt om de containers op te halen die voor een resource zijn geregistreerd. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/Read | Waarschuwings instellingen lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/Read | Gebeurtenissen lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/Read | Alle infra structuren lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/Read | Alle netwerken lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Netwerk toewijzingen lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/Read | Beveiligings containers lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Beveilig bare items lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Alle beveiligde items lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/Read | Alle replicatie herstel punten lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Alle beveiligings container toewijzingen lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/Read | Recovery Services Providers lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/Read | Alle opslag classificaties lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Alle opslag classificatie toewijzingen lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/Read | Alle vCenter lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/Read | Alle taken lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/Read | Alle beleids regels lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/Read | Herstel plannen lezen |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/storageConfig/Read |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/tokenInfo/Read |  |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/usages/Read | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | [Micro soft. Recovery Services](resource-provider-operations.md#microsoftrecoveryservices)/vaults/vaultTokens/Read | De bewerking kluis token kan worden gebruikt om het kluis token voor back-upbewerkingen op kluis niveau op te halen. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="support-request-contributor"></a>Inzender voor ondersteunings aanvragen

Hiermee kunt u ondersteunings aanvragen maken en beheren voor [meer informatie](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="tag-contributor"></a>Inzender labelen

Hiermee kunt u tags op entiteiten beheren zonder dat u toegang hebt tot de entiteiten zelf. [Meer informatie](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/resources/Read | Hiermee haalt u de resources voor de resource groep op. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resources/Read | Hiermee haalt u de resources van een abonnement op. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Tags/* |  |
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


### <a name="azure-digital-twins-data-owner"></a>Azure Digital Apparaatdubbels-gegevens eigenaar

Volledige toegang voor Digital Apparaatdubbels data-vlak meer [informatie](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/* | Gebeurtenis routes lezen, verwijderen, maken of bijwerken |
> | [Micro soft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/* | U kunt alle digitale dubbele items lezen, maken, bijwerken of verwijderen |
> | [Micro soft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/commands/* | Een wille keurige opdracht aanroepen op een digitale dubbele |
> | [Micro soft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/* | Een digitale dubbele relatie lezen, maken, bijwerken of verwijderen |
> | [Micro soft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/* | Een model lezen, maken, bijwerken of verwijderen |
> | [Micro soft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/* | Een wille keurige Digital Apparaatdubbels-grafiek opvragen |
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

### <a name="azure-digital-twins-data-reader"></a>Azure Digital Apparaatdubbels-gegevens lezer

Alleen-lezen rol voor Digital Apparaatdubbels data-vlak-eigenschappen [meer informatie](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Read | Alle digitale dubbele items lezen |
> | [Micro soft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/Read | Een digitale dubbele relatie lezen |
> | [Micro soft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/Read | Alle gebeurtenis routes lezen |
> | [Micro soft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/Read | Een model lezen |
> | [Micro soft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/Action | Een wille keurige Digital Apparaatdubbels-grafiek opvragen |
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

### <a name="biztalk-contributor"></a>BizTalk-bijdrager

Hiermee kunt u BizTalk Services beheren, maar niet de toegang tot de service.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | Micro soft. BizTalkServices/BizTalk/* | BizTalk Services maken en beheren |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="desktop-virtualization-application-group-contributor"></a>Inzender voor bureau blad-Virtualisatiehost

Bijdrager van de toepassings groep bureau blad-virtualisatie. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/* |  |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Hostpools lezen |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/Read | Hostpools/sessionhosts lezen |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="desktop-virtualization-application-group-reader"></a>Lezer van toepassings groep voor desktop-virtualisatie

Lezer van de toepassings groep bureau blad-virtualisatie. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/*/Read |  |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/Read | Applicationgroups lezen |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Hostpools lezen |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/Read | Hostpools/sessionhosts lezen |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Read | Hiermee wordt een lijst met implementaties opgehaald of weer gegeven. |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Een klassieke waarschuwing voor metrische gegevens lezen |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="desktop-virtualization-contributor"></a>Inzender voor bureau blad-virtualisatie

Inzender van bureau blad-virtualisatie. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/* |  |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="desktop-virtualization-host-pool-contributor"></a>Inzender voor RD Virtualization hostgroep

Inzender van de hostgroep voor bureau blad-virtualisatie. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/* |  |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="desktop-virtualization-host-pool-reader"></a>Lezer van hostgroep voor desktop-virtualisatie

Lezer van de hostgroep voor bureau blad-virtualisatie. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/*/Read |  |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Hostpools lezen |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Read | Hiermee wordt een lijst met implementaties opgehaald of weer gegeven. |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Een klassieke waarschuwing voor metrische gegevens lezen |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="desktop-virtualization-reader"></a>Desktop Virtualization-lezer

Lezer van bureau blad-virtualisatie. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/*/Read |  |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Read | Hiermee wordt een lijst met implementaties opgehaald of weer gegeven. |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Een klassieke waarschuwing voor metrische gegevens lezen |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="desktop-virtualization-session-host-operator"></a>Host-operator voor bureau blad-Virtualisatiehost

Operator van de host van de bureau blad-Virtualisatiehost. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Hostpools lezen |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/* |  |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="desktop-virtualization-user"></a>Gebruiker van bureau blad-virtualisatie

Hiermee kunnen gebruikers de toepassingen in een toepassings groep gebruiken. [Meer informatie](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | *geen* |  |
> | **NotActions** |  |
> | *geen* |  |
> | **DataActions** |  |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationGroups/useApplications/Action | Variabele applicationgroup gebruiken |
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

### <a name="desktop-virtualization-user-session-operator"></a>Operator voor de gebruikers sessie van de bureau blad-Virtualisatiehost

Operator van de Uesr-sessie voor bureau blad-virtualisatie. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Hostpools lezen |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/Read | Hostpools/sessionhosts lezen |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/usersessions/* |  |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="desktop-virtualization-workspace-contributor"></a>Mede werker van de werk ruimte bureau blad-virtualisatie

Inzender van de werk ruimte bureau blad-virtualisatie. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/Workspaces/* |  |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/Read | Applicationgroups lezen |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="desktop-virtualization-workspace-reader"></a>Werkruimte lezer voor desktop-virtualisatie

Lezer van de werk ruimte bureau blad-virtualisatie. [Meer informatie](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/Workspaces/Read | Werk ruimten lezen |
> | [Micro soft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/Read | Applicationgroups lezen |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Read | Hiermee wordt een lijst met implementaties opgehaald of weer gegeven. |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Een klassieke waarschuwing voor metrische gegevens lezen |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="disk-backup-reader"></a>Back-uplezer van schijf

Biedt machtiging voor back-upkluis voor het uitvoeren van schijf back-ups. [Meer informatie](../backup/disk-backup-faq.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/disks/Read | De eigenschappen van een schijf ophalen |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/Action | De SAS-URI van de schijf ophalen voor BLOB-toegang |
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

### <a name="disk-restore-operator"></a>Schijf herstel operator

Biedt machtigingen voor het maken van een back-upkluis om schijf herstel uit te voeren. [Meer informatie](../backup/restore-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/disks/write | Hiermee wordt een nieuwe schijf gemaakt of een bestaande bijgewerkt |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/disks/Read | De eigenschappen van een schijf ophalen |
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

### <a name="disk-snapshot-contributor"></a>Inzender voor schijf momentopnamen

Biedt machtiging voor back-upkluis voor het beheren van moment opnamen van schijven. [Meer informatie](../backup/backup-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/snapshots/Delete | Een moment opname verwijderen |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/snapshots/write | Een nieuwe moment opname maken of een bestaand abonnement bijwerken |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/snapshots/Read | De eigenschappen van een moment opname ophalen |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/snapshots/beginGetAccess/Action | De SAS-URI van de moment opname voor BLOB-toegang ophalen |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/snapshots/endGetAccess/Action | De SAS-URI van de moment opname intrekken |
> | [Micro soft. Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/Action | De SAS-URI van de schijf ophalen voor BLOB-toegang |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/Action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/write | Hiermee maakt u een opslag account met de opgegeven para meters of werkt u de eigenschappen of labels bij of voegt u een aangepast domein toe voor het opgegeven opslag account. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | [Micro soft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Delete | Hiermee verwijdert u een bestaand opslag account. |
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

### <a name="scheduler-job-collections-contributor"></a>Inzender voor scheduler-taak verzamelingen

Hiermee kunt u scheduler-taak verzamelingen beheren, maar niet de toegang tot deze taken.

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Een klassieke waarschuwing voor metrische gegevens maken en beheren |
> | [Micro soft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | Taak verzamelingen maken en beheren |
> | [Micro soft. ondersteuning](resource-provider-operations.md#microsoftsupport)/* | Een ondersteunings ticket maken en bijwerken |
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

### <a name="services-hub-operator"></a>Operator Services hub

Met de operator Services hub kunt u alle Lees-, schrijf-en verwijder bewerkingen uitvoeren met betrekking tot services hub-connectors. [Meer informatie](/services-hub/health/sh-connector-roles)

> [!div class="mx-tableFixed"]
> | Acties | Beschrijving |
> | --- | --- |
> | [Micro soft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Rollen en roltoewijzingen lezen |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | [Micro soft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Een implementatie maken en beheren |
> | [Micro soft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/write | Een service hub-connector maken of bijwerken |
> | [Micro soft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/Read | Service hub-connectors weer geven of vermelden |
> | [Micro soft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/Delete | Services hub-connectors verwijderen |
> | [Micro soft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/checkAssessmentEntitlement/Action | Geeft een lijst van de beoordelings rechten voor een bepaalde services hub-werk ruimte |
> | [Micro soft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/supportOfferingEntitlement/Read | De rechten van de ondersteunings aanbieding voor een bepaalde services hub-werk ruimte weer geven |
> | [Micro soft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/Workspaces/Read | De services hub-werk ruimten voor een bepaalde gebruiker weer geven |
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

- [Overeenkomende resource provider voor service](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Aangepaste Azure-rollen](custom-roles.md)
- [Machtigingen in Azure Security Center](../security-center/security-center-permissions.md)
