# Project Report
UT-TOR-DATA-PT-01-2020-U-C Week 13 Project

by Boris Smirnov and Daniel Vaithilingam


## Introduction

The goal of the project was creation of a database containig inventory of different used car dealers.
The business application of this project could be a brokerage service that helps customers to find best
offer on a used car in a local area. For that purpose we chose a number of car dealers and scraped their 
web sites for their inventories. Along with cars information we created a database collection of dealer information,
that includes not only contact data, but also geographic coordinates, which could be later used with
Google Maps API for locating dealers that are close to customer's home.


The process included folowing steps:

1. **Scraping** dealers web sites and **transformation** data from loosely structured *HTML* form to well structured *JSON*
format suitable for direct uploading into a database.

2. **Loading** scraped data into MongoDB database


## Extraction

Extraction part of the project included development of scraping scripts in Python using Jupyter Notebooks.
For scraping we used *requests* and *splinter* libraries. For each web site a separate Python script was created:

* auto8000.ipynb - for https://www.auto8000.ca
* khushiauto.ipynb - for https://www.khushiauto.ca
* leopardmotors.ipynb - for http://www.leopardmotors.ca
* rahmanmotors.ipynb - for https://www.rahmanmotors.com
* simpleauto.ipynb - for https://www.simpleautos.ca
* tabangimotors.ipynb - for https://www.tabangimotors.com

Each script creates 2 JSON files in *data* folder, one with dealer information, another - with dealers inventory.

### Problems encountered during extraction process

#### Scraping protection

"Leopard Motors" didn't allow us to use simple method of scraping using *requests*  - upon retrieval of a web page
it returned HTML with text: "Not Acceptable!". Switching to *splinter* library solved the problem.

#### Inifinite Scroll

"Auto 8000" used Inifinite Scroll on its inventory page: when opened, the web page showed only 6 cars, but when
scrolling down the page, java script pulled additional 6 cars from the server. To overcome this, extraction script
used *splinter* to run our java script snippet that automatically scrolled the page until all cars from the inventory
were loaded. Only after that the inventory was retrieved.

## Transformation

Source data (car information) was initially represented as poorly structured text in HTML format.
The result of the extraction - JSON files with well structured data suitable for loading into MongoDB.
The convertion of one to another, performed in the scraing scripts, constitutes **transformation** phase.

Extracted string from web pages were transformed based on the semantics of data.
For example, the price information is typically represented on a web page as a string in a form ``$15,960.00``,
however, in the database it should be interpreted as integer in a form ``15960``. Such kind of tranformations
were performed on individual basis, based on peculiarities of a given web site.
Other fields that were subject to similar interpretations: number of seats, number of cylinders, engine displacement.

Some effort was made to clean up textual description field, that contained a lot of advertisement related information, 
that would be completely useless in business application.


## Load
    
Since only core car description parameters coincide among different car dealers, we chose MongoDB for data
storage, because it easily allows flexibility with loosely structured data with many optional fields.

The output files that extraction scripts generate are in JSON format suitable for load into MongoDB.
The following ways of loading data were tested and confirmed to work:

* Command line utility: ``mongoimport --db cars_db --collection inventory --file data/auto8000.json``
* MongoDB Compass using *Import File* feature
* MongoDB Compass using *Insert Document* feature
* *pymongo* library in ``CarsDBConstructor2.ipynb`` Jupyter Notebook, that loads all the scraped data into the database

During development of our data import procedure, we encountered some problems, that prevented data to load.
Notably, using ``json.dumps()`` function for saving documents into files produced unsatisfactory results - the files
wouldn't load. Investigation revealed, that the correct format should be:
* only spaces are allowd in a single JSON document - the whole structure should be in one line
* a set of documents should be saved in a file one per line - no other separator then new-line characted should
be between JSON documents

To implement these requirements, we used ``json.dumps()`` funcion which was called for every single document when
saving them to a file.


## Order of script execution

1. Extraction and Tranformation  (in any order):
    * auto8000.ipynb
    * khushiauto.ipynb
    * leopardmotors.ipynb
    * rahmanmotors.ipynb
    * simpleauto.ipynb
    * tabangimotors.ipynb
2. Data load:
    CarsDBConstructor2.ipynb


### Appendix: document formats for Car and Dealer description

Dealer (contact information as found at the bottom of the front page):

* **id** - dealer Id (i.e. *khushiauto* - middle part of the web site domain name)
* **name** - dealer name (i.e. *Khushi Auto Sales Inc.*)
* **street** - street address (i.e. *2783 Derry Rd E.*)
* **city** - cyty (i.e. *Mississauga*)
* **zip** - postal code (i.e. *L4T1A3*)
* **province** - province abbreviation (i.e. *ON*)
* **phone** - contact phone number (i.e. *905-460-9624*)
* **url** - website (i.e. *https://www.khushiauto.ca*)
* **latitude**, **longitude** - geographic coordinates from Google Maps
(can be used to find a dealer in certain range from customer's home)

Car:

* **timestamp** - a timestamp for when this car info was scraped, can be obtained with `datetime.now().isoformat()`
* **dealer** - dealer Id
* **stock_id** - some dealer specific id of the scraped car
* **vin**
* **mileage** - as integer
* **price** - as integer
* **make** - car manufacturer
* **model** - car model
* **submodel** - car trim level or other specs that are a part of car's listing, i.e. Nissan Rougue **SL**
* **year** - car production year, as integer
* **body_style** - coup, wagon, sedan, etc.
* **doors** - as integer, number of doors
* **passengers** - as integer, number of passengers
* **int_color**, **ext_color**
* **transmission** - manual, automatic
* **drivetrain** - FWD, RWD, AWD
* **cylinders** - as integer, number of cylinders
* **displacement** - as float, engine displacement in litres
* **description** - free form text
* **features** - as list of strings, list of features and options
* **url** - URL to car's page at dealer website

### Example Queries

use cars_db

#### 1) Creating a collection of premium cars
```
db.inventory.find({'make': {'$in': ['Acura','Audi','Aston Martin','Bentley','BMW','Buick','Bugatti','Cadillac','Ferrari','Genesis','Infiniti','Jaguar','Land Rover','Lamborghini','Lexus','Lincoln','Maserati','Maybach','Mercedes-Benz','Porsche','Rolls-Royce']}}).forEach(function(doc) { db.premium.insert(doc);});
```

#### 2) Creating a collection of late model year cars
```
db.inventory.find({'year': {'$in': ['2020','2019','2018','2017','2016','2015']}}).forEach(function(doc) { db.late_model.insert(doc);});
```

#### 3)Creating a collection of SUVs
```
db.inventory.find({'body_style': {'$in': ['SUV']}}).forEach(function(doc) { db.SUV.insert(doc);});
```

