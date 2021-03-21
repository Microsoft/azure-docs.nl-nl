---
title: Verbinding maken met het SFTP-account (afgeschaft)
description: Taken en processen automatiseren voor het bewaken, maken, beheren, verzenden en ontvangen van bestanden voor een SFTP-server met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 11/01/2019
tags: connectors
ROBOTS: NOINDEX
ms.openlocfilehash: 70fb956af7ff45c7b54f04d7ed441ec39f9d80a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92673817"
---
# <a name="monitor-create-and-manage-sftp-files-in-azure-logic-apps"></a>SFTP-bestanden in Azure Logic Apps bewaken, maken en beheren

> [!IMPORTANT]
> Gebruik de [SFTP-SSH-connector](../connectors/connectors-sftp-ssh.md) als de SFTP-connector is afgeschaft. U kunt geen SFTP-triggers en acties meer selecteren in de ontwerp functie voor logische apps.

Voor het automatiseren van taken die bestanden op een [beveiligde File Transfer Protocol-server (SFTP)](https://www.ssh.com/ssh/sftp/) bewaken, maken, verzenden en ontvangen, kunt u integratie werk stromen bouwen en automatiseren met behulp van Azure Logic apps en de SFTP-connector. SFTP is een netwerkprotocol dat bestandstoegang, bestandsoverdracht en bestandsbeheer mogelijk maakt via elke betrouwbare gegevensstroom. Hier volgen enkele voor beelden van taken die u kunt automatiseren:

* Controleren wanneer bestanden worden toegevoegd of gewijzigd.
* Bestanden downloaden, maken, kopiëren, bijwerken, weer geven en verwijderen.
* Bestands inhoud en meta gegevens ophalen.
* Haal archief naar mappen.

U kunt triggers gebruiken die gebeurtenissen op uw SFTP-server controleren en uitvoer beschikbaar maken voor andere acties. U kunt acties gebruiken waarmee verschillende taken op uw SFTP-server worden uitgevoerd. U kunt ook andere acties in uw logische app gebruiken de uitvoer van SFTP-acties. Als u bijvoorbeeld regel matig bestanden van uw SFTP-server ophaalt, kunt u e-mail waarschuwingen over die bestanden en hun inhoud verzenden met behulp van de Office 365 Outlook Connector of de Outlook.com-connector. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limieten

De SFTP-connector verwerkt alleen bestanden van *50 MB of kleiner* en biedt geen ondersteuning voor het delen van [berichten](../logic-apps/logic-apps-handle-large-messages.md). Gebruik de [SFTP-SSH-connector](../connectors/connectors-sftp-ssh.md)voor grotere bestanden. Voor verschillen tussen de SFTP-connector en de SFTP-SSH-connector, raadpleegt u [SFTP-SSH vergeleken met SFTP](../connectors/connectors-sftp-ssh.md#comparison) in het SFTP-SSH-artikel.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Uw SFTP-server adres en account referenties, waarmee uw logische app toegang kan krijgen tot uw SFTP-account. Als u het [SSH-protocol (Secure Shell)](https://www.ssh.com/ssh/protocol/) wilt gebruiken, hebt u ook toegang nodig tot een persoonlijke SSH-sleutel en het wacht woord voor de persoonlijke SSH-sleutel.

  > [!NOTE]
  >
  > De SFTP-connector ondersteunt de volgende indelingen van persoonlijke sleutels: OpenSSH, ssh.com en PuTTy
  >
  > Wanneer u de logische app maakt, moet u, nadat u de gewenste SFTP-trigger of actie hebt toegevoegd, de verbindings gegevens voor uw SFTP-server opgeven. 
  > Als u een persoonlijke SSH-sleutel gebruikt, moet u ervoor zorgen dat u de sleutel uit uw persoonlijke SSH-sleutel bestand ***kopieert*** en die sleutel in de verbindings gegevens ***plakt*** . ***Voer de sleutel niet hand matig in of bewerk*** deze, waardoor de verbinding kan mislukken. 
  > Zie de volgende stappen in dit artikel voor meer informatie.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw SFTP-account. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met een SFTP-trigger. Als u een SFTP-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

## <a name="how-sftp-triggers-work"></a>Hoe SFTP-triggers werken

SFTP-triggers werken door het SFTP-bestands systeem te pollen en te zoeken naar een bestand dat sinds de laatste poll is gewijzigd. Met sommige hulpprogram ma's kunt u de tijds tempel behouden wanneer de bestanden worden gewijzigd. In deze gevallen moet u deze functie uitschakelen zodat de trigger kan werken. Hier volgen enkele algemene instellingen:

| SFTP-client | Bewerking |
|-------------|--------|
| WinSCP | Ga naar **Opties**  >  **voor keuren**  >  **overdracht**  >  **bewerken**  >  **behouden tijds tempel**  >  **uitschakelen** |
| FileZilla | Ga naar de **overdrachts**  >  **tijds tempels van overgebrachte bestanden**  >  **uitschakelen** |
|||

Wanneer een trigger een nieuw bestand vindt, controleert de trigger of het nieuwe bestand is voltooid en niet gedeeltelijk is geschreven. Een bestand kan bijvoorbeeld wijzigingen in voortgang hebben wanneer de trigger de bestands server controleert. Om te voor komen dat een gedeeltelijk geschreven bestand wordt geretourneerd, wordt door de trigger de tijds tempel voor het bestand met recente wijzigingen weer gegeven, maar wordt dat bestand niet direct geretourneerd. De trigger retourneert het bestand alleen wanneer de server opnieuw wordt gecontroleerd. Dit gedrag kan soms een vertraging veroorzaken die twee maal het polling interval van de trigger is.

## <a name="connect-to-sftp"></a>Verbinding maken met SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Voor lege Logic apps voert u in het zoekvak ' sftp ' in als uw filter. Selecteer de gewenste trigger onder de lijst met triggers.

   -of-

   Voor bestaande Logic apps, onder de laatste stap waar u een actie wilt toevoegen, kiest u **nieuwe stap**. Voer in het zoekvak ' sftp ' in als uw filter. Selecteer in de lijst acties de gewenste actie.

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. Kies het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Geef de benodigde gegevens voor de verbinding op.

   > [!IMPORTANT]
   >
   > Wanneer u uw persoonlijke SSH-sleutel in de **persoonlijke SSH-sleutel** eigenschap invoert, voert u de volgende aanvullende stappen uit om ervoor te zorgen dat u de volledige en juiste waarde voor deze eigenschap opgeeft. 
   > Een ongeldige sleutel zorgt ervoor dat de verbinding mislukt.

   Hoewel u een tekst editor kunt gebruiken, zijn hier voorbeeld stappen die laten zien hoe u uw sleutel correct kopieert en plakt met behulp van Notepad.exe als voor beeld.

   1. Open uw persoonlijke SSH-sleutel bestand in een tekst editor. In deze stappen wordt Klad blok als voor beeld gebruikt.

   1. Selecteer in het menu **bewerken** in Klad blok de optie **Alles selecteren**.

   1. Selecteer   >  **kopie** bewerken.

   1. Plak de *volledige* sleutel die u hebt gekopieerd in de eigenschap van de **persoonlijke SSH-sleutel** , die ondersteuning biedt voor meerdere regels in de SFTP-trigger of-actie die u hebt toegevoegd. **_Zorg ervoor dat u_*de sleutel plakt. _*_Voer de sleutel niet hand matig in of bewerk deze_**.

1. Wanneer u klaar bent met het invoeren van de verbindings gegevens, kiest u **maken**.

1. Geef de benodigde gegevens op voor de geselecteerde trigger of actie en ga door met het bouwen van de werk stroom van uw logische app.

## <a name="examples"></a>Voorbeelden

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP-trigger: wanneer een bestand wordt toegevoegd of gewijzigd

Deze trigger start een werk stroom voor logische apps wanneer een bestand wordt toegevoegd aan of gewijzigd op een SFTP-server. U kunt bijvoorbeeld een voor waarde toevoegen waarmee de inhoud van het bestand wordt gecontroleerd en de inhoud wordt opgehaald op basis van het feit of de inhoud voldoet aan een opgegeven voor waarde. U kunt vervolgens een actie toevoegen waarmee de inhoud van het bestand wordt opgehaald en die inhoud in een map op de SFTP-server plaatsen.

**Enter prise-voor beeld**: u kunt deze trigger gebruiken om een SFTP-map te bewaken voor nieuwe bestanden die klant orders vertegenwoordigen. U kunt vervolgens een SFTP-actie gebruiken, zoals het **ophalen van bestands inhoud** , zodat u de inhoud van de bestelling krijgt voor verdere verwerking en die order in een Data Base orders opslaat.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>SFTP-actie: inhoud ophalen

Met deze actie wordt de inhoud van een bestand op een SFTP-server opgehaald. U kunt bijvoorbeeld de trigger uit het vorige voor beeld toevoegen en een voor waarde waaraan de inhoud van het bestand moet worden voldaan. Als de voor waarde waar is, kan de actie die de inhoud ophaalt worden uitgevoerd.

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](../data-factory/connector-sftp.md)van de connector voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)