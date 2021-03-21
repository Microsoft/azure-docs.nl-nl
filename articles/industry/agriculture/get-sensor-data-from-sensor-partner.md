---
title: Sensor gegevens ophalen van de partners
description: In dit artikel wordt beschreven hoe u sensor gegevens van partners kunt ophalen.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 474b6f8356724546105d902a15427f483f656317
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182771"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Sensor gegevens ophalen van sensor partners

Met Azure FarmBeats kunt u streaming-gegevens van uw IoT-apparaten en Sens oren naar Datahub brengen. Op dit moment worden de volgende partners van sensor apparaten ondersteund.

  ![FarmBeats-partners](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

Het integreren van apparaatgegevens met Azure FarmBeats helpt u bij het ophalen van de basis gegevens van de IoT-Sens oren die in uw farm zijn geïmplementeerd, naar de data hub. De gegevens, zodra beschikbaar, kunnen worden gevisualiseerd via de FarmBeats-Accelerator. De gegevens kunnen worden gebruikt voor gegevens fusie en het samen stellen van machine learning/kunst matige intelligentie (ML/AI) met behulp van FarmBeats.

Controleer het volgende om te beginnen met het streamen van sensor gegevens:

-  U hebt FarmBeats geïnstalleerd in azure Marketplace.
-  U hebt besloten over de Sens oren en apparaten die u wilt installeren op uw farm.
-  Als u van plan bent om gebruik te maken van bodem vocht Sens oren, gebruikt u de plaatsings kaart FarmBeats bodem vocht sensor om een aanbeveling te krijgen over het aantal Sens oren en waar u deze precies moet plaatsen. Zie [genereren Maps](generate-maps-in-azure-farmbeats.md)voor meer informatie.
- U koopt en implementeert apparaten of Sens oren van uw apparaat-partner op uw farm. Zorg ervoor dat u toegang hebt tot de sensor gegevens via de oplossing van uw apparaat partners.

## <a name="enable-device-integration-with-farmbeats"></a>Integratie van apparaten met FarmBeats inschakelen

Nadat u het streamen van sensor gegevens hebt gestart, kunt u beginnen met het proces van het ophalen van de gegevens in uw FarmBeats-systeem. Geef de volgende informatie op voor de provider van uw apparaat om de integratie met FarmBeats in te scha kelen:

 - API-eindpunt
 - Tenant-id
 - Client-id
 - Clientgeheim
 - EventHub connection string

Volg de onderstaande stappen om de bovenstaande gegevens te genereren:

> [!NOTE]
> Deze stappen moeten worden uitgevoerd op Azure om toegang te krijgen tot het Azure-abonnement waar FarmBeats wordt geïmplementeerd.

1. Meld u aan bij https://portal.azure.com/.

2. **Als u FarmBeats-versie 1.2.7 of hoger hebt, slaat u stap a, b en c over en gaat u naar stap 3.** U kunt de FarmBeats-versie controleren door het pictogram **instellingen** te selecteren in de rechter bovenhoek van de FarmBeats-gebruikers interface.

      a.  Ga naar   >  **app-registraties** voor Azure Active Directory

      b. Selecteer de **app-registratie** die is gemaakt als onderdeel van uw FarmBeats-implementatie. Deze heeft dezelfde naam als uw FarmBeats-datahub.

      c. Selecteer **een API beschikbaar** maken > Selecteer **een client toepassing toevoegen** en voer **04B07795-8ddb-461A-bbee-02f9e1bf7b46** in en controleer de **Scope voor autoriseren**. Hiermee krijgt u toegang tot de Azure CLI (Cloud Shell) om de onderstaande stappen uit te voeren:

3. Open Cloud Shell. Deze optie is beschikbaar op de werk balk in de rechter bovenhoek van de Azure Portal.

    ![Azure Portal werk balk](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. Zorg ervoor dat de omgeving is ingesteld op **Power shell**. Standaard is deze ingesteld op bash.

    ![Power shell-werkbalk instelling](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. Ga naar de basismap.

    ```azurepowershell-interactive
    cd
    ```

6. Voer de volgende opdracht uit. Hiermee wordt een geverifieerde account verbonden voor Azure AD-aanvragen

    ```azurepowershell-interactive
    Connect-AzureAD
    ```

7. Voer de volgende opdracht uit. Hiermee wordt een script gedownload naar uw basismap.

    ```azurepowershell-interactive

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

8. Voer het volgende script uit. Het script vraagt om de Tenant-id, die kan worden verkregen van **Azure Active Directory**  >  **overzichts** pagina.

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1

    ```

> [!NOTE]
> 1. De naam van het Datahub-API-eind punt moet in kleine letters worden beschouwd.
> 2. Als u de naam-URL van de farmbeats-website voor Datahub API-eind punt kopieert, moet u ervoor zorgen dat er geen afsluitende slash (/) is.

9. Volg de instructies op het scherm voor het vastleggen van de waarden voor het **API-eind punt**, **Tenant-ID**, **client-id**, **client geheim** en **EventHub-verbindings reeks**.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Apparaatgegevens integreren met behulp van de gegenereerde referenties

U hebt nu de volgende informatie die is gegenereerd in de vorige sectie.
 - API-eindpunt
 - EventHub connection string
 - Client-id
 - Clientgeheim
 - Tenant-id

U moet dit opgeven voor de partner van uw apparaat voor het koppelen van FarmBeats. Ga naar de partner portal van het apparaat om hetzelfde te doen. Als u bijvoorbeeld apparaten van Davis-instrumenten gebruikt, gaat u naar de bijbehorende pagina's, zoals hieronder wordt beschreven:

1. [Davis-instrumenten](https://weatherlink.github.io/azure-farmbeats/setup)

2. [Teralytic](https://app.teralytic.com/)

3. [Pessl-instrumenten](https://ng.fieldclimate.com/user-api-services)

De provider van het apparaat bevestigt een geslaagde integratie. Na de bevestiging kunt u alle apparaten en Sens oren in azure FarmBeats bekijken.

## <a name="view-devices-and-sensors"></a>Apparaten en Sens oren weer geven

Gebruik de volgende sectie om de apparaten en Sens oren voor uw farm weer te geven.

### <a name="view-devices"></a>Apparaten weergeven

FarmBeats ondersteunt momenteel de volgende apparaten:

- **Knoop punt**: een apparaat waaraan een of meer Sens oren zijn gekoppeld.
- **Gateway**: een apparaat waaraan een of meer knoop punten zijn gekoppeld.

Volg deze stappen:

1. Op de start pagina selecteert u **apparaten** in het menu.
  Op de pagina **apparaten** wordt het type apparaat, het model, de status, de farm waarin het is geplaatst en de datum waarop de meta gegevens voor het laatst zijn bijgewerkt weer gegeven. De kolom Farm is standaard ingesteld op *Null*. U kunt ervoor kiezen om een apparaat toe te wijzen aan een farm. Zie [apparaten toewijzen](#assign-devices)voor meer informatie.
2. Selecteer het apparaat om de apparaateigenschappen, telemetrie en onderliggende apparaten weer te geven die zijn verbonden met het apparaat.

    ![De pagina Apparaten](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Sens oren weer geven

Volg deze stappen:

1. Selecteer op de start pagina **Sens oren** in het menu.
  Op de pagina **Sens oren** worden details weer gegeven over het type sensor, de farm waarmee deze is verbonden, het bovenliggende apparaat, de poort naam, het poort type en de laatst bijgewerkte status.
2. Selecteer de sensor om de sensor eigenschappen, actieve waarschuwingen en telemetrie van de sensor weer te geven.

    ![Pagina Sens oren](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Apparaten toewijzen

Nadat de sensor gegevens in zijn stromen, kunt u deze toewijzen aan de farm waar u de Sens oren hebt geïmplementeerd.

1. Selecteer op de start pagina de optie **Farms** in het menu. De pagina **Farms** lijst wordt weer gegeven.
2. Selecteer de farm waaraan u het apparaat wilt toewijzen en selecteer **apparaten toevoegen**.
3. Het venster **apparaten toevoegen** wordt weer gegeven. Selecteer het apparaat dat u wilt toewijzen aan de farm.

    ![Venster apparaten toevoegen](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selecteer **apparaten toevoegen**. U kunt ook naar het menu **apparaten** gaan, de apparaten selecteren die u wilt toewijzen aan een farm en **apparaten koppelen** selecteren.
5. Selecteer in het venster **apparaten koppelen** de farm in de vervolg keuzelijst en selecteer **Toep assen op alles** om de farm aan alle geselecteerde apparaten te koppelen.

    ![Venster Apparaten koppelen](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Als u elk apparaat aan een andere farm wilt koppelen, selecteert u de vervolg keuze pijl in de kolom **toewijzen aan Farm** en selecteert u een farm voor elke rij van het apparaat.

7. Selecteer **toewijzen** om de apparaattoewijzing te volt ooien.

### <a name="visualize-sensor-data"></a>Sensorgegevens visualiseren

Volg deze stappen:

1. Selecteer op de start pagina **Farms** in het menu om de pagina **Farms** weer te geven.
2. Selecteer de **Farm** waarvan u de sensor gegevens wilt bekijken.
3. Op het dash board van de **Farm** kunt u telemetrie-gegevens weer geven. U kunt live-telemetrie weer geven of een **aangepast bereik** gebruiken om een bepaald datum bereik weer te geven.

    ![Farm dashboard](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Een sensor verwijderen

Volg deze stappen:

1. Op de start pagina selecteert u **Sens oren** in het menu om de pagina **Sens oren** weer te geven.
2. Selecteer het apparaat dat u wilt verwijderen en selecteer **verwijderen** in het bevestigings venster.

    ![Scherm afbeelding die de pagina Sens oren verwijderen en de knop verwijderen markeert.](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Er wordt een bevestigings bericht weer gegeven dat de sensor is verwijderd.

## <a name="delete-devices"></a>Apparaten verwijderen

Volg deze stappen:

1. Op de start pagina selecteert u **apparaten** in het menu om de pagina **apparaten** weer te geven.
2. Selecteer het apparaat dat u wilt verwijderen en selecteer **verwijderen** in het bevestigings venster.

    ![De knop Verwijderen](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Volgende stappen

U hebt nu sensor gegevens die in uw Azure FarmBeats-exemplaar worden geplaatst. Nu leert u hoe u [kaarten](generate-maps-in-azure-farmbeats.md#generate-maps) voor uw Farms kunt genereren.
