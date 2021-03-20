---
title: End-to-end-taken uitvoeren met behulp van sjablonen
description: Met alleen CLI-opdrachten kunt u een pool maken, invoer gegevens uploaden, taken en bijbehorende taken maken en de resulterende uitvoer gegevens downloaden.
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 845a32c2feda5a5a3b8d44d237c62db94cae1779
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91848718"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Azure Batch CLI-sjablonen en-bestands overdracht gebruiken

Met behulp van een batch-uitbrei ding naar de Azure CLI kunt u batch-taken uitvoeren zonder code te schrijven.

Met de Azure CLI JSON-sjabloon bestanden maken en gebruiken om batch-Pools,-taken en-taken te maken. Gebruik CLI-extensie opdrachten om eenvoudig taak invoer bestanden te uploaden naar het opslag account dat is gekoppeld aan het batch-account en download uitvoer bestanden voor de taak.

> [!NOTE]
> JSON-bestanden ondersteunen niet dezelfde functionaliteit als [Azure Resource Manager sjablonen](../azure-resource-manager/templates/template-syntax.md). Ze zijn bedoeld om te worden ingedeeld zoals de hoofd tekst van de onbewerkte REST-aanvraag. De CLI-extensie wijzigt geen bestaande opdrachten, maar heeft een vergelijk bare sjabloon optie waarmee gedeeltelijke Azure Resource Manager sjabloon functionaliteit wordt toegevoegd. Zie [Azure batch cli-uitbrei dingen voor Windows, Mac en Linux](https://github.com/Azure/azure-batch-cli-extensions).

## <a name="overview"></a>Overzicht

Een uitbrei ding van Azure CLI maakt het mogelijk end-to-end te gebruiken voor gebruikers die geen ontwikkel aars zijn. Met alleen CLI-opdrachten kunt u een pool maken, invoer gegevens uploaden, taken en bijbehorende taken maken en de resulterende uitvoer gegevens downloaden. Er is geen aanvullende code vereist. Voer de CLI-opdrachten rechtstreeks uit of integreer ze in scripts.

Batch-sjablonen zijn gebaseerd op de bestaande batch-ondersteuning in de [Azure cli](batch-cli-get-started.md#json-files-for-resource-creation) voor json-bestanden om eigenschaps waarden op te geven bij het maken van Pools, taken, taken en andere items. Batch sjablonen voegen de volgende mogelijkheden toe:

-   Para meters kunnen worden gedefinieerd. Wanneer de sjabloon wordt gebruikt, worden alleen de parameter waarden opgegeven om het item te maken, met andere item eigenschaps waarden die zijn opgegeven in de hoofd tekst van de sjabloon. Een gebruiker die batch begrijpt en de toepassingen die door batch moeten worden uitgevoerd, kan sjablonen maken, groeps-, taak-en taak eigenschaps waarden opgeven. Een gebruiker heeft minder vertrouwd met batch en/of de toepassingen hoeven alleen de waarden voor de gedefinieerde para meters op te geven.

-   Met taak taak factoren worden een of meer taken gemaakt die aan een taak zijn gekoppeld, waardoor het niet nodig is om veel taak definities te maken en het verzenden van taken aanzienlijk te vereenvoudigen.


Taken gebruiken meestal invoer gegevensbestand en produceren uitvoer gegevens bestanden. Een opslag account wordt standaard gekoppeld aan elk batch-account. Bestanden verzenden naar en van dit opslag account met behulp van de CLI zonder code ring en geen opslag referenties.

[Ffmpeg](https://ffmpeg.org/) is bijvoorbeeld een populaire toepassing waarmee audio-en video bestanden worden verwerkt. Hier volgen stappen met de Azure Batch CLI voor het aanroepen van ffmpeg voor het transcoderen van bron video bestanden naar verschillende resoluties.

-   Een groeps sjabloon maken. De gebruiker die de sjabloon maakt, weet hoe u de ffmpeg-toepassing en de bijbehorende vereisten aanroept. Hiermee geeft u het juiste besturings systeem, de VM-grootte op, hoe ffmpeg is geïnstalleerd (vanuit een toepassings pakket of met behulp van een pakket manager, bijvoorbeeld) en andere eigenschaps waarden van de groep. Er worden para meters gemaakt, zodat alleen de groeps-ID en het aantal Vm's moeten worden opgegeven.

-   Een taak sjabloon maken. De gebruiker die de sjabloon maakt, weet hoe ffmpeg moet worden aangeroepen voor het transcoderen van de bron video naar een andere oplossing en geeft de opdracht regel van de taak op. ze weten ook dat er een map is met de bron video bestanden, met een vereiste taak per invoer bestand.

-   Een eind gebruiker met een set video bestanden voor trans code maakt eerst een pool met behulp van de groeps sjabloon, waarbij alleen de groeps-ID en het aantal vereiste Vm's worden opgegeven. Ze kunnen vervolgens de bron bestanden uploaden naar transcoderen. Een taak kan vervolgens worden verzonden met behulp van de taak sjabloon, waarbij alleen de groeps-ID en de locatie van de geüploade bron bestanden worden opgegeven. De batch-taak is gemaakt, waarbij één taak per invoer bestand wordt gegenereerd. Ten slotte kan de gedecodeerde uitvoer bestanden worden gedownload.

## <a name="installation"></a>Installatie

Als u de Azure Batch CLI-extensie wilt installeren, installeert u eerst [de Azure cli 2,0](/cli/azure/install-azure-cli)of voert u de Azure CLI uit in [Azure Cloud shell](../cloud-shell/overview.md).

Installeer de nieuwste versie van de batch-uitbrei ding met behulp van de volgende Azure CLI-opdracht:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Zie de [github opslag plaats](https://github.com/Azure/azure-batch-cli-extensions)voor meer informatie over de batch-cli-extensie en aanvullende installatie opties.


Als u de CLI-extensie functies wilt gebruiken, hebt u een Azure Batch-account nodig en voor de opdrachten waarmee bestanden worden overgedragen van en naar opslag, een gekoppeld opslag account.

Als u zich wilt aanmelden bij een batch-account met de Azure CLI, raadpleegt u [batch-resources beheren met Azure cli](batch-cli-get-started.md).

## <a name="templates"></a>Sjablonen

Azure Batch sjablonen zijn vergelijkbaar met Azure Resource Manager sjablonen, in functionaliteit en syntaxis. Ze zijn JSON-bestanden die eigenschaps namen en-waarden van items bevatten, maar de volgende hoofd concepten toevoegen:

-   **Parameters**

    -   Toestaan dat eigenschaps waarden worden opgegeven in een hoofd sectie, met alleen parameter waarden die moeten worden opgegeven wanneer de sjabloon wordt gebruikt. De volledige definitie voor een pool kan bijvoorbeeld in de hoofd tekst worden geplaatst en er is slechts één para meter gedefinieerd voor `poolId` . Daarom moet alleen een groeps-ID-reeks worden opgegeven om een groep te maken.

    -   De sjabloon hoofdtekst kan worden gemaakt door iemand met kennis van batch en de toepassingen die moeten worden uitgevoerd met batch; alleen waarden voor de door de gebruiker gedefinieerde para meters moeten worden opgegeven wanneer de sjabloon wordt gebruikt. Een gebruiker zonder de diep gaande batch en/of toepassings kennis kan daarom gebruikmaken van de sjablonen.

-   **Variabelen**

    -   Toestaan dat eenvoudige of complexe parameter waarden op één plek worden opgegeven en op een of meer plaatsen in de sjabloon tekst worden gebruikt. Variabelen kunnen de grootte van de sjabloon vereenvoudigen en beperken, en het kan gemakkelijker worden onderhouden door één locatie te hebben om eigenschappen te wijzigen.

-   **Constructies op een hoger niveau**

    -   Sommige constructies op een hoger niveau zijn beschikbaar in de sjabloon die nog niet beschikbaar zijn in de batch-Api's. U kunt bijvoorbeeld een taak-Factory definiëren in een taak sjabloon waarmee meerdere taken voor de taak worden gemaakt met behulp van een algemene taak definitie. Deze constructies voor komen dat u code hoeft te coderen om dynamisch meerdere JSON-bestanden te maken, zoals één bestand per taak, en script bestanden te maken voor het installeren van toepassingen via een pakket beheer.

    -   Op een bepaald moment kunnen deze constructies worden toegevoegd aan de batch-service en beschikbaar zijn in de batch-Api's, UIs, enzovoort.

### <a name="pool-templates"></a>Groeps sjablonen

Groeps sjablonen ondersteunen de standaard sjabloon mogelijkheden van para meters en variabelen. Ze ondersteunen ook de volgende construct op een hoger niveau:

-   **Pakket verwijzingen**

    -   U kunt eventueel ook software kopiëren naar groeps knooppunten met behulp van pakket beheerders. Pakket beheer en pakket-ID zijn opgegeven. Als u een of meer pakketten declareert, kunt u voor komen dat u een script maakt waarmee de vereiste pakketten worden opgehaald, het script wordt geïnstalleerd en het script wordt uitgevoerd op elk pool knooppunt.

Hier volgt een voor beeld van een sjabloon voor het maken van een groep virtuele Linux-machines waarop ffmpeg is geïnstalleerd. Als u deze wilt gebruiken, moet u alleen een groeps-ID en het aantal virtuele machines in de groep opgeven:

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "taskSlotsPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

Als de naam van het sjabloon bestand is _pool-ffmpeg.jsop_, roept u de sjabloon als volgt aan:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

De CLI vraagt u om waarden op te geven voor de `poolId` `nodeCount` para meters en. U kunt ook de para meters opgeven in een JSON-bestand. Bijvoorbeeld:

```json
{
  "poolId": {
    "value": "mypool"
  },
  "nodeCount": {
    "value": 2
  }
}
```

Als het JSON-bestand van de para meters de naam *pool-parameters.js* heeft, roept u de sjabloon als volgt aan:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Taak sjablonen

Taak sjablonen bieden ondersteuning voor de standaard sjabloon mogelijkheden van para meters en variabelen. Ze ondersteunen ook de volgende construct op een hoger niveau:

-   **Taak fabriek**

    -   Hiermee maakt u meerdere taken voor een taak uit de ene taak definitie. Drie typen taak fabriek worden ondersteund: parametrische sweep, taak per bestand en taak verzameling.

Hier volgt een voor beeld van een sjabloon waarmee een taak wordt gemaakt voor het transcoderen van MP4-video bestanden met ffmpeg naar een van twee lagere resoluties. Er wordt één taak per bron video bestand gemaakt. Zie [Bestands groepen en bestands overdracht](#file-groups-and-file-transfer) voor meer informatie over bestands groepen voor taak invoer en-uitvoer.

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": {
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Als de naam van het sjabloon bestand is _job-ffmpeg.jsop_, roept u de sjabloon als volgt aan:

```azurecli
az batch job create --template job-ffmpeg.json
```

Net als voorheen vraagt de CLI u waarden voor de para meters op te geven. U kunt ook de para meters opgeven in een JSON-bestand.

### <a name="use-templates-in-batch-explorer"></a>Sjablonen gebruiken in Batch Explorer

U kunt een batch-CLI-sjabloon uploaden naar de [batch Explorer](https://github.com/Azure/BatchExplorer) -bureaublad toepassing (voorheen batch Labs genoemd) om een batch-pool of-taak te maken. U kunt ook selecteren uit vooraf gedefinieerde groeps-en taak sjablonen in de galerie met Batch Explorer.

Een sjabloon uploaden:

1. Selecteer in batch Explorer **Galerie**  >  **lokale sjablonen**.

2. Selecteer een lokale groep of een taak sjabloon of sleep en zet deze neer.

3. Selecteer **deze sjabloon gebruiken** en volg de aanwijzingen op het scherm.

## <a name="file-groups-and-file-transfer"></a>Bestands groepen en bestands overdracht

Voor de meeste taken en taken zijn invoer bestanden vereist en uitvoer bestanden geproduceerd. Normaal gesp roken worden invoer bestanden en uitvoer bestanden overgebracht, hetzij van de client naar het knoop punt, hetzij van het knoop punt naar de client. De Azure Batch CLI-extensie maakt bestands overdracht mogelijk en maakt gebruik van het opslag account dat u kunt koppelen aan elk batch-account.

Een bestands groep is gelijk aan een container die is gemaakt in het Azure-opslag account. De bestands groep bevat mogelijk submappen.

De batch-CLI-extensie biedt opdrachten voor het uploaden van bestanden van de client naar een opgegeven bestands groep en het downloaden van bestanden van de opgegeven bestands groep naar een-client.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Met groeps-en taak sjablonen kunnen bestanden die zijn opgeslagen in bestands groepen worden opgegeven voor kopiëren naar groeps knooppunten of uit groeps knooppunten terug naar een bestands groep. In de eerder opgegeven taak sjabloon is bijvoorbeeld de bestands groep *ffmpeg-invoer* opgegeven voor de taak fabriek als de locatie van de bron video bestanden die zijn gekopieerd naar het knoop punt voor trans code ring. De bestands groep *ffmpeg-uitvoer* is de locatie waar de gedecodeerde uitvoer bestanden worden gekopieerd van het knoop punt waarop elke taak wordt uitgevoerd.

## <a name="summary"></a>Samenvatting

De ondersteuning voor sjablonen en bestands overdracht is momenteel alleen toegevoegd aan de Azure CLI. Het doel is om de doel groep uit te breiden die batch kan gebruiken voor gebruikers die geen code hoeven te ontwikkelen met behulp van de batch-Api's, zoals onderzoekers en IT-gebruikers. Zonder code ring kunnen gebruikers met kennis van Azure, batch en de toepassingen die moeten worden uitgevoerd met batch, sjablonen maken voor het maken van groepen en taken. Met sjabloon parameters kunnen gebruikers zonder gedetailleerde kennis van batch en de toepassingen de sjablonen gebruiken.

Probeer de batch-uitbrei ding voor de Azure CLI uit en geef ons feedback of suggesties, hetzij in de opmerkingen voor dit artikel, hetzij via de opslag plaats van de [batch-Community](https://github.com/Azure/Batch).

## <a name="next-steps"></a>Volgende stappen

- Gedetailleerde documentatie, voor beelden en de bron code van de installatie en het gebruik zijn beschikbaar in de [Azure github opslag plaats](https://github.com/Azure/azure-batch-cli-extensions).

- Meer informatie over het gebruik van [batch Explorer](https://github.com/Azure/BatchExplorer) voor het maken en beheren van batch-resources.
