optiq-web
============

Optiq adapter that reads HTML tables from URLs.

Optiq-web is another simple demonstration of how to connect <a
href="https://github.com/julianhyde/optiq">Optiq</a> to your own
data source and quickly get a full SQL/JDBC interface.

Based on the <a href="https://github.com/julianhyde/optiq-csv">example (optiq-csv)</a>
provided by <a href="https://github.com/julianhyde">Julian Hyde</a>

What it does
==================

For instance if you define:

* States - http://en.wikipedia.org/wiki/List_of_states_and_territories_of_the_United_States
* Cities - http://en.wikipedia.org/wiki/List_of_United_States_cities_by_population

You can then write a query like:
```SQL
select
	count(*) "City Count",
	sum(100 * c."Population" / s."Population") "Pct State Population"
from "Cities" c, "States" s
where c."State" = s."State" and s."State" = 'California';
```

And then get an answer like this:
```
+---------------------+----------------------+
|     City Count      | Pct State Population |
+---------------------+----------------------+
| 69                  | 48.574217177106576   |
+---------------------+----------------------+
```

Download and build
==================

You need Java (1.6 or higher; 1.7 preferred), git and maven (3.0.4 or later).

```bash
$ git clone git://github.com/HenryOlson/optiq-web.git
$ cd optiq-web
$ mvn compile
```

Kick the tires
==============

Let's take a quick look at optiq-web's (and optiq's) features.
We'll use <code>sqlline</code>, a SQL shell that connects to
any JDBC data source and is included with optiq-web.

Connect to Optiq and try out some queries:

```bash
$ ./sqlline
sqlline> !connect jdbc:optiq:model=target/test-classes/wiki.json admin admin
sqlline> !tables
sqlline> !describe "Cities"
sqlline> SELECT * FROM "Cities";
sqlline> !describe "States"
sqlline> SELECT * FROM "States";
sqlline> !quit
$ ./sqlline <target/test-classes/webjoin.sql
```

(On Windows, the command is `sqlline.bat`.)

As you can see, Optiq has a full SQL implementation that can efficiently
query any data source.

For a more leisurely walk through what Optiq can do and how it does it,
try <a href="https://github.com/julianhyde/optiq-csv/blob/master/TUTORIAL.md">the Optiq Tutorial</a>.

Mapping tables
================

Tables can be simply defined for immediate gratification:
```json
{
	tableName: "RawCities",
	url: "http://en.wikipedia.org/wiki/List_of_United_States_cities_by_population"
}
```

And subsequently refined for better usability / querying:
```json
{
	tableName: "Cities",
	url: "http://en.wikipedia.org/wiki/List_of_United_States_cities_by_population",
	path: "#mw-content-text > table.wikitable.sortable",
	index: 0,
	fieldDefs: [
	  {th: "2013 rank", name: "Rank", type: "int", pattern: "(\\d+)", matchGroup: 0},
	  {th: "City", selector: "a", selectedElement: 0},
	  {th: "State[5]", name: "State", selector: "a:eq(0)"},
	  {th: "2013 estimate", name: "Population", type: "double"},
	  {th: "2010 Census", skip: "true"},
	  {th: "Change", skip: "true"},
	  {th: "2013 land area", name: "Land Area (sq mi)", type: "double", selector: ":not(span)"},
	  {th: "2013 population density", skip: "true"},
	  {th: "ANSI", skip: "true"}
	]
},
```

More information
================

* License: Apache License, Version 2.0.
* Author: Henry Olson
* Source code: http://github.com/HenryOlson/optiq-web
* Developers list: http://groups.google.com/group/optiq-dev
* <a href="HISTORY.md">Release notes and history</a>

