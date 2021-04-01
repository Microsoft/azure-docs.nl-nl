---
title: De toegang tot het apparaat, de kracht en de connectiviteits modus Azure Data Box Gateway
description: Hierin wordt beschreven hoe u de toegangs-, Power-en connectiviteits modus beheert voor het Azure Data Box Gateway apparaat waarmee gegevens kunnen worden overgebracht naar Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: c4e2894d193309c169adbea96491e0754d479a8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786805"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>De toegang, de kracht en de connectiviteits modus voor uw Azure Data Box Gateway beheren

In dit artikel wordt beschreven hoe u de modus toegang, kracht en connectiviteit beheert voor uw Azure Data Box Gateway. Deze bewerkingen worden uitgevoerd via de lokale webgebruikersinterface of de Azure Portal. 

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Apparaattoegang beheren
> * Connectiviteits modus beheren
> * Energie beheren

## <a name="manage-device-access"></a>Apparaattoegang beheren

De toegang tot uw Data Box Gateway-apparaat wordt bepaald door het gebruik van een apparaatwachtwoord. U kunt het wacht woord wijzigen via de lokale webgebruikersinterface. U kunt het wacht woord van het apparaat ook opnieuw instellen in de Azure Portal.

### <a name="change-device-password"></a>Wachtwoord voor apparaat wijzigen

Volg deze stappen in de lokale gebruikers interface om het wacht woord van het apparaat te wijzigen.

1. Ga in de lokale web-UI naar **onderhoud > wachtwoord wijziging**.
2. Voer het huidige wacht woord en vervolgens het nieuwe wacht woord in. Het opgegeven wacht woord moet tussen 8 en 16 tekens lang zijn. Het wacht woord moet drie van de volgende tekens bevatten: hoofd letters, kleine letters, cijfers en speciale tekens. Bevestig het nieuwe wacht woord.

    ![Wachtwoord wijzigen](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Klik op **wacht woord wijzigen**.
 
### <a name="reset-device-password"></a>Wacht woord van apparaat opnieuw instellen

Voor de werk stroom opnieuw instellen is niet vereist dat de gebruiker het oude wacht woord intrekt en is nuttig wanneer het wacht woord verloren is gegaan. Deze werk stroom wordt uitgevoerd in de Azure Portal.

1. Ga in het Azure Portal naar **overzicht > het beheerders wachtwoord opnieuw** in te stellen.

    ![Wachtwoord opnieuw instellen](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Voer het nieuwe wacht woord in en bevestig het. Het opgegeven wacht woord moet tussen 8 en 16 tekens lang zijn. Het wacht woord moet drie van de volgende tekens bevatten: hoofd letters, kleine letters, cijfers en speciale tekens. Klik op **Opnieuw instellen**.

    ![Wacht woord 2 opnieuw instellen](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Toegang tot de bedrijfsresources

Als u uw Azure Data Box Gateway, IoT Hub en Azure Storage resource wilt maken, hebt u machtigingen nodig als bijdrager of hoger op het niveau van een resource groep. U moet ook de bijbehorende resource providers registreren. Voor bewerkingen waarbij de activerings sleutel en referenties betrokken zijn, zijn er ook machtigingen voor het Azure Active Directory Graph API vereist. Deze worden beschreven in de volgende secties.

### <a name="manage-microsoft-graph-api-permissions"></a>Microsoft Graph API-machtigingen beheren

Bij het genereren van de activerings sleutel voor het apparaat of voor het uitvoeren van bewerkingen waarvoor referenties zijn vereist, hebt u machtigingen nodig voor de Microsoft Graph-API. De bewerkingen die referenties nodig hebben, kunnen zijn:

-  Een share maken met een gekoppeld opslag account.
-  Het maken van een gebruiker die toegang heeft tot de shares op het apparaat.

U moet `User` toegang hebben tot de Active Directory Tenant, zodat u dit kunt doen `Read all directory objects` . Een gast gebruiker heeft geen machtigingen voor `Read all directory objects` . Als u een gast bent, worden bewerkingen zoals het genereren van een activerings sleutel, het maken van een share op uw apparaat en het maken van een gebruiker mislukt.

Zie [Microsoft Graph permissions Reference](/graph/permissions-reference)(Engelstalig) voor meer informatie over het verlenen van toegang aan gebruikers om de API te Microsoft Graph.

### <a name="register-resource-providers"></a>Resourceprovider registreren

Als u een resource in azure wilt inrichten (in het Azure Resource Manager model), hebt u een resource provider nodig die het maken van die resource ondersteunt. Als u bijvoorbeeld een virtuele machine wilt inrichten, moet u een resource provider micro soft. Compute in het abonnement hebben.
 
Resourceproviders worden geregistreerd op het niveau van het abonnement. Nieuwe Azure-abonnementen worden standaard vooraf geregistreerd bij een lijst met veelgebruikte resourceproviders. De resource provider voor micro soft. DataBoxEdge is niet opgenomen in deze lijst.

U hoeft geen toegangs machtigingen voor het abonnements niveau te verlenen zodat gebruikers resources zoals ' micro soft. DataBoxEdge ' kunnen maken in resource groepen waarvoor ze eigendoms rechten hebben, mits de resource providers voor deze resources al zijn geregistreerd.

Voordat u een resource gaat maken, moet u ervoor zorgen dat de resource provider is geregistreerd in het abonnement. Als de resource provider niet is geregistreerd, moet u ervoor zorgen dat de gebruiker die de nieuwe resource maakt, voldoende rechten heeft om de vereiste resource provider te registreren op het abonnements niveau. Als u dit nog niet hebt gedaan, ziet u de volgende fout:

*Het abonnement \<Subscription name> heeft geen machtigingen voor het registreren van de resource provider (s): micro soft. DataBoxEdge.*


Voer de volgende opdracht uit om een lijst met geregistreerde resource providers in het huidige abonnement op te halen:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Voor een Data Box Gateway apparaat `Microsoft.DataBoxEdge` moet worden geregistreerd. Als u zich wilt registreren `Microsoft.DataBoxEdge` , moet de beheerder van het abonnement de volgende opdracht uitvoeren:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Zie [fouten voor de registratie van de resource provider oplossen](../azure-resource-manager/templates/error-register-resource-provider.md)voor meer informatie over het registreren van een resource provider.

## <a name="manage-connectivity-mode"></a>Connectiviteits modus beheren

Naast de standaard normale modus kan uw apparaat ook worden uitgevoerd in een gedeeltelijk losgekoppelde of niet-verbonden modus:

- **Gedeeltelijk verbroken** : in deze modus kan het apparaat geen gegevens uploaden naar de shares. Het kan echter worden beheerd via de Azure Portal.

    Deze modus wordt doorgaans gebruikt om het gebruik van netwerk bandbreedte te minimaliseren bij een satelliet netwerk met een Data limiet. Mini maal netwerk verbruik kan nog steeds optreden voor bewerkingen voor het controleren van apparaten.

- De **verbinding is verbroken** : in deze modus is het apparaat volledig losgekoppeld van de Cloud en zijn de Cloud-uploads en down loads uitgeschakeld. Het apparaat kan alleen worden beheerd via de lokale webgebruikersinterface.

    Deze modus wordt doorgaans gebruikt wanneer u uw apparaat offline wilt zetten.

Voer de volgende stappen uit om de modus apparaat te wijzigen:

1. Ga in de lokale web-UI van uw apparaat naar **configuratie > Cloud instellingen**.
2. Schakel de **Cloud upload en down load** uit.
3. Als u het apparaat wilt uitvoeren in een gedeeltelijk niet-verbonden modus, schakelt u **Azure Portal-beheer** in.

    ![Connectiviteits modus](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Als u het apparaat in de modus zonder verbinding wilt uitvoeren, schakelt u **Azure Portal beheer** uit. Het apparaat kan nu alleen worden beheerd via de lokale webgebruikersinterface.

    ![Connectiviteits modus 2](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Energie beheren

U kunt uw virtuele apparaat afsluiten of opnieuw starten met de lokale webinterface. U wordt aangeraden de shares offline op de host en vervolgens op het apparaat te zetten voordat u het apparaat opnieuw opstart. Deze actie minimaliseert de kans op beschadigde gegevens.

1. Ga in de lokale web-UI naar **onderhoud > energie-instellingen**.
2. Klik op **Afsluiten** of **opnieuw opstarten** , afhankelijk van wat u wilt doen.

    ![Energie-instellingen](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Wanneer u om bevestiging wordt gevraagd, klikt u op **Ja** om door te gaan.

> [!NOTE]
> Als u het virtuele apparaat uitschakelt, moet u het apparaat starten via Hypervisor-beheer.