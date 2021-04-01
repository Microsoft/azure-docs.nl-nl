---
title: Een installatie van Azure Active Directory Connect aanpassen
description: In dit artikel worden de aangepaste installatie opties voor Azure AD Connect beschreven. Volg deze instructies om Active Directory met Azure AD Connect te installeren.
services: active-directory
keywords: wat is Azure AD Connect, Active Directory installeren, vereiste onderdelen voor Azure AD
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3afeadff71bd373354b891bd6690d94d28fc0805
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92096348"
---
# <a name="custom-installation-of-azure-active-directory-connect"></a>Aangepaste installatie van Azure Active Directory Connect
Gebruik *aangepaste instellingen* in azure Active Directory (Azure AD) verbinding maken als u meer opties voor de installatie wilt. Gebruik deze instellingen, bijvoorbeeld als u meerdere forests hebt of als u optionele functies wilt configureren. Gebruik aangepaste instellingen in alle gevallen waarbij de [snelle installatie](how-to-connect-install-express.md) niet voldoet aan de behoeften van uw implementatie of topologie.

Vereisten:
- [Down load Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).
- Voltooi de vereiste stappen in [Azure AD Connect: Hardware en vereisten](how-to-connect-install-prerequisites.md). 
- Zorg ervoor dat u beschikt over de accounts die zijn beschreven in [Azure AD Connect accounts en machtigingen](reference-connect-accounts-permissions.md).

## <a name="custom-installation-settings"></a>Aangepaste installatie-instellingen 

Als u een aangepaste installatie voor Azure AD Connect wilt instellen, gaat u door de wizard pagina's die in de volgende secties worden beschreven.

### <a name="express-settings"></a>Snelle instellingen
Selecteer op de pagina **snelle instellingen** de optie **aanpassen** om een installatie met aangepaste instellingen te starten.  De rest van dit artikel begeleidt u bij het aangepaste installatie proces. Gebruik de volgende koppelingen om snel naar de informatie voor een bepaalde pagina te gaan:

- [Vereiste onderdelen](#install-required-components)
- [Gebruikers aanmelding](#user-sign-in)
- [Verbinding maken met Azure AD](#connect-to-azure-ad)
- [Synchroniseren](#sync-pages)

### <a name="install-required-components"></a>Vereiste onderdelen installeren
Wanneer u de synchronisatie Services installeert, kunt u de sectie optionele configuratie uitgeschakeld laten. Azure AD Connect alles automatisch worden ingesteld. Hiermee wordt een SQL Server 2012 Express LocalDB-exemplaar ingesteld, worden de juiste groepen gemaakt en worden machtigingen toegewezen. Als u de standaard instellingen wilt wijzigen, schakelt u de desbetreffende selectie vakjes uit.  De volgende tabel bevat een overzicht van deze opties en koppelingen naar aanvullende informatie. 

![Scherm afbeelding met optionele selecties voor de vereiste installatie onderdelen in Azure AD Connect.](./media/how-to-connect-install-custom/requiredcomponents2.png)

| Optionele configuratie | Description |
| --- | --- |
|Een aangepaste installatie locatie opgeven| Hiermee kunt u het installatiepad voor Azure AD Connect wijzigen.|
| Een bestaande SQL Server gebruiken |Hiermee kunt u de SQL Server naam en de naam van het exemplaar opgeven. Kies deze optie als u al een database server hebt die u wilt gebruiken. Voer bij **instantie naam** de naam van het exemplaar, een komma en het poort nummer in als uw SQL Server-exemplaar geen Blader functie heeft ingeschakeld.  Geef vervolgens de naam op van de Azure AD Connect-Data Base.  Uw SQL-bevoegdheden bepalen of een nieuwe Data Base kan worden gemaakt of dat de SQL-beheerder de data base vooraf moet maken.  Zie [Install Azure AD Connect met behulp van een bestaande data base](how-to-connect-install-existing-database.md)als u over SQL Server Administrator-machtigingen (SA) beschikt.  Zie [Install Azure AD Connect met behulp van SQL delegated Administrator Permissions](how-to-connect-install-sql-delegation.md)(Engelstalig) als u over gedelegeerde machtigingen (dbo) beschikt. |
| Een bestaand serviceaccount gebruiken |Azure AD Connect biedt standaard een virtueel service account voor de synchronisatie Services. Als u een extern exemplaar van SQL Server gebruikt of een proxy gebruikt waarvoor authenticatie is vereist, kunt u een *beheerd service account* of een met een wacht woord beveiligd service account in het domein gebruiken. In die gevallen voert u het account in dat u wilt gebruiken. Als u de installatie wilt uitvoeren, moet u een SA in SQL hebben zodat u aanmeldings referenties voor het service account kunt maken. Zie [Azure AD Connect accounts en-machtigingen](reference-connect-accounts-permissions.md#adsync-service-account)voor meer informatie. </br></br>Als u de nieuwste versie gebruikt, kan de SQL-beheerder de data base nu buiten-band inrichten. Vervolgens kan de Azure AD Connect-beheerder deze installeren met de rechten van de data base-eigenaar.  Zie [Install Azure AD Connect met behulp van SQL delegated Administrator Permissions](how-to-connect-install-sql-delegation.md)voor meer informatie.|
| Aangepaste synchronisatiegroepen opgeven |Wanneer de synchronisatie Services zijn geïnstalleerd, maakt Azure AD Connect standaard vier groepen die lokaal zijn voor de-server. Deze groepen zijn beheerders, Opera Tors, bladeren en wacht woord opnieuw instellen. U kunt hier uw eigen groepen opgeven. De groepen moeten lokaal op de server zijn. Ze kunnen zich niet in het domein bevinden. |
|Synchronisatie-instellingen importeren (preview-versie)|Hiermee kunt u instellingen importeren uit andere versies van Azure AD Connect.  Zie Azure AD Connect-configuratie- [Instellingen importeren en exporteren](how-to-connect-import-export-config.md)voor meer informatie.|

### <a name="user-sign-in"></a>Gebruikersaanmelding
Nadat u de vereiste onderdelen hebt geïnstalleerd, selecteert u de methode voor eenmalige aanmelding van uw gebruikers. De volgende tabel bevat een korte beschrijving van de beschik bare opties. Zie voor een volledige beschrijving van de aanmeldmethodes [User sign-in](plan-connect-user-signin.md).

![Scherm opname waarin de pagina gebruikers aanmelding wordt weer gegeven. De optie voor wachtwoord hash-synchronisatie is geselecteerd.](./media/how-to-connect-install-custom/usersignin4.png)

| Optie voor eenmalige aanmelding | Description |
| --- | --- |
| Synchronisatie van wachtwoord-hashes |Gebruikers kunnen zich aanmelden bij micro soft-Cloud Services, zoals Microsoft 365, door gebruik te maken van hetzelfde wacht woord dat ze gebruiken in hun on-premises netwerk. Gebruikers wachtwoorden worden als wacht woord-hash gesynchroniseerd met Azure AD. Verificatie vindt plaats in de Cloud. Zie [Password Hash Synchronization](how-to-connect-password-hash-synchronization.md)(Engelstalig) voor meer informatie. |
|Pass-through-verificatie|Gebruikers kunnen zich aanmelden bij micro soft-Cloud Services, zoals Microsoft 365, door gebruik te maken van hetzelfde wacht woord dat ze gebruiken in hun on-premises netwerk.  Gebruikers wachtwoorden worden gevalideerd door door te gegeven aan de on-premises Active Directory domein controller.
| Federatie met AD FS |Gebruikers kunnen zich aanmelden bij micro soft-Cloud Services, zoals Microsoft 365, door gebruik te maken van hetzelfde wacht woord dat ze gebruiken in hun on-premises netwerk.  Gebruikers worden omgeleid naar hun on-premises Azure Directory Federation Services-exemplaar (AD FS) om zich aan te melden. Verificatie vindt plaats on-premises. |
| Federatie met PingFederate|Gebruikers kunnen zich aanmelden bij micro soft-Cloud Services, zoals Microsoft 365, door gebruik te maken van hetzelfde wacht woord dat ze gebruiken in hun on-premises netwerk.  Gebruikers worden omgeleid naar hun on-premises PingFederate-exemplaar om zich aan te melden. Verificatie vindt plaats on-premises. |
| Niet configureren |Er is geen functie voor het aanmelden van gebruikers geïnstalleerd of geconfigureerd. Kies deze optie als u al een Federatie server van derden of een andere oplossing hebt. |
|Eenmalige aanmelding inschakelen|Deze optie is beschikbaar met zowel wachtwoord-hash-synchronisatie als Pass-Through-verificatie. Het biedt eenmalige aanmelding voor desktop gebruikers op bedrijfs netwerken. Zie [eenmalige aanmelding](how-to-connect-sso.md)voor meer informatie. </br></br>**Opmerking:** Voor AD FS klanten is deze optie niet beschikbaar. AD FS biedt al hetzelfde niveau van eenmalige aanmelding.</br>

### <a name="connect-to-azure-ad"></a>Verbinding maken met Azure AD
Voer op de pagina **verbinding maken met Azure AD** een globaal beheerders account en wacht woord in. Als u **Federatie met AD FS** op de vorige pagina hebt geselecteerd, meldt u zich niet aan met een account in een domein dat u wilt inschakelen voor Federatie. 

U kunt een account gebruiken in het standaard domein *onmicrosoft.com* , dat wordt geleverd bij uw Azure AD-Tenant. Dit account wordt alleen gebruikt voor het maken van een service account in azure AD. Het wordt niet gebruikt nadat de installatie is voltooid.
  
![Scherm afbeelding van de pagina verbinding maken met Azure AD.](./media/how-to-connect-install-custom/connectaad.png)

Als voor uw globale beheerders account multi-factor Authentication is ingeschakeld, geeft u het wacht woord opnieuw op in het aanmeld venster en moet u de Challenge voor multi-factor Authentication volt ooien. De uitdaging kan een verificatie code of een telefoon gesprek zijn.  

![Scherm afbeelding van de pagina verbinding maken met Azure AD. Een multi-factor Authentication-veld vraagt de gebruiker om een code.](./media/how-to-connect-install-custom/connectaadmfa.png)

Het account voor de globale beheerder kan ook [privileged Identity Management](../privileged-identity-management/pim-getting-started.md) hebben ingeschakeld.

Als u een fout melding krijgt of problemen hebt met de connectiviteit, raadpleeg dan [verbindings problemen oplossen](tshoot-connect-connectivity.md).

## <a name="sync-pages"></a>Pagina's synchroniseren

In de volgende secties worden de pagina's in de sectie **Sync** beschreven.

### <a name="connect-your-directories"></a>Verbinding maken met uw directory’s
Azure AD Connect moet de forestnaam en referenties van een account met voldoende machtigingen hebben om verbinding te maken met Active Directory Domain Services (Azure AD DS).

![Scherm opname van de pagina ' Connect your directory's '.](./media/how-to-connect-install-custom/connectdir01.png)

Nadat u de naam van het forest hebt ingevoerd en  **Directory toevoegen** selecteert, wordt er een venster weer gegeven. In de volgende tabel worden de opties beschreven.

| Optie | Beschrijving |
| --- | --- |
| Nieuw account maken | Maak het Azure AD DS-account dat Azure AD Connect nodig heeft om verbinding te maken met het Active Directory forest tijdens Directory synchronisatie. Nadat u deze optie hebt geselecteerd, voert u de gebruikers naam en het wacht woord in voor een ondernemings Administrator-account.  Azure AD Connect maakt gebruik van het opgegeven account voor ondernemings Administrator om het vereiste Azure AD DS-account te maken. U kunt het domein onderdeel invoeren in de NetBIOS-indeling of de FQDN-indeling. Dat wil zeggen, Voer *FABRIKAM\administrator* of *FABRIKAM. com\administrator* in. |
| Bestaand account gebruiken | Geef een bestaand Azure AD DS-account op dat Azure AD Connect kan gebruiken om verbinding te maken met het Active Directory forest tijdens adreslijst synchronisatie. U kunt het domein onderdeel invoeren in de NetBIOS-indeling of de FQDN-indeling. Dat wil zeggen, Voer *FABRIKAM\syncuser* of *FABRIKAM. com\syncuser* in. Dit account kan een normaal gebruikers account zijn omdat het alleen de standaard machtigingen voor lezen nodig heeft. Maar afhankelijk van uw scenario hebt u mogelijk meer machtigingen nodig. Zie [Azure AD Connect accounts en-machtigingen](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account)voor meer informatie. |

![Scherm opname van de pagina ' Connect Directory ' en het venster A D forest-account, waarin u kunt kiezen of u een nieuw account wilt maken of een bestaand account wilt gebruiken.](./media/how-to-connect-install-custom/connectdir02.png)

>[!NOTE]
> Vanaf build 1.4.18.0 kunt u geen account voor ondernemings Administrator of domein beheerder gebruiken als het account van de Azure AD DS-connector. Als u een **bestaand account gebruiken** selecteert en u probeert om een ondernemings Administrator-account of een domein beheerders account in te voeren, ziet u de volgende fout: ' een ondernemings-of domein beheerders account voor uw AD-forest-account gebruiken is niet toegestaan. Laat Azure AD Connect het account voor u maken of een synchronisatie account met de juiste machtigingen opgeven. "
>

### <a name="azure-ad-sign-in-configuration"></a>Aanmeldconfiguratie Azure AD
Controleer op de pagina **aanmeldings configuratie van Azure AD** de User Principal Name (UPN)-domeinen in on-premises Azure AD DS. Deze UPN-domeinen zijn geverifieerd in azure AD. Op deze pagina configureert u het kenmerk dat voor de userPrincipalName moet worden gebruikt.

![Scherm opname van niet-geverifieerde domeinen op de pagina "Azure A D aanmeld configuratie".](./media/how-to-connect-install-custom/aadsigninconfig2.png)  

Controleer elk domein dat is gemarkeerd als **niet toegevoegd** of **niet gecontroleerd**. Zorg ervoor dat de domeinen die u gebruikt, zijn geverifieerd in azure AD. Nadat u uw domeinen hebt geverifieerd, selecteert u het pictogram voor het Vernieuwings interval. Zie voor meer informatie [toevoegen en verifiëren van het domein](../fundamentals/add-custom-domain.md).

Gebruikers gebruiken het kenmerk *userPrincipalName* wanneer ze zich aanmelden bij Azure AD en Microsoft 365. Azure AD moet de domeinen, ook wel bekend als het UPN-achtervoegsel, verifiëren voordat gebruikers worden gesynchroniseerd. Micro soft raadt u aan het standaard kenmerk userPrincipalName te hand haven. 

Als het kenmerk userPrincipalName nonroutable is en niet kan worden geverifieerd, kunt u een ander kenmerk selecteren. U kunt bijvoorbeeld e-mail selecteren als het kenmerk dat de aanmeldings-ID bevat. Wanneer u een ander kenmerk dan userPrincipalName gebruikt, wordt dit een *alternatieve id* genoemd. 

De waarde van het kenmerk alternatieve ID moet voldoen aan de RFC 822-norm. U kunt een alternatieve ID gebruiken voor de synchronisatie van hashes van wacht woorden, Pass Through-verificatie en Federatie. In Active Directory kan het kenmerk niet worden gedefinieerd als meerdere waarden, zelfs als het slechts één waarde heeft. Zie [Pass-Through-verificatie: veelgestelde vragen](./how-to-connect-pta-faq.md#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname)voor meer informatie over de alternatieve id.

>[!NOTE]
> Als u Pass-Through-verificatie inschakelt, moet u ten minste één geverifieerd domein hebben om door te gaan met het aangepaste installatie proces.

> [!WARNING]
> Alternatieve Id's zijn niet compatibel met alle Microsoft 365 workloads. Zie [alternatieve aanmeldings-Id's configureren](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)voor meer informatie.
>

### <a name="domain-and-ou-filtering"></a>Domein en OE filteren
Standaard worden alle domeinen en organisatie-eenheden (Ou's) gesynchroniseerd. Als u bepaalde domeinen of organisatie-eenheden niet wilt synchroniseren met Azure AD, kunt u de juiste selecties wissen.  

![Scherm afbeelding van de pagina domein en O U filteren.](./media/how-to-connect-install-custom/domainoufiltering.png)  

Op deze pagina configureert u filteren op basis van een domein en op basis van een organisatie-eenheid. Als u wijzigingen wilt aanbrengen, raadpleeg dan op [domein gebaseerde filtering](how-to-connect-sync-configure-filtering.md#domain-based-filtering) en [filtering op basis van organisatie-eenheid](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Sommige organisatie-eenheden zijn essentieel voor de functionaliteit. u moet deze dus ingeschakeld laten.

Als u filtering op basis van een organisatie-eenheid gebruikt met een Azure AD Connect oudere versie dan 1.1.524.0, worden nieuwe organisatie-eenheden standaard gesynchroniseerd. Als u niet wilt dat nieuwe organisatie-eenheden worden gesynchroniseerd, kunt u het standaard gedrag aanpassen na de stap voor het [filteren op basis van een organisatie-eenheid](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) . Voor Azure AD Connect 1.1.524.0 of hoger kunt u aangeven of u nieuwe organisatie-eenheden wilt synchroniseren.

Als u van plan bent om [filteren op basis van groepen](#sync-filtering-based-on-groups)te gebruiken, moet u ervoor zorgen dat de organisatie-eenheid met de groep is opgenomen en niet wordt gefilterd met behulp van OE-filtering. De OE wordt gefilterd voordat het filteren op basis van een groep wordt geëvalueerd.

Het is ook mogelijk dat sommige domeinen onbereikbaar zijn vanwege firewall beperkingen. Deze domeinen zijn standaard uitgeschakeld en er wordt een waarschuwing weer gegeven.  

![Scherm opname met onbereikbare domeinen.](./media/how-to-connect-install-custom/unreachable.png)  

Als u deze waarschuwing ziet, zorg er dan voor dat deze domeinen inderdaad onbereikbaar zijn en dat de waarschuwing wordt verwacht.

### <a name="uniquely-identifying-your-users"></a>Uw gebruikers een unieke id geven

Kies op de pagina **gebruikers identificeren** hoe gebruikers in uw on-premises directory's moeten worden geïdentificeerd en hoe ze kunnen worden geïdentificeerd met behulp van het kenmerk source Anchor.

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>Selecteren hoe gebruikers moeten worden aangeduid in uw on-premises directory’s
Met de functie voor het *vergelijken van verschillende forests* kunt u definiëren hoe gebruikers van uw Azure AD DS-forests worden weer gegeven in azure AD. Een gebruiker kan slechts één keer worden weer gegeven in alle forests of kan een combi natie van ingeschakelde en uitgeschakelde accounts hebben. De gebruiker kan in sommige forests ook worden weergegeven als een contactpersoon.

![Scherm opname van de pagina waar u uw gebruikers uniek kunt identificeren.](./media/how-to-connect-install-custom/unique2.png)

| Instelling | Beschrijving |
| --- | --- |
| [Gebruikers worden slechts één keer weer gegeven in alle forests](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Alle gebruikers worden als afzonderlijke objecten in Azure AD aangemaakt. De objecten zijn niet gekoppeld aan de tekst. |
| [E-mailkenmerk](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Deze optie koppelt gebruikers en contactpersonen als het e-mailkenmerk in verschillende forests dezelfde waarde heeft. Gebruik deze optie wanneer uw contact personen zijn gemaakt met behulp van GALSync. Als u deze optie kiest, worden gebruikers objecten waarvan het e-mail kenmerk niet is ingevuld, niet gesynchroniseerd met Azure AD. |
| [ObjectSID en msExchangeMasterAccountSID/msRTCSIP-OriginatorSID kenmerken](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Deze optie koppelt een ingeschakelde gebruiker in een account-forest met een uitgeschakelde gebruiker in een bron-forest. In Exchange wordt deze configuratie een gekoppeld postvak genoemd. U kunt deze optie gebruiken als u alleen Lync gebruikt en als Exchange niet aanwezig is in de bron-forest. |
| SAMAccountName en mailnickname-kenmerken |Deze optie wordt toegevoegd aan kenmerken waarbij de aanmeldings-ID voor de gebruiker wordt verwacht. |
| Kies een specifiek kenmerk |Met deze optie kunt u uw eigen kenmerk selecteren. Als u deze optie kiest, worden gebruikers objecten waarvan het kenmerk (geselecteerd) niet is gevuld, niet gesynchroniseerd met Azure AD. **Beperking:** Alleen kenmerken die zich al in de omgekeerde tekst bevinden, zijn beschikbaar voor deze optie. |

#### <a name="select-how-users-should-be-identified-by-using-a-source-anchor"></a>Selecteren hoe gebruikers moeten worden geïdentificeerd met behulp van een bron anker
Het kenmerk *Source Anchor* is onveranderbaar tijdens de levens duur van een gebruikers object. Het is de primaire sleutel die de on-premises gebruiker koppelt aan de gebruiker in azure AD.

| Instelling | Beschrijving |
| --- | --- |
| Azure het bron anker laten beheren | Selecteer deze optie als u wilt dat Azure AD het kenmerk voor u selecteert. Als u deze optie selecteert, past Azure AD Connect de source Anchor-kenmerk selectie logica toe die wordt beschreven in [MS-DS-ConsistencyGuid als source Anchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Nadat de aangepaste installatie is voltooid, ziet u welk kenmerk is gekozen als het kenmerk source Anchor. |
| Kies een specifiek kenmerk | Selecteer deze optie als u een bestaand AD-kenmerk wilt opgeven als het kenmerk source Anchor. |

Omdat het kenmerk source Anchor niet kan worden gewijzigd, moet u een geschikt kenmerk kiezen. Een goede kandidaat is objectGUID. Dit kenmerk wordt alleen gewijzigd als het gebruikers account wordt verplaatst tussen forests of domeinen. Kies geen kenmerken die kunnen worden gewijzigd wanneer een persoon trouwt of een andere toewijzing wijzigt. 

U kunt geen kenmerken gebruiken die een apen staartje (@) bevatten, zodat u geen e-mail en userPrincipalName kunt gebruiken. Het kenmerk is ook hoofdletter gevoelig, dus wanneer u een object tussen forests verplaatst, moet u hoofd letters en kleine letters behouden. Binaire kenmerken zijn base64-gecodeerd, maar andere kenmerk typen blijven niet-versleutelde status. 

In Federatie scenario's en sommige Azure AD-interfaces wordt het kenmerk source Anchor ook wel *immutableID* genoemd. 

Zie [ontwerp concepten](plan-connect-design-concepts.md#sourceanchor)voor meer informatie over het bron anker.

### <a name="sync-filtering-based-on-groups"></a>Synchronisatiefilters op basis van groepen
Met de functie filteren op groep kunt u slechts een kleine subset van objecten voor een pilot synchroniseren. Als u deze functie wilt gebruiken, maakt u een groep voor dit doel in uw on-premises exemplaar van Active Directory. Voeg vervolgens gebruikers en groepen toe die naar Azure AD moeten worden gesynchroniseerd als directe leden. U kunt later gebruikers toevoegen of gebruikers uit deze groep verwijderen om de lijst met objecten te onderhouden die aanwezig moeten zijn in azure AD. 

Alle objecten die u wilt synchroniseren, moeten direct lid zijn van de groep. Gebruikers, groepen, contact personen en computers of apparaten moeten allemaal direct leden zijn. Het geneste groepslid maatschap is niet opgelost. Wanneer u een groep als lid toevoegt, wordt alleen de groep zelf toegevoegd. De leden zijn niet toegevoegd.

![Scherm opname van de pagina waarop u kunt kiezen hoe gebruikers en apparaten moeten worden gefilterd.](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> Deze functie is bedoeld om alleen een pilot implementatie te ondersteunen. Gebruik het niet in een volledige productie-implementatie.
>

Bij een volledige productie-implementatie zou het lastig zijn om één groep en alle bijbehorende objecten te synchroniseren. Gebruik in plaats van de functie filteren op groep een van de methoden die worden beschreven in [filtering configureren](how-to-connect-sync-configure-filtering.md).

### <a name="optional-features"></a>Optionele functies
Op de volgende pagina kunt u optionele functies voor uw scenario selecteren.

>[!WARNING]
>Azure AD Connect versies 1.0.8641.0 en eerder zijn afhankelijk van Azure Access Control Service voor het terugschrijven van wacht woorden.  Deze service is op 7 november 2018 buiten gebruik gesteld.  Als u een van deze versies van Azure AD Connect gebruikt en wacht woord terugschrijven hebt ingeschakeld, kunnen gebruikers de mogelijkheid tot het wijzigen of opnieuw instellen van hun wacht woorden verliezen wanneer de service buiten gebruik wordt gesteld. Deze versies van Azure AD Connect bieden geen ondersteuning voor het terugschrijven van wacht woorden.
>
>Zie [Migrate from Azure Access Control service](../azuread-dev/active-directory-acs-migration.md)voor meer informatie.
>
>Als u wacht woord terugschrijven wilt gebruiken, downloadt u de [nieuwste versie van Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

![Scherm afbeelding van de pagina optionele functies.](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> Als Azure AD Sync of direct synchroniseren (DirSync) actief is, activeert u geen write-functies in Azure AD Connect.



| Optionele functies | Description |
| --- | --- |
| Hybride implementatie van Exchange |Met de functie hybride implementatie van Exchange kunnen Exchange-post vakken zowel on-premises als in Microsoft 365 worden uitgewisseld. Azure AD Connect synchroniseert een specifieke set [kenmerken](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) van Azure AD terug naar uw on-premises Directory. |
| Exchange-e-mail-open bare mappen | Met de functie open bare mappen van Exchange mail kunt u e-mail berichten met open bare mappen synchroniseren van uw on-premises exemplaar van Active Directory naar Azure AD. |
| Azure AD-app- en -kenmerkfilters |Door Azure AD-app-en-kenmerk filtering in te scha kelen, kunt u de set gesynchroniseerde kenmerken aanpassen. Door deze optie worden twee extra configuratiepagina’s aan de wizard toegevoegd. Zie voor meer informatie [Azure AD app and attribute filtering](#azure-ad-app-and-attribute-filtering). |
| Synchronisatie van wachtwoord-hashes |Als u Federatie hebt geselecteerd als de aanmeldings oplossing, kunt u wachtwoord hash-synchronisatie inschakelen. Vervolgens kunt u deze als back-upoptie gebruiken.  </br></br>Als u Pass-Through-verificatie hebt geselecteerd, kunt u deze optie inschakelen om ondersteuning te bieden voor verouderde clients en een back-up te maken.</br></br> Zie [Password Hash Synchronization](how-to-connect-password-hash-synchronization.md)(Engelstalig) voor meer informatie.|
| Wachtwoord terugschrijven |Gebruik deze optie om ervoor te zorgen dat wachtwoord wijzigingen die afkomstig zijn van in azure AD, worden teruggeschreven naar uw on-premises Directory. Zie voor meer informatie [Getting started with password management](../authentication/tutorial-enable-sspr.md). |
| Groep terugschrijven |Als u Microsoft 365 groepen gebruikt, kunt u groepen vertegenwoordigen in uw on-premises exemplaar van Active Directory. Deze optie is alleen beschikbaar als u Exchange hebt in uw on-premises exemplaar van Active Directory. Zie [Azure AD Connect Group writeing](how-to-connect-group-writeback.md)(Engelstalig) voor meer informatie.|
| Apparaat terugschrijven |Gebruik deze optie voor scenario's voor voorwaardelijke toegang om objecten in azure AD terug te schrijven naar uw on-premises exemplaar van Active Directory. Zie voor meer informatie [Enabling device writeback in Azure AD Connect](how-to-connect-device-writeback.md). |
| Synchronisatie van directory-extensiekenmerken |Selecteer deze optie om de opgegeven kenmerken te synchroniseren met Azure AD. Zie voor meer informatie [Directory extensions](how-to-connect-sync-feature-directory-extensions.md). |

### <a name="azure-ad-app-and-attribute-filtering"></a>Azure AD-app- en -kenmerkfilters
Als u wilt beperken welke kenmerken worden gesynchroniseerd met Azure AD, kunt u beginnen met het selecteren van de services die u gebruikt. Als u de selecties op deze pagina wijzigt, moet u expliciet een nieuwe service selecteren door de installatie wizard opnieuw uit te voeren.

![Scherm opname van de optionele functies van Azure A D apps.](./media/how-to-connect-install-custom/azureadapps2.png)

Op basis van de services die u in de vorige stap hebt geselecteerd, worden op deze pagina alle kenmerken weer gegeven die zijn gesynchroniseerd. Deze lijst is een combi natie van alle object typen die worden gesynchroniseerd. Als sommige kenmerken niet-gesynchroniseerd moeten blijven, kunt u de selectie uit deze kenmerken wissen.

![Scherm opname van de optionele functies van Azure A D kenmerken.](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> Het verwijderen van kenmerken kan invloed hebben op de functionaliteit. Zie [kenmerken voor synchronisatie](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize)voor aanbevolen procedures en aanbevelingen.
>

### <a name="directory-extension-attribute-sync"></a>Synchronisatie van directory-extensiekenmerken
U kunt het schema uitbreiden in azure AD met behulp van aangepaste kenmerken die uw organisatie heeft toegevoegd of door gebruik te maken van andere kenmerken in Active Directory. Als u deze functie wilt gebruiken, selecteert u op de pagina **optionele functies** de optie **synchronisatie van Directory-extensie kenmerken**. Op de pagina **Directory-extensies** kunt u meer kenmerken selecteren om te synchroniseren.

>[!NOTE]
>Het veld **beschik bare kenmerken** is hoofdletter gevoelig.

![Scherm afbeelding met de pagina Directory-extensies.](./media/how-to-connect-install-custom/extension2.png)

Zie voor meer informatie [Directory extensions](how-to-connect-sync-feature-directory-extensions.md).

### <a name="enabling-single-sign-on"></a>Eenmalige aanmelding inschakelen
Op de pagina **eenmalige aanmelding** kunt u eenmalige aanmelding configureren voor gebruik met wachtwoord synchronisatie of Pass-Through-verificatie. U voert deze stap één keer uit voor elk forest dat wordt gesynchroniseerd met Azure AD. De configuratie bestaat uit twee stappen:

1.  Maak het benodigde computer account in uw on-premises exemplaar van Active Directory.
2.  Configureer de intranet zone van de client computers voor de ondersteuning van eenmalige aanmelding.

#### <a name="create-the-computer-account-in-active-directory"></a>Het computeraccount maken in Active Directory
Voor elk forest dat is toegevoegd in Azure AD Connect, moet u referenties voor de domein beheerder opgeven, zodat het computer account in elk forest kan worden gemaakt. De referenties worden alleen gebruikt om het account te maken. Ze worden niet opgeslagen of gebruikt voor een andere bewerking. Voeg de referenties toe op de pagina **eenmalige aanmelding inschakelen** , zoals in de volgende afbeelding wordt weer gegeven.

![Scherm afbeelding met de pagina ' eenmalige aanmelding inschakelen '. De forest-referenties worden toegevoegd.](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>U kunt forests overs Laan waarvoor u eenmalige aanmelding niet wilt gebruiken.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>De intranet zone voor client computers configureren
Om ervoor te zorgen dat de client zich automatisch aanmeldt in de intranet zone, controleert u of de URL deel uitmaakt van de intranet zone. Deze stap zorgt ervoor dat de computer die lid is van het domein automatisch een Kerberos-ticket naar Azure AD verzendt wanneer het is verbonden met het bedrijfs netwerk.

Op een computer met groepsbeleid-beheer hulpprogramma's:

1.  Open de groepsbeleid-beheer hulpprogramma's.
2.  Bewerk het groeps beleid dat wordt toegepast op alle gebruikers. Bijvoorbeeld het standaard domein beleid.
3.  Ga naar **gebruikers configuratie**  >  **Beheersjablonen**  >  **Windows-onderdelen**  >  **Internet Explorer** Internet  >  **configuratie scherm**  >  **beveiligings pagina**. Selecteer vervolgens de **lijst site naar zone toewijzing**.
4.  Schakel het beleid in. Voer vervolgens in het dialoog venster de waarde naam `https://autologon.microsoftazuread-sso.com` en waarde van in `1` . Uw installatie kopie moet er als volgt uitzien.
  
    ![Scherm opname met intranet zones.](./media/how-to-connect-install-custom/sitezone.png)

6.  Selecteer twee keer op **OK** .

## <a name="configuring-federation-with-ad-fs"></a>Federatie met AD FS configureren
U kunt in slechts enkele klikken AD FS met Azure AD Connect configureren. Voordat u begint, hebt u het volgende nodig:

* Windows Server 2012 R2 of hoger voor de federatieve server. Extern beheer moet zijn ingeschakeld.
* Windows Server 2012 R2 of hoger voor de Web Application proxy-server. Extern beheer moet zijn ingeschakeld.
* Een TLS/SSL-certificaat voor de naam van de Federation-service die u wilt gebruiken (bijvoorbeeld sts.contoso.com).

>[!NOTE]
>U kunt een TLS/SSL-certificaat voor uw AD FS-farm bijwerken met behulp van Azure AD Connect, zelfs als u deze niet gebruikt voor het beheren van uw federatieve vertrouwens relatie.

### <a name="ad-fs-configuration-prerequisites"></a>Vereisten voor AD FS configuratie
Als u uw AD FS-farm wilt configureren met behulp van Azure AD Connect, moet u ervoor zorgen dat WinRM is ingeschakeld op de externe servers. Zorg ervoor dat u de andere taken in [vereisten voor Federatie](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration)hebt voltooid. Zorg er ook voor dat u de poort vereisten volgt die worden vermeld in de tabel [Azure AD Connect en Federatie/WAP-servers](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap) .

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Maak een nieuwe AD FS-farm aan of gebruik een bestaande AD FS-farm
U kunt een bestaande AD FS-farm gebruiken of een nieuwe maken. Als u ervoor kiest om een nieuw item te maken, moet u het TLS/SSL-certificaat opgeven. Als het TLS/SSL-certificaat wordt beveiligd met een wacht woord, wordt u gevraagd het wacht woord op te geven.

![Scherm afbeelding van de pagina A D F S Farm](./media/how-to-connect-install-custom/adfs1.png)

Als u ervoor kiest om een bestaande AD FS-Farm te gebruiken, ziet u de pagina waar u de vertrouwens relatie tussen AD FS en Azure AD kunt configureren.

>[!NOTE]
>U kunt Azure AD Connect gebruiken om slechts één AD FS Farm te beheren. Als u een bestaande federatieve vertrouwens relatie hebt waarbij Azure AD is geconfigureerd op de geselecteerde AD FS Farm, Azure AD Connect de vertrouwens relatie opnieuw te maken.

### <a name="specify-the-ad-fs-servers"></a>Geef de AD FS-servers op
Geef de servers op waarop u AD FS wilt installeren. U kunt een of meer servers toevoegen, afhankelijk van de capaciteits behoeften. Voordat u deze configuratie instelt, moet u alle AD FS-servers toevoegen aan Active Directory. Deze stap is niet vereist voor de Web Application proxy-servers. 

Het wordt door Microsoft aangeraden om voor proefimplementaties één AD FS-server te installeren. Na de eerste configuratie kunt u meer servers toevoegen en implementeren om te voldoen aan uw schaal behoeften door Azure AD Connect opnieuw uit te voeren.

> [!NOTE]
> Voordat u deze configuratie instelt, moet u ervoor zorgen dat al uw servers zijn gekoppeld aan een Azure AD-domein.
>


![Scherm opname van de pagina ' Federatie servers '.](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Geef de webtoepassingsproxyservers op
Geef uw Web Application proxy-servers op. De Web Application proxy-server wordt geïmplementeerd in het perimeter netwerk, gericht op het extranet. Het ondersteunt verificatie aanvragen van het extranet. U kunt een of meer servers toevoegen, afhankelijk van de capaciteits behoeften. 

Micro soft raadt u aan om één Web Application proxy-server te installeren voor test-en proef implementaties. Na de eerste configuratie kunt u meer servers toevoegen en implementeren om te voldoen aan uw schaal behoeften door Azure AD Connect opnieuw uit te voeren. Het is raadzaam dat u een gelijkwaardig aantal proxy servers hebt om te voldoen aan verificatie van het intranet.

> [!NOTE]
> - Als het account dat u gebruikt geen lokale beheerder is op de Web Application proxy-servers, wordt u gevraagd om beheerders referenties.
> - Voordat u Web Application proxy-servers opgeeft, moet u ervoor zorgen dat er een HTTP/HTTPS-verbinding is tussen de Azure AD Connect-server en de Web Application proxy-server.
> - Zorg ervoor dat er een HTTP/HTTPS-verbinding tussen de webtoepassingsserver en de AD FS-server is om verificatie aanvragen toe te staan door te lopen.
>


![Scherm opname van de pagina Web Application proxy-servers.](./media/how-to-connect-install-custom/adfs3.png)

U wordt gevraagd om referenties in te voeren, zodat de Web Application Server een beveiligde verbinding met de AD FS server tot stand kan brengen. Deze referenties moeten voor een lokaal beheerders account op de AD FS server zijn.

![Scherm opname met de pagina referenties. Beheerders referenties worden ingevoerd in het veld username en het veld wacht woord.](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Geef het serviceaccount voor de AD FS-service op
De AD FS-service vereist een domein service account voor het verifiëren van gebruikers en het opzoeken van gebruikers gegevens in Active Directory. De service kan twee soorten serviceaccounts ondersteunen:

* Door de **groep beheerde service account**: dit account type is geïntroduceerd in AD DS door Windows Server 2012. Dit type account biedt services als AD FS. Het is een enkel account waarin u het wacht woord niet regel matig hoeft bij te werken. Gebruik deze optie als u al Windows Server 2012-domeincontrollers hebt in het domein waarbij uw AD FS-servers horen.
* **Domein gebruikers account**: dit type account vereist dat u een wacht woord opgeeft en het regel matig bijwerkt wanneer het verloopt. Gebruik deze optie alleen als u niet beschikt over Windows Server 2012-domein controllers in het domein waartoe uw AD FS-servers behoren.

Als u **een beheerd service account voor een groep maken** hebt geselecteerd en deze functie nooit is gebruikt in Active Directory, voert u de referenties voor uw ondernemings Administrator in. Deze referenties worden gebruikt om de sleutelopslagplaats te beginnen en de functie in Active Directory in te schakelen.

> [!NOTE]
> Azure AD Connect controleert of de AD FS-service al is geregistreerd als een Service Principal Name (SPN) in het domein.  Azure AD DS staat niet toe dat dubbele Spn's tegelijk worden geregistreerd.  Als er een dubbele SPN wordt gevonden, kunt u verder gaan totdat de SPN wordt verwijderd.

![Scherm afbeelding van de pagina A D F S service-account.](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-want-to-federate"></a>Selecteer het Azure AD-domein dat u wilt laten communiceren
Gebruik de pagina **Azure AD-domein** om de Federatie relatie tussen AD FS en Azure AD in te stellen. Hier configureert u AD FS voor het leveren van beveiligings tokens aan Azure AD. U kunt Azure AD ook zo configureren dat de tokens van dit AD FS exemplaar worden vertrouwd. 

Op deze pagina kunt u slechts één domein configureren tijdens de eerste installatie. U kunt later meer domeinen configureren door Azure AD Connect nogmaals uit te voeren.

![Scherm opname van de pagina ' Azure A D domein '.](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Controleer het Azure AD-domein dat voor federatie is geselecteerd
Wanneer u het domein selecteert dat u wilt deheffen, Azure AD Connect geeft u informatie op die u kunt gebruiken om een niet-geverifieerd domein te controleren. Zie voor meer informatie [toevoegen en verifiëren van het domein](../fundamentals/add-custom-domain.md).

![Scherm opname van de pagina ' Azure A D domein ', met inbegrip van informatie die u kunt gebruiken om het domein te verifiëren.](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> Azure AD Connect probeert het domein te verifiëren tijdens de configuratie fase. Als u de benodigde Domain Name System (DNS)-records niet toevoegt, kan de configuratie niet worden voltooid.
>


## <a name="configuring-federation-with-pingfederate"></a>Federatie configureren met PingFederate
U kunt met slechts enkele klikken PingFederate configureren met Azure AD Connect. De volgende vereisten zijn vereist:
- PingFederate 8,4 of hoger.  Zie [PingFederate-integratie met Azure Active Directory en Microsoft 365](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html)voor meer informatie.
- Een TLS/SSL-certificaat voor de naam van de Federation-service die u wilt gebruiken (bijvoorbeeld sts.contoso.com).

### <a name="verify-the-domain"></a>Het domein verifiëren
Nadat u ervoor hebt gekozen om Federatie in te stellen met behulp van PingFederate, wordt u gevraagd om het domein te controleren dat u wilt gebruiken.  Selecteer het domein in de vervolg keuzelijst.

![Scherm opname van de pagina ' Azure A D domein '. Het voorbeeld domein ' contoso.com ' is geselecteerd.](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>De PingFederate-instellingen exporteren


Configureer PingFederate als de Federatie server voor elk federatieve Azure-domein.  Selecteer **instellingen exporteren** om deze informatie te delen met uw PingFederate-beheerder.  De beheerder van de Federatie Server werkt de configuratie bij en geeft vervolgens de URL en het poort nummer van de PingFederate-server, zodat Azure AD Connect de meta gegevens instellingen kunt controleren.  

![Scherm afbeelding met de pagina ' PingFederate-instellingen '. De knop instellingen exporteren wordt boven aan de pagina weer gegeven.](./media/how-to-connect-install-custom/ping2.png)

Neem contact op met de beheerder van PingFederate als er validatieproblemen zijn.  De volgende afbeelding toont informatie over een PingFederate-server die geen geldige vertrouwens relatie heeft met Azure.

![Scherm opname van de server informatie: de PingFederate-server is gevonden, maar de service provider verbinding voor Azure ontbreekt of is uitgeschakeld.](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>Federatieve connectiviteit verifiëren
Azure AD Connect probeert de verificatie-eind punten te valideren die worden opgehaald uit de PingFederate-meta gegevens in de vorige stap.  Azure AD Connect eerste pogingen om de eind punten op te lossen met behulp van uw lokale DNS-servers.  Vervolgens wordt geprobeerd de eind punten op te lossen met behulp van een externe DNS-provider.  Neem contact op met de beheerder van PingFederate als er validatieproblemen zijn.  

![Scherm opname met de pagina connectiviteit controleren.](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-sign-in"></a>Federatie aanmelding controleren
Ten slotte kunt u de zojuist geconfigureerde federatieve aanmeldingsstroom verifiëren door u aan te melden bij het federatieve domein. Als de aanmelding is gelukt, is de Federatie met PingFederate geconfigureerd.

![Scherm afbeelding van de pagina "federatieve aanmelding controleren". Een bericht aan de onderkant geeft aan dat het aanmelden is geslaagd.](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>Configureer en verifieer pagina 's
De configuratie vindt plaats op de pagina **configureren** .

> [!NOTE]
> Als u Federatie hebt geconfigureerd, moet u ervoor zorgen dat u ook de [naam omzetting voor Federatie servers](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers) hebt geconfigureerd voordat u doorgaat met de installatie.
>



![Scherm afbeelding met de pagina gereed om te configureren.](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="use-staging-mode"></a>Faserings modus gebruiken
Het is mogelijk om een nieuwe synchronisatie server parallel in te stellen met de faserings modus. Als u deze installatie wilt gebruiken, kan slechts één synchronisatie server exporteren naar één map in de Cloud. Maar als u wilt overstappen van een andere server, bijvoorbeeld een server met DirSync, kunt u Azure AD Connect inschakelen in de faserings modus. 

Wanneer u de faserings installatie inschakelt, wordt de synchronisatie-engine geïmporteerd en worden de gegevens als normaal gesynchroniseerd. Maar er worden geen gegevens naar Azure AD of Active Directory geëxporteerd. In de faserings modus worden de functie voor wachtwoord synchronisatie en de functie voor het terugschrijven van wacht woorden uitgeschakeld.

![Scherm afbeelding met de optie faserings modus inschakelen.](./media/how-to-connect-install-custom/stagingmode.png)

In de faserings modus kunt u de synchronisatie-engine vereiste wijzigingen aanbrengen en controleren wat er wordt geëxporteerd. Voer de installatiewizard opnieuw uit en schakel de faseringsmodus uit wanneer de configuratie er goed uitziet. 

Gegevens worden nu naar Azure AD geëxporteerd vanaf de-server. Schakel de andere server op hetzelfde moment uit, zodat slechts één server actief aan het exporteren is.

Zie voor meer informatie [Staging mode](how-to-connect-sync-staging-server.md).

### <a name="verify-your-federation-configuration"></a>Controleer uw federatieconfiguratie
Azure AD Connect verifieert de DNS-instellingen wanneer u de knop **verifiëren** selecteert. Hiermee worden de volgende instellingen gecontroleerd:

* **Intranet verbinding**
    * Federatieve FQDN omzetten: Azure AD Connect controleert of de Federatie-FQDN kan worden omgezet met DNS om verbinding te kunnen garanderen. Als Azure AD Connect de FQDN niet kunt omzetten, mislukt de verificatie. Zorg ervoor dat er een DNS-record aanwezig is voor de FQDN van de Federation service om de verificatie te volt ooien.
    * DNS A-record: Azure AD Connect controleert of uw Federation service een record bevat. Als een record ontbreekt, mislukt de verificatie. U kunt de verificatie volt ooien door een A-record (geen CNAME-record) te maken voor uw Federatie-FQDN.
* **Extranet connectiviteit**
    * Federatieve FQDN omzetten: Azure AD Connect controleert of de Federatie-FQDN kan worden omgezet met DNS om verbinding te kunnen garanderen.

      ![Scherm afbeelding met de pagina installatie voltooid.](./media/how-to-connect-install-custom/completed.png)

      ![Scherm afbeelding met de pagina installatie voltooid. Een bericht geeft aan dat de intranet configuratie is gecontroleerd.](./media/how-to-connect-install-custom/adfs7.png)

Als u end-to-end-verificatie wilt valideren, voert u hand matig een of meer van de volgende tests uit:

* Wanneer de synchronisatie is voltooid, gebruikt u in Azure AD Connect de aanvullende taak **federatieve aanmelding verifiëren** om verificatie te verifiëren voor een on-premises gebruikers account dat u kiest.
* Zorg ervoor dat u zich vanuit een computer die lid is van een domein op het intranet kunt aanmelden vanuit een browser. Verbinding maken met https://myapps.microsoft.com . Gebruik vervolgens uw aangemelde account om de aanmelding te controleren. Het ingebouwde beheerders account van Azure AD DS is niet gesynchroniseerd en u kunt het niet gebruiken voor verificatie.
* Zorg ervoor dat u zich kunt aanmelden vanaf een apparaat op het extranet. Maak op een computer thuis of op een mobiel apparaat verbinding met https://myapps.microsoft.com . Geef vervolgens uw referenties op.
* Aanmelding uitgebreide client controleren. Verbinding maken met https://testconnectivity.microsoft.com . Selecteer vervolgens de test **Office 365**  >  **Office 365 single Sign-On**.

## <a name="troubleshoot"></a>Problemen oplossen
Deze sectie bevat informatie over het oplossen van problemen die u kunt gebruiken als u een probleem ondervindt tijdens de installatie van Azure AD Connect.

Wanneer u een Azure AD Connect-installatie aanpast, kunt u op de pagina **vereiste onderdelen installeren** de optie **een bestaande SQL Server gebruiken** selecteren. Mogelijk wordt de volgende fout weer gegeven: ' de ADSync-Data Base bevat al gegevens en kan niet worden overschreven. Verwijder de bestaande data base en probeer het opnieuw. "

![Scherm afbeelding met de pagina vereiste onderdelen installeren. Onder aan de pagina wordt een fout weer gegeven.](./media/how-to-connect-install-custom/error1.png)

U ziet deze fout omdat er al een Data Base met de naam *ADSync* bestaat in het SQL-exemplaar van SQL Server dat u hebt opgegeven.

Normaal gesp roken ziet u deze fout nadat u Azure AD Connect hebt verwijderd.  De data base wordt niet verwijderd van de computer waarop SQL Server wordt uitgevoerd wanneer u Azure AD Connect verwijdert.

U kunt dit probleem als volgt oplossen:

1. Controleer de ADSync-data base die Azure AD Connect gebruikt voordat deze werd verwijderd. Zorg ervoor dat de data base niet meer wordt gebruikt.

2. Maak een back-up van de data base.

3. De data base verwijderen:
    1. Gebruik **Microsoft SQL Server Management Studio** om verbinding te maken met het SQL-exemplaar. 
    1. Zoek de **ADSync** -data base en klik er met de rechter muisknop op.
    1. Selecteer **verwijderen** in het context menu.
    1. Selecteer **OK** om de data base te verwijderen.

![Scherm opname met Microsoft SQL Server Management Studio. Er is een D-synchronisatie geselecteerd.](./media/how-to-connect-install-custom/error2.png)

Nadat u de ADSync-Data Base hebt verwijderd, selecteert u **installeren** om de installatie opnieuw uit te voeren.

## <a name="next-steps"></a>Volgende stappen
Nadat de installatie is voltooid, meldt u zich af bij Windows. Meld u vervolgens opnieuw aan voordat u Synchronization Service Manager of de editor voor synchronisatie regels gebruikt.

Nu u Azure AD Connect hebt geïnstalleerd, kunt u [de installatie verifiëren en licenties toewijzen](how-to-connect-post-installation.md).

Zie voor meer informatie over de functies die u tijdens de installatie hebt ingeschakeld, [onbedoeld verwijderen voor komen](how-to-connect-sync-feature-prevent-accidental-deletes.md) en [Azure AD Connect Health](how-to-connect-health-sync.md).

Zie [Azure AD Connect Sync: scheduler](how-to-connect-sync-feature-scheduler.md) en [uw on-premises identiteiten integreren met Azure AD](whatis-hybrid-identity.md)voor meer informatie over andere algemene onderwerpen.
