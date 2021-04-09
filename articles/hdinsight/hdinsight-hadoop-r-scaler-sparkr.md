---
title: Schaalr en Spark gebruiken met Azure HDInsight
description: Gebruik Scaleer en Spark voor het bewerken van gegevens en het ontwikkelen van modellen met ML-Services in azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/26/2019
ms.openlocfilehash: 95fcca289b0776cc19464b13eb7d243ca4f8d5ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945512"
---
# <a name="combine-scaler-and-sparkr-in-hdinsight"></a>Schaalr en Spark in HDInsight combi neren

In dit document wordt beschreven hoe u vertragingen in de vlucht kunt voors pellen met behulp van een **schaalbaar** logistiek-regressie model. In het voor beeld worden vlucht vertraging en weers gegevens gebruikt, gekoppeld met behulp van **Spark**.

Hoewel beide pakketten worden uitgevoerd op de Spark-uitvoerings engine van Apache Hadoop, worden ze geblokkeerd voor het delen van gegevens in het geheugen omdat ze elk hun eigen respectievelijk Spark-sessies vereisen. Totdat dit probleem is opgelost in een toekomstige versie van ML Server, is de tijdelijke oplossing het onderhouden van niet-overlappende Spark-sessies en het uitwisselen van gegevens via tussenliggende bestanden. In deze instructies ziet u dat deze vereisten eenvoudig zijn te verzorgen.

Dit voor beeld is in eerste instantie gedeeld in een gesprek op Strata 2016 door Mario Inchiosa en Roni Burd. U vindt dit praten op het [bouwen van een schaalbaar data Science-platform met R](https://channel9.msdn.com/blogs/Cloud-and-Enterprise-Premium/Building-A-Scalable-Data-Science-Platform-with-R-and-Hadoop).

De code is oorspronkelijk geschreven voor ML Server die worden uitgevoerd op Spark in een HDInsight-cluster in Azure. Maar het combi neren van het gebruik van Spark en schalen in één script is ook geldig in de context van on-premises omgevingen.

Bij de stappen in dit document wordt ervan uitgegaan dat u een tussenliggend niveau van de kennis van R hebt en de [scaleer](/machine-learning-server/r/concept-what-is-revoscaler) -bibliotheek van ml server. U gaat naar [Spark](https://spark.apache.org/docs/2.1.0/sparkr.html) , terwijl u dit scenario doorloopt.

## <a name="the-airline-and-weather-datasets"></a>De luchtvaart maatschappij en weer gegevens sets

De vlucht gegevens zijn beschikbaar via de [archieven van de Amerikaanse overheid](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236). Het is ook beschikbaar als post code van [AirOnTimeCSV.zip](https://packages.revolutionanalytics.com/datasets/AirOnTime87to12/AirOnTimeCSV.zip).

De weer gegevens kunnen worden gedownload als zip-bestanden in onbewerkte vorm, per maand, vanuit de [nationale Oceanic-en atmosferische beheer opslagplaats](https://www.ncdc.noaa.gov/orders/qclcd/). Voor dit voor beeld downloadt u de gegevens voor mei 2007 – december 2012. Gebruik de gegevens bestanden en het `YYYYMMMstation.txt` bestand in elk uur in elk van de zips.

## <a name="setting-up-the-spark-environment"></a>De Spark-omgeving instellen

Gebruik de volgende code om de Spark-omgeving in te stellen:

```
workDir        <- '~'  
myNameNode     <- 'default' 
myPort         <- 0
inputDataDir   <- 'wasb://hdfs@myAzureAccount.blob.core.windows.net'
hdfsFS         <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# create a persistent Spark session to reduce startup times 
#   (remember to stop it later!)
 
sparkCC        <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort, persistentRun=TRUE)

# create working directories 

rxHadoopMakeDir('/user')
rxHadoopMakeDir('user/RevoShare')
rxHadoopMakeDir('user/RevoShare/remoteuser')

(dataDir <- '/share')
rxHadoopMakeDir(dataDir)
rxHadoopListFiles(dataDir) 

setwd(workDir)
getwd()

# version of rxRoc that runs in a local CC 
rxRoc <- function(...){
  rxSetComputeContext(RxLocalSeq())
  roc <- RevoScaleR::rxRoc(...)
  rxSetComputeContext(sparkCC)
  return(roc)
}

logmsg <- function(msg) { cat(format(Sys.time(), "%Y-%m-%d %H:%M:%S"),':',msg,'\n') } 
t0 <- proc.time() 

#..start 

logmsg('Start') 
(trackers <- system("mapred job -list-active-trackers", intern = TRUE))
logmsg(paste('Number of task nodes=',length(trackers)))
```

Voeg vervolgens toe `Spark_Home` aan het zoekpad voor R-pakketten. Als u het toevoegt aan het zoekpad, kunt u Spark gebruiken en een Spark-sessie initialiseren:

```
#..setup for use of SparkR  

logmsg('Initialize SparkR') 

.libPaths(c(file.path(Sys.getenv("SPARK_HOME"), "R", "lib"), .libPaths()))
library(SparkR)

sparkEnvir <- list(spark.executor.instances = '10',
                   spark.yarn.executor.memoryOverhead = '8000')

sc <- sparkR.init(
  sparkEnvir = sparkEnvir,
  sparkPackages = "com.databricks:spark-csv_2.10:1.3.0"
)

sqlContext <- sparkRSQL.init(sc)
```

## <a name="preparing-the-weather-data"></a>De weer gegevens voorbereiden

Om de weer gegevens voor te bereiden, moet u deze deel uitmaken van de kolommen die nodig zijn voor model lering: 

- Visibility
- "DryBulbCelsius"
- "DewPointCelsius"
- "RelativeHumidity"
- "WindSpeed"
- "Altimeter"

Voeg vervolgens een luchthaven code toe die is gekoppeld aan het weer station en converteer de metingen van lokale tijd naar UTC.

Maak eerst een bestand om de informatie over het weer station (WBAN) toe te wijzen aan een luchthaven code. De volgende code leest elk van de per uur onbewerkte gegevens bestanden, subsets naar de kolommen die we nodig hebben, voegt het weer gegeven station toewijzings bestand, past de datum tijden van metingen aan UTC aan en schrijft vervolgens een nieuwe versie van het bestand:

```
# Look up AirportID and Timezone for WBAN (weather station ID) and adjust time

adjustTime <- function(dataList)
{
  dataset0 <- as.data.frame(dataList)
  
  dataset1 <- base::merge(dataset0, wbanToAirIDAndTZDF1, by = "WBAN")

  if(nrow(dataset1) == 0) {
    dataset1 <- data.frame(
      Visibility = numeric(0),
      DryBulbCelsius = numeric(0),
      DewPointCelsius = numeric(0),
      RelativeHumidity = numeric(0),
      WindSpeed = numeric(0),
      Altimeter = numeric(0),
      AdjustedYear = numeric(0),
      AdjustedMonth = numeric(0),
      AdjustedDay = integer(0),
      AdjustedHour = integer(0),
      AirportID = integer(0)
    )
    
    return(dataset1)
  }
  
  Year <- as.integer(substr(dataset1$Date, 1, 4))
  Month <- as.integer(substr(dataset1$Date, 5, 6))
  Day <- as.integer(substr(dataset1$Date, 7, 8))
  
  Time <- dataset1$Time
  Hour <- ceiling(Time/100)
  
  Timezone <- as.integer(dataset1$TimeZone)
  
  adjustdate = as.POSIXlt(sprintf("%4d-%02d-%02d %02d:00:00", Year, Month, Day, Hour), tz = "UTC") + Timezone * 3600

  AdjustedYear = as.POSIXlt(adjustdate)$year + 1900
  AdjustedMonth = as.POSIXlt(adjustdate)$mon + 1
  AdjustedDay   = as.POSIXlt(adjustdate)$mday
  AdjustedHour  = as.POSIXlt(adjustdate)$hour
  
  AirportID = dataset1$AirportID
  Weather = dataset1[,c("Visibility", "DryBulbCelsius", "DewPointCelsius", "RelativeHumidity", "WindSpeed", "Altimeter")]
  
  data.set = data.frame(cbind(AdjustedYear, AdjustedMonth, AdjustedDay, AdjustedHour, AirportID, Weather))
  
  return(data.set)
}

wbanToAirIDAndTZDF <- read.csv("wban-to-airport-id-tz.csv")

colInfo <- list(
  WBAN = list(type="integer"),
  Date = list(type="character"),
  Time = list(type="integer"),
  Visibility = list(type="numeric"),
  DryBulbCelsius = list(type="numeric"),
  DewPointCelsius = list(type="numeric"),
  RelativeHumidity = list(type="numeric"),
  WindSpeed = list(type="numeric"),
  Altimeter = list(type="numeric")
)

weatherDF <- RxTextData(file.path(inputDataDir, "WeatherRaw"), colInfo = colInfo)

weatherDF1 <- RxTextData(file.path(inputDataDir, "Weather"), colInfo = colInfo,
                filesystem=hdfsFS)

rxSetComputeContext("localpar")
rxDataStep(weatherDF, outFile = weatherDF1, rowsPerRead = 50000, overwrite = T,
           transformFunc = adjustTime,
           transformObjects = list(wbanToAirIDAndTZDF1 = wbanToAirIDAndTZDF))
```

## <a name="importing-the-airline-and-weather-data-to-spark-dataframes"></a>De luchtvaart maatschappij en weers gegevens importeren in Spark DataFrames

Nu gebruiken we de functie [Read. df ()](https://spark.apache.org/docs/latest/api/R/read.df.html) van de sparker om de weers-en vliegtuig gegevens te importeren naar Spark DataFrames. Deze functie, zoals veel andere Spark-methoden, wordt uitgevoerd vertraagd, wat inhoudt dat ze in de wachtrij worden geplaatst voor uitvoering, maar pas worden uitgevoerd als dat is vereist.

```
airPath     <- file.path(inputDataDir, "AirOnTime08to12CSV")
weatherPath <- file.path(inputDataDir, "Weather") # pre-processed weather data
rxHadoopListFiles(airPath) 
rxHadoopListFiles(weatherPath) 

# create a SparkR DataFrame for the airline data

logmsg('create a SparkR DataFrame for the airline data') 
# use inferSchema = "false" for more robust parsing
airDF <- read.df(sqlContext, airPath, source = "com.databricks.spark.csv", 
                 header = "true", inferSchema = "false")

# Create a SparkR DataFrame for the weather data

logmsg('create a SparkR DataFrame for the weather data') 
weatherDF <- read.df(sqlContext, weatherPath, source = "com.databricks.spark.csv", 
                     header = "true", inferSchema = "true")
```

## <a name="data-cleansing-and-transformation"></a>Gegevens opschonen en transformeren

Nu gaan we de gegevens van de luchtvaart maatschappij die we hebben geïmporteerd, opschonen om de namen van kolommen te wijzigen. We blijven de variabelen alleen nodig en worden de geplande vertrek tijd naar het dichtstbijzijnde uur afgerond om samen voeging met de meest recente weer gegevens bij vertrek in te scha kelen:

```
logmsg('clean the airline data') 
airDF <- rename(airDF,
                ArrDel15 = airDF$ARR_DEL15,
                Year = airDF$YEAR,
                Month = airDF$MONTH,
                DayofMonth = airDF$DAY_OF_MONTH,
                DayOfWeek = airDF$DAY_OF_WEEK,
                Carrier = airDF$UNIQUE_CARRIER,
                OriginAirportID = airDF$ORIGIN_AIRPORT_ID,
                DestAirportID = airDF$DEST_AIRPORT_ID,
                CRSDepTime = airDF$CRS_DEP_TIME,
                CRSArrTime =  airDF$CRS_ARR_TIME
)

# Select desired columns from the flight data. 
varsToKeep <- c("ArrDel15", "Year", "Month", "DayofMonth", "DayOfWeek", "Carrier", "OriginAirportID", "DestAirportID", "CRSDepTime", "CRSArrTime")
airDF <- select(airDF, varsToKeep)

# Apply schema
coltypes(airDF) <- c("character", "integer", "integer", "integer", "integer", "character", "integer", "integer", "integer", "integer")

# Round down scheduled departure time to full hour.
airDF$CRSDepTime <- floor(airDF$CRSDepTime / 100)
```

Nu voeren we vergelijk bare bewerkingen uit op de weer gegevens:

```
# Average weather readings by hour
logmsg('clean the weather data') 
weatherDF <- agg(groupBy(weatherDF, "AdjustedYear", "AdjustedMonth", "AdjustedDay", "AdjustedHour", "AirportID"), Visibility="avg",
                  DryBulbCelsius="avg", DewPointCelsius="avg", RelativeHumidity="avg", WindSpeed="avg", Altimeter="avg"
                  )

weatherDF <- rename(weatherDF,
                    Visibility = weatherDF$'avg(Visibility)',
                    DryBulbCelsius = weatherDF$'avg(DryBulbCelsius)',
                    DewPointCelsius = weatherDF$'avg(DewPointCelsius)',
                    RelativeHumidity = weatherDF$'avg(RelativeHumidity)',
                    WindSpeed = weatherDF$'avg(WindSpeed)',
                    Altimeter = weatherDF$'avg(Altimeter)'
)
```

## <a name="joining-the-weather-and-airline-data"></a>De weers-en vliegtuig gegevens samen voegen

We gebruiken nu de functie vonken [samen voegen ()](https://spark.apache.org/docs/latest/api/R/join.html) om een left outer join van de luchtvaart maatschappij en de weers gegevens door vertrek AirportID en DateTime. Met de outer join kunnen we alle gegevens records van de luchtvaart maatschappijen bewaren, zelfs als er geen overeenkomende weer gegevens zijn. Na de samen voeging verwijdert u enkele overbodige kolommen en wijzigt u de naam van de opgeslagen kolommen om het binnenkomende data frame-voor voegsel dat door de samen voeging is geïntroduceerd, te verwijderen.

```
logmsg('Join airline data with weather at Origin Airport')
joinedDF <- SparkR::join(
  airDF,
  weatherDF,
  airDF$OriginAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF1 <- select(joinedDF, varsToKeep)

joinedDF2 <- rename(joinedDF1,
                    VisibilityOrigin = joinedDF1$Visibility,
                    DryBulbCelsiusOrigin = joinedDF1$DryBulbCelsius,
                    DewPointCelsiusOrigin = joinedDF1$DewPointCelsius,
                    RelativeHumidityOrigin = joinedDF1$RelativeHumidity,
                    WindSpeedOrigin = joinedDF1$WindSpeed,
                    AltimeterOrigin = joinedDF1$Altimeter
)
```

Op een vergelijk bare manier worden de gegevens weer gegeven en de luchtvaart maatschappij gebaseerd op aankomst AirportID en DateTime:

```
logmsg('Join airline data with weather at Destination Airport')
joinedDF3 <- SparkR::join(
  joinedDF2,
  weatherDF,
  airDF$DestAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF3)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF4 <- select(joinedDF3, varsToKeep)

joinedDF5 <- rename(joinedDF4,
                    VisibilityDest = joinedDF4$Visibility,
                    DryBulbCelsiusDest = joinedDF4$DryBulbCelsius,
                    DewPointCelsiusDest = joinedDF4$DewPointCelsius,
                    RelativeHumidityDest = joinedDF4$RelativeHumidity,
                    WindSpeedDest = joinedDF4$WindSpeed,
                    AltimeterDest = joinedDF4$Altimeter
                    )
```

## <a name="save-results-to-csv-for-exchange-with-scaler"></a>Resultaten opslaan in CSV voor Exchange met Scaleer

Hiermee worden de samen voegingen voltooid die moeten worden uitgevoerd met Spark. De gegevens van de laatste Spark data frame "joinedDF5" worden opgeslagen in een CSV-bestand voor invoer naar Scaleer en vervolgens wordt de Spark-sessie gesloten. We zijn expliciet overgegaan om de resulterende CSV in 80 afzonderlijke partities op te slaan om voldoende parallellisme in te scha kelen voor de verwerking van schalen:

```
logmsg('output the joined data from Spark to CSV') 
joinedDF5 <- repartition(joinedDF5, 80) # write.df below will produce this many CSVs

# write result to directory of CSVs
write.df(joinedDF5, file.path(dataDir, "joined5Csv"), "com.databricks.spark.csv", "overwrite", header = "true")

# We can shut down the SparkR Spark context now
sparkR.stop()

# remove non-data files
rxHadoopRemove(file.path(dataDir, "joined5Csv/_SUCCESS"))
```

## <a name="import-to-xdf-for-use-by-scaler"></a>Importeren naar XDF voor gebruik door schaal

We kunnen het CSV-bestand van samengevoegde luchtvaart maatschappij en weer gegevens gebruiken als-is voor modellering via een gegevens bron voor een Schaalder tekst. Maar we importeren het eerst in XDF, omdat het efficiënter is wanneer meerdere bewerkingen worden uitgevoerd op de gegevensset:

```
logmsg('Import the CSV to compressed, binary XDF format') 

# set the Spark compute context for ML Services 
rxSetComputeContext(sparkCC)
rxGetComputeContext()

colInfo <- list(
  ArrDel15 = list(type="numeric"),
  Year = list(type="factor"),
  Month = list(type="factor"),
  DayofMonth = list(type="factor"),
  DayOfWeek = list(type="factor"),
  Carrier = list(type="factor"),
  OriginAirportID = list(type="factor"),
  DestAirportID = list(type="factor"),
  RelativeHumidityOrigin = list(type="numeric"),
  AltimeterOrigin = list(type="numeric"),
  DryBulbCelsiusOrigin = list(type="numeric"),
  WindSpeedOrigin = list(type="numeric"),
  VisibilityOrigin = list(type="numeric"),
  DewPointCelsiusOrigin = list(type="numeric"),
  RelativeHumidityDest = list(type="numeric"),
  AltimeterDest = list(type="numeric"),
  DryBulbCelsiusDest = list(type="numeric"),
  WindSpeedDest = list(type="numeric"),
  VisibilityDest = list(type="numeric"),
  DewPointCelsiusDest = list(type="numeric")
)

joinedDF5Txt <- RxTextData(file.path(dataDir, "joined5Csv"),
                           colInfo = colInfo, fileSystem = hdfsFS)
rxGetInfo(joinedDF5Txt) 

destData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

rxImport(inData = joinedDF5Txt, destData, overwrite = TRUE)

rxGetInfo(destData, getVarInfo = T)

# File name: /user/RevoShare/dev/delayDataLarge/joined5XDF 
# Number of composite data files: 80 
# Number of observations: 148619655 
# Number of variables: 22 
# Number of blocks: 320 
# Compression type: zlib 
# Variable information: 
#   Var 1: ArrDel15, Type: numeric, Low/High: (0.0000, 1.0000)
# Var 2: Year
# 26 factor levels: 1987 1988 1989 1990 1991 ... 2008 2009 2010 2011 2012
# Var 3: Month
# 12 factor levels: 10 11 12 1 2 ... 5 6 7 8 9
# Var 4: DayofMonth
# 31 factor levels: 1 3 4 5 7 ... 29 30 2 18 31
# Var 5: DayOfWeek
# 7 factor levels: 4 6 7 1 3 2 5
# Var 6: Carrier
# 30 factor levels: PI UA US AA DL ... HA F9 YV 9E VX
# Var 7: OriginAirportID
# 374 factor levels: 15249 12264 11042 15412 13930 ... 13341 10559 14314 11711 10558
# Var 8: DestAirportID
# 378 factor levels: 13303 14492 10721 11057 13198 ... 14802 11711 11931 12899 10559
# Var 9: CRSDepTime, Type: integer, Low/High: (0, 24)
# Var 10: CRSArrTime, Type: integer, Low/High: (0, 2400)
# Var 11: RelativeHumidityOrigin, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 12: AltimeterOrigin, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 13: DryBulbCelsiusOrigin, Type: numeric, Low/High: (-46.1000, 47.8000)
# Var 14: WindSpeedOrigin, Type: numeric, Low/High: (0.0000, 81.0000)
# Var 15: VisibilityOrigin, Type: numeric, Low/High: (0.0000, 90.0000)
# Var 16: DewPointCelsiusOrigin, Type: numeric, Low/High: (-41.7000, 29.0000)
# Var 17: RelativeHumidityDest, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 18: AltimeterDest, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 19: DryBulbCelsiusDest, Type: numeric, Low/High: (-46.1000, 53.9000)
# Var 20: WindSpeedDest, Type: numeric, Low/High: (0.0000, 136.0000)
# Var 21: VisibilityDest, Type: numeric, Low/High: (0.0000, 88.0000)
# Var 22: DewPointCelsiusDest, Type: numeric, Low/High: (-43.0000, 29.0000)

finalData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

```

## <a name="splitting-data-for-training-and-test"></a>Gegevens voor training en testen opsplitsen

We gebruiken rxDataStep om de 2012-gegevens voor het testen te splitsen en de rest voor training te houden:

```
# split out the training data

logmsg('split out training data as all data except year 2012')
trainDS <- RxXdfData( file.path(dataDir, "finalDataTrain" ),fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = trainDS,
            rowSelection = ( Year != 2012 ), overwrite = T )

# split out the testing data

logmsg('split out the test data for year 2012') 
testDS <- RxXdfData( file.path(dataDir, "finalDataTest" ), fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = testDS,
            rowSelection = ( Year == 2012 ), overwrite = T )

rxGetInfo(trainDS)
rxGetInfo(testDS)
```

## <a name="train-and-test-a-logistic-regression-model"></a>Een logistiek regressie model trainen en testen

Nu gaan we een model bouwen. Als u de invloed van weer gegevens op de vertraging in de aankomst tijd wilt zien, gebruiken we de logistiek regressie routine van de Scaleer. We gebruiken deze om te model leren of een aankomst vertraging van meer dan 15 minuten wordt beïnvloed door de weers verwachting op de vertrek-en aankomst lucht havens:

```
logmsg('train a logistic regression model for Arrival Delay > 15 minutes') 
formula <- as.formula(ArrDel15 ~ Year + Month + DayofMonth + DayOfWeek + Carrier +
                     OriginAirportID + DestAirportID + CRSDepTime + CRSArrTime + 
                     RelativeHumidityOrigin + AltimeterOrigin + DryBulbCelsiusOrigin +
                     WindSpeedOrigin + VisibilityOrigin + DewPointCelsiusOrigin + 
                     RelativeHumidityDest + AltimeterDest + DryBulbCelsiusDest +
                     WindSpeedDest + VisibilityDest + DewPointCelsiusDest
                   )

# Use the scalable rxLogit() function but set max iterations to 3 for the purposes of 
# this exercise 

logitModel <- rxLogit(formula, data = trainDS, maxIterations = 3)

base::summary(logitModel)
```

Laten we nu eens kijken hoe het werkt op de test gegevens door bepaalde voor spellingen te maken en te kijken naar ROC en AUC.

```
# Predict over test data (Logistic Regression).

logmsg('predict over the test data') 
logitPredict <- RxXdfData(file.path(dataDir, "logitPredict"), fileSystem = hdfsFS)

# Use the scalable rxPredict() function

rxPredict(logitModel, data = testDS, outData = logitPredict,
          extraVarsToWrite = c("ArrDel15"), 
          type = 'response', overwrite = TRUE)

# Calculate ROC and Area Under the Curve (AUC).

logmsg('calculate the roc and auc') 
logitRoc <- rxRoc("ArrDel15", "ArrDel15_Pred", logitPredict)
logitAuc <- rxAuc(logitRoc)
head(logitAuc)
logitAuc

plot(logitRoc)
```

## <a name="scoring-elsewhere"></a>Score elders

We kunnen het model ook gebruiken voor het scoren van gegevens op een ander platform. Door de app op te slaan in een RDS-bestand en deze vervolgens te verplaatsen en te importeren in een doel Score omgeving zoals Microsoft SQL Server R-Services. Het is belang rijk om ervoor te zorgen dat de factor niveaus van de gegevens die moeten worden beoordeeld overeenkomen met die op basis waarvan het model is gebouwd. Dit kan worden bereikt door de kolom informatie die is gekoppeld aan de model gegevens te extra heren en op te slaan met behulp van de functie van de `rxCreateColInfo()` gegevens bron voor voor spellingen. In het volgende code voorbeeld slaan we een paar rijen van de test gegevensset op en halen en gebruiken de kolom gegevens uit dit voor beeld in het Voorspellings script:

```
# save the model and a sample of the test dataset 

logmsg('save serialized version of the model and a sample of the test data')
rxSetComputeContext('localpar') 
saveRDS(logitModel, file = "logitModel.rds")
testDF <- head(testDS, 1000)  
saveRDS(testDF    , file = "testDF.rds"    )
list.files()

rxHadoopListFiles(file.path(inputDataDir,''))
rxHadoopListFiles(dataDir)

# stop the spark engine 
rxStopEngine(sparkCC) 

logmsg('Done.')
elapsed <- (proc.time() - t0)[3]
logmsg(paste('Elapsed time=',sprintf('%6.2f',elapsed),'(sec)\n\n'))
```

## <a name="summary"></a>Samenvatting

In dit artikel laten we zien hoe u het gebruik van Spark kunt combi neren voor het bewerken van gegevens met Scaler voor het ontwikkelen van modellen in Hadoop Spark. Voor dit scenario moet u afzonderlijke Spark-sessies onderhouden, slechts één sessie tegelijk uitvoeren en gegevens uitwisselen via CSV-bestanden. Hoewel het eenvoudig is, zou dit proces nog eenvoudiger moeten zijn in een aanstaande versie van MILLILITERs Services, wanneer Spark en Scaler een Spark-sessie kunnen delen en dus Spark DataFrames delen.

## <a name="next-steps-and-more-information"></a>Volgende stappen en meer informatie

- Zie de aan de slag- [hand leiding](/machine-learning-server/r/how-to-revoscaler-spark)voor meer informatie over het gebruik van ML Server op Apache Spark.

- Zie [overzicht van ml Services op hdinsight](r-server/r-server-overview.md)voor meer informatie over de ml van de services op hdinsight.

Zie voor meer informatie over het gebruik van Spark:

- [Apache Spark-document](https://spark.apache.org/docs/2.1.0/sparkr.html).

- [Overzicht van Spark](/azure/databricks/spark/latest/sparkr/overview)
