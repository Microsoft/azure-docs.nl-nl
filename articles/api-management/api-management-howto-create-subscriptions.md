---
title: Abonnementen maken in azure API Management | Microsoft Docs
description: Meer informatie over het maken van abonnementen in azure API Management. Er is een abonnement nodig om abonnements sleutels op te halen die toegang tot Api's toestaan.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 191323a4c150c00c93245be35c9c8af381e26b42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87904852"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Abonnementen maken in Azure API Management

Wanneer u Api's publiceert via Azure API Management, is het eenvoudig en gebruikelijk om de toegang tot die Api's te beveiligen met behulp van abonnements sleutels. Client toepassingen die de gepubliceerde Api's moeten gebruiken, moeten een geldige abonnements sleutel bevatten in HTTP-aanvragen wanneer ze aanroepen naar die Api's. Als u een abonnements sleutel voor toegang tot Api's wilt ophalen, is een abonnement vereist. Zie [abonnementen in Azure API Management](api-management-subscriptions.md)voor meer informatie over abonnementen.

In dit artikel worden de stappen beschreven voor het maken van abonnementen in de Azure Portal.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt uitvoeren, zijn de volgende vereisten:

+ [Maak een API Management-exemplaar](get-started-create-service-instance.md).
+ Meer informatie [over abonnementen in API Management](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Een nieuw abonnement maken

1. Selecteer **abonnementen** in het menu aan de linkerkant.
2. Selecteer **abonnement toevoegen**.
3. Geef een naam op voor het abonnement en selecteer het bereik.
4. Kies eventueel als het abonnement moet worden gekoppeld aan een gebruiker.
5. Selecteer **Opslaan**.

![Flexibele abonnementen](./media/api-management-subscriptions/flexible-subscription.png)

Nadat u het abonnement hebt gemaakt, worden er twee API-sleutels gegeven voor toegang tot de Api's. Een sleutel is primair en een is secundair. 

## <a name="next-steps"></a>Volgende stappen
Meer informatie over API Management:

+ Meer informatie over andere [concepten](api-management-terminology.md) in API management.
+ Volg onze [zelf studies](import-and-publish.md) voor meer informatie over API management.
+ Raadpleeg onze [pagina met veelgestelde](api-management-faq.md) vragen voor veelgestelde vragen.