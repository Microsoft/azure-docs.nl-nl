---
title: Azure MFA-server en Active Directory-Azure Active Directory
description: Hier wordt beschreven hoe u de Azure Multi-Factor Authentication-server kunt integreren met Active Directory zodat u de mappen kunt synchroniseren.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b3778ea68edf1fbbb41efb899749e6f35b39bae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742286"
---
# <a name="directory-integration-between-azure-mfa-server-and-active-directory"></a>Adreslijstintegratie tussen Azure MFA-server en Active Directory

Gebruik de sectie Adreslijstintegratie van de Azure MFA-server voor integratie met Active Directory of een andere LDAP-directory. U kunt kenmerken configureren zodat deze overeenkomen met het Active Directory-schema en automatische gebruikerssynchronisatie instellen.

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt micro soft geen MFA-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication (MFA) willen vereisen tijdens aanmeldings gebeurtenissen, moeten gebruikmaken van Azure AD-Multi-Factor Authentication in de Cloud.
>
> Zie [zelf studie: aanmeldings gebeurtenissen voor gebruikers beveiligen met Azure AD multi-factor Authentication](tutorial-enable-azure-mfa.md)om aan de slag te gaan met MFA op basis van de Cloud.
>
> Bestaande klanten die MFA-server vóór 1 juli hebben geactiveerd 2019, kunnen de meest recente versie downloaden, toekomstige updates en activerings referenties genereren.

## <a name="settings"></a>Instellingen

Standaard is de Azure MFA-server (Multi-Factor Authentication) geconfigureerd om de gebruikers van Active Directory te importeren of te synchroniseren.  Op het tabblad Directory-integratie kunt u het standaardgedrag negeren en een binding maken met een andere LDAP-directory, een ADAM-directory of specifieke Active Directory-domeincontroller.  U kunt hier ook LDAP-verificatie gebruiken om een proxy op LDAP uit te voeren of LDAP-binding gebruiken als een RADIUS-doel, pre-authenticatie voor IIS-authenticatie gebruiken of primaire authenticatie voor de gebruikersportal gebruiken.  De volgende tabel beschrijft de afzonderlijke instellingen.

![LDAP-configuratie in MFA-server bewerken](./media/howto-mfaserver-dir-ad/dirint.png)

> [!NOTE]
> Directory-integratie is niet gegarandeerd om te werken met andere mappen dan Active Directory Domain Services.

| Functie | Beschrijving |
| --- | --- |
| Active Directory gebruiken |Selecteer de optie Active Directory gebruiken om Active Directory te gebruiken voor het importeren en synchroniseren.  Dit is de standaardinstelling. <br>Opmerking: koppel de computer aan een domein en meld u aan met een domeinaccount om ervoor te zorgen dat Active Directory-integratie goed werkt. |
| Vertrouwde domeinen opnemen |Schakel **Vertrouwde domeinen opnemen** in om met de agent verbindingspogingen uit te voeren met domeinen die door het huidige domein worden vertrouwd, met een ander domein in het forest of met domeinen die zijn betrokken in een forestvertrouwensrelatie.  Wanneer u geen gebruikers uit een van de vertrouwde domeinen importeert of synchroniseert, schakelt u het selectievakje uit om de prestaties te verbeteren.  Standaard is dit selectievakje ingeschakeld. |
| Specifieke LDAP-configuratie gebruiken |Selecteer de optie Specifieke LDAP-configuratie gebruiken om de LDAP-instellingen te gebruiken die voor het importeren en synchroniseren zijn opgegeven. Opmerking: als Specifieke LDAP-configuratie gebruiken is geselecteerd, verandert de gebruikersinterface verwijzingen van Active Directory in LDAP. |
| Knop Bewerken |Met de knop Bewerken kunt de huidige LDAP-configuratie-instellingen wijzigen. |
| Query's voor kenmerkbereik gebruiken |Geeft aan of query's voor het kenmerkbereik moeten worden gebruikt.  Met query's voor het kenmerkbereik kan efficiënt in directory's worden gezocht naar in aanmerking komende records op basis van de vermeldingen in het kenmerk van een andere record.  De Azure Multi-Factor Authentication-server gebruikt query's voor het kenmerkbereik om een query uit te voeren naar de gebruikers die lid zijn van een beveiligingsgroep.   <br>Opmerking: er zijn enkele gevallen waarbij query's voor het kenmerkbereik worden ondersteund, maar niet mogen worden gebruikt.  Active Directory kan bijvoorbeeld problemen met query's voor het kenmerkbereik hebben wanneer een beveiligingsgroep leden uit meer dan één domein bevat. Schakel in dit geval het selectievakje uit. |

De volgende tabel beschrijft de LDAP-configuratie-instellingen.

| Functie | Beschrijving |
| --- | --- |
| Server |Geef de hostnaam of het IP-adres van de server op waarop de LDAP-directory wordt uitgevoerd.  U kunt ook een back-upserver opgeven, gescheiden door een puntkomma. <br>Opmerking: wanneer het bindings type SSL (TLS) is, is een volledig gekwalificeerde hostnaam vereist. |
| Basis-DN |Geef de DN-naam op van het basisdirectory-object waaruit alle directoryquery's starten.  Bijvoorbeeld: dc=abc, dc=com. |
| Bindingstype query |Selecteer het geschikte bindingstype voor gebruik bij het binden om de LDAP-directory te zoeken.  Dit wordt gebruikt voor import, synchronisatie en gebruikersnaamomzetting. <br><br>  Anoniem: Er wordt een anonieme binding uitgevoerd.  Bindings-DN en bindingswachtwoord worden niet gebruikt.  Dit werkt alleen als de LDAP-directory anonieme binding toestaat en als machtigingen toestaan dat query's naar de betreffende records en kenmerken worden uitgevoerd.  <br><br> Eenvoudig: Bindings-DN en bindingswachtwoord worden doorgegeven als tekst zonder opmaak om een binding te maken met de LDAP-directory.  Dit is bedoeld voor testdoeleinden om te controleren of de server bereikbaar is en of het bindingsaccount de juiste toegang heeft. Nadat het juiste certificaat is geïnstalleerd, gebruikt u voortaan SSL.  <br><br> SSL: Bindings-DN en bindingswachtwoord worden versleuteld met behulp van SSL om een binding te maken met de LDAP-directory.  Installeer lokaal een certificaat dat wordt vertrouwd in de LDAP-directory.  <br><br> Windows: Bind gebruikersnaam en Bindingswachtwoord worden gebruikt om een veilige verbinding te maken met een Active Directory-domeincontroller of ADAM-directory.  Als Bind gebruikersnaam niet wordt ingevuld, wordt voor de binding het aangemelde gebruikersaccount gebruikt. |
| Bindingstype - Verificaties |Selecteer het geschikte bindingstype voor gebruik bij het uitvoeren van de verificatie van LDAP-bindingen.  Zie de beschrijvingen van de bindingstypen onder Bindingstype query.  Hierdoor kan bijvoorbeeld een anonieme binding worden gebruikt voor query's terwijl voor het beveiligen van verificaties van LDAP-bindingen een SSL-binding wordt gebruikt. |
| Bindings-DN of Bind gebruikersnaam |Geef de DN-naam van de gebruikersrecord op voor het account dat moet worden gebruikt bij het maken van een binding met de LDAP-directory.<br><br>De Bindings-DN wordt alleen gebruikt wanneer het bindingstype Eenvoudig of SSL is.  <br><br>Geef de gebruikersnaam van het Windows-account op dat moet worden gebruikt bij het maken van een binding met de LDAP-directory wanneer het bindingstype Windows is.  Als dit veld niet wordt ingevuld, wordt het aangemelde gebruikersaccount gebruikt om de binding te maken. |
| Bindingswachtwoord |Voer het bindingswachtwoord in voor de Bindings-DN of gebruikersnaam die wordt gebruikt om een binding te maken met de LDAP-directory.  Schakel synchronisatie in en zorg ervoor dat de service actief is op de lokale computer om het wachtwoord voor de Multi-Factor Authentication Server-service AdSync te configureren.  Het wachtwoord wordt opgeslagen bij de gebruikersnamen en wachtwoorden in Windows onder het account waarmee de Multi-Factor Authentication Server-service AdSync wordt uitgevoerd.  Het wachtwoord wordt ook opgeslagen onder het account waarmee de gebruikersinterface van Multi-Factor Authentication-server wordt uitgevoerd, en het account waarmee de service van de Multi-Factor Authentication-server wordt uitgevoerd.  <br><br>Omdat het wachtwoord alleen wordt opgeslagen bij gebruikersnamen en wachtwoorden in Windows van de lokale server, moet deze stap worden uitgevoerd op elke Multi-Factor Authentication-server die toegang tot het wachtwoord nodig heeft. |
| Maximale grootte query |Geef de maximale grootte op voor het maximum aantal gebruikers dat bij een directoryzoekopdracht wordt geretourneerd.  Deze limiet moet overeenkomen met de configuratie in de LDAP-directory.  Voor grote zoekopdrachten waarbij wisselgeheugengebruik niet wordt ondersteund, wordt bij het importeren en synchroniseren geprobeerd gebruikers in batches op te halen.  Als de maximale grootte hier hoger is dan de limiet die is geconfigureerd in de LDAP-directory, is het mogelijk dat sommige gebruikers ontbreken. |
| Knop Testen |Klik op de knop **Testen** om de binding met de LDAP-server te testen.  <br><br>U hoeft de optie **LDAP gebruiken** niet te selecteren om de binding te testen. Hiermee kan de binding worden getest voordat u de LDAP-configuratie gebruikt. |

## <a name="filters"></a>Filters

Met behulp van filters kunt u criteria instellen om records te kwalificeren bij het uitvoeren van een directoryzoekopdracht.  Door het filter in te stellen kunt u het bereik bepalen van de objecten die u wilt synchroniseren.  

![Adreslijst filters configureren in MFA server](./media/howto-mfaserver-dir-ad/dirint2.png)

Azure Multi-Factor Authentication heeft de volgende drie filteropties:

* **Containerfilter** - Geef de filtercriteria op die worden gebruikt om containerrecords te kwalificeren bij het uitvoeren van een directoryzoekopdracht.  Voor Active Directory en ADAM wordt meestal (|(objectClass=organizationalUnit)(objectClass=container)) gebruikt.  Voor andere LDAP-directory's gebruikt u filtercriteria die elk type containerobject kwalificeren, afhankelijk van het Active Directory-schema.  <br>Opmerking: als dit veld leeg blijft, wordt standaard ((objectClass=organizationalUnit)(objectClass=container)) gebruikt.
* **Beveiligingsgroepfilter** - Geef de filtercriteria op die worden gebruikt om beveiligingsgroeprecords te kwalificeren bij het uitvoeren van een directoryzoekopdracht.  Voor Active Directory en ADAM wordt meestal (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)) gebruikt.  Voor andere LDAP-directory's gebruikt u filtercriteria die elk type beveiligingsgroepsobject kwalificeren, afhankelijk van het Active Directory-schema.  <br>Opmerking: als dit veld leeg blijft, wordt standaard (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)) gebruikt.
* **Gebruikersfilter** - Geef de filtercriteria op die worden gebruikt om gebruikersrecords te kwalificeren bij het uitvoeren van een directoryzoekopdracht.  Voor Active Directory en ADAM wordt meestal (&(objectClass=user)(objectCategory=person)) gebruikt.  Voor andere LDAP-mappen gebruikt u (objectClass=inetOrgPerson) of iets soortgelijks, afhankelijk van het Active Directory-schema. <br>Opmerking: als dit veld leeg blijft, wordt standaard (&(objectCategory=person)(objectClass=user)) gebruikt.

## <a name="attributes"></a>Kenmerken

U kunt kenmerken, indien nodig, aanpassen voor een specifieke directory.  Hierdoor kunt u aangepaste kenmerken toevoegen en de synchronisatie afstemmen op alleen de kenmerken die u nodig hebt. Gebruik de naam van het kenmerk zoals gedefinieerd in het Directory-schema voor de waarde van elk kenmerk veld. De volgende tabel bevat aanvullende informatie over elke functie.

Kenmerken kunnen handmatig worden ingevoerd en hoeven niet overeen te komen met een kenmerk in de lijst met kenmerken.

![Adreslijst integratie kenmerken in MFA server aanpassen](./media/howto-mfaserver-dir-ad/dirint3.png)

| Functie | Beschrijving |
| --- | --- |
| Unieke id |Voer de kenmerknaam van het kenmerk in die fungeert als de unieke id van de container, beveiligingsgroep en gebruikersrecords.  In Active Directory is dit doorgaans objectGUID. Andere LDAP-implementaties maken mogelijk gebruik van entryUUID of iets soortgelijks.  De standaardwaarde is objectGUID. |
| Type unieke id |Selecteer het type van het kenmerk Unieke id.  In Active Directory heeft het kenmerk objectGUID het type GUID. Andere LDAP-implementaties maken mogelijk gebruik van het type ASCII-bytematrix of Tekenreeks.  De standaardwaarde is GUID. <br><br>Het is belangrijk dat u dit type juist instelt, omdat naar synchronisatie-items wordt verwezen via de bijbehorende id's. Het unieke id-type wordt gebruikt om het object rechtstreeks te vinden in de directory.  Wanneer dit type wordt ingesteld op Tekenreeks terwijl de directory de waarde eigenlijk als een bytematrix van ASCII-tekens opslaat, verloopt de synchronisatie niet juist. |
| DN-naam |Voer de kenmerknaam in van het kenmerk dat de DN-naam voor elke record bevat.  In Active Directory is dit doorgaans distinguishedName. Andere LDAP-implementaties maken mogelijk gebruik van entryDN of iets soortgelijks.  De standaardwaarde is distinguishedName. <br><br>Als er geen kenmerk met alleen de DN-naam bestaat, kan het Ads-pad kenmerk worden gebruikt.  Het gedeelte ' LDAP:// \<server\> /' van het pad wordt automatisch verwijderd, waardoor alleen de DN-naam van het object wordt gelaten. |
| Containernaam |Voer de kenmerknaam in van het kenmerk dat de naam in een containerrecord bevat.  De waarde van dit kenmerk wordt weergegeven in de containerhiërarchie bij het importeren vanuit Active Directory of bij het toevoegen van synchronisatie-items.  De standaardwaarde is name. <br><br>Als verschillende containers verschillende kenmerken voor namen gebruiken, gebruikt u puntkomma's om meerdere containernaamkenmerken te scheiden.  Het eerste containernaamkenmerk dat in een containerobject wordt gevonden, wordt gebruikt om de naam ervan weer te geven. |
| Naam beveiligingsgroep |Voer de kenmerknaam in van het kenmerk dat de naam in een beveiligingsgroepsrecord bevat.  De waarde van dit kenmerk wordt weergegeven in de lijst Beveiligingsgroep bij het importeren vanuit Active Directory of het toevoegen van synchronisatie-items.  De standaardwaarde is name. |
| Gebruikersnaam |Voer de kenmerknaam in van het kenmerk dat de gebruikersnaam in een gebruikersrecord bevat.  De waarde van dit kenmerk wordt gebruikt als de gebruikersnaam voor de Multi-Factor Authentication-server.  Een tweede kenmerk kan worden opgegeven als een back-up voor het eerste.  Het tweede kenmerk wordt alleen gebruikt als het eerste kenmerk geen waarde voor de gebruiker bevat.  De standaardwaarden zijn userPrincipalName en sAMAccountName. |
| Voornaam |Voer de kenmerknaam in van het kenmerk dat de voornaam in een gebruikersrecord bevat.  De standaardwaarde is givenName. |
| Achternaam |Voer de kenmerknaam in van het kenmerk dat de achternaam in een gebruikersrecord bevat.  De standaardwaarde is sn. |
| E-mailadres |Voer de kenmerknaam in van het kenmerk dat het e-mailadres in een gebruikersrecord bevat.  Het e-mailadres wordt gebruikt om via e-mail welkomstberichten en updateberichten naar de gebruiker te verzenden.  De standaardwaarde is mail. |
| Gebruikersgroep |Voer de kenmerknaam in van het kenmerk dat de gebruikersgroep in een gebruikersrecord bevat.  Gebruikersgroep kan worden gebruikt voor het filteren van gebruikers in de agent en in rapporten in de beheerportal van de Multi-Factor Authentication-server. |
| Beschrijving |Voer de kenmerknaam in van het kenmerk dat de beschrijving in een gebruikersrecord bevat.  Beschrijving wordt alleen gebruikt voor zoekopdrachten.  De standaardwaarde is description. |
| Taal telefoonoproep |Voer de kenmerknaam in van het kenmerk dat de korte naam bevat van de taal die moet worden gebruikt voor telefoonoproepen voor de gebruiker. |
| Taal sms-bericht |Voer de kenmerknaam in van het kenmerk dat de korte naam bevat van de taal die moet worden gebruikt voor sms-berichten voor de gebruiker. |
| Taal mobiele app |Voer de kenmerknaam in van het kenmerk dat de korte naam bevat van de taal die moet worden gebruikt voor tekstberichten van mobiele apps voor de gebruiker. |
| Taal OATH-token |Voer de kenmerknaam in van het kenmerk dat de korte naam bevat van de taal die moet worden gebruikt voor sms-berichten met een OATH-token voor de gebruiker. |
| Telefoon (werk) |Voer de kenmerknaam in van het kenmerk dat het zakelijke telefoonnummer in een gebruikersrecord bevat.  De standaardwaarde is telephoneNumber. |
| Telefoon (thuis) |Voer de kenmerknaam in van het kenmerk dat het persoonlijke telefoonnummer thuis in een gebruikersrecord bevat.  De standaardwaarde is homePhone. |
| Pager |Voer de kenmerknaam in van het kenmerk dat het pagernummer in een gebruikersrecord bevat.  De standaardwaarde is pager. |
| Mobiele telefoon |Voer de kenmerknaam in van het kenmerk dat het mobiel telefoonnummer in een gebruikersrecord bevat.  De standaardwaarde is mobile. |
| Fax |Voer de kenmerknaam in van het kenmerk dat het faxnummer in een gebruikersrecord bevat.  De standaardwaarde is facsimileTelephoneNumber. |
| IP-telefoon |Voer de kenmerknaam in van het kenmerk dat het IP-telefoonnummer in een gebruikersrecord bevat.  De standaardwaarde is ipPhone. |
| Aangepast |Voer de kenmerknaam in van het kenmerk dat een aangepast telefoonnummer in een gebruikersrecord bevat.  Standaard is dit veld leeg. |
| Toestelnummer |Voer de kenmerknaam in van het kenmerk dat het toestelnummer in een gebruikersrecord bevat.  De waarde van het veld Toestelnummer wordt alleen gebruikt als het toestelnummer voor het primaire telefoonnummer.  Standaard is dit veld leeg. <br><br>Als het kenmerk Toestelnummer niet wordt opgegeven, kunnen toestelnummers worden opgenomen als onderdeel van het telefoonkenmerk. In dit geval begint u het toestelnummer met een x zodat het juist wordt geparseerd.  Het nummer 020-123-4567 x890 resulteert bijvoorbeeld in 020-123-4567 als het telefoonnummer en 890 als het toestelnummer. |
| Knop Standaardwaarden herstellen |Klik op **Standaardwaarden herstellen** om alle kenmerken opnieuw in te stellen op hun standaardwaarde.  De standaardinstellingen werken doorgaans juist met het normale Active Directory- of ADAM-schema. |

Als u kenmerken wilt bewerken, klikt u op het tabblad kenmerken op **bewerken** .  Hiermee wordt een venster geopend waarin u de kenmerken kunt bewerken. Selecteer de **...** naast een willekeurig kenmerk om een venster te openen waarin u kunt kiezen welke kenmerken moeten worden weergegeven.

![Toewijzing van adreslijst kenmerk in MFA-server bewerken](./media/howto-mfaserver-dir-ad/dirint4.png)

## <a name="synchronization"></a>Synchronisatie

Synchronisatie houdt de Azure MFA-gebruikersdatabase gesynchroniseerd met de gebruikers in Active Directory of een andere LDAP-directory (Lightweight Directory Access Protocol). Het proces is vergelijkbaar met het handmatig importeren van gebruikers uit Active Directory, maar pollt periodiek naar te verwerken wijzigingen in Active Directory-gebruikers en -beveiligingsgroepen.  Ook worden gebruikers ingeschakeld of verwijderd die zijn verwijderd uit een container, beveiligingsgroep of Active Directory.

De Multi-Factor Authentication-service ADSync is een Windows-service die de periodieke polling van Active Directory uitvoert.  Verwar dit niet met Azure AD Sync of Azure AD Connect.  Hoewel de Multi-Factor Authentication-service ADSync is gebouwd op een vergelijkbare codebasis, is deze service specifiek voor de Azure Multi-Factor Authentication-server.  Deze service wordt geïnstalleerd met een status Gestopt en wordt gestart door de service van de Multi-Factor Authentication-server wanneer deze is geconfigureerd om te worden uitgevoerd.  Als u een Multi-Factor Authentication-serverconfiguratie met meerdere servers hebt, kan de Multi-Factor Authentication-service ADSync slechts op één server worden uitgevoerd.

De Multi-Factor Authentication-service ADSync maakt gebruik van de DirSync LDAP-serverextensie die door Microsoft wordt geleverd om efficiënt polls naar wijzigingen uit te voeren.  De aanroeper van dit DirSync-besturingselement moet beschikken over het recht 'directory get changes' (directorywijzigingen ophalen) en het toegangsrecht DS-Replication-Get-Changes voor uitgebreide controles.  Standaard worden deze rechten toegewezen aan de accounts Administrator en LocalSystem op domeincontrollers.  De Multi-Factor Authentication-service AdSync is geconfigureerd om standaard te worden uitgevoerd als LocalSystem.  Daarom kan de service het gemakkelijkst op een domeincontroller worden uitgevoerd.  Als u de service configureert om altijd een volledige synchronisatie uit te voeren, kan deze worden uitgevoerd als een account met minder machtigingen.  Dit is minder efficiënt, maar vereist minder accountmachtigingen.

Als de LDAP-directory ondersteuning biedt en is geconfigureerd voor DirSync, wordt een poll naar wijzigingen in gebruikers en groepsbeveiligingen op dezelfde manier uitgevoerd als bij Active Directory.  Als de LDAP-directory geen ondersteuning biedt voor het besturingselement DirSync, wordt bij elke cyclus een volledige synchronisatie uitgevoerd.

![Synchronisatie van Directory-objecten naar MFA-server](./media/howto-mfaserver-dir-ad/dirint5.png)

De volgende tabel bevat aanvullende informatie over elk van de instellingen op het tabblad Synchronisatie.

| Functie | Beschrijving |
| --- | --- |
| Synchronisatie met Active Directory inschakelen |Als deze optie wordt ingeschakeld, wordt de service van de Multi-Factor Authentication-server gestart om periodiek een poll naar wijzigingen in Active Directory uit te voeren. <br><br>Opmerking: ten minste één synchronisatie-item moet worden toegevoegd en Nu synchroniseren moet worden uitgevoerd voordat de service van de Multi-Factor Authentication-server wijzigingen begint te verwerken. |
| Synchronisatie-interval |Geef op hoelang de service van de Multi-Factor Authentication-server moet wachten tussen het uitvoeren van een poll en het verwerken van wijzigingen. <br><br> Opmerking: Het opgegeven interval is de tijd tussen het begin van elke cyclus.  Als de tijd voor het verwerken van wijzigingen groter is dan het interval, wordt met de service onmiddellijk opnieuw een poll uitgevoerd. |
| Gebruikers verwijderen die niet meer in Active Directory zijn |Als deze optie wordt ingeschakeld, zal de service van de Multi-Factor Authentication-server verwijderde Active Directory-tombstones van gebruikers verwerken en de gerelateerde Multi-Factor Authentication-servergebruiker verwijderen. |
| Altijd een volledige synchronisatie uitvoeren |Als dit selectievakje wordt ingeschakeld, zal de service van de Multi-Factor Authentication-server altijd een volledige synchronisatie uitvoeren.  Als dit selectievakje wordt uitgeschakeld, zal de service van de Multi-Factor Authentication-server een incrementele synchronisatie uitvoeren door alleen een query naar gewijzigde gebruikers uit te voeren.  De standaardwaarde is uitgeschakeld. <br><br>Als deze optie is uitgeschakeld, wordt er alleen een incrementele synchronisatie uitgevoerd met Azure MFA wanneer de directory ondersteuning biedt voor het besturingselement DirSync en wanneer het account waarmee een directorybinding wordt gemaakt, machtigingen heeft voor het uitvoeren van incrementele DirSync-query's.  Als het account niet de juiste machtigingen heeft of er meerdere domeinen bij de synchronisatie zijn betrokken, wordt met Azure MFA een volledige synchronisatie uitgevoerd. |
| Goedkeuring door beheerder vereisen wanneer het aantal uitgeschakelde of verwijderde gebruikers de drempelwaarde overschrijdt |Synchronisatie-items kunnen worden geconfigureerd voor het uitschakelen of verwijderen van gebruikers die niet langer lid zijn van de container of de beveiligingsgroep van het item.  Als voorzorgsmaatregel kan de goedkeuring door een beheerder worden vereist wanneer het aantal uit te schakelen of te verwijderen gebruikers een drempelwaarde overschrijdt.  Als deze optie wordt ingeschakeld, is goedkeuring vereist voor de opgegeven drempelwaarde.  De standaardwaarde is 5 en het bereik gaat van 1 tot 999. <br><br> Goedkeuring wordt vergemakkelijkt door eerst een e-mailmelding naar beheerders te verzenden. De e-mailmelding biedt instructies voor het controleren en goedkeuren van de uitschakeling en verwijdering van gebruikers.  Wanneer de gebruikersinterface van de Multi-Factor Authentication-server wordt gestart, wordt om goedkeuring gevraagd. |

Met de knop **Nu synchroniseren** kunt u een volledige synchronisatie uitvoeren voor de opgegeven synchronisatie-items.  Een volledige synchronisatie is vereist wanneer synchronisatie-items worden toegevoegd, gewijzigd, verwijderd of opnieuw gerangschikt.  Deze is ook vereist voordat de Multi-Factor Authentication-service AdSync operationeel is, omdat bij deze synchronisatie het beginpunt wordt ingesteld op basis waarvan de service een poll naar incrementele wijzigingen uitvoert.  Als er wijzigingen aan synchronisatie-items zijn aangebracht, maar er nog geen volledige synchronisatie is uitgevoerd, wordt u gevraagd om Nu synchroniseren te kiezen.

Met de knop **Verwijderen** kan de beheerder een of meer synchronisatie-items uit de itemlijst van de Multi-Factor Authentication-server verwijderen.

> [!WARNING]
> Nadat een synchronisatie-itemrecord is verwijderd, kan het niet worden hersteld. Als u het per ongeluk hebt verwijderd, moet u het synchronisatie-itemrecord opnieuw toevoegen.

De synchronisatie-items worden verwijderd van de Multi-Factor Authentication-server.  De service van de Multi-Factor Authentication-server zal de synchronisatie-items niet langer verwerken.

Met de knoppen Omhoog en Omlaag kan de beheerder de volgorde van de synchronisatie-items wijzigen.  De volgorde is belangrijk omdat dezelfde gebruiker lid kan zijn van meer dan één synchronisatie-item (bijvoorbeeld van een container en een beveiligingsgroep).  De instellingen die bij de synchronisatie op de gebruiker worden toegepast, zijn afkomstig van het eerste synchronisatie-item in de lijst waaraan de gebruiker is gekoppeld.  Daarom moeten de synchronisatie-items in volgorde van prioriteit worden geplaatst.

> [!TIP]
> Na het verwijderen van synchronisatie-items moet een volledige synchronisatie worden uitgevoerd.  Na het rangschikken van synchronisatie-items moet een volledige synchronisatie worden uitgevoerd.  Klik op **Nu synchroniseren** om een volledige synchronisatie uit te voeren.

## <a name="multi-factor-authentication-servers"></a>Multi-Factor Authentication-servers

Aanvullende Multi-Factor Authentication servers kunnen worden ingesteld om te fungeren als een back-upradius-proxy, LDAP-proxy of voor IIS-verificatie. De configuratie van de synchronisatie wordt gedeeld tussen alle agents. Er kan echter slechts één van deze agents de Multi-Factor Authentication Server-service uitvoeren. Op dit tabblad kunt u de Multi-Factor Authentication-Server selecteren die voor synchronisatie moet worden ingeschakeld.

![Gerelateerde Multi-Factor Authentication-servers](./media/howto-mfaserver-dir-ad/dirint6.png)
