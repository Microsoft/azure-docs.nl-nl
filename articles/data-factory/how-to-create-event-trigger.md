---
title: Activering op basis van gebeurtenissen in Azure Data Factory maken
description: Meer informatie over het maken van een trigger in Azure Data Factory die een pijp lijn uitvoert in reactie op een gebeurtenis.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: ae8b1eab81e3c898c25a613f552a49c8de64f49d
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889124"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-storage-event"></a>Een trigger maken waarmee een pijp lijn wordt uitgevoerd als reactie op een opslag gebeurtenis

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden de opslag gebeurtenis triggers beschreven die u kunt maken in uw Data Factory-pijp lijnen.

Event-aangedreven architectuur (EDA) is een gemeen schappelijk gegevens integratie patroon dat betrekking heeft op productie, detectie, verbruik en reactie op gebeurtenissen. Voor scenario's voor gegevens integratie zijn vaak Data Factory-klanten vereist om pijp lijnen te activeren op basis van gebeurtenissen die zich in het opslag account voordoen, zoals de ontvangst of verwijdering van een bestand in Azure Blob Storage-account. Data Factory systeem eigen integreert met [Azure Event grid](https://azure.microsoft.com/services/event-grid/), waarmee u pijp lijnen kunt activeren voor dergelijke gebeurtenissen.

Bekijk de volgende video voor een inleiding en demonstratie van tien minuten voor deze functie:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]

> [!NOTE]
> De integratie die in dit artikel wordt beschreven, is afhankelijk van [Azure Event grid](https://azure.microsoft.com/services/event-grid/). Zorg ervoor dat uw abonnement is geregistreerd bij de resource provider Event Grid. Zie [resource providers en-typen](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)voor meer informatie. U moet de actie *micro soft. EventGrid/eventSubscriptions/** kunnen uitvoeren. Deze actie maakt deel uit van de ingebouwde rol EventSubscription Inzender voor EventGrid.

## <a name="data-factory-ui"></a>Gebruikersinterface van Data Factory

In deze sectie wordt beschreven hoe u een opslag gebeurtenis trigger maakt in de gebruikers interface van Azure Data Factory.

1. Schakel over naar het tabblad **bewerken** , weer gegeven met een potlood symbool.

1. Selecteer **activeren** in het menu en selecteer vervolgens **Nieuw/bewerken**.

1. Selecteer op de pagina **triggers toevoegen** de optie **trigger kiezen...** en selecteer **+ Nieuw**.

1. Gebeurtenis voor type trigger **opslag** selecteren

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image1.png" alt-text="Scherm afbeelding van de pagina auteur om een nieuwe opslag gebeurtenis trigger in Data Factory gebruikers interface te maken.":::

1. Selecteer uw opslag account in de vervolg keuzelijst van het Azure-abonnement of hand matig met de resource-ID van het opslag account. Kies op welke container de gebeurtenissen moeten worden uitgevoerd. Container selectie is vereist, maar mindful het selecteren van alle containers kan leiden tot een groot aantal gebeurtenissen.

   > [!NOTE]
   > De opslag gebeurtenis trigger ondersteunt momenteel alleen Azure Data Lake Storage Gen2 en algemene opslag accounts voor versie 2. Vanwege een Azure Event Grid beperking ondersteunt Azure Data Factory slechts een maximum van 500 opslag gebeurtenis triggers per opslag account. Als u de limiet bereikt, neemt u contact op met de ondersteuning voor aanbevelingen en verhoogt u de limiet bij evaluatie door Event Grid team. 

   > [!NOTE]
   > Als u een nieuwe opslag gebeurtenis trigger wilt maken of wijzigen, moet het Azure-account dat wordt gebruikt om u aan te melden bij Data Factory en de opslag gebeurtenis trigger te publiceren, beschikken over de juiste op rollen gebaseerde toegangs beheer machtiging (Azure RBAC) voor het opslag account. Er is geen aanvullende machtiging vereist: de service-principal voor de Azure Data Factory heeft _geen_ speciale machtigingen nodig voor het opslag account of event grid. Zie voor meer informatie over toegangs beheer [op rollen gebaseerd toegangs beheer](#role-based-access-control) sectie.

1. Het **BLOB-pad begint met** en het **BLOB-pad eindigt** op Eigenschappen, zodat u de containers, mappen en BLOB-namen kunt opgeven waarvoor u gebeurtenissen wilt ontvangen. Voor de opslag gebeurtenis trigger moet ten minste één van deze eigenschappen worden gedefinieerd. U kunt verschillende patronen gebruiken voor beide **BLOB-paden begint met** en het **BLOB-pad eindigt met** eigenschappen, zoals wordt weer gegeven in de voor beelden verderop in dit artikel.

    * **Pad van BLOB begint met:** Het BLOB-pad moet beginnen met een mappad. Geldige waarden zijn `2018/` `2018/april/shoes.csv` : en. Dit veld kan niet worden geselecteerd als er geen container is geselecteerd.
    * **Pad naar BLOB eindigt op:** Het pad naar de BLOB moet eindigen met een bestands naam of-extensie. Geldige waarden zijn `shoes.csv` `.csv` : en. De namen van containers en mappen, indien opgegeven, moeten worden gescheiden door een `/blobs/` segment. Een container met de naam ' orders ' kan bijvoorbeeld een waarde van hebben `/orders/blobs/2018/april/shoes.csv` . Als u een map in een container wilt opgeven, laat u het voorloop teken '/' weg. `april/shoes.csv`Er wordt bijvoorbeeld een gebeurtenis geactiveerd voor elk bestand in de `shoes.csv` map met de naam ' april ' in een wille keurige container.
    * Houd er rekening mee dat het BLOB-pad **begint met** en **eindigt** op de enige patroon overeenkomst die is toegestaan in de gebeurtenis trigger voor opslag. Andere typen overeenkomende joker tekens worden niet ondersteund voor het trigger type.

1. Selecteer of uw trigger reageert op een gebeurtenis **die** door een blob is gemaakt, een gebeurtenis voor het verwijderen van een **BLOB** of beide. In de opgegeven opslag locatie worden met elke gebeurtenis de Data Factory pijp lijnen geactiveerd die zijn gekoppeld aan de trigger.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image2.png" alt-text="Scherm afbeelding van de pagina voor het maken van een opslag gebeurtenis trigger.":::

1. Selecteer of blobs met nul bytes moeten worden genegeerd door de trigger.

1. Nadat u de trigger hebt geconfigureerd, klikt u op **volgende: voor beeld van gegevens**. In dit scherm ziet u de bestaande blobs die overeenkomen met de configuratie van de opslag gebeurtenis trigger. Zorg ervoor dat u specifieke filters hebt. Het configureren van filters die te breed zijn, kan overeenkomen met een groot aantal bestanden dat is gemaakt/verwijderd. Dit kan aanzienlijk invloed hebben op uw kosten. Klik op **volt ooien** als de filter voorwaarden zijn gecontroleerd.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image3.png" alt-text="Scherm afbeelding van de voorbeeld pagina opslag gebeurtenis trigger.":::

1. Als u een pijp lijn aan deze trigger wilt koppelen, gaat u naar het pijp lijn-canvas en klikt u op **activeren** en selecteert u **Nieuw/bewerken**. Wanneer de side-Navigator wordt weer gegeven, klikt u op de vervolg keuzelijst **Trigger selecteren...** en selecteert u de trigger die u hebt gemaakt. Klik op **volgende: voor beeld van gegevens** om te bevestigen dat de configuratie juist is en controleer vervolgens of de voorbeeld gegevens correct zijn. 

1. Als uw pijp lijn para meters heeft, kunt u deze opgeven op de activerings parameter zijde navigatie. De opslag gebeurtenis trigger legt het mappad en de bestands naam van de BLOB vast in de eigenschappen `@triggerBody().folderPath` en `@triggerBody().fileName` . Als u de waarden van deze eigenschappen in een pijp lijn wilt gebruiken, moet u de eigenschappen toewijzen aan pijplijn parameters. Nadat u de eigenschappen hebt toegewezen aan para meters, kunt u toegang krijgen tot de waarden die zijn vastgelegd door de trigger via de `@pipeline().parameters.parameterName` expressie in de pijp lijn. Zie [Naslag informatie over het activeren van meta gegevens in pijp lijnen](how-to-use-trigger-parameterization.md) voor gedetailleerde uitleg

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image4.png" alt-text="Scherm afbeelding van de toewijzing van eigenschappen van opslag gebeurtenis trigger voor pijplijn parameters.":::

    In het vorige voor beeld wordt de trigger zo geconfigureerd dat deze wordt geactiveerd wanneer een BLOB-pad dat eindigt op. csv wordt gemaakt in de map _Event-test_ in de _voorbeeld gegevens_ van de container. Met de eigenschappen **FolderPath** en **filename** wordt de locatie van de nieuwe BLOB vastgelegd. Als MoviesDB.csv bijvoorbeeld wordt toegevoegd aan het pad voor beeld/gebeurtenis-testen, `@triggerBody().folderPath` heeft de waarde `sample-data/event-testing` en `@triggerBody().fileName` een waarde van `moviesDB.csv` . Deze waarden worden in het voor beeld toegewezen aan de pijplijn parameters `sourceFolder` en `sourceFile` , die in de pijp lijn kunnen worden gebruikt als `@pipeline().parameters.sourceFolder` `@pipeline().parameters.sourceFile` respectievelijk.

1. Klik op **volt ooien** wanneer u klaar bent.

## <a name="json-schema"></a>JSON-schema

De volgende tabel bevat een overzicht van de schema-elementen die betrekking hebben op opslag gebeurtenis triggers:

| **JSON-element** | **Beschrijving** | **Type** | **Toegestane waarden** | **Vereist** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **ligt** | De Azure Resource Manager Resource-ID van het opslag account. | Tekenreeks | Azure Resource Manager-ID | Ja |
| **evenementen** | Het type gebeurtenissen dat ervoor zorgt dat deze trigger wordt gestart. | Matrix    | Micro soft. storage. BlobCreated, micro soft. storage. BlobDeleted | Ja, een wille keurige combi natie van deze waarden. |
| **blobPathBeginsWith** | Het BLOB-pad moet beginnen met het patroon dat is ingesteld voor de trigger om te starten. `/records/blobs/december/`De trigger wordt bijvoorbeeld alleen geactiveerd voor blobs in de `december` map onder de `records` container. | Tekenreeks   | | Geef een waarde op voor ten minste één van deze eigenschappen: `blobPathBeginsWith` of `blobPathEndsWith` . |
| **blobPathEndsWith** | Het BLOB-pad moet eindigen met het patroon dat is ingesteld voor de trigger om te starten. `december/boxes.csv`De trigger wordt bijvoorbeeld alleen geactiveerd voor blobs `boxes` met de naam in een `december` map. | Tekenreeks   | | U moet een waarde opgeven voor ten minste een van deze eigenschappen: `blobPathBeginsWith` of `blobPathEndsWith` . |
| **ignoreEmptyBlobs** | Hiermee wordt aangegeven of nul-byte-Blobs een pijplijn uitvoering activeren. Deze instelling is standaard ingesteld op waar. | Booleaans | waar of onwaar | Nee |

## <a name="examples-of-storage-event-triggers"></a>Voor beelden van opslag gebeurtenis triggers

Deze sectie bevat voor beelden van instellingen voor opslag gebeurtenis trigger.

> [!IMPORTANT]
> U moet het `/blobs/` segment van het pad toevoegen, zoals wordt weer gegeven in de volgende voor beelden, wanneer u container en map, container, bestand, container, map en bestand opgeeft. Voor **blobPathBeginsWith** voegt de Data Factory gebruikers interface automatisch toe `/blobs/` tussen de map en de container naam in de JSON van de trigger.

| Eigenschap | Voorbeeld | Beschrijving |
|---|---|---|
| **Pad van BLOB begint met** | `/containername/` | Hiermee ontvangt u gebeurtenissen voor alle blobs in de container. |
| **Pad van BLOB begint met** | `/containername/blobs/foldername/` | Hiermee ontvangt u gebeurtenissen voor alle blobs in de `containername` container en de `foldername` map. |
| **Pad van BLOB begint met** | `/containername/blobs/foldername/subfoldername/` | U kunt ook verwijzen naar een submap. |
| **Pad van BLOB begint met** | `/containername/blobs/foldername/file.txt` | Hiermee ontvangt u gebeurtenissen voor een BLOB `file.txt` met de naam in de `foldername` map onder de `containername` container. |
| **BLOB eindigt op** | `file.txt` | Hiermee ontvangt u gebeurtenissen voor een BLOB `file.txt` met de naam in een wille keurig pad. |
| **BLOB eindigt op** | `/containername/blobs/file.txt` | Hiermee ontvangt u gebeurtenissen voor een blob met de naam `file.txt` onder container `containername` . |
| **BLOB eindigt op** | `foldername/file.txt` | Hiermee ontvangt u gebeurtenissen voor een BLOB `file.txt` met de naam in `foldername` de map onder een wille keurige container. |

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Azure Data Factory maakt gebruik van Azure RBAC (op rollen gebaseerd toegangs beheer) om ervoor te zorgen dat onbevoegde toegang tot het Luis teren naar, het abonneren op updates en het activeren van pijp lijnen die zijn gekoppeld aan BLOB-gebeurtenissen strikt verboden is.

* Als u een bestaande opslag gebeurtenis trigger wilt maken of bijwerken, moet het Azure-account dat is aangemeld bij de Data Factory, de juiste toegang hebben tot het relevante opslag account. Anders wordt de bewerking met een fout _toegang geweigerd_.
* Data Factory hebt geen speciale machtigingen voor uw Event Grid nodig en u hoeft _geen_ speciale RBAC-machtiging toe te wijzen voor Data Factory Service-Principal voor de bewerking.

Een van de volgende RBAC-instellingen werkt voor opslag gebeurtenis trigger:

* De rol van eigenaar van het opslag account
* Rol van Inzender naar het opslag account
* _Micro soft. EventGrid/EventSubscriptions/schrijf_ machtiging voor het opslag account _/Subscriptions/# # #/resourceGroups/# # # #/providers/Microsoft.Storage/storageAccounts/storageAccountName_

Om te begrijpen hoe Azure Data Factory de twee beloftes levert, gaan we een stap teruggaan en een alvast kort achter de scène nemen. Dit zijn de werk stromen op hoog niveau voor de integratie tussen Data Factory, opslag en Event Grid.

### <a name="create-a-new-storage-event-trigger"></a>Een nieuwe opslag gebeurtenis trigger maken

Deze werk stroom op hoog niveau beschrijft hoe Azure Data Factory communiceert met Event Grid om een opslag gebeurtenis trigger te maken

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-5-create-subscription.png" alt-text="Werk stroom voor het maken van een opslag gebeurtenis trigger.":::

Twee merkbaar aanroepen van de werk stromen:

* Azure Data Factory maakt _geen_ direct contact met het opslag account. De aanvraag voor het maken van een abonnement wordt in plaats daarvan doorgestuurd en verwerkt door Event Grid. Daarom heeft Data Factory geen machtiging nodig voor het opslag account voor deze stap.

* Toegangs beheer en machtigings controle vindt plaats op Azure Data Factory zijde. Voordat de ADF een aanvraag verzendt om zich te abonneren op de opslag gebeurtenis, wordt de machtiging voor de gebruiker gecontroleerd. Meer specifiek wordt gecontroleerd of het Azure-account dat is aangemeld en probeert de opslag gebeurtenis trigger te maken, de juiste toegang heeft tot het relevante opslag account. Als de machtigings controle mislukt, mislukt het maken van de trigger ook.

### <a name="storage-event-trigger-data-factory-pipeline-run"></a>Opslag gebeurtenis trigger Data Factory pijplijn uitvoering

Deze werk stromen op hoog niveau beschrijven hoe opslag gebeurtenis triggers pijplijn worden uitgevoerd via Event Grid

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-6-trigger-pipeline.png" alt-text="Werk stroom van opslag gebeurtenis die pijplijn uitvoeringen uitvoert.":::

Wanneer het gaat om een gebeurtenis activering pijp lijn in Data Factory, zijn er drie onderhouds aanroepen in de werk stroom:

* Event Grid gebruikt een push model dat het bericht zo snel mogelijk doorstuurt wanneer het bericht in het systeem wordt neergezet. Dit wijkt af van het berichten systeem, zoals Kafka waar een pull-systeem wordt gebruikt.
* Gebeurtenis trigger op Azure Data Factory fungeert als actieve listener voor het binnenkomende bericht en de bijbehorende pijp lijn wordt op de juiste wijze geactiveerd.
* De trigger voor opslag gebeurtenissen zelf maakt geen rechtstreekse contact met het opslag account

  * Als u in de pijp lijn een kopie of andere activiteit hebt om de gegevens in het opslag account te verwerken, maakt Data Factory direct contact met Storage met behulp van de referenties die zijn opgeslagen in de gekoppelde service. Controleer of de gekoppelde service op de juiste wijze is ingesteld
  * Als u echter geen verwijzing naar het opslag account in de pijp lijn maakt, hoeft u geen machtiging te verlenen voor het Data Factory van toegang tot het opslag account

## <a name="next-steps"></a>Volgende stappen

* Zie [pijp lijnen uitvoeren en triggers](concepts-pipeline-execution-triggers.md#trigger-execution)voor meer informatie over triggers.
* Meer informatie over het verwijzen naar trigger-meta gegevens in de pijp lijn. Zie [Naslag informatie voor triggers in pijplijn uitvoeringen](how-to-use-trigger-parameterization.md)
