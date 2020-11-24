---
title: Werken met Node.js modules
description: Meer informatie over het werken met Node.js modules wanneer u Azure App Service of Cloud Services gebruikt.
services: ''
documentationcenter: nodejs
author: rloutlaw
manager: rloutlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: routlaw
ms.openlocfilehash: f61f08f32ebb2b721846d3c3017405af99421104
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95542215"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Node.js-modules gebruiken met Azure-toepassingen
Dit document bevat richt lijnen voor het gebruik van Node.js modules met toepassingen die worden gehost op Azure. Het biedt richt lijnen om ervoor te zorgen dat uw toepassing gebruikmaakt van een specifieke versie van een module en gebruikmaakt van systeem eigen modules met Azure.

Als u al bekend bent met het gebruik van Node.js modules, **package.jsop** en **npm-shrinkwrap.jsop** bestanden, biedt de volgende informatie een snelle samen vatting van wat in dit artikel wordt besproken:

* Azure App Service begrijpt **package.jsop** en **npm-shrinkwrap.jsop** bestanden en kan modules installeren op basis van vermeldingen in deze bestanden.

* Azure Cloud Services verwacht dat alle modules moeten worden geïnstalleerd in de ontwikkel omgeving en dat de map met **knooppunt \_ modules** moet worden opgenomen als onderdeel van het implementatie pakket. Het is mogelijk ondersteuning voor het installeren van modules in te scha kelen met behulp van **package.jsop** of **npm-shrinkwrap.jsop** bestanden in Cloud Services; deze configuratie vereist echter aanpassing van de standaard scripts die worden gebruikt door Cloud service-projecten. Voor een voor beeld van het configureren van deze omgeving raadpleegt [u Azure-opstart taak om de NPM-installatie uit te voeren om te voor komen dat knooppunt modules worden geïmplementeerd](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Azure Virtual Machines komen niet aan de orde in dit artikel, omdat de implementatie-ervaring in een VM afhankelijk is van het besturings systeem dat wordt gehost door de virtuele machine.
>
>

## <a name="nodejs-modules"></a>Node.js modules
Modules zijn laad bare java script-pakketten die specifieke functionaliteit bieden voor uw toepassing. Modules worden doorgaans geïnstalleerd met behulp van het opdracht regel programma **NPM** , maar sommige modules (zoals de HTTP-module) worden als onderdeel van het kern Node.js-pakket verschaft.

Als er modules zijn geïnstalleerd, worden ze opgeslagen in de map **knoop punt \_ modules** in de hoofdmap van de mapstructuur van uw toepassing. Elke module in de directory met **knooppunt \_ modules** onderhoudt een eigen map die modules bevat waarvan deze afhankelijk is, en dit gedrag wordt herhaald voor elke module, net als bij de afhankelijkheids keten. In deze omgeving kan elke module die is geïnstalleerd met eigen versie vereisten voor de modules waarvan deze afhankelijk is, echter een grote mappen structuur hebben.

Als u de map met **knooppunt \_ modules** implementeert als onderdeel van uw toepassing, neemt de grootte van de implementatie toe als deze wordt vergeleken met het gebruik van een **package.jsop** of **npm-shrinkwrap.jsin** het bestand. het garandeert echter wel dat de versies van de modules die worden gebruikt in de productie dezelfde zijn als de modules die worden gebruikt voor ontwikkeling.

### <a name="native-modules"></a>Systeem eigen modules
Hoewel de meeste modules gewoon java script-bestanden met tekst zonder opmaak zijn, zijn sommige modules platformspecifieke binaire installatie kopieën. Deze modules worden tijdens de installatie gecompileerd, meestal met behulp van python en node-Gyp. Omdat Azure Cloud Services afhankelijk is van de map **node \_ modules** die wordt geïmplementeerd als onderdeel van de toepassing, moet elke systeem eigen module die deel uitmaakt van de geïnstalleerde modules, werken in een Cloud service zolang deze is geïnstalleerd en gecompileerd op een Windows-ontwikkel systeem.

Azure App Service biedt geen ondersteuning voor alle systeem eigen modules en kan mislukken bij het compileren van modules met specifieke vereisten. Hoewel sommige populaire modules zoals MongoDB optionele systeem eigen afhankelijkheden hebben en zonder hen goed werken, zijn twee tijdelijke oplossingen bewezen met vrijwel alle systeem eigen modules die momenteel beschikbaar zijn:

* Voer de **installatie van NPM** uit op een Windows-computer waarop alle vereiste onderdelen van de systeem eigen module zijn geïnstalleerd. Implementeer vervolgens de map gemaakte **knooppunt \_ modules** als onderdeel van de toepassing die u wilt Azure app service.

  * Controleer voordat u het compileert of de lokale Node.js-installatie een overeenkomende architectuur heeft en of de versie zo dicht mogelijk bij het systeem van Azure wordt gebruikt (de huidige waarden kunnen worden gecontroleerd in runtime vanuit het eigenschappen **proces. Arch** en **process. versie**).

* Azure App Service kunnen worden geconfigureerd voor het uitvoeren van aangepaste bash of shell scripts tijdens de implementatie, zodat u aangepaste opdrachten kunt uitvoeren en de manier waarop **NPM-installatie** wordt uitgevoerd, nauw keurig moet configureren. Zie [Custom website Deployment scripts with kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/)(Engelstalig) voor een video over het configureren van die omgeving.

### <a name="using-a-packagejson-file"></a>Een package.jsgebruiken voor een bestand

De **package.jsop** bestand is een manier om de afhankelijkheden op het hoogste niveau op te geven die nodig zijn voor de toepassing, zodat het hosting platform de afhankelijkheden kan installeren, in plaats van de map met **knooppunt \_ modules** als onderdeel van de implementatie te hoeven opnemen. Nadat de toepassing is geïmplementeerd, wordt de **NPM-installatie** opdracht gebruikt voor het parseren **van depackage.jsin** het bestand en het installeren van alle afhankelijkheden die worden vermeld.

Tijdens de ontwikkeling kunt u de para meters **--Opslaan**, **--Save-dev** of **---Save-optional** gebruiken bij het installeren van modules om automatisch een vermelding voor de module toe te voegen aan uw **package.js** voor het bestand. Zie [NPM-install (](https://docs.npmjs.com/cli/install)Engelstalig) voor meer informatie.

Een mogelijk probleem met de **package.jsop** bestand is dat alleen de versie van afhankelijkheden op het hoogste niveau wordt opgegeven. Elke module die is geïnstalleerd, kan de versie van de modules waarvan deze afhankelijk is, mogelijk niet opgeven. het is dus mogelijk dat u een andere afhankelijkheids keten hebt dan die in ontwikkeling.

> [!NOTE]
> Als uw <b>package.jsin</b> het bestand verwijst naar Azure app service, ziet u mogelijk een fout die vergelijkbaar is met het volgende voor beeld bij het publiceren van de toepassing met behulp van git:
>
> NPM fout. module-name@0.6.0 installeren: ' node-Gyp-build configureren '
>
> NPM fout. cmd "/c" "node-Gyp configureren build" "is mislukt met 1
>
>

### <a name="using-a-npm-shrinkwrapjson-file"></a>Een npm-shrinkwrap.jsgebruiken voor een bestand
De **npm-shrinkwrap.jsin** het bestand is een poging om de beperkingen van de module versie van de **package.jsvoor** het bestand op te lossen. Hoewel de **package.jsin** het bestand alleen versies bevat voor de modules op het hoogste niveau, bevatten de **npm-shrinkwrap.jsin** het bestand de versie vereisten voor de volledige module afhankelijkheids keten.

Wanneer uw toepassing gereed is voor productie, kunt u de versie vereisten vergren delen en een **npm-shrinkwrap.jsin** het bestand maken met behulp van de opdracht **NPM Shrinkwrap** . Met deze opdracht worden de versies gebruikt die momenteel zijn geïnstalleerd in de map **node \_ modules** en worden deze versies opgenomen in de **npm-shrinkwrap.jsin** het bestand. Nadat de toepassing is geïmplementeerd in de hostomgeving, wordt de **NPM-installatie** opdracht gebruikt voor het parseren **van denpm-shrinkwrap.jsin** het bestand en het installeren van alle afhankelijkheden die worden weer gegeven. Zie [NPM-Shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap)voor meer informatie.

> [!NOTE]
> Als uw <b>npm-shrinkwrap.jsin</b> het bestand verwijst naar Azure app service, ziet u mogelijk een fout die vergelijkbaar is met het volgende voor beeld bij het publiceren van de toepassing met behulp van git:
>
> NPM fout. module-name@0.6.0 installeren: ' node-Gyp-build configureren '
>
> NPM fout. cmd "/c" "node-Gyp configureren build" "is mislukt met 1
>
>

## <a name="next-steps"></a>Volgende stappen
Nu u begrijpt hoe u Node.js modules kunt gebruiken met Azure, leert u hoe u [de Node.js versie kunt opgeven](https://github.com/squillace/nodejs-microservice), [een Node.js-Web-App kunt maken en implementeren](app-service/quickstart-nodejs.md)en [hoe u de Azure Command-Line-interface voor Mac en Linux gebruikt](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

Zie het [Node.js-ontwikkelaarscentrum](/azure/developer/javascript/) voor meer informatie.

[specify the Node.js version]: ./app-service/overview.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo