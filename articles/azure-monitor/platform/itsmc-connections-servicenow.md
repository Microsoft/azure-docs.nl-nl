---
title: ServiceNow verbinden met IT Service Management-connector
description: Informatie over het verbinden van ServiceNow met de IT Service Management-connector (ITSMC) in Azure Monitor om ITSM-werk items centraal te controleren en te beheren.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 7d1b4b3542f6914d413a5e29e57baa15e7a53346
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012781"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>ServiceNow verbinden met IT Service Management-connector

In dit artikel wordt beschreven hoe u de verbinding tussen een ServiceNow-exemplaar en de IT Service Management-connector (ITSMC) configureert in Log Analytics, zodat u uw werk items van uw IT-Service beheer (ITSM) centraal kunt beheren.

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat u voldoet aan de volgende vereisten voor de verbinding.

### <a name="itsmc-installation"></a>ITSMC-installatie

Zie [de IT Service Management-connector-oplossing toevoegen](./itsmc-definition.md#add-it-service-management-connector)voor meer informatie over het installeren van ITSMC.

> [!NOTE]
> ITSMC ondersteunt alleen de officiële software as a Service (SaaS)-aanbieding van ServiceNow. Persoonlijke implementaties van ServiceNow worden niet ondersteund.

### <a name="oauth-setup"></a>OAuth-installatie

ServiceNow ondersteunde versies zijn Orlando, New York, Madrid, Londen, Kingston, Jakarta, Istanboel, Helsinki en Genève.

ServiceNow-beheerders moeten een client-ID en client geheim genereren voor hun ServiceNow-exemplaar. Bekijk de volgende informatie zoals vereist:

- [OAuth instellen voor Orlando](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [OAuth voor New York instellen](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [OAuth instellen voor Madrid](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [OAuth instellen voor Londen](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [OAuth instellen voor Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [OAuth instellen voor Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [OAuth voor Istanboel instellen](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [OAuth instellen voor Helsinki](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [OAuth instellen voor Genève](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)

Als onderdeel van het instellen van OAuth, raden we het volgende aan:

1. [Een eind punt voor clients maken voor toegang tot het exemplaar](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html).

1. De levens duur van het vernieuwings token bijwerken:

   1. Zoek in het deel venster **ServiceNow** naar **systeem OAuth** en selecteer vervolgens **toepassings register**. 
   1. Selecteer de naam van de opgegeven OAuth en wijzig de duur van het **vernieuwings token** in **7.776.000 seconden** (90 dagen). 
   1. Selecteer **Bijwerken**. 

1. Stel een interne procedure in om ervoor te zorgen dat de verbinding actief blijft. Een paar dagen vóór de verwachte verval datum van de levens duur van het vernieuwings token voert u de volgende bewerkingen uit:

   1. [Voer een hand matig synchronisatie proces uit voor de configuratie van de ITSM-connector](./itsmc-resync-servicenow.md).

   1. Intrekken naar het oude vernieuwings token. Het wordt niet aangeraden oude sleutels uit veiligheids overwegingen te bewaren. 
   
      1. Zoek in het deel venster **ServiceNow** naar **systeem OAuth** en selecteer vervolgens **tokens beheren**. 
      
      1. Selecteer in de lijst het oude token volgens de OAuth-naam en de verval datum.

         ![Scherm opname waarin een lijst met tokens voor OAuth wordt weer gegeven.](media/itsmc-connections/snow-system-oauth.png)
      1. Selecteer intrekken **toegang**  >  **intrekken**.

## <a name="install-the-user-app-and-create-the-user-role"></a>De gebruikers-app installeren en de gebruikersrol maken

Gebruik de volgende procedure om de-app nu te installeren en de gebruikersrol voor integratie te maken. U gebruikt deze referenties om de ServiceNow-verbinding in azure te maken.

> [!NOTE]
> ITSMC ondersteunt alleen de officiële gebruikers-app voor de integratie van micro soft Log Analytics die wordt gedownload uit de ServiceNow-opslag. ITSMC biedt geen ondersteuning voor code opname aan de ServiceNow-zijde of een toepassing die geen deel uitmaakt van de officiële ServiceNow-oplossing. 

1. Ga naar de [ServiceNow Store](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) en installeer de **gebruikers-app voor ServiceNow en micro soft OMS-integratie** in uw ServiceNow-exemplaar.
   
   >[!NOTE]
   >Als onderdeel van de lopende overgang van Microsoft Operations Management Suite (OMS) naar Azure Monitor, wordt OMS nu Log Analytics genoemd.     
2. Na de installatie gaat u naar de linkernavigatiebalk van het ServiceNow-exemplaar en zoekt u naar **micro soft OMS integrator** en selecteert u deze.  
3. Selecteer **controle lijst voor installatie**.

   De status wordt weer gegeven als  **niet voltooid** omdat de gebruikersrol nog niet is gemaakt.

4. Voer in het tekstvak naast **integratie gebruiker maken** de naam in voor de gebruiker die verbinding kan maken met ITSMC in Azure.
5. Voer het wacht woord voor deze gebruiker in en selecteer **OK**.  

De nieuw gemaakte gebruiker wordt weer gegeven met de standaard rollen toegewezen:

- personalize_choices
- import_transformer
- x_mioms_microsoft. gebruiker
- ITIL
- template_editor
- view_changer

Nadat u de gebruiker hebt gemaakt, wordt de status van de controle lijst voor het controleren van de **installatie** naar voltooid verplaatst en worden de details weer gegeven van de gebruikersrol die **is** gemaakt voor de app.

> [!NOTE]
> ITSMC kan incidenten verzenden naar ServiceNow zonder dat er andere modules zijn geïnstalleerd op uw ServiceNow-exemplaar. Als u de EventManagement-module in uw ServiceNow-exemplaar gebruikt en u gebeurtenissen of waarschuwingen in ServiceNow wilt maken met behulp van de connector, voegt u de volgende rollen toe aan de integratie gebruiker:
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## <a name="create-a-connection"></a>Een verbinding maken
Gebruik de volgende procedure om een ServiceNow-verbinding te maken.

> [!NOTE]
> De waarschuwingen die worden verzonden vanuit Azure Monitor kunnen een van de volgende elementen maken in ServiceNow: gebeurtenissen, incidenten of waarschuwingen. 

1. Ga in Azure Portal naar **alle resources** en zoek naar **Service Desk (YourWorkspaceName)**.

2. Selecteer **ITSM-verbindingen** onder **gegevens bronnen voor werk ruimte**.

   ![Scherm opname van de selectie van een gegevens bron.](media/itsmc-connections/add-new-itsm-connection.png)

3. Selecteer aan de bovenkant van het rechterdeel venster de optie **toevoegen**.

4. Geef de informatie op zoals beschreven in de volgende tabel en selecteer vervolgens **OK**.

   | **Veld** | **Beschrijving** |
   | --- | --- |
   | **Verbindingsnaam**   | Voer een naam in voor het ServiceNow-exemplaar dat u wilt verbinden met ITSMC. U gebruikt deze naam later in Log Analytics wanneer u ITSM-werk items configureert en gedetailleerde analyses weergeeft. |
   | **Partnertype**   | Selecteer **ServiceNow**. |
   | **Server-URL**   | Voer de URL in van het ServiceNow-exemplaar dat u wilt verbinden met ITSMC. De URL moet verwijzen naar een ondersteunde SaaS-versie met het achtervoegsel *. servicenow.com*.|
   | **Gebruikersnaam**   | Voer de gebruikers naam van de integratie in die u hebt gemaakt in de ServiceNow-app om de verbinding met ITSMC te ondersteunen.|
   | **Wachtwoord**   | Voer het wacht woord in dat is gekoppeld aan deze gebruikers naam. **Opmerking**: de gebruikers naam en het wacht woord worden alleen gebruikt voor het genereren van verificatie tokens. Ze worden nergens opgeslagen in de ITSMC-service.  |
   | **Client-id**   | Voer de client-ID in die u wilt gebruiken voor OAuth2-verificatie, die u eerder hebt gegenereerd. Zie [OAuth instellen](https://wiki.servicenow.com/index.php?title=OAuth_Setup)voor meer informatie over het genereren van een client-id en een geheim. |
   | **Clientgeheim**   | Voer het client geheim in dat voor deze ID is gegenereerd.   |
   | **Bereik van gegevens synchronisatie (in dagen)** | Voer het aantal afgelopen dagen in waaruit u de gegevens wilt. De limiet is 120 dagen. |
   | **Te synchroniseren werk items**   | Selecteer de ServiceNow-werk items die u wilt synchroniseren met Azure Log Analytics via ITSMC. De geselecteerde waarden worden in Log Analytics geïmporteerd. Opties zijn incidenten en wijzigings aanvragen.|
   | **Nieuw configuratie-item maken in ITSM-product** | Selecteer deze optie als u de configuratie-items wilt maken in het ITSM-product. Wanneer deze is geselecteerd, maakt ITSMC configuratie-items (als er geen bestaat) in het ondersteunde ITSM-systeem. Het is standaard uitgeschakeld. |

![Scherm afbeelding van vakken en opties voor het toevoegen van een ServiceNow-verbinding.](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

Wanneer u verbinding hebt gemaakt en gesynchroniseerd:

- Geselecteerde werk items uit het ServiceNow-exemplaar worden geïmporteerd in Log Analytics. U kunt de samen vatting van deze werk items weer geven op de tegel **IT Service Management-connector** .

- U kunt incidenten maken op basis van Log Analytics-waarschuwingen of logboek records of vanuit Azure-waarschuwingen in dit ServiceNow-exemplaar.

> [!NOTE]
> ServiceNow heeft een frequentie limiet voor aanvragen per uur. Als u de limiet wilt configureren, definieert u **binnenkomende rest API frequentie beperking** in het ServiceNow-exemplaar.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van ITSM-connector](itsmc-overview.md)
* [ITSM-werk items maken op basis van Azure-waarschuwingen](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Problemen oplossen in de ITSM-connector](./itsmc-resync-servicenow.md)