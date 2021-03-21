---
title: Cher well verbinden met IT Service Management-connector
description: In dit artikel vindt u informatie over het Cher well van de IT Service Management-connector (ITSMC) in Azure Monitor om de ITSM-werk items centraal te controleren en te beheren.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: d1fe5e20c06648aa6efc899fabb3ced3345ab41f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045408"
---
# <a name="connect-cherwell-with-it-service-management-connector"></a>Cher well verbinden met IT Service Management-connector

Dit artikel bevat informatie over het configureren van de verbinding tussen uw Cher well-exemplaar en de IT Service Management-connector (ITSMC) in Log Analytics om uw werk items centraal te beheren.

> [!NOTE]
> Van 1-okt-2020 Cher well ITSM-integratie met Azure-waarschuwing is niet langer beschikbaar voor nieuwe klanten. Nieuwe ITSM-verbindingen worden niet ondersteund.
> Bestaande ITSM-verbindingen worden ondersteund.

De volgende secties bevatten informatie over het aansluiten van uw Cher well-product op ITSMC in Azure.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat aan de volgende vereisten wordt voldaan:

- ITSMC is geïnstalleerd. Meer informatie: [het toevoegen van de IT Service Management-connector-oplossing](./itsmc-definition.md#add-it-service-management-connector).
- De client-ID is gegenereerd. Meer informatie: [client-id genereren voor Cher well](#generate-client-id-for-cherwell).
- Gebruikersrol: beheerder.

## <a name="connection-procedure"></a>Verbindings procedure

Gebruik de volgende procedure om een Cher well-verbinding te maken:

1. Ga in Azure Portal naar **alle resources** en zoek naar **Service Desk (YourWorkspaceName)**

2. Klik onder **gegevens bronnen voor werk ruimte** op **ITSM-verbindingen**.
    ![Nieuwe verbinding](/media/itsmc-overview/add-new-itsm-connection.png)

3. Klik boven in het rechterdeel venster op **toevoegen**.

4. Geef de informatie op zoals beschreven in de volgende tabel en klik op **OK** om de verbinding te maken.

> [!NOTE]
> Al deze para meters zijn verplicht.

| **Veld** | **Beschrijving** |
| --- | --- |
| **Verbindingsnaam**   | Typ een naam voor het Cher well-exemplaar dat u wilt verbinden met ITSMC.  U kunt deze naam later gebruiken bij het configureren van werk items in deze ITSM/gedetailleerde log Analytics weer geven. |
| **Partner type**   | Selecteer **Cher well.** |
| **Gebruikersnaam**   | Typ de Cher well-gebruikers naam die verbinding kan maken met ITSMC. |
| **Wachtwoord**   | Typ het wacht woord dat is gekoppeld aan deze gebruikers naam. **Opmerking:** Gebruikers naam en wacht woord worden alleen gebruikt voor het genereren van verificatie tokens en worden nergens opgeslagen in de ITSMC-service.|
| **Server-URL**   | Typ de URL van uw Cher well-exemplaar dat u wilt verbinden met ITSMC. |
| **Client ID**   | Typ de client-ID voor het verifiëren van deze verbinding die u hebt gegenereerd in uw Cher well-exemplaar.   |
| **Bereik voor gegevens synchronisatie**   | Selecteer de Cher well-werk items die u wilt synchroniseren via ITSMC.  Deze werk items worden geïmporteerd in log Analytics.   **Opties:**  Incidenten, wijzigings aanvragen. |
| **Gegevens synchroniseren** | Typ het aantal voorbije dagen waaruit u de gegevens wilt. **Maximum limiet**: 120 dagen. |
| **Een nieuw configuratie-item maken in de ITSM-oplossing** | Selecteer deze optie als u de configuratie-items wilt maken in het ITSM-product. Wanneer dit is ingeschakeld, maakt ITSMC het betrokken CIs als configuratie-items (in het geval van een niet-bestaand CIs) in het ondersteunde ITSM-systeem. **Standaard**: uitgeschakeld. |

![Cher well-verbinding](media/itsmc-connections-cherwell/itsm-connections-cherwell-latest.png)

**Bij een geslaagde verbinding en gesynchroniseerd**:

- Geselecteerde werk items van dit Cher well-exemplaar worden geïmporteerd in azure **log Analytics.** U kunt de samen vatting van deze werk items weer geven op de tegel IT Service Management-connector.

- U kunt incidenten maken op basis van Log Analytics waarschuwingen of logboek records of vanuit Azure-waarschuwingen in dit Cher well-exemplaar.

Meer informatie: [ITSM-werk items maken op basis van Azure-waarschuwingen](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Client-ID genereren voor Cher well

Als u de client-ID/sleutel voor Cher well wilt genereren, gebruikt u de volgende procedure:

1. Meld u als beheerder aan bij uw Cher well-exemplaar.
2. Klik op **beveiligings**  >  **bewerking rest API client instellingen**.
3. Selecteer **Nieuw client**  >  **geheim** maken.

    ![Cher well-gebruikers-id](media/itsmc-connections-cherwell/itsmc-cherwell-client-id.png)

## <a name="next-steps"></a>Volgende stappen

* [ITSM-connectoroverzicht](itsmc-overview.md)
* [ITSM-werk items maken op basis van Azure-waarschuwingen](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Problemen oplossen in ITSM-connector](./itsmc-resync-servicenow.md)