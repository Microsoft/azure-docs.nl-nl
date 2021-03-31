---
title: Populaire scenario's voor het gebruik van Azure DevTest Labs
description: Dit artikel bevat de belangrijkste scenario's voor het gebruik van Azure DevTest Labs en twee algemene paden om te beginnen met het gebruik van de service in uw organisatie.
ms.topic: article
ms.date: 06/20/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1adb9b009a250942bab7e9b0e3b4b3e52840341c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85481592"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Populaire scenario's voor het gebruik van Azure DevTest Labs
Afhankelijk van de behoeften van een onderneming kunnen DevTest Labs worden geconfigureerd om te voldoen aan verschillende vereisten.  In dit artikel worden de populaire scenario's besproken. Elk scenario gaat over de voor delen die zijn gemaakt met behulp van DevTest Labs en resources voor het implementeren van die scenario's.  

- Ontwikkel aars Desk tops
- Test omgevingen
- Trainings sessies, praktijk gerichte lessen en hackathons trappen
- Sandbox-onderzoeken
- Leslokaallabs

## <a name="developer-desktops"></a>Ontwikkel aars Desk tops
Ontwikkel aars hebben vaak verschillende vereisten voor ontwikkel machines voor verschillende projecten. Met DevTest Labs kunnen ontwikkel aars toegang hebben tot virtuele machines op aanvraag die zijn geconfigureerd om te voldoen aan de meest voorkomende scenario's. DevTest Labs biedt de volgende voor delen:

- Organisaties kunnen algemene ontwikkel machines bieden die consistentie tussen teams garanderen.
- Ontwikkel aars kunnen snel hun ontwikkel machines op aanvraag inrichten of [een bestaande vooraf geconfigureerde machine claimen](devtest-lab-add-claimable-vm.md).
- Ontwikkel aars kunnen resources inrichten op een self-service manier zonder machtigingen op abonnements niveau.
- IT-beheerders kunnen [de netwerk topologie vooraf definiëren](devtest-lab-configure-vnet.md) en ontwikkel aars kunnen deze rechtstreeks op een eenvoudige en intuïtieve manier gebruiken zonder speciale toegang.
- Ontwikkel aars kunnen hun ontwikkel machines eenvoudig [aanpassen](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) als dat nodig is.
- Beheerders kunnen kosten bepalen door ervoor te zorgen dat:
    - Ontwikkel aars [kunnen niet meer vm's ophalen](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) dan ze nodig hebben voor ontwikkeling
    - [Vm's worden afgesloten wanneer ze](devtest-lab-set-lab-policy.md#set-auto-shutdown) niet worden gebruikt
    - Alleen [een subset van VM-exemplaar grootten](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) voor de specifieke Labs toestaan
    - Het beheren van de [kosten doelen en meldingen](devtest-lab-configure-cost-management.md) voor elk lab.

Zie [Azure DevTest Labs gebruiken voor ontwikkel aars](devtest-lab-developer-lab.md)voor meer informatie. 

## <a name="test-environments"></a>Test omgevingen
Het maken en beheren van test omgevingen binnen een onderneming kan veel moeite vergen. Met DevTest Labs kunnen test omgevingen eenvoudig worden gemaakt, bijgewerkt of gedupliceerd. Teams kunnen toegang krijgen tot een volledig geconfigureerde omgeving wanneer dat nodig is. In dit scenario biedt DevTest Labs de volgende voor delen:

- Organisaties kunnen algemene test omgevingen bieden die consistentie tussen teams garanderen.
- Testers kunnen de nieuwste versie van de toepassing testen door Windows-en Linux-omgevingen snel in te richten met behulp van herbruikbare sjablonen.
- Beheerders kunnen het lab verbinden met Azure DevOps om DevOps-scenario's in te scha kelen
- Lab-eigen aren kunnen kosten bepalen door ervoor te zorgen dat:
    - [Vm's in omgevingen worden afgesloten wanneer ze](devtest-lab-set-lab-policy.md#set-auto-shutdown) niet worden gebruikt
    - Alleen [een subset van VM-exemplaar grootten voor](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) de specifieke Labs toestaan
    - Het beheren van de [kosten doelen en meldingen](devtest-lab-configure-cost-management.md) voor elk lab.

Zie [Azure DevTest Labs gebruiken voor VM-en Paas-test omgevingen](devtest-lab-test-env.md)voor meer informatie.

## <a name="sandboxed-investigations"></a>Sandbox-onderzoeken
Ontwikkel aars onderzoeken vaak verschillende technologieën of infrastructuur ontwerpen. Standaard worden alle omgevingen die zijn gemaakt met DevTest Labs gemaakt in hun eigen resource groep. De DevTest Labs-gebruiker krijgt alleen lees toegang tot deze resources. Voor ontwikkel aars die meer controle nodig hebben, kan een Lab-brede instelling echter worden bijgewerkt om [bijdrage rechten](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) te verlenen aan de oorspronkelijke DevTest Labs-gebruiker voor elke omgeving die ze maken.  Met DevTest Labs kunnen ontwikkel aars automatisch Inzender machtigingen krijgen voor omgevingen die ze in het lab maken.  Dit scenario stelt ontwikkel aars in staat om Azure-resources toe te voegen en/of te wijzigen wanneer ze nodig zijn voor de ontwikkelings-en test omgevingen. Op de pagina [kosten per resource](devtest-lab-configure-cost-management.md#view-cost-by-resource) kunnen Lab-eigen aren de kosten bijhouden van elke omgeving die wordt gebruikt voor onderzoek.

Zie [toegangs rechten voor een omgevings resource groep instellen](https://aka.ms/dtl-sandbox)voor meer informatie.

## <a name="trainings-hands-on-labs-and-hackathons"></a>Trainingen, praktijk gerichte lessen en hackathons trappen 
Een lab in Azure DevTest Labs fungeert als een goede container voor tijdelijke activiteiten zoals workshops, praktijk gerichte lessen, training of hackathons trappen.  Met de service kunt u een lab maken waarin u aangepaste sjablonen kunt opgeven die elke getrainde kan gebruiken om identieke en geïsoleerde omgevingen te maken voor training. In dit scenario biedt DevTest Labs de volgende voor delen:

- [Beleids regels](devtest-lab-set-lab-policy.md) zorgen ervoor dat trainees alleen het aantal resources, zoals virtuele machines, kan ophalen dat ze nodig hebben.
- Vooraf geconfigureerde en gemaakte machines worden [geclaimd](devtest-lab-add-claimable-vm.md) met één actie van een getrainde.
- Labs wordt gedeeld met trainees door toegang te krijgen tot [de URL voor het lab](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab).
- [Verval datums](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) op virtuele machines zorgen ervoor dat computers worden verwijderd nadat ze niet meer nodig zijn.
- Het is eenvoudig om [een Lab](devtest-lab-delete-lab-vm.md#delete-a-lab) en alle [gerelateerde resources](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) te verwijderen wanneer de training is afgelopen.

Zie [Azure DevTest Labs gebruiken voor trainingen](devtest-lab-training-lab.md)voor meer informatie.  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Testen van concepten versus schaal bare implementatie
Wanneer u besluit DevTest Labs te verkennen, worden er twee algemene paden door lopen: testen van concepten versus schaal bare implementatie.  

Een **geschaalde implementatie** bestaat uit weken/maanden van het beoordelen en plannen van een intentie van het implementeren van DevTest Labs voor de hele onderneming met honderden of duizenden ontwikkel aars.

Een **testen van de concept** implementatie richt zich op een geconcentreerde inspanning van één team om de organisatie waarde te bepalen. Hoewel het een geschaalde implementatie kan oplopen, is de aanpak vaak vaker dan de optie testen van het concept. Daarom wordt u aangeraden kleine, meer inzicht te krijgen in het eerste team, dezelfde benadering te herhalen met twee tot drie extra teams en vervolgens te plannen voor een geschaalde implementatie op basis van de kennis die is opgedaan. Voor een geslaagde proef versie raden wij u aan een of twee teams te kiezen en hun scenario's te identificeren (ontwikkel omgeving versus test omgevingen), de huidige gebruiks voorbeelden te documenteren en DevTest Labs te implementeren.

## <a name="next-steps"></a>Volgende stappen
Lees de volgende artikelen:

- [DevTest Labs-concepten](devtest-lab-concepts.md)
- [Veelgestelde vragen over DevTest Labs](devtest-lab-faq.md)

