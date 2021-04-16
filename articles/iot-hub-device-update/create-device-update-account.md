---
title: Een apparaatupdateaccount maken in Apparaatupdate voor Azure IoT Hub | Microsoft Docs
description: Maak een apparaatupdateaccount in Apparaatupdate voor Azure IoT Hub.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: d3f7f4e1cdd56675d6084448abc810c9a41992f9
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520136"
---
# <a name="device-update-for-iot-hub-resource-management"></a>Resourcebeheer van Device Update for IoT Hub

Als u aan de slag wilt gaan met Apparaatupdate, moet u een Apparaatupdate-account maken, een exemplaar maken en toegangsbeheerrollen instellen. 

## <a name="prerequisites"></a>Vereisten

* Toegang tot een IoT Hub. U wordt aangeraden een S1-laag (Standard) of hoger te gebruiken. 
* Ondersteunde browsers:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>Een apparaatupdateaccount maken

1. Ga naar [Azure Portal](https://portal.azure.com)

2. Klik op Een resource maken en zoek naar 'Apparaatupdate voor IoT Hub'

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="Schermopname van Apparaatupdate voor IoT Hub resource." lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. Klik op Create -> Device Update for IoT Hub

4. Geef het Azure-abonnement op dat moet worden gekoppeld aan uw apparaatupdateaccount en resourcegroep

5. Geef een naam en locatie op voor uw apparaatupdateaccount

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="Schermopname van accountgegevens." lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > U kunt naar de [pagina Azure-producten per](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) regio gaan om de regio's te ontdekken waar Apparaatupdate voor IoT Hub beschikbaar is. Als Apparaatupdate voor IoT Hub niet beschikbaar is in uw regio, kunt u ervoor kiezen om een account te maken in een beschikbare regio die het dichtst bij u in de buurt is. 

6. Klik op Volgende: Controleren en maken>

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="Schermopname van de beoordeling van accountdetails." lightbox="media/create-device-update-account/account-review.png":::

7. Controleer de details en selecteer vervolgens Maken. U ziet dat uw implementatie wordt uitgevoerd. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="Schermopname van de accountimplementatie die wordt uitgevoerd." lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. U ziet dat de implementatiestatus binnen enkele minuten is gewijzigd in 'voltooid'. Klik op Ga naar resource

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="Schermopname van de implementatie van het account is voltooid." lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>Een apparaatupdate-exemplaar maken 

Een exemplaar van Apparaatupdate is gekoppeld aan één IoT-hub. Selecteer de IoT-hub die wordt gebruikt met Apparaatupdate. Tijdens deze stap maken we een nieuw beleid voor gedeelde toegang om ervoor te zorgen dat Apparaatupdate alleen de vereiste machtigingen gebruikt om te werken met IoT Hub (register schrijven en service verbinden). Dit beleid zorgt ervoor dat de toegang alleen beperkt is tot Apparaatupdate.

Een apparaatupdate-exemplaar maken nadat een account is gemaakt.

1. Wanneer u zich in uw zojuist gemaakte accountresource hebt gemaakt, gaat u naar de blade Exemplaren van Exemplaarbeheer

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="Schermopname van exemplaarbeheer binnen het account." lightbox="media/create-device-update-account/instance-blade.png":::

2. Klik op Maken, geef een exemplaarnaam op en selecteer uw IoT Hub

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="Schermopname van exemplaardetails." lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > De IoT Hub u aan uw Apparaatupdate-resource koppelt, hoeft zich niet in dezelfde regio te hebben als uw apparaatupdateaccount. Voor betere prestaties wordt echter aanbevolen dat uw IoT Hub zich in een regio die hetzelfde is als of dicht bij de regio van uw Apparaatupdate-account. 

3. Klik op Maken. U ziet dat het exemplaar de status Maken heeft. 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="Schermopname van het maken van een exemplaar." lightbox="media/create-device-update-account/instance-creating.png":::

4. Sta 5-10 minuten toe dat de implementatie van het exemplaar is voltooid. Vernieuw de status totdat u ziet dat de inrichtingsstatus 'Geslaagd' is.

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="Schermopname van het maken van het exemplaar is geslaagd." lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>Een IoT Hub 

Als u wilt dat Apparaatupdate wijzigingsmeldingen ontvangt van IoT Hub, kan Apparaatupdate worden geïntegreerd met de 'ingebouwde' Event Hub. Als u op de knop Configure IoT Hub klikt, configureert u de vereiste berichtroutes en het toegangsbeleid dat is vereist om te communiceren met IoT-apparaten. 

Een IoT Hub

1. Zodra de instantie 'Inrichtingsstaat' is verandert in 'Geslaagd', selecteert u het exemplaar op de blade Exemplaarbeheer. Klik op Configureren IoT Hub

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="Schermopname van het configureren IoT Hub voor een exemplaar." lightbox="media/create-device-update-account/instance-configure.png":::

2. Selecteer 'Ik ga akkoord om deze wijzigingen aan te brengen'

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="Schermopname van het akkoord gaan met het configureren IoT Hub voor een exemplaar." lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. Klik op Bijwerken

   > [!NOTE] 
   > Als u een gratis laag van Azure IoT Hub gebruikt, is het toegestane aantal berichtroutes beperkt tot 5. Apparaatupdate voor IoT Hub moet 4 berichtroutes configureren om te werken zoals verwacht. 

[Meer informatie over de berichtroutes die zijn geconfigureerd.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>Toegangsbeheerrollen configureren

Als u wilt dat andere gebruikers toegang hebben tot Apparaatupdate, moeten gebruikers toegang krijgen tot deze resource. 

1. Ga naar Toegangsbeheer (IAM) binnen het apparaatupdateaccount

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="Schermopname van toegangsbeheer binnen het Apparaatupdate-account." lightbox="media/create-device-update-account/account-access-control.png":::

2. Klik op Roltoewijzingen toevoegen

3. Selecteer onder Een rol selecteren een apparaatupdaterol uit de opgegeven opties
     - Apparaatupdatebeheerder
     - Lezer voor apparaatupdates
     - Inhoudsbeheerder voor apparaatupdates
     - Inhoudslezer voor apparaatupdates
     - Beheerder van apparaatupdate-implementaties
     - Lezer voor implementaties van apparaatupdates
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="Schermopname van roltoewijzingen voor toegangsbeheer binnen het apparaatupdateaccount." lightbox="media/create-device-update-account/role-assignment.png":::
    
    [Meer informatie over op rollen gebaseerd toegangsbeheer in Apparaatupdate voor IoT Hub](device-update-control-access.md) 
    
4. Toegang toewijzen aan een gebruiker of Azure AD-groep
5. Klik op Opslaan
6. U bent nu klaar om de apparaatupdate-ervaring te gebruiken vanuit uw IoT Hub

## <a name="next-steps"></a>Volgende stappen

Werk een apparaat bij met behulp van een van de volgende snelle zelfstudies:

 - [Apparaatupdate in een simulator](device-update-simulator.md)
 - [Apparaatupdate op Raspberry Pi](device-update-raspberry-pi.md)
 - [Apparaatupdate op Ubuntu Server 18.04 x64 Package Agent](device-update-ubuntu-agent.md)

[Meer informatie over het apparaatupdateaccount en -exemplaar.](device-update-resources.md) 

[Meer informatie over toegangsbeheerrollen voor apparaatupdates. ](device-update-control-access.md) 

