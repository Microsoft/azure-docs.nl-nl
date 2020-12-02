---
title: 'Azure Defender for App Service: de voordelen en functies'
description: Meer informatie over de voordelen en functies van Azure Defender voor App Service.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: bb0e073d5ccf73434d05c801b9a8727c1d19fa47
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122241"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>Inleiding tot Azure Defender voor App Service

Azure App Service is een volledig beheerd platform voor het bouwen en hosten van uw web-apps en API's zonder dat u zich zorgen hoeft te maken over het beheer van de infrastructuur. Het biedt beheer, bewaking en operationele inzichten om te voldoen aan de hoge vereisten voor prestaties, beveiliging en naleving van ondernemingen. Zie [Azure App Service](https://azure.microsoft.com/services/app-service/) voor meer informatie.

**In Azure Defender voor App Service** wordt gebruikgemaakt van de schaal van de cloud om aanvallen te identificeren die gericht zijn op toepassingen die via App Service worden uitgevoerd. Aanvallers testen voortdurend webtoepassingen om zwakke plekken te vinden en daar misbruik van te maken. Aanvragen voor toepassingen die in Azure worden uitgevoerd, gaan voordat ze naar specifieke omgevingen worden doorgestuurd door verschillende gateways, waar ze worden geïnspecteerd en geregistreerd. Deze gegevens worden vervolgens gebruikt om aanvallen en aanvallers te identificeren en om nieuwe patronen te leren die later worden gebruikt.

Door gebruik te maken van de zichtbaarheid van Azure als cloudprovider, analyseert Security Center interne App Service-logboeken om een aanvalsmethodologie op meerdere doelen te identificeren. Zo kan de methodologie bijvoorbeeld wijdverspreide scans en gedistribueerde aanvallen omvatten. Dit type aanval is doorgaans afkomstig van een kleine subset van IP-adressen en toont patronen met crawlen naar vergelijkbare eindpunten op meerdere hosts. De aanvallen zoeken naar een kwetsbare pagina of invoegtoepassing en kunnen niet worden geïdentificeerd vanuit het standpunt van één host.


## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemeen verkrijgbaar (GA)|
|Prijzen:|Voor [Azure Defender for App Service](azure-defender.md) gelden de prijzen op de [pagina Prijzen](security-center-pricing.md)|
|Ondersteunde App Service-plannen:|![Ja](./media/icons/yes-icon.png) Basic, Standard, Premium, Isolated of Linux<br>![Nee](./media/icons/no-icon.png) Free, Shared of Consumption<br>[Meer informatie over App Service-plannen](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Nee](./media/icons/no-icon.png) Nationaal/onafhankelijk (overheid van de VS, China, andere overheden)|
|||

## <a name="what-does-azure-defender-for-app-service-protect"></a>Wat beveiligt Azure Defender voor App Service?

Wanneer het App Service-abonnement is ingeschakeld, beoordeelt Security Center de resources die door uw App Service plan worden gedekt en genereert beveiligingsaanbevelingen op basis van de bevindingen. Security Center beveiligt het VM-exemplaar waarin uw App Service wordt uitgevoerd en de beheerinterface. Daarnaast worden ook aanvragen en antwoorden gecontroleerd die worden verzonden naar en van uw apps die worden uitgevoerd in App Service.

Als u een App Service-plan uitvoert onder Windows, heeft Security Center ook toegang tot de onderliggende sandboxes en VM's. Samen met de hierboven vermelde logboekgegevens kan de infrastructuur het verhaal vertellen, van een nieuwe aanval die in het wild circuleert tot aantasting van machines van klanten. Daarom kan Security Center, zelfs als het pas wordt geïmplementeerd nadat een web-app al is aangetast, mogelijk aanvallen die aan de gang zijn detecteren.


## <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Web-apps en API's van Azure App Service beveiligen
U kunt als volgt uw Azure App Service plan beveiligen met Azure Defender voor App Service:

- Zorg ervoor dat u een ondersteund App Service-plan hebt dat is gekoppeld aan toegewezen machines. Ondersteunde abonnementen worden hierboven vermeld onder [Beschikbaarheid](#availability).

- Schakel **Azure Defender** in voor uw abonnement (u kunt optioneel alleen het **Azure Defender voor App Service**-plan inschakelen), zoals beschreven in [Prijzen van Azure Security Center](security-center-pricing.md)

Security Center is systeemeigen geïntegreerd met App Service, waardoor implementatie en onboarding niet meer nodig is; de integratie is transparant.

>[!NOTE]
> De prijzen- en instellingenpagina bevat een aantal exemplaren voor uw **Resourcehoeveelheid**. Dit is het totale aantal rekenprocessen in alle App Service-plannen van dit abonnement dat actief was op het moment dat u de prijscategoriepagina hebt geopend.
>
> Azure App Service biedt diverse plannen. Uw App Service-plan definieert de set rekenresources waarmee een web-app kan worden uitgevoerd. Die komen overeen met serverfarms in conventionele webhosting. Een of meer apps kunnen worden geconfigureerd om te worden uitgevoerd op dezelfde rekenresources (of in hetzelfde App Service-plan).
>
>Als u het aantal wilt valideren, gaat u naar 'App Service-plannen' in de Azure-portal, waar u het aantal rekenprocessen kunt zien dat elk plan gebruikt. 



## <a name="next-steps"></a>Volgende stappen

In dit artikel bent u meer te weten gekomen over Azure Defender voor App Service. 

Raadpleeg de volgende artikelen voor gerelateerd materiaal: 

- Een waarschuwing kunt u altijd exporteren, ongeacht of deze door Security Center is gegenereerd of door Security Center is ontvangen vanuit een ander beveiligingsproduct. Als u waarschuwingen wilt exporteren naar Azure Sentinel, een extern SIEM of een ander extern hulpprogramma, volgt u de instructies in [Waarschuwingen streamen naar een SIEM-, SOAR- of IT Service Management-oplossing](export-to-siem.md).
- Zie de [Referentietabel met waarschuwingen](alerts-reference.md#alerts-azureappserv) voor een lijst met de Azure App Service-waarschuwingen.
- Zie [App Service-plannen](https://azure.microsoft.com/pricing/details/app-service/plans/) voor meer informatie over App Service-plannen.
- > [!div class="nextstepaction"]
    > [Azure Defender inschakelen](security-center-pricing.md)