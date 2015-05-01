Stock Data Warehouse
====================

This module adds a data warehouse to the stock capability included in
Tryton. 

Key Features
------------

1. Daily Stock Levels
`````````````````````

Tryton computes stock (inventory) levels of a product in a location by
computing a sum total of the moves into the location (normalized to the
default UOM of the product) and then substracting everything that went
out. Computing this for every day at run-time for a data mart could get
quite expensive. So a cron periodically fills a
``product_inventory_history`` table with the historic inventory levels of
each warehouse.

The grain of the data would have:

* date
* product
* warehouse
* quantity
* quantity_customer
* quantity_supplier
* quantity_production
* quantity_lost_and_found
* uom

The quantity fields with the location suffix represent the absolute
movement towards or away from the warehouse.

Important
.........

The above table over a period of time could easily explode into millions
of not so useful information. For example, with 10,000 products and a
single warehouse, this would result in 3,650,000 (365 x 10000) - about
3.65 million rows for just a year. Therefore, it is better to archive this
information periodically. Retaining the daily levels for 2 months and
archiving older information should suffice the need of most analysts.

2. Monthly Stock Levels
```````````````````````

Unlike the daily stock levels (which could result in too much data over
time), the monthly stock levels provide a snapshot for each product in a
warehouse. In addition to stock levels, average quantity over days in the
month are stored.

* month
* year
* product
* warehouse
* quantity
* *quantity_average*
* quantity_customer
* quantity_supplier
* quantity_production
* quantity_lost_and_found
* uom

Initial and Recomputation
-------------------------

If your database did not start with using this module, or if you have a
lot of data to be recomputed, we strongly recommend the use of
``trytond_async`` module which could do all of these operations
asynchronously and spread out over several workers.
