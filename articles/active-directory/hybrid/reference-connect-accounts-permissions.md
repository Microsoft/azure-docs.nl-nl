---
title: 'Azure AD Connect: accounts en machtigingen | Microsoft Docs'
description: In dit onderwerp wordt beschreven welke accounts worden gebruikt en gemaakt en welke machtigingen vereist zijn.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f7466931537745fb188a3bdb05646bff19912e8
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466249"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: accounts en machtigingen

## <a name="accounts-used-for-azure-ad-connect"></a>Accounts die worden gebruikt voor Azure AD Connect

![overzicht van accounts](media/reference-connect-accounts-permissions/account5.png)

Azure AD Connect maakt gebruik van 3 accounts om gegevens van on-premises of Windows Server-Active Directory te synchroniseren met Azure Active Directory.  Deze accounts zijn:

- **AD DS-Connector account**: wordt gebruikt voor het lezen/schrijven van informatie naar Windows Server Active Directory

- **ADSync-service account**: wordt gebruikt om de synchronisatie service uit te voeren en toegang te krijgen tot de SQL database

- **Azure AD-Connector account**: wordt gebruikt voor het schrijven van gegevens naar Azure AD

Naast deze drie accounts die worden gebruikt om Azure AD Connect uit te voeren, hebt u ook de volgende extra accounts nodig om Azure AD Connect te installeren.  Deze zijn:

- **Lokaal beheerders account**: de beheerder die Azure AD Connect installeert en lokale beheerders machtigingen heeft op de computer.

- **AD DS Enter prise-beheerders account**: wordt optioneel gebruikt om het AD DS-Connector account te maken.

- **Azure AD Global Administrator-account**: wordt gebruikt voor het maken van het Azure AD-Connector account en het configureren van Azure AD.  U kunt globale beheerders accounts weer geven in de Azure Portal.  Zie [toewijzingen van Azure AD-rollen weer geven](../../active-directory/roles/view-assignments.md).

- **SQL-SA-account (optioneel)**: wordt gebruikt voor het maken van de ADSync-data base wanneer de volledige versie van SQL Server wordt gebruikt.  Deze SQL Server kan lokaal of extern zijn voor de Azure AD Connect installatie.  Dit account kan hetzelfde account zijn als de ondernemings beheerder.  Het inrichten van de data base kan nu buiten de band worden uitgevoerd door de SQL-beheerder en vervolgens worden geïnstalleerd door de beheerder van de Azure AD Connect met de rechten van de data base-eigenaar.  Zie voor meer informatie [installeren Azure AD Connect met behulp van SQL delegated Administrator Permissions](how-to-connect-install-sql-delegation.md)


>[!IMPORTANT]
> Vanaf build 1.4. # # #. # het is niet meer mogelijk om een ondernemings Administrator of een domein beheerders account te gebruiken als AD DS Connector-account.  Als u probeert een account in te voeren dat een ondernemings beheerder of domein beheerder is wanneer u **bestaand account gebruiken** opgeeft, wordt er een fout bericht weer gegeven.

> [!NOTE]
> Het wordt ondersteund voor het beheren van de beheer accounts die worden gebruikt in Azure AD Connect vanuit een ESAE-beheer forest (ook wel bekend als ' rode forest ').
> Speciale beheerforests bieden organisaties de mogelijkheid om administratoraccounts, -werkstations en -groepen te hosten in een omgeving die sterkere beveiligingsmechanismen heeft dan de productieomgeving.
> Raadpleeg voor meer informatie over specifieke administratieve forests de [ontwerp benadering ESAE administratieve forest](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach).

> [!NOTE]
> De rol van globale beheerder is niet vereist na de eerste installatie en het account van de **Directory Synchronization accounts** wordt door de rol Dit betekent niet noodzakelijkerwijs dat u het account alleen wilt verwijderen met de rol van globale beheerder. Het is beter om de rol te wijzigen in een minder krachtige rol, omdat het volledig verwijderen van het account kan leiden tot problemen als u de wizard ooit opnieuw moet uitvoeren. Door de bevoegdheid van de rol te beperken, kunt u de bevoegdheden altijd opnieuw verhogen als u de wizard Azure AD Connect opnieuw moet gebruiken. 

## <a name="installing-azure-ad-connect"></a>Azure AD Connect installeren
De installatie wizard van Azure AD Connect biedt twee verschillende paden:

* In Express-instellingen vereist de wizard meer bevoegdheden.  Dit betekent dat de configuratie eenvoudig kan worden ingesteld, zonder dat u gebruikers hoeft te maken of machtigingen hoeft te configureren.
* In aangepaste instellingen biedt de wizard u meer keuzen en opties. Er zijn echter enkele situaties waarin u moet controleren of u zelf de juiste machtigingen hebt.



## <a name="express-settings-installation"></a>Installatie van snelle instellingen
In snelle instellingen vraagt de installatie wizard om het volgende:

  - Beheerders referenties van de onderneming AD DS
  - Globale beheerders referenties voor Azure AD

### <a name="ad-ds-enterprise-admin-credentials"></a>AD DS referenties voor ondernemings Administrator
Het AD DS Enter prise-beheerders account wordt gebruikt voor het configureren van uw on-premises Active Directory. Deze referenties worden alleen gebruikt tijdens de installatie en worden niet gebruikt nadat de installatie is voltooid. De ondernemings beheerder en niet de domein beheerder moeten controleren of de machtigingen in Active Directory in alle domeinen kunnen worden ingesteld.

Als u een upgrade uitvoert van DirSync, worden de AD DS beheerders referenties voor ondernemingen gebruikt om het wacht woord opnieuw in te stellen voor het account dat wordt gebruikt door DirSync. U hebt ook globale beheerders referenties voor Azure AD nodig.

### <a name="azure-ad-global-admin-credentials"></a>Globale beheerders referenties voor Azure AD
Deze referenties worden alleen gebruikt tijdens de installatie en worden niet gebruikt nadat de installatie is voltooid. Het wordt gebruikt voor het maken van het Azure AD-Connector account dat wordt gebruikt voor het synchroniseren van wijzigingen in azure AD. Het account maakt ook synchronisatie mogelijk als een functie in azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>AD DS Connector account vereiste machtigingen voor snelle instellingen
Het account voor de AD DS-connector is gemaakt voor het lezen van en schrijven naar Windows Server AD en heeft de volgende machtigingen wanneer deze worden gemaakt door snelle instellingen:

| Machtiging | Gebruikt voor |
| --- | --- |
| <li>Directory wijzigingen repliceren</li><li>Wijzigingen in Directory repliceren |Wachtwoord-hash-synchronisatie |
| Alle eigenschappen van lezen/schrijven gebruiker |Hybride importeren en uitwisselen |
| Alle eigenschappen lezen/schrijven iNetOrgPerson |Hybride importeren en uitwisselen |
| Alle eigenschappen groep lezen/schrijven |Hybride importeren en uitwisselen |
| Contact persoon voor alle eigenschappen lezen/schrijven |Hybride importeren en uitwisselen |
| Wachtwoord opnieuw instellen |Voor bereiding voor het inschakelen van wacht woord terugschrijven |

### <a name="express-installation-wizard-summary"></a>Samen vatting van de wizard Snelle installatie

![Snelle installatie](./media/reference-connect-accounts-permissions/express.png)

Hier volgt een samen vatting van de pagina's van de wizard Snelle installatie, de referenties die zijn verzameld en waarvoor ze worden gebruikt.

| Wizard pagina | Verzamelde referenties | Vereiste machtigingen | Gebruikt voor |
| --- | --- | --- | --- |
| N.v.t. |Gebruiker die de installatie wizard uitvoert |Beheerder van de lokale server |<li>Hiermee maakt u het ADSync-service account dat wordt gebruikt om de synchronisatie service uit te voeren. |
| Verbinding maken met Azure AD |Referenties voor Azure AD-Directory |Rol van globale beheerder in azure AD |<li>Synchronisatie inschakelen in de Azure AD-adres lijst.</li>  <li>Het maken van het Azure AD-Connector account dat wordt gebruikt voor voortdurende synchronisatie bewerkingen in azure AD.</li> |
| Verbinding maken met AD DS |Referenties voor on-premises Active Directory |Lid van de groep Ondernemings Administrators (EA) in Active Directory |<li>Hiermee maakt u het AD DS Connector-account in Active Directory en verleent u er machtigingen voor. Dit gemaakte account wordt gebruikt voor het lezen en schrijven van Directory gegevens tijdens de synchronisatie.</li> |


## <a name="custom-installation-settings"></a>Aangepaste installatie-instellingen

Met de installatie van aangepaste instellingen biedt de wizard u meer keuzen en opties. 

### <a name="custom-installation-wizard-summary"></a>Samen vatting van aangepaste installatie wizard

Hier volgt een samen vatting van de pagina's van de wizard Aangepaste installatie, de referenties die zijn verzameld en waarvoor ze worden gebruikt.

![Scherm afbeelding van de wizard pagina's met aangepaste installatie.](./media/reference-connect-accounts-permissions/customize.png)

| Wizard pagina | Verzamelde referenties | Vereiste machtigingen | Gebruikt voor |
| --- | --- | --- | --- |
| N.v.t. |Gebruiker die de installatie wizard uitvoert |<li>Beheerder van de lokale server</li><li>Als u een volledige SQL Server gebruikt, moet de gebruiker systeem beheerder (SA) in SQL zijn</li> |Maakt standaard het lokale account dat wordt gebruikt als service account voor de synchronisatie-engine. Het account wordt alleen gemaakt wanneer de beheerder geen bepaald account opgeeft. |
| Synchronisatie Services installeren, Service account optie |Referenties voor het AD-of lokale gebruikers account |Gebruiker, machtigingen worden verleend door de installatie wizard |Als de beheerder een account opgeeft, wordt dit account gebruikt als service account voor de synchronisatie service. |
| Verbinding maken met Azure AD |Referenties voor Azure AD-Directory |Rol van globale beheerder in azure AD |<li>Synchronisatie inschakelen in de Azure AD-adres lijst.</li>  <li>Het maken van het Azure AD-Connector account dat wordt gebruikt voor voortdurende synchronisatie bewerkingen in azure AD.</li> |
| Verbinding maken met uw directory’s |On-premises Active Directory referenties voor elk forest dat is verbonden met Azure AD |De machtigingen zijn afhankelijk van de functies die u inschakelt en vindt u in het AD DS Connector-account maken |Dit account wordt gebruikt voor het lezen en schrijven van Directory gegevens tijdens de synchronisatie. |
| AD FS-Servers |Voor elke server in de lijst worden referenties door de wizard verzameld wanneer de aanmeldings referenties van de gebruiker die de wizard uitvoert, onvoldoende zijn om verbinding te maken |Domein beheerder |Installatie en configuratie van de AD FS serverrol. |
| Web Application proxy-servers |Voor elke server in de lijst worden referenties door de wizard verzameld wanneer de aanmeldings referenties van de gebruiker die de wizard uitvoert, onvoldoende zijn om verbinding te maken |Lokale beheerder op de doel computer |Installatie en configuratie van de WAP-server functie. |
| Referenties voor proxy vertrouwen |Vertrouwens referenties van de Federation service (de referenties die de proxy gebruikt voor inschrijving van een vertrouwens certificaat van de FS |Domein account dat een lokale beheerder is van de AD FS server |Initiële inschrijving van het certificaat voor FS-WAP-vertrouwens relatie. |
| AD FS-pagina Service account, de optie een domein gebruikers account gebruiken |Referenties van AD-gebruikers account |Domein gebruiker |Het Azure AD-gebruikers account waarvan de referenties zijn geleverd, wordt gebruikt als aanmeldings account van de AD FS-service. |

### <a name="create-the-ad-ds-connector-account"></a>Het AD DS Connector-account maken

>[!IMPORTANT]
>Er is een nieuwe Power shell-module met de naam ADSyncConfig. psm1 geïntroduceerd met build **1.1.880.0** (uitgebracht in augustus 2018) die een verzameling cmdlets bevat waarmee u de juiste Active Directory machtigingen voor het account van de Azure AD DS-connector kunt configureren.
>
>Zie voor meer informatie [Azure AD Connect: AD DS-Connector account machtigingen configureren](how-to-connect-configure-ad-ds-connector-account.md)

Het account dat u opgeeft op de pagina **verbinding maken met uw adres lijsten** moet aanwezig zijn in Active Directory vóór de installatie.  Azure AD Connect versie 1.1.524.0 en hoger beschikt over de mogelijkheid om de Azure AD Connect wizard te laten maken van het **AD DS Connector-account** dat wordt gebruikt om verbinding met Active Directory te maken.  

Ook moet de vereiste machtigingen zijn toegekend. De machtigingen worden niet door de installatie wizard gecontroleerd en er zijn geen problemen gevonden tijdens de synchronisatie.

Welke machtigingen u nodig hebt, is afhankelijk van de optionele functies die u inschakelt. Als u meerdere domeinen hebt, moeten de machtigingen worden verleend voor alle domeinen in het forest. Als u geen van deze functies inschakelt, zijn de standaard **domein gebruikers** machtigingen voldoende.

| Functie | Machtigingen |
| --- | --- |
| functie MS-DS-ConsistencyGuid |Schrijf machtigingen voor het kenmerk MS-DS-ConsistencyGuid dat wordt beschreven in [ontwerp concepten-MS-DS-ConsistencyGuid gebruiken als source Anchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Wachtwoord-hash-synchronisatie |<li>Directory wijzigingen repliceren</li>  <li>Wijzigingen in Directory repliceren |
| Hybride implementatie van Exchange |Schrijf machtigingen voor de kenmerken die worden beschreven in [hybride write-back van Exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) voor gebruikers, groepen en contact personen. |
| Open bare map voor Exchange-e-mail |Lees machtigingen voor de kenmerken die in de [open bare map voor Exchange mail](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) voor open bare mappen worden beschreven. | 
| Wachtwoord terugschrijven |Schrijf machtigingen voor de kenmerken die worden beschreven in [aan de slag met wachtwoord beheer](../authentication/tutorial-enable-sspr-writeback.md) voor gebruikers. |
| Apparaat terugschrijven |Machtigingen die worden verleend met een Power shell-script, zoals beschreven in [write-back van apparaat](how-to-connect-device-writeback.md). |
| Groep terugschrijven |Hiermee kunt u **Microsoft 365 groepen** terugschrijven naar een forest waarop Exchange is geïnstalleerd.|

## <a name="upgrade"></a>Upgraden
Wanneer u een upgrade uitvoert van de ene versie van Azure AD Connect naar een nieuwe versie, hebt u de volgende machtigingen nodig:

>[!IMPORTANT]
>Vanaf build 1.1.484 heeft Azure AD Connect een regressie fout geïntroduceerd waarvoor sysadmin-machtigingen zijn vereist om de SQL database bij te werken.  Deze fout is gecorrigeerd in Build 1.1.647.  Als u een upgrade naar deze versie uitvoert, hebt u sysadmin-machtigingen nodig.  Dbo-machtigingen zijn niet voldoende.  Als u probeert een upgrade uit te voeren van Azure AD Connect zonder sysadmin-machtigingen, mislukt de upgrade en wordt Azure AD Connect niet meer correct uitgevoerd.  Micro soft is hiervan op de hoogte en werkt ermee om dit te corrigeren.


| Principal | Machtigingen vereist | Gebruikt voor |
| --- | --- | --- |
| Gebruiker die de installatie wizard uitvoert |Beheerder van de lokale server |Binaire bestanden bijwerken. |
| Gebruiker die de installatie wizard uitvoert |Lid van ADSyncAdmins |Wijzigingen aanbrengen in synchronisatie regels en andere configuratie. |
| Gebruiker die de installatie wizard uitvoert |Als u een volledige SQL Server gebruikt: DBO (of vergelijkbaar) van de data base van de synchronisatie-engine |Wijzigingen op database niveau aanbrengen, zoals het bijwerken van tabellen met nieuwe kolommen. |

## <a name="more-about-the-created-accounts"></a>Meer informatie over de gemaakte accounts
### <a name="ad-ds-connector-account"></a>AD DS Connector-account
Als u snelle instellingen gebruikt, wordt er een account gemaakt in Active Directory dat wordt gebruikt voor synchronisatie. Het account dat u hebt gemaakt, bevindt zich in het forest-hoofd domein in de gebruikers container en heeft de naam die is voorafgegaan door **MSOL_**. Het account wordt gemaakt met een lang complex wacht woord dat niet verloopt. Als u een wachtwoord beleid in uw domein hebt, moet u ervoor zorgen dat lange en complexe wacht woorden zijn toegestaan voor dit account.

![AD-account](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Als u aangepaste instellingen gebruikt, bent u zelf verantwoordelijk voor het maken van het account voordat u begint met de installatie.  Zie het AD DS Connector-account maken.

### <a name="adsync-service-account"></a>ADSync-serviceaccount
De synchronisatie service kan worden uitgevoerd onder verschillende accounts. Het kan worden uitgevoerd onder een **virtueel service account** (leverancierspecifieke naam), een **beheerd service account voor groepen** (gMSA/sMSA) of een gewoon gebruikers account. De ondersteunde opties zijn gewijzigd met de release van 2017 april wanneer u een nieuwe installatie maakt. Als u een upgrade uitvoert van een eerdere versie van Azure AD Connect, zijn deze extra opties niet beschikbaar.

| Type account | Installatie optie | Beschrijving |
| --- | --- | --- |
| [Virtueel service-account](#virtual-service-account) | Express en aangepast, 2017 april en hoger | Dit is de optie die wordt gebruikt voor alle snelle installaties, met uitzonde ring van installaties op een domein controller. Voor aangepast is dit de standaard optie, tenzij een andere optie wordt gebruikt. |
| [Door groep beheerd serviceaccount](#group-managed-service-account) | Aangepast, 2017 april en hoger | Als u een externe SQL Server gebruikt, raden we u aan een beheerd service account voor een groep te gebruiken. |
| [Gebruikersaccount](#user-account) | Express en aangepast, 2017 april en hoger | Een gebruikers account dat met AAD_ wordt voorafgegaan, wordt alleen tijdens de installatie gemaakt wanneer het is geïnstalleerd op Windows Server 2008 en wanneer geïnstalleerd op een domein controller. |
| [Gebruikersaccount](#user-account) | Express en aangepast, 2017 maart en ouder | Een lokaal account dat wordt voorafgegaan door AAD_, wordt tijdens de installatie gemaakt. Wanneer u een aangepaste installatie gebruikt, kan er een ander account worden opgegeven. |

Als u verbinding maken met een build van 2017 maart of eerder gebruikt, moet u het wacht woord niet opnieuw instellen voor het service account, omdat Windows de versleutelings sleutels om veiligheids redenen heeft vernietigd. U kunt het account niet wijzigen in een ander account zonder Azure AD Connect opnieuw te installeren. Als u een upgrade uitvoert naar een build van 2017 april of hoger, wordt het wacht woord voor het service account gewijzigd, maar u kunt het gebruikte account niet wijzigen.

> [!Important]
> U kunt het service account alleen instellen bij de eerste installatie. Het is niet mogelijk om het service account te wijzigen nadat de installatie is voltooid.

Dit is een tabel met de standaard, aanbevolen en ondersteunde opties voor het synchronisatie service account.

Legenda:

- Met **vet** wordt de standaard optie aangeduid en in de meeste gevallen de aanbevolen optie.
- *Italic* geeft de aanbevolen optie aan wanneer dit niet de standaard optie is.
- 2008-standaard optie wanneer geïnstalleerd op Windows Server 2008
- Optie niet-vet: ondersteund
- Lokaal account: lokale gebruikers account op de server
- Domein account-domein gebruikers account
- sMSA- [zelfstandig beheerd service account](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA: door [groep beheerd service account](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11))

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Aangepast telefoonnummer | Externe SQL</br>Aangepast telefoonnummer |
| --- | --- | --- | --- |
| **computer die lid is van een domein** | **KENMERK**</br>Lokaal account (2008) | **KENMERK**</br>Lokaal account (2008)</br>Lokaal account</br>Domeinaccount</br>sMSA, gMSA | **gMSA**</br>Domeinaccount |
| **Domein controller** | **Domeinaccount** | *gMSA*</br>**Domeinaccount**</br>sMSA| *gMSA*</br>**Domeinaccount**|

#### <a name="virtual-service-account"></a>Virtueel service-account
Een virtueel service account is een speciaal type account dat geen wacht woord heeft en wordt beheerd door Windows.

![Scherm opname van de virtuele service account (leverancierspecifieke kenmerken).](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

Het leveranciersspecifieke kenmerk is bedoeld om te worden gebruikt met scenario's waarbij de synchronisatie-engine en SQL zich op dezelfde server bevinden. Als u externe SQL gebruikt, raden we u aan om in plaats daarvan een beheerd service account voor een groep te gebruiken.

Voor deze functie is Windows Server 2008 R2 of later vereist. Als u Azure AD Connect installeert op Windows Server 2008, wordt de installatie in plaats daarvan terugvallen op het gebruik van een [gebruikers account](#user-account) .

#### <a name="group-managed-service-account"></a>Door groep beheerd service account
Als u een externe SQL Server gebruikt, raden we u aan een **beheerd service account** voor een groep te gebruiken. Zie overzicht van door een groep [beheerde service accounts](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11))voor meer informatie over het voorbereiden van uw Active Directory voor door een groep beheerd service account.

Als u deze optie wilt gebruiken, selecteert u op de pagina [vereiste onderdelen installeren](how-to-connect-install-custom.md#install-required-components) de optie **een bestaand service account gebruiken** en selecteert u een **beheerd service account**.  
![KENMERK](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Het wordt ook ondersteund voor het gebruik van een [zelfstandig beheerd service account](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10)). Deze kunnen echter alleen worden gebruikt op de lokale computer en er is geen voor deel voor het gebruik van de standaard virtuele-service account.

Voor deze functie is Windows Server 2012 of hoger vereist. Als u een ouder besturings systeem moet gebruiken en externe SQL wilt gebruiken, moet u een [gebruikers account](#user-account)gebruiken.

#### <a name="user-account"></a>Gebruikersaccount
Er wordt een lokaal service account gemaakt door de installatie wizard (tenzij u het account opgeeft dat moet worden gebruikt in aangepaste instellingen). Het account wordt voorafgegaan **AAD_** en wordt gebruikt om de huidige synchronisatie service uit te voeren als. Als u Azure AD Connect op een domein controller installeert, wordt het account in het domein gemaakt. Het **AAD_** -service account moet zich in het domein bevinden als:
   - u gebruikt een externe server waarop SQL Server wordt uitgevoerd
   - u gebruikt een proxy waarvoor verificatie is vereist

![Service account synchroniseren](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

Het account wordt gemaakt met een lang complex wacht woord dat niet verloopt.

Dit account wordt gebruikt om de wacht woorden voor de andere accounts op een veilige manier op te slaan. Deze andere account wachtwoorden worden in de-data base opgeslagen als versleuteld. De persoonlijke sleutels voor de versleutelings sleutels worden beveiligd met de geheime sleutel versleuteling van cryptografische Services met behulp van Windows Data Protection API (DPAPI).

Als u een volledige SQL Server gebruikt, is het service account de DBO van de gemaakte Data Base voor de synchronisatie-engine. De service werkt niet zoals bedoeld met andere machtigingen. Er wordt ook een SQL-aanmelding gemaakt.

Aan het account worden ook machtigingen verleend voor bestanden, register sleutels en andere objecten die betrekking hebben op de synchronisatie-engine.

### <a name="azure-ad-connector-account"></a>Azure AD Connector-account
Een account in azure AD is gemaakt voor het gebruik van de synchronisatie service. Dit account kan worden geïdentificeerd aan de hand van de weergave naam.

![Scherm opname van het Azure AD-account.](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

De naam van de server waarop het account wordt gebruikt, kan worden geïdentificeerd in het tweede deel van de gebruikers naam. In de afbeelding is de server naam DC1. Als u servers voor fase ring hebt, heeft elke server een eigen account.

Het account wordt gemaakt met een lang complex wacht woord dat niet verloopt. Er wordt een speciale **Directory synchronisatie-accounts** met rollen verleend die alleen machtigingen hebben voor het uitvoeren van Directory synchronisatie taken. Deze speciale ingebouwde rol kan niet worden verleend buiten de Azure AD Connect wizard. De Azure Portal geeft dit account weer met de rol **gebruiker**.

Er is een limiet van 20 synchronisatie service-accounts in azure AD. Als u de lijst met bestaande Azure AD-service accounts in uw Azure AD wilt ophalen, voert u de volgende Azure AD Power shell-cmdlet uit: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Als u ongebruikte Azure AD-service accounts wilt verwijderen, voert u de volgende Azure AD Power shell-cmdlet uit: `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>Voordat u de bovenstaande Power shell-opdrachten kunt gebruiken, moet u de [Azure Active Directory Power shell for Graph-module](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module) installeren en verbinding maken met uw exemplaar van Azure AD via [Connect-AzureAD](/powershell/module/azuread/connect-azuread)

Zie [het Azure AD Connect account beheren](how-to-connect-azureadaccount.md) voor meer informatie over het beheren of opnieuw instellen van het wacht woord voor het account van de Azure AD-connector.

## <a name="related-documentation"></a>Verwante documentatie
Als u de documentatie over het integreren van [uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md)niet hebt gelezen, vindt u in de volgende tabel koppelingen naar verwante onderwerpen.

|Onderwerp |Koppeling|  
| --- | --- |
|Azure AD Connect downloaden | [Azure AD Connect downloaden](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Installeren met de snelle instellingen | [Snelle installatie van Azure AD Connect](how-to-connect-install-express.md)|
|Installeren met behulp van aangepaste instellingen | [Aangepaste installatie van Azure AD Connect](./how-to-connect-install-custom.md)|
|Upgrade van DirSync | [Upgrade van Azure AD-synchronisatiehulpprogramma (DirSync) (Engelstalig artikel)](how-to-dirsync-upgrade-get-started.md)|
|Na de installatie | [Controleer de installatie en wijs licenties toe](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
