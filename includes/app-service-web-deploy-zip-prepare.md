---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 035399924216434de85865102db8838ea3fa15a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85570122"
---
## <a name="create-a-project-zip-file"></a>Een ZIP-bestand van het project maken

>[!NOTE]
> Als u de bestanden in een ZIP-bestand hebt gedownload, pakt u eerst de bestanden uit. Als u bijvoorbeeld een ZIP-bestand van GitHub hebt gedownload, kunt u dat bestand niet implementeren. GitHub voegt extra geneste directory's toe, die niet samen werken met App Service. 
>

Navigeer in een lokaal Terminal venster naar de hoofdmap van uw app-project. 

Deze map moet het invoer bestand bevatten naar uw web-app, zoals _index.html_, _index. php_ en _app.js_. Het kan ook pakket beheer bestanden bevatten, zoals _project.jsop_, _composer.jsop_, _package.json_, _bower.json_ en _requirements.txt_.

Tenzij u app service de implementatie automatisering voor u wilt uitvoeren, voert u alle build-taken uit (bijvoorbeeld,,, `npm` `bower` `gulp` `composer` en `pip` ) en zorgt u ervoor dat u alle bestanden hebt die u nodig hebt om de app uit te voeren. Deze stap is vereist als u [uw pakket rechtstreeks wilt uitvoeren](../articles/app-service/deploy-run-package.md).

Maak een ZIP-archief van alle bestanden in uw project. Voor `dotnet` projecten is deze map de map uitvoermap van de `dotnet publish` opdracht. De volgende opdracht maakt gebruik van het standaardhulpprogramma in de terminal:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

