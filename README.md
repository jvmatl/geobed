Geobed
============

## JVMATL Fork

I've made a fork that includes a snapshot of the downloadable data
sets, for an offline application without internet access. I only need
a subset of the functionality of the original Geobed, so parts may be
removed or modified without warning. This work is public, and if it's
useful to you, you are welcome to do whatever you want with it,
(subject to the conditions of the original author's license and that
of the data set owners,) but it is not my intention to make this a
public *project.* I just don't have that kind of time at this point in
my life.

### Summary of my changes: 
My goal is to turn this into a truly standalone
module with embedded geo data that can do high-level, high(ish) speed
reverse-geocoding in an intranet that has no internet access, and then
build that into a docker container with a thin web-service veneer on
top, so I can add it to a k8s cluster that processes a lot of location
(lat/long) data and use it to enhance that data set with
human-readable locations. Here's what I've done so far:
* Downloaded a snapshot of the geonames.org data and added it to the repo.
* found an old copy of the old public maxmind data set and did the
  same (newer versions of the data set require you to get a login,
  etc. etc. -- Not worth the hassle for my application)
* ran the program once locally, so that it would generate the cache
  files (the .dmp files are GOB encoded Golang structures, and only
  take a few seconds to load)
* commited the gob files to the repo
* embedded the gob encoded cache into the module with the go:embed
  directive and modified the code that loads and stores the cache
  files to write to a different directory from the one that stores the
  raw geo data. The raw geo data is still part of the *repo*, but the
  pre-digested map data cache is becoming part of the *binary*

# ORIGINAL README
======================


This Golang package contains an embedded geocoder. There are no major external dependendies other than some downloaded data files. Once downloaded, those data files 
are stored in memory. So after the initial load there truly are no outside dependencies. It geocodes and reverse geocodes to a city level detail. It approximates and takes 
educated guesses when not enough detail is provided. See test cases for more examples.

## Why?

To keep it short and simple, the reason this package was built was because geocoding services are really expensive. If city level detail is enough and you don't need street addresses, 
then this should be completely fine. It's also nice that there are no HTTP requests being made to do this (after initial load - and the data files can be copied to other places).

Performance is pretty good, but that is one of the goals. Overtime it should improve, but for now it geocodes a string to lat/lng in about 0.0125 - 0.0135 seconds (on a Macbook Pro).

## Usage

You should re-use the ```GeoBed``` struct as it contains a LOT of data (2.7+ million items). On this struct are the functions to geocode and reverse geocode. Be aware that
this also means your machine will need a good bit of RAM since this is all data held in memory (which is also what makes it fast too).

```
g := NewGeobed()
c := g.Geocode("london")
```

In the above case, ```c``` should end up being:

```
{London london City of London,Gorad Londan,ILondon,LON,Lakana,Landen,Ljondan,Llundain,Londain,Londan,Londar,Londe,Londen,Londinium,Londino,Londn,London,London City,Londona,Londonas,Londoni,Londono,Londonu,Londra,Londres,Londrez,Londri,Londye,Londyn,Londýn,Lonn,Lontoo,Loundres,Luan GJon,Lunden,Lundra,Lundun,Lundunir,Lundúnir,Lung-dung,Lunnainn,Lunnin,Lunnon,Luân Đôn,Lùng-dŭng,Lākana,Lůndůn,Lọndọnu,Ranana,Rānana,The City,ilantan,landan,landana,leondeon,lndn,london,londoni,lun dui,lun dun,lwndwn,lxndxn,rondon,Łondra,Λονδίνο,Горад Лондан,Лондан,Лондон,Лондонъ,Лёндан,Լոնդոն,לאנדאן,לונדון,لندن,لوندون,لەندەن,ܠܘܢܕܘܢ,लंडन,लंदन,लण्डन,लन्डन्,লন্ডন,લંડન,ଲଣ୍ଡନ,இலண்டன்,లండన్,ಲಂಡನ್,ലണ്ടൻ,ලන්ඩන්,ลอนดอน,ລອນດອນ,ལོན་ཊོན།,လန်ဒန်မြို့,ლონდონი,ለንደን,ᎫᎴ ᏗᏍᎪᏂᎯᏱ,ロンドン,伦敦,倫敦,런던 GB ENG 51.50853 -0.12574 7556900 gcpvj0u6yjcm}
```

So you can get lat/lng from the ```GeobedCity``` struct real easily with: ```c.Latitude``` and ```c.Longitude```.

You'll notice some records are larger and contain many alternate names for the city. The free data sets come from Geonames and MaxMind. MaxMind has more but less details. Geonames has more details, but it only contains cities with populations of 1,000 people or greater (about 143,000 records).

If you looked up a major city, you'll likely have information such as population (```c.Population```).

You can reverse geocode as well.

```
c := g.ReverseGeocode(30.26715, -97.74306)
```

This would give you Austin, TX for example.

## Data Sets

The data sets are provided by [Geonames](http://download.geonames.org/export/dump) and [MaxMind](https://www.maxmind.com/en/worldcities). These are open source data sets. See their web sites for additional information.
