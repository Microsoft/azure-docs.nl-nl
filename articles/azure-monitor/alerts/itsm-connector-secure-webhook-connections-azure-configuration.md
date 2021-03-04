---
title: IT Service Management-connector-beveiligd exporteren in Azure Monitor-Azure-configuraties
description: In dit artikel wordt beschreven hoe u Azure kunt configureren om uw ITSM-producten/-services te verbinden met beveiligde export in Azure Monitor om ITSM-werk items centraal te controleren en te beheren.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 01/03/2021
ms.openlocfilehash: 8eb9430e3d280c52cf84c61f0a44cb12152ac054
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037537"
---
# <a name="configure-azure-to-connect-itsm-tools-using-secure-export"></a>Azure configureren om verbinding te maken met ITSM-hulpprogram ma's met behulp van beveiligde export

In dit artikel vindt u informatie over het configureren van Azure om ' beveiligde export ' te gebruiken.
Voer de volgende stappen uit om ' beveiligde export ' te gebruiken:

1. [Registreer uw app bij Azure AD.](./itsm-connector-secure-webhook-connections-azure-configuration.md#register-with-azure-active-directory)
1. [Service-Principal definiëren.](./itsm-connector-secure-webhook-connections-azure-configuration.md#define-service-principal)
1. [Maak een beveiligde webhook-actie groep.](./itsm-connector-secure-webhook-connections-azure-configuration.md#create-a-secure-webhook-action-group)
1. Configureer uw partner omgeving.
    Beveiligd exporteren ondersteunt verbindingen met de volgende ITSM-hulpprogram ma's:
    * [ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
    * [BMC Helix](./itsmc-secure-webhook-connections-bmc.md)

## <a name="register-with-azure-active-directory"></a>Registreren bij Azure Active Directory

Volg deze stappen om de toepassing te registreren bij Azure AD:

1. Volg de stappen in [Een toepassing registreren bij het Microsoft identity platform](../../active-directory/develop/quickstart-register-app.md).
2. Selecteer **toepassing zichtbaar** maken in azure AD.
3. Selecteer **instellen** voor de URI van de **toepassings-id**.

   [![Scherm afbeelding van de optie voor het instellen van de U R I van de toepassing die ik D.](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad.png)](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad-expand.png#lightbox)
4. Selecteer **Opslaan**.

## <a name="define-service-principal"></a>Service-Principal definiëren

De Action Group-service is een eerste toepassing die daarom toestemming heeft om verificatie tokens van uw AAD-toepassing te verkrijgen om verificatie met service nu te kunnen doen.
Als optionele stap kunt u de toepassingsrol definiëren in het manifest van de gemaakte app, zodat u de toegang kunt beperken tot een manier waarop alleen bepaalde toepassingen met die specifieke rol berichten kunnen verzenden. Deze rol moet vervolgens worden toegewezen aan de service-principal van de actie groep (vereist Tenant beheerders bevoegdheden).

Deze stap kan worden uitgevoerd met dezelfde [Power shell-opdrachten](../alerts/action-groups.md#secure-webhook-powershell-script).

## <a name="create-a-secure-webhook-action-group"></a>Maak een actiegroep voor een beveiligde webhook

Nadat uw toepassing is geregistreerd bij Azure AD, kunt u werk items maken in uw ITSM-hulp programma op basis van Azure-waarschuwingen met behulp van de actie beveiligde webhook in actie groepen.

Actie groepen bieden een modulaire en herbruikbare manier om acties voor Azure-waarschuwingen te activeren. U kunt actie groepen met metrische waarschuwingen, waarschuwingen voor activiteiten logboeken en waarschuwingen voor Azure Log Analytics gebruiken in de Azure Portal.
Zie voor meer informatie over actie groepen [actie groepen maken en beheren in de Azure Portal](../alerts/action-groups.md).

Volg deze instructies voor beveiligde webhook om een webhook aan een actie toe te voegen:

1. Zoek in het [Azure Portal](https://portal.azure.com/)naar en selecteer **monitor**. In het deel venster **monitor** worden al uw bewakings instellingen en-gegevens in één weer gave geconsolideerd.
2. Selecteer **waarschuwingen**  >  **acties beheren**.
3. Selecteer [actie groep toevoegen](../alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal)en vul de velden in.
4. Voer een naam in het vak Naam van de **actie groep** in en voer een naam in het vak **korte naam** in. De korte naam wordt gebruikt in plaats van een volledige naam van de actiegroep als er meldingen via deze groep worden verzonden.
5. Selecteer **beveiligde webhook**.
6. Selecteer deze details:
   1. Selecteer de object-ID van de Azure Active Directory instantie die u hebt geregistreerd.
   2. Plak voor de URI in de webhook-URL die u hebt gekopieerd uit de [ITSM-werk omgeving](#configure-the-itsm-tool-environment).
   3. Stel **het algemene waarschuwings schema** in op **Ja**. 

   In de volgende afbeelding ziet u de configuratie van een voor beeld van een beveiligde webhook-actie:

   ![Scherm afbeelding met een beveiligde webhook-actie.](media/itsm-connector-secure-webhook-connections-azure-configuration/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>De ITSM-hulp programma omgeving configureren

De configuratie bevat twee stappen:

1. Haal de URI voor de beveiligde export definitie op.
2. Definities volgens de stroom van het ITSM-hulp programma.

## <a name="next-steps"></a>Volgende stappen

* [ServiceNow-configuratie voor beveiligde export](./itsmc-secure-webhook-connections-servicenow.md)
* [Configuratie van met BMC beveiligde export](./itsmc-secure-webhook-connections-bmc.md)
