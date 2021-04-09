---
title: Azure Machine Learning Studio inschakelen in een virtueel netwerk
titleSuffix: Azure Machine Learning
description: Meer informatie over het configureren van Azure Machine Learning Studio om toegang te krijgen tot gegevens die zijn opgeslagen in een virtueel netwerk.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperf-fy20q4, tracking-python
ms.openlocfilehash: da8007a651b62430055f263f082fabf2aa4bf610
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574285"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Azure Machine Learning Studio gebruiken in een virtueel Azure-netwerk

In dit artikel leert u hoe u Azure Machine Learning Studio kunt gebruiken in een virtueel netwerk. De Studio bevat functies zoals AutoML, de ontwerper en gegevens labeling. Als u deze functies in een virtueel netwerk wilt gebruiken, moet u de stappen in dit artikel volgen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> - De Studio toegang geven tot gegevens die zijn opgeslagen in een virtueel netwerk.
> - Toegang tot de Studio vanaf een resource binnen een virtueel netwerk.
> - Krijg inzicht in de manier waarop de Studio opslag beveiliging beïnvloedt.

Dit artikel is deel vijf uit een serie van vijf delen die u begeleidt bij het beveiligen van een Azure Machine Learning werk stroom. We raden u ten zeerste aan de vorige onderdelen te lezen om een virtuele netwerk omgeving in te stellen.

Zie de andere artikelen in deze serie:

[1. VNet-overzicht](how-to-network-security-overview.md)  >  [2. Beveilig de werk ruimte](how-to-secure-workspace-vnet.md)  >  [3. De trainings omgeving](how-to-secure-training-vnet.md)  >  [4 beveiligen. Beveilig de vergoedings omgeving](how-to-secure-inferencing-vnet.md)  >  **5. De functionaliteit van Studio inschakelen**


> [!IMPORTANT]
> Als uw werk ruimte zich in een __soevereine Cloud__ bevindt, zoals Azure Government of Azure China 21vianet, bieden geïntegreerde notebooks _geen_ ondersteuning voor het gebruik van opslag die zich in een virtueel netwerk bevindt. In plaats daarvan kunt u Jupyter-notebooks van een rekenproces gebruiken. Zie de sectie [toegang tot gegevens in een reken instantie-notitie blok](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

+ Lees het [overzicht van netwerk beveiliging](how-to-network-security-overview.md) voor inzicht in algemene scenario's en architectuur voor het virtuele netwerk.

+ Een bestaand virtueel netwerk en subnet dat moet worden gebruikt.

+ Een bestaande [Azure machine learning-werk ruimte waarvoor een persoonlijke koppeling is ingeschakeld](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ [Het virtuele netwerk is toegevoegd](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints)met een bestaand Azure Storage-account.

## <a name="configure-data-access-in-the-studio"></a>Gegevens toegang configureren in de Studio

Sommige functies van Studio zijn standaard uitgeschakeld in een virtueel netwerk. Als u deze functies weer wilt inschakelen, moet u beheerde identiteit inschakelen voor opslag accounts die u wilt gebruiken in de Studio. 

De volgende bewerkingen zijn standaard uitgeschakeld in een virtueel netwerk:

* Bekijk de gegevens in de Studio.
* Gegevens visualiseren in de ontwerp functie.
* Implementeer een model in de ontwerp functie ([standaard opslag account](#enable-managed-identity-authentication-for-default-storage-accounts)).
* Een AutoML-experiment ([standaard opslag account](#enable-managed-identity-authentication-for-default-storage-accounts)) verzenden.
* Een label project starten.

De Studio ondersteunt het lezen van gegevens uit de volgende gegevensopslag typen in een virtueel netwerk:

* Azure Blob
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

### <a name="configure-datastores-to-use-workspace-managed-identity"></a>Data stores configureren voor het gebruik van door werk ruimte beheerde identiteit

Nadat u een Azure-opslag account hebt toegevoegd aan uw virtuele netwerk met een [service-eind punt](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) of een [persoonlijk eind punt](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints), moet u uw gegevens opslag configureren voor het gebruik van [beheerde identiteits](../active-directory/managed-identities-azure-resources/overview.md) verificatie. Hierdoor kunnen de Studio-gegevens in uw opslag account worden geopend.

Azure Machine Learning maakt gebruik van [data stores](concept-data.md#datastores) om verbinding te maken met opslag accounts. Gebruik de volgende stappen om een gegevens opslag te configureren voor het gebruik van beheerde identiteiten:

1. Selecteer __gegevens opslag__ in de Studio.

1. Als u een bestaande gegevens opslag wilt bijwerken, selecteert u de gegevens opslag en selecteert u __referenties bijwerken__.

    Selecteer __+ Nieuw gegevens archief__ als u een nieuwe gegevens opslag wilt maken.

1. Selecteer in de instellingen van Data Store de optie __Ja__ voor het  __gebruik van werk ruimte beheerde identiteit voor gegevens weergave en profileren in azure machine learning Studio__.

    ![Scherm afbeelding die laat zien hoe identiteit van beheerde werk ruimte wordt ingeschakeld](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

Met deze stappen wordt de door de werk ruimte beheerde identiteit als __lezer__ aan de opslag service toegevoegd met behulp van Azure RBAC. Met __Reader__ toegang kan de werk ruimte Firewall instellingen ophalen om ervoor te zorgen dat gegevens het virtuele netwerk niet verlaten. Het kan tot tien minuten duren voordat wijzigingen zijn doorgevoerd.

### <a name="enable-managed-identity-authentication-for-default-storage-accounts"></a>Beheerde identiteits verificatie inschakelen voor standaard opslag accounts

Elke Azure Machine Learning werk ruimte heeft twee standaard opslag accounts, een standaard-Blob Storage-account en een standaard account voor het opslaan van bestanden die worden gedefinieerd wanneer u uw werk ruimte maakt. U kunt ook nieuwe standaard instellingen instellen op de pagina **Data Store** -beheer.

![Scherm afbeelding die laat zien waar standaard-gegevens opslag kan worden gevonden](./media/how-to-enable-studio-virtual-network/default-datastores.png)

In de volgende tabel wordt beschreven waarom u beheerde identiteits verificatie moet inschakelen voor de standaard opslag accounts voor uw werk ruimte.

|Storage-account  | Notities  |
|---------|---------|
|Standaard-Blob-opslag voor werk ruimte| Hierin worden model assets van de ontwerp functie opgeslagen. U moet beheerde identiteits verificatie inschakelen voor dit opslag account om modellen te implementeren in de ontwerp functie. <br> <br> U kunt een designer-pijp lijn visualiseren en uitvoeren als er een niet-standaard gegevens opslag wordt gebruikt die is geconfigureerd voor het gebruik van beheerde identiteit. Als u echter probeert een getraind model te implementeren zonder beheerde identiteit in te scha kelen op de standaard gegevens opslag, mislukt de implementatie ongeacht andere gegevens opslag die in gebruik zijn.|
|Standaard bestands Archief voor de werk ruimte| Hiermee worden AutoML-experiment-assets opgeslagen. U moet beheerde identiteits verificatie inschakelen voor dit opslag account om AutoML experimenten in te dienen. |

> [!WARNING]
> Er is een bekend probleem waarbij de standaard bestands opslag niet automatisch de map maakt `azureml-filestore` , die vereist is voor het verzenden van AutoML experimenten. Dit gebeurt wanneer gebruikers tijdens het maken van de werk ruimte een bestaande File Store instellen als de standaard File Store.
> 
> Om dit probleem te voor komen, hebt u twee opties: 1) gebruik de standaard File Store die automatisch wordt gemaakt voor het maken van werk ruimten. 2) als u uw eigen File Store wilt maken, moet u ervoor zorgen dat de File Store zich buiten het VNet bevindt tijdens het maken van de werk ruimte. Nadat de werk ruimte is gemaakt, voegt u het opslag account toe aan het virtuele netwerk.
>
> U kunt dit probleem oplossen door het File Store-account uit het virtuele netwerk te verwijderen en vervolgens weer toe te voegen aan het virtuele netwerk.

### <a name="grant-workspace-managed-identity-__reader__-access-to-storage-private-link"></a>Toegang tot de beheerde __identiteit van__ de werk ruimte verlenen aan de persoonlijke opslag koppeling

Als uw Azure-opslag account gebruikmaakt van een persoonlijk eind punt, moet u de toegang tot de werkruimte beheerde identiteits **lezer** verlenen aan de privé-koppeling. Zie de ingebouwde rol van [lezer](../role-based-access-control/built-in-roles.md#reader) voor meer informatie. 

Als uw opslag account gebruikmaakt van een service-eind punt, kunt u deze stap overs Laan.

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Toegang tot de Studio vanaf een resource binnen het VNet

Als u de Studio opent vanuit een resource binnen een virtueel netwerk (bijvoorbeeld een reken instantie of virtuele machine), moet u uitgaand verkeer van het virtuele netwerk naar de Studio toestaan. 

Als u bijvoorbeeld netwerk beveiligings groepen (NSG) gebruikt om uitgaand verkeer te beperken, voegt __u een regel__ toe aan een servicetag bestemming __AzureFrontDoor.__ front-end.

## <a name="technical-notes-for-managed-identity"></a>Technische opmerkingen voor beheerde identiteit

Het gebruik van beheerde identiteit voor toegang tot opslag Services is van invloed op de beveiligings overwegingen. In deze sectie worden de wijzigingen voor elk type opslag account beschreven. 

Deze overwegingen zijn uniek voor het __type opslag account__ dat u wilt openen.

### <a name="azure-blob-storage"></a>Azure Blob Storage

Voor __Azure Blob Storage__ wordt de door de werk ruimte beheerde identiteit ook toegevoegd als een [gegevens lezer voor blobs](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) , zodat deze gegevens uit de Blob-opslag kan lezen.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Toegangs beheer Azure Data Lake Storage Gen2

U kunt zowel de toegangs beheer lijsten (Acl's) van Azure RBAC als POSIX-stijl gebruiken om de toegang tot gegevens binnen een virtueel netwerk te beheren.

Als u Azure RBAC wilt gebruiken, voegt u de door de werk ruimte beheerde identiteit toe aan de rol [BLOB data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Zie [Op rollen gebaseerd toegangsbeheer in Azure](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control) voor meer informatie.

Als u Acl's wilt gebruiken, kan de door de werk ruimte beheerde identiteit toegang krijgen net als elk ander beveiligings principe. Zie [toegangs beheer lijsten voor bestanden en mappen](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)voor meer informatie.

### <a name="azure-data-lake-storage-gen1-access-control"></a>Toegangs beheer Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 ondersteunt alleen Access Control Lists in POSIX-stijl. U kunt de door de werk ruimte beheerde identiteits toegang toewijzen aan bronnen net als andere beveiligings principes. Zie [toegangs beheer in azure data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md)voor meer informatie.

### <a name="azure-sql-database-contained-user"></a>Azure SQL Database opgenomen gebruiker

Als u toegang wilt krijgen tot gegevens die zijn opgeslagen in een Azure SQL Database met behulp van beheerde identiteit, moet u een SQL-Inge sloten gebruiker maken die is gekoppeld aan de beheerde identiteit. Zie voor meer informatie over het maken van een gebruiker van een externe provider [opgenomen gebruikers maken die zijn toegewezen aan Azure AD-identiteiten](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Nadat u een SQL-Inge sloten gebruiker hebt gemaakt, moet u er machtigingen voor verlenen met behulp van de [opdracht T-SQL toewijzen](/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="azure-machine-learning-designer-intermediate-module-output"></a>Uitvoer van de tussenliggende module van Azure Machine Learning Designer

U kunt de uitvoer locatie opgeven voor elke module in de ontwerp functie. Gebruik dit om tussenliggende gegevens sets op te slaan op een afzonderlijke locatie voor beveiliging, logboek registratie of controle doeleinden. Uitvoer opgeven:

1. Selecteer de module waarvan u de uitvoer wilt opgeven.
1. In het deel venster module-instellingen dat rechts wordt weer gegeven, selecteert u **uitvoer instellingen**.
1. Geef de gegevens opslag op die u voor elke module-uitvoer wilt gebruiken.
 
Zorg ervoor dat u toegang hebt tot de tussenliggende opslag accounts in uw virtuele netwerk. Anders mislukt de pijp lijn.

U moet ook [beheerde identiteits verificatie inschakelen](#configure-datastores-to-use-workspace-managed-identity) voor tussenliggende opslag accounts om uitvoer gegevens te visualiseren.

## <a name="next-steps"></a>Volgende stappen

Dit artikel is deel vijf uit een virtuele netwerk reeks van vijf delen. Raadpleeg de rest van de artikelen voor meer informatie over het beveiligen van een virtueel netwerk:

* [Deel 1: overzicht van virtueel netwerk](how-to-network-security-overview.md)
* [Deel 2: de resources van de werk ruimte beveiligen](how-to-secure-workspace-vnet.md)
* [Deel 3: de trainings omgeving beveiligen](how-to-secure-training-vnet.md)
* [Deel 4: de omgeving voor het afwijzen van interferentie beveiligen](how-to-secure-inferencing-vnet.md)

Zie ook het artikel over het gebruik van [aangepaste DNS](how-to-custom-dns.md) voor naam omzetting.