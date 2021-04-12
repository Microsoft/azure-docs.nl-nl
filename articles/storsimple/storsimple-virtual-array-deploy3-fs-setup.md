---
title: StorSimple virtuele array instellen als bestands server | Microsoft Docs
description: Met deze derde zelf studie in de implementatie van StorSimple Virtual Array kunt u een virtueel apparaat instellen als bestands server.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 244fdbf7cb723fe85e0987d176a13242f0bff064
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005925"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>StorSimple Virtual array implementeren: ingesteld als bestands server via Azure Portal
![Diagram met de stappen die nodig zijn voor het implementeren van een virtuele matrix. Het eerste deel van de derde stap is ingesteld als bestands server en is gemarkeerd.](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introductie

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

In dit artikel wordt beschreven hoe u een initiële installatie uitvoert, uw StorSimple-Bestands server registreert, de installatie van het apparaat voltooit en verbinding maakt met SMB-shares. Dit is het laatste artikel in de reeks implementatie zelfstudies die vereist zijn om uw virtuele array volledig te implementeren als een bestands server of een iSCSI-server.

Het installatie-en configuratie proces kan ongeveer 10 minuten duren. De informatie in dit artikel is alleen van toepassing op de implementatie van de virtuele StorSimple-matrix. Voor de implementatie van StorSimple 8000-serie apparaten gaat u naar: [Implementeer uw StorSimple 8000 Series-apparaat met update 2](./storsimple-8000-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Vereisten voor installatie
Voordat u de virtuele StorSimple-matrix configureert en instelt, moet u ervoor zorgen dat:

* U hebt een virtuele matrix ingericht en er verbinding mee gemaakt zoals beschreven in de [virtuele StorSimple-matrix inrichten in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) of [een virtuele StorSimple-matrix inrichten in VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* U hebt de service registratie sleutel van de StorSimple Apparaatbeheer-service die u hebt gemaakt voor het beheren van StorSimple virtuele matrices. Zie voor meer informatie [stap 2: de service registratie sleutel ophalen voor de](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) virtuele StorSimple-matrix.
* Als dit de tweede of volgende virtuele matrix is die u registreert met een bestaande StorSimple-Apparaatbeheer service, moet u de versleutelings sleutel voor service gegevens hebben. Deze sleutel is gegenereerd toen het eerste apparaat is geregistreerd bij deze service. Als u deze sleutel kwijtraakt, raadpleegt u de versleutelings [sleutel voor service gegevens ophalen](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) voor uw virtuele StorSimple-matrix.

## <a name="step-by-step-setup"></a>Stapsgewijze installatie
Gebruik de volgende stapsgewijze instructies voor het instellen en configureren van uw virtuele StorSimple-matrix.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Stap 1: de installatie van de lokale webgebruikersinterface volt ooien en uw apparaat registreren
#### <a name="to-complete-the-setup-and-register-the-device"></a>De installatie volt ooien en het apparaat registreren
1. Open een browser venster en maak verbinding met de lokale web-UI. Type:
   
   `https://<ip-address of network interface>`
   
   Gebruik de verbindings-URL die u in de vorige stap hebt genoteerd. Er wordt een fout bericht weer gegeven met de melding dat er een probleem is met het beveiligings certificaat van de website. Klik op **door gaan naar deze webpagina**.
   
   ![Scherm opname van een browser venster met een probleem met het beveiligings certificaat. Er zijn twee koppelingen zichtbaar: één voor de website en één voor de start pagina van de gebruiker.](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Meld u aan bij de Web-UI van uw virtuele matrix als **StorSimpleAdmin**. Voer het beheerders wachtwoord voor het apparaat in dat u hebt gewijzigd in stap 3: de virtuele matrix starten in [een StorSimple-virtuele matrix inrichten in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) of in [een StorSimple-virtuele matrix inrichten in VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![Scherm afbeelding van de StorSimple-aanmeldings pagina. De StorSimpleAdmin-gebruikers naam is zichtbaar en het vak wacht woord is gevuld met onbepaalde tekens.](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. U wordt naar de **Start** pagina geleid. Op deze pagina worden de verschillende instellingen beschreven die nodig zijn om de virtuele matrix te configureren en te registreren bij de StorSimple-Apparaatbeheer service. De **netwerk instellingen**, **webproxy-instellingen** en **tijd instellingen** zijn optioneel. De enige vereiste instellingen zijn **Apparaatinstellingen** en **Cloud instellingen**.
   
   ![Scherm opname van de start pagina. Tekst geeft aan dat het apparaat niet is geconfigureerd. Koppelingen naar verschillende soorten instellingen zijn zichtbaar.](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. Op de pagina **netwerk instellingen** onder **netwerk interfaces** wordt gegevens 0 automatisch voor u geconfigureerd. Elke netwerk interface is standaard ingesteld om automatisch een IP-adres (DHCP) op te halen. Daarom worden automatisch een IP-adres, subnet en gateway toegewezen (voor zowel IPv4 als IPv6).
   
   ![Scherm afbeelding van de pagina netwerk instellingen met de IP-adressen die zijn geconfigureerd voor de verschillende versies van de Internet Protocol.](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Als u meer dan één netwerk interface hebt toegevoegd tijdens het inrichten van het apparaat, kunt u deze hier configureren. Opmerking: u kunt uw netwerk interface alleen configureren als IPv4 of als IPv4 en IPv6. Alleen IPv6-configuraties worden niet ondersteund.
5. DNS-servers zijn vereist omdat ze worden gebruikt wanneer het apparaat probeert te communiceren met uw Cloud Storage-service providers of als uw apparaat moet worden omgezet op naam wanneer het is geconfigureerd als een bestands server. Op de pagina **netwerk instellingen** van de **DNS-servers**:
   
   1. Een primaire en secundaire DNS-server worden automatisch geconfigureerd. Als u statische IP-adressen wilt configureren, kunt u DNS-servers opgeven. Voor maximale Beschik baarheid kunt u het beste een primaire en secundaire DNS-server configureren.
   2. Klik op **Toep assen** om de netwerk instellingen toe te passen en te valideren.
6. Op de pagina **Apparaatinstellingen** :
   
   1. Wijs een unieke **naam** toe aan uw apparaat. Deze naam mag 1-15 tekens lang zijn en mag letter, cijfers en afbreek streepjes bevatten.
   2. Klik op het pictogram **Bestands server** :::image type="icon" source="./media/storsimple-virtual-array-deploy3-fs-setup/image6.png"::: voor het **type** apparaat dat u maakt. Met een bestands server kunt u gedeelde mappen maken.
   3. Als uw apparaat een bestands server is, moet u het apparaat toevoegen aan een domein. Voer een **domein naam** in.
   4. Klik op **Toepassen**.
7. Er wordt een dialoog venster weer gegeven. Voer uw domein referenties in de opgegeven indeling in. Klik op het vinkje. De domein referenties worden geverifieerd. Er wordt een fout bericht weer gegeven als de referenties onjuist zijn.
   
   ![Scherm opname met een dialoog venster waarin de gebruikers naam en het wacht woord zijn ingevuld.](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Klik op **Toepassen**. Hiermee worden de apparaatinstellingen toegepast en gevalideerd.
   
   ![Scherm afbeelding van de pagina Apparaatinstellingen. De naam van het apparaat en de domein naam zijn ingevuld.](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Zorg ervoor dat uw virtuele array zich in een eigen organisatie-eenheid (OE) bevindt voor Active Directory en dat er geen groeps beleidsobjecten (GPO) op zijn toegepast of worden overgenomen. Met groeps beleid kunnen toepassingen zoals antivirus software op de virtuele StorSimple-matrix worden geïnstalleerd. Het installeren van aanvullende software wordt niet ondersteund en kan leiden tot gegevens beschadiging. 
   > 
   > 
9. (Optioneel) Configureer uw webproxyserver. De configuratie van uw webproxy is weliswaar optioneel, maar houd er rekening mee dat als u een webproxy gebruikt, deze alleen hier kan worden geconfigureerd.
   
   ![Scherm afbeelding van de pagina Web Proxy-instellingen. De optie webproxy inschakelen is uitgeschakeld en de verificatie is ingesteld op geen. Er zijn geen andere waarden ingesteld.](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   Op de pagina **webproxy** :
   
   1. Geef de **URL van de webproxy** op in deze indeling: *http:// &lt; host-IP-adres of FQDN &gt; :P sorteren-nummer*. Let op: HTTPS-Url's worden niet ondersteund.
   2. Geef **verificatie** op als **basis** of **geen**.
   3. Als u verificatie gebruikt, moet u ook een **gebruikers naam** en **wacht woord** opgeven.
   4. Klik op **Toepassen**. Hiermee worden de geconfigureerde web proxy-instellingen gevalideerd en toegepast.
10. (Optioneel) de tijd instellingen voor uw apparaat configureren, zoals de tijd zone en de primaire en secundaire NTP-servers. NTP-servers zijn vereist, omdat uw apparaat de tijd moet synchroniseren voor verificatie met uw cloudserviceproviders.
    
    ![Scherm afbeelding van de pagina tijd instellingen. De tijd zone en de primaire N T P-server zijn ingevuld. De secundaire N T P-server is leeg.](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    Op de pagina **tijd instellingen** :
    
    1. Selecteer in de vervolg keuzelijst de **tijd zone** op basis van de geografische locatie waar het apparaat wordt geïmplementeerd. De standaard tijdzone voor uw apparaat is PST. Het apparaat zal deze tijdzone gebruiken voor alle geplande bewerkingen.
    2. Geef een **primaire NTP-server** voor uw apparaat op of accepteer de standaard waarde van time.Windows.com. Zorg ervoor dat in uw netwerk NTP-verkeer kan worden doorgegeven van uw datacenter naar internet.
    3. Geef eventueel een **secundaire NTP-server** op voor uw apparaat.
    4. Klik op **Toepassen**. Hiermee worden de geconfigureerde tijd instellingen gevalideerd en toegepast.
11. Configureer de Cloud instellingen voor uw apparaat. In deze stap voltooit u de configuratie van het lokale apparaat en registreert u het apparaat met uw StorSimple-Apparaatbeheer service.
    
    1. Voer de **service registratie sleutel** in die u in [stap 2 hebt ontvangen: de service registratie sleutel voor de](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) virtuele StorSimple-matrix ophalen.
    2. Als dit de eerste keer is dat u het apparaat registreert bij deze service, wordt de **versleutelings sleutel voor service gegevens** weer gegeven. Kopieer deze sleutel en bewaar deze op een veilige plaats. Deze sleutel is vereist bij de service registratie sleutel om extra apparaten te registreren bij de StorSimple-Apparaatbeheer service. 
       
       Als dit niet het eerste apparaat is dat u bij deze service registreert, moet u de versleutelings sleutel voor de service gegevens opgeven. Raadpleeg voor meer informatie de [versleutelings sleutel voor service gegevens](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) ophalen op uw lokale webinterface.
    3. Klik op **Registreren**. Hiermee wordt het apparaat opnieuw opgestart. Mogelijk moet u 2-3 minuten wachten voordat het apparaat is geregistreerd. Nadat het apparaat opnieuw is opgestart, wordt u naar de aanmeldings pagina geleid.
       
       ![Scherm afbeelding van de pagina met Cloud instellingen. De vakken registratie sleutel en versleutelings sleutel zijn ingevuld, maar de waarden worden opnieuw geredigeerd.](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Ga terug naar Azure Portal. Ga naar **alle resources**, zoek naar uw StorSimple-Apparaatbeheer service.
    
    ![Scherm afbeelding van de pagina alle resources van de Azure Portal. Een Apparaatbeheer-service is gemarkeerd.](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. Selecteer uw StorSimple-Apparaatbeheer service in de gefilterde lijst en navigeer vervolgens naar **beheer > apparaten**. Controleer op de Blade **apparaten** of het apparaat is verbonden met de service en of de status **gereed is om in te stellen**.
    
    ![Implementeren](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Stap 2: het apparaat configureren als bestands server
Voer de volgende stappen uit in de [Azure Portal](https://portal.azure.com/) om de vereiste apparaatinstellingen te volt ooien.

#### <a name="to-configure-the-device-as-file-server"></a>Het apparaat als bestands server configureren
1. Ga naar de StorSimple-Apparaatbeheer service en ga vervolgens naar  **beheer > apparaten**. Selecteer op de Blade **apparaten** het apparaat dat u zojuist hebt gemaakt. Dit apparaat wordt weer gegeven als **gereed voor configuratie**.
   
   ![Een bestands server configureren](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Klik op het apparaat. er wordt een banner bericht weer gegeven dat aangeeft dat het apparaat gereed is voor installatie.
   
    ![Een bestands Server 2 configureren](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Klik op **configureren** op de opdracht balk. Hiermee opent u de Blade **configureren** . Ga als volgt te werk op de Blade **configureren** :
   
   1. De naam van de bestands server wordt automatisch ingevuld.
    
   2. Zorg ervoor dat de versleuteling van de Cloud opslag is ingesteld op **ingeschakeld**. Hiermee worden alle gegevens die naar de cloud worden verzonden, versleuteld. 
    
   3. Er wordt een 256-bits AES-sleutel gebruikt in combi natie met de door de gebruiker gedefinieerde sleutel voor versleuteling. Geef een 32-teken sleutel op en voer de sleutel opnieuw in om deze te bevestigen. Noteer de sleutel in een app voor sleutel beheer voor toekomstig naslag doeleinden.
    
   4. Klik op **vereiste instellingen configureren** om referenties voor het opslag account op te geven die u met uw apparaat wilt gebruiken. Klik op **nieuwe toevoegen** als er geen referenties voor het opslag account zijn geconfigureerd. **Zorg ervoor dat het opslag account dat u gebruikt, blok-blobs ondersteunt. Pagina-blobs worden niet ondersteund.** Meer informatie over het blok keren van [blobs en pagina-blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
      ![Een bestands Server 3 configureren](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. Ga als volgt te werk op de Blade **een opslag account referenties toevoegen** : 

    1. Kies het huidige abonnement als het opslag account zich in hetzelfde abonnement bevindt als de service. Andere opgeven is het opslag account valt buiten het service abonnement. 
    
    2. Kies een bestaand opslag account in de vervolg keuzelijst. 
    
    3. De locatie wordt automatisch ingevuld op basis van het opgegeven opslag account. 
    
    4. Schakel TLS in om een beveiligd netwerk communicatie kanaal tussen het apparaat en de cloud te waarborgen.
    
    5. Klik op **toevoegen** om deze opslag account referentie toe te voegen. 
   
        ![Een bestands Server 4 configureren](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Zodra de referenties van het opslag account zijn gemaakt, wordt de Blade **configureren** bijgewerkt om de opgegeven referenties voor het opslag account weer te geven. Klik op **Configureren**
   
   ![Een bestands server configureren 5](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   U ziet dat een bestands server wordt gemaakt. Zodra de bestands server is gemaakt, wordt u hiervan op de hoogte gebracht.
   
   ![Een bestands server 5b configureren](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   De apparaatstatus wordt ook gewijzigd in **online**.
   
   ![Een file server 5c configureren](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   U kunt door gaan met het toevoegen van een share.

## <a name="step-3-add-a-share"></a>Stap 3: een share toevoegen
Voer in de [Azure-portal](https://portal.azure.com/) de volgende stappen uit om een share te maken.

#### <a name="to-create-a-share"></a>Een share maken
1. Selecteer het bestands server apparaat dat u in de vorige stap hebt geconfigureerd en klik op **...** (of met de rechter muisknop). Selecteer in het context menu de optie **share toevoegen**. U kunt ook op **+ share toevoegen** klikken op de opdracht balk van het apparaat.
   
   ![Een share toevoegen](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Geef de volgende share-instellingen op:

   1. Een unieke naam voor uw share. De naam moet een teken reeks zijn die tussen de 3 en 127 tekens bevat.
    
   2. Een optionele **Beschrijving** voor de share. De beschrijving helpt bij het identificeren van de eigen aren van shares.
    
   3. Een **type** voor de share. Het type kan worden **gelaagd** of **lokaal vastgemaakt**, waarbij de standaard instelling wordt ingesteld op laag. Voor werk belastingen waarvoor lokale garanties, lage latenties en hogere prestaties zijn vereist, selecteert u een **lokaal vastgemaakte** share. Voor alle andere gegevens selecteert u een **gelaagde** share.
      Een lokaal vastgemaakte share is dik ingericht en zorgt ervoor dat de primaire gegevens op de share lokaal op het apparaat blijven en niet overvloeit naar de Cloud. Een gelaagde share aan de andere kant is dun ingericht. Wanneer u een gelaagde share maakt, wordt 10% van de ruimte ingericht op de lokale laag en wordt 90% van de ruimte ingericht in de Cloud. Als u bijvoorbeeld een volume van 1 TB hebt ingericht, zou 100 GB zich in de lokale ruimte bevinden en wordt 900 GB in de Cloud gebruikt wanneer de gegevens lagen. Dit betekent dat als u alle lokale ruimte op het apparaat uitvoert, u geen gelaagde share kunt inrichten.
   
   4. Wijs in het veld **standaard volledige machtigingen instellen op** de machtigingen toe aan de gebruiker of de groep die toegang tot deze share heeft. Geef de naam op van de gebruiker of de gebruikers groep in de *john \@ contoso.com* -indeling. We raden u aan om een gebruikers groep (in plaats van één gebruiker) te gebruiken om beheerders bevoegdheden voor toegang tot deze shares toe te staan. Als u de bevoegdheden hier hebt toegewezen, kunt u Verkenner gebruiken om de bevoegdheden te wijzigen.
   
   5. Klik op **toevoegen** om de share te maken. 
    
       ![Een share toevoegen 1](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       U ontvangt een melding als wordt begonnen met het maken van de share.
   
       ![Een share toevoegen 2](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      Nadat de share is gemaakt met de opgegeven instellingen, wordt de Blade **shares** bijgewerkt met de nieuwe share. Bewaking en back-up zijn standaard ingeschakeld voor de share.
   
      ![Een share 3 toevoegen](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Stap 4: verbinding maken met de share
U moet nu verbinding maken met een of meer shares die u in de vorige stap hebt gemaakt. Voer de volgende stappen uit op de Windows Server-host die is verbonden met uw virtuele StorSimple-matrix.

#### <a name="to-connect-to-the-share"></a>Verbinding maken met de share
1. Druk op :::image type="icon" source="./media/storsimple-virtual-array-deploy3-fs-setup/image22.png"::: + R. Geef in het venster uitvoeren de *&#92;&#92;&lt; bestands &gt; Server naam* op als het pad, waarbij de naam van de *Bestands server* wordt vervangen door de apparaatnaam die u aan de bestands server hebt toegewezen. Klik op **OK**.
   
   ![Scherm afbeelding van het dialoog venster uitvoeren. Het vak openen is ingevuld met een pad dat leidt naar de bestands server.](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Hiermee opent u bestanden Verkenner. U moet nu de shares kunnen zien die u als mappen hebt gemaakt. Selecteer een share (map) en dubbelklik erop om de inhoud te bekijken.
   
   ![Scherm opname van bestanden Verkenner. Er zijn verschillende mappen zichtbaar die de zojuist gemaakte shares vertegenwoordigen.](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. U kunt nu bestanden toevoegen aan deze shares en een back-up maken.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het gebruik van de lokale web-UI voor [het beheren van uw StorSimple-virtuele matrix](storsimple-ova-web-ui-admin.md).