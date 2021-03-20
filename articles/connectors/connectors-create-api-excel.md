---
title: Gegevens, werk bladen en tabellen in Excel online beheren
description: Gegevens beheren in werk bladen en tabellen in Excel online voor bedrijven of Excel online voor OneDrive met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 097db6683127b410e713be53e6de838cf7734ddc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91400720"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Excel online-gegevens beheren met Azure Logic Apps

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en de [Excel online voor bedrijven](/connectors/excelonlinebusiness/) -connector of [Excel online voor OneDrive](/connectors/excelonline/) -connector kunt u geautomatiseerde taken en werk stromen maken op basis van uw gegevens in Excel online voor bedrijven of OneDrive. Deze connector bevat acties die u helpen bij het werken met uw gegevens en voor het beheren van spread sheets, bijvoorbeeld:

* Nieuwe werk bladen en tabellen maken.
* Werk bladen, tabellen en rijen ophalen en beheren.
* Voeg enkele rijen en sleutel kolommen toe.

Vervolgens kunt u de uitvoer van deze acties gebruiken met acties voor andere services. Als u bijvoorbeeld een actie gebruikt waarmee werk bladen per week worden gemaakt, kunt u een andere actie gebruiken waarmee een e-mail met een bevestiging wordt verzonden met behulp van de Office 365 Outlook-Connector.

Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> De [Excel online voor bedrijven](/connectors/excelonlinebusiness/) en [Excel online voor OneDrive](/connectors/excelonline/) -connectors werken met Azure Logic apps en verschillen van de [Excel-connector voor PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [werk-of school account](https://www.office.com/) voor uw werk account of persoonlijke Microsoft-account

  Uw Excel-gegevens kunnen bestaan als een CSV-bestand (Comma-Separated Value) in een opslagmap, bijvoorbeeld in OneDrive. 
  U kunt ook hetzelfde CSV-bestand gebruiken met de [Connector voor platte bestanden](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw Excel online-gegevens. Deze connector biedt alleen acties, dus als u uw logische app wilt starten, selecteert u een afzonderlijke trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

## <a name="add-excel-action"></a>Excel-actie toevoegen

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Kies **nieuwe stap** onder de trigger.

1. Voer in het zoekvak ' Excel ' in als uw filter. Selecteer in de lijst acties de gewenste actie.

   > [!NOTE]
   > Met de ontwerp functie voor logische apps kunnen geen tabellen worden geladen die 100 of meer kolommen hebben. Als dat mogelijk is, vermindert u het aantal kolommen in de geselecteerde tabel zodat de ontwerper de tabel kan laden.

1. Meld u aan bij uw werk-of school account als u hierom wordt gevraagd.

   Met uw referenties wordt uw logische app geautoriseerd om een verbinding met Excel online te maken en toegang te krijgen tot uw gegevens.

1. Blijf de benodigde Details voor de geselecteerde actie opgeven en de werk stroom van uw logische app bouwen.

## <a name="connector-reference"></a>Connector-verwijzing

Voor technische details, zoals triggers, acties en limieten, zoals wordt beschreven door de OpenAPI (voorheen Swagger)-bestanden van de connector, raadpleegt u deze referentie pagina's voor Connectors:

* [Excel online voor bedrijven](/connectors/excelonlinebusiness/)
* [Excel online voor OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
