---
title: De Connect Health-Agents in Azure Active Directory installeren
description: In dit Azure AD Connect Health artikel wordt de installatie van agents voor Active Directory Federation Services (AD FS) en voor synchronisatie beschreven.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/20/2020
ms.topic: how-to
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b680c275b92340cc7efba187769cb17602b08b45
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973283"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Installatie van Azure AD Connect Health-Agent

In dit artikel leert u hoe u de Azure Active Directory (Azure AD) Connect Health-Agents installeert en configureert. Zie [deze instructies](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent)voor het downloaden van de agents.

## <a name="requirements"></a>Vereisten

De volgende tabel bevat de vereisten voor het gebruik van Azure AD Connect Health.

| Vereiste | Beschrijving |
| --- | --- |
| Azure AD Premium is geïnstalleerd. |Azure AD Connect Health is een functie van Azure AD Premium. Zie [registreren voor Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)voor meer informatie. <br /><br />Als u een gratis proef versie van 30 dagen wilt starten, raadpleegt u [een proef versie starten](https://azure.microsoft.com/trial/get-started-active-directory/). |
| U bent een globale beheerder in azure AD. |Standaard kunnen alleen globale beheerders de Health-Agents installeren en configureren, de portal openen en bewerkingen uitvoeren in Azure AD Connect Health. Voor meer informatie raadpleegt u [Uw Azure AD-directory beheren](../fundamentals/active-directory-whatis.md). <br /><br /> Door gebruik te maken van op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure kunt u andere gebruikers in uw organisatie toegang geven tot Azure AD Connect Health. Zie [Azure RBAC voor Azure AD Connect Health](how-to-connect-health-operations.md#manage-access-with-azure-rbac)voor meer informatie. <br /><br />**Belang rijk**: gebruik een werk-of school account om de agents te installeren. U kunt geen Microsoft-account gebruiken. Zie [registreren voor Azure als organisatie](../fundamentals/sign-up-organization.md)voor meer informatie. |
| De Azure AD Connect Health-Agent wordt op elke doel server geïnstalleerd. | Health-Agents moeten worden geïnstalleerd en geconfigureerd op de doel servers, zodat ze gegevens kunnen ontvangen en bewakings-en analyse mogelijkheden bieden. <br /><br />Als u bijvoorbeeld gegevens wilt ophalen uit de infra structuur van uw Active Directory Federation Services (AD FS), moet u de agent installeren op de AD FS-server en de webtoepassingsproxy. Op dezelfde manier moet u de agent op de domein controllers installeren om gegevens op te halen van uw on-premises Azure AD Domain Services (Azure AD DS)-infra structuur.  |
| De eind punten van de Azure-service hebben een uitgaande verbinding. | Tijdens de installatie en runtime moet de agent verbonden zijn met de Azure AD Connect Health-service-eindpunten. Als firewalls uitgaande verbindingen blok keren, voegt u de [uitgaande connectiviteit-eind punten](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) toe aan de acceptatie lijst. |
|Uitgaande connectiviteit is gebaseerd op IP-adressen. | Zie [Azure IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653)voor informatie over het filteren van firewalls op basis van IP-adressen.|
| TLS-inspectie voor uitgaand verkeer wordt gefilterd of uitgeschakeld. | De agent registratie stap of het uploaden van gegevens kan mislukken als er TLS-inspectie of-beëindiging voor uitgaand verkeer op de netwerklaag is. Zie [TLS-inspectie instellen](/previous-versions/tn-archive/ee796230(v=technet.10))voor meer informatie. |
| De agent wordt uitgevoerd op de firewall poorten op de server. |De agent moet de volgende firewall poorten open hebben zodat deze kan communiceren met de Azure AD Connect Health Service-eind punten: <br /><li>TCP-poort 443</li><li>TCP-poort 5671</li> <br />Voor de nieuwste versie van de agent is poort 5671 niet vereist. Voer een upgrade uit naar de nieuwste versie zodat alleen poort 443 is vereist. Zie voor meer informatie [hybride identiteit vereiste poorten en protocollen](./reference-connect-ports.md). |
| Als verbeterde beveiliging van Internet Explorer is ingeschakeld, kunt u opgegeven websites toestaan.  |Als verbeterde beveiliging van Internet Explorer is ingeschakeld, kunt u de volgende websites op de server waarop u de Agent installeert, toestaan:<br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https: \/ /aadcdn.msftauth.net</li><li>De Federatie server voor uw organisatie die wordt vertrouwd door Azure AD (bijvoorbeeld https: \/ /STS.contoso.com)</li> <br />Zie [How to configure Internet Explorer](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing)(Engelstalig) voor meer informatie. Als u een proxy in uw netwerk hebt, kunt u de notitie aan het einde van deze tabel weer geven.|
| Power shell-versie 4,0 of hoger is geïnstalleerd. | Windows Server 2012 bevat Power shell-versie 3,0. Deze versie is *niet* voldoende voor de agent.</br></br> Windows Server 2012 R2 en hoger bevatten een voldoende recente versie van Power shell.|
|FIPS (Federal Information Processing Standard) is uitgeschakeld.|Azure AD Connect Health-Agents bieden geen ondersteuning voor FIPS.|

> [!IMPORTANT]
> Windows Server Core biedt geen ondersteuning voor de installatie van de Azure AD Connect Health-Agent.


> [!NOTE]
> Als u een zeer vergrendelde en beperkte omgeving hebt, moet u meer Url's toevoegen dan de tabel lijsten voor verbeterde beveiliging van Internet Explorer. Voeg ook Url's toe die worden vermeld in de tabel in de volgende sectie.  


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Uitgaande verbinding met de Azure-service-eindpunten

Tijdens de installatie en runtime moet de agent verbinding hebben met Azure AD Connect Health Service-eind punten. Als firewalls uitgaande verbindingen blok keren, zorgt u ervoor dat de Url's in de volgende tabel niet standaard worden geblokkeerd. 

Schakel de beveiligings controle of inspectie van deze Url's niet uit. In plaats daarvan kunt u toestaan dat andere Internet verkeer wordt toegestaan. 

Met deze Url's kunt u communicatie met Azure AD Connect Health Service-eind punten toestaan. Verderop in dit artikel leert u hoe u de [uitgaande connectiviteit kunt controleren](#test-connectivity-to-azure-ad-connect-health-service) met behulp van `Test-AzureADConnectHealthConnectivity` .

| Domein omgeving | Vereiste Azure-service-eindpunten |
| --- | --- |
| Algemeen openbaar | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;. servicebus.windows.net-Port: 5671 (dit eind punt is niet vereist in de nieuwste versie van de agent.)</li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.Office.com (dit eind punt wordt alleen gebruikt voor detectie doeleinden tijdens de registratie.)</li> |
| Azure Duitsland | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https: \/ /www.Office.de (dit eind punt wordt alleen gebruikt voor detectie doeleinden tijdens de registratie.)</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.Office.com (dit eind punt wordt alleen gebruikt voor detectie doeleinden tijdens de registratie.)</li> |


## <a name="install-the-agent"></a>De agent installeren

De Azure AD Connect Health-Agent downloaden en installeren: 

* Zorg ervoor dat u voldoet aan de [vereisten](how-to-connect-health-agent-install.md#requirements) voor Azure AD Connect Health.
* Aan de slag met Azure AD Connect Health voor AD FS:
    * [Down load de Azure AD Connect Health-Agent voor AD FS](https://go.microsoft.com/fwlink/?LinkID=518973).
    * Zie de [installatie-instructies](#install-the-agent-for-ad-fs).
* Aan de slag met Azure AD Connect Health voor synchronisatie:
    * [Download en installeer de nieuwste versie van Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771). De Health-Agent voor synchronisatie is geïnstalleerd als onderdeel van de Azure AD Connect-installatie (versie 1.0.9125.0 of hoger).
* Aan de slag met Azure AD Connect Health voor Azure AD DS:
    * [Down load de Azure AD Connect Health Agent voor Azure AD DS](https://go.microsoft.com/fwlink/?LinkID=820540).
    * Zie de [installatie-instructies](#install-the-agent-for-azure-ad-ds).

## <a name="install-the-agent-for-ad-fs"></a>De agent voor AD FS installeren

> [!NOTE]
> Uw AD FS-server moet verschillen van de synchronisatie server. Installeer de AD FS-agent niet op de synchronisatie server.
>

Voordat u de Agent installeert, moet u ervoor zorgen dat de hostnaam van de AD FS server uniek is en niet aanwezig is in de AD FS-service.
Dubbel klik op het *exe* -bestand dat u hebt gedownload om de installatie van de agent te starten. Selecteer in het eerste venster **installeren**.

![Scherm opname van het installatie venster voor de Azure AD Connect Health AD FS agent.](./media/how-to-connect-health-agent-install/install1.png)

Nadat de installatie is voltooid, selecteert **u nu configureren**.

![Scherm afbeelding met het bevestigings bericht voor de installatie van de Azure AD Connect Health AD FS agent.](./media/how-to-connect-health-agent-install/install2.png)

Er wordt een Power shell-venster geopend om het registratie proces voor de agent te starten. Wanneer u hierom wordt gevraagd, meldt u zich aan met een Azure AD-account dat is gemachtigd om de agent te registreren. Het account van de globale beheerder heeft standaard machtigingen.

![Scherm opname van het aanmeldings venster voor Azure AD Connect Health AD FS.](./media/how-to-connect-health-agent-install/install3.png)

Nadat u zich hebt aangemeld, wordt Power shell voortgezet. Wanneer de service is voltooid, kunt u Power shell sluiten. De configuratie is voltooid.

Op dit moment moet de agent services automatisch worden gestart zodat de agent de vereiste gegevens veilig kan uploaden naar de Cloud service.

Als u niet alle vereisten hebt voldaan, worden er waarschuwingen weer gegeven in het Power shell-venster. Zorg ervoor dat u de [vereisten](how-to-connect-health-agent-install.md#requirements) voltooit voordat u de Agent installeert. De volgende scherm afbeelding toont een voor beeld van deze waarschuwingen.

![Scherm opname met de Azure AD Connect Health AD FS script configureren.](./media/how-to-connect-health-agent-install/install4.png)

Als u wilt controleren of de agent is geïnstalleerd, zoekt u de volgende services op de server. Als de configuratie is voltooid, moeten deze services worden uitgevoerd. Anders worden ze gestopt totdat de configuratie is voltooid.

* Azure AD Connect Health AD FS Diagnostics-service
* Azure AD Connect Health AD FS Insights-service
* Azure AD Connect Health AD FS Monitoring-service

![Scherm opname van Azure AD Connect Health AD FS Services.](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>Controle inschakelen voor AD FS

> [!NOTE]
> Deze sectie is alleen van toepassing op AD FS-servers. U hoeft deze stappen niet uit te voeren op de Web Application proxy-servers.
>

De functie voor gebruiks analyse moet gegevens verzamelen en analyseren. Daarom heeft de Azure AD Connect Health-Agent de informatie nodig in de AD FS controle Logboeken. Deze logboeken zijn niet standaard ingeschakeld. Gebruik de volgende procedures om AD FS controle in te scha kelen en de AD FS controle logboeken op uw AD FS-servers te vinden.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Controle inschakelen voor AD FS in Windows Server 2012 R2

1. Open **Serverbeheer** op het Start scherm en open vervolgens **lokaal beveiligings beleid**. Open **Serverbeheer** op de taak balk en selecteer vervolgens **extra/lokaal beveiligings beleid**.
2. Ga naar de map *beveiligings-Beleid\toewijzing rechten toewijzen* . Dubbel klik vervolgens op **beveiligings controle genereren**.
3. Controleer op het tabblad **Lokale beveiligingsinstelling** of het AD FS-serviceaccount wordt vermeld. Als deze niet wordt weer gegeven, selecteert u **gebruiker of groep toevoegen** en voegt u deze toe aan de lijst. Selecteer vervolgens **OK**.
4. Als u controle wilt inschakelen, opent u een opdracht prompt venster met verhoogde bevoegdheden. Voer vervolgens de volgende opdracht uit: 
    
    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Kies **Lokaal beveiligingsbeleid**.
    >[!Important]
    >De volgende stappen zijn alleen vereist voor primaire AD FS-servers. 
1. Open de module **AD FS-beheer**. (Selecteer in **Serverbeheer** **extra**  >  **AD FS beheer**.)
1. Selecteer in het deel venster **acties** de optie **Federation service eigenschappen bewerken**.
1. Selecteer in het dialoog venster **Eigenschappen van Federation service** het tabblad **gebeurtenissen** .
1. Selecteer de selectie vakjes **geslaagde controles en mislukte** controles en selecteer vervolgens **OK**.
1. Als u uitgebreide logboek registratie via Power shell wilt inschakelen, gebruikt u de volgende opdracht: 

    `Set-AdfsProperties -LOGLevel Verbose`

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Controle inschakelen voor AD FS in Windows Server 2016

1. Open **Serverbeheer** op het Start scherm en open vervolgens **lokaal beveiligings beleid**. Open **Serverbeheer** op de taak balk en selecteer vervolgens **extra/lokaal beveiligings beleid**.
2. Ga naar de map *Security-Beleid\toewijzing Rights Assignment* en dubbel klik vervolgens op **beveiligings controle genereren**.
3. Controleer op het tabblad **Lokale beveiligingsinstelling** of het AD FS-serviceaccount wordt vermeld. Als deze niet wordt weer gegeven, selecteert u **gebruiker of groep toevoegen** en voegt u de AD FS-service account toe aan de lijst. Selecteer vervolgens **OK**.
4. Als u controle wilt inschakelen, opent u een opdracht prompt venster met verhoogde bevoegdheden. Voer vervolgens de volgende opdracht uit: 

    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Kies **Lokaal beveiligingsbeleid**.
    >[!Important]
    >De volgende stappen zijn alleen vereist voor primaire AD FS-servers.
1. Open de module **AD FS-beheer**. (Selecteer in **Serverbeheer** **extra**  >  **AD FS beheer**.)
1. Selecteer in het deel venster **acties** de optie **Federation service eigenschappen bewerken**.
1. Selecteer in het dialoog venster **Eigenschappen van Federation service** het tabblad **gebeurtenissen** .
1. Selecteer de selectie vakjes **geslaagde controles en mislukte** controles en selecteer vervolgens **OK**. Geslaagde controles en mislukte controles moeten standaard worden ingeschakeld.
1. Open een Power shell-venster en voer de volgende opdracht uit: 

    `Set-AdfsProperties -AuditLevel Verbose`

Het Basic-controle niveau is standaard ingeschakeld. Zie [AD FS controle verbetering in Windows Server 2016](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server)voor meer informatie.


#### <a name="to-locate-the-ad-fs-audit-logs"></a>De AD FS-auditlogboeken zoeken

1. Open **Logboeken**.
2. Ga naar **Windows-logboeken** en selecteer vervolgens **beveiliging**.
3. Selecteer aan de rechter kant **filter huidige logboeken**.
4. Selecteer **AD FS controle** voor **gebeurtenis bronnen**.

    Zie [Operations questions](reference-connect-health-faq.md#operations-questions)(Engelstalig) voor meer informatie over audit Logboeken.

    ![Scherm opname van het venster huidig logboek filteren. In het veld gebeurtenis bronnen is AD FS controle is geselecteerd.](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> Met een groepsbeleid kan AD FS-controle worden uitgeschakeld. Als AD FS controle is uitgeschakeld, zijn gebruiks analyses over aanmeldings activiteiten niet beschikbaar. Zorg ervoor dat er geen groeps beleid is waarmee AD FS controle wordt uitgeschakeld.
>


## <a name="install-the-agent-for-sync"></a>De agent voor synchronisatie installeren

De Azure AD Connect Health-Agent voor synchronisatie wordt automatisch geïnstalleerd in de meest recente versie van Azure AD Connect. Als u Azure AD Connect voor synchronisatie wilt gebruiken, [downloadt u de meest recente versie van Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) en installeert u deze.

Als u wilt controleren of de agent is geïnstalleerd, zoekt u de volgende services op de server. Als u de configuratie hebt voltooid, zijn de Services al actief. Anders worden de services gestopt totdat de configuratie is voltooid.

* Azure AD Connect Health Sync Insights-service
* Azure AD Connect Health Sync Monitoring-service

![Scherm afbeelding met de actieve Azure AD Connect Health voor synchronisatie Services op de server.](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Houd er rekening mee dat u Azure AD Premium moet hebben om Azure AD Connect Health te gebruiken. Als u niet beschikt over Azure AD Premium, kunt u de configuratie in de Azure Portal niet volt ooien. Zie de [vereisten](how-to-connect-health-agent-install.md#requirements)voor meer informatie.
>
>

## <a name="manually-register-azure-ad-connect-health-for-sync"></a>Azure AD Connect Health voor synchronisatie hand matig registreren

Als de Azure AD Connect Health voor de registratie van de synchronisatie agent mislukt nadat u Azure AD Connect hebt geïnstalleerd, kunt u een Power shell-opdracht gebruiken om de agent hand matig te registreren.

> [!IMPORTANT]
> Gebruik deze Power shell-opdracht alleen als de registratie van de agent mislukt nadat u Azure AD Connect hebt geïnstalleerd.
>
>

Registreer de Azure AD Connect Health Agent hand matig voor synchronisatie met behulp van de volgende Power shell-opdracht. De Azure AD Connect Health-services worden niet gestart nadat de agent is geregistreerd.

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Voor de opdracht worden de volgende parameters gebruikt:

* **AttributeFiltering**: `$true` (standaard) als Azure AD Connect de standaardkenmerkset niet synchroniseert en is aangepast voor het gebruik van een gefilterde kenmerkset. Gebruik anders `$false` .
* **StagingMode**: `$false` (standaard) als de Azure AD Connect-server zich *niet* in de faserings modus bevindt. Als de server is geconfigureerd om te worden uitgevoerd in de faserings modus, gebruikt u `$true` .

Wanneer u wordt gevraagd om verificatie, gebruikt u hetzelfde globale beheerders account (zoals admin@domain.onmicrosoft.com ) dat u hebt gebruikt om Azure AD Connect te configureren.

## <a name="install-the-agent-for-azure-ad-ds"></a>De agent voor Azure AD DS installeren

Dubbel klik op het *exe* -bestand dat u hebt gedownload om de installatie van de agent te starten. Selecteer in het eerste venster **installeren**.

![Scherm afbeelding van het installatie venster Azure AD Connect Health Agent voor AD DS.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

Wanneer de installatie is voltooid, selecteert **u nu configureren**.

![Scherm opname van het venster waarin de installatie van de Azure AD Connect Health Agent voor Azure AD DS is voltooid.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

Er wordt een opdracht prompt venster geopend. Power shell wordt uitgevoerd `Register-AzureADConnectHealthADDSAgent` . Wanneer u wordt gevraagd, meldt u zich aan bij Azure.

![Scherm opname van het aanmeldings venster voor de Azure AD Connect Health Agent voor Azure AD DS.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

Nadat u zich hebt aangemeld, wordt Power shell voortgezet. Wanneer de service is voltooid, kunt u Power shell sluiten. De configuratie is voltooid.

Op dit moment moeten de services automatisch worden gestart, zodat de agent gegevens kan bewaken en verzamelen. Als u niet voldoet aan alle vereisten die in de vorige secties zijn beschreven, worden er waarschuwingen weer gegeven in het Power shell-venster. Zorg ervoor dat u de [vereisten](how-to-connect-health-agent-install.md#requirements) voltooit voordat u de Agent installeert. De volgende scherm afbeelding toont een voor beeld van deze waarschuwingen.

![Scherm opname met een waarschuwing voor de configuratie van de Azure AD Connect Health Agent voor Azure AD DS.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

Als u wilt controleren of de agent is geïnstalleerd, zoekt u de volgende services op de domein controller:

* Azure AD Connect Health AD DS Insights-service
* Azure AD Connect Health AD DS Monitoring-service

Als de configuratie is voltooid, worden deze services als het goed is al uitgevoerd. Anders worden ze gestopt tot de configuratie is voltooid.

![Scherm afbeelding met de actieve services op de domein controller.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quickly-install-the-agent-on-multiple-servers"></a>De agent snel op meerdere servers installeren

1. Maak een gebruikers account in azure AD. Beveilig het met een wacht woord.
2. Wijs de rol van **eigenaar** voor dit lokale Azure ad-account in azure AD Connect Health toe met behulp van de portal. Volg [deze stappen](how-to-connect-health-operations.md#manage-access-with-azure-rbac). Wijs de rol toe aan alle service-exemplaren. 
3. Down load het *exe* MSI-bestand in de lokale domein controller voor de installatie.
4. Voer het volgende script uit. Vervang de para meters door uw nieuwe gebruikers account en het bijbehorende wacht woord. 

    ```powershell
    AdHealthAddsAgentSetup.exe /quiet
    Start-Sleep 30
    $userName = "NEWUSER@DOMAIN"
    $secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
    $myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
    import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
     
    Register-AzureADConnectHealthADDSAgent -Credential $myCreds
    
    ```

Wanneer u klaar bent, kunt u de toegang tot het lokale account verwijderen door een of meer van de volgende taken uit te voeren: 
* Verwijder de roltoewijzing voor het lokale account voor Azure AD Connect Health.
* Het wacht woord voor het lokale account draaien. 
* Schakel het lokale Azure AD-account uit.
* Verwijder het lokale Azure AD-account.  

## <a name="register-the-agent-by-using-powershell"></a>De agent registreren met behulp van Power shell

Nadat u het juiste agent *setup.exe* -bestand hebt geïnstalleerd, kunt u de agent registreren met behulp van de volgende Power shell-opdrachten, afhankelijk van de rol. Open een Power shell-venster en voer de juiste opdracht uit:

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

Deze opdrachten accepteren `Credential` als een para meter voor het niet interactief volt ooien van de registratie of voor het volt ooien van de registratie op een computer waarop Server kernen worden uitgevoerd. Denk hierbij aan het volgende:
* U kunt vastleggen `Credential` in een Power shell-variabele die wordt door gegeven als een para meter.
* U kunt een Azure AD-identiteit opgeven die machtigingen heeft voor het registreren van de agents en waarvoor *geen* multi-factor Authentication is ingeschakeld.
* Standaard hebben globale beheerders machtigingen voor het registreren van de agents. U kunt ook de identiteiten met minder privileges toestaan om deze stap uit te voeren. Zie [Azure RBAC](how-to-connect-health-operations.md#manage-access-with-azure-rbac)voor meer informatie.

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Azure AD Connect Health agents configureren voor het gebruik van HTTP-proxy

U kunt Azure AD Connect Health agents configureren voor gebruik met een HTTP-proxy.

> [!NOTE]
> * `Netsh WinHttp set ProxyServerAddress` wordt niet ondersteund. De agent gebruikt System.Net in plaats van Windows HTTP-Services om webaanvragen te maken.
> * Het geconfigureerde HTTP-proxy adres wordt gebruikt om versleutelde HTTPS-berichten door te geven.
> * Proxy’s die zijn geverifieerd (met HTTPBasic), worden niet ondersteund.
>
>

### <a name="change-the-agent-proxy-configuration"></a>De configuratie van de agent proxy wijzigen

Als u de Azure AD Connect Health-Agent voor het gebruik van een HTTP-proxy wilt configureren, kunt u het volgende doen:
* Bestaande proxy-instellingen importeren.
* Geef proxy adressen hand matig op.
* Wis de bestaande proxy configuratie.

> [!NOTE]
> Als u de proxy-instellingen wilt bijwerken, moet u alle Azure AD Connect Health Agent-services opnieuw starten. Voer de volgende opdracht uit:
>
> `Restart-Service AzureADConnectHealth*`

#### <a name="import-existing-proxy-settings"></a>Bestaande proxy-instellingen importeren

U kunt de HTTP-proxy-instellingen van Internet Explorer importeren, zodat de Azure AD Connect Health-Agents de instellingen kunnen gebruiken. Voer de volgende Power shell-opdracht uit op elk van de servers waarop de Health-Agent wordt uitgevoerd:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

U kunt WinHTTP-proxy-instellingen importeren, zodat de Azure AD Connect Health Agent deze kunnen gebruiken. Voer de volgende Power shell-opdracht uit op elk van de servers waarop de Health-Agent wordt uitgevoerd:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>Proxy adressen hand matig opgeven

U kunt hand matig een proxy server opgeven. Voer de volgende Power shell-opdracht uit op elk van de servers waarop de Health-Agent wordt uitgevoerd:

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

Hier volgt een voorbeeld: 

`Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443`

In dit voorbeeld geldt het volgende:
* De `address` instelling kan een DNS-herstel bare server naam of een IPv4-adres zijn.
* U kunt weglaten `port` . Als u dit doet, is 443 de standaard poort.

#### <a name="clear-the-existing-proxy-configuration"></a>De bestaande proxy configuratie wissen

U kunt de bestaande proxyconfiguratie wissen door de volgende opdracht uit te voeren:

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>De huidige proxyinstellingen lezen

U kunt de huidige proxy-instellingen lezen door de volgende opdracht uit te voeren:

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Verbinding met Azure AD Connect Health-Service testen

In sommige gevallen kan de Azure AD Connect Health-Agent de verbinding met de Azure AD Connect Health-Service verliezen. Oorzaken van dit verbindings verlies kunnen netwerk problemen, machtigings problemen en verschillende andere problemen zijn.

Als de agent gedurende meer dan twee uur geen gegevens kan verzenden naar de Azure AD Connect Health-Service, wordt de volgende waarschuwing weer gegeven in de portal: ' Health Service gegevens zijn niet bijgewerkt. ' 

U kunt nagaan of de betrokken Azure AD Connect Health Agent gegevens kan uploaden naar de Azure AD Connect Health-Service door de volgende Power shell-opdracht uit te voeren:

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

De rolparameter heeft momenteel de volgende waarden:

* ADFS
* Synchroniseren
* ADDS

> [!NOTE]
> Als u het connectiviteits hulpprogramma wilt gebruiken, moet u de agent eerst registreren. Als u de registratie van de agent niet kunt volt ooien, moet u ervoor zorgen dat u aan alle [vereisten](how-to-connect-health-agent-install.md#requirements) voor Azure AD Connect Health hebt voldaan. De connectiviteit wordt standaard getest tijdens de registratie van de agent.
>
>

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende verwante artikelen:

* [Azure AD Connect Health (Engelstalig)](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health bewerkingen](how-to-connect-health-operations.md)
* [Azure AD Connect Health gebruiken met AD FS](how-to-connect-health-adfs.md)
* [Azure AD Connect Health for Sync gebruiken](how-to-connect-health-sync.md)
* [Azure AD Connect Health met Azure AD DS gebruiken](how-to-connect-health-adds.md)
* [Veelgestelde vragen over Azure AD Connect Health](reference-connect-health-faq.md)
* [Versiegeschiedenis van Azure AD Connect Health](reference-connect-health-version-history.md)
