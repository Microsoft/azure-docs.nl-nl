---
title: Verbinding maken met bestands systemen op locatie
description: Taken en werk stromen automatiseren die verbinding maken met on-premises bestands systemen met de bestandssysteem connector via de on-premises gegevens gateway in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 4715d7173dd959d12350229e457717c908a83756
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91873231"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Verbinding maken met on-premises bestandssystemen met Azure Logic Apps

Met Azure Logic Apps en de File System-connector kunt u geautomatiseerde taken en werk stromen maken waarmee u bestanden op een on-premises bestands share maakt en beheert, bijvoorbeeld:

- Bestanden maken, ophalen, toevoegen, bijwerken en verwijderen.
- Bestanden in mappen of hoofd mappen weer geven.
- Bestands inhoud en meta gegevens ophalen.

  > [!IMPORTANT]
  > De bestandssysteem connector ondersteunt momenteel alleen Windows-bestands systemen op Windows-besturings systemen.  

In dit artikel wordt uitgelegd hoe u verbinding kunt maken met een on-premises bestands systeem, zoals wordt beschreven in dit voorbeeld scenario: Kopieer een bestand dat is geüpload naar Dropbox naar een bestands share en verzend vervolgens een e-mail bericht. Logic apps gebruiken de [on-premises gegevens gateway](../logic-apps/logic-apps-gateway-connection.md)om veilig verbinding te maken en toegang te krijgen tot on-premises systemen. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md). Zie Naslag informatie over de [File System-connector](/connectors/filesystem/)voor connector-specifieke technische gegevens.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Voordat u logische apps kunt verbinden met on-premises systemen zoals uw bestandssysteem server, moet u [een on-premises gegevens gateway installeren en instellen](../logic-apps/logic-apps-gateway-install.md). Op die manier kunt u opgeven dat u de gateway-installatie wilt gebruiken wanneer u de bestandssysteem verbinding maakt vanuit uw logische app.

* Een [Dropbox-account](https://www.dropbox.com/), dat u gratis kunt registreren. Uw account referenties zijn nodig voor het maken van een verbinding tussen uw logische app en uw Dropbox-account.

* Toegang tot de computer met het bestands systeem dat u wilt gebruiken. Als u bijvoorbeeld de gegevens gateway op dezelfde computer installeert als uw bestands systeem, hebt u de account referenties voor die computer nodig.

* Een e-mail account van een provider die wordt ondersteund door Logic Apps, zoals Office 365 Outlook, Outlook.com of Gmail. Voor andere providers [kunt u hier de lijst met connectors bekijken](/connectors/). In deze logische app wordt gebruikgemaakt van een werk- of schoolaccount. Als u een ander e-mailaccount gebruikt, zijn de algemene stappen hetzelfde, maar ziet de gebruikersinterface er misschien iets anders uit.

  > [!IMPORTANT]
  > Als u de Gmail-connector wilt gebruiken, kunnen alleen bedrijfsaccounts van G Suite deze connector zonder beperking in logische apps gebruiken. Als u een Gmail-consumentenaccount hebt, kunt u deze connector alleen gebruiken met specifieke door Google goedgekeurde services, of u kunt [een Google-client-app maken voor verificatie bij uw Gmail-connector](/connectors/gmail/#authentication-and-bring-your-own-application). Zie [Beleid voor gegevensbeveiliging en privacybeleid voor Google-connectors in Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md) voor meer informatie.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Voor dit voor beeld hebt u een lege logische app nodig.

## <a name="add-trigger"></a>Trigger toevoegen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Voer in het zoekvak ' Dropbox ' in als uw filter. Selecteer in de lijst triggers deze trigger: **Wanneer een bestand wordt gemaakt**

   ![Dropbox-trigger selecteren](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Meld u aan met de referenties van uw Dropbox-account en machtig toegang tot uw Dropbox-gegevens voor Azure Logic Apps.

1. Geef de vereiste informatie op voor de trigger.

   ![Dropbox-trigger](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Acties toevoegen

1. Kies **volgende stap** onder de trigger. Voer in het zoekvak ' bestands systeem ' in als uw filter. Selecteer in de lijst acties deze actie: **bestand maken**

   ![Bestandssysteem connector zoeken](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Als u nog geen verbinding hebt met uw bestands systeem, wordt u gevraagd om een verbinding te maken.

   ![Verbinding maken](media/logic-apps-using-file-connector/file-system-connection.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   | -------- | -------- | ----- | ----------- |
   | **Verbindingsnaam** | Ja | <*verbindings naam*> | De naam die u voor de verbinding wilt hebben |
   | **Hoofdmap** | Ja | <*root-mapnaam*> | De hoofdmap voor uw bestands systeem, bijvoorbeeld als u de on-premises gegevens gateway hebt geïnstalleerd, zoals een lokale map op de computer waarop de on-premises gegevens gateway is geïnstalleerd, of de map voor een netwerk share waartoe de computer toegang heeft. <p>Bijvoorbeeld: `\\PublicShare\\DropboxFiles` <p>De hoofdmap is de bovenliggende map, die wordt gebruikt voor relatieve paden voor alle bestand-gerelateerde acties. |
   | **Verificatie type** | Nee | <*verificatie-type*> | Het type verificatie dat door het bestands systeem wordt gebruikt: **Windows** |
   | **Gebruikersnaam** | Ja | < > \\ domein < *gebruikers naam*> <p>-of- <p><*lokale* > \\ computer < *gebruikers naam*> | De gebruikers naam voor de computer waarop u de map van het bestands systeem hebt. <p>Als uw bestandssysteem map zich op dezelfde computer bevindt als de on-premises gegevens gateway, kunt u <gebruikers naam van de *lokale computer* gebruiken > \\ < >. |
   | **Wachtwoord** | Ja | <*uw-wacht woord*> | Het wacht woord voor de computer waarop u het bestands systeem hebt |
   | **#b0** | Ja | <*geïnstalleerd-gateway naam*> | De naam voor de eerder geïnstalleerde gateway |
   |||||

1. Wanneer u klaar bent, kiest u **Maken**.

   Logic Apps configureert en test uw verbinding, zodat u zeker weet dat de verbinding goed werkt. Als de verbinding correct is ingesteld, worden er opties weer gegeven voor de actie die u eerder hebt geselecteerd.

1. Geef in de actie **bestand maken** de details op voor het kopiëren van bestanden van Dropbox naar de hoofdmap in uw on-premises bestands share. Als u uitvoer van de vorige stappen wilt toevoegen, klikt u in de vakken en selecteert u uit de beschik bare velden wanneer de lijst met dynamische inhoud wordt weer gegeven.

   ![Bestands actie maken](media/logic-apps-using-file-connector/create-file-filled.png)

1. Voeg nu een Outlook-actie toe waarmee een e-mail bericht wordt verzonden, zodat de juiste gebruikers over het nieuwe bestand weten. Voer de ontvangers, de titel en de hoofd tekst van het e-mail bericht in. Voor het testen kunt u uw eigen e-mail adres gebruiken.

   ![Actie E-mail verzenden](media/logic-apps-using-file-connector/send-email.png)

1. Sla uw logische app op. Test uw app door een bestand te uploaden naar Dropbox.

   Uw logische app moet het bestand naar uw on-premises bestands share kopiëren en de ontvangers een e-mail sturen over het gekopieerde bestand.

## <a name="connector-reference"></a>Connector-verwijzing

Voor meer technische informatie over deze connector, zoals triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector, raadpleegt u de [referentie pagina van de connector](/connectors/fileconnector/).

> [!NOTE]
> Voor Logic apps in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), maakt de ISE-versie van deze connector gebruik van de [ISE-bericht limieten](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) in plaats daarvan.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van verbinding met on-premises gegevens](../logic-apps/logic-apps-gateway-connection.md) 
* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
