---
title: Provance verbinden met IT Service Management-connector
description: In dit artikel vindt u informatie over het Provance van de IT Service Management-connector (ITSMC) in Azure Monitor om de ITSM-werk items centraal te controleren en te beheren.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 542b127823a73058f319e6a39c001bd563f042ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045374"
---
# <a name="connect-provance-with-it-service-management-connector"></a>Provance verbinden met IT Service Management-connector

Dit artikel bevat informatie over het configureren van de verbinding tussen uw Provance-exemplaar en de IT Service Management-connector (ITSMC) in Log Analytics om uw werk items centraal te beheren.

> [!NOTE]
> Van 1-okt-2020 Provance ITSM-integratie met Azure-waarschuwing is niet langer beschikbaar voor nieuwe klanten. Nieuwe ITSM-verbindingen worden niet ondersteund.
> Bestaande ITSM-verbindingen worden ondersteund.

De volgende secties bevatten informatie over het aansluiten van uw Provance-product op ITSMC in Azure.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat aan de volgende vereisten wordt voldaan:

- ITSMC is geïnstalleerd. Meer informatie: [het toevoegen van de IT Service Management-connector-oplossing](./itsmc-definition.md#add-it-service-management-connector).
- De Provance-app moet worden geregistreerd met Azure AD-en client-ID beschikbaar worden gesteld. Zie [Active Directory-verificatie configureren](../../app-service/configure-authentication-provider-aad.md)voor gedetailleerde informatie.

- Gebruikersrol: beheerder.

## <a name="connection-procedure"></a>Verbindings procedure

Gebruik de volgende procedure om een Provance-verbinding te maken:

1. Ga in Azure Portal naar **alle resources** en zoek naar **Service Desk (YourWorkspaceName)**

2. Klik onder **gegevens bronnen voor werk ruimte** op **ITSM-verbindingen**.
    ![Nieuwe verbinding](media/itsmc-overview/add-new-itsm-connection.png)

3. Klik boven in het rechterdeel venster op **toevoegen**.

4. Geef de informatie op zoals beschreven in de volgende tabel en klik op **OK** om de verbinding te maken.

> [!NOTE]
> Al deze para meters zijn verplicht.

| **Veld** | **Beschrijving** |
| --- | --- |
| **Verbindingsnaam**   | Typ een naam voor het Provance-exemplaar dat u wilt verbinden met ITSMC.  U kunt deze naam later gebruiken bij het configureren van werk items in deze ITSM/gedetailleerde log Analytics weer geven. |
| **Partner type**   | Selecteer **Provance**. |
| **Gebruikersnaam**   | Typ de gebruikers naam waarmee verbinding kan worden gemaakt met ITSMC.    |
| **Wachtwoord**   | Typ het wacht woord dat is gekoppeld aan deze gebruikers naam. **Opmerking:** Gebruikers naam en wacht woord worden alleen gebruikt voor het genereren van verificatie tokens en worden nergens opgeslagen in de ITSMC-service.|
| **Server-URL**   | Typ de URL van uw Provance-exemplaar dat u wilt verbinden met ITSMC. |
| **Client ID**   | Typ de client-ID voor het verifiëren van deze verbinding die u hebt gegenereerd in uw Provance-exemplaar.  Zie [Active Directory-verificatie configureren](../../app-service/configure-authentication-provider-aad.md)voor meer informatie over de client-id. |
| **Bereik voor gegevens synchronisatie**   | Selecteer de Provance-werk items die u wilt synchroniseren met Azure Log Analytics via ITSMC.  Deze werk items worden geïmporteerd in log Analytics.   **Opties:**   Incidenten, wijzigings aanvragen.|
| **Gegevens synchroniseren** | Typ het aantal voorbije dagen waaruit u de gegevens wilt. **Maximum limiet**: 120 dagen. |
| **Een nieuw configuratie-item maken in de ITSM-oplossing** | Selecteer deze optie als u de configuratie-items wilt maken in het ITSM-product. Wanneer dit is ingeschakeld, maakt ITSMC het betrokken CIs als configuratie-items (in het geval van een niet-bestaand CIs) in het ondersteunde ITSM-systeem. **Standaard**: uitgeschakeld.|

![Scherm opname van de lijsten met de verbindings naam en het partner type.](media/itsmc-connections-provance/itsm-connections-provance-latest.png)

**Bij een geslaagde verbinding en gesynchroniseerd**:

- Geselecteerde werk items van dit Provance-exemplaar worden geïmporteerd in azure **log Analytics.** U kunt de samen vatting van deze werk items weer geven op de tegel IT Service Management-connector.

- U kunt incidenten maken op basis van Log Analytics waarschuwingen of logboek records of vanuit Azure-waarschuwingen in dit Provance-exemplaar.

## <a name="next-steps"></a>Volgende stappen

* [ITSM-connectoroverzicht](itsmc-overview.md)
* [ITSM-werk items maken op basis van Azure-waarschuwingen](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Problemen oplossen in ITSM-connector](./itsmc-resync-servicenow.md)