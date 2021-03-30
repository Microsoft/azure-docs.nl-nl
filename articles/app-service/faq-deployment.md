---
title: Veelgestelde vragen over implementatie-Azure App Service | Microsoft Docs
description: Krijg antwoorden op veelgestelde vragen over implementatie voor de functie Web Apps van Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 163a6940e50d1f8beacc23855fd1e6f9daad0085
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88080470"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Veelgestelde vragen over implementaties voor web-apps in azure

In dit artikel vindt u antwoorden op veelgestelde vragen over implementatie problemen voor de [Web apps functie van Azure app service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Ik ben gewoon aan de slag met App Service web-apps. Hoe kan ik mijn code publiceren?

Hier volgen enkele opties voor het publiceren van uw web-app-code:

*   Implementeren met behulp van Visual Studio. Als u de Visual Studio-oplossing hebt, klikt u met de rechter muisknop op het Project Web Application en selecteert u **publiceren**.
*   Implementeren met behulp van een FTP-client. Down load in het Azure Portal het publicatie profiel voor de web-app waarop u de code wilt implementeren. Upload de bestanden vervolgens naar \site\wwwroot door dezelfde FTP-referenties voor het publicatie profiel te gebruiken.

Zie [uw app implementeren voor app service](deploy-local-git.md)voor meer informatie.

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Er wordt een fout bericht weer gegeven wanneer ik probeer te implementeren vanuit Visual Studio. Hoe kan ik deze fout op te lossen?

Als u het volgende bericht ziet, gebruikt u mogelijk een oudere versie van de SDK: ' fout tijdens de implementatie van de resource ' YourResourceName ' in de resource groep ' YourResourceGroup ': MissingRegistrationForLocation: het abonnement is niet geregistreerd voor het resource type ' componenten ' in de locatie ' Central US '. Meld u opnieuw aan voor deze provider om toegang te krijgen tot deze locatie. " 

Als u deze fout wilt oplossen, moet u een upgrade uitvoeren naar de [nieuwste SDK](https://azure.microsoft.com/downloads/). Als u dit bericht ziet en u de nieuwste SDK hebt, dient u een ondersteunings aanvraag in.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Hoe kan ik een ASP.NET-toepassing implementeren vanuit Visual Studio naar App Service?
<a id="deployasp"></a>

In de zelf studie [maakt u binnen vijf minuten uw eerste ASP.net-Web-app in azure](quickstart-dotnetcore.md) . hierin wordt beschreven hoe u een ASP.NET-webtoepassing implementeert in een web-app in app service met behulp van Visual Studio.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Wat zijn de verschillende typen implementatie referenties?

App Service ondersteunt twee typen referenties voor lokale Git-implementatie en FTP/S-implementatie. Zie [implementatie referenties voor app service configureren voor](deploy-configure-credentials.md)meer informatie over het configureren van implementatie referenties.

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Wat is de bestands-of mapstructuur van mijn App Service web-app?

Zie [bestands structuur in azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)voor informatie over de bestands structuur van uw app service-app.

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Hoe kan ik ' FTP-fout 550: er is onvoldoende ruimte op de schijf ' wanneer ik mijn bestanden probeer te herstellen?

Als dit bericht wordt weer gegeven, is het waarschijnlijk dat u in het service abonnement voor uw web-app werkt met een schijf quotum. Mogelijk moet u omhoog schalen naar een hogere servicelaag op basis van de behoeften van uw schijf ruimte. Zie [app service prijzen](https://azure.microsoft.com/pricing/details/app-service/)voor meer informatie over prijzen plannen en resource limieten.

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Hoe kan ik doorlopende implementatie instellen voor mijn App Service web-app?

U kunt continue implementatie van verschillende bronnen instellen, waaronder Azure DevOps, OneDrive, GitHub, bitbucket, Dropbox en andere Git-opslag plaatsen. Deze opties zijn beschikbaar in de portal. [Continue implementatie naar app service](deploy-continuous-deployment.md) is een handige zelf studie waarin wordt uitgelegd hoe u doorlopende implementatie kunt instellen.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Hoe kan ik problemen met continue implementatie van GitHub en bitbucket oplossen?

Zie [continue implementatie onderzoeken](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)voor informatie over het onderzoeken van problemen met continue implementatie vanuit github of bitbucket.

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>Ik kan geen FTP naar mijn site maken en mijn code publiceren. Hoe kan ik dit probleem oplossen?

FTP-problemen oplossen:

1. Controleer of u de juiste hostnaam en referenties hebt ingevoerd. Zie [implementatie referenties](https://github.com/projectkudu/kudu/wiki/Deployment-credentials)voor meer informatie over de verschillende typen referenties en hoe u deze kunt gebruiken.
2. Controleer of de FTP-poorten niet zijn geblokkeerd door een firewall. De poorten moeten de volgende instellingen hebben:
    * Verbindingspoort voor FTP-besturingselement: 21
    * FTP-gegevens verbindings poort: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Hoe kan ik mijn code naar App Service publiceren?

De Snelstartgids van Azure is ontworpen om u te helpen uw app te implementeren met behulp van de implementatie stack en de methode van uw keuze. Als u de Snelstartgids wilt gebruiken, gaat u in het Azure Portal naar uw app service onder **implementatie** en selecteert u **Quick** start.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Waarom wordt mijn app soms opnieuw opgestart na de implementatie naar App Service?

Zie [implementatie versus runtime](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts")voor meer informatie over de omstandigheden waaronder een toepassings implementatie kan resulteren in een herstart. Zoals beschreven in het artikel App Service implementeert bestanden in de map wwwroot. De app wordt nooit direct opnieuw opgestart.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Hoe kan ik Azure DevOps-code integreren met App Service?

U hebt twee opties voor het gebruik van doorlopende implementatie met Azure DevOps:

*   Gebruik een Git-project. Maak verbinding via App Service met behulp van het implementatie centrum.
*   Gebruik een team Foundation Version Control (TFVC)-project. Implementeren met behulp van de build-agent voor App Service.

De implementatie van doorlopende code voor beide opties is afhankelijk van bestaande ontwikkel werk stromen en check-in-procedures. Raadpleeg deze artikelen voor meer informatie: 

*   [Continue implementatie van uw app implementeren op een Azure-website](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Stel een Azure DevOps-organisatie in zodat deze kan worden geïmplementeerd in een web-app](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Hoe kan ik FTP of FTPS gebruiken om mijn app op App Service te implementeren?

Voor informatie over het gebruik van FTP of FTPS om uw web-app te implementeren op App Service, raadpleegt u [uw app implementeren op app service met behulp van FTP/S](deploy-ftp.md).
