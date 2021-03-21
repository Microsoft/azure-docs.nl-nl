---
title: De on-premises beheerconsole beheren
description: Meer informatie over opties voor on-premises beheer console, zoals back-up en herstel, het definiëren van de hostnaam en het instellen van een proxy voor Sens oren.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: f3c9f8f78f17153c3d2eb7b014cf616253b3c0c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618250"
---
# <a name="manage-the-on-premises-management-console"></a>De on-premises beheerconsole beheren

In dit artikel worden de opties voor on-premises beheer console behandeld, zoals back-up en herstel, het activerings bestand voor het laden van een apparaat, het bijwerken van certificaten en het instellen van een proxy voor Sens oren.

U kunt de on-premises beheer console onboarden vanaf de Azure Portal.

## <a name="upload-an-activation-file"></a>Een activerings bestand uploaden

Wanneer u zich voor het eerst aanmeldt, wordt er een activerings bestand voor de on-premises beheer console gedownload. Dit bestand bevat de geaggregeerde toegewezen apparaten die tijdens het voorbereidings proces worden gedefinieerd. De lijst bevat Sens oren die aan meerdere abonnementen zijn gekoppeld.

Na de eerste activering overschrijdt het aantal bewaakte apparaten het aantal toegewezen apparaten dat tijdens de onboarding is gedefinieerd. Deze gebeurtenis kan bijvoorbeeld optreden als u meer Sens oren verbindt met de beheer console. Als er een verschil is tussen het aantal bewaakte apparaten en het aantal toegewezen apparaten, wordt er een waarschuwing weer gegeven in de beheer console. Als deze gebeurtenis zich voordoet, moet u een nieuw activerings bestand uploaden.

Een activerings bestand uploaden:

1. Ga naar de pagina met **prijzen** voor Azure Defender voor IOT.
1. Selecteer de **down load het activerings bestand voor het tabblad beheer console** . Het activerings bestand wordt gedownload.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Down load het activerings bestand.":::

1. Selecteer **systeem instellingen** in de beheer console.
1. Selecteer **Activering**.
1. Selecteer **een bestand kiezen** en selecteer het bestand dat u hebt opgeslagen.

## <a name="manage-certificates"></a>Certificaten beheren

Na de installatie van de on-premises beheer console wordt een lokaal zelfondertekend certificaat gegenereerd en gebruikt om toegang te krijgen tot de webtoepassing van de beheer console. Wanneer gebruikers met beheerders rechten zich voor de eerste keer aanmelden bij de beheer console, wordt ze gevraagd een SSL/TLS-certificaat op te geven. Zie [uw on-premises beheer console activeren en instellen](how-to-activate-and-set-up-your-on-premises-management-console.md)voor meer informatie over de eerste keer instellen.

De volgende secties bevatten informatie over het bijwerken van certificaten, het werken met certificaat-CLI-opdrachten en ondersteunde certificaten en certificaat parameters.

### <a name="about-certificates"></a>Over certificaten

Azure Defender voor IoT gebruikt SSL-en TLS-certificaten voor het volgende:

- Voldoen aan specifieke vereisten voor certificaat en versleuteling die door uw organisatie zijn aangevraagd door het door de certificerings instantie ondertekende certificaat te uploaden.

- Sta validatie toe tussen de beheer console en verbonden Sens oren en tussen een beheer console en een beheer console met hoge Beschik baarheid. Validatie wordt geëvalueerd op basis van een certificaatintrekkingslijst en de verval datum van het certificaat. *Als de validatie mislukt, wordt de communicatie tussen de beheer console en de sensor gestopt en wordt er een validatie fout weer gegeven in de console*. Deze optie is standaard ingeschakeld na de installatie.

Regels voor door sturen van derden worden niet gevalideerd. Voor beelden zijn waarschuwings gegevens die worden verzonden naar SYSLOG, Splunk of ServiceNow; en communicatie met Active Directory.

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

### <a name="update-certificates"></a>Certificaten bijwerken

Gebruikers met beheerders rechten van de on-premises beheer console kunnen certificaten bijwerken.

Een certificaat bijwerken:  

1. Selecteer **systeem instellingen**.

1. Selecteer **SSL/TLS-certificaten**.
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

- Lokaal gegenereerd op het apparaat (lokaal zelf ondertekend) 

  > [!IMPORTANT]
  > Het is niet raadzaam om zelfondertekende certificaten te gebruiken. Dit type verbinding is niet beveiligd en moet alleen worden gebruikt voor test omgevingen. Omdat de eigenaar van het certificaat niet kan worden gevalideerd en de beveiliging van uw systeem niet kan worden gehandhaafd, moeten zelfondertekende certificaten nooit worden gebruikt voor productie netwerken.

### <a name="supported-ssl-certificates"></a>Ondersteunde SSL-certificaten 

De volgende para meters worden ondersteund: 

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
- Organisatie-eenheid (OE) van het onderwerp = gedefinieerd; bijvoorbeeld: contoso Labs
- Onderwerp (O) rganisatie = gedefinieerd; bijvoorbeeld contoso Inc

**Sleutel bestand**

- Het sleutel bestand dat is gegenereerd bij het maken van de CSR

- RSA 2048 bits (mini maal) of 4096 bits

 > [!Note]
 > Met een sleutel lengte van 4096bits:
 > - De SSL-Handshake aan het begin van elke verbinding is langzamer.  
 > - Er is een toename van het CPU-gebruik tijdens de handshake. 

**Certificaat keten**

- Het tussenliggende certificaat bestand (indien aanwezig) dat is geleverd door uw CA.

- Het CA-certificaat dat het certificaat van de server heeft uitgegeven, moet eerst in het bestand staan, gevolgd door andere tot de hoofdmap. 
- De keten kan Bag-kenmerken bevatten.

**Wachtzin**

- Er wordt één sleutel ondersteund.

- Instellen bij het importeren van het certificaat.

Certificaten met andere para meters werken mogelijk wel, maar micro soft ondersteunt deze niet.

#### <a name="encryption-key-artifacts"></a>Versleutelings sleutel artefacten

**. pem: certificaat container bestand**

Privacy Enhanced Mail-bestanden (PEM) zijn het algemene bestands type dat wordt gebruikt voor het beveiligen van e-mail berichten. Tegenwoordig, PEM-bestanden worden gebruikt met certificaten en gebruiken x509-ASN1-sleutels.  

Het container bestand is gedefinieerd in RFC 1421 tot 1424, een container indeling die alleen het open bare certificaat kan bevatten. Bijvoorbeeld Apache-installaties, een CA-certificaat, bestanden, enzovoort, SSL of certificaten. Dit kan een volledige certificaat keten zijn met inbegrip van de open bare sleutel, persoonlijke sleutel en basis certificaten.  

Er kan ook een CSR worden gecodeerd als de PKCS10-indeling, die kan worden vertaald in PEM.

**. cert. cer. CRT: certificaat container bestand**

Een `.pem` -of `.der` opgemaakt bestand met een andere extensie. Het bestand wordt door Windows Verkenner herkend als een certificaat. Het `.pem`   bestand wordt niet herkend door Windows Verkenner.

**. key: persoonlijk sleutel bestand**

Een sleutel bestand heeft dezelfde indeling als een PEM-bestand, maar heeft een andere extensie. 

#### <a name="additional-commonly-available-key-artifacts"></a>Meer algemeen beschik bare sleutel artefacten

**. CSR-aanvraag voor certificaat ondertekening**.  

Dit bestand wordt gebruikt voor het indienen van certificerings instanties. De werkelijke indeling is PKCS10, die is gedefinieerd in RFC 2986, en kan enkele of alle sleutel Details van het aangevraagde certificaat bevatten. Bijvoorbeeld onderwerp, organisatie en status. Het is de open bare sleutel van het certificaat dat door de CA wordt ondertekend en ontvangt een certificaat als resultaat.  

Het geretourneerde certificaat is het open bare certificaat, inclusief de open bare sleutel, maar niet de persoonlijke sleutel. 

**. pkcs12/pfx-profiel. pfx. p12 – wachtwoord container**. 

Oorspronkelijk door RSA gedefinieerd in de Public-Key Cryptography Standards (PKCS), is de 12-variant oorspronkelijk verbeterd door micro soft en later ingediend als RFC 7292.  

Voor deze container indeling is een wacht woord vereist dat zowel open bare als persoonlijke certificaat paren bevat. In tegens telling tot `.pem`   bestanden is deze container volledig versleuteld.  

U kunt OpenSSL gebruiken om het bestand in een `.pem`   bestand met open bare en persoonlijke sleutels in te scha kelen: `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**. der-binary encoded PEM**.

De manier om ASN. 1-syntaxis in binaire code te coderen, bevindt zich via een `.pem`   bestand, dat slechts een base64-gecodeerd `.der` bestand is. 

OpenSSL kan deze bestanden converteren naar een `.pem` :  `openssl x509 -inform der -in to-convert.der -out converted.pem` .  

Windows herkent deze bestanden als certificaat bestanden. Standaard exporteert Windows certificaten als `.der` geformatteerde bestanden met een andere extensie.

**. CRL: certificaat intrekkings lijst**.  

Certificerings instanties produceren deze als een manier om certificaten te autoriseren vóór de verval datum. 

#### <a name="cli-commands"></a>CLI-opdrachten

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

| Beschrijving | CLI-opdracht |
|--|--|
| Een nieuwe aanvraag voor een persoonlijke sleutel en certificaat ondertekening genereren | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| Een zelfondertekend certificaat maken | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| Een aanvraag voor certificaat ondertekening (CSR) genereren voor een bestaande persoonlijke sleutel | `openssl req -out CSR.csr -key privateKey.key -new` |
| Een aanvraag voor certificaat ondertekening genereren op basis van een bestaand certificaat | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| Een wachtwoordzin uit een persoonlijke sleutel verwijderen | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

Als u de gegevens in een certificaat, CSR of persoonlijke sleutel wilt controleren, gebruikt u deze opdrachten.

| Beschrijving | CLI-opdracht |
|--|--|
| Een aanvraag voor certificaat ondertekening controleren (CSR) | `openssl req -text -noout -verify -in CSR.csr` |
| Een persoonlijke sleutel controleren | `openssl rsa -in privateKey.key -check` |
| Een certificaat controleren | `openssl x509 -in certificate.crt -text -noout`  |

Als er een fout bericht wordt weer gegeven dat de persoonlijke sleutel niet overeenkomt met het certificaat, of dat een certificaat dat u hebt geïnstalleerd op een site niet wordt vertrouwd, gebruikt u deze opdrachten om de fout op te lossen.

| Beschrijving | CLI-opdracht |
|--|--|
| Een MD5-hash van de open bare sleutel controleren om ervoor te zorgen dat deze overeenkomt met wat er in een CSR of persoonlijke sleutel staat | i. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> twee. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

Als u certificaten en sleutels naar verschillende indelingen wilt converteren om ze compatibel te maken met specifieke typen servers of software, gebruikt u deze opdrachten.

| Beschrijving | CLI-opdracht |
|--|--|
| Een DER-bestand (. CRT. cer. der) converteren naar PEM  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| Een PEM-bestand converteren naar DER | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| Een PKCS # 12-bestand (. pfx. p12) met een persoonlijke sleutel en certificaten naar PEM converteren | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />U kunt `-nocerts` alleen toevoegen om de persoonlijke sleutel uit te voeren, of `-nokeys` alleen toevoegen om de certificaten uit te voeren. |
| Een PEM-certificaat bestand en een persoonlijke sleutel converteren naar PKCS # 12 (. pfx. p12) | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="define-backup-and-restore-settings"></a>Instellingen voor back-up en herstel definiëren

De on-premises beheer console systeem back-up wordt automatisch, dagelijks uitgevoerd. De gegevens worden opgeslagen op een andere schijf. De standaardlocatie is `/var/cyberx/backups`. 

U kunt dit bestand automatisch overdragen naar het interne netwerk. 

> [!NOTE]
> U kunt de back-up-en herstel procedure alleen op dezelfde versie uitvoeren. 

Een back-up maken van de on-premises beheer console computer: 

- Meld u aan bij een Administrator-account en voer in `sudo cyberx-management-backup -full` .

Het meest recente back-upbestand herstellen:

- Meld u aan bij een Administrator-account en voer in `$ sudo cyberx-management-system-restore` .

De back-up opslaan op een externe SMB-server:

1. Maak een gedeelde map op de externe SMB-server.  

   Het mappad, de gebruikers naam en het wacht woord ophalen dat is vereist voor toegang tot de SMB-server. 

2. In Defender voor IoT maakt u een map voor de back-ups:

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. Fstab bewerken:  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. Referenties voor de te delen SMB-server bewerken of maken: 

   - `sudo nano /etc/samba/user` 

5. Voeg toe: 

   - `username=<user name>`

   - `password=<password>` 

6. De map koppelen: 

   - `sudo mount -a` 

7. Een back-upmap configureren in de gedeelde map op de on-premises beheer console van Defender voor IoT:  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>De hostnaam bewerken

De hostnaam van de beheer console bewerken die is geconfigureerd op de DNS-server van de organisatie:

1. Selecteer **systeem instellingen** in het linkerdeel venster van de beheer console.  

2. Selecteer in de sectie netwerken van de console de optie **netwerk**. 

3. Voer de hostnaam in die is geconfigureerd op de DNS-server van de organisatie. 

4. Selecteer **Opslaan**.

## <a name="define-vlan-names"></a>VLAN-namen definiëren

VLAN-namen worden niet gesynchroniseerd tussen de sensor en de beheer console. U moet identieke namen definiëren voor onderdelen.

Selecteer **VLAN** in het gebied netwerk en Voeg namen toe aan de GEDETECTEERDe VLAN-id's. Selecteer vervolgens **Opslaan**.

## <a name="define-a-proxy-to-sensors"></a>Een proxy voor Sens oren definiëren

Verbeter de beveiliging van het systeem door de gebruikers aanmelding rechtstreeks bij de sensor te voor komen. Gebruik in plaats daarvan proxy tunneling om gebruikers toegang te geven tot de sensor vanuit de on-premises beheer console met één firewall regel. Deze uitbrei ding beperkt de kans op onbevoegde toegang tot de netwerk omgeving buiten de sensor.

Gebruik een proxy in omgevingen waar er geen directe connectiviteit is met Sens oren.

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="Gebruiker.":::

Met de volgende procedure verbindt u een sensor met de on-premises beheer console en maakt u tunneling mogelijk op die console:

1. Meld u aan bij de on-premises beheer console apparaat CLI met beheerders referenties.

2. Typ `sudo cyberx-management-tunnel-enable` en selecteer **Enter**.

4. Typ `--port 10000` en selecteer **Enter**.

## <a name="adjust-system-properties"></a>Systeem eigenschappen aanpassen

Systeem eigenschappen bepalen verschillende bewerkingen en instellingen in de beheer console. Het bewerken of aanpassen hiervan kan de bewerking van de beheer console beschadigen. Neem contact op met [Microsoft ondersteuning](https://support.microsoft.com) voordat u de instellingen wijzigt.

Om toegang te krijgen tot systeem eigenschappen: 

1. Meld u aan bij de on-premises beheer console of de sensor.

2. Selecteer **systeem instellingen**.

3. Selecteer **systeem eigenschappen** in de sectie **Algemeen** .

## <a name="change-the-name-of-the-on-premises-management-console"></a>De naam van de on-premises beheer console wijzigen

U kunt de naam van de on-premises beheer console wijzigen. De nieuwe namen worden weer gegeven in de webbrowser van de console, in verschillende console vensters en in Logboeken voor probleem oplossing. De standaard naam is **beheer console**.

Ga als volgt te werk om de naam te wijzigen:

1. Selecteer de huidige naam onder in het linkerdeel venster.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="Scherm opname van de versie van de on-premises beheer console.":::

2. Voer in het dialoog venster **configuratie van beheer console bewerken** de nieuwe naam in. De naam mag niet langer zijn dan 25 tekens.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="Scherm opname van het bewerken van de Defender voor IoT-platform configuratie.":::

3. Selecteer **Opslaan**. De nieuwe naam wordt toegepast.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="Scherm opname van de gewijzigde naam van de console.":::

## <a name="password-recovery"></a>Wachtwoord herstel

Wachtwoord herstel voor uw on-premises beheer console is gekoppeld aan het abonnement waaraan het apparaat is gekoppeld. U kunt een wacht woord niet herstellen als u niet weet welk abonnement aan een apparaat is gekoppeld.

Uw wachtwoord opnieuw instellen:

1. Ga naar de aanmeldings pagina van de on-premises beheer console.
1. Selecteer **wachtwoord herstel**.
1. Kopieer de unieke id.
1. Ga naar de pagina Defender voor IoT- **sites en Sens oren** en selecteer het tabblad **mijn wacht woord herstellen** .
1. Voer de unieke id in en selecteer **herstellen**. Het activerings bestand wordt gedownload.
1. Ga naar de pagina voor **wachtwoord herstel** en upload het activerings bestand.
1. Selecteer **Next**.
 
   U krijgt nu uw gebruikers naam en een nieuw door het systeem gegenereerd wacht woord.

> [!NOTE]
> De sensor is gekoppeld aan het abonnement waarmee deze oorspronkelijk was verbonden. U kunt het wacht woord alleen herstellen door gebruik te maken van hetzelfde abonnement dat is gekoppeld aan.

## <a name="update-the-software-version"></a>De software versie bijwerken

In de volgende procedure wordt beschreven hoe u de software versie van de on-premises beheer console bijwerkt. Het update proces duurt ongeveer 30 minuten.

1. Ga naar de [Azure Portal](https://portal.azure.com/).

1. Ga naar Defender voor IoT.

1. Ga naar de pagina **updates** .

1. Selecteer een versie in de sectie on-premises beheer console.

1. Selecteer **Downloaden** en sla het bestand op.

1. Meld u aan bij de on-premises beheer console en selecteer **systeem instellingen** in het menu aan de zijkant.

1. Selecteer in het deel venster **versie bijwerken** de optie **bijwerken**.

1. Selecteer het bestand dat u hebt gedownload van de pagina Defender voor IoT- **updates** .

## <a name="mail-server-settings"></a>E-mailserver instellingen

Instellingen voor de SMTP-mail server definiëren voor de on-premises beheer console.

Definiëren:

1. Meld u aan bij de CLI voor het on-premises beheer met beheerders referenties.
1. Typ ```nano /var/cyberx/properties/remote-interfaces.properties```.
1. Selecteer Enter. De volgende prompts worden weer gegeven.
```mail.smtp_server= ```
```mail.port=25 ```
```mail.sender=```
1. Voer de naam van de SMTP-server en de afzender in en selecteer ENTER.

## <a name="see-also"></a>Zie ook

[Sens oren beheren vanuit de beheer console](how-to-manage-sensors-from-the-on-premises-management-console.md)

[Afzonderlijke sensoren beheren](how-to-manage-individual-sensors.md)
