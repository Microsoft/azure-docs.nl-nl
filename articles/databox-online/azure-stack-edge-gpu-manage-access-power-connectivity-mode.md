---
title: Toegang tot het apparaat van Azure Stack Edge Pro GPU, kracht en connectiviteits modus | Microsoft Docs
description: Hierin wordt beschreven hoe u de toegang, de kracht en de connectiviteits modus beheert voor het Azure Stack Edge Pro GPU-apparaat waarmee gegevens kunnen worden overgebracht naar Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 39990a557315c3fcc79f2b9dab59f25f758ab2bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443111"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-stack-edge-pro-gpu"></a>Toegang, kracht en connectiviteits modus beheren voor uw Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u de modus toegang, kracht en connectiviteit beheert voor uw Azure Stack Edge Pro met GPU-apparaat. Deze bewerkingen worden uitgevoerd via de lokale webgebruikersinterface of de Azure Portal.

Dit artikel is van toepassing op Azure Stack Edge Pro GPU, Azure Stack Edge Pro R en Azure Stack Edge mini-R-apparaten.


In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Apparaattoegang beheren
> * Toegang tot de bedrijfsresources
> * Connectiviteits modus beheren
> * Energie beheren


## <a name="manage-device-access"></a>Apparaattoegang beheren

De toegang tot uw Azure Stack Edge Pro-apparaat wordt bepaald door het gebruik van een wacht woord voor het apparaat. U kunt het wacht woord wijzigen via de lokale webgebruikersinterface. U kunt het wacht woord van het apparaat ook opnieuw instellen in de Azure Portal.

De toegang tot gegevens op de schijven van het apparaat wordt ook bepaald door versleuteling-at-rest-sleutels.

### <a name="change-device-password"></a>Wachtwoord voor apparaat wijzigen

Volg deze stappen in de lokale gebruikers interface om het wacht woord van het apparaat te wijzigen.

1. Ga in de lokale web-UI naar **Onderhouds > wacht woord**.
2. Voer het huidige wacht woord en vervolgens het nieuwe wacht woord in. Het opgegeven wacht woord moet tussen 8 en 16 tekens lang zijn. Het wacht woord moet drie van de volgende tekens bevatten: hoofd letters, kleine letters, cijfers en speciale tekens. Bevestig het nieuwe wacht woord.

    ![Wachtwoord wijzigen](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/change-password-1.png)

3. Selecteer **wacht woord wijzigen**.
 
### <a name="reset-device-password"></a>Wacht woord van apparaat opnieuw instellen

Voor de werk stroom opnieuw instellen is niet vereist dat de gebruiker het oude wacht woord intrekt en is nuttig wanneer het wacht woord verloren is gegaan. Deze werk stroom wordt uitgevoerd in de Azure Portal.

1. Ga in het Azure Portal naar **overzicht > het beheerders wachtwoord opnieuw** in te stellen.

    ![Scherm afbeelding toont het apparaat waarvoor het wacht woord van het apparaat opnieuw is ingesteld.](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/reset-password-1.png)


2. Voer het nieuwe wacht woord in en bevestig het. Het opgegeven wacht woord moet tussen 8 en 16 tekens lang zijn. Het wacht woord moet drie van de volgende tekens bevatten: hoofd letters, kleine letters, cijfers en speciale tekens. Selecteer **Opnieuw instellen**.

    ![Wacht woord 2 opnieuw instellen](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

### <a name="manage-access-to-device-data"></a>Toegang tot apparaatgegevens beheren

Voor de Azure Stack Edge Pro R-en Azure Stack Edge mini R-apparaten wordt de toegang tot apparaatgegevens bepaald met behulp van versleuteling-at-rest-sleutels voor de stations van het apparaat. Nadat u het apparaat voor versleuteling-op-rest hebt geconfigureerd, wordt de optie versleutelings-op-rest sleutels beschikbaar gemaakt in de lokale gebruikers interface van het apparaat. 

Met deze bewerking kunt u de sleutels voor BitLocker-volumes `HcsData` en `HcsInternal` alle zelf versleutelde stations op het apparaat wijzigen.

Volg deze stappen om de sleutels voor versleuteling op rest te draaien.

1. Ga in de lokale gebruikers interface van het apparaat naar de pagina **aan de slag** . Op de tegel **beveiliging** selecteert u **versleuteling-at-rest: de optie sleutels draaien** . Deze optie is alleen beschikbaar nadat u de sleutels voor versleuteling op rest hebt geconfigureerd.

    ![Selecteer sleutels draaien voor versleuteling-at-rest op de pagina aan de slag](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-1.png)

1. U kunt uw eigen BitLocker-sleutels gebruiken of de door het systeem gegenereerde sleutels gebruiken.  

    Als u uw eigen sleutel wilt opgeven, voert u een teken reeks met een lange basis-64-code van 32 tekens in. De invoer is vergelijkbaar met wat u zou doen wanneer u de versleuteling voor de eerste keer configureert.

    ![Uw eigen code ring-at-rest-sleutel nemen](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-2.png)

    U kunt er ook voor kiezen om een door het systeem gegenereerde sleutel te gebruiken.

    ![Door het systeem gegenereerde versleuteling-at-rest-sleutel gebruiken](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-3.png)

1. Selecteer **Toepassen**. De sleutel beveiligingen worden gedraaid.

    ![De nieuwe sleutel voor versleuteling op rest Toep assen](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-4.png)

1. Wanneer u wordt gevraagd het sleutel bestand te downloaden en op te slaan, selecteert u **downloaden en door gaan**. 

    ![Het sleutel bestand downloaden en voortzetten](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-5.png)

    Sla het `.json` sleutel bestand op een veilige locatie op. Dit bestand wordt gebruikt om een potentieel toekomstig herstel van het apparaat mogelijk te maken.

    ![Scherm afbeelding wordt weer gegeven in het dialoog venster wacht woord voor apparaat opnieuw instellen.](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Toegang tot de bedrijfsresources

Als u uw Azure Stack EDGE/Data Box Gateway, IoT Hub en Azure Storage resource wilt maken, hebt u machtigingen nodig als bijdrager of hoger op het niveau van een resource groep. U moet ook de bijbehorende resource providers registreren. Voor bewerkingen waarbij de activerings sleutel en referenties betrokken zijn, zijn er ook machtigingen voor de Microsoft Graph-API vereist. Deze worden beschreven in de volgende secties. 

### <a name="manage-microsoft-graph-api-permissions"></a>Microsoft Graph API-machtigingen beheren

Bij het genereren van de activerings sleutel voor het Azure Stack Edge Pro-apparaat of het uitvoeren van bewerkingen waarvoor referenties zijn vereist, hebt u machtigingen nodig voor het Azure Active Directory van Graph API. De bewerkingen die referenties nodig hebben, kunnen zijn:

-  Een share maken met een gekoppeld opslag account.
-  Het maken van een gebruiker die toegang heeft tot de shares op het apparaat.

U moet toegang hebben `User` tot Active Directory Tenant als u dit moet kunnen doen `Read all directory objects` . U kunt geen gast gebruiker zijn omdat ze geen machtigingen hebben voor `Read all directory objects` . Als u een gast bent, worden de bewerkingen, zoals het genereren van een activerings sleutel, het maken van een share op uw Azure Stack Edge Pro-apparaat, het maken van een gebruiker, het configureren van de Edge Compute-functie, het opnieuw instellen van het wacht woord voor het apparaat, niet meer uitgevoerd.

Zie [Microsoft Graph permissions Reference](/graph/permissions-reference)(Engelstalig) voor meer informatie over het verlenen van toegang aan gebruikers om de API te Microsoft Graph.

### <a name="register-resource-providers"></a>Resourceprovider registreren

Als u een resource in azure wilt inrichten (in het Azure Resource Manager model), hebt u een resource provider nodig die het maken van die resource ondersteunt. Als u bijvoorbeeld een virtuele machine wilt inrichten, moet u een resource provider micro soft. Compute in het abonnement hebben.
 
Resourceproviders worden geregistreerd op het niveau van het abonnement. Nieuwe Azure-abonnementen worden standaard vooraf geregistreerd bij een lijst met veelgebruikte resourceproviders. De resource provider voor micro soft. DataBoxEdge is niet opgenomen in deze lijst.

U hoeft geen toegangs machtigingen voor het abonnements niveau te verlenen zodat gebruikers resources zoals ' micro soft. DataBoxEdge ' kunnen maken binnen hun resource groepen waarvoor ze eigendoms rechten hebben, op voor waarde dat de resource providers voor deze resources al zijn geregistreerd.

Voordat u probeert een resource te maken, moet u ervoor zorgen dat de resource provider is geregistreerd in het abonnement. Als de resource provider niet is geregistreerd, moet u ervoor zorgen dat de gebruiker die de nieuwe resource maakt, voldoende rechten heeft om de vereiste resource provider te registreren op het abonnements niveau. Als u dit nog niet hebt gedaan, ziet u de volgende fout:

*Het abonnement \<Subscription name> heeft geen machtigingen voor het registreren van de resource provider (s): micro soft. DataBoxEdge.*


Voer de volgende opdracht uit om een lijst met geregistreerde resource providers in het huidige abonnement op te halen:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Voor Azure Stack Edge Pro-apparaat `Microsoft.DataBoxEdge` moet worden geregistreerd. Als u de registratie wilt registreren `Microsoft.DataBoxEdge` , moet u de volgende opdracht uitvoeren:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Zie [fouten voor de registratie van de resource provider oplossen](../azure-resource-manager/templates/error-register-resource-provider.md)voor meer informatie over het registreren van een resource provider.

## <a name="manage-connectivity-mode"></a>Connectiviteits modus beheren

Naast de standaard modus volledig verbonden, kan het apparaat ook worden uitgevoerd in een gedeeltelijk verbonden modus of in een volledig niet-verbrekende weer gave. Elk van deze modi wordt hieronder beschreven:

- **Volledig verbonden** : dit is de normale standaard modus waarin het apparaat actief is. Het uploaden en downloaden van gegevens in de Cloud is in deze modus ingeschakeld. U kunt de Azure Portal of de lokale web-UI gebruiken om het apparaat te beheren.

- **Gedeeltelijk verbroken** : in deze modus kan het apparaat geen share gegevens uploaden of downloaden, maar kan ook worden beheerd via de Azure Portal.

    Deze modus wordt doorgaans gebruikt wanneer op een satelliet netwerk met een Data limiet het doel is om het gebruik van de netwerk bandbreedte te minimaliseren. Mini maal netwerk verbruik kan nog steeds optreden voor bewerkingen voor het controleren van apparaten.

- **Verbinding verbroken** : in deze modus is het apparaat volledig losgekoppeld van de Cloud en worden de Cloud-uploads en down loads uitgeschakeld. Het apparaat kan alleen worden beheerd via de lokale webgebruikersinterface.

    Deze modus wordt doorgaans gebruikt wanneer u uw apparaat offline wilt zetten.

Voer de volgende stappen uit om de modus apparaat te wijzigen:

1. Ga in de lokale web-UI van uw apparaat naar **configuratie > Cloud**.
2. Selecteer in de vervolg keuzelijst de modus waarin u het apparaat wilt bedienen. U kunt een **volledig verbonden**, **gedeeltelijk verbonden** en **volledig losgekoppelde** selectie selecteren. Als u het apparaat wilt uitvoeren in een gedeeltelijk niet-verbonden modus, schakelt u **Azure Portal-beheer** in.

 
## <a name="manage-power"></a>Energie beheren

U kunt uw fysieke apparaat afsluiten of opnieuw starten met de lokale webinterface. We raden u aan de shares vóór de herstart offline te zetten op de gegevensserver en vervolgens op het apparaat. Deze actie minimaliseert de kans op beschadigde gegevens.

1. Ga in de lokale web-UI naar **onderhoud > energie**.
2. Selecteer **Afsluiten** of **opnieuw opstarten** , afhankelijk van wat u wilt doen.

    ![Energie-instellingen](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Als u om bevestiging wordt gevraagd, selecteert u **Ja** om door te gaan.

> [!NOTE]
> Als u het fysieke apparaat afsluit, moet u de aan/uit-knop op het apparaat pushen om deze in te scha kelen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [beheren van shares](azure-stack-edge-manage-shares.md).