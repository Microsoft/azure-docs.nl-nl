---
title: Webtaak, achtergrond taken op Azure
description: Meer informatie over webjobs.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7714b090399b0b184e2e216ff6da7b10f2bf4386
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452268"
---
# <a name="webjobs-run-background-tasks-in-azure-app-service"></a>Webjobs achtergrond taken in Azure App Service uitvoeren

In dit artikel wordt beschreven hoe u webjobs kunt implementeren met behulp van de [Azure Portal](https://portal.azure.com) voor het uploaden van een uitvoerbaar bestand of script. Zie [Webjobs implementeren met Visual Studio](webjobs-dotnet-deploy-vs.md)voor meer informatie over het ontwikkelen en implementeren van webjobs met Visual Studio.

## <a name="overview"></a>Overzicht
Webjobs is een functie van [Azure app service](index.yml) waarmee u een programma of script kunt uitvoeren in hetzelfde exemplaar als een web-app, API-app of mobiele app. Er zijn geen extra kosten verbonden aan het gebruik van webjobs.

> [!IMPORTANT]
> Webjobs wordt nog niet ondersteund voor App Service in Linux.

De Azure WebJobs SDK kan worden gebruikt met webjobs om veel programmeer taken te vereenvoudigen. Zie [Wat is de Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)? voor meer informatie.

Azure Functions biedt een andere manier om Program ma's en scripts uit te voeren. Zie [kiezen tussen flow, Logic apps, functions en Webjobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)voor een vergelijking tussen webjobs en functies.

## <a name="webjob-types"></a>Webtaak typen

In de volgende tabel worden de verschillen tussen *doorlopende* en *geactiveerde* webjobs beschreven.


|Continu  |Geactiveerd  |
|---------|---------|
| Wordt onmiddellijk gestart wanneer de Webtaak wordt gemaakt. Om te voor komen dat de taak eindigt, wordt het programma of het script doorgaans binnen een oneindige lus uitgevoerd. Als de taak wordt beëindigd, kunt u deze opnieuw starten. | Wordt alleen gestart wanneer hand matig of volgens een planning wordt geactiveerd. |
| Wordt uitgevoerd op alle exemplaren waarop de web-app wordt uitgevoerd. U kunt de Webtaak optioneel beperken tot één exemplaar. |Wordt uitgevoerd op één exemplaar dat door Azure wordt geselecteerd voor taak verdeling.|
| Ondersteunt fout opsporing op afstand. | Biedt geen ondersteuning voor externe fout opsporing.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="add-webjob-to-source-control"></a>Webtaak toevoegen aan broncode beheer

Als u broncode beheer hebt geconfigureerd met uw toepassing, moeten de webjobs worden geïmplementeerd als onderdeel van de bron beheer integratie. Zodra broncode beheer is geconfigureerd met uw toepassing, kan een Webtaak niet vanuit Azure Portal worden toegevoegd.

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Ondersteunde bestands typen voor scripts of Program ma's

De volgende bestandstypen worden ondersteund:

* . cmd,. bat,. exe (met behulp van Windows CMD)
* . ps1 (met Power shell)
* . sh (met behulp van bash)
* . php (met PHP)
* . py (met python)
* . js (met behulp van Node.js)
* . jar (met behulp van Java)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van een Webtaak](./webjobs-create-ieux.md)
* De logboek geschiedenis van [Webjobs](./webjobs-create-ieux-view-log.md) weer geven
