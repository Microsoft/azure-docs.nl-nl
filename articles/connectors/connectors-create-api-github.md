---
title: Uw GitHub-opslag plaats openen, bewaken en beheren
description: GitHub-gebeurtenissen bewaken en uw GitHub-opslag plaats beheren door automatische werk stromen te maken met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999537"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>Bewaak en beheer uw GitHub-opslag plaats met behulp van Azure Logic Apps

GitHub is een op het web gebaseerde Git-hosting service voor opslag plaatsen die alle gedistribueerde revisie beheer en SCM-functionaliteit (broncode beheer) biedt in Git en andere functies.

[Maak eerst een logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om aan de slag te gaan met de GitHub-connector.

## <a name="create-a-connection-to-github"></a>Een verbinding met GitHub maken

Als u de GitHub-connector in een logische app wilt gebruiken, moet u eerst een *verbinding* maken en vervolgens Details voor deze eigenschappen opgeven: 

| Eigenschap | Vereist | Beschrijving | 
| -------- | -------- | ----------- | 
| Token | Ja | Geef uw GitHub-referenties op. |

Nadat u de verbinding hebt gemaakt, kunt u de acties uitvoeren en Luis teren naar de triggers die in dit artikel worden beschreven.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina van de connector](/connectors/github/)voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)