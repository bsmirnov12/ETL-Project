# ETL-Project
 UT-TOR-DATA-PT-01-2020-U-C Week 13 Project


# Note

We have to agree on a key-naming convention, so our scraper scripts would generate consistent JSON structures.


Dealer keys (contact information is found at the bottom of the front page):
* **id** - dealer Id (i.e. *khushiauto* - middle part of the web site domain name)
* **name** - dealer name (i.e. *Khushi Auto Sales Inc.*)
* **street** - street address (i.e. *2783 Derry Rd E.*)
* **city** - cyty (i.e. *Mississauga*)
* **zip** - postal code (i.e. *L4T1A3*)
* **province** - province abbreviation (i.e. *ON*)
* **phone** - contact phone number (i.e. *905-460-9624*)
* **url** - website (i.e. *https://www.khushiauto.ca*)


Optional, extra effort (but would be cool, if we use Google Maps API to query this info)


* **latitude**, **longitude** - geographic coordinates (can be used to find a dealer in certain range from customer's home)

Car keys:
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

I guess we have to agree on minimal mandatory subset of key-names, but it is ok if our scraped data is a little bit different - we can always rename keys in a collection using MongoDb commands, or with search-n-replace directly in text files.


## Web sites to scratch (2 out of 3):


**Dan:**
* https://www.khushiauto.ca/used-cars
* https://www.simpleautos.ca/used-cars/
* https://www.tabangimotors.com/used-cars/


**Boris:**
* https://www.rahmanmotors.com/cars/used/
* https://www.auto8000.ca/inventory/
* http://www.leopardmotors.ca/listings/

P.S. I checked their robots.txt, none of the sites prohibit scraping


## Additional reading

* [Is web scraping legal or not? Things to consider before doing web data extraction?](https://medium.com/dataflow-kit/is-web-scraping-legal-or-not-f6c26074584)
* [US court fully legalized website scraping and technically prohibited it](https://parsers.me/us-court-fully-legalized-website-scraping-and-technically-prohibited-it/)
