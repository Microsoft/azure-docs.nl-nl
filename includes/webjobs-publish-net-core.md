---
title: bestand opnemen
description: Include-bestand
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: e208b52c67f173bd0d289715b63562df656b1ec9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88009750"
---
1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**.

1. Selecteer in het dialoog venster **publiceren** de optie **Azure** voor **doel** en selecteer vervolgens **volgende**. 

1. Selecteer **Azure WebJobs** voor een **specifiek doel** en selecteer vervolgens **volgende**.

1. Selecteer **een nieuwe Azure-Webtaak maken**.

   ![Publicatie doel selecteren](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Gebruik in het dialoog venster **app service (Windows)** de hosting-instellingen in de volgende tabel.

    | Instelling      | Voorgestelde waarde  | Beschrijving                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Naam** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app uniek wordt aangeduid. |
    | **Abonnement** | Kies uw abonnement | Het te gebruiken Azure-abonnement. |
    | **[Resourcegroep](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Naam van de resourcegroep waarin uw functie-app moet worden gemaakt. Kies **Nieuw** om een nieuwe resourcegroep te maken.|
    | **[Hostingabonnement](../articles/app-service/overview-hosting-plans.md)** | App Service-plan | Een [App Service-plan](../articles/app-service/overview-hosting-plans.md) geeft de locatie, de grootte en de functies van de webserverfarm aan die als host fungeert voor uw app. U kunt geld besparen bij het hosten van meerdere apps door de web-apps te configureren voor het delen van één App Service-plan. Met App Service plannen worden de regio, de grootte van het exemplaar, het aantal schalen en de SKU (gratis, gedeeld, basis, standaard of Premium) gedefinieerd. Kies **Nieuw** om een nieuw app service plan te maken. |

    ![Het dialoogvenster App Service maken](./media/webjobs-publish-netcore/app-service-dialog.png)

1. Selecteer **maken** om een Webtaak en gerelateerde resources in azure te maken met deze instellingen en om uw project code te implementeren.