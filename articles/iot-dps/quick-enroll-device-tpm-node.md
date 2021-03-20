---
title: Snelstart - TPM-apparaten inschrijven bij Azure Device Provisioning Service met behulp van Node.js
description: 'Quickstart: TPM-apparaat inschrijven bij Azure IoT Hub Device Provisioning Service (DPS) met behulp van de Node.js Service-SDK. In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 184fb4bbf8845b749459e1963bed3c6d9fa64856
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323842"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Quickstart: TPM-apparaat inschrijven bij IoT Hub Device Provisioning Service met behulp van de Node.js service-SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

In deze stappen ziet u hoe u programmatisch een afzonderlijke inschrijving voor een TPM-apparaat in Azure IoT Hub Device Provisioning Service kunt maken met behulp van de Node.js Service-SDK en een Node.js-voorbeeldtoepassing. U kunt desgewenst ook een gesimuleerd TPM-apparaat bij de inrichtingsservice inschrijven met behulp van deze vermelding voor een afzonderlijke inschrijving.

## <a name="prerequisites"></a>Vereisten

- U hebt [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) voltooid.
- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v4.0+](https://nodejs.org). In deze quickstart wordt de [Node.js Service-SDK](https://github.com/Azure/azure-iot-sdk-node) hieronder geïnstalleerd.
- Goedkeuringssleutel (optioneel). Volg de stappen in [Een gesimuleerd apparaat maken en inrichten](quick-create-simulated-device.md) totdat u de sleutel hebt opgehaald. Maak dus geen afzonderlijke inschrijving via de Azure-portal.

## <a name="create-the-individual-enrollment-sample"></a>Het voorbeeld van de afzonderlijke inschrijving maken 

 
1. Voer vanuit een opdrachtvenster in de werkmap het volgende uit:
  
    ```cmd\sh
    npm install azure-iot-provisioning-service
    ```  

2. Maak met behulp van een teksteditor een bestand **create_individual_enrollment.js** in de werkmap. Voeg de volgende code toe aan het bestand en sla het op:

    ```
    'use strict';

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);
    var endorsementKey = process.argv[3];

    var enrollment = {
      registrationId: 'first',
      attestation: {
        type: 'tpm',
        tpm: {
          endorsementKey: endorsementKey
        }
      }
    };

    serviceClient.createOrUpdateIndividualEnrollment(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the individual enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
      }
    });
    ```

## <a name="run-the-individual-enrollment-sample"></a>Het voorbeeld van de afzonderlijke inschrijving uitvoeren
  
1. Als u het voorbeeld wilt uitvoeren, hebt u de verbindingsreeks voor de inrichtingsservice nodig. 
    1. Meld u aan bij Azure Portal, selecteer in het linkermenu de knop **Alle resources** en open Device Provisioning Service. 
    2. Selecteer **Gedeeld toegangsbeleid** en selecteer vervolgens het toegangsbeleid dat u wilt gebruiken om de eigenschappen te openen. Kopieer of noteer de verbindingsreeks van de primaire sleutel uit het venster **Toegangsbeleid**. 

       ![Verbindingsreeks voor de inrichtingsservice ophalen uit de portal](./media/quick-enroll-device-tpm-node/get-service-connection-string.png) 


2. U hebt de goedkeuringssleutel ook nodig voor uw apparaat. Als u de stappen in de quickstart [Create and provision a simulated device](quick-create-simulated-device.md) (Een gesimuleerd apparaat maken en inrichten) voor het maken van een gesimuleerd TPM-apparaat hebt gevolgd, gebruikt u de sleutel voor dit apparaat. U kunt anders de volgende goedkeuringssleutel gebruiken die is opgegeven met de [Node.js Services-SDK](https://github.com/Azure/azure-iot-sdk-node), om een voorbeeld te maken van een afzonderlijke inschrijving:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUScTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3dyKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKRdln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFeWlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==
    ```

3. Voer de volgende opdracht uit om een afzonderlijke inschrijving te maken voor uw TPM-apparaat (inclusief de aanhalingstekens rond het opdrachtargumenten):
 
     ```cmd\sh
     node create_individual_enrollment.js "<the connection string for your provisioning service>" "<endorsement key>"
     ```
 
3. Als de inschrijving is gemaakt, worden in het opdrachtvenster de eigenschappen weergegeven van de nieuwe afzonderlijke inschrijving.

    ![Eigenschappen van de inschrijving in de opdrachtuitvoer](./media/quick-enroll-device-tpm-node/output.png) 

4. Controleer of er een afzonderlijke inschrijving is gemaakt. Selecteer in Azure Portal **Inschrijvingen beheren** in de overzichtsblade van Device Provisioning Service. Selecteer het tabblad **Afzonderlijke inschrijvingen** en selecteer de vermelding voor de nieuwe inschrijving (*eerste*) om de goedkeuringssleutel en andere eigenschappen voor de vermelding te controleren.

    ![Eigenschappen van de inschrijving in de portal](./media/quick-enroll-device-tpm-node/verify-enrollment-portal.png) 
 
Nu u een afzonderlijke inschrijving hebt gemaakt voor een TPM-apparaat, kunt u doorgaan met de resterende stappen in [Create and provision a simulated device](quick-create-simulated-device.md) (Een gesimuleerd apparaat maken en inrichten) als u een gesimuleerd apparaat wilt inschrijven. Sla de stappen voor het maken van een afzonderlijke inschrijving via de Azure-portal in de desbetreffende quickstart over.

## <a name="clean-up-resources"></a>Resources opschonen
Als u van plan bent om de voorbeelden van de Node.js-service te verkennen, verwijdert u de resources die u in deze quickstart hebt gemaakt, niet. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources te verwijderen die via deze quickstart zijn gemaakt.

1. Sluit het uitvoervenster van het Node.js-voorbeeld op de computer.
1. Als u een gesimuleerd TPM-apparaat hebt gemaakt, sluit u het venster van de TPM-simulator.
2. Navigeer naar Device Provisioning Service in Azure Portal, selecteer **Inschrijvingen beheren** en selecteer vervolgens het tabblad **Afzonderlijke inschrijvingen**. Schakel het selectievakje naast de *Registratie-id* in voor de inschrijvingsvermelding die u hebt gemaakt met behulp van deze quickstart. Klik vervolgens op de knop **Verwijderen** bovenaan het deelvenster. 
 
## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u programmatisch een afzonderlijke inschrijvingsvermelding gemaakt voor een TPM-apparaat. Ook hebt u desgewenst een gesimuleerd TPM-apparaat op de computer gemaakt en dit apparaat ingericht voor uw IoT-hub met Azure IoT Hub Device Provisioning Service. Voor meer informatie over device provisioning, gaat u verder met de zelfstudie voor het instellen van Device Provisioning Service in Azure Portal. 
 
> [!div class="nextstepaction"]
> [Zelfstudies over Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)