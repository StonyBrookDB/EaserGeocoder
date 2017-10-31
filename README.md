# EaserGeocoder

## Project Summary

Project description and why we have done this is summarized here:

http://bmidb.cs.stonybrook.edu/projects/biggeocoding

## Installation
This is a portable and OS independent system, therefore installation is practically just downloading and running the system.

Steps:
1. Download required files from [our download page](http://bmidb.cs.stonybrook.edu/easergeocoder/download)
2. [Start the Solr](README.md#start-the-solr)
3. Make the input file according to our [format](README.md#input-file).
4. Use jar file for running the program. Check out the [example](README.md#running-example)

That is it! We have encapsulated everything in order to make it as simple as possible.
In case you are facing any problems, please see [troubleshooting](README.md#troubleshooting) section.


## System Description


### Solr 

In this project Apache Solr has been used as database for indexing/storing all information. A set of four reference sources are imported in Solr in different cores as our main database. Since this process could take time and a little bit confusing, the Solr containing index data is prepared for usage of this system. It is included in the zip file mentioned earlier.


### API
The jar file could take following parameters.


#### 1. Input File
This mandatory parameter is the address of input file with the format in below. 
Currently we are only supporting csv format with headers as follow - Latitude, Longitude, Address 1, Address 2, City, County, State, Zip code, Country. There are four requirements for the input file listed below.

1. Must have meaningful building number.
2. Must have zip code.
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

There are samples of input files format at our [download page](http://bmidb.cs.stonybrook.edu/easergeocoder/download), check them out.



#### 2. Mode
This is used to assign the input and output format. Different modes are explained in details here.

#### Program Modes

This program works in four modes. Although, the functionality of the program is more or less the same, these modes are useful for different scenarios.

1. [**Comparision Mode**](README.md#comparision-mode) For measuring accuracy of this system given a list of addresses with their geolocations as the ground truth.

2. [**Searching Mode**](README.md#searching-mode) To extract geolocations for list of addresses, most common mode.

3. [**Detailed Debug Mode**](README.md#detailed-debug-mode) Generates the entire search information for all sources.

4. [**Full Features Mode**](README.md#full-features-mode) If you want to look get the features to be trained in decision tree for all addresses for all sources, choose mode 4, which is fully feature mode.



#### 3. NumberOfThreads
Specifying number of additional threads the program could use which helps improving performance of this system. The minimum amount is one, default is set to 8 and recommended number is two times cpu virtual cores.

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

java -jar *EaserGeocoder_VX.X*.jar inputfilename

java -jar *EaserGeocoder_VX.X*.jar inputfilename outputfilename

The program will geocode the addresses the inputfilename, results will be written in outputfilename. In case the outputfilename is missing, the output file will have the same name as inputfilename and it will be placed in output folder near jar file.

The jar file could take three other optional parameters as following with their default value.

| Name         | Description                                | Default Value              | Parameter Syntax  | 
| -------------|--------------------------------------------|----------------------------|-------------------|
| Output Mode  | Specifiying the output format              | 5                          | -o                |
| Threads      | Number of additional active threads        | 8                          | -t                |

An example of running the program with all additional parameters:

java -jar *EaserGeocoder_VX.X*.jar inputfilename outputfilename -t 10 -o 2 


### Troubleshooting

1. Solr Problems


Solr is not OS independent, make sure the right command (solr or solr.cmd) is used. 

If you get exception that solr didn’t start in 30 seconds, when you start solr first time in your computer. Then you need to stop solr and start it again, exception should be cleared.
If you encountered any other exception, you should download [Apache Solr](http://lucene.apache.org/solr/mirrors-solr-latest-redir.html) based on your system, then replace the example/cloud/ folder of our [Solr](http://bmidb.cs.stonybrook.edu/easergeocoder/download) with new version you have downloaded. 

