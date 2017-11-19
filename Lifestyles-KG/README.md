# Lifestyles-KB a fuzzy ontology for lifestyle profiling

Lifestyles-KB.fdl  is the fuzzy DL ontology

Lifestyles-KB.owl is an equivalent crisp  sub-version of the fuzzy one

The ontology is described in:

[1] Couch potato or gym addict? Semantic lifestyle profiling with wearables and knowledge graphs. Natalia ~Díaz-Rodríguez, Aki Härmä, Ignacio Huitzil, Rim Helaoui, Fernando Bobillo and Umberto Straccia. Submitted to NIPS workshop on Automatic Knowledge Base Construction, 2017, Long Beach, CA.


![alt text](https://github.com/NataliaDiaz/Ontologies/blob/master/Lifestyles-KG/Lifestyles-KB-documentation.png)


## Running the clustering algorithms for fuzzy datatype learning:

The clustering application is done in Java, and a .jar executable allows to select the csv files to apply clustering on. All data records .csv must be contained in a data/folder and there must be only .csv files in that folder.
It requires a config.txt file that contains pairs of integers separated by comma where each integer is the index of the column (starting by 0) followed by the number of clusters to extract for that column (in clustering types 1 and 2).
E.g.to apply clustering on columns 2,3, and 4 with 5 clusters each, the following config file would be provided:

```
1,5,2,5,3,5
```
If some of the columns is NOT numerical, please remove it from the config file (e.g., First column is not used in our example because it indicates the day segment type).


To run the 3 clustering algorithms:

```
>java -jar FuzzyDatatypeLearner.jar 1     # option 1: fuzzy-k means clustering
>java -jar FuzzyDatatypeLearner.jar 2     # option 2: k means clustering
>java -jar FuzzyDatatypeLearner.jar 3     # option 3: Mean-shift clustering
```

To save the datatypes into a fuzzyDL ontology file .fdl, we can run

```
java -jar FuzzyDatatypeLearner.jar 1  >>  fuzzOnt.fdl
```

Example of output for two columns for day segment (commute) "ToWork":
```
% -----------------------------------------------------------------------
% DataProperty: StartTime SegmentType: ToHome
% Learnt using k-means
% Centroids results: [518.4][679.19][860.11][1055.57][1182.31]
(define-fuzzy-concept VeryLowStartTimeToHome left-shoulder(-10000,10000,518.4,679.19))
(define-fuzzy-concept LowStartTimeToHome triangular(-10000,10000,518.4,679.19,860.11))
(define-fuzzy-concept NeutralStartTimeToHome triangular(-10000,10000,679.19,860.11,1055.57))
(define-fuzzy-concept HighStartTimeToHome triangular(-10000,10000,860.11,1055.57,1182.31))
(define-fuzzy-concept VeryHigStartTimeToHome right-shoulder(-10000,10000,1055.57,1182.31))
% -----------------------------------------------------------------------
% DataProperty: StartTimeFixedNight has 0 data
% -----------------------------------------------------------------------
% DataProperty: StartTime SegmentType: AtWork
% Learnt using k-means
% Centroids results: [574.46][679.88][826.07][1037.87][1274.0]
(define-fuzzy-concept VeryLowStartTimeAtWork left-shoulder(-10000,10000,574.46,679.88))
(define-fuzzy-concept LowStartTimeAtWork triangular(-10000,10000,574.46,679.88,826.07))
(define-fuzzy-concept NeutralStartTimeAtWork triangular(-10000,10000,679.88,826.07,1037.87))
(define-fuzzy-concept HighStartTimeAtWork triangular(-10000,10000,826.07,1037.87,1274.0))
(define-fuzzy-concept VeryHigStartTimeAtWork right-shoulder(-10000,10000,1037.87,1274.0))
% -----------------------------------------------------------------------
% DataProperty: Timestamp SegmentType: AtWork
% Learnt using k-means
% Centroids results: [736271.63][736296.5][736352.69][736394.5][736408.5]
(define-fuzzy-concept VeryLowTimestampAtWork left-shoulder(-10000,10000,736271.63,736296.5))
(define-fuzzy-concept LowTimestampAtWork triangular(-10000,10000,736271.63,736296.5,736352.69))
(define-fuzzy-concept NeutralTimestampAtWork triangular(-10000,10000,736296.5,736352.69,736394.5))
(define-fuzzy-concept HighTimestampAtWork triangular(-10000,10000,736352.69,736394.5,736408.5))
(define-fuzzy-concept VeryHigTimestampAtWork right-shoulder(-10000,10000,736394.5,736408.5))
% -----------------------------------------------------------------------
% DataProperty: SkinTemperature SegmentType: ToWork
% Learnt using k-means
% Centroids results: [4.9][15.19][22.02][30.6][34.0]
(define-fuzzy-concept VeryLowSkinTemperatureToWork left-shoulder(-10000,10000,4.9,15.19))
(define-fuzzy-concept LowSkinTemperatureToWork triangular(-10000,10000,4.9,15.19,22.02))
(define-fuzzy-concept NeutralSkinTemperatureToWork triangular(-10000,10000,15.19,22.02,30.6))
(define-fuzzy-concept HighSkinTemperatureToWork triangular(-10000,10000,22.02,30.6,34.0))
(define-fuzzy-concept VeryHigSkinTemperatureToWork right-shoulder(-10000,10000,30.6,34.0))
% -----------------------------------------------------------------------

```

## Some notes about Day Segments and other datatypes:  

The basic unit in which a day footprint is divided into is a day segment. Fixed day segments by clock
0-6, 6-12, 12-18, 18-24h exists, although regular ones such as morning (the time between waking up
and going to work) are based on behaviour sensing. They are provided with start and end time -in minutes- and they are commputed by external algorithm that takes into account to compute them the following clusters of locations:

* Home, H – All places user calls ‘Home’ or where the user is last thing in the evening and
first thing in the morning.
* Work, W – All places the user calls ‘Work’, ‘Philips’, ‘HTC’ or where the user regularly
commutes to and from or is in the middle of a weekday.
* Key points, K – Frequented places in W <–> H transfers where the user regularly stops at
(kindergarten, supermarket, fitness centre). K has no loops.

* Other places, O - All other places that users visit either from H or W. These will often be
more sporadically visited places, such as restaurants, theaters, museums or other cities.

* Examples of segmentType:
```
toHome
eveningAfterWork
freeDayTime
eveningAfterFree
fixedNight
fixedMorning
fixedAfternoon
fixedEvening
wholeDay
sleep
awake
morning
toWork
atWork
```

* Datatype (columns) applied clustering on:

```
startTime	endTime	timestamp	skinTemperature	galvanicSkinResponse	bodyTemperature	weight	heartRate	restingHeartRate	heartRateRecovery	calories	ldlCholesterol	hdlCholesterol	vldlCholesterol	triglycerides	fatPercentage	breathingRate	bloodGlucose	bloodPressureSystolic	bloodPressureDiastolic	bloodOxygen	energyLevel	steps	stepDistance	stepSpeed	sleepState	sleepTossTurn	elevation	floors	waistCircumference	locationLongitude	locationLatitude	mood	userId	placeId	activityId	mealId	sleepRating	activeEnergyExpenditure	moodStrength	activityStart	activityDuration	activityDistance	activityTypeId	activityCode	placeName	placeLocationLongitude	placeLocationLatitude	movesId	fourSquareId	placeTypeId	placeCode	measurementDate	measurementTime	maxHeartRate	vO2max	energyIntake	mealSize	foodGroup	activeMinutes	lsg_speed	lsg_latitude	lsg_longitude	lsg_cleansteps	lsg_AID	lsg_MET	lsg_wear	weekday	dayType	sedentaryRatio	walkingRatio	cyclingRatio	transportRatio	sedentaryMaxDur	walkingMaxDur	cyclingMaxDur	transportMaxDur
```

* Weekday SUNDAY = 1;... SATURDAY = 7;



## Data used for learning the fuzzy datatypes:

 Each file contains a stack of days from one individual used and each day consists of a fixed set of segments. Each file contains the same segments but there may be a different number of days, and therefore the number of segments too.


## Using the Lifestyles-KB ontology:

For seeing examples of queries on fuzzyDL reasoner, we refer the reader to the examples section in the fuzzyDL website.



## Examples of fuzzyDL queries for satisfiability of a given lifestyle:
```
(g-and (some hasCycle_count cycle_count_u109s4T93) (some hasCyclingR cycling_u109s4T93) (some hasCycling_distance cycling_distance_u109s4T93) (some hasCycling_transport cycling_transport_u109s4T93) (some hasEndT end_u109s4T93) (some spentNCalories calories_u109s4T93) Evening )


2-(g-and (some hasEndT end_u87s4) (some hasGalvanicSkinResponse galvanicSkinResponse_u87s4) (some hasMaxHR maxHR_u87s4) (some hasMostCommonPlace (w-sum (LocationHub62443755 0.111) (LocationHub60754861 0.778) (LocationHub286069606 0.111) )) (some hasStartT start_u87s4) (some hasTransportR transport_u87s4) (some hasTransport_distance transport_distance_u87s4) (some hasWalkingR walking_u87s4) (some hasWalking_distance walking_distance_u87s4) (some hasWalking_transport walking_transport_u87s4) (some hasWalking_transport_ordered walking_transport_ordered_u87s4) (some spentNCalories calories_u87s4) (some walkedNSteps steps_u87s4) Evening )


0.0


Max sat. matching deg. of concepts 1-

(g-and (some hasCycle_count cycle_count_u109s4T93) (some hasCyclingR cycling_u109s4T93) (some hasCycling_distance cycling_distance_u109s4T93) (some hasCycling_transport cycling_transport_u109s4T93) (some hasEndT end_u109s4T93) (some spentNCalories calories_u109s4T93) Evening )
2-(g-and (some hasEndT end_u90s4) (some hasStartT start_u90s4) (some hasTransp_count transp_count_u90s4) (some hasTransportR transport_u90s4) (some hasTransport_distance transport_distance_u90s4) (some hasWalkingR walking_u90s4) (some hasWalking_distance walking_distance_u90s4) (some hasWalking_transport walking_transport_u90s4) (some hasWalking_transport_ordered walking_transport_ordered_u90s4) (some hasWalks_count walks_count_u90s4) (some hasWalks_other_ratio walks_other_ratio_u90s4) (some spentNCalories calories_u90s4) (some walkedNSteps steps_u90s4) Evening )


1.0

```



## TO-DOs:

* An updated jar file will be made public soon. Due to privacy, the data cannot be shared at the moment.
* Comment normalization accross versions of fuzzyDL
* Label homogeneization for means-shift clustering (Algorithm option 3)
