---
title: Overzicht Azure Blockchain Service
description: Overzicht van Azure Blockchain Service
ms.date: 01/04/2021
ms.topic: overview
ms.reviewer: ravastra
ms.openlocfilehash: 15d1126a98a4d3194a256ed07de3918cb066a4e2
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963875"
---
# <a name="what-is-azure-blockchain-service"></a>Wat is Azure Blockchain Service?

Azure Blockchain Service is een volledig beheerde grootboekservice waarmee gebruikers in staat zijn om blockchain-netwerken op schaal te vergroten of te bedienen in Azure. Door geïntegreerd beheer te bieden voor zowel infrastructuurbeheer als governance van blockchain-netwerken, biedt Azure Blockchain Service de volgende mogelijkheden:

* Eenvoudige netwerkimplementatie en -bewerkingen
* Ingebouwd consortiumbeheer
* Ontwikkel slimme contracten met vertrouwde ontwikkelprogramma's

Azure Blockchain Service is ontworpen om meerdere grootboekprotocollen te ondersteunen. Momenteel biedt de service ondersteuning voor het Ethereum [Quorum](https://www.goquorum.com/)-grootboek met behulp van het consensusmechanisme [Istanbul Byzantine Fault Tolerance (IBFT)](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus).

Deze mogelijkheden vereisen nauwelijks beheer, en alles wordt zonder extra kosten geleverd. U kunt zich richten op het ontwikkelen van apps en bedrijfslogica in plaats van het toewijzen van tijd en resources voor het beheren van virtuele machines en infrastructuur. U kunt bovendien de opensource-programma's en het platform van uw keuze blijven gebruiken om uw toepassing verder te ontwikkelen om uw oplossingen te leveren zonder dat u nieuwe vaardigheden hoeft te leren.

## <a name="network-deployment-and-operations"></a>Netwerkimplementatie en -bewerkingen

De implementatie van Azure Blockchain Service wordt uitgevoerd via de Azure-portal, Azure CLI of Visual Studio-code met behulp van de Azure Blockchain-extensie. Implementatie is vereenvoudigd, inclusief het inrichten van zowel transactie- als validatieknooppunten, virtuele Azure-netwerken voor beveiligingsisolatie en door service beheerde opslag.  Daarnaast maken gebruikers bij het implementeren van een nieuw blockchain-lid ook een consortium of voegen zich erbij.  Met consortiums kunnen meerdere partijen in verschillende Azure-abonnementen veilig communiceren met elkaar op een gedeelde blockchain.  Deze vereenvoudigde implementatie reduceert de blockchain-netwerkimplementatie van dagen naar minuten.

### <a name="performance-and-service-tiers"></a>Prestatie- en servicelagen

Azure Blockchain Service biedt twee servicelagen: *Basic* en *Standard*. Elke laag biedt verschillende prestaties en mogelijkheden voor het ondersteunen van lichte ontwikkelings- en testworkloads tot enorme schaalbare productie-implementaties van blockchains. Gebruik de *Basic*-laag voor ontwikkeling, tests en concepten. Gebruik de *Standard*-laag voor implementaties van productiekwaliteit. Beide lagen bevatten ten minste één transactieknooppunt en één validatieknooppunt (Basic) of twee validatieknooppunten (Standard). 

![Prijscategorieën](./media/overview/pricing-tiers.png)

Naast het aanbieden van twee validatieknooppunten biedt de *Standard*-laag twee *vCores* voor elke trans actie en een validatieknooppunt, terwijl de *Basic*-laag een 1 vCore-configuratie biedt.  Door 2 vCores voor transactie-en validatieknooppunten te bieden, kan 1 vCore worden toegewezen aan het Quorum-grootboek terwijl de resterende 1 vCore kan worden gebruikt voor andere infrastructuurservices, waardoor de prestaties van de productieworkloads voor blockchains optimaal zijn. Zie [prijzen voor Azure Blockchain Service](https://azure.microsoft.com/pricing/details/blockchain-service) voor meer informatie over de prijzen.

### <a name="security-and-maintenance"></a>Beveiliging en onderhoud

Nadat u uw eerste blockchain-lid hebt ingericht, hebt u de mogelijkheid om extra transactieknooppunten aan uw lid toe te voegen.  Transactieknooppunten worden standaard beveiligd via firewallregels en vereisen configuratie voor toegang.  Daarnaast versleutelen alle transactieknooppunten gegevens in beweging via TLS.  Er bestaan meerdere opties voor het beveiligen van toegang tot transactieknooppunten, zoals firewallregels, basisverificatie, toegangssleutels en integratie van Azure Active Directory. Zie [transactieknooppunten configureren](configure-transaction-nodes.md) en [toegang tot Azure Active Directory configureren](configure-aad.md) voor meer informatie.

Als beheerde service zorgt Azure Blockchain Service ervoor dat de knooppunten van uw blockchain-lid worden bijgewerkt met het nieuwste besturingssysteem van de host en stackupdates van de grootboeksoftware, geconfigureerd voor hoge beschikbaarheid (alleen Standard-laag), waardoor veel van de vereiste DevOps voor traditionele knooppunten voor IaaS-blockchains niet meer nodig zijn.  Raadpleeg voor meer informatie over patches en updates [ondersteunde Azure Blockchain Service-grootboekversies](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Bewaking en registratie

Daarnaast biedt Azure Blockchain Service uitgebreide metrische gegevens via Azure Monitor Service die inzichten biedt in het CPU-, geheugen- en opslaggebruik van knooppunten.  Azure Monitor biedt ook nuttige inzichten in netwerkactiviteiten van blockchains, zoals afgeleide transacties en blokken, de diepte van de transactiewachtrij en actieve verbindingen.  Metrische gegevens kunnen worden aangepast om weergaven te bieden in de inzichten die belangrijk zijn voor uw blockchain-toepassing.  Daarnaast kunnen drempelwaarden worden gedefinieerd via waarschuwingen, waardoor gebruikers acties kunnen activeren, zoals het verzenden van een e-mail of sms-bericht, het uitvoeren van een logische app, een Azure-functie of het verzenden naar een aangepaste webhook.

![Schermopname van bewaking met waarden voor blokken, transacties, transacties in behandeling en verwerkte aanvragen.](./media/overview/metrics.png)

Via Azure Log Analytics kunnen gebruikers logboeken weergeven die betrekking hebben op het Quorum-grootboek of andere belangrijke informatie, zoals pogingen tot verbinding met de transactieknooppunten.

## <a name="built-in-consortium-management"></a>Ingebouwd consortiumbeheer

Wanneer u uw eerste blockchain-lid implementeert, moet u lid worden van een consortium of een nieuw consortium maken.  Een consortium is een logische groep die wordt gebruikt voor het beheren van de governance en de connectiviteit tussen blockchain-leden die een proces voor meerdere partijen verwerken.  Azure Blockchain Service biedt ingebouwde besturingselementen voor governance via vooraf gedefinieerde slimme contracten die bepalen welke acties leden van het consortium kunnen ondernemen.  Deze besturingselementen voor governance kunnen zo nodig worden aangepast door de beheerder van het consortium. Wanneer u een nieuw consortium maakt, is uw blockchain-lid de standaardbeheerder van het consortium, waardoor andere partijen kunnen worden uitgenodigd om deel te nemen aan uw consortium.  U kunt alleen lid worden van een consortium als u eerder bent uitgenodigd.  Wanneer uw blockchain-lid zich bij een consortium voegt, valt uw blockchain-lid onder de besturingselementen voor governance die zijn ingesteld door de beheerder van het consortium.

![Consortiumbeheer](./media/overview/consortium.png)

Acties voor consortiumbeheer, zoals leden toevoegen aan en verwijderen uit een consortium, kunnen worden gebruikt via PowerShell en een REST API. U kunt een consortium programmatisch beheren met behulp van veelgebruikte interfaces, in plaats van op Solidity gebaseerde slimme contracten te wijzigen en te verzenden. Zie [consortiumbeheer](consortium.md) voor meer informatie.

## <a name="develop-using-familiar-development-tools"></a>Ontwikkelen met behulp van vertrouwde ontwikkelprogramma's

Op basis van het opensource-grootboek Quorum Ethereum, kunt u toepassingen voor Azure Blockchain Service op dezelfde manier ontwikkelen als voor bestaande Ethereum-toepassingen. Met de Visual Studio-code-extensie van Azure Blockchain Development Kit kunnen ontwikkelaars samen met toonaangevende partners werken om vertrouwde programma's zoals Truffle Suite inzetten, om slimme contracten te bouwen. Met behulp van de Azure Blockchain Visual Studio-code-extensie kunt u een bestaand consortium maken of er verbinding mee maken zodat u uw slimme contracten vanuit één IDE kunt bouwen en implementeren. Zie [Azure Blockchain Development Kit in de VS Code Marketplace](https://aka.ms/vscodebcextension) en de [gebruikershandleiding voor Azure Blockchain Development Kit](https://aka.ms/vscodebcextensionwiki) voor meer informatie.

## <a name="publish-blockchain-data"></a>Blockchain-gegevens publiceren

Blockchain Data Manager voor Azure Blockchain Service registreert, transformeert en levert Azure Blockchain Service-transactiegegevens aan Azure Event Grid-onderwerpen met betrouwbare en schaalbare blockchain-grootboekintegratie met Azure-services. U kunt Blockchain Data Manager gebruiken om toepassingen en gegevensarchieven uit de keten te integreren. Voor meer informatie raadpleegt u [Blockchain Data Manager voor Azure Blockchain Service](data-manager.md).

## <a name="support-and-feedback"></a>Ondersteuning en feedback

Voor nieuws voer Azure Blockchain gaat u naar de [Azure Blockchain-blog](https://azure.microsoft.com/blog/topics/blockchain/) om op de hoogte te blijven van aanbiedingen van blockchainservices en informatie van het technische team van Azure Blockchain.

Als u feedback over producten wilt geven of nieuwe functies wilt aanvragen, kunt u een idee plaatsen of erop stemmen via het [Azure-feedbackforum voor blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Ondersteuning voor community

In contact komen met Microsoft-technici en experts uit de Azure Blockchain-community.

* [Microsoft Q&A-vragenpagina voor Azure Blockchain Service](/answers/topics/azure-blockchain-service.html)
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-service)

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan, kunt u een quickstart proberen of meer informatie krijgen van deze resources.
* [Maak een blockchain-lid met behulp van de Azure-portal](create-member.md) of [maak een blockchain-lid met behulp van Azure CLI](create-member-cli.md)
* Volg het Microsoft Learn-pad [Aan de slag met blockchain-ontwikkeling](/learn/paths/ethereum-blockchain-development)
* Bekijk de [Beginner's series to blockchain](https://channel9.msdn.com/Series/Beginners-Series-to-Blockchain) (serie over blockchain voor beginners)
* Bekijk de [pagina met prijzen](https://azure.microsoft.com/pricing/details/blockchain-service) voor kostenvergelijkingen en calculators
* Uw eerste app bouwen met behulp van de [Azure Block Chain Development Kit](https://github.com/Azure-Samples/blockchain-devkit)
* [Gebruikershandleiding](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) voor Azure Block Chain VSCode-extensie
