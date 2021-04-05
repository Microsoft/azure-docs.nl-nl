---
title: Beheerhub
description: Uw verbindingen, broncode beheer configuratie en algemene ontwerp eigenschappen beheren in de Azure Data Factory Management hub
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.date: 02/01/2021
ms.openlocfilehash: b4b9ecef84f8ffcc82107299ad6603466380d1c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371495"
---
# <a name="management-hub-in-azure-data-factory"></a>Beheer hub in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

De beheer-hub, toegankelijk via het tabblad *beheren* in de Azure Data Factory UX, is een portal die de algemene beheer acties voor uw Data Factory host. Hier kunt u uw verbindingen met gegevens archieven en externe berekeningen, configuratie van broncode beheer en trigger instellingen beheren.

## <a name="manage-connections"></a>Verbindingen beheren

### <a name="linked-services"></a>Gekoppelde services

Gekoppelde services definiëren de verbindings gegevens voor Azure Data Factory om verbinding te maken met externe gegevens opslag en reken omgevingen. Zie [concepten van gekoppelde services](concepts-linked-services.md)voor meer informatie. Het maken, bewerken en verwijderen van gekoppelde services vindt plaats in de Management hub.

![Gekoppelde services beheren](media/author-management-hub/management-hub-linked-services.png)

### <a name="integration-runtimes"></a>Integration Runtimes

Een Integration runtime is een reken infrastructuur die wordt gebruikt door Azure Data Factory om mogelijkheden voor gegevens integratie in verschillende netwerk omgevingen te bieden. Meer informatie vindt u in [concepten over Integration runtime](concepts-integration-runtime.md). In de Management hub kunt u integratie-Runtimes maken, verwijderen en bewaken.

![Integratieruntime beheren](media/author-management-hub/management-hub-integration-runtime.png)

## <a name="manage-source-control"></a>Broncode beheer beheren

### <a name="git-configuration"></a>Git-configuratie

U kunt alle informatie over git weer geven/bewerken onder de configuratie-instellingen voor git in de Management hub. 

Informatie over de laatste gepubliceerde door Voer wordt ook weer gegeven en kan helpen om inzicht te krijgen in de precieze door voering, die voor het laatst is gepubliceerd/geïmplementeerd in omgevingen. Het kan ook handig zijn bij het uitvoeren van hotfixes tijdens de productie.

Meer informatie over [broncode beheer vindt u in azure Data Factory](source-control.md).

![Git-opslag plaats beheren](media/author-management-hub/management-hub-git.png)

### <a name="parameterization-template"></a>Parameterisering-sjabloon

Als u de gegenereerde Resource Manager-sjabloon parameters wilt overschrijven wanneer u publiceert vanuit de collaboration Branch, kunt u een bestand met aangepaste para meters genereren of bewerken. Meer informatie over het [gebruik van aangepaste para meters in de Resource Manager-sjabloon](continuous-integration-deployment.md#use-custom-parameters-with-the-resource-manager-template). De parameterisering-sjabloon is alleen beschikbaar wanneer u werkt in een Git-opslag plaats. Als de *arm-template-parameters-definition.jsin* het bestand niet bestaat in de werk vertakking, wordt deze door het bewerken van de standaard sjabloon gegenereerd.

![Aangepaste para meters beheren](media/author-management-hub/management-hub-custom-parameters.png)

## <a name="manage-authoring"></a>Ontwerpen beheren

### <a name="triggers"></a>Triggers

Triggers bepalen wanneer een pijplijn uitvoering moet worden gestart. Momenteel kunnen actieve triggers worden gebruikt voor een muur Clock, een periodiek interval uitvoeren of afhankelijk zijn van een gebeurtenis. Meer informatie vindt u in de [uitvoering van triggers](concepts-pipeline-execution-triggers.md#trigger-execution). In de Management hub kunt u de huidige status van een trigger maken, bewerken, verwijderen of weer geven.

![Scherm afbeelding die laat zien waar u de huidige status van een trigger kunt maken, bewerken, verwijderen of weer geven.](media/author-management-hub/management-hub-triggers.png)

### <a name="global-parameters"></a>Globale parameters

Globale para meters zijn constanten over een data factory die kunnen worden gebruikt door een pijp lijn in een wille keurige expressie. Meer informatie vindt u in [algemene para meters](author-global-parameters.md).

![Algemene para meters maken](media/author-global-parameters/create-global-parameter-3.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u [een Git-opslag plaats kunt configureren](source-control.md) voor uw ADF


