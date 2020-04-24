# ETL-Project
 UT-TOR-DATA-PT-01-2020-U-C Week 13 Project


# Note


We have to agree on a key-naming convention, so our scraper scripts would generate consistent JSON structures.


That is:
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
* **int_color**, **ext_color**
* **transmission** - manual, automatic
* **description** - free text
* ??? open for suggestions

I guess we have to agree on minimal mandatory subset of key-names, but it is ok if our scraped data will be a little bit different - we can always rename keys in a collection using MongoDb commands, or with search-n-replace directly in text files.


## Web sites to scratch (2 out of 3):


**Dan:**
* https://www.khushiauto.ca/used-cars
* https://www.simpleautos.ca/used-cars/
* https://www.tabangimotors.com/used-cars/


**Boris:**
* https://www.rahmanmotors.com/cars/used/
* https://www.auto8000.ca/inventory/
* http://www.leopardmotors.ca/listings/
