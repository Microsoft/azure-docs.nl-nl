---
title: Zelfstudie voor web-apps - Azure API for FHIR instellen
description: Deze zelfstudie laat zien hoe u een eenvoudige webtoepassing implementeert. In deze eerste zelfstudie worden de vereisten en de implementatie van Azure API for FHIR beschreven
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.custom: devx-track-js
ms.openlocfilehash: cb183b5c8aff018d4dc73819b938b24ad0daa934
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018176"
---
# <a name="deploy-javascript-app-to-read-data-from-fhir-service"></a>De JavaScript-app implementeren om gegevens van de FHIR-service te lezen
In deze zelfstudie implementeert u een kleine JavaScript-app waarmee gegevens van een FHIR-service worden gelezen. De stappen in deze zelfstudie zijn als volgt:
1. Een FHIR-server implementeren
1. Een openbare client-app registreren
1. Toegang tot de toepassing testen
1. Een webtoepassing maken waarmee deze FHIR-gegevens worden gelezen

## <a name="prerequisites"></a>Vereisten
U hebt de volgende items nodig om met deze set zelfstudies te kunnen beginnen:
1. Een Azure-abonnement
1. Een Azure Active Directory-tenant
1. [Postman](https://www.getpostman.com/) is geïnstalleerd

> [!NOTE]
> Voor deze zelfstudie moeten de FHIR-service, de Azure AD-toepassing en de Azure AD-gebruikers zich allemaal in dezelfde Azure AD-tenant bevinden. Als dit niet het geval is, kunt u deze zelfstudie wel volgen, maar moet u mogelijk enkele van de documenten waarnaar wordt verwezen raadplegen om extra stappen uit te voeren.

## <a name="deploy-azure-api-for-fhir"></a>Azure API for FHIR implementeren
De eerste stap in de zelfstudie is Azure API for FHIR correct in te stellen.

1. Implementeer [Azure API for FHIR](fhir-paas-portal-quickstart.md) als u dat nog niet hebt gedaan.
1. Als u Azure API for FHIR hebt geïmplementeerd, configureert u de [CORS](configure-cross-origin-resource-sharing.md)-instellingen door naar Azure API for FHIR te gaan en CORS te selecteren. 
    1. Stel **Oorsprongen** in op *
    1. Stel **Headers** in op *
    1. Kies onder **Methoden** de optie **Alle selecteren**
    1. Stel de **Maximumleeftijd** in op **600**

## <a name="next-steps"></a>Volgende stappen
Nu u Azure API for FHIR hebt geïmplementeerd, kunt u een openbare clienttoepassing registreren.

>[!div class="nextstepaction"]
>[Openbare client-app registreren](tutorial-web-app-public-app-reg.md)
