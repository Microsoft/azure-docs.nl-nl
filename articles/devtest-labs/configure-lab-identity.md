---
title: Een Lab-identiteit in Azure DevTest Labs configureren
description: Meer informatie over het configureren van een Lab-identiteit in azure DevTest.
ms.topic: article
ms.date: 08/20/2020
ms.openlocfilehash: a652eb5751f9b723911a1c1baaaaf9860febc5b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88719642"
---
# <a name="configure-a-lab-identity"></a>Een Lab-identiteit configureren

Een veelvoorkomende uitdaging bij het bouwen van cloud-apps is het beheren van de referenties in uw code voor verificatie bij cloudservices. Het is belangrijk dat de referenties veilig worden bewaard. In het ideale geval worden de referenties nooit weergegeven op werkstations van ontwikkelaars en niet ingecheckt in broncodebeheer. Azure Key Vault biedt een manier om referenties, geheimen en andere sleutels veilig op te slaan, maar uw code moet worden geverifieerd bij Key Vault om ze op te halen. 

Met de functie beheerde identiteiten voor Azure-resources in Azure Active Directory (Azure AD) kunt u dit probleem oplossen. De functie biedt Azure-services met een automatisch beheerde identiteit in Azure AD. U kunt de identiteit gebruiken voor verificatie bij alle services die ondersteuning bieden voor Azure AD-verificatie, inclusief Key Vault, zonder referenties in de code. Meer informatie over [beheerde identiteiten in azure](../active-directory/managed-identities-azure-resources/overview.md). 

Er zijn twee typen beheerde identiteit: 

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit  

Een door het **systeem toegewezen beheerde identiteit**   wordt rechtstreeks ingeschakeld voor een Azure-service-exemplaar. Wanneer de identiteit is ingeschakeld, wordt een identiteit voor het service-exemplaar in de Azure AD-tenant gemaakt, dat wordt vertrouwd door het abonnement van het service-exemplaar. Nadat de identiteit is gemaakt, worden de referenties op het exemplaar ingericht. De levenscyclus van een door het systeem toegewezen identiteit is rechtstreeks gekoppeld aan de Azure-service-exemplaar waarop de identiteit is ingeschakeld. Als het exemplaar wordt verwijderd, ruimt Azure automatisch de referenties en de identiteit in Azure AD op. 

### <a name="scenarios-for-using-labs-system-assigned-identity"></a>Scenario's voor het gebruik van de door het systeem toegewezen identiteit van het lab  

Elke DevTest Labs wordt gemaakt met een door het systeem toegewezen identiteit die geldig is voor de levens duur van het lab. De toegewezen identiteit van het systeem wordt gebruikt voor de volgende doel einden:  

- Alle implementaties op basis van [Azure Resource Manager](devtest-lab-create-environment-from-arm.md) die worden gebruikt om meerdere vm's en/of platforms als een service omgeving in te zetten, worden uitgevoerd met behulp van de door het systeem toegewezen identiteit van het lab  
- Schijf versleuteling voor Lab-schijven met een door de klant beheerde sleutel wordt ondersteund door de door het systeem toegewezen identiteit van het lab. Door expliciete toegang tot de identiteit van het lab te bieden om toegang te krijgen tot uw schijf versleutelings, kan het lab alle schijven van de virtuele machine namens u versleutelen. Meer informatie over [het inschakelen van schijf versleuteling](encrypt-disks-customer-managed-keys.md) voor uw Lab-schijven met behulp van een door de klant beheerde sleutel.  

### <a name="configure-identity"></a>Identiteit configureren

In deze sectie ziet u hoe u het identiteits beleid van Lab kunt configureren.

> [!NOTE]
> Voor Labs die is gemaakt vóór 8/10/2020, wordt de toegewezen identiteit van het systeem ingesteld op uit. Als eigenaar van het lab kunt u deze inschakelen voor het geval u Labs wilt gebruiken voor de doel einden die in de vorige sectie worden genoemd.  
>
> Voor nieuwe Labs die zijn gemaakt na 8/10/2020, wordt de toegewezen systeem-id van het lab standaard ingesteld op ingeschakeld en kan de eigenaar van het lab dit niet uitschakelen voor de levens cyclus van het lab.  

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar **DevTest Labs**.
1. Selecteer in de lijst met Labs het gewenste Lab.
1. Selecteer de identiteit van de **configuratie en het beleid**  ->  **(preview-versie)**. 

> [!div class="mx-imgBorder"]
> ![Identiteit configureren](./media/configure-lab-identity/configure-identity.png)

## <a name="user-assigned-managed-identity"></a>Door de gebruiker toegewezen beheerde identiteit  

Een door de gebruiker toegewezen beheerde identiteit wordt gemaakt als een zelfstandige Azure-resource. Via een productieproces maakt Azure een identiteit in de Azure AD-tenant, die wordt vertrouwd door het gebruikte abonnement. Nadat de identiteit is gemaakt, kan deze worden toegewezen aan een of meer Azure-service-exemplaren. De levenscyclus van een door de gebruiker toegewezen identiteit wordt afzonderlijk beheerd van de levenscyclus van de Azure Service-exemplaren waaraan de identiteit is toegewezen. 

DevTest Labs ondersteunt door de gebruiker toegewezen identiteiten voor zowel virtuele machines als op Azure Resource Manager gebaseerde omgevingen.  Zie de volgende onderwerpen voor meer informatie:

- [Een door de gebruiker toegewezen identiteit toevoegen om Lab Azure Resource Manager-omgevingen te implementeren](use-managed-identities-environments.md)
- [Een door de gebruiker toegewezen identiteiten toevoegen voor het implementeren van virtuele lab-machines](enable-managed-identities-lab-vms.md)

## <a name="next-steps"></a>Volgende stappen

[Kosten beheer](devtest-lab-configure-cost-management.md) controleren