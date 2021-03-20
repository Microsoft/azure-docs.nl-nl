---
title: Beheer hulpprogramma voor virtueel bureau blad van Windows-Azure
description: Problemen oplossen met het beheer programma voor virtueel bureau blad (klassiek) van Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6f03284103b4a2aa6900bf1ba5c50a4688c50b0e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88005479"
---
# <a name="troubleshoot-the-windows-virtual-desktop-classic-management-tool"></a>Problemen oplossen met het beheer programma voor virtuele Bureau bladen van Windows (klassiek)

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten.

In dit artikel worden problemen beschreven die zich kunnen voordoen tijdens de implementatie van het Windows-hulp programma virtueel bureau blad-beheer en hoe u deze kunt oplossen.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Fout: beheer hulpprogramma Services geconfigureerd, maar automatische installatie mislukt

Wanneer u de services voor het beheer programma hebt ingesteld, maar de automatische installatie mislukt, wordt dit fout bericht weer gegeven:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Dit betekent meestal een van de volgende twee dingen:

- De gebruiker heeft eigenaars machtigingen voor hun abonnement en globale beheerder op Tenant niveau, maar kan zich niet aanmelden bij Azure.
- Multi-factor Authentication is ingeschakeld voor de account instellingen van de gebruiker.

U kunt dit als volgt oplossen:

1. Zorg ervoor dat de gebruiker die u hebt gemaakt voor de Azure Active Directory User Principal Name, het abonnements niveau ' bijdrager ' heeft.
2. Meld u aan bij <portal.azure.com> met het UPN-account om de account instellingen te controleren en ervoor te zorgen dat multi-factor Authentication niet is ingeschakeld. Als de functie is ingeschakeld, schakelt u deze uit.
3. Ga naar de Windows-pagina toestemming voor virtueel bureau blad en controleer of de server-en client-apps toestemming hebben.
4. Bekijk de zelf studie [een beheer programma implementeren](manage-resources-using-ui.md) als het probleem zich blijft voordoen en het hulp programma opnieuw te implementeren.

## <a name="error-job-with-specified-id-already-exists"></a>Fout: er bestaat al een taak met de opgegeven ID

Als uw gebruiker het fout bericht met de opgegeven ID al bestaat, ziet u dat ze geen unieke naam hebben opgegeven in de para meter ' toepassings naam ' bij het implementeren van de sjabloon.

U kunt dit oplossen door het beheer programma opnieuw te implementeren met de para meter ' toepassings naam ' gevuld.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Vraag om toestemming bij het openen van het beheer programma

Wanneer u het beheer programma implementeert, wordt de prompt voor toestemming mogelijk niet meteen geopend. Dit betekent dat de Azure web app-service langer duurt dan normaal om te laden. De prompt moet worden weer gegeven nadat het laden van Azure web is voltooid.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>De gebruiker kan het beheer programma niet implementeren in de regio VS Oost

Als een klant de regio in VS Oost heeft ingesteld, kan het beheer programma niet worden geïmplementeerd.

U kunt dit oplossen door het beheer programma in een andere regio te implementeren. Het opnieuw implementeren van het hulp programma in een andere regio is niet van invloed op de gebruikers ervaring.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over escalatie trajecten bij het [oplossen van problemen met overzicht, feedback en ondersteuning](troubleshoot-set-up-overview-2019.md).
- Meer informatie over het rapporteren van problemen met virtuele bureau blad-hulpprogram ma's van Windows op [arm-sjablonen voor extern bureaublad-services](https://github.com/Azure/RDS-Templates/blob/master/README.md).
- Zie [een beheer programma implementeren](manage-resources-using-ui.md)voor meer informatie over het implementeren van het beheer programma.