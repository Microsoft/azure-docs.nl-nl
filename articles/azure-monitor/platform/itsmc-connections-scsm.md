---
title: SCSM verbinden met IT Service Management-connector
description: In dit artikel vindt u informatie over het SCSM van de IT Service Management-connector (ITSMC) in Azure Monitor om de ITSM-werk items centraal te controleren en te beheren.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 907a78b15cca4718308f79bc6be2e6258bc04d19
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492668"
---
# <a name="connect-system-center-service-manager-with-it-service-management-connector"></a>System Center Service Manager verbinden met IT Service Management-connector

Dit artikel bevat informatie over het configureren van de verbinding tussen uw System Center Service Manager-exemplaar en de IT Service Management-connector (ITSMC) in Log Analytics om uw werk items centraal te beheren.

De volgende secties bevatten informatie over het aansluiten van uw System Center Service Manager-product op ITSMC in Azure.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat aan de volgende vereisten wordt voldaan:

- ITSMC is geïnstalleerd. Meer informatie: [het toevoegen van de IT Service Management-connector-oplossing](./itsmc-definition.md).
- De Service Manager-webtoepassing (Web-app) wordt geïmplementeerd en geconfigureerd. [Hier](#create-and-deploy-service-manager-web-app-service)vindt u informatie over de web-app.
- Hybride verbinding is gemaakt en geconfigureerd. Meer informatie: [de hybride verbinding configureren](#configure-the-hybrid-connection).
- Ondersteunde versies van Service Manager: 2012 R2 of 2016.
- Gebruikersrol:  [Geavanceerde operator](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10)).
- De waarschuwingen die vanaf Azure Monitor worden verzonden, kunnen nu worden gemaakt in System Center Service Manager incidenten.

> [!NOTE]
> - ITSM-connector kan alleen verbinding maken met ServiceNow-exemplaren op basis van de Cloud. On-premises ServiceNow-instanties worden momenteel niet ondersteund.
> - Als u aangepaste [sjablonen](./itsmc-definition.md#define-a-template) wilt gebruiken als onderdeel van de acties, moet de para meter "ProjectionType" in de sjabloon SCSM worden toegewezen aan "IncidentManagement! System. WorkItem. incident. ProjectionType "

## <a name="connection-procedure"></a>Verbindings procedure

Gebruik de volgende procedure om uw System Center Service Manager-exemplaar te verbinden met ITSMC:

1. Ga in Azure Portal naar **alle resources** en zoek naar **Service Desk (YourWorkspaceName)**

2. Klik onder **gegevens bronnen voor werk ruimte** op **ITSM-verbindingen**.

    ![Nieuwe verbinding](media/itsmc-connections/add-new-itsm-connection.png)

3. Klik boven in het rechterdeel venster op **toevoegen**.

4. Geef de informatie op zoals beschreven in de volgende tabel en klik op **OK** om de verbinding te maken.

> [!NOTE]
> Al deze para meters zijn verplicht.

| **Veld** | **Beschrijving** |
| --- | --- |
| **Verbindingsnaam**   | Typ een naam voor het System Center Service Manager-exemplaar dat u wilt verbinden met ITSMC.  U kunt deze naam later gebruiken bij het configureren van werk items in dit exemplaar/gedetailleerde log Analytics weer geven. |
| **Partner type**   | Selecteer **System Center Service Manager**. |
| **Server-URL**   | Typ de URL van de Service Manager web-app. [Hier](#create-and-deploy-service-manager-web-app-service)vindt u meer informatie over Service Manager web-app.
| **Client ID**   | Typ de client-ID die u hebt gegenereerd (met behulp van het automatische script) voor de verificatie van de web-app. Meer informatie over het geautomatiseerde script vindt u [hier.](./itsmc-service-manager-script.md)|
| **Clientgeheim**   | Typ het client geheim dat voor deze ID wordt gegenereerd.   |
| **Gegevens synchroniseren**   | Selecteer de Service Manager werk items die u wilt synchroniseren via ITSMC.  Deze werk items worden geïmporteerd in Log Analytics. **Opties:**  Incidenten, wijzigings aanvragen.|
| **Bereik voor gegevens synchronisatie** | Typ het aantal voorbije dagen waaruit u de gegevens wilt. **Maximum limiet**: 120 dagen. |
| **Een nieuw configuratie-item maken in de ITSM-oplossing** | Selecteer deze optie als u de configuratie-items wilt maken in het ITSM-product. Wanneer dit is ingeschakeld, maakt Log Analytics het betrokken CIs als configuratie-items (in het geval van een niet-bestaand CIs) in het ondersteunde ITSM-systeem. **Standaard**: uitgeschakeld. |

![Service Manager-verbinding](media/itsmc-connections/service-manager-connection.png)

**Bij een geslaagde verbinding en gesynchroniseerd**:

- Geselecteerde werk items uit Service Manager worden in azure **log Analytics geïmporteerd.** U kunt de samen vatting van deze werk items weer geven op de tegel IT Service Management-connector.

- U kunt incidenten maken op basis van Log Analytics waarschuwingen of logboek records of vanuit Azure-waarschuwingen in dit Service Manager exemplaar.

Meer informatie: [ITSM-werk items maken op basis van Azure-waarschuwingen](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="create-and-deploy-service-manager-web-app-service"></a>Service Manager web app-service maken en implementeren

Als u de on-premises Service Manager wilt verbinden met ITSMC in azure, heeft micro soft een Service Manager-web-app gemaakt op de GitHub.

Ga als volgt te werk om de ITSM-web-app in te stellen voor uw Service Manager:

- **De web-app implementeren** : implementeer de web-app, stel de eigenschappen in en verificatie met Azure AD. U kunt de web-app implementeren met behulp van het [geautomatiseerde script](./itsmc-service-manager-script.md) dat micro soft u heeft verschaft.
- **De hybride verbinding configureren**  -  [Deze verbinding](#configure-the-hybrid-connection)hand matig configureren.

### <a name="deploy-the-web-app"></a>De web-app implementeren
Gebruik het geautomatiseerde [script](./itsmc-service-manager-script.md) om de web-app te implementeren, de eigenschappen in te stellen en te verifiëren met Azure AD.

Voer het script uit door de volgende vereiste gegevens op te geven:

- Details van Azure-abonnement
- Naam van de resourcegroep
- Locatie
- Service Manager server Details (Server naam, domein, gebruikers naam en wacht woord)
- Het voor voegsel van de site naam voor uw web-app
- Naam ruimte ServiceBus.

Met het script wordt de web-app gemaakt met de naam die u hebt opgegeven (samen met enkele extra teken reeksen om deze uniek te maken). De web- **app-URL**, de **client-id** en het **client geheim** worden gegenereerd.

Sla de waarden op. u gebruikt deze wanneer u een verbinding maakt met ITSMC.

**De installatie van de web-app controleren**

1. Ga naar **Azure Portal**-  >  **resources**.
2. Selecteer de web-app en klik op **instellingen**  >  **Toepassings instellingen**.
3. Controleer de informatie over het Service Manager exemplaar dat u hebt gegeven op het moment van de implementatie van de app via het script.

## <a name="configure-the-hybrid-connection"></a>De hybride verbinding configureren

Gebruik de volgende procedure om de hybride verbinding te configureren die het Service Manager-exemplaar met ITSMC in azure verbindt.

1. Zoek de Service Manager web-app onder **Azure-resources**.
2. Klik op **instellingen**  >  **netwerken**.
3. Klik onder **hybride verbindingen** op **uw hybride verbindings eindpunten configureren**.

    ![Netwerk voor hybride verbindingen](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. Klik op de Blade **hybride verbindingen** op **hybride verbinding toevoegen**.

    ![Hybride verbinding toevoegen](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. Klik op de Blade **hybride verbindingen toevoegen** op **nieuwe hybride verbinding maken**.

    ![Nieuwe hybride verbinding](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Typ de volgende waarden:

   - **Eindpunt naam**: Geef een naam op voor de nieuwe hybride verbinding.
   - **Endpoint-host**: FQDN van de Service Manager-beheer server.
   - **Eindpunt poort**: type 5724
   - **Servicebus naam ruimte**: gebruik een bestaande Servicebus-naam ruimte of maak een nieuwe.
   - **Locatie**: Selecteer de locatie.
   - **Naam**: Geef een naam op voor de servicebus als u deze maakt.

     ![Hybride verbindings waarden](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Klik op **OK** om de Blade **hybride verbinding maken** te sluiten en te beginnen met het maken van de hybride verbinding.

    Zodra de hybride verbinding is gemaakt, wordt deze weer gegeven onder de Blade.

7. Nadat de hybride verbinding is gemaakt, selecteert u de verbinding en klikt u op **geselecteerde hybride verbinding toevoegen**.

    ![Nieuwe hybride verbinding](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

### <a name="configure-the-listener-setup"></a>De listener-installatie configureren

Gebruik de volgende procedure om de installatie van de listener voor de hybride verbinding te configureren.

1. Klik op de Blade **hybride verbindingen** op **down load verbindings beheer** en installeer het op de computer waarop System Center Service Manager exemplaar wordt uitgevoerd.

    Nadat de installatie is voltooid, is **Hybrid Connection Manager optie gebruikers interface** beschikbaar in het menu **Start** .

2. Klik op **Hybrid Connection Manager gebruikers interface** . u wordt gevraagd om uw Azure-referenties.

3. Meld u aan met uw Azure-referenties en selecteer uw abonnement waar de hybride verbinding is gemaakt.

4. Klik op **Opslaan**.

De verbinding met uw hybride verbinding is geslaagd.

![geslaagde hybride verbinding](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Nadat de hybride verbinding is gemaakt, controleert u de verbinding en test u deze door naar de geïmplementeerde Service Manager web-app te gaan. Zorg ervoor dat de verbinding is geslaagd voordat u verbinding probeert te maken met ITSMC in Azure.

In de volgende voorbeeld afbeelding ziet u de details van een geslaagde verbinding:

![Test Hybride verbinding](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="next-steps"></a>Volgende stappen

* [ITSM-connectoroverzicht](itsmc-overview.md)
* [ITSM-werk items maken op basis van Azure-waarschuwingen](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Problemen oplossen in ITSM-connector](./itsmc-resync-servicenow.md)