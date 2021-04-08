---
title: Overzicht van Azure Blockchain Workbench (preview-versie)
description: Hier vindt u een overzicht van Azure Blockchain Workbench (preview-versie) en de mogelijkheden ervan.
ms.date: 05/22/2020
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: fbd6be3907dbd10b003d065dfb14031a0e378478
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87003141"
---
# <a name="what-is-azure-blockchain-workbench"></a>Wat is Azure Blockchain Workbench?

Azure Blockchain Workbench (preview-versie) is een verzameling Azure-services en -functies die zijn ontworpen om u te helpen bij het maken en implementeren van blockchain-toepassingen voor het delen van bedrijfsprocessen en gegevens met andere organisaties. Azure Blockchain Workbench biedt de infrastructuur-scaffolding voor het bouwen van blockchain-toepassingen, zodat ontwikkelaars zich kunnen richten op het maken van bedrijfslogica en slimme contracten. Met Blockchain Workbench wordt het ook eenvoudiger om blockchain-toepassingen te maken door verschillende Azure-services en -functies te integreren om zo algemene ontwikkeltaken te automatiseren.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="create-blockchain-applications"></a>Blockchain-toepassingen maken

U kunt met Blockchain Workbench blockchain-toepassingen definiëren met behulp van configuratie en door code voor slimme contracten te schrijven. U kunt de ontwikkeling van blockchains een boost geven en richten op het definiëren van contracten en het schrijven van bedrijfslogica, in plaats van scaffolding te bouwen en ondersteunende services in te stellen.

## <a name="manage-applications-and-users"></a>Toepassingen en gebruikers beheren

Azure Blockchain Workbench biedt een webtoepassing en REST-API's voor het beheren van blockchain-toepassingen en -gebruikers. Beheerders van Blockchain Workbench kunnen de toegang tot toepassingen beheren en toepassingsrollen toewijzen aan uw gebruikers. Gebruikers van Azure AD worden automatisch toegewezen aan leden in de toepassing.

## <a name="integrate-blockchain-with-applications"></a>Blockchain integreren met toepassingen

U kunt de REST-API's van Blockchain Workbench en API's op berichtbasis integreren met bestaande systemen. De API's bieden een interface die het mogelijk maakt om meerdere technologieën voor een gedistribueerd grootboek, opslag en databaseaanbiedingen te vervangen of gebruiken.

Blockchain Workbench kan berichten die zijn verzonden naar de API op berichtbasis, transformeren in transacties met een indeling die wordt verwacht door de native API van die blockchain.  Workbench kan transacties ondertekenen en naar de juiste blockchain routeren. 

Gebeurtenissen worden door Workbench automatisch afgeleverd aan Service Bus en Event Grid om berichten te verzenden naar downstream-consumenten. Ontwikkelaars kunnen hun code integreren met een van deze berichtsystemen om transacties te faciliteren en de resultaten te bekijken.

## <a name="deploy-a-blockchain-network"></a>Een blockchain-netwerk implementeren

Azure Blockchain Workbench vereenvoudigt het instellen van een blockchain-netwerk voor een consortium door een vooraf geconfigureerde oplossing aan te bieden in de vorm van een Azure Resource Manager-oplossingssjabloon. De sjabloon biedt vereenvoudigde implementatie van alle onderdelen die nodig zijn voor het exploiteren van een consortium. Op dit moment wordt alleen Ethereum ondersteund door Blockchain Workbench.

## <a name="use-active-directory"></a>Active Directory gebruiken

Bij bestaande blockchain-protocollen worden blockchain-identiteiten voorgesteld als een adres in het netwerk. Azure Blockchain Workbench abstraheert de blockchain-identiteit door deze te koppelen aan een Active Directory-identiteit, zodat het eenvoudiger wordt om bedrijfstoepassingen met Active Directory-identiteiten te bouwen.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>Gegevens in de blockchain synchroniseren met opslag buiten de blockchain

Met Azure Blockchain Workbench is het eenvoudiger om blockchain-gebeurtenissen en -gegevens te analyseren doordat gegevens in de blockchain automatisch worden gesynchroniseerd naar opslag buiten de blockchain. In plaats van de gegevens rechtstreeks uit de blockchain te extraheren, kunt u query's uitvoeren op databasesystemen buiten de blockchain, zoals SQL Server. Er is geen kennis van de blockchain vereist voor eindgebruikers die gegevensanalyse uitvoeren.

## <a name="support-and-feedback"></a>Ondersteuning en feedback

Voor nieuws voer Azure Blockchain gaat u naar de [Azure Blockchain-blog](https://azure.microsoft.com/blog/topics/blockchain/) om op de hoogte te blijven van aanbiedingen van blockchainservices en informatie van het technische team van Azure Blockchain.

Als u feedback over producten wilt geven of nieuwe functies wilt aanvragen, kunt u een idee plaatsen of erop stemmen via het [Azure-feedbackforum voor blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Ondersteuning voor community

In contact komen met Microsoft-technici en experts uit de Azure Blockchain-community.

* [Microsoft Q&A-vragenpagina voor Azure Blockchain Workbench](/answers/topics/azure-blockchain-workbench.html)
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Architectuur van Azure Blockchain Workbench](architecture.md)
