---
title: Azure Data Studio Dash boards
description: Azure Data Studio Dash boards
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a96be6d4da3d292b2e9881652aad28f318ccee8a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92107569"
---
# <a name="azure-data-studio-dashboards"></a>Azure Data Studio Dash boards

[Azure Data Studio](/sql/azure-data-studio/what-is) biedt een soort gelijke ervaring als de Azure portal voor het weer geven van informatie over uw Azure-Arc-resources.  Deze weer gaven worden **Dash boards** genoemd en hebben een indeling en opties die vergelijkbaar zijn met wat u zou kunnen zien over een bepaalde resource in de Azure Portal, maar bieden u de flexibiliteit om de gegevens lokaal in uw omgeving te zien in gevallen waarin u geen verbinding hebt met Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connecting-to-a-data-controller"></a>Verbinding maken met een gegevens controller

### <a name="prerequisites"></a>Vereisten

- [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) downloaden
- De Azure Arc-extensie is geïnstalleerd

### <a name="determine-the-data-controller-server-api-endpoint-url"></a>De URL van het data controller Server API-eind punt bepalen

Eerst moet u Azure Data Studio verbinding maken met uw data controller Service API-eind punt-URL.

Als u dit eind punt wilt ophalen, kunt u de volgende opdracht uitvoeren:

```console
kubectl get svc/controller-svc-external -n <namespace name>

#Example:
kubectl get svc/controller-svc-external -n arc
```

U ziet uitvoer die er ongeveer als volgt uitziet:

```console
NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                       AGE
controller-svc-external   LoadBalancer   10.0.175.137   52.154.152.24    30080:32192/TCP                               22h
```

Als u een LoadBalancer-type gebruikt, wilt u het externe IP-adres en poort nummer kopiëren. Als u NodePort gebruikt, wilt u het IP-adres van uw Kubernetes API-server en het poort nummer dat wordt vermeld onder de kolom poort (S) gebruiken.

Nu gaat u een URL naar uw eind punt samen stellen door deze informatie als volgt te combi neren:

```console
https://<ip address>:<port>

Example:
https://52.154.152.24:30080
```

Noteer uw IP-adres zoals u dit in de volgende stap gaat gebruiken.

### <a name="connect"></a>Verbinding maken

1. Azure Data Studio openen

1. Selecteer het tabblad **verbindingen** aan de linkerkant

Vouw het deel venster met de naam **Azure Arc-controllers** naar beneden uit.

Klik op het pictogram + om een nieuwe gegevens controller verbinding toe te voegen.

Klik boven aan het scherm in het opdracht palet op ENTER om de URL op te geven die u in stap 1 hebt gemaakt.
Voer de gebruikers naam voor de gegevens controller in.  Dit was de waarde van de gebruikers naam die u tijdens de implementatie van de gegevens controller hebt door gegeven.  Klik op ENTER.
Voer het wacht woord voor de gegevens controller in.  Dit was de wachtwoord waarde die u tijdens de implementatie van de gegevens controller hebt door gegeven. Klik op ENTER.

Nu u verbinding hebt met een gegevens controller, kunt u de Dash boards voor de gegevens controller en alle door SQL beheerde instanties of PostgreSQL grootschalige-server groeps bronnen weer geven die u hebt.

## <a name="view-the-data-controller-dashboard"></a>Het dash board van de gegevens controller weer geven

Klik met de rechter muisknop op de gegevens controller in het deel venster verbindingen in het Uitvouw bare deel venster **Arc-controllers** en kies **beheren**.

Hier ziet u details over de gegevens controller bron, zoals de naam, de regio, de verbindings modus, de resource groep, het abonnement, het eind punt van de controller en de naam ruimte.  U ziet ook een lijst met alle beheerde database resources die worden beheerd door de gegevens controller.

U ziet dat de lay-out lijkt op wat u ziet in de Azure Portal.

U kunt het maken van een SQL Managed instance of PostgreSQL grootschalige-Server groep ook starten door te klikken op de knop + nieuw exemplaar.

U kunt ook de Azure Portal in context voor deze gegevens controller openen door te klikken op de knop openen in Azure Portal.

## <a name="view-the-sql-managed-instance-dashboards"></a>De Dash boards van het SQL-beheerde exemplaar weer geven

Als u een aantal SQL Managed instances hebt gemaakt, kunt u deze weer geven in het deel venster verbindingen in het uitbreid bare paneel van Azure-gegevens controllers onder de gegevens controller die ze beheert.

Als u het dash board van SQL Managed instance voor een bepaald exemplaar wilt weer geven, klikt u met de rechter muisknop op het exemplaar en kiest u beheren.

Het deel venster verbinding wordt weer gegeven aan de rechter kant en vraagt u om de aanmelding/het wacht woord om verbinding te maken met het SQL-exemplaar. Als u weet dat de verbindings gegevens kunnen worden ingevoerd en klikt u op verbinding maken.  Als u niet weet, kunt u op Annuleren klikken.  In beide gevallen wordt u naar het dash board gebracht wanneer het deel venster verbinding wordt gesloten.

Op het tabblad Overzicht kunt u details weer geven over het Managed SQL-exemplaar, zoals de resource groep, de gegevens controller, de abonnements-ID, de status, de regio en meer.  U kunt ook een koppeling zien waarop u kunt klikken om naar het Grafana-of Kibana-dash board te gaan in de context van het SQL Managed instance.

Als u verbinding kunt maken met het exemplaar van SQL-beheer, kunt u hier aanvullende informatie bekijken.

U kunt de SQL Managed instance hier verwijderen of de Azure Portal openen om het beheerde exemplaar van SQL in het Azure Portal weer te geven.

Als u op het tabblad verbindings reeksen aan de linkerkant klikt, kunt u een lijst met vooraf samengestelde verbindings reeksen voor die SQL Managed instance bekijken, zodat u deze eenvoudig kunt kopiëren/plakken in verschillende andere toepassingen of code.

## <a name="view-the-postgresql-hyperscale-server-group-dashboards"></a>De PostgreSQL grootschalige-Server groep-Dash boards weer geven

Als u een aantal PostgreSQL grootschalige-Server groepen hebt gemaakt, kunt u deze weer geven in het deel venster verbindingen van het uitbreidings venster Azure-gegevens controllers, onder de gegevens controller die ze beheert.

Als u het dash board van de PostgreSQL grootschalige-Server groep voor een bepaalde server groep wilt weer geven, klikt u met de rechter muisknop op de Server groep en kiest u beheren.

Op het tabblad Overzicht kunt u details weer geven over de Server groep, zoals de resource groep, de gegevens controller, de abonnements-ID, de status, de regio en meer.  U kunt ook een koppeling zien waarop u kunt klikken om naar de Grafana-of Kibana-Dash boards in de context van die server groep te gaan.

U kunt de Server groep hier verwijderen of de Azure Portal openen om de Server groep in het Azure Portal weer te geven.

Als u op het tabblad verbindings reeksen aan de linkerkant klikt, ziet u een lijst met vooraf samengestelde verbindings reeksen voor die server groep waarmee u gemakkelijk kunt kopiëren/plakken in verschillende andere toepassingen of code.

Als u op het tabblad Eigenschappen aan de linkerkant klikt, kunt u aanvullende informatie weer geven.

Als u op het tabblad Resource status aan de linkerkant klikt, kunt u de huidige status op hoog niveau van die server groep weer geven.

Als u aan de linkerkant op het tabblad problemen vaststellen en oplossen klikt, kunt u de PostgreSQL Troubleshooting notebook starten.

Als u aan de linkerkant op het tabblad nieuwe ondersteunings aanvraag klikt, kunt u de Azure Portal in de context van de Server groep starten en daar een ondersteunings aanvraag voor Azure maken.