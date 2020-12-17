---
title: 'CLI: Een app terugzetten vanuit de back-up'
description: Meer informatie over het gebruik van de Azure CLI voor het automatiseren van de implementatie en het beheer van uw App Service-app. In dit voorbeeld ziet u hoe u een app terugzet vanuit een back-up.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/07/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18
ms.openlocfilehash: a8b7d20c3eee57d10a7025b05605603f82437cdb
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006409"
---
# <a name="restore-a-web-app-from-a-backup-using-cli"></a>Een web-app terugzetten vanuit een back-up met CLI

Met dit voorbeeldscript wordt er een web-app gemaakt in App Service, inclusief de bijbehorende resources, en wordt er vervolgens een eenmalige back-up van de app gemaakt. 

Als u dit script wilt uitvoeren, moet u een bestaande back-up voor een web-app hebben. Instructies voor het maken van een back-up vindt u in [Een back-up van een web-app maken ](cli-backup-onetime.md) of [Een geplande back-up voor een web-app maken](cli-backup-scheduled.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u Azure CLI versie 2.0 of hoger nodig. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-restore/backup-restore.sh?highlight=3-4,9 "Restore a web app from a backup")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [`az webapp config backup list`](/cli/azure/webapp/config/backup#az-webapp-config-backup-list) | Hiermee vraagt u een lijst met back-ups op voor een web-app. |
| [`az webapp config backup restore`](/cli/azure/webapp/config/backup#az-webapp-config-backup-restore) | Hiermee zet u een web-app terug vanuit een back-up. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Meer voorbeelden van App Service CLI-scripts vindt u in de [documentatie van Azure App Service](../samples-cli.md).
