---
title: Afzonderlijke sensoren beheren
description: Meer informatie over het beheren van afzonderlijke Sens oren, waaronder het beheren van activerings bestanden, het uitvoeren van back-ups en het bijwerken van een zelfstandige sensor.
ms.date: 02/02/2021
ms.topic: how-to
ms.openlocfilehash: df1d1dd6ca2085fb3ab12c104723c63e32249403
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781956"
---
# <a name="manage-individual-sensors"></a>Afzonderlijke sensoren beheren

In dit artikel wordt beschreven hoe u afzonderlijke Sens oren beheert. Taken omvatten het beheren van activerings bestanden, het uitvoeren van back-ups en het bijwerken van een zelfstandige sensor.

U kunt ook bepaalde sensor beheer taken uitvoeren vanuit de on-premises beheer console, waar meerdere Sens oren gelijktijdig kunnen worden beheerd.

U gebruikt de Azure Portal voor het voorbereiden van de sensor en registratie.

## <a name="manage-sensor-activation-files"></a>Activerings bestanden voor de sensor beheren

Uw sensor is met Azure Defender voor IoT geboardd vanuit het Azure Portal. Elke sensor is ingeboardd als een lokaal verbonden sensor of een met de Cloud verbonden sensor.

Er wordt een uniek activerings bestand geüpload naar elke sensor die u implementeert. Zie [nieuwe activerings bestanden uploaden](#upload-new-activation-files)voor meer informatie over wanneer en hoe u een nieuw bestand kunt gebruiken. Zie [problemen met het uploaden van activerings bestanden oplossen](#troubleshoot-activation-file-upload)als u het bestand niet kunt uploaden.

### <a name="about-activation-files-for-locally-connected-sensors"></a>Over activerings bestanden voor lokaal verbonden Sens oren

Lokaal verbonden Sens oren zijn gekoppeld aan een Azure-abonnement. Het activerings bestand voor uw lokaal verbonden Sens oren bevat een verval datum. Eén maand vóór deze datum wordt een waarschuwing weergegeven bovenaan de sensorconsole. De waarschuwing blijft tot nadat u het activerings bestand hebt bijgewerkt.

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="De scherm opname van de systeem instellingen.":::

U kunt ook blijven werken met Defender voor IoT-functies, zelfs als het activerings bestand is verlopen. 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>Over activerings bestanden voor met Clouds verbonden Sens oren

Sens oren die zijn verbonden met de Cloud, zijn gekoppeld aan de Defender voor IoT hub. Deze Sens oren worden niet beperkt door tijds perioden voor het activerings bestand. Het activerings bestand voor met Cloud verbonden Sens oren wordt gebruikt om te zorgen voor verbinding met de Defender voor IoT hub.

### <a name="upload-new-activation-files"></a>Nieuwe activerings bestanden uploaden

Mogelijk moet u een nieuw activerings bestand uploaden voor een voorbereide sensor wanneer:

- Een activerings bestand verloopt op een lokaal verbonden sensor. 

- U wilt werken in een andere sensor beheer modus. 

- U wilt een nieuwe Defender voor IoT-hub toewijzen aan een in de Cloud aangesloten sensor.

Een nieuw activerings bestand toevoegen:

1. Ga naar de pagina voor het **beheer van Sens oren** .

2. Selecteer de sensor waarvoor u een nieuw activerings bestand wilt uploaden.

3. Deze verwijderen.

4. Onboarding van de sensor opnieuw vanaf de pagina **onboarding** in de nieuwe modus of met een nieuwe Defender voor IOT hub.

5. Down load het activerings bestand op de pagina **activerings bestand downloaden** .

6. Sla het bestand op.

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="Down load het activerings bestand van de Defender voor IoT hub.":::

7. Meld u aan bij de Defender voor IoT-sensor console.

8. Selecteer in de sensor console de optie **systeem instellingen** opnieuw  >  **activeren**.

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="Selectie opnieuw activeren in het scherm systeem instellingen.":::

9. Selecteer **uploaden** en selecteer het bestand dat u hebt opgeslagen.

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="Upload het bestand dat u hebt opgeslagen.":::

10. Selecteer **Activate**.

### <a name="troubleshoot-activation-file-upload"></a>Problemen met het uploaden van activerings bestanden oplossen

Er wordt een fout bericht weer gegeven als het activerings bestand niet kan worden geüpload. De volgende gebeurtenissen zijn mogelijk opgetreden:

- **Voor lokaal verbonden Sens oren**: het activerings bestand is niet geldig. Als het bestand niet geldig is, gaat u naar de portal Defender voor IoT. Selecteer op de pagina **sensor beheer** de sensor met het ongeldige bestand en down load een nieuw activerings bestand.

- **Voor in de Cloud verbonden Sens oren**: de sensor kan geen verbinding maken met internet. Controleer de netwerk configuratie van de sensor. Als uw sensor verbinding moet maken via een webproxy voor toegang tot internet, controleert u of de proxy server correct is geconfigureerd op het scherm **sensor netwerk configuratie** . Controleer of \* . Azure-devices.net:443 is toegestaan in de firewall en/of de proxy. Als joker tekens niet worden ondersteund of als u meer controle wilt, moet de FQDN voor uw specifieke Defender voor IoT hub worden geopend in uw firewall en/of proxy. Zie [referentie-IOT hub-eind punten](../iot-hub/iot-hub-devguide-endpoints.md)voor meer informatie.  

- **Voor met Clouds verbonden Sens oren**: het activerings bestand is geldig, maar Defender voor IOT heeft dit geweigerd. Als dit probleem niet kan worden opgelost, kunt u een andere activering downloaden vanaf de pagina sites en Sens oren van de Defender voor IoT-Portal. Als dit niet werkt, neemt u contact op met Microsoft Ondersteuning.

## <a name="manage-certificates"></a>Certificaten beheren

Na installatie van de sensor wordt een lokaal zelfondertekend certificaat gegenereerd dat wordt gebruikt voor toegang tot de sensor-webtoepassing. Wanneer u zich voor de eerste keer aanmeldt bij de sensor, wordt gebruikers van de beheerder gevraagd een SSL/TLS-certificaat op te geven.  Zie voor meer informatie over [het configureren van de eerste keer aanmelden en een sensor activeren](how-to-activate-and-set-up-your-sensor.md).

Dit artikel bevat informatie over het bijwerken van certificaten, het werken met certificaat-CLI-opdrachten en ondersteunde certificaten en certificaat parameters.

### <a name="about-certificates"></a>Over certificaten

Azure Defender voor IoT gebruikt SSL/TLS-certificaten voor het volgende:

- Voldoen aan specifieke vereisten voor certificaat en versleuteling die door uw organisatie zijn aangevraagd door het door de certificerings instantie ondertekende certificaat te uploaden.

- Sta validatie toe tussen de beheer console en verbonden Sens oren en tussen een beheer console en een beheer console met hoge Beschik baarheid. Validaties worden geëvalueerd op basis van een certificaatintrekkingslijst en de verval datum van het certificaat. *Als de validatie mislukt, wordt de communicatie tussen de beheer console en de sensor gestopt en wordt er een validatie fout weer gegeven in de console*. Deze optie is standaard ingeschakeld na de installatie.

 Regels voor het door sturen van derden, bijvoorbeeld waarschuwings informatie, verzonden naar SYSLOG, Splunk of ServiceNow; of communicatie met Active Directory worden niet gevalideerd.

#### <a name="ssl-certificates"></a>SSL-certificaten

De Defender voor IoT-sensor en de on-premises beheer console gebruiken SSL-en TLS-certificaten voor de volgende functies: 

 - Beveiligde communicatie tussen gebruikers en de webconsole van het apparaat. 
 
 - Veilige communicatie met de REST API op de sensor en on-premises beheer console.
 
 - Beveiligde communicatie tussen Sens oren en een on-premises beheer console. 

Zodra het apparaat is geïnstalleerd, wordt een lokaal zelfondertekend certificaat gegenereerd om voorlopige toegang tot de webconsole toe te staan. Enter prise SSL-en TLS-certificaten kunnen worden geïnstalleerd met behulp van het [`cyberx-xsense-certificate-import`](#cli-commands) opdracht regel programma.

 > [!NOTE]
 > Voor integraties en doorstuur regels waarbij het apparaat de client en initiator van de sessie is, worden specifieke certificaten gebruikt en zijn deze niet gerelateerd aan de systeem certificaten.  
 >
 >In dergelijke gevallen worden de certificaten doorgaans van de server ontvangen of worden asymmetrische versleuteling gebruikt, waarbij een specifiek certificaat wordt opgegeven om de integratie in te stellen.

Apparaten kunnen unieke certificaat bestanden gebruiken. Als u een certificaat moet vervangen, hebt u het geüpload;

- In versie 10,0 kunt u het certificaat vervangen in het menu systeem instellingen.

- Voor eerdere versies van 10,0, kan het SSL-certificaat worden vervangen met behulp van het opdracht regel programma.

### <a name="update-certificates"></a>Certificaten bijwerken

Gebruikers van de sensor beheerder kunnen certificaten bijwerken.

Een certificaat bijwerken:  

1. Selecteer **systeem instellingen**.

1. Selecteer **SSL/TLS-certificaten.**
1. Verwijder of bewerk het certificaat en voeg een nieuwe toe.

    - Voeg een certificaat naam toe.
    
    - Upload een CRT-bestand en een sleutel bestand en voer een wachtwoordzin in.
    - Upload zo nodig een PEM-bestand.

De validatie-instelling wijzigen:

1. Schakel de schakel optie **certificaat validatie inschakelen** in of uit.

1. Selecteer **Opslaan**.

Als de optie is ingeschakeld en de validatie mislukt, wordt de communicatie tussen de beheer console en de sensor gestopt en wordt er een validatie fout weer gegeven in de console.

### <a name="certificate-support"></a>Ondersteuning voor certificaten

De volgende certificaten worden ondersteund:

- Persoonlijke en bedrijfs sleutel infrastructuur (persoonlijke PKI)

- Open bare-sleutel infrastructuur (open bare PKI) 

- Lokaal gegenereerd op het apparaat (lokaal zelf ondertekend). 

> [!IMPORTANT]
> Het is niet raadzaam om zelfondertekende certificaten te gebruiken. Dit type verbinding is niet beveiligd en moet alleen worden gebruikt voor test omgevingen. Omdat de eigenaar van het certificaat niet kan worden gevalideerd en de beveiliging van uw systeem niet kan worden gehandhaafd, moeten zelfondertekende certificaten nooit worden gebruikt voor productie netwerken.

### <a name="supported-ssl-certificates"></a>Ondersteunde SSL-certificaten 

De volgende para meters worden ondersteund. 

**CRT voor certificaten**

- Het primaire certificaat bestand voor uw domein naam

- Handtekening algoritme = SHA256RSA
- Hash-algoritme hand tekening = SHA256
- Geldig van = geldige datum in het verleden
- Geldig tot = geldige datum in de toekomst
- Open bare sleutel = RSA 2048 bits (mini maal) of 4096 bits
- CRL-distributie punt = URL naar. CRL-bestand
- Onderwerp CN = URL, kan een certificaat met Joker tekens zijn. bijvoorbeeld: sensor. contoso. <span> com of *. contoso. <span> com
- Onderwerp (C) ountry = gedefinieerd, bijvoorbeeld VS
- Organisatie-eenheid voor het onderwerp (OE) = gedefinieerd, bijvoorbeeld contoso Labs
- Onderwerp (O) rganisatie = gedefinieerd, bijvoorbeeld contoso Inc.

**Sleutel bestand**

- Het sleutel bestand dat is gegenereerd bij het maken van de CSR.

- RSA 2048 bits (minimum) of 4096 bits.

 > [!Note]
 > Met een sleutel lengte van 4096bits:
 > - De SSL-Handshake aan het begin van elke verbinding is langzamer.  
 > - Er is een toename van het CPU-gebruik tijdens de handshake. 

**Certificaatketen**

- Het tussenliggende certificaat bestand (indien aanwezig) dat is geleverd door uw CA

- Het CA-certificaat dat het certificaat van de server heeft uitgegeven, moet eerst in het bestand staan, gevolgd door andere tot de hoofdmap. 
- Kan Bag-kenmerken bevatten.

**Wachtzin**

- Eén sleutel wordt ondersteund.

- Instellen bij het importeren van het certificaat.

Certificaten met andere para meters werken mogelijk wel, maar micro soft ondersteunt deze niet.

#### <a name="encryption-key-artifacts"></a>Versleutelings sleutel artefacten

**. PEM – certificaat container bestand**

Privacy Enhanced Mail-bestanden (PEM) zijn het algemene bestands type dat wordt gebruikt voor het beveiligen van e-mail berichten. Tegenwoordig, PEM-bestanden worden gebruikt met certificaten en gebruiken x509-ASN1-sleutels.  

Het container bestand is gedefinieerd in RFC 1421 tot 1424, een container indeling die alleen het open bare certificaat kan bevatten. Bijvoorbeeld Apache-installaties, een CA-certificaat, bestanden, enzovoort, SSL of certificaten. Dit kan een volledige certificaat keten zijn met inbegrip van de open bare sleutel, persoonlijke sleutel en basis certificaten.  

Er kan ook een CSR worden gecodeerd als de PKCS10-indeling, die kan worden vertaald in PEM.

**. cert. cer. CRT: certificaat container bestand**

Een `.pem` -of `.der` opgemaakt bestand met een andere extensie. Het bestand wordt door Windows Verkenner herkend als een certificaat. Het `.pem`   bestand wordt niet herkend door Windows Verkenner.

**sleutel – bestand met persoonlijke sleutel**

Een sleutel bestand heeft dezelfde indeling als een PEM-bestand, maar heeft een andere extensie.

#### <a name="additional-commonly-available-key-artifacts"></a>Meer algemeen beschik bare sleutel artefacten

**. CSR-aanvraag voor certificaat ondertekening**.  

Dit bestand wordt gebruikt voor het indienen van certificerings instanties. De werkelijke indeling is PKCS10, die is gedefinieerd in RFC 2986, en kan enkele of alle sleutel Details van het aangevraagde certificaat bevatten. Bijvoorbeeld onderwerp, organisatie en status. Het is de open bare sleutel van het certificaat dat door de CA wordt ondertekend en ontvangt een certificaat als resultaat.  

Het geretourneerde certificaat is het open bare certificaat, inclusief de open bare sleutel, maar niet de persoonlijke sleutel. 

**. pkcs12/pfx-profiel. pfx. p12 – wachtwoord container**. 

Oorspronkelijk door RSA gedefinieerd in de Public-Key Cryptography Standards (PKCS), is de 12-variant oorspronkelijk verbeterd door micro soft en later ingediend als RFC 7292.  

Voor deze container indeling is een wacht woord vereist dat zowel open bare als persoonlijke certificaat paren bevat. In tegens telling tot `.pem`   bestanden is deze container volledig versleuteld.  

U kunt OpenSSL gebruiken om dit in te scha kelen in een `.pem`   bestand met zowel open bare als persoonlijke sleutels: `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**. der-binary encoded PEM**.

De manier om ASN. 1-syntaxis in binaire code te coderen, bevindt zich via een `.pem`   bestand, dat slechts een base64-gecodeerd `.der` bestand is. 

OpenSSL kan deze bestanden converteren naar een `.pem` :  `openssl x509 -inform der -in to-convert.der -out converted.pem` .  

Windows herkent deze bestanden als certificaat bestanden. Standaard exporteert Windows certificaten als `.der` geformatteerde bestanden met een andere extensie.  

**. CRL: certificaat intrekkings lijst**.  
Certificerings instanties genereren deze bestanden als een manier om certificaten te autoriseren vóór de verval datum.
 
##### <a name="cli-commands"></a>CLI-opdrachten

Gebruik de `cyberx-xsense-certificate-import` cli-opdracht om certificaten te importeren. Als u dit hulp programma wilt gebruiken, moet u certificaat bestanden uploaden naar het apparaat met behulp van hulpprogram ma's zoals WinSCP of wget.

De opdracht ondersteunt de volgende invoer vlaggen:

- `-h`: Hiermee wordt de Help-syntaxis van de opdracht regel weer gegeven.

- `--crt`: Pad naar een certificaat bestand (. CRT-extensie).

- `--key`:  \* . sleutel bestand. De sleutel lengte moet mini maal 2.048 bits zijn.

- `--chain`: Pad naar een certificaat keten bestand (optioneel).

- `--pass`: Wachtwoordzin die wordt gebruikt voor het versleutelen van het certificaat (optioneel).

- `--passphrase-set`: Standaard = `False` , ongebruikt. Stel in `True` om de vorige wachtwoordzin te gebruiken die is opgegeven met het vorige certificaat (optioneel).

Wanneer u de CLI-opdracht gebruikt:

- Controleer of de certificaat bestanden kunnen worden gelezen op het apparaat.

- Controleer of de domein naam en het IP-adres in het certificaat overeenkomen met de configuratie die de IT-afdeling heeft gepland.

### <a name="use-openssl-to-manage-certificates"></a>OpenSSL gebruiken voor het beheren van certificaten

Beheer uw certificaten met de volgende opdrachten:

| Description | CLI-opdracht |
|--|--|
| Een nieuwe aanvraag voor een persoonlijke sleutel en certificaat ondertekening genereren | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| Een zelfondertekend certificaat maken | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| Een aanvraag voor certificaat ondertekening (CSR) genereren voor een bestaande persoonlijke sleutel | `openssl req -out CSR.csr -key privateKey.key -new` |
| Een aanvraag voor certificaat ondertekening genereren op basis van een bestaand certificaat | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| Een wachtwoordzin uit een persoonlijke sleutel verwijderen | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

Als u de gegevens in een certificaat, CSR of persoonlijke sleutel wilt controleren, gebruikt u deze opdrachten.

| Description | CLI-opdracht |
|--|--|
| Een aanvraag voor certificaat ondertekening controleren (CSR) | `openssl req -text -noout -verify -in CSR.csr` |
| Een persoonlijke sleutel controleren | `openssl rsa -in privateKey.key -check` |
| Een certificaat controleren | `openssl x509 -in certificate.crt -text -noout`  |

Als er een fout bericht wordt weer gegeven dat de persoonlijke sleutel niet overeenkomt met het certificaat, of dat een certificaat dat u hebt geïnstalleerd op een site niet wordt vertrouwd, gebruikt u deze opdrachten om de fout op te lossen.

| Description | CLI-opdracht |
|--|--|
| Een MD5-hash van de open bare sleutel controleren om ervoor te zorgen dat deze overeenkomt met wat er in een CSR of persoonlijke sleutel staat | i. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> twee. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

Als u certificaten en sleutels naar verschillende indelingen wilt converteren om ze compatibel te maken met specifieke typen servers of software, gebruikt u deze opdrachten.

| Description | CLI-opdracht |
|--|--|
| Een DER-bestand (. CRT. cer. der) converteren naar PEM  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| Een PEM-bestand converteren naar DER | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| Een PKCS # 12-bestand (. pfx. p12) met een persoonlijke sleutel en certificaten naar PEM converteren | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />U kunt `-nocerts` alleen toevoegen om de persoonlijke sleutel uit te voeren, of `-nokeys` alleen toevoegen om de certificaten uit te voeren. |
| Een PEM-certificaat bestand en een persoonlijke sleutel converteren naar PKCS # 12 (. pfx. p12) | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="connect-a-sensor-to-the-management-console"></a>Een sensor verbinden met de beheer console

In deze sectie wordt beschreven hoe u verbinding tussen de sensor en de on-premises beheer console waarborgt. U moet dit doen als u in een Air-gapped-netwerk werkt en u apparaat-en waarschuwings gegevens wilt verzenden naar de beheer console van de sensor. Met deze verbinding kan ook de beheer console systeem instellingen naar de sensor pushen en andere beheer taken op de sensor uitvoeren.

Verbinding maken:

1. Meld u aan bij de on-premises beheer console.

2. Selecteer **systeem instellingen**.

3. Kopieer in de sectie **sensor Setup – verbindings reeks** de automatisch gegenereerde Connection String.

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="Kopieer de connection string van dit scherm."::: 

4. Meld u aan bij de sensor console.

5. Selecteer **systeem instellingen** in het linkerdeel venster.

6. Selecteer een **beheer console verbinding**.

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="Scherm afbeelding van het dialoog venster verbinding met beheer console.":::

7. Plak de connection string in het vak **verbindingsteken reeks** en selecteer **verbinding maken**.

8. Wijs in de on-premises beheer console in het venster **site beheer** de sensor toe aan een zone.

## <a name="change-the-name-of-a-sensor"></a>De naam van een sensor wijzigen

U kunt de naam van uw sensor console wijzigen. De nieuwe naam wordt weer gegeven in de webbrowser van de console, in verschillende console vensters en in Logboeken voor probleem oplossing.

Het proces voor het wijzigen van sensor namen is afhankelijk van lokaal verbonden Sens oren en met de Cloud verbonden Sens oren. De standaard naam is **sensor**.

### <a name="change-the-name-of-a-locally-connected-sensor"></a>De naam van een lokaal verbonden sensor wijzigen

Ga als volgt te werk om de naam te wijzigen:

1. Onder in het linkerdeel venster van de console selecteert u het huidige sensor label.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="Scherm opname van het sensor label.":::

1. Voer in het dialoog venster **sensor naam bewerken** een naam in.

1. Selecteer **Opslaan**. De nieuwe naam wordt toegepast.

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>De naam van een in de Cloud aangesloten sensor wijzigen

Als uw sensor is geregistreerd als een in de Cloud aangesloten sensor, wordt de naam van de sensor gedefinieerd door de naam die tijdens de registratie is toegewezen. De naam is opgenomen in het activerings bestand dat u hebt geüpload toen u zich voor de eerste keer aanmeldt. Als u de naam van de sensor wilt wijzigen, moet u een nieuw activerings bestand uploaden.

Ga als volgt te werk om de naam te wijzigen:

1. Ga in de Azure Defender voor IoT-Portal naar de pagina sites en Sens oren.

1. Verwijder de sensor van de pagina sites en Sens oren.

1. Meld u aan met de nieuwe naam door op de pagina aan de slag de optie voor **onboarding** te selecteren.

1. Down load het nieuwe activerings bestand.

1. Meld u aan bij de Defender voor IoT-sensor console.

1. Selecteer in de sensor console de optie **systeem instellingen** en selecteer vervolgens opnieuw **activeren**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Upload uw activerings bestand om de sensor opnieuw te activeren.":::

1. Selecteer **uploaden** en selecteer het bestand dat u hebt opgeslagen.

1. Selecteer **Activate**.

## <a name="update-the-sensor-network-configuration"></a>De netwerk configuratie van de sensor bijwerken

De netwerk configuratie van de sensor is gedefinieerd tijdens de installatie van de sensor. U kunt configuratie parameters wijzigen. U kunt ook een proxy configuratie instellen.

Als u een nieuw IP-adres maakt, moet u zich mogelijk opnieuw aanmelden.

De configuratie wijzigen:

1. Selecteer **systeem instellingen** in het menu aan de zijkant.

2. Selecteer in het venster **systeem instellingen** de optie **netwerk**.

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="Configureer uw netwerk instellingen.":::

3. Stel de para meters in:

    | Parameter | Beschrijving |
    |--|--|
    | IP-adres | Het IP-adres van de sensor |
    | Subnetmasker | Het masker adres |
    | Standaardgateway | Het standaard gateway adres |
    | DNS | Het DNS-server adres |
    | Hostnaam | De hostnaam van de sensor |
    | Proxy | Proxy-host en poort naam |

4. Selecteer **Opslaan**.

## <a name="synchronize-time-zones-on-the-sensor"></a>Tijd zones op de sensor synchroniseren

U kunt de tijd en regio van de sensor zo configureren dat alle gebruikers dezelfde tijd en regio zien.

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="Configureer de tijd en regio.":::

| Parameter | Beschrijving |
|--|--|
| Tijdzone | De tijdzone definitie voor:<br />-Waarschuwingen<br />-Trends en statistieken widgets<br />-Gegevens analyse rapporten<br />   -Rapporten over risico analyse<br />-Aanvals vectoren |
| Datum notatie | Selecteer een van de volgende indelings opties:<br />-DD-MM-JJJJ uu: mm: SS<br />-MM/DD/JJJJ uu: mm: SS<br />-JJJJ/MM/DD uu: mm: SS |
| Datum en tijd | Hiermee worden de huidige datum en lokale tijd weer gegeven in de indeling die u hebt geselecteerd.<br />Als uw werkelijke locatie bijvoorbeeld America en New York is, maar de tijd zone is ingesteld op Europa en Berlijn, wordt de tijd weer gegeven volgens Berlijn lokale tijd. |

De sensor tijd configureren:

1. Selecteer **systeem instellingen** in het menu aan de zijkant.

2. Selecteer in het venster **systeem instellingen** de optie **tijd & regionaal**.

3. Stel de para meters in en selecteer **Opslaan**.

## <a name="set-up-backup-and-restore-files"></a>Back-up-en herstel bestanden instellen

Systeem back-up wordt automatisch om 3:00 uur uitgevoerd. De gegevens worden opgeslagen op een andere schijf in de sensor. De standaardlocatie is `/var/cyberx/backups`.

U kunt dit bestand automatisch overdragen naar het interne netwerk.

> [!NOTE]
> - De procedure voor het maken en terugzetten van back-ups kan alleen worden uitgevoerd in dezelfde versies.
> - In sommige architecturen wordt de back-up uitgeschakeld. U kunt deze inschakelen in het `/var/cyberx/properties/backup.properties` bestand.

Wanneer u een sensor beheert met behulp van de on-premises beheer console, kunt u het back-upschema van de sensor gebruiken om deze back-ups te verzamelen en op te slaan in de-beheer console of op een externe back-upserver.

**Waarvan wordt een back-up gemaakt:** Configuraties en gegevens.

**Waarvan wordt geen back-up gemaakt:** PCAP-bestanden en-Logboeken. U kunt hand matig back-ups maken en PCAPs en logboeken herstellen.

Sensor back-upbestanden worden automatisch benoemd met de volgende indeling: `<sensor name>-backup-version-<version>-<date>.tar` . Een voorbeeld is `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`.

Back-up configureren:

- Meld u aan bij een Administrator-account en voer in `$ sudo cyberx-xsense-system-backup` .

Het meest recente back-upbestand herstellen:

- Meld u aan bij een Administrator-account en voer in `$ sudo cyberx-xsense-system-restore` .

De back-up opslaan op een externe SMB-server:

1. Maak een gedeelde map op de externe SMB-server.

    Het mappad, de gebruikers naam en het wacht woord ophalen dat is vereist voor toegang tot de SMB-server.

2. Maak in de sensor een map voor de back-ups:

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. Bewerken `fstab` :

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. Bewerk en maak referenties om te delen voor de SMB-server:

    `sudo nano /etc/samba/user` 

5. Voeg toe:

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. De map koppelen:

    `sudo mount -a`

7. Een back-upmap configureren in de gedeelde map op de Defender voor IoT-sensor:  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>Sens oren herstellen

U kunt back-ups herstellen vanuit de sensor console en door gebruik te maken van de CLI.

**Herstellen vanaf de-console:**

- Selecteer **installatie kopie herstellen** in het venster **systeem instellingen** van de sensor.

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="Herstel de installatie kopie door de knop te selecteren.":::

Er worden herstel fouten weer gegeven in de console.

**Herstellen met behulp van de CLI:**

- Meld u aan bij een Administrator-account en voer in `$ sudo cyberx-management-system-restore` .

## <a name="update-a-standalone-sensor-version"></a>Een zelfstandige sensor versie bijwerken

In de volgende procedure wordt beschreven hoe u een zelfstandige sensor bijwerkt met behulp van de sensor console. Het update proces duurt ongeveer 30 minuten.

1. Ga naar de [Azure Portal](https://portal.azure.com/).

2. Ga naar Defender voor IoT.

3. Ga naar de pagina **updates** .

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="Scherm afbeelding van de pagina updates van Defender voor IoT.":::

4. Selecteer **downloaden** in het gedeelte **Sens oren** en sla het bestand op.

5. Selecteer in de zijbalk van de sensor console de optie **systeem instellingen**.

6. Selecteer **bijwerken** in het deel venster **versie-upgrade** .

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="Scherm opname van het deel venster bijwerken.":::

7. Selecteer het bestand dat u hebt gedownload van de pagina Defender voor IoT- **updates** .

8. Het update proces wordt gestart, terwijl het systeem twee keer opnieuw wordt opgestart. Na de eerste keer opnieuw opstarten (vóór het volt ooien van het update proces), wordt het systeem geopend met het aanmeld venster. Nadat u zich hebt aangemeld, wordt de upgrade versie linksonder in de zijbalk weer gegeven.

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="Scherm afbeelding van de upgrade versie die wordt weer gegeven nadat u zich hebt aangemeld.":::

## <a name="forward-sensor-failure-alerts"></a>Fout meldingen van sensor door sturen

U kunt waarschuwingen door sturen naar derden om informatie te geven over:

- Niet-verbonden Sens oren

- Externe back-upfouten

Deze informatie wordt verzonden wanneer u een doorstuur regel voor systeem meldingen maakt.

> [!NOTE]
> Beheerders kunnen systeem meldingen verzenden.

Meldingen verzenden:

1. Meld u aan bij de on-premises beheer console.
1. Selecteer **door sturen** in het menu aan de zijkant.
1. Een doorstuur regel maken.
1. Selecteer **rapport systeem meldingen**.

Zie [Forward alert Information](how-to-forward-alert-information-to-partners.md)(Engelstalig) voor meer informatie over regels voor door sturen.

## <a name="adjust-system-properties"></a>Systeem eigenschappen aanpassen

Systeem eigenschappen bepalen verschillende bewerkingen en instellingen in de sensor. Bewerken of aanpassen hiervan kan de werking van de sensor console beschadigen.

Neem contact op met [Microsoft ondersteuning](https://support.microsoft.com/) voordat u de instellingen wijzigt.

Om toegang te krijgen tot systeem eigenschappen:

1. Meld u aan bij de on-premises beheer console of de sensor.

2. Selecteer **systeem instellingen**.

3. Selecteer **systeem eigenschappen** in de sectie **Algemeen** .

## <a name="see-also"></a>Zie ook

[Onderzoek en pakketten voor bedreigings informatie](how-to-work-with-threat-intelligence-packages.md)

[Sens oren beheren vanuit de beheer console](how-to-manage-sensors-from-the-on-premises-management-console.md)