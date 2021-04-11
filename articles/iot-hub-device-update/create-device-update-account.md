---
title: Een update account voor een apparaat maken in de update voor Azure IoT Hub | Microsoft Docs
description: Maak een update voor het bijwerken van het apparaat in de update voor Azure IoT Hub.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 5956b7b74d27a4f9a2b79ee3950c8ac765610c70
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558479"
---
# <a name="device-update-for-iot-hub-resource-management"></a>Resourcebeheer van Device Update for IoT Hub

Om aan de slag te gaan met de update van het apparaat, moet u een update account voor het apparaat, instance en toegangs beheer rollen maken. 

## <a name="prerequisites"></a>Vereisten

* Toegang tot een IoT Hub. U wordt aangeraden een S1-laag (Standard) of hoger te gebruiken. 
* Ondersteunde browsers:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>Een update account voor een apparaat maken

1. Ga naar [Azure Portal](https://portal.azure.com)

2. Klik op een resource maken en zoek naar ' apparaat bijwerken voor IoT Hub '

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="Scherm afbeelding van het bijwerken van het apparaat voor IoT Hub bron." lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. Klik op > update voor het apparaat voor IoT Hub

4. Geef het Azure-abonnement op dat moet worden gekoppeld aan uw update account voor het apparaat en de resource groep

5. Geef een naam en locatie op voor het update account van uw apparaat

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="Scherm opname van account gegevens." lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > U kunt naar de [pagina met Azure-producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) gaan om de regio's te detecteren waar de update van het apparaat voor IOT hub beschikbaar is. Als het bijwerken van het apparaat voor IoT Hub niet beschikbaar is in uw regio, kunt u ervoor kiezen om een account te maken in een beschik bare regio die het dichtst bij u ligt. 

6. Klik op volgende: controleren + maken>

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="Scherm opname van account gegevens controleren." lightbox="media/create-device-update-account/account-review.png":::

7. Bekijk de details en selecteer vervolgens maken. U ziet dat uw implementatie wordt uitgevoerd. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="Scherm opname van account implementatie wordt uitgevoerd." lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. In enkele minuten ziet u dat de implementatie status is gewijzigd in voltooid. Klik op Ga naar resource

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="Scherm opname van de account implementatie is voltooid." lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>Een update-exemplaar voor een apparaat maken 

Een exemplaar van het bijwerken van het apparaat is gekoppeld aan één IoT-hub. Selecteer de IoT-hub die wordt gebruikt bij het bijwerken van het apparaat. Tijdens deze stap maken we een nieuw beleid voor gedeelde toegang om ervoor te zorgen dat de update van het apparaat alleen de vereiste machtigingen voor het werken met IoT Hub (register schrijf-en service-verbinding) gebruikt. Dit beleid zorgt ervoor dat toegang alleen is beperkt tot het bijwerken van apparaten.

Een update-exemplaar van het apparaat maken nadat een account is gemaakt.

1. Als u zich in de zojuist gemaakte account resource bevindt, gaat u naar de Blade exemplaren van instantie beheer

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="Scherm opname van instantie beheer binnen het account." lightbox="media/create-device-update-account/instance-blade.png":::

2. Klik op maken en geef een exemplaar naam op en selecteer uw IoT Hub

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="Scherm afbeelding van exemplaar gegevens." lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > Het IoT Hub dat u koppelt aan de resource voor het bijwerken van het apparaat, hoeft niet in dezelfde regio te zijn als het account voor het bijwerken van uw apparaat. Voor betere prestaties is het echter raadzaam dat uw IoT Hub in een regio hetzelfde zijn als of dicht bij de regio van uw update account voor uw apparaat. 

3. Klik op Maken. U ziet het exemplaar in de status ' maken '. 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="Scherm afbeelding van het maken van een exemplaar." lightbox="media/create-device-update-account/instance-creating.png":::

4. Het volt ooien van de implementatie van het exemplaar 5-10 minuten toestaan. Als u de status vernieuwt, wordt de ' inrichtings status ' naar geslaagd weer geven.

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="Scherm opname van het maken van het exemplaar is voltooid." lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>IoT Hub configureren 

Als u wilt dat de apparaat-update meldingen over wijzigingen ontvangt van IoT Hub, integreert de update van het apparaat met de ' ingebouwde ' event hub. Als u op de knop IoT Hub configureren klikt, worden de vereiste bericht routes en het toegangs beleid dat is vereist voor de communicatie met IoT-apparaten geconfigureerd. 

IoT Hub configureren

1. Zodra het exemplaar ' inrichtings status ' is ingeschakeld, selecteert u het exemplaar in de Blade exemplaar beheer. Klik op IoT Hub configureren

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="Scherm opname van het configureren van IoT Hub voor een exemplaar." lightbox="media/create-device-update-account/instance-configure.png":::

2. Selecteer Ik ga akkoord om deze wijzigingen aan te brengen

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="Scherm opname van het accepteren van IoT Hub voor een exemplaar." lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. Klik op bijwerken

[Meer informatie over de routerings berichten die zijn geconfigureerd.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>Toegangs beheer rollen configureren

Gebruikers moeten toegang hebben tot deze bron om andere gebruikers toegang te geven tot de update van het apparaat. 

1. Ga naar toegangs beheer (IAM) binnen de update account van het apparaat

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="Scherm opname van toegangs beheer binnen het update account voor het apparaat." lightbox="media/create-device-update-account/account-access-control.png":::

2. Klik op Roltoewijzingen toevoegen.

3. Selecteer onder een rol selecteren een update-rol voor het apparaat uit de opgegeven opties
     - Beheerder apparaat bijwerken
     - Apparaat bijwerken lezer
     - Beheerder van de inhoud van het apparaat bijwerken
     - Lezer inhoud apparaat bijwerken
     - Beheerder voor update-implementaties van apparaten
     - Lezer voor update-implementaties van apparaat
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="Scherm opname van roltoewijzingen voor toegangs beheer binnen het update account van het apparaat." lightbox="media/create-device-update-account/role-assignment.png":::
    
    [Meer informatie over op rollen gebaseerd toegangs beheer in de update van het apparaat voor IoT Hub](device-update-control-access.md) 
    
4. Toegang toewijzen aan een gebruiker of een Azure AD-groep
5. Klik op Opslaan
6. U bent nu klaar om de update-ervaring van het apparaat te gebruiken vanuit uw IoT Hub

## <a name="next-steps"></a>Volgende stappen

Probeer een apparaat bij te werken met een van de volgende snelle zelf studies:

 - [Apparaat bijwerken op een simulator](device-update-simulator.md)
 - [Apparaat bijwerken op Raspberry pi](device-update-raspberry-pi.md)
 - [Apparaat bijwerken op Ubuntu Server 18,04 x64-pakket agent](device-update-ubuntu-agent.md)

[Meer informatie over het update account en-exemplaar van het apparaat.](device-update-resources.md) 

[Meer informatie over toegangs beheer rollen voor het bijwerken van apparaten. ](device-update-control-access.md) 

