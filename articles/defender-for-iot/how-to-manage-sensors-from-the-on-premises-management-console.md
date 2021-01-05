---
title: Sens oren beheren vanuit de on-premises beheer console
description: Meer informatie over het beheren van Sens oren vanuit de beheer console, inclusief het bijwerken van sensor versies, het pushen van systeem instellingen naar Sens oren en het inschakelen en uitschakelen van engines op Sens oren.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 36db1b23d8fb17cec4fe981c938f8c7003543b4d
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839834"
---
# <a name="manage-sensors-from-the-management-console"></a>Sens oren beheren vanuit de beheer console

In dit artikel wordt beschreven hoe u Sens oren beheert vanuit de beheer console, met inbegrip van:

- Push systeem instellingen naar Sens oren

- Engines op Sens oren in-en uitschakelen

- Sensor versies bijwerken

## <a name="push-configurations"></a>Push configuraties

U kunt verschillende systeem instellingen definiëren en deze automatisch Toep assen op Sens oren die zijn verbonden met de beheer console. Dit bespaart tijd en zorgt voor gestroomlijnde instellingen voor uw Enter prise Sens oren.

U kunt de volgende systeem instellingen voor de sensor definiëren vanuit de beheer console:

- E-mail server

- Bewaking van SNMP MIB

- Active Directory

- DNS-instellingen

- Subnetten

- Poort aliassen

Systeem instellingen Toep assen:

1. Selecteer **systeem instellingen** in het linkerdeel venster van de console.

2. Selecteer een van de opties in het deel venster **Sens oren configureren** .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="De opties voor systeem instellingen voor een sensor.":::

   In het volgende voor beeld wordt beschreven hoe u para meters voor de mail server definieert voor uw Enter prise Sens oren.

3. Selecteer **e-mail server**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Selecteer uw e-mail server in het scherm systeem instellingen.":::

4. Selecteer een sensor aan de linkerkant.

5. Stel de mailserver-para meters in en selecteer **dupliceren**. Elk item in de sensor structuur wordt weer gegeven met een selectie vakje ernaast.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="Zorg ervoor dat de selectie vakjes zijn ingeschakeld voor uw Sens oren.":::

6. Selecteer in de sensor structuur de items waarop u de configuratie wilt Toep assen.

7. Selecteer **Opslaan**.

## <a name="update-versions"></a>Update versies

U kunt verschillende Sens oren tegelijk bijwerken vanuit de on-premises beheer console.

Meerdere Sens oren bijwerken:

1. Ga naar [Azure Portal](https://portal.azure.com/).

2. Ga naar Azure Defender voor IoT.

3. Ga naar de pagina **updates** .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="Scherm afbeelding van de dashboard weergave updates.":::

4. Selecteer **downloaden** in het gedeelte **Sens oren** en sla het bestand op.

5. Meld u aan bij de beheer console en selecteer **systeem instellingen**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="Scherm opname van het menu beheer om systeem instellingen te selecteren.":::

6. Markeer de Sens oren die u wilt bijwerken in het gedeelte **configuratie van sensor engine** en selecteer vervolgens **Automatische updates**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="Twee Sens oren met de leer modus en automatische updates.":::

7. Selecteer **Save changes**.

8. Selecteer in het deel venster **versie-upgrade sensors** de optie :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/plus-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/display-files.png" alt-text="Scherm voor het bijwerken van de sensor versie om bestanden weer te geven.":::

9. Het dialoog venster **bestand uploaden** wordt geopend. Upload het bestand dat u hebt gedownload van de pagina **updates** .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="Selecteer de knop Bladeren om het bestand te uploaden.":::

10. Tijdens het update proces wordt de update status van elke sensor weer gegeven in het venster **site beheer** .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="Bekijk de voortgang van de update.":::

## <a name="update-threat-intelligence-packages"></a>Update van Threat Intelligence-pakketten 

Het gegevens pakket voor bedreigings informatie wordt geleverd met elke nieuwe versie van Defender voor IoT, of, indien nodig, tussen releases. Het pakket bevat hand tekeningen (inclusief malware-hand tekeningen), CVEs en andere beveiligings inhoud. 

U kunt dit bestand hand matig uploaden vanaf de pagina met **updates** voor de Defender voor IOT-Portal en deze automatisch bijwerken naar Sens oren. 

De bedreigings informatie gegevens bijwerken: 

1. Ga naar de pagina Defender voor IoT- **updates** . 

2. Down load het bestand en sla het op.

3. Meld u aan bij de beheer console. 

4. Selecteer **systeem instellingen** in het menu aan de zijkant. 

5. Selecteer de Sens oren die de update moeten ontvangen in het gedeelte **configuratie van sensor engine** .  

6. Selecteer in de sectie **Threat Intelligence-gegevens selecteren** het plus teken ( **+** ). 

7. Upload het pakket dat u hebt gedownload van de pagina Defender voor IoT- **updates** .

## <a name="understand-sensor-disconnection-events"></a>Verbindings gebeurtenissen van sensors begrijpen

In het venster **site manager** wordt informatie over de ontkoppeling weer gegeven als Sens oren de verbinding met hun toegewezen on-premises beheer console verbreken. De volgende verbindings gegevens voor de sensor zijn beschikbaar:

- "De on-premises beheer console kan geen gegevens verwerken die van de sensor zijn ontvangen."

- ' Tijden drift gedetecteerd. De verbinding tussen de on-premises beheer console en de sensor is verbroken.

- ' De sensor communiceert niet met on-premises beheer console. Controleer de netwerk verbinding of de validatie van het certificaat.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Scherm opname van de weer gave zone 1.":::

U kunt waarschuwingen naar derden verzenden met informatie over niet-verbonden Sens oren. Zie [problemen met fout meldingen door sturen](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts)voor meer informatie.

## <a name="enable-or-disable-sensors"></a>Sens oren in-of uitschakelen

Sens oren worden beschermd door vijf Defender voor IoT-engines. U kunt de engines voor verbonden Sens oren in-of uitschakelen.

| Engine | Beschrijving | Voorbeeldscenario |
|--|--|--|
| Engine voor protocol overtreding | Een schending van het protocol treedt op wanneer de pakket structuur of veld waarden niet voldoen aan de protocol specificatie. | De waarschuwing ongeldige MODBUS-bewerking (functie code is nul). Deze waarschuwing geeft aan dat een primair apparaat een aanvraag met functie code 0 naar een secundair apparaat heeft verzonden. Dit is niet toegestaan volgens de protocol specificatie en het secundaire apparaat kan de invoer mogelijk niet correct afhandelen. |
| Engine voor beleids overtreding | Een beleids schending treedt op met een afwijking van de basislijn gedrag die is gedefinieerd in het geleerde of geconfigureerde beleid. | Waarschuwing voor niet-geautoriseerde HTTP-gebruikers agent. Deze waarschuwing geeft aan dat een toepassing die niet is geleerd of goedgekeurd door het beleid wordt gebruikt als een HTTP-client op een apparaat. Dit kan een nieuwe webbrowser of toepassing op dat apparaat zijn. |
| Malware-engine | De malware-engine detecteert schadelijke netwerk activiteit. | Waarschuwing voor het vermoeden van schadelijke activiteiten (Stuxnet). Deze waarschuwing geeft aan dat de sensor verdachte netwerk activiteit heeft gevonden die te maken heeft met de Stuxnet malware. Dit is een geavanceerde permanente bedreiging die is gericht op industrieel beheer en SCADA netwerken. |
| Afwijkende engine | De malware-engine detecteert een afwijking in het netwerk gedrag. | "Periodiek gedrag in communicatie kanaal". Dit is een onderdeel dat netwerk verbindingen inspecteert en het periodieke of cyclische gedrag van gegevens overdracht vindt, wat gebruikelijk is in industriële netwerken. |
| Operationele engine | Deze engine detecteert operationele incidenten of werkt niet goed. | De waarschuwing ' het activum wordt verdacht om de verbinding te verbreken (reageert niet). Deze waarschuwing wordt geactiveerd wanneer een apparaat niet reageert op aanvragen voor een vooraf gedefinieerde periode. Dit kan erop duiden dat het apparaat wordt afgesloten, verbroken of niet goed werkt.
|

Engines voor verbonden Sens oren in-of uitschakelen:

1. Selecteer **systeem instellingen** in het linkerdeel venster van de console.

2. Selecteer in de sectie **sensor-Engine configureren** de optie **inschakelen** of **uitschakelen** voor de engines.
         
3. Selecteer **wijzigingen opslaan**.

   Er wordt een rood uitroep teken weer gegeven als de ingeschakelde engines niet overeenkomen met een van uw Enter prise Sens oren. De engine kan rechtstreeks van de sensor zijn uitgeschakeld.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="De ingeschakelde engines komen niet overeen."::: 

## <a name="define-sensor-backup-schedules"></a>Back-upplanningen voor sensors definiëren

U kunt sensor back-ups plannen en back-ups op aanvraag uitvoeren vanuit de on-premises beheer console. Zo kunt u zich beschermen tegen storingen van harde schijven en gegevens verlies.

- Waarvan wordt een back-up gemaakt: configuraties en gegevens.

- Waarvan geen back-up wordt gemaakt: PCAP bestanden en Logboeken. U kunt hand matig back-ups maken en PCAPs en logboeken herstellen.

Sens oren worden standaard automatisch een back-up gemaakt op 3:00 uur. Met de functie voor back-upplanning voor Sens oren kunt u deze back-ups verzamelen en opslaan op de on-premises beheer console of op een externe back-upserver. Het kopiëren van bestanden van Sens oren naar de on-premises beheer console vindt plaats via een versleuteld kanaal.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="Een weer gave van het back-upscherm van de sensor.":::

Wanneer de standaard locatie voor de back-upsensor wordt gewijzigd, haalt de on-premises beheer console automatisch de bestanden op van de nieuwe locatie op de sensor of een externe locatie, op voor waarde dat de console toegangs rechten heeft voor de locatie. 

Wanneer de Sens oren niet zijn geregistreerd bij de on-premises beheer console, wordt in het dialoog venster **back-upschema van sensor** aangegeven dat er geen Sens oren worden beheerd.  

Het herstel proces is hetzelfde, ongeacht waar de bestanden worden opgeslagen.

### <a name="backup-storage-for-sensors"></a>Back-upopslag voor Sens oren

U kunt de on-premises beheer console gebruiken voor het onderhouden van Maxi maal negen back-ups voor elke beheerde sensor, op voor waarde dat de back-upbestanden niet groter zijn dan de maximale back-upruimte die is toegewezen. 

De beschik bare ruimte wordt berekend op basis van het beheer console model waarmee u werkt: 

- **Productie model**: standaard opslag is 40 GB; de limiet is 100 GB. 

- **Gemiddeld model**: standaard opslag is 20 GB; de limiet is 50 GB. 

- **Laptop model**: standaard opslag is 10 GB; de limiet is 25 GB. 

- **Dun model**: standaard opslag is 2 GB; de limiet is 4 GB. 

- **Robuust model**: standaard opslag is 10 GB; de limiet is 25 GB. 

De standaard toewijzing wordt weer gegeven in het dialoog venster **back-upschema van sensor** . 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="Het scherm e-mail server configuratie bewerken.":::

Er is geen opslag limiet wanneer u een back-up maakt op een externe server. U moet echter een bovengrens voor de limiet definiëren in het veld aangepast pad voor de **sensor back-upschema**  >   . De volgende cijfers en tekens worden ondersteund: `/, a-z, A-Z, 0-9, and _` . 

Hier vindt u informatie over het overschrijden van limieten voor toewijzings opslag:

- Als u de toegewezen opslag ruimte overschrijdt, wordt er geen back-up van de sensor gemaakt. 

- Als u een back-up maakt van meer dan één sensor, probeert de beheer console sensor bestanden voor de beheerde Sens oren op te halen.  

- Als het ophalen van de ene sensor de limiet overschrijdt, probeert de beheer console back-upgegevens op te halen van de volgende sensor. 

Wanneer u het behouden aantal gedefinieerde back-ups overschrijdt, wordt het oudste back-upbestand verwijderd voor de nieuwe.

Back-upbestanden van de sensor hebben automatisch de naam in de volgende indeling: `<sensor name>-backup-version-<version>-<date>.tar` . Bijvoorbeeld: `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`. 

Back-ups maken Sens oren:

1. Selecteer **plan sensor backup** in het venster **systeem instellingen** . Sens oren die uw on-premises beheer console beheert, worden weer gegeven in het dialoog venster **back-upschema van sensor** .  

2. Schakel de optie **back-ups verzamelen** in.  

3. Selecteer een kalender interval, datum en tijd zone. De tijd notatie is gebaseerd op een 24-uurs klok. Voer bijvoorbeeld 6:00 PM in als **18:00**. 

4. Voer in het veld **toewijzing van back-upopslag** de opslag in die u wilt toewijzen voor uw back-ups. U ontvangt een melding als u de maximale ruimte overschrijdt.

5. In het veld **laatste bewaren** geeft u het aantal back-ups per sensor op dat u wilt behouden. Wanneer de limiet wordt overschreden, wordt de oudste back-up verwijderd.  

6. Kies een back-uplocatie:  

   - Als u een back-up wilt maken naar de on-premises beheer console, schakelt u het **aangepaste pad** in of uit. De standaardlocatie is `/var/cyberx/sensor-backups`.  

   - Als u een back-up wilt maken naar een externe server, schakelt u het **aangepaste pad** in en voert u een locatie in. De volgende cijfers en tekens worden ondersteund: `/, a-z, A-Z, 0-9, and, _` . 

7. Selecteer **Opslaan**. 

Direct een back-up maken: 

- Selecteer **Nu back-up maken**. De on-premises beheer console maakt en verzamelt sensor back-upbestanden. 

### <a name="receiving-backup-notifications-for-sensors"></a>Back-upmeldingen ontvangen voor Sens oren 

In het dialoog venster **back-upschema** van de sensor en het back-uplogboek wordt automatisch informatie weer geven over geslaagde back-ups en fouten.  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="Bekijk uw Sens oren en de locatie waar ze zich bevinden en alle relevante informatie.":::

Er kunnen fouten optreden vanwege:    

- Er is geen back-upbestand gevonden. 

- Er is een bestand gevonden, maar dit kan niet worden opgehaald.  

- Er is een netwerk verbindings fout opgetreden. 

- Er is onvoldoende ruimte toegewezen aan de on-premises beheer console om de back-up te volt ooien.  

U kunt een e-mail melding, syslog-updates en systeem meldingen verzenden wanneer er een fout optreedt. U doet dit door een doorstuur regel te maken in **systeem meldingen**. 

### <a name="restoring-sensors"></a>Sens oren herstellen 

U kunt back-ups herstellen vanuit de on-premises beheer console en door gebruik te maken van de CLI.  

Herstellen vanaf de-console: 

- Selecteer **installatie kopie herstellen** in het venster **sensor systeem** instelling.

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="Herstel een back-up van uw installatie kopie.":::

  Vervolgens worden er herstel fouten weer gegeven in de console.  

Herstellen met behulp van de CLI: 

- Meld u aan bij een Administrator-account en voer in `$ sudo cyberx-xsense-system-restore` . 

### <a name="save-a-sensor-backup-to-an-external-smb-server"></a>Een sensor back-up opslaan op een externe SMB-server

Als u een SMB-server wilt instellen, kunt u een sensor back-up opslaan op een extern station: 

1. Maak een gedeelde map op de externe SMB-server. 

2. Het mappad, de gebruikers naam en het wacht woord ophalen dat is vereist voor toegang tot de SMB-server. 

3. In Defender voor IoT maakt u een map voor de back-ups: 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

4. Fstab bewerken:  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

5. Bewerk of maak referenties om te delen. Dit zijn de referenties voor de SMB-server: 

   `sudo nano /etc/samba/user` 

6. Voeg toe:  

   `username=<user name>` 

   `password=<password>` 

7. De map koppelen: 

   `sudo mount -a` 

8. Een back-upmap configureren in de gedeelde map op de Defender voor IoT-sensor:  

   `sudo nano /var/cyberx/properties/backup.properties` 

9. Ingesteld `Backup.shared_location` op `<backup_folder_name_on_cyberx_server>` .

## <a name="see-also"></a>Zie tevens

[Afzonderlijke Sens oren beheren](how-to-manage-individual-sensors.md)
