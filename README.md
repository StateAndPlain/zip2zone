# Motivation

A project required a US ZIP Code to Plant Hardiness Zone mapping database like the [USDA Agricultural Research Service](http://planthardiness.ars.usda.gov/PHZMWeb/) provides, but the project did not have the budget to purchase the 19 region GIS shapefiles at $149.95 each (late 2014 pricing).

The [Open Plant Hardiness Zones](https://github.com/wboykinm/ophz) project contributors did the heavy lifting of rasterizing the publicly available high resolution maps that are available free of charge.

This tool combines a data source of US ZIP Code to latitude/longitude mappings with the OPHZ shapefiles to create a database mapping US ZIP Codes to Plant Hardiness data.

There are several sources for US ZIP Code to latitude/longitude coordinates, some of which require a small fee depending on your usage, so this tool does not automatically include one.  Indeed, per a discussion on [GIS Stack Exchange](http://gis.stackexchange.com/questions/5114/where-can-i-obtain-an-up-to-date-list-of-us-zip-codes-with-latitude-and-longitud) there are a number of reasons asking for a ZIP Code to coordinate mapping doesn't make sense.

# Required Software

* Ruby.  Tested on 2.1.2, but the dependencies all say they'll work on 1.9.3.
* [sqlite3](http://www.sqlite.org/download.html)
* [spatialite](https://www.gaia-gis.it/fossil/libspatialite/index)
* [spatialite-tools](https://www.gaia-gis.it/fossil/spatialite-tools/index)

I installed `sqlite3`, `spatialite` and `spatialite-tools` using [Homebrew](http://brew.sh/) on Mac OS X 10.9.  

Tip: to get the spatialite formulae you must first `brew tap homebrew/homebrew-science`.

Tip: On Mac OS X 10.9 I had to `gem uninstall sqlite3` and then `gem install sqlite3 -- --with-sqlite3-include=/usr/local/Cellar/sqlite/3.8.7.1/include --with-sqlite3-lib=/usr/local/Cellar/sqlite/3.8.7.1/lib` to get the sqlite3 gem to build against the homebrew installed sqlite3 instead of the OS-installed one.

# Usage

1. Acquire a CSV mapping ZIP Codes to Latitude / Longitude.  Options include:
    * [2010 census.gov ZIP Code Tabluation Areas (ZCTAs)](http://www.census.gov/geo/maps-data/data/gazetteer2010.html)
    * [http://www.unitedstateszipcodes.org/zip-code-database/](http://www.unitedstateszipcodes.org/zip-code-database/)
    * [http://www.boutell.com/zipcodes/](http://www.boutell.com/zipcodes/)
2. Convert the data into a CSV file with the fields ZIP Code, Latitude, Longitude, in that order, including column headers.  Excel, Google Docs Spreadsheet and OpenOffice are all decent tools to use for this purpose.  Save the file as `zip2latlong.csv` at the root of this directory.
3. Run `bundle install`.
4. Run `rake load_shapes`.
5. Run `rake zip2zone`.  Get a beverage of your choice and wait.

Congratulations!  You should have a GDBM in `zip2zone.gdbm` with the keys being ZIP Codes and the values being JSON-encoded objects with the keys `zone` and `temp`.

# Acknowledgements

I'm guessing the [GIS rasterization](https://github.com/wboykinm/ophz) of the high resolution images available from [http://planthardiness.ars.usda.gov/PHZMWeb/](http://planthardiness.ars.usda.gov/PHZMWeb/) required a lot of work. For that effort I am grateful.