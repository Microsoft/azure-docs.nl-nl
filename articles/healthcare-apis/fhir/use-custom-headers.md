---
title: Gegevens toevoegen aan auditlogboeken met aangepaste headers - Azure API for FHIR
description: In dit artikel wordt beschreven hoe u gegevens toevoegt aan auditlogboeken met behulp van aangepaste HTTP-headers in Azure API for FHIR.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: 937be72bfec96119474e7effe9ba88a2cf253444
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017976"
---
# <a name="add-data-to-audit-logs-by-using-custom-http-headers"></a>Aangepaste HTTP-headers gebruiken om gegevens toe te voegen aan auditlogboeken

In de Azure FHIR-API (Fast Healthcare Interoperability Resources) kan een gebruiker in de logboeken aanvullende informatie opnemen, die afkomstig is van het aanroepende systeem.

Als de gebruiker van de API bijvoorbeeld wordt geverifieerd door een extern systeem, stuurt dat systeem de aanroep door naar de FHIR-API. Op de FHIR API-laag is de informatie over de oorspronkelijke gebruiker verloren gegaan omdat de aanroep is doorgestuurd. Het kan nodig zijn om deze gebruikersgegevens te registreren en te bewaren voor controle- of beheerdoeleinden. Het aanroepende systeem kan een gebruikers-id, locatie van de aanroepende functie of andere noodzakelijke gegevens in de HTTP-headers opgeven, die worden meegenomen wanneer de aanroep wordt doorgestuurd.

U kunt deze gegevensstroom bekijken in het volgende diagram:

:::image type="content" source="media/custom-headers/custom-headers-diagram.png" alt-text="Diagram met aangepaste headers":::

U kunt aangepaste headers gebruiken om verschillende soorten informatie vast te leggen. Bijvoorbeeld:

* Identiteits- of autorisatiegegevens
* Oorsprong van de aanroepende functie
* Organisatie van herkomst
* Details van het clientsysteem (elektronisch gezondheidsdossier, patiëntenportal)

> [!IMPORTANT]
> Houd er rekening mee dat de informatie die in aangepaste headers wordt verzonden, 30 dagen lang in een intern Microsoft-logboekregistratiesysteem blijft bewaard nadat die informatie beschikbaar is gekomen in de Azure-logboekregistratie. Het is raadzaam alle gegevens te versleutelen voordat u deze toevoegt aan aangepaste headers. U mag geen PHI-informatie doorgeven via aangepaste headers.

U moet de volgende naamconventie gebruiken voor uw HTTP-headers: X-MS-AZUREFHIR-AUDIT-\<name>.

Deze HTTP-headers zijn opgenomen in een eigenschappenverzameling die aan het logboek wordt toegevoegd. Bijvoorbeeld:

* X-MS-AZUREFHIR-AUDIT-USERID: 1234 
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: XXXX
* X-MS-AZUREFHIR-AUDIT-XYZ: 1234

Deze informatie wordt vervolgens geserialiseerd naar JSON wanneer deze wordt toegevoegd aan de kolom met eigenschappen in het logboek. Bijvoorbeeld:

```json
{ "X-MS-AZUREFHIR-AUDIT-USERID" : "1234",
"X-MS-AZUREFHIR-AUDIT-USERLOCATION" : "XXXX",
"X-MS-AZUREFHIR-AUDIT-XYZ" : "1234" }
```
 
Net als bij elke HTTP-header kan dezelfde headernaam worden herhaald met andere waarden. Bijvoorbeeld:

* X-MS-AZUREFHIR-AUDIT-USERLOCATION: ZiekenhuisA
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: Noodgeval

Wanneer u de waarden toevoegt aan het logboek, worden ze gecombineerd met een door komma's gescheiden lijst. Bijvoorbeeld:

{ "X-MS-AZUREFHIR-AUDIT-USERLOCATION" : "ZiekenhuisA, Noodgeval" }
 
U kunt maximaal 10 unieke headers toevoegen (herhalingen van dezelfde header met verschillende waarden tellen als één header). De totale maximumlengte van de waarde voor een header is 2048 tekens.

Als u de Firefly C# client-API-bibliotheek gebruikt, ziet de code er ongeveer zo uit:

```C#
FhirClient client;
client = new FhirClient(serverUrl);
client.OnBeforeRequest += (object sender, BeforeRequestEventArgs e) =>
{
    // Add custom headers to be added to the logs
    e.RawRequest.Headers.Add("X-MS-AZUREFHIR-AUDIT-UserLocation", "HospitalA");
};
client.Get("Patient");
```
## <a name="next-steps"></a>Volgende stappen
In dit artikel werd beschreven hoe u gegevens kunt toevoegen aan auditlogboeken met behulp van aangepaste headers in de Azure API for FHIR. Hierna krijgt u informatie over andere aanvullende instellingen die u kunt configureren in de Azure API for FHIR.
 
>[!div class="nextstepaction"]
>[Aanvullende instellingen](azure-api-for-fhir-additional-settings.md)
