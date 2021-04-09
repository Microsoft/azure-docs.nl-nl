---
title: Veelgestelde vragen over IoT Solution Accelerators-Azure | Microsoft Docs
description: In dit artikel worden de veelgestelde vragen voor IoT-oplossings Accelerators beantwoord. Het bevat koppelingen naar de GitHub-opslag plaatsen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 1fd2b8461bd66c826dc4890c331b740c4703f896
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96903986"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Veelgestelde vragen over IoT-oplossingsversnellers

Zie ook de [verbonden specifieke, vooraf gedefinieerde standaard Veelgestelde vragen](iot-accelerators-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Waar vind ik de bron code voor de oplossings Accelerators?

De bron code wordt opgeslagen in de volgende GitHub-opslag plaatsen:

* [Oplossings versneller Connected Factory](https://github.com/Azure/azure-iot-connected-factory)
* [Versneller oplossing apparaat simulatie](https://github.com/Azure/device-simulation-dotnet)

### <a name="where-can-i-find-the-remote-monitoring-and-predictive-maintenance-solution-accelerators"></a>Waar vind ik de oplossings versnellers voor controle op afstand en voorspellende onderhoud?

Vanaf 10 december 2020 zijn de Accelerators voor controle op afstand en voor speld onderhoud verwijderd van de site met [accelerators van Azure IOT-oplossingen](https://www.azureiotsolutions.com/Accelerators) en zijn ze niet langer beschikbaar voor nieuwe implementaties. De GitHub-opslag plaatsen voor beide Accelerators zijn gearchiveerd. De code is nog steeds beschikbaar voor iedereen om toegang te krijgen, maar de opslag plaatsen nemen geen nieuwe bijdragen in rekening.

### <a name="what-happens-to-my-existing-remote-monitoring-and-predictive-maintenance-deployments"></a>Wat gebeurt er met mijn bestaande externe bewakings-en voorspellende onderhouds implementaties?

Bestaande implementaties worden niet beïnvloed door het verwijderen van de oplossings Accelerators voor externe controle en voorspellende onderhoud en blijven werken. Gevorkte opslag plaatsen worden ook niet beïnvloed. De hoofd opslagplaatsen op GitHub zijn gearchiveerd.

### <a name="how-do-i-deploy-device-simulation-solution-accelerator"></a>De oplossings versneller voor het simuleren van apparaten Hoe kan ik?

Zie de [device simulatie](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) github-opslag plaats voor het implementeren van de apparaat simulatie oplossings versneller.

### <a name="where-can-i-find-information-about-the-removed-solution-accelerators"></a>Waar vind ik informatie over de verwijderde oplossings Accelerators?

Zie de volgende pagina's op de site van de vorige versie:

* [Externe bewaking](/previous-versions/azure/iot-accelerators/about-iot-accelerators)
* [Voorspellend onderhoud](/previous-versions/azure/iot-accelerators/about-iot-accelerators)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Welke Sdk's kan ik gebruiken om apparaatclients te ontwikkelen voor de oplossings Accelerators?

U vindt koppelingen naar de verschillende talen (C, .NET, Java, Node.js, python) IoT-apparaat-Sdk's in de [Microsoft Azure IOT sdk's](https://github.com/Azure/azure-iot-sdks) github-opslag plaatsen.

Als u het DevKit-apparaat gebruikt, kunt u bronnen en voor beelden vinden in de DevKit-opslag plaats van [IOT](https://github.com/Microsoft/devkit-sdk) github.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Ik ben een service beheerder en ik wil de adreslijst toewijzing wijzigen tussen mijn abonnement en een specifieke Azure AD-Tenant. Wilt u deze taak Hoe kan ik volt ooien?

Zie [een bestaand abonnement toevoegen aan uw Azure AD-adres lijst](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Ik wil een service beheerder of Co-Administrator wijzigen wanneer u bent aangemeld met een organisatie account

Zie de ondersteunings artikelen [service beheerder en Co-Administrator wijzigen wanneer u bent aangemeld met een organisatie account](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Waarom wordt deze fout weer gegeven? "Uw account beschikt niet over de juiste machtigingen om een oplossing te maken. Neem contact op met uw account beheerder of probeer met een ander account.

Bekijk het volgende diagram voor hulp:

![Toestemmings stroomdiagram](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Als u de fout blijft zien nadat u een globale beheerder bent van de Azure AD-Tenant en een mede beheerder van het abonnement, moet uw account beheerder de gebruiker verwijderen en de benodigde machtigingen opnieuw toewijzen in deze volg orde. Voeg eerst de gebruiker toe als globale beheerder en voeg vervolgens een gebruiker toe als co-beheerder van het Azure-abonnement. Neem contact op met de [ondersteuning van hulp &](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)als er problemen blijven optreden.

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Waarom wordt deze fout weer gegeven wanneer ik een Azure-abonnement heb? "Een Azure-abonnement is vereist voor het maken van vooraf geconfigureerde oplossingen. U kunt in slechts een paar minuten een gratis proef account maken. "

Als u zeker weet dat u een Azure-abonnement hebt, valideert u de Tenant toewijzing voor uw abonnement en controleert u of de juiste Tenant is geselecteerd in de vervolg keuzelijst. Als u de Tenant juist hebt gevalideerd, volgt u het voor gaande diagram en valideert u de toewijzing van uw abonnement en deze Azure AD-Tenant.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Wat is het verschil tussen het verwijderen van een resource groep in de Azure Portal en het klikken op verwijderen in een oplossings versneller in azureiotsolutions.com?

* Als u de oplossings versneller in [azureiotsolutions.com](https://www.azureiotsolutions.com/)verwijdert, verwijdert u alle resources die zijn geïmplementeerd tijdens het maken van de oplossings versneller. Als u extra resources aan de resource groep hebt toegevoegd, worden deze resources ook verwijderd.
* Als u de resource groep in de [Azure Portal](https://portal.azure.com)verwijdert, verwijdert u alleen de resources in die resource groep. U moet ook de Azure Active Directory toepassing verwijderen die aan de oplossings versneller is gekoppeld.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Kan ik mijn bestaande investeringen in azure IoT-oplossings Accelerators blijven gebruiken?

Ja. Alle oplossingen die nu bestaan, blijven werken in uw Azure-abonnement en de bron code blijft beschikbaar in GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hoeveel IoT Hub exemplaren kan ik inrichten in een abonnement?

U kunt standaard [10 IOT-hubs inrichten per abonnement](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits). U kunt een [ondersteunings ticket voor Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) maken om deze limiet te verhogen. Omdat elke oplossings versneller een nieuw IoT Hub heeft, kunt u Maxi maal 10 oplossings Accelerators inrichten in een bepaald abonnement.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hoeveel Azure Cosmos DB exemplaren kan ik inrichten in een abonnement?

50. U kunt een [ondersteunings ticket voor Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) maken om deze limiet te verhogen, maar standaard kunt u maxi maal 50 exemplaren van Cosmos DB per abonnement inrichten.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Kan ik een oplossings versneller maken als ik Microsoft Azure heb voor DreamSpark?

> [!NOTE]
> Microsoft Azure voor DreamSpark is nu bekend als Microsoft Imagine voor studenten.

Op dit moment kunt u geen oplossings versneller maken met een [Microsoft Azure voor DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) -account. U kunt echter in slechts een paar minuten een [gratis proef account voor Azure](https://azure.microsoft.com/free/) maken, waardoor u een oplossings versneller maakt.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Hoe kan ik een Azure AD-Tenant verwijderen?

Zie het blog bericht over het [verwijderen van een Azure AD-Tenant met](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant)de bespreking van het weblog boek van Eric golpe.

### <a name="next-steps"></a>Volgende stappen

U kunt ook enkele van de andere functies en mogelijkheden van de IoT-oplossingsversnellers bekijken:

* [Connected Factory Solution Accelerator implementeren](quickstart-connected-factory-deploy.md)
* [Fundamentele IoT-beveiliging](../iot-fundamentals/iot-security-ground-up.md)