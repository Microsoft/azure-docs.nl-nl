---
title: Door de gebruiker gedefinieerde U-SQL-Opera tors ontwikkelen-Azure Data Lake Analytics
description: Meer informatie over het ontwikkelen van door de gebruiker gedefinieerde Opera tors die moeten worden gebruikt en opnieuw gebruikt in Azure Data Lake Analytics taken.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: 11efdb727bacadb674fb49374ef1c70fcc788ecc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219986"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Door de gebruiker gedefinieerde U-SQL-Opera tors ontwikkelen (Udo's)
In dit artikel wordt beschreven hoe u door de gebruiker gedefinieerde Opera tors ontwikkelt om gegevens in een U-SQL-taak te verwerken.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Een door de gebruiker gedefinieerde operator definiëren en gebruiken in U-SQL

### <a name="to-create-and-submit-a-u-sql-job"></a>Een U-SQL-taak maken en verzenden

1. Selecteer in Visual Studio **File > New > project > U-SQL project**.
2. Klik op **OK**. Visual Studio maakt een oplossing met een script. usql-bestand.
3. Vouw in **Solution Explorer** script. usql uit en dubbel klik vervolgens op **script. usql. cs**.
4. Plak de volgende code in het bestand:

   ```usql
   using Microsoft.Analytics.Interfaces;
   using System.Collections.Generic;
   namespace USQL_UDO
   {
       public class CountryName : IProcessor
       {
           private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
           {
               {
                   "Deutschland", "Germany"
               },
               {
                   "Suisse", "Switzerland"
               },
               {
                   "UK", "United Kingdom"
               },
               {
                   "USA", "United States of America"
               },
               {
                   "中国", "PR China"
               }
           };
           public override IRow Process(IRow input, IUpdatableRow output)
           {
               string UserID = input.Get<string>("UserID");
               string Name = input.Get<string>("Name");
               string Address = input.Get<string>("Address");
               string City = input.Get<string>("City");
               string State = input.Get<string>("State");
               string PostalCode = input.Get<string>("PostalCode");
               string Country = input.Get<string>("Country");
               string Phone = input.Get<string>("Phone");
               if (CountryTranslation.Keys.Contains(Country))
               {
                   Country = CountryTranslation[Country];
               }
               output.Set<string>(0, UserID);
               output.Set<string>(1, Name);
               output.Set<string>(2, Address);
               output.Set<string>(3, City);
               output.Set<string>(4, State);
               output.Set<string>(5, PostalCode);
               output.Set<string>(6, Country);
               output.Set<string>(7, Phone);
               return output.AsReadOnly();
           }
       }
   }
   ```

5. Open **script. usql** en plak het volgende U-SQL-script:

   ```usql
   @drivers =
       EXTRACT UserID      string,
               Name        string,
               Address     string,
               City        string,
               State       string,
               PostalCode  string,
               Country     string,
               Phone       string
       FROM "/Samples/Data/AmbulanceData/Drivers.txt"
       USING Extractors.Tsv(Encoding.Unicode);

   @drivers_CountryName =
       PROCESS @drivers
       PRODUCE UserID string,
               Name string,
               Address string,
               City string,
               State string,
               PostalCode string,
               Country string,
               Phone string
       USING new USQL_UDO.CountryName();

   OUTPUT @drivers_CountryName
       TO "/Samples/Outputs/Drivers.csv"
       USING Outputters.Csv(Encoding.Unicode);
   ```

6. Geef het Data Lake Analytics-account, de database en het schema op.
7. Ga naar **Solution Explorer**, klik met de rechtermuisknop op **Script.usql** en klik vervolgens op **Build Script**.
8. Ga naar **Solution Explorer**, klik met de rechtermuisknop op **Script.usql** en klik vervolgens op **Submit Script**.
9. Als u nog geen verbinding hebt gemaakt met uw Azure-abonnement, wordt u gevraagd om uw Azure-account referenties in te voeren.
10. Klik op **Submit** De inzendings resultaten en taak koppeling zijn beschikbaar in het resultaten venster wanneer de verzen ding is voltooid.
11. Klik op de knop **vernieuwen** om de nieuwste taak status weer te geven en het scherm te vernieuwen.

### <a name="to-see-the-output"></a>Als u de uitvoer wilt bekijken

1. Vouw in **Server Explorer** **Azure** uit, vouw **Data Lake Analytics** uit, vouw uw data Lake Analytics account uit, vouw **opslag accounts** uit, klik met de rechter muisknop op de standaard opslag en klik vervolgens op **Explorer**.

2. Vouw voor beelden uit, vouw uitvoer uit en dubbel klik vervolgens op **Drivers.csv**.

## <a name="next-steps"></a>Volgende stappen

* [U-SQL-expressies uitbreiden met gebruikers code](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Data Lake-Hulpprogram Ma's voor Visual Studio gebruiken voor het ontwikkelen van U-SQL-toepassingen](data-lake-analytics-data-lake-tools-get-started.md)
