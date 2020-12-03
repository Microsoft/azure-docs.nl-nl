---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: 8cae7ac2d3b961120cb9100f5072dc141769afff
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557865"
---
## <a name="create-a-container-registry"></a>Een containerregister maken

In deze zelfstudie gebruikt u de Azure IoT Tools-extensie om een module te bouwen en maakt u een **containerinstallatiekopie** van de bestanden. Vervolgens pusht u deze installatiekopie naar een **register** waarin uw installatiekopieën worden opgeslagen en beheerd. Tot slot implementeert u de installatiekopie uit het register voor uitvoering op uw IoT Edge-apparaat.

U kunt een Docker-register gebruiken om de containerinstallatiekopieën op te slaan. Twee populaire Docker-registerservices zijn [Azure Container Registry](../articles/container-registry/index.yml) en [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). In deze zelfstudie wordt Azure Container Registry gebruikt.

Als u nog geen containerregister hebt, volgt u deze stappen om een nieuw containerregister te maken in Azure:

1. Selecteer in [Azure Portal](https://portal.azure.com) de optie **Een resource maken** > **Containers** > **Container Registry**.

2. Geef de volgende waarden op om uw containerregister te maken:

   | Veld | Waarde |
   | ----- | ----- |
   | Abonnement | Selecteer een abonnement in de vervolgkeuzelijst. |
   | Resourcegroep | Het wordt aangeraden om dezelfde resourcegroep te gebruiken voor alle test-resources die u maakt met de snelstartgidsen en zelfstudies voor IoT Edge, zoals **IoTEdgeResources**. |
   | Registernaam | Geef hier een unieke naam op. |
   | Locatie | Kies een locatie dicht bij u in de buurt. |
   | SKU | Selecteer **Basic**. |

3. Selecteer **Maken**.

4. Nadat u het containerregister hebt gemaakt, bladert u ernaartoe en selecteert u in het linkerdeelvenster **Toegangssleutels** in het menu onder **Instellingen**. 

5. Klik om de beheerder in te schakelen om de **gebruikersnaam** en het **wachtwoord** voor het containerregister weer te geven.

6. Kopieer de waarden voor **Aanmeldingsserver**, **Gebruikersnaam** en **Wachtwoord** en sla deze op een handige plaats op. U gebruikt deze waarden verderop in de zelfstudie om toegang te verlenen tot het containerregister.

   ![Aanmeldingsserver, gebruikersnaam en wachtwoord voor het containerregister kopiëren](./media/iot-edge-create-container-registry/registry-access-key.png)