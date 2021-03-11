---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93bfb515c46413400ecd47105de378c7b677cecd
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564952"
---
## <a name="prepare-your-repository"></a>Uw opslagplaats voorbereiden

Als u automatische builds van Azure App Service build-server wilt maken, moet u ervoor zorgen dat de hoofdmap van uw opslag plaats de juiste bestanden in uw project bevat.

| Runtime | Bestanden in hoofdmap |
|-|-|
| ASP.NET (alleen Windows) | _*.sln_, _*.csproj_ of _default.aspx_ |
| ASP.NET Core | _*.sln_ of _*.csproj_ |
| PHP | _index.php_ |
| Ruby (alleen Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ of _package.json_ met een startscript |
| Python | _\*.py_, _requirements.txt_ of _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ of _iisstart.htm_ |
| WebJobs | _\<job_name>/run.\<extension>_ onder _App\_Data/jobs/continuous_ voor continue WebJobs, of _App\_Data/jobs/triggered_ voor geactiveerde WebJobs. Zie [Kudu WebJobs documentation](https://github.com/projectkudu/kudu/wiki/WebJobs) (Documentatie over Kudu WebJobs) voor meer informatie. |
| Functies | Zie [Continue implementatie voor Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Als u uw implementatie wilt aanpassen, neemt u een *DEPLOYMENT*-bestand op in de hoofdmap van de opslagplaats. Zie [Customize deployments](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) (Implementaties aanpassen) en [Custom deployment script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) (Aangepast implementatiescript) voor meer informatie.

> [!NOTE]
> Als u in Visual Studio ontwikkelt, laat [u Visual Studio een opslagplaats voor u maken](/azure/devops/repos/git/creatingrepo?tabs=visual-studio). Het project kan direct worden geïmplementeerd met behulp van Git.
>

