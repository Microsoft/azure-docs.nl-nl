---
title: RosettaNet berichten voor B2B-integratie
description: RosettaNet-berichten uitwisselen in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: f02cbdc7ca8822c5fcc91b106856d7f8f547536b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91565102"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Exchange RosettaNet-berichten voor B2B Enter prise integration in Azure Logic Apps

[RosettaNet](https://resources.gs1us.org) is een non-profit consortium dat standaard processen heeft opgezet voor het delen van Bedrijfs gegevens. Deze standaarden worden vaak gebruikt voor toeleverings processen en zijn wijd verbreid in de halfgeleider-, elektronica-en logistieke branches. Het RosettaNet consortium maakt en onderhoudt partner interface processen (PIPs), die algemene bedrijfsproces definities bieden voor alle berichten uitwisselingen van RosettaNet. RosettaNet is gebaseerd op XML en definieert bericht richtlijnen, interfaces voor bedrijfs processen en implementatie raamwerken voor communicatie tussen bedrijven.

In [Azure Logic apps](../logic-apps/logic-apps-overview.md)kunt u met de RosettaNet-connector integratie oplossingen maken die ondersteuning bieden voor RosettaNet-standaarden. De connector is gebaseerd op RosettaNet implementation Framework (RNIF) versie 2.0.01. RNIF is een open netwerk toepassings raamwerk waarmee zaken partners gezamenlijk RosettaNet PIPs kunnen uitvoeren. Dit framework definieert de bericht structuur, de code ring van bevestigingen, Multipurpose Internet Mail Extensions (MIME) en de digitale hand tekening.

De connector biedt met name de volgende mogelijkheden:

* Versleutelen of ontvangen van RosettaNet-berichten.
* RosettaNet-berichten decoderen of verzenden.
* Wacht op de reactie en het genereren van een melding van de fout.

Voor deze mogelijkheden ondersteunt de connector alle PIPs die zijn gedefinieerd door RNIF 2.0.01. Communicatie met de partner kan synchroon of asynchroon zijn.

## <a name="rosettanet-concepts"></a>RosettaNet-concepten

Hier volgen enkele concepten en termen die uniek zijn voor de RosettaNet-specificatie en zijn belang rijk bij het bouwen van op RosettaNet gebaseerde integraties:

* **GOOIEN**

  De RosettaNet-organisatie maakt en onderhoudt partner interface processen (PIPs), die algemene bedrijfs proces definities bieden voor alle RosettaNet-bericht uitwisselingen. Elke PIP-specificatie bevat een Document Type Definition DTD-bestand en een bericht richtlijn document. Het DTD-bestand definieert de bericht structuur voor de service-inhoud. Het bericht richtlijn document, een HTML-bestand met lees bare tekst, geeft beperkingen op element niveau. Samen bieden deze bestanden een volledige definitie van het bedrijfs proces.

   PIPs worden gecategoriseerd door een bedrijfs functie op hoog niveau, of cluster, en een subfunctie of segment. "3A4" is bijvoorbeeld de PIP voor aankoop order, terwijl "3" de functie voor order beheer is en "3A" de subfunctie order entry &. Zie de [RosettaNet-site](https://resources.gs1us.org)voor meer informatie.

* **Actie**

  Een deel van een PIP zijn actie berichten die worden uitgewisseld tussen partners.

* **Melden**

   Als onderdeel van een PIP zijn signaal berichten bevestigingen die worden verzonden in antwoord op actie berichten.

* **Enkele actie en dubbele actie**

  Voor een PIP-trans actie is de enige reactie een bevestigings signaal bericht. Bij een dubbel actie-PIP ontvangt de initiator een antwoord bericht en beantwoordt het een bevestiging naast de bericht stroom met één actie.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, [meldt u zich aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) voor het opslaan van uw overeenkomst en andere B2B-artefacten. Dit integratie account moet worden gekoppeld aan uw Azure-abonnement.

* Ten minste twee [partners](../logic-apps/logic-apps-enterprise-integration-partners.md) die zijn gedefinieerd in uw integratie account en zijn geconfigureerd met de kwalificatie ' Duns ' onder **Business Identities**

* Een PIP-proces configuratie, die is vereist voor het verzenden of ontvangen van RosettaNet-berichten, in uw integratie account. De proces configuratie slaat alle eigenschappen van de PIP-configuratie op. U kunt vervolgens naar deze configuratie verwijzen wanneer u een overeenkomst maakt met de partner. Zie [PIP-proces configuratie toevoegen](#add-pip)voor het maken van een PIP-proces configuratie in uw integratie account.

* Optionele [certificaten](../logic-apps/logic-apps-enterprise-integration-certificates.md) voor het versleutelen, ontsleutelen of ondertekenen van de berichten die u uploadt naar het integratie account. Certificaten zijn alleen vereist als u gebruikmaakt van ondertekening of versleuteling.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>PIP-proces configuratie toevoegen

Voer de volgende stappen uit om een PIP-proces configuratie toe te voegen aan uw integratie account:

1. Zoek en open uw integratie account in de [Azure Portal](https://portal.azure.com).

1. Selecteer in het deel venster **overzicht** de tegel **RosettaNet PIP** .

   ![Tegel RosettaNet kiezen](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. Klik onder **ROSETTANET PIP** op **toevoegen**. Geef uw PIP-Details op.

   ![Details van RosettaNet-PIP toevoegen](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Naam** | Ja | Uw PIP-naam |
   | **PIP-code** | Ja | De PIP-code van drie cijfers. Zie [RosettaNet PIPs](/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips)voor meer informatie. |
   | **PIP-versie** | Ja | Het PIP-versie nummer, dat beschikbaar is op basis van de geselecteerde PIP-code |
   ||||

   Ga naar de [website van RosettaNet](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)voor meer informatie over deze PIP-eigenschappen.

1. Wanneer u klaar bent, kiest u **OK** om de PIP-configuratie te maken.

1. Als u de proces configuratie wilt weer geven of bewerken, selecteert u het PIP en kiest u **bewerken als JSON**.

   Alle configuratie-instellingen van het proces zijn afkomstig uit de specificaties van de PIP. Logic Apps vult de meeste instellingen met de standaard waarden die de meestgebruikte waarden voor deze eigenschappen zijn.

   ![RosettaNet PIP-configuratie bewerken](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Controleer of de instellingen overeenkomen met de waarden in de juiste PIP-specificatie en voldoen aan de behoeften van uw bedrijf. Als dat nodig is, werkt u de waarden in JSON bij en slaat u deze wijzigingen op.

## <a name="create-rosettanet-agreement"></a>RosettaNet-overeenkomst maken

1. Zoek en open uw integratie account in de [Azure Portal](https://portal.azure.com)als dit nog niet is geopend.

1. Selecteer in het deel venster **overzicht** de tegel **overeenkomsten** .

   ![De tegel overeenkomsten kiezen](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. Klik onder **overeenkomsten** op **toevoegen**. Geef de details van uw overeenkomst op.

   ![Details van overeenkomst toevoegen](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Naam** | Ja | De naam van de overeenkomst |
   | **Type overeenkomst** | Ja | Selecteer **RosettaNet**. |
   | **Host-partner** | Ja | Een overeenkomst vereist zowel een host-als een gast partner. De host-partner vertegenwoordigt de organisatie die de overeenkomst configureert. |
   | **Host-id** | Ja | Een id voor de host-partner |
   | **Gast partner** | Ja | Een overeenkomst vereist zowel een host-als een gast partner. De gast partner vertegenwoordigt de organisatie die zakendoet met de host-partner. |
   | **Gast identiteit** | Ja | Een id voor de gast partner |
   | **Instellingen voor ontvangen** | Varieert | Deze eigenschappen zijn van toepassing op alle berichten die door de host-partner worden ontvangen |
   | **Instellingen verzenden** | Varieert | Deze eigenschappen zijn van toepassing op alle berichten die worden verzonden door de host-partner |  
   | **RosettaNet PIP-verwijzingen** | Ja | De PIP-verwijzingen voor de overeenkomst. Alle RosettaNet-berichten vereisen PIP-configuraties. |
   ||||

1. Als u uw overeenkomst wilt instellen voor het ontvangen van inkomende berichten van de gast partner, selecteert u **instellingen voor ontvangen**.

   ![Instellingen voor ontvangen](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Als u het ondertekenen of versleutelen voor inkomende berichten wilt inschakelen, selecteert u in **berichten** het selectie vakje **bericht moet zijn ondertekend** of **moet het bericht respectievelijk worden versleuteld** .

      | Eigenschap | Vereist | Beschrijving |
      |----------|----------|-------------|
      | **Het bericht moet worden ondertekend** | Nee | Registreer inkomende berichten met het geselecteerde certificaat. |
      | **Certificaat** | Ja, als ondertekenen is ingeschakeld | Het certificaat dat moet worden gebruikt voor ondertekening |
      | **Bericht versleuteling inschakelen** | Nee | Inkomende berichten met het geselecteerde certificaat versleutelen. |
      | **Certificaat** | Ja, als versleuteling is ingeschakeld | Het certificaat dat voor versleuteling moet worden gebruikt |
      ||||

   1. Selecteer onder elke selectie het respectieve [certificaat](./logic-apps-enterprise-integration-certificates.md), dat u eerder hebt toegevoegd aan uw integratie account, dat u kunt gebruiken voor ondertekening of versleuteling.

1. Selecteer **instellingen verzenden** om uw overeenkomst voor het verzenden van berichten naar de gast partner in te stellen.

   ![Instellingen verzenden](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Als u het ondertekenen of versleutelen voor uitgaande berichten wilt inschakelen, selecteert u in **berichten** de optie **bericht ondertekening inschakelen** of respectievelijk **bericht versleuteling** inschakelen. Selecteer onder elke selectie het respectievelijke algoritme en [certificaat](./logic-apps-enterprise-integration-certificates.md), dat u eerder hebt toegevoegd aan uw integratie account, dat u kunt gebruiken voor ondertekening of versleuteling.

      | Eigenschap | Vereist | Beschrijving |
      |----------|----------|-------------|
      | **Bericht ondertekening inschakelen** | Nee | Uitgaande berichten ondertekenen met het geselecteerde handtekening algoritme en certificaat. |
      | **Algoritme voor ondertekening** | Ja, als ondertekenen is ingeschakeld | Het Ondertekeningsalgoritme dat moet worden gebruikt, op basis van het geselecteerde certificaat |
      | **Certificaat** | Ja, als ondertekenen is ingeschakeld | Het certificaat dat moet worden gebruikt voor ondertekening |
      | **Bericht versleuteling inschakelen** | Nee | Versleutel uitgaande met het geselecteerde versleutelings algoritme en certificaat. |
      | **Versleutelings algoritme** | Ja, als versleuteling is ingeschakeld | Het versleutelings algoritme dat moet worden gebruikt, op basis van het geselecteerde certificaat |
      | **Certificaat** | Ja, als versleuteling is ingeschakeld | Het certificaat dat voor versleuteling moet worden gebruikt |
      ||||

   1. Geef onder **eind punten** de vereiste url's op die moeten worden gebruikt voor het verzenden van actie berichten en bevestigingen.

      | Eigenschap | Vereist | Beschrijving |
      |----------|----------|-------------|
      | **Actie-URL** |  Ja | De URL die moet worden gebruikt voor het verzenden van actie berichten. De URL is een verplicht veld voor zowel synchrone als asynchrone berichten. |
      | **Bevestigings-URL** | Ja | De URL die moet worden gebruikt voor het verzenden van bevestigings berichten. De URL is een verplicht veld voor asynchrone berichten. |
      ||||

1. Als u uw overeenkomst met de RosettaNet PIP-verwijzingen voor partners wilt instellen, selecteert u **ROSETTANET PIP-verwijzingen**. Selecteer onder **PIP-naam** de naam voor het eerder gemaakte PIP.

   ![PIP-verwijzingen](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   De overige eigenschappen worden door de selectie gevuld, op basis van de PIP die u hebt ingesteld in uw integratie account. Als dat nodig is, kunt u de **rol PIP** wijzigen.

   ![Geselecteerde PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Nadat u deze stappen hebt voltooid, kunt u RosettaNet-berichten verzenden of ontvangen.

## <a name="rosettanet-templates"></a>RosettaNet-sjablonen

Voor het versnellen van de ontwikkeling en het aanbevelen van integratie patronen kunt u logische app-sjablonen gebruiken voor het decoderen en coderen van RosettaNet-berichten. Wanneer u een logische app maakt, kunt u kiezen uit de sjabloon galerie in de ontwerp functie voor logische apps. U kunt deze sjablonen ook vinden in de [github-opslag plaats voor Azure Logic apps](https://github.com/Azure/logicapps).

![RosettaNet-sjablonen](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>RosettaNet-berichten ontvangen of decoderen

1. [Maak een lege logische app](quickstart-create-first-logic-app-workflow.md).

1. [Koppel uw integratie account](logic-apps-enterprise-integration-create-integration-account.md#link-account) aan uw logische app.

1. Voordat u een actie kunt toevoegen om het RosettaNet-bericht te decoderen, moet u een trigger toevoegen voor het starten van uw logische app, zoals een trigger voor aanvragen.

1. Nadat u de trigger hebt toegevoegd, kiest u **nieuwe stap**.

   ![Aanvraag trigger toevoegen](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Voer in het zoekvak ' RosettaNet ' in en selecteer deze actie: **RosettaNet decoderen**

   ![Zoek en selecteer de actie ' RosettaNet decode '](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Geef de informatie op voor de eigenschappen van de actie:

   ![Scherm afbeelding die laat zien waar u de gegevens voor de eigenschappen van de actie opgeeft.](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Bericht** | Ja | Het RosettaNet-bericht dat moet worden gedecodeerd  |
   | **Kopteksten** | Ja | De HTTP-headers die de waarden opgeven voor de versie, die de RNIF-versie is, en het antwoord type, waarmee het communicatie type tussen de partners wordt aangegeven en synchroon of asynchroon kan zijn |
   | **Role** | Ja | De rol van de host-partner in het PIP- |
   ||||

   De uitvoer van de RosettaNet decode-actie, samen met andere eigenschappen, omvat het **uitgaande signaal**, dat u kunt coderen en terugsturen naar de partner, of een andere actie op die uitvoer moet uitvoeren.

## <a name="send-or-encode-rosettanet-messages"></a>RosettaNet-berichten verzenden of coderen

1. [Maak een lege logische app](quickstart-create-first-logic-app-workflow.md).

1. [Koppel uw integratie account](logic-apps-enterprise-integration-create-integration-account.md#link-account) aan uw logische app.

1. Voordat u een actie kunt toevoegen om het RosettaNet-bericht te coderen, moet u een trigger toevoegen voor het starten van uw logische app, zoals een aanvraag trigger.

1. Nadat u de trigger hebt toegevoegd, kiest u **nieuwe stap**.

   ![Aanvraag trigger toevoegen](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Voer in het zoekvak ' RosettaNet ' in en selecteer deze actie: **RosettaNet coderen**

   ![Zoek en selecteer de actie ' RosettaNet encode '](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Geef de informatie op voor de eigenschappen van de actie:

   ![Actie Details opgeven](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Bericht** | Ja | Het RosettaNet-bericht dat moet worden gecodeerd  |
   | **Host-partner** | Ja | De naam van de host-partner |
   | **Gast partner** | Ja | De naam van de gast partner |
   | **PIP-code** | Ja | De PIP-code |
   | **PIP-versie** | Ja | De PIP-versie |  
   | **PIP-exemplaar-id** | Ja | De unieke id voor dit PIP-bericht |  
   | **Berichttype** | Ja | Het type van het bericht dat moet worden gecodeerd |  
   | **Role** | Ja | De rol van de host-partner |
   ||||

   Het gecodeerde bericht is nu gereed om te verzenden naar de partner.

1. In dit voor beeld wordt de **http** -actie, de naam ' http-gecodeerd bericht verzenden naar de partner ', gebruikt om het gecodeerde bericht te verzenden.

   ![HTTP-actie voor het verzenden van RosettaNet-bericht](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   RosettaNet-standaarden worden bedrijfs transacties alleen als voltooid beschouwd wanneer alle stappen zijn voltooid die zijn gedefinieerd door de PIP.

1. Nadat de host het gecodeerde bericht naar de partner heeft verzonden, wacht de host op het signaal en de bevestiging. Als u deze taak wilt uitvoeren, voegt u de **RosettaNet wachten op antwoord** actie toe.

   ![Actie ' RosettaNet wachten op antwoord ' toevoegen](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   De duur die moet worden gebruikt voor wachtend en het aantal nieuwe pogingen is gebaseerd op de PIP-configuratie in uw integratie account. Als de reactie niet wordt ontvangen, wordt door deze actie een melding van de fout gegenereerd. Als u nieuwe pogingen wilt afhandelen, moet u de **code ring** altijd plaatsen en **wachten op reactie** acties in een lus **until** .

   ![Until-lus met RosettaNet-acties](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het valideren, transformeren en andere bericht bewerkingen met de [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
