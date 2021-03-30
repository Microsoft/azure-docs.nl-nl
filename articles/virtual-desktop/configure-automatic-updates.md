---
title: Micro soft endpoint Configuration Manager configureren-Azure
description: Micro soft endpoint Configuration Manager configureren voor het implementeren van software-updates voor Windows 10 Enter prise multi-session op een virtueel Windows-bureau blad.
author: Heidilohr
ms.topic: how-to
ms.date: 06/12/2020
ms.author: helohr
ms.reviewer: v-cawood; clemr
manager: lizross
ms.openlocfilehash: bfc797e0c11a1804d27c6c97fea8cb2ba62dca7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88010120"
---
# <a name="configure-microsoft-endpoint-configuration-manager"></a>Micro soft endpoint Configuration Manager configureren

In dit artikel wordt uitgelegd hoe u micro soft endpoint Configuration Manager configureert om automatisch updates toe te passen op een Windows-host met Windows 10 Enter prise multi-session.

## <a name="prerequisites"></a>Vereisten

Als u deze instelling wilt configureren, hebt u het volgende nodig:

   - Zorg ervoor dat u de micro soft endpoint Configuration Manager-agent hebt geïnstalleerd op uw virtuele machines.
   - Zorg ervoor dat uw versie van micro soft endpoint Configuration Manager ten minste op filiaal niveau 1906 is. Gebruik vertakkings niveau 1910 of hoger voor de beste resultaten.

## <a name="configure-the-software-update-point"></a>Het software-updatepunt configureren

Als u updates voor Windows 10 Enter prise multi-session wilt ontvangen, moet u Windows Server versie 1903 en hoger inschakelen als een product binnen micro soft endpoint Configuration Manager. Deze product instelling is ook van toepassing als u de Windows Server-Update service gebruikt om updates te implementeren op uw systemen.

Updates ontvangen:

1. Open micro soft endpoint Configuration Manager en selecteer **sites**.
2. Selecteer **site onderdelen configureren**.
3. Selecteer **Software-update punt** in de vervolg keuzelijst.
4. Selecteer het tabblad **Producten**.
5. Schakel het selectie vakje in met de tekst **Windows Server, versie 1903 en hoger**.
6. Ga naar **software bibliotheek**  >  **overzicht**  >  **software-updates**  >  **alle software-updates** en selecteer **software-updates synchroniseren**.
7. Controleer het bestand bestand Wsyncmgr. log in **programma bestanden**  >  **micro soft Configuration Manager**  >  **logs** om te controleren of uw wijzigingen zijn opgeslagen. Het kan enkele minuten duren om de updates te synchroniseren.

## <a name="create-a-query-based-collection"></a>Een op query's gebaseerde verzameling maken

Als u een verzameling virtuele machines met meerdere sessies van Windows 10 Enter prise wilt maken, kunt u een op query's gebaseerde verzameling gebruiken om de specifieke SKU van het besturings systeem te identificeren.

Een verzameling maken:

1. Selecteer **activa en naleving**.
2. Ga naar **overzicht**  >  **Apparaatsets** en klik met de rechter muisknop op **apparaat** Collections en selecteer **apparaat-verzameling maken** in de vervolg keuzelijst.
3. Voer op het tabblad **Algemeen** van het menu dat wordt geopend een naam in die uw verzameling beschrijft in het veld **naam** . In het veld **Opmerking** kunt u aanvullende informatie geven over de werking van de verzameling. In de **verzameling beperken** definieert u welke machines u wilt opnemen in de verzamelings query.
4. Voeg op het tabblad **lidmaatschaps regels** een regel toe voor uw query door **regel toevoegen** te selecteren en vervolgens **query regel** te selecteren.
5. Voer in de **query regel eigenschappen** een naam in voor de regel en definieer vervolgens de para meters van de regel door **query-instructie bewerken** te selecteren.
6. Selecteer **query-instructie weer geven**.
7. Voer in de instructie de volgende teken reeks in:

    ```syntax
    select
    SMS_R_SYSTEM.ResourceID,SMS_R_SYSTEM.ResourceType,SMS_R_SYSTEM.Name,SMS_R_SYSTEM.SMSUniqueIdentifier,SMS_R_SYSTEM.ResourceDomainORWorkgroup,SMS_R_SYSTEM.Client
    from SMS_R_System inner join SMS_G_System_OPERATING_SYSTEM on
    SMS_G_System_OPERATING_SYSTEM.ResourceId = SMS_R_System.ResourceId where
    SMS_G_System_OPERATING_SYSTEM.OperatingSystemSKU = 175
    ```

8. Selecteer **OK** om de verzameling te maken.
9. Als u wilt controleren of u de verzameling hebt gemaakt, gaat u naar **activa en naleving**  >  **overzicht**  >  **apparaat verzamelingen**.
