---
title: IT Service Management-connector in Azure Monitor
description: Dit artikel bevat informatie over het verbinden van uw ITSM-producten/-services met de IT Service Management-connector (ITSMC) in Azure Monitor om de ITSM-werk items centraal te controleren en te beheren.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 9b097b561ef6b91ae648a950247d1a88b99e7e64
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614809"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>ITSM-producten/-services verbinden met IT-servicebeheerconnector
Dit artikel bevat informatie over het configureren van de verbinding tussen uw ITSM-product/-service en de IT Service Management-connector (ITSMC) in Log Analytics om uw werk items centraal te beheren. Zie [overzicht](./itsmc-overview.md)voor meer informatie over ITSMC.

De volgende ITSM-producten/-services worden ondersteund. Selecteer het product om gedetailleerde informatie weer te geven over hoe u het product verbindt met ITSMC.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> We stellen onze klanten van Cher well en Provance voor om de [actie webhook](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) te gebruiken voor Cher well en Provance eind punt als een andere oplossing voor de integratie.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>System Center Service Manager verbinding maken met IT Service Management-connector in azure

De volgende secties bevatten informatie over het aansluiten van uw System Center Service Manager-product op ITSMC in Azure.

### <a name="prerequisites"></a>Vereisten

Zorg ervoor dat aan de volgende vereisten wordt voldaan:

- ITSMC is geïnstalleerd. Meer informatie: [het toevoegen van de IT Service Management-connector-oplossing](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview).
- De Service Manager-webtoepassing (Web-app) wordt geïmplementeerd en geconfigureerd. [Hier](#create-and-deploy-service-manager-web-app-service)vindt u informatie over de web-app.
- Hybride verbinding is gemaakt en geconfigureerd. Meer informatie: [de hybride verbinding configureren](#configure-the-hybrid-connection).
- Ondersteunde versies van Service Manager: 2012 R2 of 2016.
- Gebruikersrol:  [Geavanceerde operator](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10)).
- De waarschuwingen die vanaf Azure Monitor worden verzonden, kunnen nu worden gemaakt in System Center Service Manager incidenten.

> [!NOTE]
> 
> - ITSM-connector kan alleen verbinding maken met ServiceNow-exemplaren op basis van de Cloud. On-premises ServiceNow-instanties worden momenteel niet ondersteund.
> - Als u aangepaste [sjablonen](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview) wilt gebruiken als onderdeel van de acties, moet de para meter "ProjectionType" in de sjabloon SCSM worden toegewezen aan "IncidentManagement! System. WorkItem. incident. ProjectionType "

### <a name="connection-procedure"></a>Verbindings procedure

Gebruik de volgende procedure om uw System Center Service Manager-exemplaar te verbinden met ITSMC:

1. Ga in Azure Portal naar **alle resources** en zoek naar **Service Desk (YourWorkspaceName)**

2.  Klik onder **gegevens bronnen voor werk ruimte** op **ITSM-verbindingen**.

    ![Nieuwe verbinding](media/itsmc-connections/add-new-itsm-connection.png)

3. Klik boven in het rechterdeel venster op **toevoegen**.

4. Geef de informatie op zoals beschreven in de volgende tabel en klik op **OK** om de verbinding te maken.

> [!NOTE]
> 
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


Meer informatie: [ITSM-werk items maken op basis van Azure-waarschuwingen](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Service Manager web app-service maken en implementeren

Als u de on-premises Service Manager wilt verbinden met ITSMC in azure, heeft micro soft een Service Manager-web-app gemaakt op de GitHub.

Ga als volgt te werk om de ITSM-web-app in te stellen voor uw Service Manager:

- **De web-app implementeren** : implementeer de web-app, stel de eigenschappen in en verificatie met Azure AD. U kunt de web-app implementeren met behulp van het [geautomatiseerde script](./itsmc-service-manager-script.md) dat micro soft u heeft verschaft.
- **De hybride verbinding configureren**  -  [Deze verbinding](#configure-the-hybrid-connection)hand matig configureren.

#### <a name="deploy-the-web-app"></a>De web-app implementeren
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

### <a name="configure-the-hybrid-connection"></a>De hybride verbinding configureren

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

#### <a name="configure-the-listener-setup"></a>De listener-installatie configureren

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

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>ServiceNow verbinden met IT Service Management-connector in azure

De volgende secties bevatten informatie over het aansluiten van uw ServiceNow-product op ITSMC in Azure.

### <a name="prerequisites"></a>Vereisten
Zorg ervoor dat aan de volgende vereisten wordt voldaan:
- ITSMC is geïnstalleerd. Meer informatie: [het toevoegen van de IT Service Management-connector-oplossing](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#add-it-service-management-connector).
- ServiceNow ondersteunde versies: Orlando, New York, Madrid, Londen, Kingston, Jakarta, Istanboel, Helsinki, Genève.
- De waarschuwingen die via Azure Monitor worden verzonden, kunnen nu worden gemaakt in ServiceNow een van de volgende elementen: gebeurtenissen, incidenten of waarschuwingen.
> [!NOTE]
> ITSMC biedt alleen ondersteuning voor de officiële SaaS-aanbieding van de service. Persoonlijke implementaties van de service worden nu niet ondersteund. 

**ServiceNow-beheerders moeten het volgende doen in hun ServiceNow-exemplaar**:
- Genereer een client-ID en client geheim voor het ServiceNow-product. Voor informatie over het genereren van client-ID en geheim, raadpleegt u de volgende informatie zoals vereist:

    - [OAuth instellen voor Orlando](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth voor New York instellen](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth instellen voor Madrid](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth instellen voor Londen](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth instellen voor Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth instellen voor Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth voor Istanboel instellen](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth instellen voor Helsinki](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth instellen voor Genève](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)
> [!NOTE]
> Als onderdeel van de definitie van de ' OAuth ' instellen, kunt u het beste het volgende doen:
>
> 1) **De levens duur van het vernieuwings token bijwerken naar 90 dagen (7.776.000 seconden):** Als onderdeel van de [instelling OAuth](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_SettingUpOAuth.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696739125&sdata=Q7mF6Ej8MCupKaEJpabTM56EDZ1T8vFVyihhoM594aA%3D&reserved=0) in fase 2: [een eind punt maken voor clients om toegang te krijgen tot het exemplaar](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_CreateEndpointforExternalClients.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696749123&sdata=hoAJHJAFgUeszYCX1Q%2FXr4N%2FAKiFcm5WV7mwR2UqeWA%3D&reserved=0) na de definitie van het eind punt, in ServiceNow Blade zoeken naar systeem-OAuth dan selecteert u toepassings register. Kies de naam van de opgegeven OAuth en werk het veld van de duur van het vernieuwings token bij naar 7.776.000 (90 dagen in seconden).
> Klik aan het einde op bijwerken.
> 2) **We raden u aan een interne procedure in te stellen om ervoor te zorgen dat de verbinding actief blijft:** Volgens de levens duur van het vernieuwings token voor het vernieuwen van het token. Zorg ervoor dat de volgende bewerkingen voorafgaand aan het vernieuwings token een verwachte verloop tijd (paar dagen voordat de levens duur van het vernieuwings token verloopt, wordt aangeraden):
>
> 1. [Een hand matig synchronisatie proces voor de configuratie van de ITSM-connector volt ooien](./itsmc-resync-servicenow.md)
> 2. Intrekken naar het oude vernieuwings token, omdat dit niet wordt aangeraden om oude sleutels uit veiligheids overwegingen te hand haven. Zoek in de Blade ServiceNow naar systeem-OAuth dan Selecteer tokens beheren. Kies het oude token in de lijst op basis van de OAuth-naam en de verval datum.
> ![Definitie van het winter-systeem](media/itsmc-connections/snow-system-oauth.png)
> 3. Klik op toegang intrekken en vervolgens op intrekken.

- Installeer de gebruikers-app voor micro soft Log Analytics Integration (ServiceNow-app). [Meer informatie](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
> [!NOTE]
> ITSMC ondersteunt alleen de officiële gebruikers-app voor de integratie van micro soft Log Analytics die wordt gedownload uit ServiceNow Store. ITSMC bieden geen ondersteuning voor code opname in ServiceNow-zijde of de toepassing die geen deel uitmaakt van de officiële ServiceNow-oplossing. 
- Maak een gebruikersrol voor integratie voor de app van de gebruiker geïnstalleerd. [Hier](#create-integration-user-role-in-servicenow-app)vindt u informatie over het maken van de gebruikersrol integratie.

### <a name="connection-procedure"></a>**Verbindings procedure**
Gebruik de volgende procedure om een ServiceNow-verbinding te maken:


1. Ga in Azure Portal naar **alle resources** en zoek naar **Service Desk (YourWorkspaceName)**

2.  Klik onder **gegevens bronnen voor werk ruimte** op **ITSM-verbindingen**.
    ![Nieuwe verbinding](media/itsmc-connections/add-new-itsm-connection.png)

3. Klik boven in het rechterdeel venster op **toevoegen**.

4. Geef de informatie op zoals beschreven in de volgende tabel en klik op **OK** om de verbinding te maken.


> [!NOTE]
> Al deze para meters zijn verplicht.

| **Veld** | **Beschrijving** |
| --- | --- |
| **Verbindingsnaam**   | Typ een naam voor het ServiceNow-exemplaar dat u wilt verbinden met ITSMC.  U gebruikt deze naam later in Log Analytics bij het configureren van werk items in deze ITSM/gedetailleerde log Analytics weer geven. |
| **Partner type**   | Selecteer **ServiceNow**. |
| **Gebruikersnaam**   | Typ de gebruikers naam voor integratie die u hebt gemaakt in de ServiceNow-app om de verbinding met ITSMC te ondersteunen. Meer informatie: [Maak een gebruikersrol](#create-integration-user-role-in-servicenow-app)voor de ServiceNow-app.|
| **Wachtwoord**   | Typ het wacht woord dat is gekoppeld aan deze gebruikers naam. **Opmerking**: de gebruikers naam en het wacht woord worden alleen gebruikt voor het genereren van verificatie tokens en worden nergens opgeslagen in de ITSMC-service.  |
| **Server-URL**   | Typ de URL van het ServiceNow-exemplaar dat u wilt verbinden met ITSMC. De URL moet verwijzen naar een ondersteunde SaaS-versie met achtervoegsel '. servicenow.com '.|
| **Client ID**   | Typ de client-ID die u wilt gebruiken voor OAuth2-verificatie, die u eerder hebt gegenereerd.  Meer informatie over het genereren van client-ID en geheim:   [OAuth Setup](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Clientgeheim**   | Typ het client geheim dat voor deze ID wordt gegenereerd.   |
| **Bereik voor gegevens synchronisatie**   | Selecteer de ServiceNow-werk items die u wilt synchroniseren met Azure Log Analytics via de ITSMC.  De geselecteerde waarden worden in log Analytics geïmporteerd.   **Opties:**  Incidenten en wijzigings aanvragen.|
| **Gegevens synchroniseren** | Typ het aantal voorbije dagen waaruit u de gegevens wilt. **Maximum limiet**: 120 dagen. |
| **Een nieuw configuratie-item maken in de ITSM-oplossing** | Selecteer deze optie als u de configuratie-items wilt maken in het ITSM-product. Wanneer dit is ingeschakeld, maakt ITSMC het betrokken CIs als configuratie-items (in het geval van een niet-bestaand CIs) in het ondersteunde ITSM-systeem. **Standaard**: uitgeschakeld. |

![ServiceNow-verbinding](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Bij een geslaagde verbinding en gesynchroniseerd**:

- Geselecteerde werk items van ServiceNow-exemplaar worden geïmporteerd in azure **log Analytics.** U kunt de samen vatting van deze werk items weer geven op de tegel IT Service Management-connector.

- U kunt incidenten maken op basis van Log Analytics waarschuwingen of logboek records of vanuit Azure-waarschuwingen in dit ServiceNow-exemplaar.

Meer informatie: [ITSM-werk items maken op basis van Azure-waarschuwingen](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#create-itsm-work-items-from-azure-alerts).


> [!NOTE]
> In ServiceNow is een frequentie limiet voor aanvragen per uur. Als u de limiet wilt configureren, gebruikt u voor het definiëren van ' binnenkomende REST API-frequentie beperking ' in het ServiceNow-exemplaar.

### <a name="create-integration-user-role-in-servicenow-app"></a>De gebruikersrol integratie maken in de ServiceNow-app

Gebruiker de volgende procedure:

1. Ga naar de [ServiceNow Store](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) en installeer de **gebruikers-app voor ServiceNow en micro soft OMS-integratie** in uw ServiceNow-exemplaar.
   
   >[!NOTE]
   >Als onderdeel van de lopende overgang van Microsoft Operations Management Suite (OMS) naar Azure Monitor, wordt OMS nu aangeduid als Log Analytics.     
2. Na de installatie gaat u naar de linkernavigatiebalk van het ServiceNow-exemplaar, zoekt u naar micro soft OMS integrator en selecteert u deze.  
3. Klik op **controle lijst voor installatie**.

   De status wordt weer gegeven als  **niet voltooid** als de gebruikersrol nog moet worden gemaakt.

4. Voer in de tekst vakken naast **integratie gebruiker maken** de gebruikers naam in voor de gebruiker die verbinding kan maken met ITSMC in Azure.
5. Voer het wacht woord voor deze gebruiker in en klik op **OK**.  

> [!NOTE]
> 
> U gebruikt deze referenties om de ServiceNow-verbinding in azure te maken.

De zojuist gemaakte gebruiker wordt weer gegeven met de standaard rollen toegewezen.

**Standaard rollen**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft. gebruiker
-   ITIL
-   template_editor
-   view_changer

Zodra de gebruiker is gemaakt, wordt de status van de controle **lijst voor installatie controleren** verplaatst naar voltooid. hierin worden de details weer gegeven van de gebruikersrol die voor de app is gemaakt.

> [!NOTE]
> 
> ITSM-connector kunt incidenten verzenden naar ServiceNow zonder dat er andere modules zijn geïnstalleerd op uw ServiceNow-exemplaar. Als u de EventManagement-module in uw ServiceNow-exemplaar gebruikt en u gebeurtenissen of waarschuwingen in ServiceNow wilt maken met behulp van de connector, voegt u de volgende rollen toe aan de integratie gebruiker:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Provance verbinden met IT Service Management-connector in azure

De volgende secties bevatten informatie over het aansluiten van uw Provance-product op ITSMC in Azure.

> [!NOTE]
> 
> We stellen onze Provance-klanten voor om [webhook-actie](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) te gebruiken voor Cher well en Provance-eind punt als een andere oplossing voor de integratie.

### <a name="prerequisites"></a>Vereisten

Zorg ervoor dat aan de volgende vereisten wordt voldaan:


- ITSMC is geïnstalleerd. Meer informatie: [het toevoegen van de IT Service Management-connector-oplossing](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#add-it-service-management-connector).
- De Provance-app moet worden geregistreerd met Azure AD-en client-ID beschikbaar worden gesteld. Zie [Active Directory-verificatie configureren](../../app-service/configure-authentication-provider-aad.md)voor gedetailleerde informatie.

- Gebruikersrol: beheerder.

### <a name="connection-procedure"></a>Verbindings procedure

Gebruik de volgende procedure om een Provance-verbinding te maken:

1. Ga in Azure Portal naar **alle resources** en zoek naar **Service Desk (YourWorkspaceName)**

2.  Klik onder **gegevens bronnen voor werk ruimte** op **ITSM-verbindingen**.
    ![Nieuwe verbinding](media/itsmc-connections/add-new-itsm-connection.png)

3. Klik boven in het rechterdeel venster op **toevoegen**.

4. Geef de informatie op zoals beschreven in de volgende tabel en klik op **OK** om de verbinding te maken.

> [!NOTE]
> 
> Al deze para meters zijn verplicht.

| **Veld** | **Beschrijving** |
| --- | --- |
| **Verbindingsnaam**   | Typ een naam voor het Provance-exemplaar dat u wilt verbinden met ITSMC.  U kunt deze naam later gebruiken bij het configureren van werk items in deze ITSM/gedetailleerde log Analytics weer geven. |
| **Partner type**   | Selecteer **Provance**. |
| **Gebruikersnaam**   | Typ de gebruikers naam waarmee verbinding kan worden gemaakt met ITSMC.    |
| **Wachtwoord**   | Typ het wacht woord dat is gekoppeld aan deze gebruikers naam. **Opmerking:** Gebruikers naam en wacht woord worden alleen gebruikt voor het genereren van verificatie tokens en worden nergens opgeslagen in de ITSMC-service._|
| **Server-URL**   | Typ de URL van uw Provance-exemplaar dat u wilt verbinden met ITSMC. |
| **Client ID**   | Typ de client-ID voor het verifiëren van deze verbinding die u hebt gegenereerd in uw Provance-exemplaar.  Zie [Active Directory-verificatie configureren](../../app-service/configure-authentication-provider-aad.md)voor meer informatie over de client-id. |
| **Bereik voor gegevens synchronisatie**   | Selecteer de Provance-werk items die u wilt synchroniseren met Azure Log Analytics via ITSMC.  Deze werk items worden geïmporteerd in log Analytics.   **Opties:**   Incidenten, wijzigings aanvragen.|
| **Gegevens synchroniseren** | Typ het aantal voorbije dagen waaruit u de gegevens wilt. **Maximum limiet**: 120 dagen. |
| **Een nieuw configuratie-item maken in de ITSM-oplossing** | Selecteer deze optie als u de configuratie-items wilt maken in het ITSM-product. Wanneer dit is ingeschakeld, maakt ITSMC het betrokken CIs als configuratie-items (in het geval van een niet-bestaand CIs) in het ondersteunde ITSM-systeem. **Standaard**: uitgeschakeld.|

![Scherm opname van de lijsten met de verbindings naam en het partner type.](media/itsmc-connections/itsm-connections-provance-latest.png)

**Bij een geslaagde verbinding en gesynchroniseerd**:

- Geselecteerde werk items van dit Provance-exemplaar worden geïmporteerd in azure **log Analytics.** U kunt de samen vatting van deze werk items weer geven op de tegel IT Service Management-connector.

- U kunt incidenten maken op basis van Log Analytics waarschuwingen of logboek records of vanuit Azure-waarschuwingen in dit Provance-exemplaar.

Meer informatie: [ITSM-werk items maken op basis van Azure-waarschuwingen](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Cher well verbinden met IT Service Management-connector in azure

De volgende secties bevatten informatie over het aansluiten van uw Cher well-product op ITSMC in Azure.

> [!NOTE]
> 
> We stellen onze Cher well-klanten voor om [webhook-actie](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) te gebruiken voor Cher well en Provance-eind punt als een andere oplossing voor de integratie.

### <a name="prerequisites"></a>Vereisten

Zorg ervoor dat aan de volgende vereisten wordt voldaan:

- ITSMC is geïnstalleerd. Meer informatie: [het toevoegen van de IT Service Management-connector-oplossing](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#add-it-service-management-connector).
- De client-ID is gegenereerd. Meer informatie: [client-id genereren voor Cher well](#generate-client-id-for-cherwell).
- Gebruikersrol: beheerder.

### <a name="connection-procedure"></a>Verbindings procedure

Gebruik de volgende procedure om een Cher well-verbinding te maken:

1. Ga in Azure Portal naar **alle resources** en zoek naar **Service Desk (YourWorkspaceName)**

2.  Klik onder **gegevens bronnen voor werk ruimte** op **ITSM-verbindingen**.
    ![Nieuwe verbinding](media/itsmc-connections/add-new-itsm-connection.png)

3. Klik boven in het rechterdeel venster op **toevoegen**.

4. Geef de informatie op zoals beschreven in de volgende tabel en klik op **OK** om de verbinding te maken.

> [!NOTE]
> 
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


![Provance-verbinding](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Bij een geslaagde verbinding en gesynchroniseerd**:

- Geselecteerde werk items van dit Cher well-exemplaar worden geïmporteerd in azure **log Analytics.** U kunt de samen vatting van deze werk items weer geven op de tegel IT Service Management-connector.

- U kunt incidenten maken op basis van Log Analytics waarschuwingen of logboek records of vanuit Azure-waarschuwingen in dit Cher well-exemplaar.

Meer informatie: [ITSM-werk items maken op basis van Azure-waarschuwingen](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Client-ID genereren voor Cher well

Als u de client-ID/sleutel voor Cher well wilt genereren, gebruikt u de volgende procedure:

1. Meld u als beheerder aan bij uw Cher well-exemplaar.
2. Klik op **beveiligings**  >  **bewerking rest API client instellingen**.
3. Selecteer **Nieuw client**  >  **geheim** maken.

    ![Cher well-gebruikers-id](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Volgende stappen
 - [ITSM-werk items maken op basis van Azure-waarschuwingen](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#create-itsm-work-items-from-azure-alerts)
