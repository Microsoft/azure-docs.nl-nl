---
title: 'Azure Defender for App Service: de voordelen en functies'
description: Meer informatie over de mogelijkheden van Azure Defender voor App Service en hoe u deze functie inschakelt voor uw abonnement
author: memildin
ms.author: memildin
ms.date: 01/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 200e1fd7bfffef403fa459d3de13dc31145b8a33
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796635"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>Inleiding tot Azure Defender voor App Service

Azure App Service is een volledig beheerd platform voor het bouwen en hosten van uw web-apps en Api's. Omdat het platform volledig wordt beheerd, hoeft u zich geen zorgen te maken over de infra structuur. Het biedt beheer, bewaking en operationele inzichten om te voldoen aan de hoge vereisten voor prestaties, beveiliging en naleving van ondernemingen. Zie [Azure App Service](https://azure.microsoft.com/services/app-service/) voor meer informatie.

**In Azure Defender voor App Service** wordt gebruikgemaakt van de schaal van de cloud om aanvallen te identificeren die gericht zijn op toepassingen die via App Service worden uitgevoerd. Aanvallers testen voortdurend webtoepassingen om zwakke plekken te vinden en daar misbruik van te maken. Aanvragen voor toepassingen die in Azure worden uitgevoerd, gaan voordat ze naar specifieke omgevingen worden doorgestuurd door verschillende gateways, waar ze worden geïnspecteerd en geregistreerd. Deze gegevens worden vervolgens gebruikt om aanvallen en aanvallers te identificeren en om nieuwe patronen te leren die later worden gebruikt.


## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemeen verkrijgbaar (GA)|
|Prijzen:|Voor [Azure Defender for App Service](azure-defender.md) gelden de prijzen op de [pagina Prijzen](security-center-pricing.md)<br>Op de pagina prijzen en instellingen wordt het aantal exemplaren voor de **resource hoeveelheid** weer gegeven. Dit is het totale aantal reken instanties, in alle App Service plannen voor dit abonnement, dat wordt uitgevoerd op het moment dat u de pagina prijs categorie hebt geopend.<br>Als u het aantal wilt valideren, opent u **app service plannen** in de Azure Portal en controleert u het aantal reken instanties dat door elk abonnement wordt gebruikt.|
|Ondersteunde App Service-plannen:|![Ja](./media/icons/yes-icon.png) Basic, Standard, Premium, Isolated of Linux<br>![Nee](./media/icons/no-icon.png) Free, Shared of Consumption<br>[Meer informatie over App Service-plannen](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Nee](./media/icons/no-icon.png) Nationaal/onafhankelijk (overheid van de VS, China, andere overheden)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-app-service"></a>Wat zijn de voor delen van Azure Defender voor App Service?

Wanneer u Azure Defender voor App Service inschakelt, kunt u direct profiteren van de volgende services die worden aangeboden door dit Azure Defender-abonnement:

- **Secure** -Security Center evalueert de resources die door uw app service plan worden gedekt en genereert beveiligings aanbevelingen op basis van de bevindingen. Volg de gedetailleerde instructies in deze aanbevelingen om uw App Service bronnen te beveiligen.

- **Detecteren** : Azure Defender detecteert een groot aantal bedreigingen voor uw app service-resources door de bewaking van
    - het VM-exemplaar waarin uw App Service wordt uitgevoerd, en de bijbehorende beheer interface
    - de aanvragen en antwoorden die zijn verzonden naar en van uw App Service-apps
    - de onderliggende sandboxes en virtuele machines
    - App Service interne logboeken-beschikbaar dankzij de zicht baarheid van Azure als een Cloud provider

Als een Cloud-systeem eigen oplossing kan Azure Defender aanvals methoden identificeren die van toepassing zijn op meerdere doelen. Vanaf één enkele host zou het lastig zijn om een gedistribueerde aanval vanuit een kleine subset van IP-adressen te identificeren en te verkennen naar vergelijk bare eind punten op meerdere hosts.

De logboek gegevens en de infra structuur kunnen samen het verhaal vertellen: van een nieuwe aanval die in het wild wordt circuleren naar inbreuk op de klant machines. Daarom kan Security Center, zelfs als het pas wordt geïmplementeerd nadat een web-app al is aangetast, mogelijk aanvallen die aan de gang zijn detecteren.


## <a name="what-threats-can-azure-defender-for-app-service-detect"></a>Welke bedreigingen kunnen Azure Defender voor App Service detecteren?

### <a name="threats-by-mitre-attck-tactics"></a>Bedreigingen per MITRE ATT&verzonken tactiek

Azure Defender bewaakt een groot aantal bedreigingen voor uw App Service-resources. De waarschuwingen dekken bijna de volledige lijst met MITRE ATT&verzonken-tactiek van de pre-aanval tot opdracht en controle. Azure Defender kan detecteren:

- **Bedreigingen voor aanvallen** -Defender kan de uitvoering detecteren van meerdere typen beveiligings problemen die aanvallers vaak gebruiken om toepassingen te testen op zwakke plekken.

- **Eerste toegangs bedreigingen**  -  [Micro soft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) voorziet in deze waarschuwingen waarbij een waarschuwing wordt gegenereerd wanneer een bekend schadelijk IP-adres verbinding maakt met uw Azure app service FTP-interface.

- **Uitvoerings bedreigingen** : Defender kan pogingen detecteren om opdrachten met hoge bevoegdheden uit te voeren, Linux-opdrachten op een Windows app service, aanvals gedrag op basis van bestanden, hulpprogram ma's voor de analyse van digitale valuta en vele andere activiteiten voor het uitvoeren van verdachte en schadelijke code.

### <a name="dangling-dns-detection"></a>Dangling DNS-detectie

Azure Defender voor App Service identificeert ook eventuele DNS-vermeldingen die resteren in uw DNS-registratie-instantie wanneer een App Service website buiten gebruik wordt gesteld. dit worden Dangling DNS-vermeldingen genoemd. Wanneer u een website verwijdert en het aangepaste domein niet uit uw DNS-REGI ster verwijdert, verwijst de DNS-vermelding naar een niet-bestaande resource en is uw subdomein kwetsbaar voor een overname. Azure Defender scant uw DNS-REGI ster niet op *bestaande* Dangling DNS-vermeldingen. u wordt gewaarschuwd wanneer een App Service website buiten gebruik wordt gesteld en het aangepaste domein (DNS-vermelding) niet wordt verwijderd.

De overname van subdomeinen is een veelvoorkomende bedreiging van hoge urgentie voor organisaties. Wanneer een Threat actor een DNS-vermelding van Dangling detecteert, maken ze hun eigen site op het doel adres. Het verkeer dat is bedoeld voor het domein van de organisatie, wordt vervolgens doorgestuurd naar de site van de Threat actor en kan dat verkeer gebruiken voor een breed scala aan schadelijke activiteiten.

Dangling DNS-beveiliging is beschikbaar, ongeacht of uw domeinen worden beheerd met Azure DNS of een externe domein registratie en van toepassing zijn op App Service in Windows en Linux.

:::image type="content" source="media/defender-for-app-service-introduction/dangling-dns-alert.png" alt-text="Een voor beeld van een Azure Defender-waarschuwing over een gedetecteerde DNS-vermelding in Dangling. Schakel Azure Defender voor App Service in om deze en andere waarschuwingen voor uw omgeving te ontvangen." lightbox="media/defender-for-app-service-introduction/dangling-dns-alert.png":::

Meer informatie over Dangling DNS en de dreiging van subdomein overname, in [Dangling DNS-vermeldingen voor komen en de overname van subdomeinen voor komen](../security/fundamentals/subdomain-takeover.md).

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azureappserv)voor een volledige lijst van de Azure app Service waarschuwingen.

> [!NOTE]
> Mogelijk worden Dangling DNS-waarschuwingen niet geactiveerd als uw aangepaste domein niet rechtstreeks naar een App Service Resource verwijst, of als Defender geen verkeer naar uw website heeft bewaakt sinds de Dangling DNS-beveiliging is ingeschakeld (omdat er geen logboeken zijn om te helpen bij het identificeren van het aangepaste domein).

## <a name="how-to-protect-your-azure-app-service-web-apps-and-apis"></a>Uw Azure App Service web-apps en Api's beveiligen

U kunt als volgt uw Azure App Service plan beveiligen met Azure Defender voor App Service:

1. Zorg ervoor dat u een ondersteund App Service-plan hebt dat is gekoppeld aan toegewezen machines. Ondersteunde abonnementen worden hierboven vermeld onder [Beschikbaarheid](#availability).

2. Schakel **Azure Defender** in voor uw abonnement, zoals beschreven in de [prijs van Azure Security Center](security-center-pricing.md).

    U kunt optioneel afzonderlijke abonnementen inschakelen in azure Defender (zoals Azure Defender voor App Service).

    Security Center is systeemeigen geïntegreerd met App Service, waardoor implementatie en onboarding niet meer nodig is; de integratie is transparant.


## <a name="next-steps"></a>Volgende stappen

In dit artikel bent u meer te weten gekomen over Azure Defender voor App Service. 

Raadpleeg de volgende artikelen voor gerelateerd materiaal: 

- Als u waarschuwingen wilt exporteren naar Azure Sentinel, een extern SIEM of een ander extern hulpprogramma, volgt u de instructies in [Waarschuwingen streamen naar een SIEM-, SOAR- of IT Service Management-oplossing](export-to-siem.md).
- Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azureappserv)voor een lijst met de waarschuwingen voor Azure Defender voor app service.
- Zie [App Service-plannen](https://azure.microsoft.com/pricing/details/app-service/plans/) voor meer informatie over App Service-plannen.
> [!div class="nextstepaction"]
> [Azure Defender inschakelen](security-center-pricing.md#enable-azure-defender)