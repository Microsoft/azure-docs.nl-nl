---
title: Aangepaste gebeurtenis triggers maken in Azure Data Factory
description: Meer informatie over het maken van een aangepaste trigger in Azure Data Factory die een pijp lijn uitvoert in reactie op een aangepaste gebeurtenis die is gepubliceerd op Event Grid.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 2d2f26b24e7fa10d9244de8f99d78c64a44b3d61
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785645"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-custom-event-preview"></a>Een trigger maken waarmee een pijp lijn wordt uitgevoerd als reactie op een aangepaste gebeurtenis (preview-versie)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden de aangepaste gebeurtenis triggers beschreven die u kunt maken in uw Data Factory-pijp lijnen.

Event-aangedreven architectuur (EDA) is een gemeen schappelijk gegevens integratie patroon dat betrekking heeft op productie, detectie, verbruik en reactie op gebeurtenissen. Voor scenario's voor gegevens integratie zijn vaak Data Factory-klanten vereist om pijp lijnen te activeren op basis van bepaalde gebeurtenissen. Data Factory systeem eigen integratie met [Azure Event grid](https://azure.microsoft.com/services/event-grid/) omvat nu [aangepaste gebeurtenissen](../event-grid/custom-topics.md): klanten sturen wille keurige gebeurtenissen naar een event Grid-onderwerp en Data Factory zich op te abonneren en te Luis teren naar het onderwerp en pijp lijnen dienovereenkomstig te activeren.

> [!NOTE]
> De integratie die in dit artikel wordt beschreven, is afhankelijk van [Azure Event grid](https://azure.microsoft.com/services/event-grid/). Zorg ervoor dat uw abonnement is geregistreerd bij de resource provider Event Grid. Zie [resource providers en-typen](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)voor meer informatie. U moet de actie *micro soft. EventGrid/eventSubscriptions/** kunnen uitvoeren. Deze actie maakt deel uit van de ingebouwde rol EventSubscription Inzender voor EventGrid.

Daarnaast kunnen klanten de pijplijn parameters en aangepaste gebeurtenis triggers combi neren en de nettolading van aangepaste _gegevens_ in pijplijn uitvoeringen parseren en ernaar verwijzen. het _gegevens_ veld in de aangepaste gebeurtenis lading is een gestructureerde JSON-sleutel-waarde, waarmee klanten maximale controle over de door de gebeurtenis gestuurde pijplijn uitvoeringen kunnen krijgen.

> [!IMPORTANT]
> Daarom kan een sleutel waarnaar in parameterisering wordt verwezen, ontbreken in de nettolading van de aangepaste gebeurtenis. De _uitvoering_ van de trigger mislukt met een fout, waarin wordt aangegeven dat de expressie niet kan worden geëvalueerd omdat de eigenschaps _naam_ niet bestaat. __Er wordt geen__ _pijplijn uitvoering_ geactiveerd door de gebeurtenis.

## <a name="setup-event-grid-custom-topic"></a>Event Grid aangepast onderwerp instellen

Als u de aangepaste gebeurtenis trigger in Data Factory wilt gebruiken, moet u _eerst_ een [aangepast onderwerp instellen in Event grid](../event-grid/custom-topics.md). De werk stroom wijkt af van de opslag gebeurtenis trigger, waar Data Factory het onderwerp voor u instelt. Hier moet u door de Azure Event Grid navigeren en het onderwerp zelf maken. Zie Azure Event Grid [Portal zelf studies](../event-grid/custom-topics.md#azure-portal-tutorials) en [cli-zelf studies](../event-grid/custom-topics.md#azure-cli-tutorials) voor meer informatie over het maken van een aangepast onderwerp.

Gegevens fabrieken verwachten dat de gebeurtenissen worden gevolgd [Event grid gebeurtenis schema](../event-grid/event-schema.md). Zorg ervoor dat gebeurtenis-nettoladingen de volgende velden bevatten.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

## <a name="data-factory-ui"></a>Gebruikersinterface van Data Factory

In deze sectie wordt beschreven hoe u een opslag gebeurtenis trigger maakt in de gebruikers interface van Azure Data Factory.

1. Schakel over naar het tabblad **bewerken** , weer gegeven met een potlood symbool.

1. Selecteer **activeren** in het menu en selecteer vervolgens **Nieuw/bewerken**.

1. Selecteer op de pagina **triggers toevoegen** de optie **trigger kiezen...** en selecteer **+ Nieuw**.

1. Selecteer **aangepaste gebeurtenissen** voor het trigger type

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Scherm afbeelding van de pagina auteur om een nieuwe aangepaste gebeurtenis trigger in Data Factory gebruikers interface te maken." lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. Selecteer uw aangepaste onderwerp in de vervolg keuzelijst voor het Azure-abonnement of voer het bereik van de gebeurtenis onderwerp hand matig in.

   > [!NOTE]
   > Als u een nieuwe aangepaste gebeurtenis trigger wilt maken of wijzigen, moet het Azure-account dat wordt gebruikt om u aan te melden bij Data Factory en de opslag gebeurtenis trigger te publiceren, beschikken over de juiste machtiging voor op rollen gebaseerd toegangs beheer (Azure RBAC) voor het onderwerp. Er is geen aanvullende machtiging vereist: de service-principal voor de Azure Data Factory vereist _geen_ speciale machtiging voor Event grid. Zie voor meer informatie over toegangs beheer [op rollen gebaseerd toegangs beheer](#role-based-access-control) sectie.

1. Het **onderwerp begint met** en **onderwerp eindigt** op Eigenschappen, zodat u gebeurtenissen kunt filteren waarvoor u de pijp lijn wilt activeren. Beide eigenschappen zijn optioneel.

1. Gebruik **+ Nieuw** om **gebeurtenis typen** toe te voegen waarop u wilt filteren. Aangepaste gebeurtenis trigger voor werk nemer a of relatie voor de lijst: als een aangepaste gebeurtenis een eigenschap _Event_ type heeft die hier wordt vermeld, wordt een pijplijn uitvoering geactiveerd. Het gebeurtenis type is niet hoofdletter gevoelig. In de onderstaande scherm afbeelding komt de trigger bijvoorbeeld overeen met alle gebeurtenissen _copycompleted_ of _copysucceeded_ met betrekking tot het onderwerp begint met- _fabrieken_

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Scherm afbeelding van de pagina trigger bewerken om gebeurtenis typen en het filteren van het onderwerp in Data Factory gebruikers interface te verklaren.":::

1. Met de trigger voor aangepaste gebeurtenissen kan de nettolading van aangepaste _gegevens_ worden geparseerd en verzonden naar uw pijp lijn. Maak eerst de pijplijn parameters en vul de waarden in op de pagina **para meters** . Gebruik format **@triggerBody (). Event. data._de naam_** van de gegevens voor het parseren van de gegevenspayload en het door geven van waarden aan pijplijn parameters. Zie [Naslag informatie over het activeren van meta gegevens in pijp lijnen](how-to-use-trigger-parameterization.md) en [systeem variabelen in een aangepaste gebeurtenis trigger](control-flow-system-variables.md#custom-event-trigger-scope) voor gedetailleerde uitleg

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="Scherm opname van de instelling pijplijn parameters.":::

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="Scherm afbeelding van de pagina para meters om te verwijzen naar de nettolading van gegevens in een aangepaste gebeurtenis.":::

1. Klik op **OK** wanneer u klaar bent.

## <a name="json-schema"></a>JSON-schema

De volgende tabel bevat een overzicht van de schema-elementen die betrekking hebben op aangepaste gebeurtenis triggers:

| **JSON-element** | **Beschrijving** | **Type** | **Toegestane waarden** | **Vereist** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **ligt** | De Azure Resource Manager Resource-ID van het onderwerp Event grid. | Tekenreeks | Azure Resource Manager-ID | Ja |
| **evenementen** | Het type gebeurtenissen dat ervoor zorgt dat deze trigger wordt gestart. | Matrix van tekenreeksen    |  | Ja, er wordt ten minste één waarde verwacht |
| **subjectBeginsWith** | Het onderwerpveld moet beginnen met het patroon dat is ingevoerd voor de trigger om te starten. Bijvoorbeeld, wordt `factories` alleen de trigger voor gebeurtenis onderwerp gestart, te beginnen met `factories` . | Tekenreeks   | | Nee |
| **subjectEndsWith** | Het onderwerpveld moet eindigen met het patroon dat is ingevoerd voor de trigger om te starten. | Tekenreeks   | | Nee |

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Azure Data Factory maakt gebruik van Azure RBAC (op rollen gebaseerd toegangs beheer) om ervoor te zorgen dat onbevoegde toegang tot het Luis teren naar, het abonneren op updates en het activeren van pijp lijnen die zijn gekoppeld aan aangepaste gebeurtenissen, strikt is verboden.

* Als u een nieuwe aangepaste gebeurtenis trigger wilt maken of een bestaande wilt bijwerken, moet het Azure-account dat is aangemeld bij de Data Factory, de juiste toegang hebben tot het relevante opslag account. Anders wordt de bewerking met een fout _toegang geweigerd_.
* Data Factory hebt geen speciale machtigingen voor uw Event Grid nodig en u hoeft _geen_ speciale Azure RBAC-machtiging toe te wijzen voor Data Factory Service-Principal voor de bewerking.

Met name heeft de klant _micro soft. EventGrid/EventSubscriptions/schrijf_ machtiging nodig voor _/Subscriptions/# # # #/resourceGroups//# #_ # #/providers/Microsoft.EventGrid/topics/someTopics

## <a name="next-steps"></a>Volgende stappen

* Zie [pijp lijnen uitvoeren en triggers](concepts-pipeline-execution-triggers.md#trigger-execution)voor meer informatie over triggers.
* Meer informatie over het verwijzen naar trigger-meta gegevens in de pijp lijn. Zie [Naslag informatie voor triggers in pijplijn uitvoeringen](how-to-use-trigger-parameterization.md)
