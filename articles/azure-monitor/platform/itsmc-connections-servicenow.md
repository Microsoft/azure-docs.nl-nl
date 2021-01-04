---
title: ServiceNow verbinden met IT Service Management-connector
description: In dit artikel vindt u informatie over het ServiceNow van de IT Service Management-connector (ITSMC) in Azure Monitor om de ITSM-werk items centraal te controleren en te beheren.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 662c36e4f0082c376a6e250e9a0885f0cd225964
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729659"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>ServiceNow verbinden met IT Service Management-connector

Dit artikel bevat informatie over het configureren van de verbinding tussen uw ServiceNow-exemplaar en de IT Service Management-connector (ITSMC) in Log Analytics om uw werk items centraal te beheren.

De volgende secties bevatten informatie over het aansluiten van uw ServiceNow-product op ITSMC in Azure.

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat aan de volgende vereisten wordt voldaan:
- ITSMC is geïnstalleerd. Meer informatie: [het toevoegen van de IT Service Management-connector-oplossing](./itsmc-definition.md#add-it-service-management-connector).
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
>     1. [Een hand matig synchronisatie proces voor de configuratie van de ITSM-connector volt ooien](./itsmc-resync-servicenow.md)
>     2. Intrekken naar het oude vernieuwings token, omdat dit niet wordt aangeraden om oude sleutels uit veiligheids overwegingen te hand haven. Zoek in de Blade ServiceNow naar systeem-OAuth dan Selecteer tokens beheren. Kies het oude token in de lijst op basis van de OAuth-naam en de verval datum.
> ![Definitie van het winter-systeem](media/itsmc-connections/snow-system-oauth.png)
>     3. Klik op toegang intrekken en vervolgens op intrekken.

- Installeer de gebruikers-app voor micro soft Log Analytics Integration (ServiceNow-app). [Meer informatie](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
> [!NOTE]
> ITSMC ondersteunt alleen de officiële gebruikers-app voor de integratie van micro soft Log Analytics die wordt gedownload uit ServiceNow Store. ITSMC bieden geen ondersteuning voor code opname in ServiceNow-zijde of de toepassing die geen deel uitmaakt van de officiële ServiceNow-oplossing. 
- Maak een gebruikersrol voor integratie voor de app van de gebruiker geïnstalleerd. [Hier](#create-integration-user-role-in-servicenow-app)vindt u informatie over het maken van de gebruikersrol integratie.

## <a name="connection-procedure"></a>**Verbindings procedure**
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

> [!NOTE]
> In ServiceNow is een frequentie limiet voor aanvragen per uur. Als u de limiet wilt configureren, gebruikt u voor het definiëren van ' binnenkomende REST API-frequentie beperking ' in het ServiceNow-exemplaar.

## <a name="create-integration-user-role-in-servicenow-app"></a>De gebruikersrol integratie maken in de ServiceNow-app

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
> ITSM-connector kunt incidenten verzenden naar ServiceNow zonder dat er andere modules zijn geïnstalleerd op uw ServiceNow-exemplaar. Als u de EventManagement-module in uw ServiceNow-exemplaar gebruikt en u gebeurtenissen of waarschuwingen in ServiceNow wilt maken met behulp van de connector, voegt u de volgende rollen toe aan de integratie gebruiker:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="next-steps"></a>Volgende stappen

* [Overzicht van ITSM-connector](itsmc-overview.md)
* [ITSM-werk items maken op basis van Azure-waarschuwingen](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Problemen oplossen in ITSM-connector](./itsmc-resync-servicenow.md)