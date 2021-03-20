---
title: Ontwikkelaars accounts beheren met groepen in azure API Management
titleSuffix: Azure API Management
description: Meer informatie over het beheren van ontwikkelaars accounts met behulp van groepen in azure API Management. Maak groepen en koppel deze vervolgens aan producten of ontwikkel aars.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: ea674981036b4be292329a4b30b43180ed26d642
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92092780"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Groepen maken en gebruiken voor beheer van ontwikkelaarsaccounts in Azure API Management

In API Management worden groepen gebruikt voor het beheren van de zichtbaarheid van producten voor ontwikkelaars. Producten worden eerst zichtbaar gemaakt voor groepen en vervolgens kunnen ontwikkel aars in die groepen de producten die aan de groepen zijn gekoppeld, bekijken en er een abonnement op nemen. 

API Management heeft de volgende onveranderbare systeemgroepen:

* **Beheerders** - Azure-abonnementbeheerders zijn lid van deze groep. Beheerders beheren service-exemplaren van API Management, door het maken van de API's, bewerkingen en producten die door ontwikkelaars worden gebruikt.
* **Ontwikkelaars** - Geverifieerde gebruikers van de ontwikkelaarsportal vallen in deze groep. Ontwikkelaars zijn de klanten die toepassingen bouwen met uw API's. Ontwikkelaars krijgen toegang tot de ontwikkelaarsportal en bouwen toepassingen waarmee de bewerkingen van een API worden aangeroepen.
* **Gasten** - Niet-geverifieerde gebruikers van de ontwikkelaarsportal, zoals potentiële klanten die de ontwikkelaarsportal van een API Management-exemplaar bezoeken, vallen in deze groep. Ze kunnen bepaalde alleen-lezentoegang krijgen, zoals de mogelijkheid om API's te bekijken maar niet om ze aan te roepen.

Naast deze systeemgroepen kunnen beheerders aangepaste groepen maken of [gebruikmaken van externe groepen in gekoppelde Azure Active Directory-tenants][leverage external groups in associated Azure Active Directory tenants]. Aangepaste en externe groepen kunnen naast systeemgroepen worden gebruikt om ontwikkelaars zichtbaarheid van en toegang tot API-producten te geven. U kunt bijvoorbeeld één aangepaste groep maken voor ontwikkelaars die zijn gekoppeld aan een specifieke partnerorganisatie en hen toegang geven tot de API's vanuit een product dat alleen relevante API's bevat. Een gebruiker kan lid zijn van meerdere groepen.

In deze hand leiding wordt uitgelegd hoe beheerders van een API Management-exemplaar nieuwe groepen kunnen toevoegen en ze aan producten en ontwikkel aars kan koppelen.

Naast het maken en beheren van groepen in de Publisher-Portal kunt u uw groepen maken en beheren met behulp van de entiteit API Management REST API [groep](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) .

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Vereisten

Voltooi de taken in dit artikel: [een Azure API Management-exemplaar maken](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-group"></a><a name="create-group"> </a>Een groep maken

In deze sectie wordt uitgelegd hoe u een nieuwe groep toevoegt aan uw API Management-account.

1. Selecteer het tabblad **groepen** links in het scherm.
2. Klik op **+ toevoegen**.
3. Voer een unieke naam in voor de groep en een optionele beschrijving.
4. Kies **Maken**.

    ![Een nieuwe groep maken](./media/api-management-howto-create-groups/groups001.png)

Zodra de groep is gemaakt, wordt deze toegevoegd aan de lijst met **groepen** . <br/>Als u de **naam** of **Beschrijving** van de groep wilt bewerken, klikt u op de naam van de groep en de **instellingen**.<br/>Als u de groep wilt verwijderen, klikt u op de naam van de groep en drukt u op **verwijderen**.

Nu de groep is gemaakt, kan deze worden gekoppeld aan producten en ontwikkel aars.

## <a name="associate-a-group-with-a-product"></a><a name="associate-group-product"> </a>Een groep koppelen aan een product

1. Selecteer het tabblad **producten** aan de linkerkant.
2. Klik op de naam van het gewenste product.
3. Druk op **toegangs beheer**.
4. Klik op **+ groep toevoegen**.

    ![Scherm opname van de knop groep toevoegen.](./media/api-management-howto-create-groups/groups002.png)
5. Selecteer de groep die u wilt toevoegen.

    ![Scherm opname van de geselecteerde groep en markeert de knop selecteren.](./media/api-management-howto-create-groups/groups003.png)

    Als u een groep uit het product wilt verwijderen, klikt u op **verwijderen**.

    ![Een groep verwijderen](./media/api-management-howto-create-groups/groups004.png)

Zodra een product is gekoppeld aan een groep, kunnen ontwikkel aars in die groep het product weer geven en zich hierop abonneren.

> [!NOTE]
> Zie [ontwikkelaars accounts autoriseren met behulp van Azure Active Directory in Azure API Management](api-management-howto-aad.md)om Azure Active Directory groepen toe te voegen.

## <a name="associate-groups-with-developers"></a><a name="associate-group-developer"> </a>Groepen koppelen aan ontwikkel aars

In deze sectie wordt beschreven hoe u groepen koppelt aan leden.

1. Selecteer het tabblad **groepen** links in het scherm.
2. Selecteer **Leden**.

    ![Lid toevoegen](./media/api-management-howto-create-groups/groups005.png)
3. Druk op **+ toevoegen** en selecteer een lid.

    ![Scherm afbeelding met de knop toevoegen, de geselecteerde gebruiker en de knop selecteren.](./media/api-management-howto-create-groups/groups006.png)
4. Druk op **selecteren**.

Zodra de koppeling tussen de ontwikkelaar en de groep is toegevoegd, kunt u deze weer geven op het tabblad **gebruikers** .

## <a name="next-steps"></a><a name="next-steps"> </a>Volgende stappen

* Zodra een ontwikkelaar aan een groep is toegevoegd, kunnen ze de producten die aan die groep zijn gekoppeld, bekijken en zich hierop abonneren. Zie [hoe een product maken en publiceren in Azure API Management][How create and publish a product in Azure API Management]voor meer informatie.
* Naast het maken en beheren van groepen in de Publisher-Portal kunt u uw groepen maken en beheren met behulp van de entiteit API Management REST API [groep](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) .

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
