---
title: Ondersteuning voor implementatie van Azure VMware-oplossing of het inrichten van een fout
description: Informatie ophalen uit de privécloud van uw Azure VMware-oplossing om een service aanvraag te doen voor een implementatie van een Azure VMware-oplossing of een inrichtings fout.
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 27b645f4ca225fdd74bca6499b6581b3803e41a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542402"
---
# <a name="open-a-support-request-for-an-azure-vmware-solution-deployment-or-provisioning-failure"></a>Een ondersteunings aanvraag openen voor een implementatie van een Azure VMware-oplossing of een inrichtings fout

In dit artikel leest u hoe u een [ondersteunings aanvraag](https://rc.portal.azure.com/#create/Microsoft.Support) opent en belang rijke informatie verstrekt voor een implementatie van een Azure VMware-oplossing of een inrichtings fout. 

Als er een fout is opgetreden in uw privécloud, moet u een ondersteunings aanvraag openen in de Azure Portal. Als u een ondersteunings aanvraag wilt openen, moet u eerst een aantal belang rijke informatie ophalen in de Azure Portal:

- Correlatie-id
- ID van het Azure ExpressRoute-circuit
- Foutberichten

## <a name="get-the-correlation-id"></a>De correlatie-ID ophalen
 
Wanneer u een privécloud of een resource in azure maakt, wordt er automatisch een correlatie-ID voor de resource gegenereerd. Neem de correlatie-ID van de privécloud op in uw ondersteunings aanvraag om het verzoek sneller te openen en op te lossen.

In de Azure Portal kunt u op twee manieren de correlatie-ID voor een resource ophalen:

* **Overzichts** venster
* Implementatie logboeken
 
 ### <a name="get-the-correlation-id-from-the-resource-overview"></a>De correlatie-ID uit het resource-overzicht ophalen

Hier volgt een voor beeld van de bewerkings Details van een mislukte implementatie van een privécloud, waarbij de correlatie-ID is geselecteerd:

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Scherm opname van een mislukte implementatie van een privécloud waarvoor de correlatie-ID is geselecteerd.":::

Voor toegang tot de implementatie resultaten in het **overzichts** venster van een privécloud:

1. Selecteer uw privécloud in de Azure Portal.

1. Selecteer **overzicht** in het menu links.

Nadat een implementatie is gestart, worden de resultaten van de implementatie weer gegeven in het **overzichts** venster van de privécloud.

Kopieer en sla de correlatie-ID van de implementatie van de privécloud op die u wilt toevoegen in de service aanvraag.

### <a name="get-the-correlation-id-from-the-deployment-log"></a>De correlatie-ID ophalen uit het implementatie logboek

U kunt de correlatie-ID voor een mislukte implementatie verkrijgen door het activiteiten logboek van de implementatie te doorzoeken in de Azure Portal.

Het implementatie logboek openen:

1. Selecteer uw privécloud in de Azure Portal en selecteer vervolgens het pictogram meldingen.

   :::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Scherm opname van het pictogram meldingen in het Azure Portal.":::

1. Selecteer in  het deel venster meldingen **meer gebeurtenissen in het activiteiten logboek**:

    :::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Scherm afbeelding met de meer gebeurtenissen in de koppeling activiteiten logboek die is geselecteerd in het deel venster meldingen.":::

1. Zoek de naam van de resource of andere informatie die u hebt gebruikt om de resource te maken om de mislukte implementatie en de correlatie-ID te vinden. 

    In het volgende voor beeld ziet u zoek resultaten voor een privécloud-resource met de naam pc03.
 
    :::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Scherm opname van de zoek resultaten voor een persoonlijke Cloud resource en het deel venster een PrivateCloud maken of bijwerken.":::
 
1. Selecteer in de zoek resultaten in het deel venster **activiteiten logboek** de bewerkings naam van de mislukte implementatie.

1. Selecteer in het deel venster **een PrivateCloud maken of bijwerken** het tabblad **JSON** en zoek `correlationId` in het logboek dat wordt weer gegeven. Kopieer de `correlationId` waarde zodat deze wordt toegevoegd aan uw ondersteunings aanvraag. 
 
## <a name="copy-error-messages"></a>Fout berichten kopiëren

Neem alle fout berichten die worden weer gegeven in de Azure Portal, om uw implementatie probleem op te lossen. Selecteer een waarschuwings bericht om een samen vatting van fouten weer te geven:
 
:::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Scherm opname met de fout details op het tabblad samen vatting van het deel venster fouten, waarbij het pictogram kopiëren is geselecteerd.":::

Selecteer het Kopieer pictogram om het fout bericht te kopiëren. Sla het gekopieerde bericht op dat u wilt toevoegen aan uw ondersteunings aanvraag.
 
## <a name="get-the-expressroute-id-uri"></a>De ExpressRoute-ID (URI) ophalen
 
Misschien probeert u een bestaande privécloud te schalen of een peer te koppelen met het ExpressRoute-circuit van de privécloud. In dat scenario moet de ExpressRoute-ID worden toegevoegd aan uw ondersteunings aanvraag.

De ExpressRoute-ID kopiëren:

1. Selecteer uw privécloud in de Azure Portal.
1. Selecteer in het menu links onder **beheren** de optie **connectiviteit**. 
1. Selecteer in het rechterdeel venster het tabblad **ExpressRoute** .
1. Selecteer het Kopieer pictogram voor de **ExpressRoute-id** en sla de waarde op die moet worden gebruikt in uw ondersteunings aanvraag.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Kopieer de ExpressRoute-ID naar het klem bord."::: 
 
## <a name="pre-validation-failures"></a>Mislukte voorbereidende validaties

Als de pre-validatie controle van uw privécloud is mislukt (vóór de implementatie), is er geen correlatie-ID gegenereerd. In dit scenario kunt u de volgende informatie in uw ondersteunings aanvraag opgeven:

- Fout-en fout berichten. Deze berichten kunnen nuttig zijn in veel storingen, bijvoorbeeld voor problemen met betrekking tot quota. Het is belang rijk dat u deze berichten kopieert en opneemt in de ondersteunings aanvraag, zoals beschreven in dit artikel.
- Informatie die u hebt gebruikt om de privécloud van de Azure VMware-oplossing te maken, met inbegrip van:
  - Locatie
  - Resourcegroep
  - Resourcenaam

## <a name="create-your-support-request"></a>Uw ondersteunings aanvraag maken

Zie een ondersteunings [aanvraag voor Azure maken](../azure-portal/supportability/how-to-create-azure-support-request.md)voor algemene informatie over het maken van een ondersteunings aanvraag. 

Een ondersteunings aanvraag maken voor een implementatie van een Azure VMware-oplossing of een inrichtings fout:

1. Selecteer in de Azure Portal het **Help** -pictogram en selecteer vervolgens **nieuwe ondersteunings aanvraag**.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Scherm opname van het deel venster nieuwe ondersteunings aanvraag in de Azure Portal.":::

1. Voer de vereiste gegevens in of Selecteer deze:

   1. Op het tabblad **Basis**:

      1. Selecteer bij **probleem type** **configuratie-en installatie problemen**.

      1. Selecteer **een Privécloud inrichten voor het** **subtype** van het probleem.

   1. Op het tabblad **Details** :

      1. Voer de vereiste informatie in of selecteer deze.

      1. Plak uw correlatie-ID of ExpressRoute-ID waar deze informatie wordt aangevraagd. Als u voor deze waarden geen specifiek tekstvak ziet, plakt u deze in het tekstvak **Geef details over het probleem** op.

    1. Plak alle fout details, met inbegrip van de fout-of fout berichten die u hebt gekopieerd, in het tekstvak **Details over het probleem opgeven** .

1. Controleer uw vermeldingen en selecteer vervolgens **maken** om uw ondersteunings aanvraag te maken.
