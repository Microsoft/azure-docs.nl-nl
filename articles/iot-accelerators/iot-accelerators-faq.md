---
title: Veelgestelde vragen over IoT-oplossingsversnellers - Azure | Microsoft Docs
description: In dit artikel vindt u antwoorden op de veelgestelde vragen over IoT-oplossingsversnellers. Het bevat koppelingen naar de GitHub-opslagplaatsen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 61993df77b0831926f16339a741a2553e80c2a0d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713836"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Veelgestelde vragen over IoT-oplossingsversnellers

Zie ook de [veelgestelde vragen over verbonden factory's.](iot-accelerators-faq-cf.md)

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Waar vind ik de broncode voor de oplossingsversnellers?

De broncode wordt opgeslagen in de volgende GitHub-opslagplaatsen:

* [Oplossingsversneller voor verbonden factory's](https://github.com/Azure/azure-iot-connected-factory)
* [Oplossingsversneller voor apparaatsimulatie](https://github.com/Azure/azure-iot-pcs-device-simulation)

### <a name="where-can-i-find-the-remote-monitoring-and-predictive-maintenance-solution-accelerators"></a>Waar vind ik de oplossingsversnellers voor externe bewaking en voorspeld onderhoud?

Vanaf 10 december 2020 zijn de versnellers voor externe bewaking en voorspeld onderhoud verwijderd van de [Azure IoT-oplossingsversnellers-site](https://www.azureiotsolutions.com/Accelerators) en zijn ze niet meer beschikbaar voor nieuwe implementaties. De GitHub-opslagplaatsen voor beide accelerators zijn gearchiveerd. De code is nog steeds beschikbaar voor iedereen, maar de opslagplaatsen leveren geen nieuwe bijdragen.

### <a name="what-happens-to-my-existing-remote-monitoring-and-predictive-maintenance-deployments"></a>Wat gebeurt er met mijn bestaande implementaties voor externe bewaking en voorspeld onderhoud?

Bestaande implementaties worden niet beïnvloed door het verwijderen van de oplossingsversnellers voor externe bewaking en voorspeld onderhoud en blijven werken. Ook gevorkte opslagplaatsen worden niet beïnvloed. De hoofdarchieven op GitHub zijn gearchiveerd.

### <a name="how-do-i-deploy-device-simulation-solution-accelerator"></a>Hoe kan ik oplossingsversneller voor apparaatsimulatie implementeren?

Zie de GitHub-opslagplaats voor [](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) apparaatsimulatie om de oplossingsversneller voor apparaatsimulatie te implementeren.

### <a name="where-can-i-find-information-about-the-removed-solution-accelerators"></a>Waar vind ik informatie over de verwijderde oplossingsversnellers?

Zie de volgende pagina's op de site met eerdere versies:

* [Externe bewaking](/previous-versions/azure/iot-accelerators/about-iot-accelerators)
* [Voorspellend onderhoud](/previous-versions/azure/iot-accelerators/about-iot-accelerators)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Welke SDK's kan ik gebruiken om apparaat-clients te ontwikkelen voor de oplossingsversnellers?

U vindt koppelingen naar de verschillende IoT-apparaat-SDK's (C, .NET, Java, Node.js, Python) in de GitHub-opslagplaatsen van Microsoft Azure [IoT-SDK's.](https://github.com/Azure/azure-iot-sdks)

Als u het DevKit-apparaat gebruikt, kunt u resources en voorbeelden vinden in de GitHub-opslagplaats van de [IoT DevKit SDK.](https://github.com/Microsoft/devkit-sdk)

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Ik ben een servicebeheerder en ik wil de maptoewijzing tussen mijn abonnement en een specifieke Azure AD-tenant wijzigen. Hoe kan ik deze taak voltooien?

Zie [Een bestaand abonnement toevoegen aan uw Azure AD-directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Ik wil een servicebeheerder wijzigen of Co-Administrator aangemeld met een organisatieaccount

Zie het ondersteuningsartikel [Servicebeheerder wijzigen en Co-Administrator wanneer u bent aangemeld met een organisatieaccount.](https://azure.microsoft.com/support/changing-service-admin-and-co-admin)

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Waarom zie ik deze fout? 'Uw account heeft niet de juiste machtigingen om een oplossing te maken. Neem contact op met uw accountbeheerder of probeer het met een ander account.

Bekijk het volgende diagram voor hulp:

![Stroomdiagram voor machtigingen](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Als u de fout blijft zien nadat u hebt valideren dat u een globale beheerder van de Azure AD-tenant en een medebeheerder van het abonnement bent, moet u de accountbeheerder de gebruiker laten verwijderen en de benodigde machtigingen in deze volgorde opnieuw toewijzen. Voeg eerst de gebruiker toe als globale beheerder en voeg vervolgens de gebruiker toe als medebeheerder van het Azure-abonnement. Als problemen zich blijven voordoen, kunt u contact [opnemen & Ondersteuning.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Waarom krijg ik deze foutmelding wanneer ik een Azure-abonnement heb? 'Er is een Azure-abonnement vereist om vooraf geconfigureerde oplossingen te maken. U kunt binnen een paar minuten een gratis proefaccount maken.

Als u zeker weet dat u een Azure-abonnement hebt, valideert u de tenanttoewijzing voor uw abonnement en controleert u of de juiste tenant is geselecteerd in de vervolgkeuzekeuze. Als u hebt gevalideerd dat de tenant juist is, volgt u het voorgaande diagram en valideert u de toewijzing van uw abonnement en deze Azure AD-tenant.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Wat is het verschil tussen het verwijderen van een resourcegroep in de Azure Portal en klikken op Verwijderen op een oplossingsversneller in azureiotsolutions.com?

* Als u de oplossingsversneller in [azureiotsolutions.com](https://www.azureiotsolutions.com/)verwijdert, verwijdert u alle resources die zijn geïmplementeerd toen u de oplossingsversneller maakte. Als u extra resources aan de resourcegroep hebt toegevoegd, worden deze resources ook verwijderd.
* Als u de resourcegroep in de Azure Portal [verwijdert,](https://portal.azure.com)verwijdert u alleen de resources in die resourcegroep. U moet ook de toepassing Azure Active Directory de oplossingsversneller verwijderen.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Kan ik mijn bestaande investeringen in mijn Azure IoT-oplossingsversnellers?

Ja. Elke oplossing die tegenwoordig bestaat, blijft werken in uw Azure-abonnement en de broncode blijft beschikbaar in GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hoeveel IoT Hub kan ik inrichten in een abonnement?

Standaard kunt u [10 IoT-hubs per abonnement inrichten.](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits) U kunt een ondersteuning voor Azure [maken om](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) deze limiet te verhogen. Als gevolg hiervan kunt u, omdat elke oplossingsversneller een nieuw IoT Hub, maximaal 10 oplossingsversnellers inrichten in een bepaald abonnement.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hoeveel Azure Cosmos DB kan ik inrichten in een abonnement?

Vijftig. U kunt een [ondersteuning voor Azure maken om](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) deze limiet te verhogen, maar standaard kunt u slechts 50 Cosmos DB per abonnement inrichten.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Kan ik een oplossingsversneller maken als ik Microsoft Azure voor DreamSpark?

> [!NOTE]
> Microsoft Azure voor DreamSpark staat nu bekend als Microsoft Imagine voor studenten.

Op dit moment kunt u geen oplossingsversneller maken met [een Microsoft Azure voor het Account van DreamSpark.](https://azure.microsoft.com/pricing/member-offers/imagine/) U kunt echter in een paar minuten een gratis proefaccount voor [Azure](https://azure.microsoft.com/free/) maken waarmee u een oplossingsversneller kunt maken.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Hoe kan ik Azure AD-tenant verwijderen?

Zie de blogpost van Eric Golpe [Walkthrough of Deleting an Azure AD Tenant](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant)(Overzicht van het verwijderen van een Azure AD-tenant).

### <a name="next-steps"></a>Volgende stappen

U kunt ook enkele van de andere functies en mogelijkheden van de IoT-oplossingsversnellers bekijken:

* [Oplossingsversneller voor verbonden factory's implementeren](quickstart-connected-factory-deploy.md)
* [Fundamentele IoT-beveiliging](../iot-fundamentals/iot-security-ground-up.md)