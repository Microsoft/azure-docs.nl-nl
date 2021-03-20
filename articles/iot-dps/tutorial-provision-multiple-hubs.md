---
title: 'Zelfstudie: Apparaten inrichten op Azure IoT Hub Device Provisioning Service met gelijke taakverdeling'
description: Deze zelfstudie laat zien hoe de Device Provisioning Service (DPS) automatische apparaatinrichting mogelijk maakt voor IoT-hubs met gelijke taakverdeling in Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 6cca34774038156cfb01e872e751338c1c96efaa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91315997"
---
# <a name="tutorial-provision-devices-across-load-balanced-iot-hubs"></a>Zelfstudie: Apparaten inrichten in IoT-hubs met gelijke taakverdeling

In deze zelfstudie kunt u zien hoe u apparaten kunt inrichten voor meerdere IoT-hubs met gelijke taakverdeling met behulp van de Device Provisioning Service. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure Portal gebruiken om een tweede apparaat in te richten op een tweede IoT-hub 
> * Een vermelding in de registratielijst toevoegen aan het tweede apparaat
> * Het toewijzingsbeleid van de Device Provisioning Service instellen op **gelijkmatige verdeling**
> * De nieuwe IoT-hub koppelen aan de Device Provisioning Service

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Deze zelfstudie gaat verder op de vorige zelfstudie [Een apparaat inrichten op een hub](tutorial-provision-device-to-hub.md).

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Azure Portal gebruiken om een tweede apparaat in te richten op een tweede IoT-hub

Volg de stappen in de zelfstudie [Een apparaat inrichten op een hub](tutorial-provision-device-to-hub.md) om een tweede apparaat in te richten op een andere IoT-hub.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>Een vermelding in de registratielijst toevoegen aan het tweede apparaat

Via de registratielijst wordt voor de Device Provisioning Service aangegeven welke methode van attestation (de methode waarmee een apparaat-id wordt bevestigd) wordt gebruikt voor het apparaat. In de volgende stap voegt u een vermelding aan de registratielijst toe voor het tweede apparaat. 

1. Op de pagina van uw Device Provisioning Service, klikt u op **Registraties beheren**. De pagina **Vermelding aan registratielijst toevoegen** wordt weergegeven. 
2. Klik boven aan de pagina op **Toevoegen**.
2. Vul de velden in en klik op **Opslaan**.

## <a name="set-the-device-provisioning-service-allocation-policy"></a>Het toewijzingsbeleid instellen van de Device Provisioning Service

Het toewijzingsbeleid is een instelling voor de Device Provisioning Service die bepaalt hoe apparaten aan een IoT-hub worden toegewezen. Er worden drie soorten toewijzingsbeleid ondersteund: 

1. **Laagste latentie**: apparaten worden ingericht voor een IoT-hub op basis van de hub met de laagste latentie voor het apparaat.
2. **Gelijk gewogen distributie** (standaardinstelling): gekoppelde IoT-hubs hebben evenveel kans dat apparaten voor ze worden ingericht. Dit is de standaardinstelling. Als u apparaten voor slechts één IoT-hub inricht, kunt u deze instelling bewaren. 
3. **Statische configuratie via de lijst inschrijving**: specificatie van de gewenste IoT-hub in de registratielijst heeft voorrang boven het toewijzingsbeleid op het niveau van de Device Provisioning Service.

Volg deze stappen om het toewijzingsbeleid in te stellen:

1. Als u het toewijzingsbeleid wilt instellen, klikt u op de pagina Device Provisioning Service op **Toewijzingsbeleid beheren**.
2. Stel het DPS-toewijzingsbeleid in op **Gelijk gewogen distributie**.
3. Klik op **Opslaan**.

## <a name="link-the-new-iot-hub-to-the-device-provisioning-service"></a>De nieuwe IoT-hub koppelen aan de Device Provisioning Service

Koppel de Device Provisioning Service en IoT Hub zodat de Device Provisioning Service apparaten kan registreren bij die hub.

1. Klik op de pagina **Alle resources** op de Device Provisioning Service die u eerder hebt gemaakt.
2. Klik op de pagina Device Provisioning Service op **Gekoppelde IoT-hubs**.
3. Klik op **Add**.
4. Geef op de pagina **Koppeling toevoegen aan IoT-hub** met behulp van de keuzerondjes aan of de gekoppelde IoT-hub zich in het huidige abonnement of in een ander abonnement bevindt. Kies vervolgens de naam van de IoT-hub in het vak **IoT-hub**.
5. Klik op **Opslaan**.

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Azure Portal gebruiken om een tweede apparaat in te richten op een tweede IoT-hub 
> * Een vermelding in de registratielijst toevoegen aan het tweede apparaat
> * Het toewijzingsbeleid van de Device Provisioning Service instellen op **gelijkmatige verdeling**
> * De nieuwe IoT-hub koppelen aan de Device Provisioning Service

## <a name="next-steps"></a>Volgende stappen

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps]()
-->
