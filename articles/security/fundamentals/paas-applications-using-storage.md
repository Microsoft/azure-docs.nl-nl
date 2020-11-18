---
title: PaaS-toepassingen beveiligen met Azure Storage | Microsoft Docs
description: Meer informatie over Azure Storage aanbevolen procedures voor beveiliging bij het beveiligen van uw PaaS-web-en mobiele toepassingen.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: tomsh
ms.openlocfilehash: 082408f357e97a2ed2153d43dbea459ff09ba704
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94693177"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Aanbevolen procedures voor het beveiligen van PaaS web-en mobiele toepassingen met Azure Storage
In dit artikel bespreken we een verzameling Azure Storage best practices voor het beveiligen van uw PaaS-web en mobiele toepassingen (platform-as-a-Service). Deze aanbevolen procedures zijn afgeleid van onze ervaring met Azure en de ervaringen van klanten, zoals uzelf.

Azure maakt het mogelijk om opslag te implementeren en gebruiken op manieren die niet eenvoudig on-premises kunnen worden behaald. Met Azure Storage kunt u een hoge mate van schaal baarheid en beschik baarheid bereiken met relatief weinig moeite. Het is niet alleen Azure Storage de basis voor Windows-en Linux Azure Virtual Machines, maar biedt ook ondersteuning voor grote gedistribueerde toepassingen.

Azure Storage biedt de volgende vier services: Blob Storage, Table Storage, Queue Storage en file storage. Zie [Inleiding tot Microsoft Azure Storage](../../storage/common/storage-introduction.md)voor meer informatie.

De [Azure Storage-beveiligings handleiding](../../storage/blobs/security-recommendations.md) is een goede bron voor gedetailleerde informatie over Azure Storage en beveiliging. In deze best practices wordt een groot aantal concepten besproken die zijn gevonden in de beveiligings handleiding en koppelingen naar de beveiligings handleiding, evenals andere bronnen, voor meer informatie.

Dit artikel heeft betrekking op de volgende aanbevolen procedures:

- Shared Access Signatures (SAS)
- Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)
- Versleuteling aan client zijde voor gegevens met hoge waarde
- Storage Service Encryption


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Een Shared Access Signature gebruiken in plaats van een opslag account sleutel
Toegangs beheer is essentieel. Om u te helpen bij het beheren van de toegang tot Azure Storage, genereert Azure 2 512-bits SAKs (Storage account Keys) wanneer u een opslag account maakt. Het niveau van sleutel redundantie maakt het u mogelijk om service onderbrekingen te voor komen tijdens het draaien van routine sleutels. 

Opslag toegangs sleutels zijn geheimen met een hoge prioriteit en mogen alleen toegankelijk zijn voor degenen die verantwoordelijk zijn voor toegangs beheer voor opslag. Als de verkeerde personen toegang krijgen tot deze sleutels, hebben ze de volledige controle over de opslag en kunnen ze bestanden vervangen, verwijderen of toevoegen aan de opslag. Dit omvat malware en andere soorten inhoud die uw organisatie of uw klanten mogelijk kunnen binnenbrengen.

U hebt nog steeds een manier nodig om toegang te bieden tot objecten in de opslag. Als u meer gedetailleerde toegang wilt bieden, kunt u gebruikmaken van Shared Access Signature (SAS). De SAS maakt het u mogelijk om specifieke objecten in de opslag te delen voor een vooraf gedefinieerd tijds interval en met specifieke machtigingen. Met een Shared Access Signature kunt u het volgende definiëren:

- Het interval waarover de SA'S geldig zijn, met inbegrip van de begin tijd en de verloop tijd.
- De machtigingen die door de SAS worden verleend. Een SAS op een BLOB kan bijvoorbeeld lees-en schrijf machtigingen van een gebruiker verlenen aan die blob, maar geen machtigingen voor verwijderen.
- Een optioneel IP-adres of een bereik met IP-adressen waarvan de SA'S door Azure Storage worden geaccepteerd. U kunt bijvoorbeeld een bereik van IP-adressen opgeven die deel uitmaken van uw organisatie. Dit biedt een andere beveiligings maatregel voor uw SA'S.
- Het protocol waarover Azure Storage de SAS accepteert. U kunt deze optionele para meter gebruiken om de toegang tot clients te beperken met behulp van HTTPS.

Met SAS kunt u inhoud delen op de manier die u wilt delen zonder dat u de sleutels van uw opslag account hoeft op te geven. Het gebruik van SAS in uw toepassing is een veilige manier om uw opslag resources te delen zonder in te boeten voor de sleutels van uw opslag account.

Zie [using Shared Access signatures](../../storage/common/storage-sas-overview.md)(Engelstalig) voor meer informatie over de hand tekening voor gedeelde toegang. 

## <a name="use-azure-role-based-access-control"></a>Toegangs beheer op basis van rollen gebruiken
Een andere manier om toegang te beheren is door [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/overview.md)te gebruiken. Met Azure RBAC kunt u zich richten op het geven van de exacte machtigingen die ze nodig hebben, op basis van de beveiligings principes van de nood zaak om te kennen en te voldoen aan de minimale bevoegdheden. Te veel machtigingen kunnen een account bloot stellen aan kwaadwillende personen. Te weinig machtigingen betekenen dat werk nemers hun werk niet efficiënt kunnen uitvoeren. Azure RBAC helpt dit probleem op te lossen door nauw keurig toegangs beheer voor Azure aan te bieden. Dit is van cruciaal belang voor organisaties die beveiligings beleid voor gegevens toegang willen afdwingen.

U kunt ingebouwde Azure-rollen in azure gebruiken om machtigingen toe te wijzen aan gebruikers. Gebruik bijvoorbeeld Inzender voor opslag accounts voor Cloud operators die opslag accounts en de rol Inzender voor klassieke opslag accounts moeten beheren voor het beheren van klassieke opslag account. Voor Cloud operators die virtuele machines moeten beheren, maar niet het virtuele netwerk of het opslag account waarmee ze zijn verbonden, kunt u deze toevoegen aan de rol Inzender voor virtuele machines.

Organisaties die geen gegevens toegangs beheer afdwingen door gebruik te maken van mogelijkheden als Azure RBAC, kunnen meer bevoegdheden geven dan nodig zijn voor hun gebruikers. Dit kan leiden tot inbreuk op gegevens door bepaalde gebruikers toegang te geven tot gegevens die ze niet in de eerste plaats hoeven te hebben.

Zie voor meer informatie over Azure RBAC:

- [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure-portal](../../role-based-access-control/role-assignments-portal.md)
- [Ingebouwde Azure-rollen](../../role-based-access-control/built-in-roles.md)
- [Azure Storage-beveiligingshandleiding](../../storage/blobs/security-recommendations.md) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Versleuteling aan client zijde gebruiken voor gegevens met hoge waarde
Met versleuteling aan de client zijde kunt u gegevens in transit programmatisch versleutelen voordat u deze uploadt naar Azure Storage, en gegevens programmatisch ontsleutelen bij het ophalen. Dit zorgt voor versleuteling van gegevens tijdens de overdracht, maar biedt ook versleuteling van gegevens in rust. Versleuteling aan de client zijde is de veiligste methode voor het versleutelen van uw gegevens, maar hiervoor moet u programmatisch wijzigingen aanbrengen in uw toepassing en de belangrijkste beheer processen plaatsen.

Versleuteling aan de client zijde biedt u ook de mogelijkheid om de versleutelings sleutels te beheren. U kunt uw eigen versleutelings sleutels genereren en beheren. Er wordt gebruikgemaakt van een envelop techniek waarbij de Azure Storage-client bibliotheek een CEK (content Encryption Key) genereert die vervolgens wordt verpakt (versleuteld) met behulp van de sleutel versleutelings sleutel (KEK). De KEK wordt geïdentificeerd aan de hand van een sleutel-id en kan een asymmetrisch sleutel paar of een symmetrische sleutel zijn en kan lokaal worden beheerd of opgeslagen in [Azure Key Vault](../../key-vault/general/overview.md).

Versleuteling aan client zijde is ingebouwd in de Java-en de .NET Storage-client bibliotheken. Zie [versleuteling aan client zijde en Azure Key Vault voor Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md) voor informatie over het versleutelen van gegevens in client toepassingen en het genereren en beheren van uw eigen versleutelings sleutels.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Storage Service Encryption inschakelen voor Data-at-rest
Als [Storage service Encryption](../../storage/common/storage-service-encryption.md) voor File Storage is ingeschakeld, worden de gegevens automatisch versleuteld met AES-256-versleuteling. Micro soft handelt alle versleuteling, ontsleuteling en sleutel beheer af. Deze functie is beschikbaar voor LRS-en GRS-redundantie typen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een verzameling van Azure Storage aanbevolen beveiligings procedures geïntroduceerd voor het beveiligen van uw PaaS-webtoepassingen en mobiele toepassingen. Zie voor meer informatie over het beveiligen van uw PaaS-implementaties:

- [PaaS-implementaties beveiligen](paas-deployments.md)
- [PaaS-webtoepassingen en mobiele toepassingen beveiligen met Azure-app Services](paas-applications-using-app-services.md)
- [PaaS-databases beveiligen in Azure](paas-applications-using-sql.md)