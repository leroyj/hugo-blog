---
title: "\U0001F4CA Try to do this with Excel :)"
date: 2022-02-02T21:59:43.000Z
draft: false
tags:
    - EN
    - howto
asciinema: true
description: Deal with 5GB csv file easily with ripgrep and xsv - Command line still make sense for data stuff
lastmod: '2022-02-03T18:42:49.743Z'
preview: /file-not-loaded-completely.png
---
_How to deal with a 5GB file to extract simple information?
CLI is here to save your day!_

![Ouch!](/file-not-loaded-completely.png)

## Yes, the command line is still alive

Not only the Command Line Interface (CLI) is still a thing today on the ops side but we can even see a kind of renaissance.

### Automation

Devops and Continuous Delivery push a frequent deployment by the developers (more than daily). This is only achievable if you have a full delivery chain automation. Such an automation, assuming a hig level of customization, is allowed by scripting and sharp tools.

And scripting means also CLI...

### Rust 🦀

![rust](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d5/Rust_programming_language_black_logo.svg/144px-Rust_programming_language_black_logo.svg.png)

Rust becomes more and more popular for system stuff these days.  
It was initially developed by Mozilla Research since 2010.
It's success is mainly because it's a modern system language offering a high security **_and_** speed.
With this success comes a lot of rewriting of classical CLI tools. And it goes far behind a simple rewriting.  

### Tools on steroids

One of the famous rewriting of tool is [ripgrep](https://github.com/BurntSushi/ripgrep) (aka `rg`). This is a light faster reimplementation of the famous `grep` tool to search strings in files.  
Its blazing performance makes it a tool of choice to be integrated in major project such as Microsoft Visual Studio Code.

Give a try to these other nice tools:

* `exa` instead of `ls`
* `fd` instead of `find`  
* `dust` instead of `du`

## Why should I care?

### The use case

For some reason, I had to find the office of a specific company in a given city in France. Of course, there are some online service that provides this service.
However the company I was looking for (Orange) has more that 4,000 Offices and the service doesn't offer the capability to filter by city and the
 list contains both the Active and Closed office.

### Open Data

Thanks to the Open Data community, most of the governements are now opening data.
There is even an official web site from the governement that serve the French company database called [Annuaire Entreprise](https://annuaire-entreprises.data.gouv.fr/).
Unfortunatly, once again, no way to filter the office from this huge list.
Let's go to the [data.gouv.fr](https://www.data.gouv.fr/en/) platform.
You can easely find the French company database called [Sirene](https://www.data.gouv.fr/fr/datasets/base-sirene-des-entreprises-et-de-leurs-etablissements-siren-siret/).

However, once recovered and decompressed, the file is still 5.4 GigaBytes!

### Now, what to do with a 5,3GB csv file?

Ripgrep aka `rg` is here to help to spot your data.
It's then easy to extract the useful and clean data using xsv.

Here's how:

```bash
# Get the French company file from the open data platform
curl -LO https://www.data.gouv.fr/fr/datasets/r/0651fb76-bcf3-4f6a-a38d-bc04fa708576
# unzip the 1.38GB file to a 5.3GB csv file
# Search for Orange (unique id 380129866) and extract the Orange's offices in the city of Guyancourt (Postal Code 78280) that are still active
time (\
xsv search -s siren '380129866' StockEtablissement_utf8.csv \
| xsv search -s codePostalEtablissement '78280' \
| xsv search -s etatAdministratifEtablissement 'A' \
| xsv select siren,siret,enseigne1Etablissement,typeVoieEtablissement,libelleVoieEtablissement,libelleCommuneEtablissement \
| xsv table )
```

{{< asciinema key="466256" speed="2" preload="1" rows="29" idle-time-limit="0.5" font-size="small" poster="npt:2:47" >}}

Command line still make sense for data stuff.
Try this with your favorite Graphical Interface app :P
