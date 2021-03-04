---
title: IT Service Management-connector-beveiligd exporteren in Azure Monitor-configuratie met BMC
description: In dit artikel wordt beschreven hoe u uw ITSM-producten of-Services verbindt met BMC in Secure export in Azure Monitor.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 7c50d533f95e1131e26ddd808c77023ae4591a26
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041702"
---
# <a name="connect-bmc-helix-to-azure-monitor"></a>BMC Helix verbinden met Azure Monitor

De volgende secties bevatten informatie over hoe u verbinding kunt maken met uw BMC Helix-product en hoe u de export in azure kunt beveiligen.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan de volgende vereisten voldoet:

* Azure AD is geregistreerd.
* U hebt de ondersteunde versie van BMC Helix multi-Cloud Service Management (versie 19,08 of hoger).

## <a name="configure-the-bmc-helix-connection"></a>De BMC Helix-verbinding configureren

1. Gebruik de volgende procedure in de BMC Helix-omgeving om de URI voor de beveiligde export te verkrijgen:

   1. Meld u aan bij Integration Studio.
   1. Zoek naar de stroom **incident maken vanuit Azure Alerts** .
   1. Kopieer de webhook-URL.
   
   ![Scherm afbeelding van de webhook U R L in Integration Studio.](media/itsmc-secure-webhook-connections-bmc/bmc-url.png)
   
2. Volg de instructies op basis van de versie:
   * Het [inschakelen van vooraf gebouwde integratie met Azure monitor voor versie 20,02](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html).
   * Het [inschakelen van vooraf gebouwde integratie met Azure monitor voor versie 19,11](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html).

3. Als onderdeel van de configuratie van de verbinding in BMC Helix gaat u naar uw integratie-BMC-exemplaar en volgt u deze instructies:

   1. Selecteer **Catalog**.
   2. Selecteer **Azure-waarschuwingen**.
   3. Selecteer **connectors**.
   4. Selecteer **configuratie**.
   5. Selecteer de configuratie **nieuwe verbinding toevoegen** .
   6. Vul de informatie in voor de configuratie sectie:
      - **Name**: Maak uw eigen naam.
      - **Autorisatie type**: **geen**
      - **Beschrijving**: Maak uw eigen naam.
      - **Site**: **Cloud**
      - **Aantal instanties**: **2**, de standaard waarde.
      - **Controle**: standaard ingeschakeld om gebruik in te scha kelen.
      - De Azure-Tenant-ID en de Azure-toepassings-ID zijn afkomstig uit de toepassing die u eerder hebt gedefinieerd.

![Scherm opname van de BMC-configuratie.](media/itsmc-secure-webhook-connections-bmc/bmc-configuration.png)
