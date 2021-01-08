---
title: Verbinding maken met de SFTP-server met SSH
description: Taken automatiseren voor het bewaken, maken, beheren, verzenden en ontvangen van bestanden voor een SFTP-server met behulp van SSH en Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm, azla
ms.topic: article
ms.date: 01/07/2021
tags: connectors
ms.openlocfilehash: 388d747da692160ab6d0a89c0c35de348d921486
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016759"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>SFTP-bestanden bewaken, maken en beheren met behulp van SSH en Azure Logic Apps

U kunt met behulp van het SSH-protocol [(Secure Shell)](https://www.ssh.com/ssh/protocol/) werk stromen met behulp van Azure Logic apps en de SFTP-SSH-connector maken en automatiseren om taken te automatiseren die bestanden op een [beveiligde File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/) -server bewaken, maken, verzenden en ontvangen. SFTP is een netwerkprotocol dat bestandstoegang, bestandsoverdracht en bestandsbeheer mogelijk maakt via elke betrouwbare gegevensstroom.

Hier volgen enkele voor beelden van taken die u kunt automatiseren:

* Controleren wanneer bestanden worden toegevoegd of gewijzigd.
* Bestanden downloaden, maken, kopiëren, een andere naam geven, bijwerken, weer geven en verwijderen.
* Mappen maken.
* Bestands inhoud en meta gegevens ophalen.
* Haal archief naar mappen.

U kunt triggers gebruiken die gebeurtenissen op uw SFTP-server controleren en uitvoer beschikbaar maken voor andere acties. U kunt acties gebruiken waarmee verschillende taken op uw SFTP-server worden uitgevoerd. U kunt ook andere acties in uw logische app gebruiken de uitvoer van SFTP-acties. Als u bijvoorbeeld regel matig bestanden van uw SFTP-server ophaalt, kunt u e-mail waarschuwingen over die bestanden en hun inhoud verzenden met behulp van de Office 365 Outlook Connector of de Outlook.com-connector. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

Zie de sectie [SFTP-SSH versus SFTP vergelijken](#comparison) verderop in dit onderwerp voor verschillen tussen de SFTP-SSH-connector en de SFTP-connector.

## <a name="limits"></a>Limieten

* De SFTP-SSH-connector biedt momenteel geen ondersteuning voor deze SFTP-servers:

  * IBM DataPower
  * MessageWay
  * Opentekst Secure MFT
  * GXS voor opentekst

* De SFTP-SSH-connector ondersteunt verificatie van de persoonlijke sleutel of wachtwoord verificatie, niet beide.

* SFTP-SSH-acties die ondersteuning bieden voor [segmentering](../logic-apps/logic-apps-handle-large-messages.md) , kunnen bestanden van Maxi maal 1 GB afhandelen, terwijl SFTP-SSH-acties die geen ondersteuning bieden voor bestands verwerking, bestanden tot 50 MB kunnen verwerken. Hoewel de standaard grootte van het segment 15 MB is, kan deze grootte dynamisch worden gewijzigd, beginnend bij 5 MB en geleidelijk toenemen tot een maximum van 50 MB, op basis van factoren zoals netwerk latentie, Server reactietijd, enzovoort.

  > [!NOTE]
  > Voor Logic apps in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), moet de ISE-label versie van deze connector worden gesegmenteerd om in plaats daarvan de [ISE-bericht limieten](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) te gebruiken.

  U kunt dit adaptieve gedrag onderdrukken wanneer u in plaats daarvan [een constante segment grootte opgeeft](#change-chunk-size) die u wilt gebruiken. Deze grootte kan variëren van 5 MB tot 50 MB. Stel bijvoorbeeld dat u een 45-MB-bestand hebt en een netwerk dat deze bestands grootte zonder latentie kan ondersteunen. De resultaten van een adaptieve Chunking worden in verschillende aanroepen in plaats van de aanroep. Als u het aantal aanroepen wilt beperken, kunt u een grootte van 50 MB segmenten instellen. In een ander scenario kunt u, als er een time-out optreedt voor uw logische app, bijvoorbeeld een segment van 15 MB gebruiken om de grootte te verkleinen tot 5 MB.

  Segment grootte is gekoppeld aan een verbinding, wat betekent dat u dezelfde verbinding kunt gebruiken voor acties die Chunking ondersteunen en vervolgens voor acties die geen ondersteuning bieden voor Chunking. In dit geval wordt de segment grootte voor acties die geen Chunking-bereik ondersteunen van 5 MB tot 50 MB. In deze tabel ziet u welke SFTP-SSH-acties Chunking ondersteunen:

  | Actie | Ondersteuning voor segmentering | Ondersteuning voor segment grootte negeren |
  |--------|------------------|-----------------------------|
  | **Bestand kopiëren** | Nee | Niet van toepassing |
  | **Bestand maken** | Ja | Ja |
  | **Map maken** | Niet van toepassing | Niet van toepassing |
  | **Bestand verwijderen** | Niet van toepassing | Niet van toepassing |
  | **Archief naar map uitpakken** | Niet van toepassing | Niet van toepassing |
  | **Bestands inhoud ophalen** | Ja | Ja |
  | **Bestands inhoud ophalen met behulp van pad** | Ja | Ja |
  | **Meta gegevens van bestand ophalen** | Niet van toepassing | Niet van toepassing |
  | **Meta gegevens van bestand ophalen met behulp van pad** | Niet van toepassing | Niet van toepassing |
  | **Bestanden in de map weer geven** | Niet van toepassing | Niet van toepassing |
  | **Bestands naam wijzigen** | Niet van toepassing | Niet van toepassing |
  | **Bestand bijwerken** | Nee | Niet van toepassing |
  ||||

* SFTP-SSH-Triggers bieden geen ondersteuning voor het segmenteren van berichten. Bij het aanvragen van bestands inhoud selecteren triggers alleen bestanden die 15 MB of kleiner zijn. Als u bestanden groter dan 15 MB wilt ophalen, volgt u dit patroon:

  1. Gebruik een SFTP-SSH-trigger die alleen bestands eigenschappen retourneert, zoals **Wanneer een bestand wordt toegevoegd of gewijzigd (alleen eigenschappen)**.

  1. Volg de trigger met de bewerking voor het **ophalen van bestands inhoud** van SFTP-SSH, die het volledige bestand leest en impliciet gebruikmaakt van het segmenteren van berichten.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>SFTP-SSH versus SFTP vergelijken

Hier volgen andere belang rijke verschillen tussen de SFTP-SSH-connector en de SFTP-connector waarbij de SFTP-SSH-connector over de volgende mogelijkheden beschikt:

* Maakt gebruik van de [SSH.net-bibliotheek](https://github.com/sshnet/SSH.NET), een open-source SSH-bibliotheek (Secure Shell) die .net ondersteunt.

* Biedt de actie **map maken** , waarmee een map op het opgegeven pad op de sftp-server wordt gemaakt.

* Biedt de actie **Bestands naam wijzigen** , waarmee een bestand op de sftp-server wordt hernoemd.

* Hiermee wordt de verbinding met de SFTP-server *Maxi maal 1 uur* in de cache opgeslagen, waardoor de prestaties worden verbeterd en het aantal pogingen om verbinding te maken met de server wordt verminderd. Als u de duur voor dit cache gedrag wilt instellen, bewerkt u de eigenschap [**' ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) in de SSH-configuratie op de sftp-server.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Uw SFTP-server adres en account referenties, waarmee uw logische app toegang kan krijgen tot uw SFTP-account. U hebt ook toegang nodig tot een persoonlijke SSH-sleutel en het wacht woord voor de persoonlijke SSH-sleutel. Als u Chunking wilt gebruiken bij het uploaden van grote bestanden, hebt u lees-en schrijf machtigingen nodig voor de hoofdmap van de SFTP-server. Anders krijgt u de fout melding ' 401 niet toegestaan '.

  > [!IMPORTANT]
  >
  > De SFTP-SSH-connector ondersteunt *alleen* deze indelingen, algoritmen en vinger afdrukken van persoonlijke sleutels:
  >
  > * **Indelingen van persoonlijke sleutels**: RSA-sleutels (Rivest Shamir Adleman) en DSA (Digital Signature Algorithm) in zowel de OpenSSH-als de SSH.com-indeling. Als uw persoonlijke sleutel zich in de PuTTy-bestands indeling (. ppk) bevindt, [moet u eerst de sleutel converteren naar de OpenSSH-bestands indeling (. pem)](#convert-to-openssh).
  >
  > * **Versleutelings algoritmen**: des-EDE3-CBC, des-EDE3-CFB, des-CBC, AES-128-CBC, AES-192-CBC en AES-256-cbc
  >
  > * **Vinger afdruk**: MD5
  >
  > Nadat u de SFTP-SSH-trigger of actie die u wilt toevoegen aan uw logische app hebt toegevoegd, moet u verbindings gegevens voor uw SFTP-server opgeven. Wanneer u uw persoonlijke SSH-sleutel voor deze verbinding opgeeft,*_hoeft u niet hand matig de sleutel_* _ in te voeren of te bewerken. Dit kan ertoe leiden dat de verbinding mislukt. Zorg er in plaats daarvan voor dat u _*_de sleutel kopieert_*_ uit uw persoonlijke SSH-sleutel bestand en _*_plak_*_ die sleutel in de verbindings gegevens. 
  > Zie de sectie [verbinding maken met SFTP met SSH verderop in](#connect) dit artikel voor meer informatie.

Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw SFTP-account. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met een SFTP-SSH-trigger. Als u een SFTP-SSH-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

## <a name="how-sftp-ssh-triggers-work"></a>Hoe werken SFTP-SSH-triggers

<a name="polling-behavior"></a>

### <a name="polling-behavior"></a>Polling-gedrag

SFTP-SSH-triggers pollen het SFTP-bestands systeem en zoeken naar alle bestanden die zijn gewijzigd sinds de laatste poll. Met sommige hulpprogram ma's kunt u de tijds tempel behouden wanneer de bestanden worden gewijzigd. In deze gevallen moet u deze functie uitschakelen zodat de trigger kan werken. Hier volgen enkele algemene instellingen:

| SFTP-client | Actie |
|-------------|--------|
| WinSCP | Ga naar **Opties**  >  **voor keuren**  >  **overdracht**  >  **bewerken**  >  **behouden tijds tempel**  >  **uitschakelen** |
| FileZilla | Ga naar de **overdrachts**  >  **tijds tempels van overgebrachte bestanden**  >  **uitschakelen** |
|||

Wanneer een trigger een nieuw bestand vindt, controleert de trigger of het nieuwe bestand is voltooid en niet gedeeltelijk is geschreven. Een bestand kan bijvoorbeeld wijzigingen in voortgang hebben wanneer de trigger de bestands server controleert. Om te voor komen dat een gedeeltelijk geschreven bestand wordt geretourneerd, wordt door de trigger de tijds tempel voor het bestand met recente wijzigingen weer gegeven, maar wordt dat bestand niet direct geretourneerd. De trigger retourneert het bestand alleen wanneer de server opnieuw wordt gecontroleerd. Dit gedrag kan soms een vertraging veroorzaken die twee maal het polling interval van de trigger is.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Verschuiving van terugkeer patroon en drift activeren

Op verbindingen gebaseerde triggers waarbij u eerst een verbinding moet maken, zoals de SFTP-SSH-trigger, verschillen van ingebouwde triggers die in Azure Logic Apps systeem eigen worden uitgevoerd, zoals de [terugkeer patroon trigger](../connectors/connectors-native-recurrence.md). In terugkerende activeringen op basis van een verbinding is de terugkeer planning niet het enige stuur programma dat de uitvoering beheert, en de tijd zone bepaalt alleen de eerste begin tijd. De volgende uitvoeringen zijn afhankelijk van het terugkeer schema, de laatste uitvoering van triggers *en* andere factoren die kunnen leiden tot een onverwacht gedrag of het produceren van onverwachte werking, zoals het niet behouden van het opgegeven schema wanneer zomer tijd (DST) begint en eindigt. Om ervoor te zorgen dat de tijd van het terugkeer patroon niet wordt verschoven wanneer het ZOMERtijd duurt, moet u het terugkeer patroon hand matig aanpassen zodat uw logische app op het verwachte tijdstip blijft worden uitgevoerd. Anders wordt de begin tijd één uur vooruit geschoven wanneer zomer tijd wordt gestart en één uur terug wanneer de DST eindigt. Zie [terugkeer patroon voor triggers op basis](../connectors/apis-list.md#recurrence-connection-based)van een verbinding voor meer informatie.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Op PuTTy gebaseerde sleutel converteren naar OpenSSH

Als uw persoonlijke sleutel zich in de PuTTy-indeling bevindt, waarbij de bestandsnaam extensie. ppk (PuTTy persoonlijke sleutel) wordt gebruikt, moet u eerst de sleutel converteren naar de OpenSSH-indeling. Dit maakt gebruik van de bestandsnaam extensie. pem (Privacy Enhanced Mail).

### <a name="unix-based-os"></a>Op UNIX gebaseerd besturings systeem

1. Als u de PuTTy-hulpprogram ma's niet op uw systeem hebt geïnstalleerd, doet u dat nu bijvoorbeeld:

   `sudo apt-get install -y putty`

1. Voer deze opdracht uit om een bestand te maken dat u met de SFTP-SSH-connector kunt gebruiken:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Bijvoorbeeld:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows OS

1. Als u dit nog niet hebt gedaan, [downloadt u het hulp programma voor de nieuwste putty-Generator (puttygen.exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)en start u het hulp programma.

1. Selecteer **laden** op dit scherm.

   ![Selecteer laden](./media/connectors-sftp-ssh/puttygen-load.png)

1. Blader naar uw persoonlijke sleutel bestand in de PuTTy-indeling en selecteer **openen**.

1. Selecteer in het menu **conversies** de optie **OpenSSH key**.

   ![Selecteer ' OpenSSH-sleutel exporteren '](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Sla het bestand met de persoonlijke sleutel op met de `.pem` bestandsnaam extensie.

## <a name="considerations"></a>Overwegingen

In deze sectie worden overwegingen beschreven voor het controleren van de triggers en acties van deze connector.

<a name="create-file"></a>

### <a name="create-file"></a>Bestand maken

Als u een bestand op uw SFTP-server wilt maken, kunt u de actie SFTP-SSH- **bestand maken** gebruiken. Wanneer deze actie het bestand maakt, roept de Logic Apps-service ook automatisch uw SFTP-server aan om de meta gegevens van het bestand op te halen. Als u het zojuist gemaakte bestand echter verplaatst voordat de Logic Apps-service de aanroep de meta gegevens kan ophalen, wordt er een `404` fout bericht weer gegeven `'A reference was made to a file or folder which does not exist'` . Als u het lezen van de meta gegevens van het bestand na het maken van het bestand wilt overs Laan, volgt u de stappen voor het [toevoegen en instellen van de eigenschap **alle meta gegevens ophalen** op **Nee**](#file-does-not-exist).

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Verbinding maken met SFTP met SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Voer als filter toe voor lege logische apps in het zoekvak `sftp ssh` . Selecteer de gewenste trigger onder de lijst met triggers.

   -of-

   Voor bestaande Logic apps, onder de laatste stap waar u een actie wilt toevoegen, selecteert u **nieuwe stap**. Voer in het zoekvak `sftp ssh` als uw filter in. Selecteer in de lijst acties de gewenste actie.

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Geef de benodigde gegevens voor de verbinding op.

   > [!IMPORTANT]
   >
   > Wanneer u uw persoonlijke SSH-sleutel in de **persoonlijke SSH-sleutel** eigenschap invoert, voert u de volgende aanvullende stappen uit om ervoor te zorgen dat u de volledige en juiste waarde voor deze eigenschap opgeeft. Een ongeldige sleutel zorgt ervoor dat de verbinding mislukt.

   Hoewel u een tekst editor kunt gebruiken, zijn hier voorbeeld stappen die laten zien hoe u uw sleutel correct kopieert en plakt met behulp van Notepad.exe als voor beeld.

   1. Open uw persoonlijke SSH-sleutel bestand in een tekst editor. In deze stappen wordt Klad blok als voor beeld gebruikt.

   1. Selecteer in het menu **bewerken** in Klad blok de optie **Alles selecteren**.

   1. Selecteer   >  **kopie** bewerken.

   1. Plak de *volledige* sleutel die u hebt gekopieerd in de eigenschap van de **persoonlijke SSH-sleutel** , die ondersteuning biedt voor meerdere regels in de SFTP-SSH-trigger of de actie die u hebt toegevoegd.  **_Zorg ervoor dat u_* de sleutel plakt. _*_Voer de sleutel niet hand matig in of bewerk deze_*_.

1. Wanneer u klaar bent met het invoeren van de verbindings gegevens, selecteert u _ * maken * *.

1. Geef nu de gegevens op die nodig zijn voor de geselecteerde trigger of actie en blijf de werk stroom van uw logische app bouwen.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Segment grootte overschrijven

Als u het standaard adaptieve gedrag dat door de segmentering wordt gebruikt, wilt overschrijven, kunt u een constante segment grootte van 5 MB tot 50 MB opgeven.

1. Selecteer de knop met weglatings tekens (**...**) in de rechter bovenhoek van de actie en selecteer vervolgens **instellingen**.

   ![SFTP-SSH-instellingen openen](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. Voer onder **inhouds overdracht**, in de eigenschap **segment grootte** , een geheel getal in van `5` tot `50` , bijvoorbeeld: 

   ![Geef de segment grootte op die u in plaats daarvan wilt gebruiken](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Nadat u klaar bent, selecteert u **gereed**.

## <a name="examples"></a>Voorbeelden

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP-SSH-trigger: wanneer een bestand wordt toegevoegd of gewijzigd

Deze trigger start een werk stroom voor logische apps wanneer een bestand wordt toegevoegd aan of gewijzigd op een SFTP-server. U kunt bijvoorbeeld een voor waarde toevoegen waarmee de inhoud van het bestand wordt gecontroleerd en de inhoud wordt opgehaald op basis van het feit of de inhoud voldoet aan een opgegeven voor waarde. U kunt vervolgens een actie toevoegen waarmee de inhoud van het bestand wordt opgehaald en die inhoud in een map op de SFTP-server plaatsen.

**Enter prise-voor beeld**: u kunt deze trigger gebruiken om een SFTP-map te bewaken voor nieuwe bestanden die klant orders vertegenwoordigen. U kunt vervolgens een SFTP-actie gebruiken, zoals het **ophalen van bestands inhoud** , zodat u de inhoud van de bestelling krijgt voor verdere verwerking en die order in een Data Base orders opslaat.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP-SSH-actie: bestands inhoud ophalen met behulp van pad

Met deze actie wordt de inhoud van een bestand op een SFTP-server opgehaald door het bestandspad op te geven. U kunt bijvoorbeeld de trigger uit het vorige voor beeld toevoegen en een voor waarde waaraan de inhoud van het bestand moet worden voldaan. Als de voor waarde waar is, kan de actie die de inhoud ophaalt worden uitgevoerd.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-problems"></a>Problemen oplossen

In deze sectie worden mogelijke oplossingen beschreven voor veelvoorkomende fouten of problemen.

<a name="connection-attempt-failed"></a>

### <a name="504-error-a-connection-attempt-failed-because-the-connected-party-did-not-properly-respond-after-a-period-of-time-or-established-connection-failed-because-connected-host-has-failed-to-respond-or-request-to-the-sftp-server-has-taken-more-than-000030-seconds"></a>504 fout: ' een verbindings poging is mislukt omdat de verbonden partij niet op de juiste manier heeft gereageerd of een verbinding tot stand is gebracht, omdat de verbinding met de SFTP-server meer dan 00:00:30 seconden heeft plaatsgevonden

Deze fout kan optreden wanneer de logische app geen verbinding kan maken met de SFTP-server. Er kunnen verschillende redenen zijn om dit probleem te verhelpen, dus probeer deze opties voor probleem oplossing:

* De verbindingstime-out is 20 seconden. Controleer of de SFTP-server goede prestaties en tussenliggende apparaten heeft, zoals firewalls, maar geen overhead toevoegen. 

* Als u een firewall hebt ingesteld, moet u ervoor zorgen dat u de **IP-** adressen van de beheerde connector aan de goedgekeurde lijst toevoegt. Zie [limieten en configuratie voor Azure Logic apps](../logic-apps/logic-apps-limits-and-config.md#multi-tenant-azure---outbound-ip-addresses)als u de IP-adressen voor de regio van de logische app wilt vinden.

* Als deze fout regel matig optreedt, wijzigt u de beleids instelling voor **opnieuw proberen** voor de SFTP-SSH-actie in een aantal pogingen hoger dan de standaard vier nieuwe pogingen.

* Controleer of de SFTP-server een limiet voor het aantal verbindingen van elk IP-adres heeft. Als er een limiet bestaat, moet u mogelijk het aantal gelijktijdige logische app-exemplaren beperken.

* Verhoog de eigenschap [**' ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) in de SSH-configuratie voor uw SFTP-server om de kosten voor de verbinding te verlagen.

* Bekijk het SFTP-server logboek om te controleren of de SFTP-server is bereikt met de aanvraag vanuit de logische app. Als u meer informatie wilt over het connectiviteits probleem, kunt u ook een netwerk tracering uitvoeren op uw firewall en op uw SFTP-server.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>404 fout: ' er is een verwijzing gemaakt naar een bestand of map die niet bestaat '

Deze fout kan zich voordoen wanneer uw logische app een nieuw bestand op uw SFTP-server maakt met behulp van de bewerking SFTP-SSH- **bestand maken** , maar het zojuist gemaakte bestand direct verplaatst voordat de Logic apps-service de meta gegevens van het bestand kan ophalen. Wanneer uw logische app de actie **bestand maken** uitvoert, roept de Logic apps-service ook automatisch uw SFTP-server aan om de meta gegevens van het bestand op te halen. Als uw logische app echter het bestand verplaatst, kan de Logic Apps-service het bestand niet meer vinden zodat het `404` fout bericht wordt weer gegeven.

Als u het verplaatsen van het bestand niet kunt voor komen of vertragen, kunt u de meta gegevens van het bestand na het maken van het bestand overs Laan door de volgende stappen uit te voeren:

1. Open in de actie **bestand maken** de lijst **nieuwe para meter toevoegen** , selecteer de eigenschap **alle meta gegevens van het bestand ophalen** en stel de waarde in op **Nee**.

1. Als u later de meta gegevens van dit bestand nodig hebt, kunt u de actie **bestand meta gegevens ophalen** gebruiken.

## <a name="connector-reference"></a>Connector-verwijzing

Voor meer technische informatie over deze connector, zoals triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector, raadpleegt u de [referentie pagina van de connector](/connectors/sftpwithssh/).

> [!NOTE]
> Voor Logic apps in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), moet de ISE-label versie van deze connector worden gesegmenteerd om de [ISE-bericht limieten](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) te gebruiken.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
