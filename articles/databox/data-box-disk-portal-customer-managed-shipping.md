---
title: Microsoft Azure Data Box Disk zelf beheerde verzen ding | Microsoft Docs in gegevens
description: Beschrijving van een zelf beheerde verzend werk stroom voor Azure Data Box Disk apparaten
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: f512b4415f4a83e779a8f9bf790ba2806e3b05c5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99526327"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>Zelf-beheerde verzen ding voor Azure Data Box Disk gebruiken in de Azure Portal

In dit artikel worden zelf beheerde verzend taken beschreven voor het best Ellen, ophalen en verwijderen van Azure Data Box Disk. U kunt Data Box Disk beheren met behulp van de Azure Portal.

## <a name="prerequisites"></a>Vereisten

Zelf-beheerde verzen ding is beschikbaar als optie wanneer u [Azure data Box Disk order](data-box-disk-deploy-ordered.md). Zelf-beheerde verzen ding is alleen beschikbaar in de volgende regio's:

* Amerikaanse overheid
* Verenigd Koninkrijk
* West-Europa
* Australië
* Japan
* Singapore
* Zuid-Korea
* Zuid-Afrika
* India (preview-versie)

## <a name="use-self-managed-shipping"></a>Zelfbeheerde verzending gebruiken

Wanneer u een Data Box Disk order plaatst, kunt u de optie voor zelf-beheerde verzen ding kiezen.

1. Selecteer in uw Azure Data Box Disk bestelling, onder de **contact gegevens**, **+ Verzend adres toevoegen**.

   ![Scherm opname van de wizard voor het weer geven van de contact gegevens stap met de optie verzend adres toevoegen.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Wanneer u verzend type kiest, selecteert u de optie **zelf beheerde verzen ding** . Deze optie is alleen beschikbaar als u zich in een ondersteunde regio bevindt, zoals beschreven in de vereisten.

3. Nadat u uw verzend adres hebt verstrekt, moet u het valideren en uw bestelling volt ooien.

   ![Schermopname van het dialoogvenster Verzendadres toevoegen met de opties Verzenden met en Verzendadres toevoegen uitgelicht.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Zodra het apparaat is voor bereid en u een e-mail melding hebt ontvangen, kunt u een ophaling plannen. Ga in uw Azure Data Box Disk order naar **overzicht** en selecteer vervolgens **ophalen plannen**.

   ![Een Data Box-apparaat best Ellen voor ophalen](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. Volg de instructies in de **planning ophalen voor Azure**. Voordat u uw autorisatie code kunt ophalen, moet u een e-mail sturen [adbops@microsoft.com](mailto:adbops@microsoft.com) om het ophalen van apparaten te plannen vanuit het Data Center van uw regio.

   ![Ophalen plannen](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. Nadat u het ophalen van uw apparaten hebt gepland, kunt u uw autorisatie code bekijken in **ophalen plannen voor Azure**.

   ![Scherm afbeelding van het dialoog venster voor het ophalen van Azure-inhoud, met het tekstvak autorisatie code voor ophalen.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   Noteer deze autorisatie code.

   Volgens de beveiligings vereisten moet u, op het moment van het plannen van de planning, de naam van de persoon presen teren die in gesprek komt om op te halen.

   U moet ook details opgeven van wie gaat naar het Data Center voor het ophalen van de gegevens. U of het contact punt moet een door de overheid goedgekeurde foto-ID bevatten die wordt gevalideerd op het Data Center.

   De persoon die het apparaat ophaalt, moet ook de autorisatie code hebben. De autorisatie code is uniek voor een verzameling of een afmelding en wordt gevalideerd in het Data Center.

7. Uw bestelling wordt automatisch verplaatst naar de status **opgehaald** wanneer het apparaat wordt opgehaald uit het Data Center.

   ![Opgehaald](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. Nadat het apparaat is opgehaald, kunt u gegevens naar de Data Box Disk (s) op uw site kopiëren. Wanneer het kopiëren van de gegevens is voltooid, kunt u voorbereiden om de Data Box Disk te verzenden.

   Nadat u klaar bent met het kopiëren van de gegevens, neemt u contact op met de bewerkingen om een afspraak te plannen voor de vervolg keuzelijst. U moet de gegevens van de persoon die binnenkomt op het Data Center delen om de schijven uit te kunnen zetten. Het Data Center moet ook de autorisatie code controleren op het moment van de vervolg keuzelijst. U vindt de autorisatie code voor de vervolg keuzelijst in de Azure Portal onder **Schedule drop**.

   > [!NOTE]
   > Deel de autorisatie code niet via e-mail. Dit wordt alleen gecontroleerd bij het Data Center tijdens de vervolg keuzelijst.

9. Nadat u een afspraak voor een vervolg keuzelijst hebt ontvangen, moet de volg orde in de Azure Portal **gereed om te worden ontvangen bij Azure Data Center worden weer** gegeven.

   ![Scherm afbeelding van het dialoog venster verzend adres toevoegen met het verzenden via opties en de optie verzend adres toevoegen.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. Nadat uw ID en autorisatie code zijn geverifieerd en u het apparaat in het Data Center hebt neergezet, moet de status van de bestelling worden **ontvangen**.

    ![Ontvangen voltooid](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. Zodra het apparaat is ontvangen, wordt het kopiëren van de gegevens voortgezet. Wanneer de Kopieer bewerking is voltooid, wordt de volg orde voltooid.

## <a name="next-steps"></a>Volgende stappen

* [Quickstart: Azure Data Box Disk implementeren met behulp van de Azure-portal](data-box-disk-quickstart-portal.md)
