---
title: Microsoft Azure Data Box zelf beheerde verzen ding | Microsoft Docs in gegevens
description: Beschrijving van een zelf beheerde verzend werk stroom voor Azure Data Box apparaten
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 07529b18191c71776a9a36edbfa4cfd8ded5af4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99524546"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>Zelf-beheerde verzen ding voor Azure Data Box gebruiken in de Azure Portal

In dit artikel worden zelf beheerde verzend taken beschreven voor het best Ellen, ophalen en verwijderen van een Azure Data Box apparaat. U kunt het Data Box-apparaat beheren met behulp van de Azure Portal.

## <a name="prerequisites"></a>Vereisten

Zelf-beheerde verzen ding is beschikbaar als optie wanneer u [Azure data Box order](data-box-deploy-ordered.md). Zelf-beheerde verzen ding is alleen beschikbaar in de volgende regio's:

* Amerikaanse overheid
* Verenigd Koninkrijk
* West-Europa
* Japan
* Singapore
* Zuid-Korea
* India
* Zuid-Afrika
* Australië

## <a name="use-self-managed-shipping"></a>Zelfbeheerde verzending gebruiken

Wanneer u een Data Box order plaatst, kunt u de optie zelf beheerde verzen ding kiezen.

1. Selecteer in uw Azure Data Box bestelling, onder de **contact gegevens**, **+ Verzend adres toevoegen**.
 
   ![Zelf-beheerde verzen ding, verzend adres toevoegen](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Selecteer de optie **zelf beheerde verzen ding** bij het kiezen van een verzend type. Deze optie is alleen beschikbaar als u zich in een ondersteunde regio bevindt, zoals beschreven in de vereisten.

3. Zodra u uw verzend adres hebt ingevoerd, moet u dit valideren en uw bestelling volt ooien.

   ![Zelf beheerde verzen ding, valideren en adres toevoegen](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Zodra het apparaat is voor bereid en u een e-mail melding voor de apparaten hebt ontvangen, kunt u een ophaling plannen.

   Ga in uw Azure Data Box order naar **overzicht** en selecteer vervolgens **ophalen plannen**.

   ![Data Box order, optie voor het ophalen van plannen](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. Volg de instructies in de **planning ophalen voor Azure**.

   Voordat u uw autorisatie code kunt ophalen, moet u een e-mail sturen [adbops@microsoft.com](mailto:adbops@microsoft.com) om het ophalen van apparaten te plannen vanuit het Data Center van uw regio.

   ![Instructies voor het ophalen van Azure plannen](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

6. Nadat u het ophalen van uw apparaten hebt gepland, kunt u de autorisatie code van uw apparaat bekijken in het deel venster **ophalen voor Azure plannen** .

   ![De autorisatie code van uw apparaat weer geven](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   Noteer deze **autorisatie code**. Volgens de beveiligings vereisten moet u, op het moment van het plannen van de planning, de naam van de persoon presen teren die zou arriveren voor ophalen.

   U moet ook details opgeven van wie gaat naar het Data Center voor ophalen. U of het contact punt moet een door de overheid goedgekeurde foto-ID hebben die wordt gevalideerd op het Data Center.

   Daarnaast moet de persoon die het apparaat ophaalt ook de **autorisatie code** hebben. De autorisatie code wordt gevalideerd op het tijdstip van ophalen van het Data Center.

7. Uw bestelling wordt automatisch verplaatst naar de status **opgehaald** wanneer het apparaat is opgehaald van het Data Center.

    ![Een order met de status opgehaald](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

8. Nadat het apparaat is opgehaald, kopieert u gegevens naar de Data Box op uw site. Wanneer het kopiëren van de gegevens is voltooid, kunt u voorbereiden om de Data Box te verzenden. Zie [voorbereiding voor verzending](data-box-deploy-picked-up.md#prepare-to-ship)voor meer informatie.

   De **voorbereiding voor verzending** stap moet worden voltooid zonder kritieke fouten. Anders moet u deze stap opnieuw uitvoeren nadat u de benodigde oplossingen hebt gemaakt. Nadat de **voorbereiding voor verzending** stap is voltooid, kunt u de autorisatie code voor de vervolg keuzelijst bekijken op de lokale gebruikers interface van het apparaat.

   > [!NOTE]
   > Deel de autorisatie code niet via e-mail. Dit wordt alleen gecontroleerd bij het Data Center tijdens het verwijderen.

9. Als u een afspraak hebt ontvangen voor vervolg keuzelijst, moet de volg orde van de status van **Azure Data Center** in de Azure Portal zijn voltooid. Volg de instructies in de **vervolg keuzelijst planning** om het apparaat te retour neren.

   ![Instructies voor het verwijderen van apparaten](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

10. Nadat uw ID en autorisatie code zijn geverifieerd en u het apparaat in het Data Center hebt verwijderd, moet u de status van de bestelling **ontvangen**.

    ![Een order met de status ontvangen](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

11. Zodra het apparaat is ontvangen, wordt het kopiëren van de gegevens voortgezet. Wanneer de Kopieer bewerking is voltooid, wordt de volg orde voltooid.

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met de Azure Data Box](data-box-quickstart-portal.md)
