---
title: Verbinding maken met SFTP-server met SSH
description: Automatiseer taken die bestanden voor een SFTP-server bewaken, maken, beheren, verzenden en ontvangen met behulp van SSH en Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/19/2021
tags: connectors
ms.openlocfilehash: a19253e117f748b4d4045bfd2a29552018bba91e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781555"
---
# <a name="create-and-manage-sftp-files-using-ssh-and-azure-logic-apps"></a>SFTP-bestanden maken en beheren met SSH en Azure Logic Apps

Als u taken wilt automatiseren die bestanden maken en beheren op een [SFTP-server (Secure File Transfer Protocol)](https://www.ssh.com/ssh/sftp/) met behulp van het [SSH-protocol (Secure Shell),](https://www.ssh.com/ssh/protocol/) kunt u geautomatiseerde integratiewerkstromen maken met behulp van Azure Logic Apps en de SFTP-SSH-connector. SFTP is een netwerkprotocol dat bestandstoegang, bestandsoverdracht en bestandsbeheer mogelijk maakt via elke betrouwbare gegevensstroom.

Hier volgen enkele voorbeeldtaken die u kunt automatiseren:

* Controleer wanneer bestanden worden toegevoegd of gewijzigd.
* Bestanden downloaden, maken, kopiëren, hernoemen, bijwerken, lijst en verwijderen.
* Mappen maken.
* Bestandsinhoud en metagegevens downloaden.
* Archieven uitpakken in mappen.

In uw werkstroom kunt u een trigger gebruiken die gebeurtenissen op uw SFTP-server bewaakt en uitvoer beschikbaar maakt voor andere acties. Vervolgens kunt u acties gebruiken om verschillende taken uit te voeren op uw SFTP-server. U kunt ook andere acties opnemen die gebruikmaken van de uitvoer van SFTP-SSH-acties. Als u bijvoorbeeld regelmatig bestanden op haalt van uw SFTP-server, kunt u e-mailwaarschuwingen over deze bestanden en hun inhoud verzenden met behulp van de Office 365 Outlook-connector of Outlook.com-connector. Als u geen toegang hebt tot logische apps, bekijkt [u Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Voor verschillen tussen de SFTP-SSH-connector en de SFTP-connector, bekijkt u de sectie [SFTP-SSH](#comparison) vergelijken met SFTP verder op dit onderwerp.

## <a name="limits"></a>Limieten

* De SFTP-SSH-connector biedt momenteel geen ondersteuning voor deze SFTP-servers:

  * IBM DataPower
  * MessageWay
  * OpenText Secure MFT
  * OpenText GXS

* SFTP-SSH-acties [](../logic-apps/logic-apps-handle-large-messages.md) die segmentering ondersteunen, kunnen bestanden van maximaal 1 GB verwerken, terwijl SFTP-SSH-acties die geen ondersteuning bieden voor chunking bestanden tot 50 MB kunnen verwerken. De standaard chunkgrootte is 15 MB. Deze grootte kan echter dynamisch veranderen, beginnend bij 5 MB en geleidelijk verhogen tot het maximum van 50 MB. Dynamische grootten zijn gebaseerd op factoren zoals netwerklatentie, reactietijd van de server, en meer.

  > [!NOTE]
  > Voor logische apps in een [ISE (Integration Service Environment) is](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)voor de ise-gelabelde versie van deze connector segmentering vereist om in plaats daarvan de [ISE-berichtlimieten te](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) gebruiken.

  U kunt dit adaptieve gedrag overschrijven wanneer u in plaats daarvan een [constante chunkgrootte](#change-chunk-size) opgeeft die moet worden gebruikt. Deze grootte kan variëren van 5 MB tot 50 MB. Stel bijvoorbeeld dat u een bestand van 45 MB en een netwerk hebt dat ondersteuning biedt voor die bestandsgrootte zonder latentie. Adaptieve segmentering resulteert in verschillende aanroepen, in plaats van die ene aanroep. Als u het aantal aanroepen wilt verminderen, kunt u proberen een chunkgrootte van 50 MB in te stellen. Als er in een ander scenario een time-out is voor uw logische app, bijvoorbeeld wanneer u segmenten van 15 MB gebruikt, kunt u proberen de grootte te verkleinen tot 5 MB.

  De chunkgrootte is gekoppeld aan een verbinding. Dit kenmerk betekent dat u dezelfde verbinding kunt gebruiken voor acties die ondersteuning bieden voor segmentering en acties die geen ondersteuning bieden voor segmentering. In dit geval varieert de segmentgrootte voor acties die geen ondersteuning bieden voor segmentering van 5 MB tot 50 MB. In deze tabel ziet u welke SFTP-SSH-acties ondersteuning bieden voor segmentering:

  | Bewerking | Ondersteuning voor segmentering | Ondersteuning voor segmentgrootte overschrijven |
  |--------|------------------|-----------------------------|
  | **Bestand kopiëren** | No | Niet van toepassing |
  | **Bestand maken** | Ja | Ja |
  | **Map maken** | Niet van toepassing | Niet van toepassing |
  | **Bestand verwijderen** | Niet van toepassing | Niet van toepassing |
  | **Archief uitpakken naar map** | Niet van toepassing | Niet van toepassing |
  | **Bestandsinhoud downloaden** | Ja | Ja |
  | **Bestandsinhoud downloaden met behulp van pad** | Ja | Ja |
  | **Bestandsmetagegevens downloaden** | Niet van toepassing | Niet van toepassing |
  | **Metagegevens van bestanden downloaden met behulp van pad** | Niet van toepassing | Niet van toepassing |
  | **Bestanden in map weergeven** | Niet van toepassing | Niet van toepassing |
  | **Naam van bestand wijzigen** | Niet van toepassing | Niet van toepassing |
  | **Bestand bijwerken** | No | Niet van toepassing |
  ||||

* SFTP-SSH-triggers bieden geen ondersteuning voor het segmenteren van berichten. Bij het aanvragen van bestandsinhoud selecteren triggers alleen bestanden van 15 MB of kleiner. Als u bestanden wilt op halen die groter zijn dan 15 MB, volgt u in plaats daarvan dit patroon:

  1. Gebruik een SFTP-SSH-trigger die alleen bestandseigenschappen retourneert. Deze triggers hebben namen die de beschrijving bevatten, **(alleen eigenschappen).**

  1. Volg de trigger met de actie  Bestandsinhoud van SFTP-SSH downloaden. Met deze actie wordt het volledige bestand gelezen en wordt impliciet bericht segmentering gebruikt.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>SFTP-SSH vergelijken met SFTP

In de volgende lijst worden de belangrijkste SFTP-SSH-mogelijkheden beschreven die verschillen van de SFTP-connector:

* Maakt gebruik [SSH.NET bibliotheek](https://github.com/sshnet/SSH.NET), een opensource SSH-bibliotheek (Secure Shell) die ondersteuning biedt voor .NET.

* Biedt de **actie Map** maken, waarmee een map wordt gemaakt op het opgegeven pad op de SFTP-server.

* Biedt de **actie Bestandsnaam wijzigen,** waarmee de naam van een bestand op de SFTP-server wordt gewijzigd.

* De verbinding met de SFTP-server wordt maximaal 1 uur in de cache *opgeslagen.* Deze mogelijkheid verbetert de prestaties en vermindert hoe vaak de connector verbinding probeert te maken met de server. Als u de duur voor dit cachinggedrag wilt instellen, bewerkt u de eigenschap [ **ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) in de SSH-configuratie op uw SFTP-server.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Uw SFTP-serveradres en accountreferenties, zodat uw werkstroom toegang heeft tot uw SFTP-account. U hebt ook toegang nodig tot een persoonlijke SSH-sleutel en het wachtwoord voor de persoonlijke SSH-sleutel. Als u grote bestanden wilt uploaden met behulp van chunking, hebt u zowel lees- als schrijftoegang nodig voor de hoofdmap op uw SFTP-server. Anders wordt de foutmelding '401 Niet geautoriseerd' weergegeven.

  De SFTP-SSH-connector ondersteunt zowel verificatie met een persoonlijke sleutel als wachtwoordverificatie. De SFTP-SSH-connector ondersteunt echter *alleen* deze persoonlijke sleutelindelingen, algoritmen en vingerafdrukken:

  * **Indelingen van persoonlijke sleutels:** RSA-sleutels (Rivest Shamir Adleman) en DSA-sleutels (Digital Signature Algorithm) in zowel OpenSSH- als ssh.com-indelingen. Als uw persoonlijke sleutel de bestandsindeling PuTTY (.ppk) heeft, converteert u de sleutel eerst naar de [bestandsindeling OpenSSH (.pem).](#convert-to-openssh)
  * Versleutelingsalgoritmen: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC en AES-256-CBC
  * **Vingerafdruk:** MD5

  Nadat u een SFTP-SSH-trigger of -actie aan uw werkstroom hebt toevoegen, moet u verbindingsgegevens voor uw SFTP-server verstrekken. Wanneer u uw persoonlijke SSH-sleutel voor deze verbinding op geeft,**voert** u * de sleutel niet handmatig in of bewerkt u deze niet, waardoor de verbinding kan mislukken. Zorg er in plaats daarvan voor dat _*_u de sleutel_*_ uit uw persoonlijke SSH-sleutelbestand kopieert en _ *_plak_** deze sleutel in de verbindingsgegevens. Zie de sectie Verbinding maken met [SFTP met SSH](#connect) verder in dit artikel voor meer informatie.

* Basiskennis van [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De werkstroom van de logische app waar u toegang wilt krijgen tot uw SFTP-account. Als u wilt beginnen met een SFTP-SSH-trigger, [maakt u een lege werkstroom voor logische apps.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Als u een SFTP-SSH-actie wilt gebruiken, start u uw werkstroom met een andere trigger, bijvoorbeeld de **trigger Terugkeerpatroon.**

## <a name="how-sftp-ssh-triggers-work"></a>Hoe werken SFTP-SSH-triggers

<a name="polling-behavior"></a>

### <a name="polling-behavior"></a>Pollinggedrag

SFTP-SSH-triggers peilen het SFTP-bestandssysteem en zoeken naar een bestand dat is gewijzigd sinds de laatste poll. Met sommige hulpprogramma's kunt u de tijdstempel behouden wanneer de bestanden worden gewijzigd. In dergelijke gevallen moet u deze functie uitschakelen, zodat uw trigger kan werken. Hier zijn enkele algemene instellingen:

| SFTP-client | Bewerking |
|-------------|--------|
| Winscp | Ga naar **Optiesvoorkeuren**  >    >    >  **Overdracht Bewerken**  >  **Tijdstempel behouden**  >  **uitschakelen** |
| Filezilla | Ga naar **Tijdstempels** van overgedragen bestanden  >  **behouden**  >   uitschakelen |
|||

Wanneer een trigger een nieuw bestand vindt, controleert de trigger of het nieuwe bestand is voltooid en niet gedeeltelijk is geschreven. Een bestand kan bijvoorbeeld wijzigingen hebben die worden uitgevoerd wanneer de trigger de bestandsserver controleert. Om te voorkomen dat een gedeeltelijk geschreven bestand wordt retourneren, noteert de trigger het tijdstempel voor het bestand dat recente wijzigingen heeft, maar dat bestand niet onmiddellijk retourneert. De trigger retourneert het bestand alleen wanneer de server opnieuw wordt gepeild. Soms kan dit gedrag een vertraging veroorzaken die tot tweemaal het polling-interval van de trigger is.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Terugkeerpatroon en drift activeren

Triggers op basis van verbindingen waarbij u eerst een verbinding moet maken, zoals de SFTP-SSH-trigger, verschillen van ingebouwde triggers die native worden uitgevoerd in Azure Logic Apps, zoals de trigger [Terugkeerpatroon.](../connectors/connectors-native-recurrence.md) In terugkerende triggers op basis van verbindingen is het terugkeerschema niet het enige stuurprogramma dat de uitvoering bepaalt en bepaalt de tijdzone alleen de initiële begintijd. Volgende uitvoeringen zijn afhankelijk van het terugkeerschema, de laatste uitvoering van de trigger *en* andere factoren die ertoe kunnen leiden dat uitvoeringstijden veranderen of onverwacht gedrag produceren. Onverwacht gedrag kan bijvoorbeeld zijn dat het niet lukt om de opgegeven planning bij te houden wanneer de zomer- en wintertijd (DST) wordt gestart en beëindigd. Om ervoor te zorgen dat de terugkeertijd niet verschuift wanneer DST van kracht wordt, past u het terugkeerpatroon handmatig aan. Op die manier blijft uw werkstroom op het verwachte tijdstip worden uitgevoerd. Anders verschuift de begintijd één uur vooruit wanneer DST wordt gestart en één uur achterwaarts wanneer DST eindigt. Zie Terugkeerpatroon voor [triggers op basis van verbindingen voor meer informatie.](../connectors/apis-list.md#recurrence-for-connection-based-triggers)

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Op PuTTY gebaseerde sleutel converteren naar OpenSSH

De PuTTY-indeling en OpenSSH-indeling gebruiken verschillende bestandsnaamextensies. De PuTTY-indeling maakt gebruik van de bestandsnaamextensie .ppk of PuTTY Private Key. De OpenSSH-indeling maakt gebruik van de bestandsnaamextensie .pem of Privacy Enhanced Mail. Als uw persoonlijke sleutel de PuTTY-indeling heeft en u de OpenSSH-indeling moet gebruiken, converteert u de sleutel eerst naar de OpenSSH-indeling door de volgende stappen uit te voeren:

### <a name="unix-based-os"></a>Unix-besturingssysteem

1. Als u de PuTTY-hulpprogramma's niet op uw systeem hebt geïnstalleerd, doet u dat nu, bijvoorbeeld:

   `sudo apt-get install -y putty`

1. Voer deze opdracht uit om een bestand te maken dat u kunt gebruiken met de SFTP-SSH-connector:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Bijvoorbeeld:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows OS

1. Als u dit nog niet hebt gedaan, downloadt u het meest recente [puttygeneratorprogramma (puttygen.exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)en start u het hulpprogramma.

1. Selecteer in dit scherm **Laden.**

   ![Selecteer 'Laden'](./media/connectors-sftp-ssh/puttygen-load.png)

1. Blader naar uw persoonlijke sleutelbestand in PuTTY-indeling en selecteer **Openen.**

1. Selecteer in het menu **Conversies** **de optie OpenSSH-sleutel exporteren.**

   ![Selecteer OpenSSH-sleutel exporteren](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Sla het bestand met de persoonlijke sleutel op met `.pem` de bestandsnaamextensie.

## <a name="considerations"></a>Overwegingen

In deze sectie worden overwegingen beschreven die u moet controleren wanneer u de triggers en acties van deze connector gebruikt.

<a name="different-folders-trigger-processing-file-storage"></a>

### <a name="use-different-sftp-folders-for-file-upload-and-processing"></a>Verschillende SFTP-mappen gebruiken voor het uploaden en verwerken van bestanden

Gebruik op uw SFTP-server afzonderlijke mappen voor het opslaan van geüploade bestanden en voor de trigger om deze bestanden te controleren voor verwerking. Anders wordt de trigger niet ge fireed en gedraagt deze zich onvoorspelbaar, bijvoorbeeld door een willekeurig aantal bestanden over te slaan dat door de trigger wordt verwerkt. Deze vereiste betekent echter wel dat u een manier nodig hebt om bestanden tussen die mappen te verplaatsen. 

Als dit triggerprobleem zich voor doet, verwijdert u de bestanden uit de map die door de trigger wordt bewaakt en gebruikt u een andere map om de geüploade bestanden op te slaan.

<a name="create-file"></a>

### <a name="create-file"></a>Bestand maken

Als u een bestand wilt maken op uw SFTP-server, kunt u de actie SFTP-SSH-bestand maken gebruiken.  Wanneer met deze actie het bestand wordt gemaakt, Logic Apps de SFTP-server ook automatisch aanroepen om de metagegevens van het bestand op te halen. Als u het zojuist gemaakte bestand echter verplaatst voordat de Logic Apps-service de aanroep kan doen om de metagegevens op te halen, krijgt u een `404` foutbericht, `'A reference was made to a file or folder which does not exist'` . Als u het lezen van de metagegevens van het bestand wilt overslaan nadat het bestand is gemaakt, volgt u de stappen om de eigenschap Alle metagegevens van het bestand op Nee toe te voegen en [ **in** **te stellen.**](#file-does-not-exist)

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Verbinding maken met SFTP via SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij [Azure Portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Voor lege logische apps voert u in het zoekvak `sftp ssh` in als uw filter. Selecteer in de lijst met triggers de trigger die u wilt.

   -of-

   Voor bestaande logische apps selecteert u nieuwe stap onder de laatste stap waar u een actie **wilt toevoegen.** Voer in het zoekvak `sftp ssh` als uw filter in. Selecteer de actie die u wilt uitvoeren in de lijst met acties.

   Als u een actie tussen stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen stappen. Selecteer het plusteken ( **+** ) dat wordt weergegeven en selecteer vervolgens Een actie **toevoegen.**

1. Geef de benodigde gegevens op voor uw verbinding.

   > [!IMPORTANT]
   >
   > Wanneer u uw persoonlijke SSH-sleutel in de eigenschap Persoonlijke **SSH-sleutel** typt, volgt u deze aanvullende stappen om ervoor te zorgen dat u de volledige en juiste waarde voor deze eigenschap op geeft. Een ongeldige sleutel zorgt ervoor dat de verbinding mislukt.

   Hoewel u elke teksteditor kunt gebruiken, vindt u hier voorbeeldstappen die laten zien hoe u uw sleutel correct kopieert en plakt met Notepad.exe voorbeeld.

   1. Open uw persoonlijke SSH-sleutelbestand in een teksteditor. In deze stappen wordt Kladblok als voorbeeld gebruikt.

   1. Selecteer In het menu **Kladblok** bewerken de **optie Alles selecteren.**

   1. Selecteer **Kopiëren**  >  **bewerken.**

   1. Plak in de SFTP-SSH-trigger of -actie de volledige gekopieerde sleutel in de **eigenschap Persoonlijke SSH-sleutel,** die meerdere regels ondersteunt.  **_Voer de sleutel niet handmatig in of bewerk deze niet._**

1. Nadat u klaar bent met het invoeren van de verbindingsgegevens, selecteert **u Maken.**

1. Geef nu de benodigde gegevens op voor de geselecteerde trigger of actie en ga door met het bouwen van de werkstroom van uw logische app.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Segmentgrootte overschrijven

Als u het standaard adaptieve gedrag wilt overschrijven dat door segmentering wordt gebruikt, kunt u een constante chunkgrootte van 5 MB tot 50 MB opgeven.

1. Selecteer in de rechterbovenhoek van de actie de knop met het beletselmenu (**...**) en selecteer vervolgens **Instellingen.**

   ![SFTP-SSH-instellingen openen](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. Voer **onder Inhoudsoverdracht** in de **eigenschap Chunkgrootte** een geheel getal in van `5` tot , `50` bijvoorbeeld: 

   ![Geef in plaats daarvan de segmentgrootte op die moet worden gebruikt](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Nadat u klaar bent, selecteert **u Klaar.**

## <a name="examples"></a>Voorbeelden

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - SSH-trigger: wanneer een bestand wordt toegevoegd of gewijzigd

Met deze trigger wordt een werkstroom gestart wanneer een bestand wordt toegevoegd of gewijzigd op een SFTP-server. Als voorbeeld van vervolgacties kan de werkstroom een voorwaarde gebruiken om te controleren of de bestandsinhoud voldoet aan opgegeven criteria. Als de inhoud voldoet  aan de voorwaarde, kan de actie Bestandsinhoud downloaden SFTP-SSH de inhoud krijgen. Vervolgens kan een andere SFTP-SSH-actie dat bestand in een andere map op de SFTP-server plaatsen.

**Bedrijfsvoorbeeld:** u kunt deze trigger gebruiken om een SFTP-map te controleren op nieuwe bestanden die klantorders vertegenwoordigen. Vervolgens kunt u een SFTP-SSH-actie gebruiken, zoals Bestandsinhoud downloaden, zodat u de inhoud van de bestelling ophaalt voor verdere verwerking en die bestelling opgeslagen in een orderdatabase. 

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP - SSH-actie: Bestandsinhoud met pad downloaden

Met deze actie wordt de inhoud van een bestand op een SFTP-server opgeslagen door het bestandspad op te geven. U kunt bijvoorbeeld de trigger uit het vorige voorbeeld toevoegen en een voorwaarde toevoegen waar de inhoud van het bestand aan moet voldoen. Als de voorwaarde waar is, kan de actie die de inhoud opneemt, worden uitgevoerd.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-problems"></a>Problemen oplossen

In deze sectie worden mogelijke oplossingen voor veelvoorkomende fouten of problemen beschreven.

<a name="connection-attempt-failed"></a>

### <a name="504-error-a-connection-attempt-failed-because-the-connected-party-did-not-properly-respond-after-a-period-of-time-or-established-connection-failed-because-connected-host-has-failed-to-respond-or-request-to-the-sftp-server-has-taken-more-than-000030-seconds"></a>504-fout: 'Een verbindingspoging is mislukt omdat de verbonden partij niet goed heeft gereageerd na een bepaalde periode, of de verbinding tot stand is gebracht omdat de verbonden host niet heeft gereageerd' of 'Aanvraag bij de SFTP-server heeft meer dan 00:00:30' seconden in beslag genomen'

Deze fout kan zich voor doen wanneer uw logische app geen verbinding kan maken met de SFTP-server. Er kunnen verschillende redenen voor dit probleem zijn. Probeer daarom deze opties voor probleemoplossing:

* De time-out voor de verbinding is 20 seconden. Controleer of uw SFTP-server goede prestaties heeft en tussenliggende apparaten, zoals firewalls, geen overhead toevoegen. 

* Als u een firewall hebt ingesteld, moet u ervoor zorgen dat u de **IP-adressen** van de beheerde connector toevoegt aan de goedgekeurde lijst. Zie Limieten en configuratie voor Azure Logic Apps om de IP-adressen voor de regio van uw logische app [te Azure Logic Apps.](../logic-apps/logic-apps-limits-and-config.md#multi-tenant-azure---outbound-ip-addresses)

* Als deze fout af en toe  wordt weergegeven, wijzigt u de beleidsinstelling Opnieuw proberen voor de actie SFTP-SSH in een aantal nieuwe poging dat hoger is dan de standaard vier nieuwe poging.

* Controleer of de SFTP-server een limiet stelt voor het aantal verbindingen van elk IP-adres. Als er een limiet bestaat, moet u mogelijk het aantal gelijktijdige exemplaren van logische apps beperken.

* Verhoog de eigenschap [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) in de SSH-configuratie voor uw SFTP-server naar ongeveer één uur om de verbindingskosten te verlagen.

* Controleer het SFTP-serverlogboek om te controleren of de aanvraag van de logische app de SFTP-server heeft bereikt. Voor meer informatie over het connectiviteitsprobleem kunt u ook een netwerk trace uitvoeren op uw firewall en uw SFTP-server.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>404-fout: 'Er is een verwijzing gemaakt naar een bestand of map die niet bestaat'

Deze fout kan optreden wanneer uw werkstroom een bestand maakt op uw  SFTP-server met de actie SFTP-SSH-bestand maken, maar dat bestand onmiddellijk verplaatst voordat de Logic Apps-service de metagegevens van het bestand kan krijgen. Wanneer in uw werkstroom de **actie** Bestand maken wordt uitgevoerd, roept de Logic Apps service automatisch uw SFTP-server aan om de metagegevens van het bestand op te halen. Als uw logische app het bestand verplaatst, kan de Logic Apps service het bestand niet meer vinden, zodat u het `404` foutbericht krijgt.

Als u het verplaatsen van het bestand niet kunt vermijden of vertragen, kunt u het lezen van de metagegevens van het bestand overslaan nadat het bestand is gemaakt door de volgende stappen uit te voeren:

1. Open in **de actie** Bestand maken de lijst **Nieuwe parameter** toevoegen, selecteer de eigenschap **Alle** bestandsmetagegevens downloaden en stel de waarde in op **Nee.**

1. Als u deze bestandsmetagegevens later nodig hebt, kunt u de **actie Bestandsmetagegevens downloaden** gebruiken.

## <a name="connector-reference"></a>Connector-verwijzing

Zie de referentiepagina van de connector voor meer technische informatie over deze connector, zoals triggers, acties en limieten zoals beschreven in het Swagger-bestand van de [connector.](/connectors/sftpwithssh/)

> [!NOTE]
> Voor logische apps in een [ISE (Integration Service Environment) is](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)voor de ise-gelabelde versie van deze connector segmentering vereist om in plaats daarvan de ISE-berichtlimieten [te](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) gebruiken.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Logic Apps connectors](../connectors/apis-list.md)
