# EaserGeocoder

## Project Summary

Project description and why we have done this is summarized here:

http://bmidb.cs.stonybrook.edu/projects/biggeocoding

## Short Version

Download required files from [our download page](http://bmidb.cs.stonybrook.edu/easergeocoder/download), then follow instractions in below.

Steps:
1. Download and unzip the zip file
2. [Start the Solr](README.md#start-the-solr)
3. Make input file according to our [format](README.md#input-file-format).
4. Use jar file or edu.stonybrook.cs.bmidb.biggeocoding.multithreading.MulthithreaderJar.java for running the program. Check out the [example](README.md#jar-file-example)

That is it! We have encapsulated everything in order to make it as simple as possible.
In case you are facing any problems, please see [troubleshooting](README.md#troubleshooting) section.


## Long Version


### Solr 

In this project Apache Solr has been used as database for indexing/storing all information. A set of four reference sources are imported in Solr in different cores as our main database. Since this process could take time and a little bit confusing, the Solr containing index data is prepared for usage of this system. It is included in the zip file mentioned earlier.


### API

You can find multithreadtest.java file in edu.stonybrook.cs.bmidb.biggeocoding.multithreading.multithreadtest package.

Then you could run it to test the program.
There are 5 paramters you can change to modify the program.

#### 1. SourceType[] typesToBeTested
Here you could decide which data source you want to use in this program.
Default values are:
SourceType[] typesToBeTested ={SourceType.OpenAddress,SourceType.PARCEL,SourceType.SAM,SourceType.TIGER};

#### 2. int mode
This is used to assign the input and output format.

#### 3. String inputfilename
This is used to designate which file you want this program to geocode.

#### 4. int NumberOfThreads
This is used to decide how many threads you want this program to use.

#### 5. int maxNumber
This is used to decide the maximum number of address you want the program to geocode.



#### Input File Format

For standard input file, you need to prepare a csv file with headers as follow - Latitude, Longitude, Address 1, Address 2, City, County, State, Zip code, Country. There are four requirements for the input file listed below.

1. Must have meaningful building number.
2. //TODO Must have zip code.
3. Must be in New York state, USA.
4. Must have street name.

For searching geolocation of this address 339 Hamilton St., Albany, NY 12210, input file should have this format:

|latitude|longitude|address1|address2|city|county|state|zipcode|country|
| ------------- | ------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |
|42.651895|-73.764145|339 Hamilton St||| Albany| NY|12210|USA|

When your address has two parts, you need to put place name below the address1, and the part with street name below the address2.

Strong Memorial Hospital,601 Elmwood Ave, Rochester, NY 14642.

|latitude|longitude|address1|address2|city|county|state|zipcode|country|
| ------------- | ------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |
|43.122824|-77.625281|Strong Memorial Hospital|601 Elmwood Ave|Rochester||NY|14642|USA|

There are two samples in folder "input", check them out.

### Program Modes

This program works in four modes. Although, the functionality of the program is more or less the same, these modes are useful for different scenarios.

1. [**Comparision Mode**](README.md#comparision-mode) For measuring accuracy of this system given a list of addresses with their geolocations as the ground truth.

2. [**Searching Mode**](README.md#searching-mode) To extract geolocations for list of addresses, most common mode.

3. [**Detailed Debug Mode**](README.md#detailed-debug-mode) Generates the entire search information for all sources.

4. [**Full Features Mode**](README.md#full-features-mode) If you want to look get the features to be trained in decision tree for all addresses for all sources, choose mode 4, which is fully feature mode.


##### Comparision Mode

The best geolocation for each address will be extraced and compared to the given geolocation.

For each query, output has two parts, input part and output part. Input part format for all queries are same. It will be like:

|Score|Index|Apartment Number|Base Number|Base Building Spliter|Building Number|Complement Part|Prefix|Street name|Postfix|City|County|State|Zip code|Country|Full input address||Location|
| ------------- | ------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |
|3.834683099558874|11|null|null|null|45|null|null|buckingham|dr|null|albany|null|12208|null|45 buckingham dr  albany  ny 12208 usa|false|Latitude: 42.6609691	  Longitude: -73.8140429|

For different sources, output has differnet formats. The reason is rooted in the variety of characteristics of each reference source map which is not possible to gather in a same set of features.

#### OpenAddress Example

|Prefix|Street name|Postfix|City|County|State|Base number|Building number|Postcode part 1|Postcode part 2|finale base number|finale building number|final zip code|...|Latitude|Longitude|Source|Distance|Distance < 400 meters|Is perfect match|County Centroid|City Centroid|Zip code Centroid|
| ------------- | ------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- | ------------- | ------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |
|n|northgate|dr|guilderland|albany|ny|null|63|null|null||63||...|42.6981639|-73.8741739|OpenAddress|4758.34587622398|false|false|Latitude: 42.58824	  Longitude: -73.97401|null|Latitude: 42.681048	  Longitude: -73.84698|


#### Parcel Example

|County name|Muni name|City name|Prefix|Street name|Postfix|Zip code|Base number|Building Number|finale base number|finale building number|final zip code|...|Latitude|Longitude|Source|Distance|Distance < 400 meters|Is perfect match|County Centroid|City Centroid|Zip code Centroid|
| ------------- | ------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- | ------------- | ------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |
|albany|bethlehem|bethlehem|n|bridge|dr|null|null|63||63||...|42.6687666|-73.83191|PARCEL|2.3594866813368776|true|false|Latitude: 42.58824	  Longitude: -73.97401|null|Latitude: 42.681048	  Longitude: -73.84698|

#### SAM Example
|Base number|Building number|Prefix direction|Street name|Postfix|Postfix direction|State|Zipcode|Place|County|City|finale base number|finale building number|final zip code|...|Latitude|Longitude|Source|Distance|Distance < 400 meters|Is perfect match|County Centroid|City Centroid|Zip code Centroid|
| ------------- | ------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- | ------------- | ------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |
|null|63|n|bridge|dr|null|ny|12203|mckownville|albany|bethlehem||63||...|42.6687937436|-73.8319731329|SAM|8.338857058329397|true|false|Latitude: 42.58824	  Longitude: -73.97401|null|Latitude: 42.681048	  Longitude: -73.84698|

#### Tiger Example

|State|County|LHN_BASE|LFROMHN|LTOHN|RHN_BASE|RFROMHN|RTOHN|ZIPL|ZIPR|Street name|Prefix|Postfix|finale base number|finale building number|final zip code|...|Latitude|Longitude|Source|Distance|Distance < 400 meters|Is perfect match|County Centroid|City Centroid|Zip code Centroid|
| ------------- | ------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- | ------------- | ------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |
|ny|albany|null|48|98|null|47|73|12203|12203|bridge|n|dr|63|null|12203 | ... |42.66831769230769|-73.83205369230768|TIGER|50.651561812302035|true|false|Latitude: 42.58824	  Longitude: -73.97401|null|Latitude: 42.681048	  Longitude: -73.84698|



##### Searching Mode

Output is very straight forward:

|Index|Base Number|Building Number|Prefix|Street Name|Postfix|city|county|zipcode|latitude|longitude|
| ------------- | ------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |
|8|null|8|null|beekman|st|albany|albany|null|42.6400256|-73.783031|


##### Detailed Debug Mode


In this mode, program will return full list of all responses from difference sources. It is useful only for developers fixing a bug.

##### Full Features Mode
|Have response or not|Base Number Difference|Building Number difference|Prefix difference|Street Name difference|Postfix difference|city name difference|county name difference|zipcode difference|source map|zipcode|
| ------------- | ------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |------------- |
|1|	0|	1|	0|	1|	0.888888889|	0|	1|	2|	12208|

The values for each feature is defined as:

Have response or not: 0 for having response, -1 for not.
Base Number/Building Number difference: Absolute integer difference.
Street Name/City Name/County Name/Zip Code difference: Normalized Edit Distance.
Source map: Tiger-0, Parcel-1, OpenAddress-2, SAM-4.
Zip Code: zip code number.


### Exceptions

Common exceptions are listed in below. If program throws exceptions which are not specified in below and the input address has all required tokens, please report a bug containing the input address and output program generates.

|Index|Exception Message|
| ------------- | ------------- |
|1|Your input address is out of New York State|
|2|Your input address is out of USA|
|3|Your input address doesn't have a meaningful building number|
|4|Your input address doesn't have a zip code|

#### Start the Solr

First you need to run the solr, go to the solr folder in you computer, in Linux and Mac, use command:

bin/solr start -e cloud -noprompt

On Windows, use:

bin/solr.cmd start -e cloud -noprompt

Stop Solr in linux and Mac use command:

bin/solr stop -all

In Windows, use:

bin/solr.cmd stop -all


#### Running Example

java -jar BigGeocodingJava.jar inputfilename

java -jar BigGeocodingJava.jar inputfilename outputfilename

The program will geocode the addresses the inputfilename, results will be written in outputfilename. In case the outputfilename is missing, the output file will have the same name as inputfilename and it will be placedin output folder near jar file.

The jar file could take four other optional parameters as following with their default value.

| Name         | Description                                | Default Value               | Parameter Syntax  | 
| -------------|--------------------------------------------|-----------------------------|-------------------|
| Max          | Number of rows in input file for geocoding | Till the end of the file    | -m                |
| Output Mode  | Specifiying the output format              | 5                           | -o                |
| Threads      | Number of additional active threads        | 15                          | -t                |
| Cores        | Reference Source maps for geocoding        | OpenAddress PARCEL SAM TIGER| -c                |

An example of running the program with all additional parameters:

java -jar BigGeocodingJava.jar inputfilename outputfilename -max 2000 -thread 10 -mode 2 -core OpenAddress Parcel SAM Tiger

It means, first 2000 addresses in inputfilename file will be geocoded, by 10 threads based on maps in the order of OpenAddress, Parcel, SAM and Tiger, outputed based on mode 2.


### Troubleshooting

+ Solr Problems


Solr is not OS independent, make sure the right command (solr or solr.cmd) is used. 

If you get exception that solr didn’t start in 30 seconds, when you start solr first time in your computer. Then you need to stop solr and start it again, exception should be cleared.

