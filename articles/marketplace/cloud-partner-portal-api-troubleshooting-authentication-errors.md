---
title: Algemene verificatie fouten oplossen, Azure Marketplace
description: Biedt hulp bij veelvoorkomende verificatie fouten bij het gebruik van de Cloud Partner-portal Api's in azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 01af5357e4ae2f4dfb317a0931a8d0bc2b2d54e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89487314"
---
# <a name="troubleshooting-common-authentication-errors"></a>Veelvoorkomende verificatie fouten oplossen

> [!NOTE]
> De Cloud Partner-portal-Api's zijn geïntegreerd in en blijven werken in het partner centrum. De overgang introduceert kleine wijzigingen. Controleer de wijzigingen die worden vermeld in [Cloud Partner-Portal API-referentie](./cloud-partner-portal-api-overview.md) om ervoor te zorgen dat uw code blijft werken na het overstappen naar het partner centrum. CCP-Api's mogen alleen worden gebruikt voor bestaande producten die al zijn geïntegreerd vóór de overgang naar het partner centrum. nieuwe producten moeten de indienings-Api's van partner Center gebruiken.

Dit artikel biedt hulp bij veelvoorkomende verificatie fouten bij het gebruik van de Cloud Partner-portal-Api's.

## <a name="unauthorized-error"></a>Niet-geautoriseerde fout

Als u consequent `401 unauthorized` fouten krijgt, controleert u of u een geldig toegangs token hebt.  Als u dit nog niet hebt gedaan, maakt u een Basic Azure Active Directory-toepassing (Azure AD) en een Service-Principal zoals beschreven in [Portal gebruiken om een Azure Active Directory toepassing en Service-Principal te maken die toegang hebben tot resources](../active-directory/develop/howto-create-service-principal-portal.md). Gebruik vervolgens de toepassing of een eenvoudige HTTP POST-aanvraag om uw toegang te controleren.  U neemt de Tenant-ID, toepassings-ID, object-ID en de geheime sleutel op om het toegangs token op te halen.

## <a name="forbidden-error"></a>Fout: Niet-toegestaan

Als er een `403 forbidden` fout optreedt, moet u ervoor zorgen dat de juiste Service-Principal is toegevoegd aan uw uitgevers account in de Cloud Partner-Portal. Volg de stappen op de pagina [vereisten](./cloud-partner-portal-api-prerequisites.md) om uw Service-Principal toe te voegen aan de portal.

Als de juiste Service-Principal is toegevoegd, controleert u alle andere gegevens. Let op de object-ID die is ingevoerd op de portal. De Azure Active Directory app-registratie pagina bevat twee object-Id's en u moet de lokale object-ID gebruiken. U kunt de juiste waarde vinden door naar de pagina **app-registraties** voor uw app te gaan en te klikken op de naam van de app onder **beheerde toepassing in de lokale map**. Hiermee gaat u naar de lokale eigenschappen voor de app, waar u de juiste object-ID kunt vinden op de pagina **Eigenschappen** , zoals wordt weer gegeven in de volgende afbeelding. Zorg er ook voor dat u de juiste uitgever-ID gebruikt om de Service-Principal toe te voegen en de API-aanroep te maken.
