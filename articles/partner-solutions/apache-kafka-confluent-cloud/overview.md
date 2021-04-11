---
title: Apache Kafka op confluente Cloud Overview-Azure-partner oplossingen
description: Meer informatie over het gebruik van Apache Kafka op confluente Cloud in de Azure Marketplace.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 01/15/2021
ms.author: tomfitz
ms.openlocfilehash: fefbc21c385e3beacbf570c31ffbf97238c780fc
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109081"
---
# <a name="what-is-apache-kafka-for-confluent-cloud"></a>Wat is Apache Kafka voor confluente Cloud?

Apache Kafka voor confluente Cloud is een Azure Marketplace-aanbieding die Apache Kafka als een service biedt. Het wordt volledig beheerd, zodat u zich kunt richten op het bouwen van uw toepassingen in plaats van de clusters te beheren.

Om de belasting van platformoverschrijdende beheer te verminderen, verstuurt micro soft een partner met confluente Cloud voor het bouwen van een geïntegreerde inrichtings laag van Azure tot confluente Cloud. Het biedt een geconsolideerde ervaring voor het gebruik van confluente Cloud op Azure. U kunt confluente Cloud eenvoudig integreren en beheren met uw Azure-toepassingen.

Voorheen moest u het inzoomen van de cloud in de Marketplace aanschaffen en het account afzonderlijk instellen in confluente Cloud. Als u configuraties en bronnen wilt beheren, moet u navigeren tussen de portals voor Azure en confluente Cloud.

Nu kunt u de confluente cloud resources inrichten via een resource provider met de naam **micro soft. Confluent**. U kunt confluente Cloud-organisatie resources maken en beheren via de [Azure Portal](https://portal.azure.com/), [Azure cli](/cli/azure/)of [Azure sdk's](/azure/#languages-and-tools). Confluente Cloud is eigenaar van en voert de software as a Service (SaaS)-toepassing uit, met inbegrip van de omgevingen, clusters, onderwerpen, API-sleutels en beheerde connectors.

## <a name="capabilities"></a>Functies

De grondige integratie tussen de confluente Cloud en Azure biedt de volgende mogelijkheden:

- Richt een nieuwe confluente resource van de Cloud organisatie in van de Azure Portal met volledig beheerde infra structuur.
- U kunt eenmalige aanmelding (SSO) van Azure stroom lijnen om Cloud te confluenten met Azure Active Directory (Azure AD). Er is geen afzonderlijke verificatie nodig vanuit de Confluent Cloud Portal.
- Profiteer van de Unified Billing van het gebruik van de Cloud door Azure-abonnements facturering.
- Confluente cloud resources beheren vanuit de Azure Portal en deze bijhouden op de pagina **alle resources** met uw andere Azure-resources.

## <a name="confluent-organization"></a>Confluent-organisatie

Een confluente organisatie is een resource die de toewijzing van de Azure-en confluentie van Cloud bronnen verschaft. Het is de bovenliggende resource voor andere confluente cloud resources.

Elk Azure-abonnement kan meerdere confluente plannen bevatten. Elk Confluent-abonnement wordt toegewezen aan een gebruikers account en organisatie in de confluente Portal. Binnen elke confluente organisatie kunt u meerdere omgevingen, clusters, onderwerpen en connectors maken.

Wanneer u een confluente Cloud resource in azure inricht, krijgt u een confluente organisatie-ID, standaard omgeving en gebruikers account. Zie [Snelstartgids: aan de slag met confluente Cloud op Azure](create.md)voor meer informatie.

Voor de facturering wordt elke in de Marketplace gekochte Cloud aanbieding voor een unieke, confluente organisatie toegewezen.

## <a name="single-sign-on"></a>Eenmalige aanmelding

Wanneer u zich aanmeldt bij de Azure Portal, worden uw referenties ook gebruikt om u aan te melden bij de confluente Cloud SaaS-Portal. De ervaring maakt gebruik van [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) en [Azure AD SSO](../../active-directory/manage-apps/what-is-single-sign-on.md) om u te voorzien van een veilige en handige manier om u aan te melden.

Zie [eenmalige aanmelding](manage.md#single-sign-on)voor meer informatie.

## <a name="billing"></a>Billing

Er zijn twee facturerings opties beschikbaar: maandelijks abonnement op basis van betalen naar gebruik en toezeggings plan.

- Met het **maandelijks abonnement betalen per gebruik** ontvangt u de kosten voor het gebruik van de cloud in uw maandelijkse factuur van Azure.
- Met een **toezeggings plan** meldt u zich aan voor een minimum hoeveelheid en krijgt u een korting op uw doorgevoerde gebruik van de confluente Cloud.

U bepaalt welke facturerings optie u wilt gebruiken bij het maken van de service.

## <a name="connector-to-azure-cosmos-db"></a>Connector naar Azure Cosmos DB

Installeer vanuit de client voor de Confluent-hub de Cosmos DB-connector zoals aanbevolen in de [lijst Confluent hub](https://www.confluent.io/hub/microsoftcorporation/kafka-connect-cosmos). 

Als u de connector hand matig wilt installeren, downloadt u eerst een uber JAR van de [pagina Cosmos DB releases](https://github.com/microsoft/kafka-connect-cosmosdb/releases). U kunt ook [uw eigen uber-jar rechtstreeks vanuit de bron code maken](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector). Voltooi de installatie door de richt lijnen te volgen die worden beschreven in de Confluent-documentatie voor het [hand matig installeren van connectors](https://docs.confluent.io/home/connect/install.html#install-connector-manually).  

## <a name="confluent-links"></a>Confluente koppelingen

Zie de volgende koppelingen naar de [confluente site](https://docs.confluent.io/home/overview.html)voor meer informatie over het gebruik van Apache Kafka voor confluente Cloud.

Zie voor meer informatie over facturerings opties:

* [Azure Marketplace met betalen per gebruik](https://docs.confluent.io/cloud/current/billing/ccloud-azure-payg.html)
* [Azure Marketplace met toezeg gingen](https://docs.confluent.io/cloud/current/billing/ccloud-azure-ubb.html)

Zie voor meer informatie over het beheren van de oplossingen:

* [Een cluster maken in confluente Cloud](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)
* [Confluente Cloud omgevingen](https://docs.confluent.io/current/cloud/using/environments.html)
* [Basis beginselen van de Cloud](https://docs.confluent.io/current/cloud/using/cloud-basics.html)

Voor ondersteuning en voor waarden raadpleegt u:

* [Ondersteuning voor confluentiteit](https://support.confluent.io)
* [Service voorwaarden](https://www.confluent.io/confluent-cloud-tos).

## <a name="next-steps"></a>Volgende stappen

Voor het maken van een instantie van Apache Kafka voor confluente Cloud raadpleegt u [Quick Start: aan de slag met confluente Cloud op Azure](create.md).
